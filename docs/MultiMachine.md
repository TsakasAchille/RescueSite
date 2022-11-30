# Communication Multimachine

En robotique il est souvent necessaire d'avoir plusieur machines qui communiquent entre elles. Dans notre cas nous avons un robot mobile qui doit communiquer avec une console deporté

Il exsite plusieur façon de communiquer entre machine avec ROS

Nous allons decrire quelque une de ces methodes en presentant leur avantages et inconveniants

Dans toutes ces méthodes les machines partage le même reseau.

## 1. Utilisation d'une machine "maître"

La méthode la plus simple et d'utiliser le master d'une machine et d'exporter ce master au niveau des autres machines


<figure markdown>
  ![Image title](images/RosMasterUnique.png){ width="300" }
  <figcaption>Image caption</figcaption>
</figure>


Au niveau de la machine maître, dans un terminal:

    export ROS_HOSTNAME={IP MAITRE}
    export ROS_MASTER_URI=http://{IP MAITRE}:11311



Au niveau de la machine esclave, dans un terminal:


    export ROS_HOSTNAME={IP ESCLAVE}
    export ROS_MASTER_URI=http://{IP MAITRE}:11311



Prenons un exemple, nous avons deux machine, un robot et une console, d'adresse IP respective suivante :
192.168.1.1 et 192.168.1.2 

![Image title](images/RosMasterUnique2.png)

Ici le master sera celui de la console.

Au niveau de la console, dans un terminal:

    export ROS_HOSTNAME=192.168.1.1
    export ROS_MASTER_URI=http://192.168.1.1:11311


Au niveau du robot, dans un terminal:


    export ROS_HOSTNAME=192.168.1.2
    export ROS_MASTER_URI=http://192.168.1.1:11311


Ici dans la deuxième commande on exporte le master de la console. Ainsi notre robot utilisera le master de notre console.
### Avantage

Cette méthode et simple et rapide à mettre en place.


### Inconveniants

Le problème est l'ulisation d'un master unique. En effet si une machine "esclave" pert la connection avec la machine "maître" alors elle ne peut plus utiliser les nodes ROS. Dans notre cas si le robot s'eloigne de notre console est se deconnecte alors il ne pourra plus utiliser ROS.



## 2. Utilisation de la classe c++ ros::master

Cette méthode plus compliqué mais elle permet d'utiliser plusieurs master.
Pour cela une machine se connecte au master d'une machine "cible" souscrit a un topic et publie une copie de ce même topic dans son environement local (master local)

Il existe une methode de la classe ros,  ``` ros::master::init() ```  qui peremt de se connecte à un autre master dans le même reseau.

Attention, pour utiliser cette méthode il important d'ajouter au niveau de chaque machine dans ```/etc/hosts ``` les adresses IP des autres machines

####  Exemple de code :

dans le code suivant une console se connecte au master d'un robot, souscirt a son topic SCAN et publie une copie de ce topic dans son environnement local.

```cpp



int main(int argc, char **argv)
{
    // On initialse notre node scan_copieur dans l'environnement local
    ros::init(argc,argv,"scan_copieur");
    ros::NodeHandle nh;


    // on créer un objet de notre classe PublisherLocal, on appel ainsi le constructeur qui créé un publisher local
    PublisherLocal PublisherLocal;


 
    //On declare les deux environemments
    std::string robot_master = "http://192.168.1.4::11311";
    std::string console_master= "http://192.168.1.5::11311";

    //On crée une varibale map M_string et on insere à l'index __master le master auquel on veux se connecter
    ros::M_string remappings; 
    remappings["__master"] = robot_master;

    //On se connecte au master souhaité
    ros::master::init(remappings);

    //On souscrit au topic Scan, ici on souscrit dans l'environnement du robot_master
    ros::Subscriber subscriberFeedback = nh.subscribe("/scan", 1000, &PublisherLocal::callback, &hostTopic);  

    [....]

}

class PublisherLocal{

    public:
        PublisherLocal();
        ~PublisherLocal();
        void callback(const sensor_msgs::LaserScan msg);    
        ros::Publisher pub;
      

    private:
        ros::NodeHandle n;

};


PublisherLocal::PublisherLocal() {
    pub = n.advertise<sensor_msgs::LaserScan>("/scan", 1000);
}

PublisherLocal::~PublisherLocal(){}


void PublisherLocal::callback(const sensor_msgs::LaserScan msg){ 

    if(ok == 1){
        oldSeq = msg.header.seq;
        ok = 0;
    }
    else{
        newSeq = msg.header.seq;
        ok = 1;
    }

    if(newSeq > oldSeq)
            pub.publish(msg);
    

}




``` 

Voir code complet -> [lien git hub]


 

# 3. Utilisation de ROS Bridge


