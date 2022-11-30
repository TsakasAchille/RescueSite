
Dans turtlebot3_navigation -> turtlebot3_gazebo nous avons plusieur environnements de simulation 


![Image title](images/1.png){ align=left }

Dans launch nous avons les different fichier d'exécution des environnement de simulation

Dans model nous avons les modeles 3d de robot et d'élément de l’environnement

Dans worlds nous avons les different monde 3d

Tous les elemeents 3D sont codée en XML dans un format URDF



#Launch File 

Pour commencer nous allons lanver une simulation gazebo dans le monde trurtlebot word 3

``` xml
    <launch>
    <arg name="model" default="$(env TURTLEBOT3_MODEL)" doc="model type [burger, waffle, waffle_pi]"/>
    <arg name="x_pos" default="-2.0"/>
    <arg name="y_pos" default="-0.5"/>
    <arg name="z_pos" default="0.0"/>
```

Ici, le launch file attend un argument “model” qui nous laisse le choix entre different modele de robot 
[burger, waffle, waffle_pi],  
ces modèles sont present dans ls fichier models

Les trois arguments suivatn x_pos, y_pos, et z_pos sont la position ou va apparaitre notre robot dans la map

``` xml
  <include file="$(find gazebo_ros)/launch/empty_world.launch">
        <arg name="world_name" value="$(find turtlebot3_gazebo)/worlds/turtlebot3_world.world"/>
        <arg name="paused" value="false"/>
        <arg name="use_sim_time" value="true"/>
        <arg name="gui" value="true"/>
        <arg name="headless" value="false"/>
        <arg name="debug" value="false"/>
    </include>
    
```


  



On inclu le launch file empty_word.launch qui va demarer une simulation, ce launch file prend plusieur argument d’entrée comme la map utilisé, “word_name”
ici la valeur de notre argument va chercher un model monde present dans le fichier wordls.
l’agument “gui” mis a “true” permet de lancer l’interface graphique de la simulation


``` xml

    <param name="robot_description" command="$(find xacro)/xacro --inorder $(find turtlebot3_description)/urdf/turtlebot3_$(arg model).urdf.xacro" />

    <node pkg="gazebo_ros" type="spawn_model" name="spawn_urdf"  args="-urdf -model turtlebot3_$(arg model) -x $(arg x_pos) -y $(arg y_pos) -z $(arg z_pos) -param robot_description" />
    </launch>
```


Nous lançons le node spawn_urdf du packet gazebo_ros qui prend comme parametre “robot description” , ce paramètre pointe vers l’urdf de notre robot
ici, si nous choisision le robot burger, l’urdf sera :
turtlebot3_burger.urdf.xacro


Ce fichier se trouve dans turtlebo3/turtlebot3_description/urdf  , il est la description du robot au format URDF, dans l’entete du fichier nous avons deux includes important:


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



# Lancement

Nous allons lancer la simulation:

On choisi le modèle du robot que l’on souhaite parmis la liste suivant [burger, waffle, waffle_pi]

    export TURTLEBOT3_MODEL=burger

On lance la simulation:

    roslaunch turtlebot3_gazebo turtlebot3_world.launch 



Voici le résultsat du gui Gazeo

![Alt Text](images/GazeboBurger.gif)


