cmake_minimum_required(VERSION 2.8)

# Make sure the user is not executing this script directly
if(NOT InOpenJK)
	message(FATAL_ERROR "Use the top-level cmake script!")
endif(NOT InOpenJK)

set(MPGameIncludeDirectories "${MPDir}")
if(WIN32)
	set(MPGameLibraries "winmm")
else(WIN32)
	message("codemp/game/CMakeLists.txt might need some libraries to link properly on your platform.")
endif(WIN32)
set(MPGameDefines ${MPSharedDefines} "QAGAME" "MISSIONPACK" )
set(MPGameSortMe
	"${MPDir}/game/ai_main.c"
	"${MPDir}/game/ai_util.c"
	"${MPDir}/game/ai_wpnav.c"
	"${MPDir}/game/AnimalNPC.c"
	"${MPDir}/game/bg_g2_utils.c"
	"${MPDir}/game/bg_misc.c"
	"${MPDir}/game/bg_panimate.c"
	"${MPDir}/game/bg_pmove.c"
	"${MPDir}/game/bg_saber.c"
	"${MPDir}/game/bg_saberLoad.c"
	"${MPDir}/game/bg_saga.c"
	"${MPDir}/game/bg_slidemove.c"
	"${MPDir}/game/bg_vehicleLoad.c"
	"${MPDir}/game/bg_weapons.c"
	"${MPDir}/game/FighterNPC.c"
	"${MPDir}/game/g_active.c"
	"${MPDir}/game/g_arenas.c"
	"${MPDir}/game/g_bot.c"
	"${MPDir}/game/g_client.c"
	"${MPDir}/game/g_cmds.c"
	"${MPDir}/game/g_combat.c"
	"${MPDir}/game/g_exphysics.c"
	"${MPDir}/game/g_ICARUScb.c"
	"${MPDir}/game/g_items.c"
	"${MPDir}/game/g_log.c"
	"${MPDir}/game/g_main.c"
	"${MPDir}/game/g_mem.c"
	"${MPDir}/game/g_misc.c"
	"${MPDir}/game/g_missile.c"
	"${MPDir}/game/g_mover.c"
	"${MPDir}/game/g_nav.c"
	"${MPDir}/game/g_navnew.c"
	"${MPDir}/game/g_object.c"
	"${MPDir}/game/g_saga.c"
	"${MPDir}/game/g_session.c"
	"${MPDir}/game/g_spawn.c"
	"${MPDir}/game/g_strap.c"
	"${MPDir}/game/g_svcmds.c"
	"${MPDir}/game/g_syscalls.c"
	"${MPDir}/game/g_target.c"
	"${MPDir}/game/g_team.c"
	"${MPDir}/game/g_timer.c"
	"${MPDir}/game/g_trigger.c"
	"${MPDir}/game/g_turret.c"
	"${MPDir}/game/g_turret_G2.c"
	"${MPDir}/game/g_utils.c"
	"${MPDir}/game/g_vehicles.c"
	"${MPDir}/game/g_vehicleTurret.c"
	"${MPDir}/game/g_weapon.c"
	"${MPDir}/game/NPC.c"
	"${MPDir}/game/NPC_AI_Atst.c"
	"${MPDir}/game/NPC_AI_Default.c"
	"${MPDir}/game/NPC_AI_Droid.c"
	"${MPDir}/game/NPC_AI_GalakMech.c"
	"${MPDir}/game/NPC_AI_Grenadier.c"
	"${MPDir}/game/NPC_AI_Howler.c"
	"${MPDir}/game/NPC_AI_ImperialProbe.c"
	"${MPDir}/game/NPC_AI_Interrogator.c"
	"${MPDir}/game/NPC_AI_Jedi.c"
	"${MPDir}/game/NPC_AI_Mark1.c"
	"${MPDir}/game/NPC_AI_Mark2.c"
	"${MPDir}/game/NPC_AI_MineMonster.c"
	"${MPDir}/game/NPC_AI_Rancor.c"
	"${MPDir}/game/NPC_AI_Remote.c"
	"${MPDir}/game/NPC_AI_Seeker.c"
	"${MPDir}/game/NPC_AI_Sentry.c"
	"${MPDir}/game/NPC_AI_Sniper.c"
	"${MPDir}/game/NPC_AI_Stormtrooper.c"
	"${MPDir}/game/NPC_AI_Utils.c"
	"${MPDir}/game/NPC_AI_Wampa.c"
	"${MPDir}/game/NPC_behavior.c"
	"${MPDir}/game/NPC_combat.c"
	"${MPDir}/game/NPC_goal.c"
	"${MPDir}/game/NPC_misc.c"
	"${MPDir}/game/NPC_move.c"
	"${MPDir}/game/NPC_reactions.c"
	"${MPDir}/game/NPC_senses.c"
	"${MPDir}/game/NPC_sounds.c"
	"${MPDir}/game/NPC_spawn.c"
	"${MPDir}/game/NPC_stats.c"
	"${MPDir}/game/NPC_utils.c"
	"${MPDir}/qcommon/q_math.c"
	"${MPDir}/qcommon/q_shared.c"
	"${MPDir}/game/SpeederNPC.c"
	"${MPDir}/game/tri_coll_test.c"
	"${MPDir}/game/w_force.c"
	"${MPDir}/game/w_saber.c"
	"${MPDir}/game/WalkerNPC.c"
	
	"${MPDir}/game/ai.h"
	"${MPDir}/game/ai_main.h"
	"${MPDir}/game/anims.h"
	"${MPDir}/cgame/animtable.h"
	"${MPDir}/game/b_local.h"
	"${MPDir}/game/b_public.h"
	"${MPDir}/botlib/be_aas.h"
	"${MPDir}/botlib/be_ai_char.h"
	"${MPDir}/botlib/be_ai_chat.h"
	"${MPDir}/botlib/be_ai_gen.h"
	"${MPDir}/botlib/be_ai_goal.h"
	"${MPDir}/botlib/be_ai_move.h"
	"${MPDir}/botlib/be_ai_weap.h"
	"${MPDir}/botlib/be_ea.h"
	"${MPDir}/game/bg_local.h"
	"${MPDir}/game/bg_public.h"
	"${MPDir}/game/bg_saga.h"
	"${MPDir}/game/bg_strap.h"
	"${MPDir}/game/bg_vehicles.h"
	"${MPDir}/game/bg_weapons.h"
	"${MPDir}/game/botlib.h"
	"${MPDir}/cgame/cg_local.h"
	"${MPDir}/cgame/cg_public.h"
	"${MPDir}/game/chars.h"
	"${MPDir}/qcommon/disablewarnings.h"
	"${MPDir}/ghoul2/G2.h"
	"${MPDir}/game/g_headers.h"
	"${MPDir}/game/g_ICARUScb.h"
	"${MPDir}/game/g_local.h"
	"${MPDir}/game/g_nav.h"
	"${MPDir}/game/g_public.h"
	"${MPDir}/game/g_team.h"
	"${MPDir}/icarus/interpreter.h"
	"${MPDir}/game/g_xcvar.h"
	"${MPDir}/game/inv.h"
	"${MPDir}/game/match.h"
	"${MPDir}/ui/menudef.h"
	"${MPDir}/game/npc_headers.h"
	"${MPDir}/icarus/Q3_Interface.h"
	"${MPDir}/qcommon/q_shared.h"
	"${MPDir}/game/say.h"
	"${MPDir}/game/surfaceflags.h"
	"${MPDir}/game/syn.h"
	"${MPDir}/qcommon/tags.h"
	"${MPDir}/game/teams.h"
	"${MPDir}/cgame/tr_types.h"
	"${MPDir}/game/w_saber.h"
	)
set(MPGameFiles ${MPGameSortMe})
source_group("SortMe" FILES ${MPGameSortMe})

add_library(${MPGame} SHARED ${MPGameFiles})
set_target_properties(${MPGame} PROPERTIES COMPILE_DEFINITIONS_RELEASE "${MPGameDefines};${ReleaseDefines}")
set_target_properties(${MPGame} PROPERTIES COMPILE_DEFINITIONS_DEBUG "${MPGameDefines};${DebugDefines}")
set_target_properties(${MPGame} PROPERTIES INCLUDE_DIRECTORIES "${MPGameIncludeDirectories}")
# no libraries used
if(MPGameLibraries)
	target_link_libraries(${MPGame} ${MPGameLibraries})
endif(MPGameLibraries)