


# Move base Flex

Move_base_flex est un composant ROS (Robot Operating System) utilisé pour la planification de la navigation autonome dans un environnement de robot. Il fournit une solution pour la planification de la navigation autonome en utilisant des algorithmes de planification de la trajectoire et de la gestion de la carte de coûts.

![Image title](images/move_base_flex.png)



MoveBaseFlex est une extension de la bibliothèque de navigation de base de ROS (Robot Operating System), qui est également appelée MoveBase. MoveBaseFlex hérite du fonctionnement de base de MoveBase, mais ajoute des fonctionnalités supplémentaires pour prendre en charge différents algorithmes de planification et de suivi de chemin pour les robots mobiles.

MoveBaseFlex est conçu pour être plus flexible que MoveBase et peut être utilisé pour des scénarios de navigation plus complexes. Il permet de changer facilement les algorithmes de planification et de suivi de chemin en fonction des besoins spécifiques de l'application, sans avoir à modifier le code source.

Voir -> [Documentation Move_Base_Flex]( https://uos.github.io/mbf_docs/)


    source devel/setup.bash
    export TURTLEBOT3_MODEL=rescue
    roslaunch turtlebot3_gazebo turtlebot3_world.launch


##
    source devel/setup.bash
    export TURTLEBOT3_MODEL=rescue
    roslaunch mbf_advanced amcl_demo_mbf_recovery.launch 

##

    source devel/setup.bash
    rosrun mbf_advanced pytreesRecovery.py 

##


    rosrun rqt_py_trees rqt_py_trees



# Move base Client

Le launch file suivant lance  "move_base_flex". Ce noeud appartient au package "mbf_costmap_nav" et utilise le type "mbf_costmap_nav".

Le noeud charge plusieurs fichiers de paramètres ROS à l'aide de la commande "rosparam". Les fichiers de paramètres définissent les comportements du noeud "move_base_flex", tels que les paramètres de coûts pour les cartes locales et globales de l'environnement ainsi que les paramètres de planification de la base locale. Les paramètres de récupération "move_base_flex_recovery" sont également chargés pour définir les comportements de récupération.


``` xml
  <node name="move_base_flex" pkg="mbf_costmap_nav" type="mbf_costmap_nav" required="true" output="screen" clear_params="true">

    <rosparam file="$(find mbf_advanced)/param/move_base_classic/costmap_common_params_$(arg model).yaml" command="load" ns="global_costmap" />
    <rosparam file="$(find mbf_advanced)/param/move_base_classic/costmap_common_params_$(arg model).yaml" command="load" ns="local_costmap" />
    <rosparam file="$(find mbf_advanced)/param/move_base_classic/local_costmap_params.yaml" command="load" />
    <rosparam file="$(find mbf_advanced)/param/move_base_classic/global_costmap_params.yaml" command="load" />
    <rosparam file="$(find mbf_advanced)/param/move_base_flex_recovery.yaml" command="load"/>
    <rosparam file="$(find mbf_advanced)/param/move_base_classic/base_local_planner_params.yaml" command="load" />


  </node>

```

Nous incluons les different paramètre necessaire pour parametre nos plugins







La premiere etape et de se soscrire au topic poseStamped qui peremt d'envyer un goal
et d'envoyer ce goal a move base en utilisant les action de action lib


``` py
#!/usr/bin/env python

import actionlib
import rospy
import nav_msgs.srv as nav_srvs
import mbf_msgs.msg as mbf_msgs
import move_base_msgs.msg as mb_msgs
from geometry_msgs.msg import PoseStamped


def simple_goal_cb(msg):
    
    mbf_mb_ac.send_goal(mbf_msgs.MoveBaseGoal(target_pose=msg))
    rospy.logdebug("Relaying move_base_simple/goal pose to mbf")

    mbf_mb_ac.wait_for_result()

    status = mbf_mb_ac.get_state()
    result = mbf_mb_ac.get_result()

    rospy.logdebug("MBF execution completed with result [%d]: %s", result.outcome, result.message)

if __name__ == '__main__':
    rospy.init_node("move_base_relay")

    # move base flex ation client relays incoming mb goals to mbf
    mbf_mb_ac = actionlib.SimpleActionClient("move_base_flex/move_base", mbf_msgs.MoveBaseAction)
    mbf_mb_ac.wait_for_server(rospy.Duration(20))

    # move_base simple topic and action server
    mb_sg = rospy.Subscriber('move_base_simple/goal', PoseStamped, simple_goal_cb)

    rospy.on_shutdown(lambda: mbf_mb_ac.cancel_all_goals())

    rospy.spin()sd
```


Explication du code:

``` py    

mb_sg = rospy.Subscriber('move_base_simple/goal', PoseStamped, simple_goal_cb)

```

On créer un subsciber "mb_sb" qui souscit au topic "move_base_simple/goal" de type PoseStamped dont le CallBack et la fonction simple_goal_cb

``` py    

mbf_mb_ac = actionlib.SimpleActionClient("move_base_flex/move_base", mbf_msgs.MoveBaseAction)
mbf_mb_ac.wait_for_server(rospy.Duration(20))

```

On instancit un client mbf_mb_ac  qui va se connecter au node (serveur) /move_base_flex  avec commme action :
/move_base_flex/move_base

Le node move_base_flex dispose de plusieur action (fichier source dans /move_base_flex/mbf_abstract_nav/src/move_base_action.cpp)


Whatch py_tree doc
http://docs.ros.org/en/kinetic/api/py_trees_ros/html/tutorials.html