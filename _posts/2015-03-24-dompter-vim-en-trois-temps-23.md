---
layout: post
title: Dompter VIM en trois temps 2/3
date: 2015-03-24 11:14
author: yakhya dabo
comments: true
categories: [Bonnes pratiques de dév, editeur, Linux, outils, Outils, Programmation, vi, vim]
---
<h2 style="text-align: center;">Temps 2 : Grok your VIM</h2>

<p style="text-align: justify;">Après avoir été convaincu de <em><strong><a href="http://www.arolla.fr/blog/2015/01/dompter-vim-en-trois-temps-03/" target="_blank">changer votre éditeur pour Vim</a></strong></em> et avoir pris connaissance de quelques <em><strong><a href="http://www.arolla.fr/blog/2015/02/dompter-vim-en-trois-temps-13/" target="_blank">commandes de bases</a></strong></em>, place à la phase suivante : la configuration.</p>

<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/03/vim_love.png"><img class="aligncenter size-full wp-image-3118" src="http://www.arolla.fr/blog/wp-content/uploads/2015/03/vim_love.png" alt="vim_love" width="450" height="300" /></a>

<p style="text-align: justify;">On n'écrit pas que du code dans notre travail. On s'exprime aussi par écrit, dans un langage plus humain, pour échanger avec d'autres collaborateurs qui ne sont pas tout le temps du même métier; on n'est pas dispensé de la maîtrise de l'orthographe. Voyons ce que le correcteur d'orthographe de VIM propose pour le spell checking.</p>

Pour l'activation/désactivation …

<strong>:set spell</strong>
<strong> :set nopell</strong>

… et pour les commandes d'utilisation

<strong>z=</strong> voir les suggestions sur le mot mal orthographié
<strong>]s</strong> se mettre sur le prochain mot mal orthographié
<strong>[s</strong> se mettre sur le précédent mot mal orthographié
<strong>zg</strong> ajouter le mot au dictionnaire
<strong>zug</strong> annuler l'ajout du mot au dictionnaire

<p style="text-align: justify;">Le spell checking parait ici contre-intuitif. Pour chacune des commandes, on a au minimum deux caractères à taper, y compris parfois un caractère spécial (“]”,”[“ ou “=”). Utiliser Vim de cette manière peut être très vite déroutant. Les premiers signes de frustration font leur apparition, comme cet utilisateur qui se plaint sur Stackoverflow d'être plus productif avec ses précédents éditeurs qu'avec VIM qu'on lui a pourtant présenté comme étant l'éditeur du développeur...</p>

<blockquote><span style="color: #003366;">I've heard a lot about Vim, both pros and cons. It really seems you should be (as a developer) faster with Vim than with any other editor. I'm using Vim to do some basic stuff and I'm at best 10 times less productive with Vim.</span></blockquote>

… Et la réponse qui a suivi résume l'idée de cet article :

<blockquote><span style="color: #003366;">Your problem with Vim is that you don't grok vi.</span></blockquote>

<a href="http://stackoverflow.com/questions/1218390/what-is-your-most-productive-shortcut-with-vim" target="_blank">http://stackoverflow.com/questions/1218390/what-is-your-most-productive-shortcut-with-vim</a>

<p style="text-align: justify;">Sans une configuration minimale l'éditeur reste très peu pratique. Pour découvrir la vraie puissance de VIM il faut l'adapter à ses habitudes. Le travail commence dans le fichier de config ~/.vimrc qui joue le même rôle que ~/.gitconfig pour Git ou ~/.bashrc pour Linux. On peut trouver des fichiers .vimrc sur github mais il vaut mieux en prendre un qui soit basique et l'enrichir au fur et à mesure. Cela doit refléter une habitude qui n'est pas universelle. On trouvera principalement dans le fichier des configs de bases, des mappings et des macros (fonctions).</p>

<h1><strong>Configuration de base</strong></h1>

<p style="text-align: justify;">Dans la partie configuration on met les options par défaut, celles dont on a besoin le plus souvent. Les options rarement utilisées pourront être activées manuellement.</p>

<p style="text-align: justify;"><strong>Exemple :</strong> Puis qu'il est plus pratique de se repérer à partir de la position courante que des numéros de lignes, on choisira par défaut l'affichage des numéros de lignes en mode relatif.</p>

<strong>set relativeNumber</strong>

<p style="text-align: justify;">Si au cour d'une session on veut passer à l'affichage en mode absolu on exécute les deux commandes (en mode command-line):</p>

<strong>:set norelativenumber</strong>
<strong> :set nu</strong>

On va essayer de voir d'autres options que l'on peut avoir besoin de spécifier dans le fichier de config.

Faire une recherche intelligente
<strong>set ignorecase</strong>
<strong> set smartcase</strong>

<p style="text-align: justify;">Avec cette configuration si le mot recherché est en minuscule la casse n'est pas prise en compte. Par contre si une des lettres du mot est en majuscule on a la recherche en case-sensitive.</p>

<p style="text-align: justify;">Ne pas rendre VIM compatible avec Vi. Si on active la compatibilité on perd les fonctionnalités de VIM qui n'existent pas dans VI.
<strong>set nocompatible</strong></p>

Mettre en évidence la position du curseur avec une ligne horizontale.
<strong>set cursorline</strong>

Afficher la commande sur la barre d'état.
<strong>set showcmd</strong>

Mettre en évidence les parenthèses qui correspondent.
<strong>set showmatch</strong>

Activer la recherche incrémentale. La recherche s'effectue au fur et à mesure qu'on fait la saisie.
<strong>set incsearch</strong>

Masquer les buffers quand ils ne sont plus utilisés.
<strong>set hidden</strong>

Définir la couleur de fond.
<strong>set background=dark</strong>

Choisir l'encoding pour l'affichage et l'encoding pour les fichiers.
<strong>set encoding=utf8</strong>
<strong> set fileencoding=utf-8</strong>

Prendre le dictionnaire français pour le spell checking.
<strong>set spell spelllang=fr_fr</strong>

Mettre 4 espaces pour l'indentation. On utilise soit la tabulation (tabstop) soit "<strong>&gt;&gt;</strong>" (shiftwidth).
<strong>set tabstop=4</strong>
<strong> set shiftwidth=4</strong>

80, le nombre maximal de caractères par ligne.
<strong>set textwidth=80</strong>

Ne pas couper un mot pour aller à la ligne.
<strong>set linebreak</strong>

Par défaut leader est mappé en "<span style="color: #800000;"><strong>\</strong></span>". Il est remappé en "<span style="color: #800000;">,</span>" pour une utilisation plus simple.
<strong>let mapleader = “,”</strong>

<h1><strong>Du Mapping pour aller plus vite !</strong></h1>

<p style="text-align: justify;">Pour faire un petit parallèle risqué avec le développement : quand on a un block de codes qui revient à différents endroits d'un projet on pense automatique à le simplifier par une structure plus compacte. C'est presque la même chose avec le mapping, on cherche à simplifier pour aller encore plus vite. On y a recours généralement quand on doit exécuter une combinaison de commandes fréquemment utilisée ou invoquer une fonction. On peut appliquer le mapping à un mode spécifique, <strong>modal mapping</strong> (imap, nmap et vmap), ou à tous les modes, <strong>general mapping</strong> (map).</p>

<p style="text-align: justify;">Une des contraintes quand on crée un mapping est d'avoir des collisions. Certains raccourcis peuvent être déjà définis par l'OS, l'éditeur même, un plugin de l'éditeur ou une macro. On n'en crée pas délibérément. Quelques astuces sont mises à notre disposition pour limiter les risques de collisions et aussi pour avoir des raccourcis plus intuitifs :</p>

<ul>
    <li>Utiliser les touches dont on ne se sert presque jamais F2, F3, ...</li>
    <li>Combiner les touches avec les<strong> keys modifier</strong> Ctrl, Alt, Shift,</li>
    <li style="text-align: justify;">Préfixer avec <strong>leader</strong> : c'est une touche qu'on choisit pour préfixer les mappings. Par défaut c'est “\” mais dans la pratique on utilise très souvent “,” ou “-”. Il doit donc être remappé dans le .vimrc.</li>
</ul>

<p style="text-align: justify;">Le mode <strong><em>Insert</em></strong> est très pauvre. On doit le quitter à chaque fois qu'on doit faire une action un peu compliquée. Pour supprimer une ligne par exemple il faut en général faire :</p>

<p style="text-align: justify;"><strong>&lt;esc&gt;</strong> pour aller au mode normal
<strong>dd</strong> suppression de la ligne
<strong>i</strong> pour revenir en mode insert</p>

Pour mettre un mot en majuscule depuis le mode il faut au minium faire <strong><em>&lt;esc&gt;viwgUi</em></strong><em> .</em><strong><em>
</em></strong>

<p style="text-align: justify;">Ces actions sont très courantes quand on fait de l'édition. Comme le développeur a horreur de la répétition, VIM nous facilite la tâche avec le mapping qui nous permet de muscler notre éditeur.</p>

<p style="text-align: justify;">En ajoutant ces lignes dans notre config on pourra réaliser des actions sans quitter le mode Insert. Ctrl-D pour supprimer une ligne, Ctrl-U pour mettre en majuscule un mot ou Ctrl-Y pour dupliquer une ligne.</p>

<p style="text-align: justify;"><span style="color: #993300;">imap &lt;C-D&gt; &lt;esc&gt; ddi</span>
<span style="color: #993300;"> imap &lt;C-U&gt; &lt;esc&gt; viwgUi</span>
<span style="color: #993300;"> imap &lt;C-Y&gt; &lt;esc&gt; yypi</span></p>

<strong>j et k pour une Navigation entre lignes virtuelles</strong>
<span style="color: #993300;">nmap j gj</span>
<span style="color: #993300;"> nmap k gk</span>

<blockquote><strong>Recursive mappings</strong>
Par défaut le mapping sur VIM est récursif. Si on ajoute ce map
<span style="color: #993300;">map j gg</span>
<span style="color: #993300;"> map Q j</span>
Q est mappé en j , j mappé en gg, donc Q mappé en gg. On parle de <strong>recursive mapping</strong>

<span style="color: #993300;">noremap W j</span>
Si on ne veut pas de mapping recursif on utilise le mot clé <strong><em>noremap</em></strong>. Ici W devient juste j et pas gg.

Pour se mettre à l'abri des mauvaises surprises il est préférable d'utiliser par défaut un mapping non récursif.</blockquote>

<strong>Ctrl-S pour sauvegarder depuis les modes Normal ou Insert …</strong>
<span style="color: #993300;">noremap &lt;C-S&gt; :w&lt;CR&gt;</span>
<span style="color: #993300;"> inoremap &lt;C-S&gt;&lt;C-O&gt; :w&lt;CR&gt;</span>

… on peut aussi passer &lt;leader&gt;w

<span style="color: #993300;">noremap &lt;leader&gt;w :w&lt;CR&gt;</span>
<span style="color: #993300;"> inoremap &lt;leader&gt;w &lt;C-O&gt;:w&lt;CR&gt;</span>

<strong>jj, Enter ou Shift-Enter pour un Echappe plus intuitif</strong>
<span style="color: #993300;">inoremap jj &lt;esc&gt;</span>
<span style="color: #993300;"> inoremap &lt;CR&gt; &lt;esc&gt;</span>
<span style="color: #993300;"> inoremap &lt;S-CR&gt; &lt;esc&gt;</span>

<strong>Arrow keys désactivé</strong>
Certains proposent de désactiver les touches fléchées, avec la valeur Nop, pour se forcer à utiliser les touches hjkl pour la navigation. D'autres proposent d'en faire <a href="http://codingfearlessly.com/2012/08/21/vim-putting-arrows-to-use/" target="_blank">un autre usage.</a>

<span style="color: #993300;">noremap &lt;Up&gt; &lt;Nop&gt;</span>
<span style="color: #993300;"> noremap &lt;Down&gt; &lt;Nop&gt;</span>
<span style="color: #993300;"> noremap &lt;Left&gt; &lt;Nop&gt;</span>
<span style="color: #993300;"> noremap &lt;Right&gt; &lt;Nop&gt;</span>

<strong>Alt-J et Alt-K pour déplacer la ligne courante en mode normal</strong>
<span style="color: #993300;">nmap &lt;M-J&gt; mz:m+&lt;CR&gt;'z</span>
<span style="color: #993300;">nmap &lt;M-K&gt; mz:m-2&lt;CR&gt;'z</span>

<strong>Alt-J et Alt-K pour déplacer un block de lignes en mode Visual</strong>
<span style="color: #993300;">vmap &lt;M-J&gt; :m'&gt;+&lt;CR&gt;'&lt;my'&gt;mzgv'yo'z</span>
<span style="color: #993300;"> vmap &lt;M-K&gt; :m'&lt;-2'&gt;&lt;CR&gt;my'&lt;mzgv'yo'z</span>

<blockquote><span style="color: #003366;"><strong>Leader</strong></span>
<span style="color: #003366;"> The leader is a kind of “namespace” to keep those customizations separate</span>
<span style="color: #003366;"> and prevent them from shadowing default commands. (Steve Losh)</span></blockquote>

<strong>&lt;leader&gt;i pour les aller retour entre les modes Normal et Insert</strong>
<span style="color: #993300;">nnoremap &lt;leader&gt;i i</span>
<span style="color: #993300;"> inoremap &lt;leader&gt;i &lt;es&gt;</span>

<strong>Spell checking (http://amix.dk/vim/vimrc.html)</strong>

<strong>&lt;leader&gt;ss pour activer/désactiver le spell checking …</strong>
<span style="color: #993300;">nnoremap ss :setlocal spell!</span>

… et les commandes ,sn ,sp ,sa ,sz pour l'utilisation
<span style="color: #993300;">nnoremap &lt;leader&gt;sn ]s</span>
<span style="color: #993300;"> nnoremap &lt;leader&gt;sp [s</span>
<span style="color: #993300;"> nnoremap &lt;leader&gt;sa zg</span>
<span style="color: #993300;"> nnoremap &lt;leader&gt;sz z=</span>

<strong>Edition de .vimrc avec&lt;leader&gt;ev et&lt;leader&gt;sv</strong>
Il est très fréquent de vouloir faire des modifications sur .vimrc, et de voir les effets dans l'immédiat, alors qu'on est en pleine édition. Voici un mapping simple qui facilite l'écriture et le chargement des modifications.

<span style="color: #993300;">nnoremap &lt;leader&gt;ev :vsplit $MYVIMRC</span>
<span style="color: #993300;"> nnoremap &lt;leader&gt;sv :source $MYVIMRC</span>

<strong>$MYVIMRC</strong> une variable qui correspond au fichier .vimrc
<strong>:vsplit</strong> pour ouvrir .vimrc dans un nouvel onglet vertical
<strong>:source</strong> pour charger la dernière configuration

<strong>Basculer de relative à absolute</strong>
On peut avoir besoin de basculer entre le relative et l'absolute dans l'affichage des numéros de lignes. Avec ce mapping la commande est<strong>&lt;leader&gt;n</strong>

<span style="color: #003300;"><em>  function! NumberToggle()</em></span>
<span style="color: #003300;"><em>     if(&amp;relativenumber == 1)</em></span>
<span style="color: #003300;"><em>       set norelativenumber</em></span>
<span style="color: #003300;"><em>       set number</em></span>
<span style="color: #003300;"><em>       highlight LineNr ctermfg=yellow</em></span>
<span style="color: #003300;"><em>    else</em></span>
<span style="color: #003300;"><em>       set nonumber</em></span>
<span style="color: #003300;"><em>       set relativenumber</em></span>
<span style="color: #003300;"><em>       highlight LineNr ctermfg=green</em></span>
<span style="color: #003300;"><em>    endif</em></span>
<span style="color: #003300;"><em>  endfunc</em></span>

<span style="color: #003300;"><em>  nnoremap&lt;leader&gt;n :call NumberToggle()&lt;CR&gt;</em></span>

<a href="http://jeffkreeftmeijer.com/2012/relative-line-numbers-in-vim-for-super-fast-movement/" target="_blank">http://jeffkreeftmeijer.com/2012/relative-line-numbers-in-vim-for-super-fast-movement/</a>

<strong>Bonus abbréviation</strong>

<span style="color: #993300;">iabbrev @@ yakhya.dabo@arolla.fr</span>
<span style="color: #993300;"> iabbrev cc AROLLA – Mastering Software Development</span>

<p style="text-align: justify;">On ferme la deuxième partie de la série sur l’abréviation. Elle nous permet d'insérer rapidement les champs qu'on utilise couramment dans le texte, prénom, nom, adresse e-mail, signature, site web, … Avec la config ci-dessus, on peut se limiter à taper <strong>@@</strong> pour l'e-mail ou <strong>cc</strong> pour copyright.</p>

<h1><strong>Conclusion</strong></h1>

<p style="text-align: justify;">C'est dans dans la partie configuration qu'on découvre la vraie puissance de VIM. Le mapping doit être le premier réflexe pour rendre plus simples des actions complexes et pour éviter des répétitions comme les aller-retour entre le mode normal et les autres modes. N'hésitez pas à vous inspirer d'exemples sur le net pour enrichir votre configuration.</p>

On est bien tenté de dire qu'avec VIM on peut tout faire.
