cmake_minimum_required(VERSION 2.8)

# Make sure the user is not executing this script directly
if(NOT InOpenJK)
	message(FATAL_ERROR "Use the top-level cmake script!")
endif(NOT InOpenJK)

set(MPSharedDefines ${SharedDefines} "_JK2")

#    Add Game Project
if(BuildMPGame)
	add_subdirectory("${MPDir}/game")
endif(BuildMPGame)

#    Add CGame Project
if(BuildMPCGame)
	add_subdirectory("${MPDir}/cgame")
endif(BuildMPCGame)

#    Add UI Project
if(BuildMPUI)
	add_subdirectory("${MPDir}/ui")
endif(BuildMPUI)


#        Common things between Engine and Dedicated Server
if(BuildMPEngine OR BuildMPDed)

	#    Botlib
	# the files could arguably just be put into the engine and dedicated projects without having a library for it.
	set(MPBotlibDefines "BOTLIB")
	set(MPBotlibIncludeDirectories "${MPDir}")
	set(MPBotlibHeaderFiles
		"${MPDir}/botlib/aasfile.h"
		"${MPDir}/botlib/be_aas.h"
		"${MPDir}/botlib/be_aas_bsp.h"
		"${MPDir}/botlib/be_aas_cluster.h"
		"${MPDir}/botlib/be_aas_debug.h"
		"${MPDir}/botlib/be_aas_def.h"
		"${MPDir}/botlib/be_aas_entity.h"
		"${MPDir}/botlib/be_aas_file.h"
		"${MPDir}/botlib/be_aas_funcs.h"
		"${MPDir}/botlib/be_aas_main.h"
		"${MPDir}/botlib/be_aas_move.h"
		"${MPDir}/botlib/be_aas_optimize.h"
		"${MPDir}/botlib/be_aas_reach.h"
		"${MPDir}/botlib/be_aas_route.h"
		"${MPDir}/botlib/be_aas_routealt.h"
		"${MPDir}/botlib/be_aas_sample.h"
		"${MPDir}/botlib/be_ai_char.h"
		"${MPDir}/botlib/be_ai_chat.h"
		"${MPDir}/botlib/be_ai_gen.h"
		"${MPDir}/botlib/be_ai_goal.h"
		"${MPDir}/botlib/be_ai_move.h"
		"${MPDir}/botlib/be_ai_weap.h"
		"${MPDir}/botlib/be_ai_weight.h"
		"${MPDir}/botlib/be_ea.h"
		"${MPDir}/botlib/be_interface.h"
		"${MPDir}/game/botlib.h"
		"${MPDir}/qcommon/cm_public.h"
		"${MPDir}/qcommon/disablewarnings.h"
		"${MPDir}/botlib/l_crc.h"
		"${MPDir}/botlib/l_libvar.h"
		"${MPDir}/botlib/l_log.h"
		"${MPDir}/botlib/l_memory.h"
		"${MPDir}/botlib/l_precomp.h"
		"${MPDir}/botlib/l_script.h"
		"${MPDir}/botlib/l_struct.h"
		"${MPDir}/botlib/l_utils.h"
		"${MPDir}/qcommon/q_shared.h"
		"${MPDir}/qcommon/qcommon.h"
		"${MPDir}/qcommon/qfiles.h"
		"${MPDir}/server/server.h"
		"${MPDir}/game/surfaceflags.h"
		"${MPDir}/qcommon/tags.h"
		)
	source_group("Header Files" FILES ${MPBotlibHeaderFiles})
	set(MPBotlibSourceFiles
		"${MPDir}/botlib/be_aas_bspq3.cpp"
		"${MPDir}/botlib/be_aas_cluster.cpp"
		"${MPDir}/botlib/be_aas_debug.cpp"
		"${MPDir}/botlib/be_aas_entity.cpp"
		"${MPDir}/botlib/be_aas_file.cpp"
		"${MPDir}/botlib/be_aas_main.cpp"
		"${MPDir}/botlib/be_aas_move.cpp"
		"${MPDir}/botlib/be_aas_optimize.cpp"
		"${MPDir}/botlib/be_aas_reach.cpp"
		"${MPDir}/botlib/be_aas_route.cpp"
		"${MPDir}/botlib/be_aas_routealt.cpp"
		"${MPDir}/botlib/be_aas_sample.cpp"
		"${MPDir}/botlib/be_ai_char.cpp"
		"${MPDir}/botlib/be_ai_chat.cpp"
		"${MPDir}/botlib/be_ai_gen.cpp"
		"${MPDir}/botlib/be_ai_goal.cpp"
		"${MPDir}/botlib/be_ai_move.cpp"
		"${MPDir}/botlib/be_ai_weap.cpp"
		"${MPDir}/botlib/be_ai_weight.cpp"
		"${MPDir}/botlib/be_ea.cpp"
		"${MPDir}/botlib/be_interface.cpp"
		"${MPDir}/botlib/l_crc.cpp"
		"${MPDir}/botlib/l_libvar.cpp"
		"${MPDir}/botlib/l_log.cpp"
		"${MPDir}/botlib/l_memory.cpp"
		"${MPDir}/botlib/l_precomp.cpp"
		"${MPDir}/botlib/l_script.cpp"
		"${MPDir}/botlib/l_struct.cpp"
		)
	source_group("Source Files" FILES ${MPBotlibSourceFiles})
	
	add_library(${MPBotLib} STATIC ${MPBotlibHeaderFiles} ${MPBotlibSourceFiles})
	set_target_properties(${MPBotLib} PROPERTIES COMPILE_DEFINITIONS "${MPBotlibDefines}")
	set_target_properties(${MPBotLib} PROPERTIES INCLUDE_DIRECTORIES "${MPBotlibIncludeDirectories}")
	
	#    Common files/libraries/defines of both Engine and Dedicated Server
	
	# libraries: Botlib
	set(MPEngineAndDedLibraries ${MPBotLib})
	# Platform-specific libraries
	if(WIN32)
		set(MPEngineAndDedLibraries ${MPEngineAndDedLibraries} "winmm" "wsock32")
	else(WIN32)
		message("You might need to add some libraries to codemp/CMakeLists.txt or you could get linker errors on non-Windows-OSs.")
	endif(WIN32)
	# Include directories
	set(MPEngineAndDedIncludeDirectories ${MPDir}) # codemp folder, since includes are not always relative in the files
	
	if(UseInternalZlib)
		set(MPEngineAndDedIncludeDirectories ${MPEngineAndDedIncludeDirectories} "${MPDir}/zlib")
		# OpenAL32.lib is included for windows
		find_library(ZlibLibrary NAMES zlib PATHS "${MPDir}/libs/win32")
		if(NOT ZlibLibrary)
			message(FATAL_ERROR "UseInternalOpenAL set, but libs/OpenAL32 not found!")
		endif(NOT ZlibLibrary)
		set(MPEngineAndDedLibraries ${MPEngineAndDedLibraries} ${ZlibLibrary})
	else(UseInternalZlib)
		find_package(ZLIB REQUIRED)
		set(MPEngineAndDedIncludeDirectories ${MPEngineAndDedIncludeDirectories} ${ZLIB_INCLUDE_DIR})
		set(MPEngineAndDedLibraries ${MPEngineAndDedLibraries} ${ZLIB_LIBRARY})
	endif(UseInternalZlib)
	
	# All the rest, yet to be sorted (let's just get it to compile first)
	# TODO sort this
	set(MPEngineAndDedAllSortMe
		"${MPDir}/cgame/cg_public.h"
		"${MPDir}/cgame/tr_types.h"
		"${MPDir}/client/client.h"
		"${MPDir}/client/keys.h"
		"${MPDir}/client/snd_public.h"
		"${MPDir}/game/anims.h"
		"${MPDir}/game/bg_public.h"
		"${MPDir}/game/bg_weapons.h"
		"${MPDir}/game/botlib.h"
		"${MPDir}/game/g_public.h"
		"${MPDir}/qcommon/q_shared.h"
		"${MPDir}/game/surfaceflags.h"
		"${MPDir}/ghoul2/G2.h"
		"${MPDir}/ghoul2/G2_API.cpp"
		"${MPDir}/ghoul2/G2_bolts.cpp"
		"${MPDir}/ghoul2/G2_bones.cpp"
		"${MPDir}/ghoul2/G2_gore.h"
		"${MPDir}/ghoul2/G2_local.h"
		"${MPDir}/ghoul2/G2_misc.cpp"
		"${MPDir}/ghoul2/G2_surfaces.cpp"
		"${MPDir}/ghoul2/ghoul2_shared.h"
		"${MPDir}/icarus/BlockStream.cpp"
		"${MPDir}/icarus/blockstream.h"
		"${MPDir}/icarus/GameInterface.cpp"
		"${MPDir}/icarus/GameInterface.h"
		"${MPDir}/icarus/icarus.h"
		"${MPDir}/icarus/Instance.cpp"
		"${MPDir}/icarus/instance.h"
		"${MPDir}/icarus/Interface.cpp"
		"${MPDir}/icarus/interface.h"
		"${MPDir}/icarus/interpreter.h"
		"${MPDir}/icarus/Memory.cpp"
		"${MPDir}/icarus/module.h"
		"${MPDir}/icarus/Q3_Interface.cpp"
		"${MPDir}/icarus/Q3_Interface.h"
		"${MPDir}/icarus/Q3_Registers.cpp"
		"${MPDir}/icarus/Q3_Registers.h"
		"${MPDir}/icarus/Sequence.cpp"
		"${MPDir}/icarus/sequence.h"
		"${MPDir}/icarus/Sequencer.cpp"
		"${MPDir}/icarus/sequencer.h"
		"${MPDir}/icarus/TaskManager.cpp"
		"${MPDir}/icarus/taskmanager.h"
		"${MPDir}/icarus/tokenizer.h"
		"${MPDir}/qcommon/chash.h"
		"${MPDir}/qcommon/cm_draw.cpp"
		"${MPDir}/qcommon/cm_draw.h"
		"${MPDir}/qcommon/cm_landscape.h"
		"${MPDir}/qcommon/cm_load.cpp"
		"${MPDir}/qcommon/cm_local.h"
		"${MPDir}/qcommon/cm_patch.cpp"
		"${MPDir}/qcommon/cm_patch.h"
		"${MPDir}/qcommon/cm_polylib.cpp"
		"${MPDir}/qcommon/cm_polylib.h"
		"${MPDir}/qcommon/cm_public.h"
		"${MPDir}/qcommon/cm_randomterrain.cpp"
		"${MPDir}/qcommon/cm_randomterrain.h"
		"${MPDir}/qcommon/cm_shader.cpp"
		"${MPDir}/qcommon/cm_terrain.cpp"
		"${MPDir}/qcommon/cm_terrainmap.h"
		"${MPDir}/qcommon/cm_test.cpp"
		"${MPDir}/qcommon/cm_trace.cpp"
		"${MPDir}/qcommon/cmd_common.cpp"
		"${MPDir}/qcommon/cmd_pc.cpp"
		"${MPDir}/qcommon/common.cpp"
		"${MPDir}/qcommon/cvar.cpp"
		"${MPDir}/qcommon/disablewarnings.h"
		"${MPDir}/qcommon/files_common.cpp"
		"${MPDir}/qcommon/files_pc.cpp"
		"${MPDir}/qcommon/game_version.h"
		"${MPDir}/qcommon/GenericParser2.cpp"
		"${MPDir}/qcommon/GenericParser2.h"
		"${MPDir}/qcommon/huffman.cpp"
		"${MPDir}/qcommon/md4.cpp"
		"${MPDir}/qcommon/MiniHeap.h"
		"${MPDir}/qcommon/msg.cpp"
		"${MPDir}/qcommon/net_chan.cpp"
		"${MPDir}/qcommon/q_math.cpp"
		"${MPDir}/qcommon/q_shared.cpp"
		"${MPDir}/qcommon/qcommon.h"
		"${MPDir}/qcommon/qfiles.h"
		"${MPDir}/qcommon/RoffSystem.cpp"
		"${MPDir}/qcommon/RoffSystem.h"
		"${MPDir}/qcommon/sstring.h"
		"${MPDir}/qcommon/stringed_ingame.cpp"
		"${MPDir}/qcommon/stringed_ingame.h"
		"${MPDir}/qcommon/stringed_interface.cpp"
		"${MPDir}/qcommon/stringed_interface.h"
		"${MPDir}/qcommon/tags.h"
		"${MPDir}/qcommon/timing.h"
		"${MPDir}/qcommon/vm.cpp"
		"${MPDir}/qcommon/vm_interpreted.cpp"
		"${MPDir}/qcommon/vm_local.h"
		"${MPDir}/qcommon/vm_x86.cpp"
		"${MPDir}/qcommon/z_memman_pc.cpp"
		"${MPDir}/renderer/glext.h"
		"${MPDir}/renderer/matcomp.c"
		"${MPDir}/renderer/matcomp.h"
		"${MPDir}/renderer/mdx_format.h"
		"${MPDir}/renderer/qgl.h"
		"${MPDir}/renderer/tr_backend.cpp"
		"${MPDir}/renderer/tr_font.h"
		"${MPDir}/renderer/tr_ghoul2.cpp"
		"${MPDir}/renderer/tr_image.cpp"
		"${MPDir}/renderer/tr_init.cpp"
		"${MPDir}/renderer/tr_local.h"
		"${MPDir}/renderer/tr_main.cpp"
		"${MPDir}/renderer/tr_mesh.cpp"
		"${MPDir}/renderer/tr_model.cpp"
		"${MPDir}/renderer/tr_public.h"
		"${MPDir}/renderer/tr_shader.cpp"
		"${MPDir}/RMG/RM_Area.cpp"
		"${MPDir}/RMG/RM_Area.h"
		"${MPDir}/RMG/RM_Headers.h"
		"${MPDir}/RMG/RM_Instance.cpp"
		"${MPDir}/RMG/RM_Instance.h"
		"${MPDir}/RMG/RM_Instance_BSP.cpp"
		"${MPDir}/RMG/RM_Instance_BSP.h"
		"${MPDir}/RMG/RM_Instance_Group.cpp"
		"${MPDir}/RMG/RM_Instance_Group.h"
		"${MPDir}/RMG/RM_Instance_Random.cpp"
		"${MPDir}/RMG/RM_Instance_Random.h"
		"${MPDir}/RMG/RM_Instance_Void.cpp"
		"${MPDir}/RMG/RM_Instance_Void.h"
		"${MPDir}/RMG/RM_InstanceFile.cpp"
		"${MPDir}/RMG/RM_InstanceFile.h"
		"${MPDir}/RMG/RM_Manager.cpp"
		"${MPDir}/RMG/RM_Manager.h"
		"${MPDir}/RMG/RM_Mission.cpp"
		"${MPDir}/RMG/RM_Mission.h"
		"${MPDir}/RMG/RM_Objective.cpp"
		"${MPDir}/RMG/RM_Objective.h"
		"${MPDir}/RMG/RM_Path.cpp"
		"${MPDir}/RMG/RM_Path.h"
		"${MPDir}/RMG/RM_Terrain.cpp"
		"${MPDir}/RMG/RM_Terrain.h"
		"${MPDir}/server/NPCNav/gameCallbacks.cpp"
		"${MPDir}/server/NPCNav/navigator.cpp"
		"${MPDir}/server/NPCNav/navigator.h"
		"${MPDir}/server/server.h"
		"${MPDir}/server/sv_bot.cpp"
		"${MPDir}/server/sv_ccmds.cpp"
		"${MPDir}/server/sv_client.cpp"
		"${MPDir}/server/sv_game.cpp"
		"${MPDir}/server/sv_init.cpp"
		"${MPDir}/server/sv_main.cpp"
		"${MPDir}/server/sv_net_chan.cpp"
		"${MPDir}/server/sv_snapshot.cpp"
		"${MPDir}/server/sv_world.cpp"
		"${MPDir}/ui/keycodes.h"
		"${MPDir}/ui/ui_public.h"
		"${MPDir}/png/png.h"
		"${MPDir}/png/png.cpp"
		
		# TODO: replace the following with proper libraries
		
		"${MPDir}/qcommon/unzip.cpp" #minizip
		"${MPDir}/qcommon/unzip.h"
		"${MPDir}/zlib/ioapi.c"
		"${MPDir}/zlib/ioapi.h"
		)
	if(WIN32)
		set(MPEngineAndDedAllSortMe
			${MPEngineAndDedAllSortMe}
			"${MPDir}/win32/win_local.h"
			"${MPDir}/win32/win_net.cpp"
			"${MPDir}/win32/win_shared.cpp"
			)
	else(WIN32)
		message("TODO: Non-Win32 support")
	endif(WIN32)
	set(MPEngineAndDedFiles ${MPEngineAndDedFiles} ${MPEngineAndDedAllSortMe})
	source_group("SortMe" FILES ${MPEngineAndDedAllSortMe})
	
endif(BuildMPEngine OR BuildMPDed)

#        Engine/Executable (jamp.exe)

if(BuildMPEngine)
	set(MPEngineLibraries ${MPEngineAndDedLibraries})
	set(MPEngineIncludeDirectories ${MPEngineAndDedIncludeDirectories})
	set(MPEngineFiles ${MPEngineAndDedFiles})
	set(MPEngineDefines ${MPSharedDefines} "_JK2MP")
	
	#    Dependencies
	
	# OpenGL
	# (mrw) TODO Is this actually required on windows? It is loaded at runtime, should not be linked?
	find_package(OpenGL REQUIRED)
	set(MPEngineIncludeDirectories ${MPEngineIncludeDirectories} ${OPENGL_INCLUDE_DIR})
	set(MPEngineLibraries ${MPEngineLibraries} ${OPENGL_LIBRARIES})

	# OpenAL (is optionally included for Windows)
	if(UseInternalOpenAL)
		set(MPEngineIncludeDirectories ${MPEngineIncludeDirectories} "${MPDir}/client/OpenAL")
		# There's a libs/OpenAL32.lib
		# (mrw) TODO Is this actually required on windows? It is loaded at runtime, should not be linked?
		find_library(OpenALLibrary NAMES OpenAL32 PATHS "${MPDir}/libs/win32")
		if(NOT OpenALLibrary)
			message(FATAL_ERROR "UseInternalOpenAL set, but codemp/libs/win32/OpenAL32 not found!")
		endif(NOT OpenALLibrary)
		set(MPEngineLibraries ${MPEngineLibraries} ${OpenALLibrary})
	else(UseInternalOpenAL)
		find_package(OpenAL REQUIRED)
		set(MPEngineIncludeDirectories ${MPEngineIncludeDirectories} ${OPENAL_INCLUDE_DIR})
		set(MPEngineLibraries ${MPEngineLibraries} ${OPENAL_LIBRARY})
	endif(UseInternalOpenAL)
	
	# EAX is Windows-Only (right?)
	if(WIN32)
		set(MPEngineFiles ${MPEngineFiles}
			"${MPDir}/client/eax/eax.h"
			"${MPDir}/client/eax/EaxMan.h"
			)
	endif(WIN32)
	
	#    Source Files
	
	file(GLOB_RECURSE J_SRC "${MPDir}/jpeg-8c/*.c" "${MPDir}/jpeg-8c/*.h")
	
	# TODO sort this
	set(MPEngineSortMe
		"${MPDir}/client/cl_avi.cpp"
		"${MPDir}/client/cl_cgame.cpp"
		"${MPDir}/client/cl_cin.cpp"
		"${MPDir}/client/cl_console.cpp"
		"${MPDir}/client/cl_input.cpp"
		"${MPDir}/client/cl_keys.cpp"
		"${MPDir}/client/cl_main.cpp"
		"${MPDir}/client/cl_net_chan.cpp"
		"${MPDir}/client/cl_parse.cpp"
		"${MPDir}/client/cl_scrn.cpp"
		"${MPDir}/client/cl_ui.cpp"
		"${MPDir}/client/FXExport.cpp"
		"${MPDir}/client/FXExport.h"
		"${MPDir}/client/FxPrimitives.cpp"
		"${MPDir}/client/FxPrimitives.h"
		"${MPDir}/client/FxScheduler.cpp"
		"${MPDir}/client/FxScheduler.h"
		"${MPDir}/client/FxSystem.cpp"
		"${MPDir}/client/FxSystem.h"
		"${MPDir}/client/FxTemplate.cpp"
		"${MPDir}/client/FxUtil.cpp"
		"${MPDir}/client/FxUtil.h"
		"${MPDir}/client/snd_ambient.cpp"
		"${MPDir}/client/snd_ambient.h"
		"${MPDir}/client/snd_dma.cpp"
		"${MPDir}/client/snd_local.h"
		"${MPDir}/client/snd_mem.cpp"
		"${MPDir}/client/snd_mix.cpp"
		"${MPDir}/client/snd_mp3.cpp"
		"${MPDir}/client/snd_mp3.h"
		"${MPDir}/client/snd_music.cpp"
		"${MPDir}/client/snd_music.h"
		"${MPDir}/qcommon/cm_terrainmap.cpp"
		"${MPDir}/qcommon/CNetProfile.cpp"
		"${MPDir}/qcommon/exe_headers.cpp"
		"${MPDir}/qcommon/exe_headers.h"
		"${MPDir}/qcommon/files.h"
		"${MPDir}/qcommon/hstring.cpp"
		"${MPDir}/qcommon/hstring.h"
		"${MPDir}/qcommon/INetProfile.h"
		"${MPDir}/qcommon/platform.h"
		"${MPDir}/Ratl/bits_vs.h"
		"${MPDir}/Ratl/ratl_common.h"
		"${MPDir}/Ratl/vector_vs.h"
		"${MPDir}/Ravl/CVec.h"
		"${MPDir}/renderer/tr_animation.cpp"
		"${MPDir}/renderer/tr_arioche.cpp"
		"${MPDir}/renderer/tr_bsp.cpp"
		"${MPDir}/renderer/tr_cmds.cpp"
		"${MPDir}/renderer/tr_curve.cpp"
		"${MPDir}/renderer/tr_font.cpp"
		"${MPDir}/renderer/tr_landscape.h"
		"${MPDir}/renderer/tr_light.cpp"
		"${MPDir}/renderer/tr_marks.cpp"
		"${MPDir}/renderer/tr_noise.cpp"
		"${MPDir}/renderer/tr_quicksprite.cpp"
		"${MPDir}/renderer/tr_quicksprite.h"
		"${MPDir}/renderer/tr_scene.cpp"
		"${MPDir}/renderer/tr_shade.cpp"
		"${MPDir}/renderer/tr_shade_calc.cpp"
		"${MPDir}/renderer/tr_shadows.cpp"
		"${MPDir}/renderer/tr_sky.cpp"
		"${MPDir}/renderer/tr_surface.cpp"
		"${MPDir}/renderer/tr_surfacesprites.cpp"
		"${MPDir}/renderer/tr_terrain.cpp"
		"${MPDir}/renderer/tr_world.cpp"
		"${MPDir}/renderer/tr_WorldEffects.cpp"
		"${MPDir}/renderer/tr_WorldEffects.h"
		"${MPDir}/mp3code/cdct.c"
		"${MPDir}/mp3code/config.h"
		"${MPDir}/mp3code/csbt.c"
		"${MPDir}/mp3code/csbtb.c"
		"${MPDir}/mp3code/csbtl3.c"
		"${MPDir}/mp3code/cup.c"
		"${MPDir}/mp3code/cupini.c"
		"${MPDir}/mp3code/cupl1.c"
		"${MPDir}/mp3code/cupl3.c"
		"${MPDir}/mp3code/cwin.c"
		"${MPDir}/mp3code/cwinb.c"
		"${MPDir}/mp3code/cwinm.c"
		"${MPDir}/mp3code/htable.h"
		"${MPDir}/mp3code/hwin.c"
		"${MPDir}/mp3code/jdw.h"
		"${MPDir}/mp3code/l3.h"
		"${MPDir}/mp3code/l3dq.c"
		"${MPDir}/mp3code/l3init.c"
		"${MPDir}/mp3code/mdct.c"
		"${MPDir}/mp3code/mhead.c"
		"${MPDir}/mp3code/mhead.h"
		"${MPDir}/mp3code/mp3struct.h"
		"${MPDir}/mp3code/msis.c"
		"${MPDir}/mp3code/port.h"
		"${MPDir}/mp3code/small_header.h"
		"${MPDir}/mp3code/tableawd.h"
		"${MPDir}/mp3code/towave.c"
		"${MPDir}/mp3code/uph.c"
		"${MPDir}/mp3code/upsf.c"
		"${MPDir}/mp3code/wavep.c"
		
		# TODO: replace the following with proper libraries
		
		#"${MPDir}/jpeg-8c/*.c"
		#"${MPDir}/jpeg-8c/*.h"
		
		#"${MPDir}/jpeg-6/jdatasrc.cpp" #libjpeg
		#"${MPDir}/jpeg-6/jdmainct.cpp"
		#"${MPDir}/jpeg-6/jpegint.h"
		#"${MPDir}/jpeg-6/jcinit.cpp"
		#"${MPDir}/jpeg-6/jddctmgr.cpp"
		#"${MPDir}/jpeg-6/jerror.h"
		#"${MPDir}/jpeg-6/jdsample.cpp"
		#"${MPDir}/jpeg-6/jcapimin.cpp"
		#"${MPDir}/jpeg-6/jdapimin.cpp"
		#"${MPDir}/jpeg-6/jconfig.h"
		#"${MPDir}/jpeg-6/jdcolor.cpp"
		#"${MPDir}/jpeg-6/jccolor.cpp"
		#"${MPDir}/jpeg-6/jdct.h"
		#"${MPDir}/jpeg-6/jdmaster.cpp"
		#"${MPDir}/jpeg-6/jdhuff.cpp"
		#"${MPDir}/jpeg-6/jdpostct.cpp"
		#"${MPDir}/jpeg-6/jmemnobs.cpp"
		#"${MPDir}/jpeg-6/jinclude.h"
		#"${MPDir}/jpeg-6/jcdctmgr.cpp"
		#"${MPDir}/jpeg-6/jdapistd.cpp"
		#"${MPDir}/jpeg-6/jmorecfg.h"
		#"${MPDir}/jpeg-6/jcphuff.cpp"
		#"${MPDir}/jpeg-6/jidctflt.cpp"
		#"${MPDir}/jpeg-6/jdhuff.h"
		#"${MPDir}/jpeg-6/jchuff.cpp"
		#"${MPDir}/jpeg-6/jutils.cpp"
		#"${MPDir}/jpeg-6/jccoefct.cpp"
		#"${MPDir}/jpeg-6/jchuff.h"
		#"${MPDir}/jpeg-6/jdtrans.cpp"
		#"${MPDir}/jpeg-6/jcmaster.cpp"
		#"${MPDir}/jpeg-6/jmemsys.h"
		#"${MPDir}/jpeg-6/jdatadst.cpp"
		#"${MPDir}/jpeg-6/jpeglib.h"
		#"${MPDir}/jpeg-6/jmemmgr.cpp"
		#"${MPDir}/jpeg-6/jcmarker.cpp"
		#"${MPDir}/jpeg-6/jctrans.cpp"
		#"${MPDir}/jpeg-6/jdcoefct.cpp"
		#"${MPDir}/jpeg-6/jcsample.cpp"
		#"${MPDir}/jpeg-6/jdmarker.cpp"
		#"${MPDir}/jpeg-6/jcprepct.cpp"
		#"${MPDir}/jpeg-6/jcomapi.cpp"
		#"${MPDir}/jpeg-6/jdinput.cpp"
		#"${MPDir}/jpeg-6/jcmainct.cpp"
		#"${MPDir}/jpeg-6/jcparam.cpp"
		#"${MPDir}/jpeg-6/jerror.cpp"
		#"${MPDir}/jpeg-6/jfdctflt.cpp"
		#"${MPDir}/jpeg-6/jversion.h"
		)
	if(WIN32)
		set(MPEngineSortMe
			${MPEngineSortMe}
			"${MPDir}/win32/glw_win.h"
			"${MPDir}/win32/icon.ico"
			"${MPDir}/win32/resource.h"
			"${MPDir}/win32/win_gamma.cpp"
			"${MPDir}/win32/win_glimp.cpp"
			"${MPDir}/win32/win_input.cpp"
			"${MPDir}/win32/win_main.cpp"
			"${MPDir}/win32/win_qgl.cpp"
			"${MPDir}/win32/win_snd.cpp"
			"${MPDir}/win32/win_syscon.cpp"
			"${MPDir}/win32/win_wndproc.cpp"
			"${MPDir}/win32/engine.rc"
			)
	else(WIN32)
		message("Please add the OS-Specific files for your OS to codemp/CMakeLists.txt")
	endif(WIN32)
	set(MPEngineFiles ${MPEngineFiles} ${MPEngineSortMe} ${J_SRC})
	source_group("SortMe" FILES ${MPEngineSortMe})
	source_group("jpeg-8c" FILES ${J_SRC})

	# todo: windows resources

	if(WIN32)
		add_executable(${MPEngine} WIN32 ${MPEngineFiles})
	else(WIN32)
		add_executable(${MPEngine} ${MPEngineFiles})
	endif(WIN32)
	set_target_properties(${MPEngine} PROPERTIES COMPILE_DEFINITIONS_RELEASE "${MPEngineDefines};${ReleaseDefines}")
	set_target_properties(${MPEngine} PROPERTIES COMPILE_DEFINITIONS_DEBUG "${MPEngineDefines};${DebugDefines}")
	set_target_properties(${MPEngine} PROPERTIES INCLUDE_DIRECTORIES "${MPEngineIncludeDirectories}")
	target_link_libraries(${MPEngine} ${MPEngineLibraries})

endif(BuildMPEngine)

#        Dedicated Server (Engine) (jampded.exe)

if(BuildMPDed)
	set(MPDedLibraries ${MPEngineAndDedLibraries})
	set(MPDedIncludeDirectories ${MPEngineAndDedIncludeDirectories})
	set(MPDedFiles ${MPEngineAndDedFiles})
	set(MPDedDefines ${MPSharedDefines} "_CONSOLE" "DEDICATED")
	
	# TODO sort this
	set(MPDedSortMe
		"${MPDir}/null/null_client.cpp"
		"${MPDir}/null/null_glimp.cpp"
		"${MPDir}/null/null_input.cpp"
		"${MPDir}/null/null_renderer.cpp"
		"${MPDir}/null/null_snddma.cpp"
		)
	if(WIN32)
		set(MPDedSortMe ${MPDedSortMe}
			"${MPDir}/null/win_main.cpp"
			"${MPDir}/win32/dedicated.rc"
			)
	else(WIN32)
		message( "Add your dedicated server files to codemp/CMakeLists.txt please" )
	endif(WIN32)
	set(MPDedFiles ${MPDedFiles} ${MPDedSortMe})
	source_group("SortMe" FILES ${MPDedSortMe})
	
	add_executable(${MPDed} ${MPDedFiles})
	set_target_properties(${MPDed} PROPERTIES COMPILE_DEFINITIONS_RELEASE "${MPDedDefines};${ReleaseDefines}")
	set_target_properties(${MPDed} PROPERTIES COMPILE_DEFINITIONS_DEBUG "${MPDedDefines};${DebugDefines}")
	set_target_properties(${MPDed} PROPERTIES INCLUDE_DIRECTORIES "${MPDedIncludeDirectories}")
	target_link_libraries(${MPDed} ${MPDedLibraries})
endif(BuildMPDed)