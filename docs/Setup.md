

Créations d ‘un Workspace que nous allons appeler move_base_ws

schema simplifier 



    mkdir moveBase_ws
    cd moveBase_ws

Création d’un fichier source:

    mkdir src

Dans source nous allons créer un fichier  allons appeler mvb_ws:

    cd src
    mkdir mvb_pkg

nous allons copier dans notre package les fichier source de navigation

    sudo git clone https://github.com/ros-planning/navigation.git
Il est également possible d’installer les fichier source directement avec apt-get
    XXXXX

nous allons ajouter le paquet turtlebot 3 utilisants move_base

    sudo git clone https://github.com/ROBOTIS-GIT/turtlebot3.git

Enfin nous allons cloner le paquet turtlebot permettant la simulation dans l'environnement 3d gazebo 

    sudo git clone https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git

