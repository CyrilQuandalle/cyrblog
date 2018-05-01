---
layout: post
title: De la conteneurisation avec Docker
date: 2015-10-22 12:14
author: yakhya dabo
comments: true
categories: [Agilité, Bonnes pratiques de dév, devops, docker, Linux, Outils]
---
<p style="text-align: justify;">Il est l'un des outils qui fait le plus de buzz ces temps-ci, bien avant la première release officielle. Dans les conférences il ravit la vedette à toutes les nouveautés technologiques. De Londres à Paris les meetups font salles combles. Pas un seul jour sans un article de blog sur un retour d'expérience. Les principaux fournisseurs de cloud, de Google à Amazon, l'ont pris en compte dans leur offre. Même Microsoft a dû bidouiller son OS pour une intégration future avec Windows Server 2016. Pour beaucoup d'entreprises Docker arrive exactement à une phase cruciale. Dans un contexte <strong>DevOps</strong> où la vieille architecture monolithique cède progressivement la place aux <strong>micro services</strong> afin de permettre à des équipes <strong>Agiles</strong> d'avoir des déploiements fluides sur le <strong>Cloud</strong> grâce au <strong>Continuous Delivery</strong>.</p>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/10/20130603194143_c5.jpg"><img class="aligncenter wp-image-3561" src="http://www.arolla.fr/blog/wp-content/uploads/2015/10/20130603194143_c5-300x191.jpg" alt="" width="600" height="382" /></a></p>

<p style="text-align: justify;">Avec Docker l’unité de déploiement est un conteneur. L'objectif est de réduire le gap entre Dev et Ops, avec un packaging plus portable, facile à installer, léger, … L’environnement est identique partout, autant en dev qu'en prod. Le livrable contiendra le binaire (jar, war, deb, ...) et tout ce qui est nécessaire à son exécution, notamment l'OS. Le moteur de conteneurisation, ici Docker, s'occupera du déploiement. Différentes versions d'une même techno (Java 1.6, 1.7, 1.8, … ) peuvent coexister sur une même machine. Avec cette portabilité on reste moins l'otage du fameux “ça marche sur ma machine” et des bugs fantômes difficilement reproductibles. Pour un développeur cela permet d'explorer des chantiers nouveaux à moindre coût et d'avoir des feedbacks rapides. On pull un conteneur prêt à l'emploi, on l’expérimente et on s'en débarrasse en un temps record.</p>

<p style="text-align: justify;">Le succès de Docker est lié au fait qu'il propose une nouvelle manière de faire de l'isolation de processus par la conteneurisation. Avec la virtualisation hardware (Hyperviseur 1 et 2) des débuts de solutions ont été apportées, toutefois sans être populaires à cause de la lourdeur de leur mises en œuvre. En fait la conteneurisation est antérieure à l'outil, le mérite de Docker est de démocratiser son utilisation en rendant son implémentation plus simple.</p>

<p style="text-align: justify;">La conteneurisation ne permet pas seulement d'isoler des processus sur une même machine, elle offre aussi la possibilité de traiter un datacenter comme une machine.</p>

<h2>L'architecture de Docker</h2>

<p style="text-align: justify;">L'architecture est un simple client serveur. Pour les cas d'utilisation les plus courantes le client reçoit une commande de l'utilisateur et la transmet au daemon dans une socket. Mais Docker expose aussi une API REST qu'on peut utiliser pour les mêmes opérations.</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/10/docker-architecture-techtip39.png"><img class="aligncenter size-full wp-image-3571" src="http://www.arolla.fr/blog/wp-content/uploads/2015/10/docker-architecture-techtip39.png" alt="docker-architecture-techtip39" width="753" height="284" /></a></p>

<h2>LXC (Linux container) – Cgroup - Namespaces</h2>

<p style="text-align: justify;">Jusque dans sa version 0.8 Docker reposait sur un driver LXC, Linux conteneur. La version 0.9 utilise par défaut <strong>libcontainer</strong> de Docker même. Par opposition aux VM qui font une virtualisation au niveau physique (hardware), une technologie de conteneur comme LXC permet une virtualisation niveau OS, les processus partagent le même kernel mais disposent d'une vue locale des ressources CPU, mémoire, réseau, I/O, … De l’extérieur le conteneur peut être vu comme un processus normal mais de l’intérieur, dans son espace privé, il se comporte comme une VM. Et pour que cette technique d'isolation de processus marche le LXC va avoir besoin de deux nouveaux features, <em>Cgroup</em> et <em>Namespaces</em>, nés de travaux de Google effectués sur le Kernel depuis 2006.</p>

A un niveau différent, le chroot peut offrir le même type d'isolation.

<p style="text-align: justify;"><strong>Cgroups</strong> (aka control groups): il permet d'allouer une quantité de ressources - temps de consommation CPU, bande passante réseau, espace mémoire/disque, … — à un ensemble de processus.</p>

<p style="text-align: justify;"><strong>Namespaces :</strong> il limite un processus à son environnement, de sorte qu'il n'ait pas accès aux processus se trouvant dans d'autres conteneurs. Il ajoute donc de la sécurité, la vulnérabilité d'un processus ne contamine pas le host. Ainsi on a le Network Namespace pour garantir que chaque conteneur a sa propre interface réseau, le User Namespace qui fait que le userX du conteneur A soit différent du user X du conteneur B... le Pid Namespace qui limite les fils dans le Namespaces de leur parent.</p>

<p style="text-align: justify;"><strong>COW (Copy On Write) </strong>L'autre point de comparaison entre Docker et les VM est l'utilisation de systèmes de fichiers de type COW tels que OverlayFS, AUFS et BTRFS. L'objectif de COW est d'optimiser l'utilisation de l'espace mémoire/disque. Quand une même donnée est utilisée par un nombre n de processus cette donnée n'est pas copiée n fois. Une copie n'est faite qu'au moment où un processus tente de faire une modification. La donnée initiale reste toujours inchangée. Ce qui fait que 100 conteneurs d'une même image ne vont pas occuper un espace de 100 fois la taille de l'image. Le système de fichier de type COW permet également d'avoir une structure de données en couche qui permet de faire du cache.</p>

<p style="text-align: justify;">Ces fonctionnalités du Kernel (Namespaces, Cgroup et le COW) nous permettent de faire une comparaison objective entre VM et conteneur sur trois points :
- Occupation d'espace disque/memoire : un conteneur utilise moins d'espace.<strong><a href="https://blog.docker.com/2015/10/raspberry-pi-dockercon-challenge-winner/" target="_blank"> 2500 conteneurs sur Rasberry Pi</a>.</strong>
- Temps de lancement : du fait qu'il utilise le Kernel du host le conteneur démarre plus rapidement.
- Niveau d'isolation : la VM offre un niveau d'isolation plus élevé, dans la mesure où elle embarque son propre OS (on peut faire tourner une VM Linux sur un PC Windows).</p>

<p style="text-align: justify;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/10/Container_vs_VM.png"><img class="aligncenter wp-image-3572 size-medium" src="http://www.arolla.fr/blog/wp-content/uploads/2015/10/Container_vs_VM-300x128.png" alt="Container_vs_VM" width="300" height="128" /></a></p>

<h2>Regard Critique</h2>

<p style="text-align: justify;">Docker bénéficie d'une communauté très dynamique (meetups, DockerConf, github, twitter, ...). Des changements intéressants sont apportés d'une version à une autre. Cela n’empêche pas naturellement de voir fleurir des critiques. Le projet est encore jeune avec un important axe d’amélioration qui sera sûrement pris en compte dans les prochaines versions. On peut se plaindre déjà d'un système de <strong><a href="http://kimh.github.io/blog/en/docker/gotchas-in-writing-dockerfile-en/#build_caching_what_invalids_cache_and_not">cache très fragile</a></strong> qui peut être invalidé par une légère modification syntaxique.</p>

<p style="text-align: justify;">Sur la sécurité, un des contributeurs non moins importants du projet comme CoreOS pointe du doigt l'architecture “qui doit être radicalement repensée”. Le <em>Namespaces</em> et le <em>Cgroup</em> sur lesquels reposent Docker requièrent un accès root pour être manipulés. Si vous ajoutez à cela le fait que le daemon expose des services via l'API REST vous aurez un risque considérable de contamination du host en cas d'attaque. C'est l'un des aspects sur lesquels CoreOS veut mettre l'accent avec son projet Rocket, concurrent de Docker.</p>

<p style="text-align: justify;">Le deuxième point, toujours avec CoreOS, concerne la nouvelle politique qui ne cadre pas avec les intentions initiales du projet comme on peut le lire sur leur <strong><a href="https://coreos.com/blog/rocket/" target="_blank">blog</a></strong>.</p>

<blockquote>Docker now is building tools for launching cloud servers, systems for clustering, and a wide range of functions: building images, running images, uploading, downloading, and eventually even overlay networking, all compiled into one monolithic binary running primarily as root on your server. The standard conteneur manifesto was removed. We should stop talking about Docker conteneurs, and start talking about the Docker Platform. It is not becoming the simple composable building block we had envisioned.</blockquote>

<h2>Conclusion</h2>

<p style="text-align: justify;">Quoi qu'il en soit l’utilisation des conteneurs va continuer à se démocratiser. C'est un moyen très pratique de faire de la virtualisation à moindre frais. Tout comme les Tests Unitaires aident a réduire les couplages entres différents composants logiciels la conteneurisation d'une application , avec comme règle de base “un service par conteneur”, force à garder des services autonomes autour d'une architecture micro-services. Elle permet de détecter d’éventuelles erreurs de design, notamment celles qui violent la règle VII (<a href="http://12factor.net/port-binding" target="_blank">Port binding</a>) des twelve factors.</p>

<p style="text-align: justify;">Docker est un bon début pour commencer, même si l'aspect sécurité fait de plus en plus débat. Linus Truval répondant à une question sur la sécurité sous Linux rappelle qu'on sera appelé a faire un compromis entre la sécurité et le “plus de fonctionnalités”. Ce qui veut dire qu'il faut aussi s’intéresser aux alternatives de Docker, dans leur architecture technique et leur choix stratégique, pour faire un choix moins coûteux sur le long terme.</p>
