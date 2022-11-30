# Move base Client



``` xml
<?xml version="1.0"?>

<launch>
  <!-- Base amcl demo -->
  <include file="$(find mbf_advanced)/launch/amcl_demo_base.launch"/>

  <!-- Move Base Flex -->
  <include file="$(find mbf_advanced)/launch/mbf_recovery.launch"/>
</launch>


```
Titre : mbf.launch


``` xml
<launch>
    <!-- Arguments -->
  <arg name="model" default="$(env TURTLEBOT3_MODEL)" doc="model type [burger, waffle, waffle_pi]"/>

  <node name="move_base_flex" pkg="mbf_costmap_nav" type="mbf_costmap_nav" required="true" output="screen" clear_params="true">
    <rosparam file="$(find turtlebot3_navigation)/param/costmap_common_params_$(arg model).yaml" command="load" ns="global_costmap" />
    <rosparam file="$(find turtlebot3_navigation)/param/costmap_common_params_$(arg model).yaml" command="load" ns="local_costmap" />
    <rosparam file="$(find turtlebot3_navigation)/param/local_costmap_params.yaml" command="load" />
    <rosparam file="$(find turtlebot3_navigation)/param/global_costmap_params.yaml" command="load" />
    <rosparam file="$(find mbf_advanced)/param/move_base_flex_recovery.yaml" command="load"/>
  </node>
  
  <!-- Launch box into gazebo -->
  <include file="$(find mbf_advanced)/launch/spawn_box.launch"/>
</launch>
```







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