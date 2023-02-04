



# URDF



``` xml
<?xml version="1.0" ?>
<robot name="turtlebot3_burger" xmlns:xacro="http://ros.org/wiki/xacro">
  <xacro:include filename="$(find turtlebot3_description)/urdf/common_properties.xacro"/>
  <xacro:include filename="$(find turtlebot3_description)/urdf/turtlebot3_burger.gazebo.xacro"/>
```

Le premier, common_properties.xacro permet de definir la couleur des links utilisé en code RGB

Le deuxieme, turtlebot3_burger.gazebo.xacro, contient les paramètres physiques appliqué à nos joint comme le frotement ou l’inertie et des plugin utilisé lors de la simulation.

###^^Plugins^^

-  ^^turtlebot3_burger_controller^^

Il permet de contrôler le robot en publiant dasn un topic et de generer une odométrie


``` xml
<gazebo>
<plugin name="turtlebot3_burger_controller" filename="libgazebo_ros_diff_drive.so">
    <commandTopic>cmd_vel</commandTopic>
    <odometryTopic>odom</odometryTopic>
    <odometryFrame>odom</odometryFrame>
    <odometrySource>world</odometrySource>
    <publishOdomTF>true</publishOdomTF>
    <robotBaseFrame>base_footprint</robotBaseFrame>
    <publishWheelTF>false</publishWheelTF>
    <publishTf>true</publishTf>
    <publishWheelJointState>true</publishWheelJointState>
    <legacyMode>false</legacyMode>
    <updateRate>30</updateRate>
    <leftJoint>wheel_left_joint</leftJoint>
    <rightJoint>wheel_right_joint</rightJoint>
    <wheelSeparation>0.160</wheelSeparation>
    <wheelDiameter>0.066</wheelDiameter>
    <wheelAcceleration>1</wheelAcceleration>
    <wheelTorque>10</wheelTorque>
    <rosDebugLevel>na</rosDebugLevel>
</plugin>
</gazebo>
```


 
Ici nous pouvons controler le robot en publiant dans “cmd_vel”, le topic odométrie et “odom” et la transformation ce fait entre “odom” et “base_footprint”

-  ^^gazebo_ros_lds_lfcd_controller^^

Ce plugin  permet de simuler un lidar: 


``` xml
 <plugin name="gazebo_ros_lds_lfcd_controller" filename="libgazebo_ros_laser.so">
        <topicName>scan</topicName>
        <frameName>base_scan</frameName>
      </plugin>

```

 
Il genère un topic “scan” dont le nom est definti dans **TopicName**,  le scan ce fait dans le plan definti par le link **frameName** ici,  “base-scan”.




