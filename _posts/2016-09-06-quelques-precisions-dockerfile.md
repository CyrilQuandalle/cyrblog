---
layout: post
title: Quelques précisions sur le Dockerfile
date: 2016-09-06 09:31
author: yakhya dabo
comments: true
categories: [Bonnes pratiques de dév, devops, docker, Linux, outils, Outils, tools]
---
Précédemment, dans la présentation de Docker et de la technologie des conteneurs, on avait parlé des features sur lesquelles reposent Docker. Le <strong>Cgroup</strong> et le <strong>Namespaces</strong> pour l’isolation de processus et le système de fichier <strong>COW</strong> pour l’optimisation de l’espace disque. Dans cet article, on passera à la loupe le Dockerfile, le fichier de description d’une image et d’un conteneur Docker . Même si le nombre de commandes est réduit, leur subtilité n’apparaît pas à la première utilisation. Le site officiel demeure la meilleure source de documentation. On ne s’attardera que sur les commandes de base - <strong>ADD</strong>, <strong>COPY</strong>, <strong>CMD</strong> et <strong>ENTRYPOINT</strong>.

<div id="content">
<div class="sect1">
<div class="sectionbody">
<div class="paragraph"></div>
<div class="sidebarblock">
<div class="content">
<div class="paragraph">

Les exemples ont été utilisés avec la version 1.11 de Docker. Des mises à jours seront éventuellement apportées si nécessaire.

</div>
</div>
</div>
<div class="sect2">
<h3 id="__strong_comprendre_les_layers_strong"><strong>Comprendre les layers :</strong></h3>
<div class="paragraph">

Dans le Dockerfile, chaque commande (ADD, COPY, ENTRYPOINT, CMD, …) fait l’objet d’ajout d’une nouvelle couche (COW) à l’image. Et les couches, moins on en a, mieux c’est pour la taille des images. Sachant qu'en plus, le nombre de couches a une limite (42 auparavant et 127 depuis peu). Même si la contrainte du nombre de lignes (ou couches) est quasiment écartée (si vous avez un Dockerfile avec 127 lignes, c'est qu'il y a un vrai problème !), regrouper les commandes facilite la compréhension du Dockerfile.

</div>
<div class="paragraph">

Tip : Regrouper les commandes autant que possible

</div>
<div class="exampleblock">
<div class="title">Example 1. Dockerfile (avec 4 couches)</div>
<div class="content">
<div class="listingblock">
<div class="content">
<pre class="highlight"><code class="shell language-shell">RUN curl -fsSL http://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz
RUN tar xzf apache-maven-$MAVEN_VERSION-bin.tar.gz  - -C /usr/share
RUN mv /usr/share/apache-maven-$MAVEN_VERSION /usr/share/maven
RUN ln -s /usr/share/maven/bin/mvn /usr/bin/mvn</code></pre>
</div>
</div>
</div>
</div>
<div class="exampleblock">
<div class="title">Example 2. Dockerfile “refactore” en une seule commande</div>
<div class="content">
<div class="listingblock">
<div class="content">
<pre class="highlight"><code class="shell language-shell">RUN curl -fsSL http://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar xzf - -C /usr/share \
  &amp;&amp; mv /usr/share/apache-maven-$MAVEN_VERSION /usr/share/maven \
  &amp;&amp; ln -s /usr/share/maven/bin/mvn /usr/bin/mvn</code></pre>
</div>
</div>
</div>
</div>
<div class="paragraph">

On réduit le nombre de couches a une et on a gagne en lisibilité.

</div>
</div>
<div class="sect2">
<h3 id="__strong_le_cache_strong"><strong>Le cache</strong></h3>
<div class="paragraph">

Toutes les couches d’une image sont mises dans le cache. Quand on lance le build d’une image Docker, il faut d’abord commencer par chercher dans le cache des couches qu’il pourrait réutiliser pour optimiser le temps de construction de l’image en évitant les téléchargements inutiles. De ce fait, en cas de modification, seules les lignes concernées par la modification seront réexecutées.

</div>
<div class="exampleblock">
<div class="content">
<div class="listingblock">
<div class="content">
<pre class="highlight"><code class="shell language-shell">FROM java:openjdk-8-jdk
MAINTAINER Yakhya DABO
ENV MAVEN_VERSION 3.3.3
ENV M2_HOME /usr/share/maven
ENV PROJECT_DIR /usr/src/app
...

...
8. RUN mkdir -p $PROJECT_DIR
9. COPY config/settings.xml $M2_HOME/conf/
10. RUN curl -fsSL http://archive.apache.org/dist/maven/maven-3/$MAVEN_VERSION/binaries/apache-maven-$MAVEN_VERSION-bin.tar.gz | tar xzf - -C /usr/share \
  &amp;&amp; mv /usr/share/apache-maven-$MAVEN_VERSION /usr/share/maven \
  &amp;&amp; ln -s /usr/share/maven/bin/mvn /usr/bin/mvn
11. VOLUME $PROJECT_DIR
12. WORKDIR $PROJECT_DIR</code></pre>
</div>
</div>
</div>
</div>
<div class="paragraph">

Si on apporte une modification sur la ligne 9, le cache invalide toutes ses filles, les lignes 10, 11, … qui seront réexecutées. Les lignes 0, 1, … 8 n'étant pas des filles de 9, elles ne seront pas concernées par les modifications, c'est le cache qui sera utilisé.

</div>
<div class="paragraph">

<strong>Deux astuces pour bien profiter du cache :</strong>

</div>
<div class="olist arabic">
<ol class="arabic">
    <li>Placer les lignes qui changent souvent le plus bas possible (ajout d’un jar par exemple, COPY).</li>
    <li>Mettre les opérations coûteuses le plus haut possible dans le Dockerfile afin d'éviter de les réexécuter à chaque modification (téléchargement par exemple, RUN curl).</li>
</ol>
</div>
</div>
<div class="sect2">
<h3 id="__strong_commandes_add_et_copy_strong"><strong>Commandes ADD et COPY</strong></h3>
<div class="paragraph">

Ces deux commandes sont facilement sujet à confusion, de par leur nom mais aussi de par leur syntaxe : elles semblent faire la même chose, sauf que dans la pratique, ce n'est pas tout le temps le cas.

</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code class="shell language-shell"> ADD  src dest
 COPY   src dest</code></pre>
</div>
</div>
<div class="paragraph">

<strong>src :</strong> un répertoire (ou fichier) du host
<strong>dest :</strong> un répertoire (ou fichier) du conteneur

</div>
<div class="paragraph">

Une petite précision : dans la commande “docker build”, on spécifie le context du Dockerfile.

</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code> $ docker build -f dockerfileDir contextDir</code></pre>
</div>
</div>
<div class="paragraph">

ou

</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code> $ docker build contextDir  // (si contextDir = buildDir)</code></pre>
</div>
</div>
<div class="paragraph">

<strong>src</strong> est relatif au contextDir.

</div>
<div class="paragraph">

<strong>dest</strong> est soit un nom de fichier (/var/opt/fileName), soit un répertoire (/var/opt/).

</div>
<div class="paragraph">

<strong>COPY</strong> se contente tout simplement de prendre un fichier (ou répertoire) du host et de le mettre dans le conteneur.

</div>
<div class="paragraph">

<strong>ADD</strong> peut aussi faire la même chose, mais le <strong><em>src</em></strong> peut être une URL. Dans ce cas, docker se charge du téléchargement et de placer le fichier téléchargé dans <strong><em>dest</em></strong>. Si <strong><em>src</em></strong> est un fichier zippé et <strong><em>dest</em></strong> un répertoire docker dezzipe <strong><em>src</em></strong> (ADD file.zip /var/opt/). Un peu confus tout ça …

</div>
<div class="paragraph">

La documentation de Docker conseille d’utiliser COPY au profit de ADD, sauf pour des cas spécifiques. Mais pour quelqu'un qui a un peu baigné dans la philosophie Unix (faire une et une seule chose) ou le principe SRP, on voit que ADD c’est du legacy, donc inutile. Mieux vaut ne pas l’avoir dans son Dockerfile.

</div>
<div class="paragraph">

On peut se contenter de <strong><em>COPY</em></strong> pour les opérations simples de copie du host vers le conteneur, et de coupler <strong><em>RUN</em></strong> avec les utilitaires existants tels que <strong>tar</strong>, <strong>unzip</strong>, <strong>wget</strong>, <strong>curl</strong>, … si on a besoin de zipper ou de télécharger des fichiers.

</div>
</div>
<div class="sect2">
<h3 id="__strong_commandes_cmd_et_entrypoint_strong"><strong>Commandes CMD et ENTRYPOINT</strong></h3>
<div class="paragraph">

Dans la pratique, les deux commandes peuvent avoir le même résultat: exécuter le script de démarrage du conteneur. Mais d’après la doc, <a href="https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#entrypoint">Entrypoint</a> sert à configurer un container au démarrage, et <a href="https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#cmd">CMD</a> est utilisé pour définir la commande de démarrage par défaut du conteneur.

</div>
<div class="exampleblock">
<div class="title">Example 3. Utilisation de CMD</div>
<div class="content">
<div class="listingblock">
<div class="content">
<pre class="highlight"><code class="shell language-shell">FROM maven:3.3.3-jdk-8
WORKDIR projectDir
...
CMD ["mvn clean install”]</code></pre>
</div>
</div>
</div>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>$ docker run my_maven_image va exécuter mvn clean install</code></pre>
</div>
</div>
<div class="paragraph">

… et si on veut surcharger cette commande …

</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>$ docker run my_maven_image mvn clean verify</code></pre>
</div>
</div>
<div class="exampleblock">
<div class="title">Example 4. Utilisation de ENTRYPOINT</div>
<div class="content">
<div class="paragraph">

Pour mon conteneur git, j'aurais besoin des paramètres du committeur (user.name et user.email) au moment de lancer le container. Je peux donc utiliser un entrypoint pour fixer ces deux paramètres.

</div>
</div>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code class="shell language-shell">FROM git:2.0
….
COPY entrypoint.sh /var/lib
ENTRYPOINT [“/var/lib/entrypoint.sh”]</code></pre>
</div>
</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code>$ docker run -e GIT_USER_NAME=username -e GIT_USER_EMAIL=email my_git_image git commit -m “xxxxx”</code></pre>
</div>
</div>
<div class="paragraph">

Le contenu de mon fichier entrypoint.sh :

</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code class="shell language-shell"> #!/bin/bash

 set -e

 git config user.name "$GIT_USER_NAME"
 git config user.email "$GIT_USER_EMAIL"

 exec "$@"</code></pre>
</div>
</div>
<div class="paragraph">

Il est important de noter que <strong>ENTRYPOINT</strong> utilise <strong>CMD</strong> comme argument ("$@"). Sa valeur par défaut est <strong><em>/bin/sh -c</em></strong>, qui prend en paramètre une commande. Ce qui fait que quand on ne définit pas notre propre <strong>ENTRYPOINT</strong> (dans le Dockerfile ou en paramètre a docker run avec –entrypoint), <strong>CMD</strong> devient la commande a exécuter (éventuellement avec ses paramètres).

</div>
<div class="exampleblock">
<div class="title">Exemple 5. Avec New Relic</div>
<div class="content">
<div class="paragraph">

J’utilise New Relic en Prod pour le monitoring de la JVM de mon conteneur. Mais je veux aussi avoir le choix de m'en passer quand je n'en ai pas besoin, en Dev par exemple.

</div>
</div>
</div>
<div class="ulist">
<ul>
    <li>La solution la plus simple pourrait être d’avoir deux images différentes, une pour la Prod, avec New Relic, et la seconde pour le Dev, sans New Relic. Mais cette option ne respecte pas les principes du Continuous Delivery, <em>“le livrable doit être le même dans tous les envs”</em>.</li>
    <li>Une deuxième solution, celle que je préfère, sera d’utiliser ENTRYPOINT pour décider de lancer ou non New Relic selon que les variables <strong>NEWRELIC_KEY</strong> et <strong>NEWRELIC_APP_NAME</strong> sont spécifiées ou non.</li>
</ul>
</div>
<div class="paragraph">

Pour lancer le container en Prod :

</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code> $ docker run -e  NEWRELIC_KEY=XXXXXXXX -e  NEWRELIC_APP_NAME=my_app_name my_service_image</code></pre>
</div>
</div>
<div class="paragraph">

… et en Dev :

</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code> $ docker run -e my_service_image</code></pre>
</div>
</div>
<div class="paragraph">

Dans mon ENTRYPOINT, je peux avoir le script d’initialisation de l'environnement d’exécution pour positionner les paramètres des fichiers de config avec les variables d'environnements passées en paramètres (nom, cle, url, mot de passe, login, …) et CMD pour spécifier la commande à exécuter après l’initialisation de l’environnement.

</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code class="shell language-shell">Dockerfile
….
ENTRYPOINT [“entrypoint.sh”]
CMD […...........]</code></pre>
</div>
</div>
<div class="paragraph">

entrypoint.sh

</div>
<div class="listingblock">
<div class="content">
<pre class="highlight"><code class="shell language-shell">#!/bin/sh

set -e


if [ -z "$NEWRELIC_KEY" ]; then
        java -Djava.security.egd=file:/dev/./urandom -jar app.jar
else
        if [ -z "$NEWRELIC_APP_NAME" ]; then
                echo &gt;&amp;2 'error: missing required environment variable'
                echo &gt;&amp;2 'error: NEWRELIC_APP_NAME must be set when using New Relic'
                exit 1
        fi

        NEW_RELIC_CONFIG_FILE=$NEW_RELIC_DIR/newrelic.yml
       cp $NEW_RELIC_CONFIG_FILE $NEW_RELIC_CONFIG_FILE.original

        # Override key and app_name
        sed -i -e "s/app_name:\ My\ Application/app_name:\ ${NEWRELIC_APP_NAME}/g" $NEW_RELIC_CONFIG_FILE
        sed -i -e "s/'&lt;\%= license_key \%&gt;'/${NEWRELIC_KEY}/g" $NEW_RELIC_CONFIG_FILE

        exec "$@"
fi</code></pre>
</div>
</div>
</div>
<div class="sect2">
<h3 id="__strong_ce_qu_il_faut_retenir_strong"><strong>Ce qu'il faut retenir …</strong></h3>
<div class="paragraph">

Comprendre le fonctionnement du cache est important pour réduire le temps de build des images, une contrainte essentielle pour faire du Continuous Delivery.

</div>
<div class="paragraph">

Toujours utiliser la commande <strong>COPY</strong> à la place de <strong>ADD</strong>.

</div>
<div class="paragraph">

Se limiter à <strong>CMD</strong> pour les commandes simples, sans besoin de configuration du conteneur. Et utiliser <strong>ENTRYPOINT</strong> + <strong>CMD</strong> quand on a besoin d’appliquer des configurations au conteneur avant de le lancer.

</div>
</div>
</div>
</div>
</div>

&nbsp;
