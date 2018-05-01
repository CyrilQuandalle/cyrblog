---
layout: post
title: Dompter VIM en trois temps 1/3
date: 2015-02-09 11:43
author: yakhya dabo
comments: true
categories: [Bonnes pratiques de dév, editeur, Linux, outils, Outils, Programmation, vi, vim]
---
<p align="center"><span style="font-size: x-large;"><b>Temps 1 : </b></span><span style="font-size: x-large;"><b>Les principaux modes de VIM</b></span></p>

<p align="justify">Comme énoncé dans <a href="http://www.arolla.fr/blog/?p=2955" target="_blank">l'introduction</a> VIM est un éditeur modal. Chaque mode expose une manière différente d'interpréter les touches du clavier et d’interagir avec l'éditeur pour effectuer des actions sur le texte. On en distingue principalement quatre :</p>

<ul>
    <li>Mode <i><b>Normal</b></i> pour exécuter des commandes sur le texte</li>
    <li>Mode <i><b>Insert</b></i> pour insérer du texte</li>
    <li>Mode <i><b>Command-line</b></i> pour exécuter des commandes à travers le shell de VIM</li>
    <li>Mode <i><b>Visuel</b></i> pour travailler sur une sélection</li>
</ul>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/vim_modes_diagram.png"><img class="aligncenter wp-image-2968 size-large" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/vim_modes_diagram-1024x437.png" alt="vim_modes_diagram" width="910" height="388" /></a></p>

<p align="left">On verra pour chacun de ces modes quelques cas d'utilisation.</p>

<p align="left"><b>Nota</b> : Dans la documentation on peut trouver <i>Command Mode</i> à la place de <i>Normal Mode</i>. Ne pas faire la confusion avec <i>Command-Line Mode</i>.</p>

<p align="left"><span style="font-size: x-large;"><b>Le Mode <i>Normal</i></b></span></p>

<p align="justify">C'est le mode par défaut, le mode naturel de VIM, là où l'on passera le plus de temps. La majeure partie des commandes de manipulation de texte (recherche, navigation, modification, suppression) s'effectue en mode <em>Normal</em>. Pour passer d'un mode à un autre on passe par le mode <em>Normal</em>. Il suffit d'appuyer sur <i><b>&lt;</b></i><i><b>ESC</b></i><i><b>&gt;</b></i> pour y arriver.</p>

<p align="justify"><b>Navigation</b> : On peut utiliser les touches fléchées du clavier pour se déplacer dans le texte. Mais pour rester fidèle à l'epsrit VIM il est recommandé d'utiliser les touches <b>h</b>(left), <b>j</b>(down), <b>k</b>(up) et <b>l</b>(right).</p>

<p style="text-align: center;" align="justify"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/hjkl.png"><img class="aligncenter size-medium wp-image-2972" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/hjkl-300x131.png" alt="hjkl" width="300" height="131" /></a></p>

<p align="left">n<b>G</b> : place le curseur sur la ligne <b>n</b></p>

<p align="left"><b>H</b>(<i>High</i>) : place le curseur en haut de l'écran.</p>

<p align="left"><b>M</b>(<i>Middle</i>): place le curseur au milieu de l'écran.</p>

<p align="left"><b>L</b>(<i>Low</i>) : place le curseur en bas de l'écran.</p>

<p align="justify"><b>Notion de ligne :</b> VIM fait la différence entre une ligne <i><b>virtuelle</b></i> et une ligne <i><b>réelle</b></i>. La ligne virtuelle est celle affichée à l'écran. La ligne réelle correspond à une ligne du fichier, elle se termine par le caractère <i><b>&lt;</b></i><i><b>EOL</b></i><i><b>&gt;</b></i>. Une ligne réelle peut correspondre à plusieurs lignes virtuelles.</p>

<p align="justify"><b>j/k</b> : Pour se déplacer à travers les lignes réelles.</p>

<p align="justify"><b>gj/gk</b> : Pour se déplacer à travers les lignes virtuelles.</p>

<p align="center"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/linej.gif"><img class="aligncenter size-full wp-image-2990" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/linej.gif" alt="linej" width="500" height="250" /></a></p>

<p align="center"><span style="font-size: small;">Déplacement avec j et k.</span></p>

<p align="center"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/linegj.gif"><img class="aligncenter size-full wp-image-2991" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/linegj.gif" alt="linegj" width="500" height="250" /></a></p>

<p align="center"><span style="font-size: small;">Déplacement avec gj et gk.</span></p>

<p align="center"><span style="font-size: small;">On peut compter 8 lignes réelles et 11 lignes virtuelles. </span></p>

<p align="left"><b>Notion de word :</b> Une autre subtilité dans VIM est la différence entre word (avec w en minuscule) et WORD (avec W en majuscule). Voici ce qu'on peut lire dans le manuel (:help word) :</p>

<p align="left"><i>A word consists of a sequence of letters, digits and underscores, or a sequence of other non-blank characters, separated with white space (spaces, tabs, ). This can be changed with the 'iskeyword' option. An empty line is also considered to be a word. </i></p>

<p align="left"><i>A WORD consists of a sequence of non-blank characters, separated with white space. An empty line is also considered to be a WORD. </i></p>

<p align="left">Word avec W n'est délimité que par des espaces (tab, space ou &lt;CR&gt;). Il correspond à la définition grammaticlale d'un mot tandis que word avec w englobe les ponctuations ".", ";", ";", "_", "-", ...</p>

<p align="left"><b>w</b> : Pour passer d'un word à un autre</p>

<p align="left"><b>W</b> : Pour passer d'un WORD à un autre</p>

<p align="justify">En mode normal les commandes de manipulation de texte sont de la forme<b> : command &lt;motion&gt;</b>. Sur son blog, <a href="http://stevelosh.com/blog/2010/09/coming-home-to-vim/#a-language-of-text-editing" target="_blank"><strong>Steve Losh</strong></a> parle d' "<i><b>action + complément d'objet</b></i>". L'action c'est le <i>command</i> et le complément d'objet correspond au <i>motion</i> qui peut être une suite de caractères, de mots ou de lignes.</p>

<p align="left">Exemple :</p>

<p align="left"><b>d2d</b>
command = d (delete)
motion = 2d (2 lines from cursor position)</p>

<p align="left"><b>y2y</b>
command = y (yank)
motion = 2y (2 lines from cursor position)</p>

<p align="left"><b>caw</b>
command = c (change)
motion = aw (arround word)</p>

<p align="left">Chaque <i>motion</i> peut avoir un multiplicateur, 2d, 2y, 3w, ... Pour les lignes on peut préciser la direction, vers le bas (par défaut) ou vers le haut. La position du curseur est toujours le point de départ, le point 0.</p>

<p style="text-align: center;" align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/line.gif"><img class="aligncenter wp-image-2976 size-full" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/line.gif" alt="line" width="500" height="150" /></a></p>

<p align="left"><b>d2d</b> : supprimer la ligne courante et la ligne en dessous (0 et 1 vers le bas)</p>

<p align="left"><b>d2j</b> : supprimer les lignes 0, 1 et 2 vers le bas</p>

<p align="left"><b>d2k</b> : supprimer les lignes 0, 1 et 2 vers le haut</p>

<p align="left"><b>Précision pour copier/couper et coller </b></p>

<p align="justify">Pour faire une action de copier, couper ou coller on écrit ou lit dans le presse-papier. Avec VIM c'est dans le <i><b>registre</b></i> qu'on agit. Il existe plusieurs registres, et le presse-papier en est un. Quand on fait un <em>yank</em>, un <em>delete</em> ou un <em>past</em> on écrit dans un registre spécifique appelé registre par défaut. Un article spécifique pourra être consacré à la manipulation des registres.</p>

<p align="left"><strong>Copier avec y (yank)</strong></p>

<p align="left"><strong>y2y</strong> : copier la ligne courante et la ligne en dessous.</p>

<p align="left"><strong>y2j</strong> : copier la ligne courante et les deux lignes en dessous.</p>

<p align="left"><strong>y2k</strong> : copier la ligne courante et les deux ligne au dessus.</p>

<p align="left"><strong>yaw</strong> : copier le mot courant</p>

<p align="left"><strong>y2w</strong> ou <strong>2yw</strong> : copier le mot courant et le mot qui vient juste après</p>

<p align="left"><strong>Couper avec d (delete)</strong></p>

<p align="left">Il présente les mêmes motions que yy, avec daw, d2w, 2dw, d2d, d3k (4 line above)</p>

<p align="left"><strong>D</strong> : Couper, à partir de la position du curseur, jusqu'à la fin de la ligne</p>

<p style="text-align: center;" align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/D.gif"><img class="aligncenter size-full wp-image-2977" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/D.gif" alt="D" width="500" height="150" /></a></p>

<p align="left"><strong>p/P</strong> (past): passe le contenu du registre après/avant le curseur</p>

<p align="left">Exemple avec <strong>yyp</strong> pour copier la ligne courante juste en dessous (effectue une duplication)</p>

<p style="text-align: center;"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/yyp.gif"><img class="aligncenter size-full wp-image-2996" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/yyp.gif" alt="yyp" width="500" height="150" /></a></p>

<p align="left">Exemple avec <strong>ddp</strong> pour inverser la ligne courante avec celle en dessous</p>

<p style="text-align: center;" align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/ddp.gif"><img class="aligncenter size-full wp-image-2997" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/ddp.gif" alt="ddp" width="500" height="150" /></a></p>

<p align="left"><strong>o/O</strong> : insérer une ligne vide en dessous/au dessus de la ligne courante</p>

<p align="left"><strong>s</strong> (substitute): Supprimer le caractère courant et passe en mode Insert (pour remplacer)</p>

<p align="left"><strong>S</strong> : supprime la ligne courante et passe en mode Insert</p>

<p align="left"><strong>f{char}</strong> : positionne le curseur sur la première occurrence du caractère. On peut ensuite taper ";" pour passer à l’occurrence suivante ou "," pour revenir à l’occurrence précédente.</p>

<p align="left">Exemple avec <strong>fl</strong> (find l)</p>

<p style="text-align: center;" align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/findCharacter.gif"><img class="aligncenter size-full wp-image-2978" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/findCharacter.gif" alt="findCharacter" width="500" height="150" /></a></p>

<p align="left">f{char} peut être utilisé comme motion. Ex <strong>dfx</strong>,<strong>cfx</strong></p>

<p align="left"><strong>u</strong> : Annule la dernière action et <strong>&lt;Ctrl-R&gt;</strong> pour revenir sur l'annulation</p>

<p align="left"><strong>A</strong> : positionne le curseur en fin de ligne et passe en mode <i><b>insert</b></i></p>

<p align="left">Le <i>motion</i> "<strong>t</strong>" (till) est utilisé pour spécifier l'endroit où s'arrête l'action.</p>

<p align="left">Exemple avec <strong>dt.</strong> (delete till the dot )</p>

<p align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/dt.gif"><img class="aligncenter size-full wp-image-2979" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/dt.gif" alt="dt" width="500" height="150" /></a></p>

<p align="left"><b>Les actions opposées : </b>Certaines commandes ont des effets opposés.</p>

<p align="left"><strong>w / b</strong> : Se déplacer d'un caractère vers l'avant/arrière</p>

<p align="left"><strong>. / ;</strong> : repète/annuler la dernière répétition</p>

<p align="left"><strong>u / &lt;Ctrl-r&gt;</strong> : Undo/Redo</p>

<p align="left"><span style="font-size: x-large;"><b>Le Mode <i>Insert</i></b></span></p>

<p align="justify">On se retrouve dans ce mode très spécialement pour taper du texte. C'est l'ultime mode des éditeurs de textes classiques (notpad, sublime, ...). Il suffit de faire I, i, A ou la touche <strong><em>insert,</em></strong> depuis le mode <em>Normal</em> pour y arriver. D'autres commandes d'édition du mode <em>Normal</em> (c, s, A) nous basculent directement dans ce mode.</p>

<p align="justify"><b>La commande</b><b> </b><b>&lt;</b><b>Ctrl-</b><b>O</b><b>&gt;</b></p>

<p align="justify">Comme dit plus haut la majeure partie des commandes est utilisée en mode normal. En pleine édition on peut avoir besoin de supprimer des lignes, remplacer des caractères, copier/coller, sauvegarder les dernières modifications, ... La première solution est de faire &lt;<i><b>E</b></i><i><b>SC</b></i>&gt; pour passer en mode normal, taper la commande, et faire "i" pour revenir en mode insert. Un autre moyen, plus rapide, c'est d'utiliser &lt;Ctrl-O&gt;. Il nous permet de passer provisoirement du mode <i><b>I</b></i><i><b>nsert </b></i>au mode <i><b>N</b></i><i><b>ormal</b></i> juste le temps de taper une commande du mode <i><b>Normal</b></i>.</p>

<p align="justify">(Dans le prochain chapitre on montrera d'autres astuces plus pratique pour taper des commandes depuis le mode insert sans avoir à le quitter.)</p>

<p align="left"><span style="font-size: x-large;"><b>Le Mode <i>Command-Line</i></b></span></p>

<p align="justify">C'est un peu l'équivalent du shell pour vim. On y arrive en tapant ":" depuis le mode normal. Le prompt s'affiche ensuite et nous laisse la main pour taper une commande. On peut aussi faire des recherches dans ce mode avec “/” ou faire des filtres avec “!”</p>

<p align="left"><b>Les numéros de lignes </b></p>

<p align="justify">Toutes les lignes d'un fichier possèdent un numéro et la numérotation commence à 1. Le numéro d'une ligne peut être “absolu” ou “relatif”, en référence à la position courante, le point 0. Pour la plupart des actions sur les lignes on aura besoin des numéros “relatifs”.</p>

<p align="left">Pour afficher ou masquer les numéros absolus</p>

<p align="left"><b> :set nu
:set nonu
</b></p>

<p align="left">Pour afficher ou masquer les numéros relatifs</p>

<p align="left"><b> :set relativenumber
:set norelativenumber
</b></p>

<p align="center"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/line.gif"><img class="aligncenter size-full wp-image-2976" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/line.gif" alt="line" width="500" height="150" /></a></p>

<p align="left"><b>Play with range </b></p>

<p align="left">La force du mode <i>Command-Line</i> est la facilité de travailler sur des blocks de lignes, <i><b>range</b></i>.</p>

<p align="left">Exemple de ranges :</p>

<p align="left"><strong>1</strong> : la première ligne</p>

<p align="left"><strong>$</strong> : la dernière ligne</p>

<p align="left"><strong>.</strong> : la ligne courante</p>

<p align="left"><strong>1,$</strong> : de la ligne jusqu'à la dernière ligne</p>

<p align="left"><strong>.,2</strong> : de la ligne courante jusqu'à la deuxième ligne</p>

<p align="left"><strong>%</strong> : l'ensemble des lignes (texte complet)</p>

<p align="left">On peut aussi référencer les lignes de manières relatives, en ajoutant le signe “+” (vers le haut) ou “–“ (vers le bas) devant les bornes.</p>

<p align="left"><strong>-1, -2</strong> : De la première ligne (1) au dessus de la ligne courante jusqu'à la deuxième ligne au dessus de la ligne courante.</p>

<p align="left"><strong>1,+3</strong> : De la première ligne (1) jusqu'à la troisième en dessous de la ligne courante.</p>

<p align="left">Exemples</p>

<p align="left"><strong>:23d4</strong> : Supprimer les lignes 23, 24, 25 et 26 .</p>

<p align="left"><strong>:+1,$d</strong> : Supprimer de la ligne courante +1 jusqu'à la fin.</p>

<p align="left"><strong>:2,5p</strong> : Afficher de la ligne 2 jusqu'à la ligne 5 ( p = print) .</p>

<p align="left"><strong>:range move/copy x</strong> : Déplacer/Copier les lignes spécifiées dans <em>range</em> s à la ligne <em>x</em></p>

<p align="left">Exemple avec <i><b>:1,2move</b></i><i><b>5</b></i><i><b> </b></i></p>

<p style="text-align: center;" align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/move.gif"><img class="aligncenter size-full wp-image-2980" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/move.gif" alt="move" width="500" height="150" /></a></p>

<p align="left">On peut exécuter une commande du mode normal tout en étant en mode Command -Line</p>

<p align="left"><strong>:range normal {command}</strong></p>

<p align="left">Exemple avec <i><b>:% normal A ;</b></i></p>

<p align="left"><i><b><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/normal.gif"><img class="aligncenter size-full wp-image-2981" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/normal.gif" alt="normal" width="500" height="150" /></a></b></i></p>

<p align="left"><strong>:%</strong> sélectionner toutes les lignes
<b>normal </b>passer en mode Normal
<strong>A</strong> aller à la fin de la ligne et passer en mode Insert
<strong>;</strong> écrire un point virgule
Quand on valide avec Entrée la commande s'applique sur toutes les lignes</p>

<p align="left"><b>Find and replace avec s</b></p>

<p align="left"><b>:s/oldWord/newWord </b>: Remplacer, sur la ligne courante, la première occurrence de <em>oldWord</em> par <em>newWord</em></p>

<p align="left"><b>:s/oldWord/newWord/g </b>: Remplacer, sur la ligne courante, toutes les occurrences de <em>oldWord</em> par <em>newWord</em></p>

<p align="left"><b>:s/oldWord/newWord/c</b> : Demande confirmation avant d'effectuer le remplacement</p>

<p align="left">Exemple avec <b>:s/line4/LINE4/g </b></p>

<p style="text-align: center;" align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/findReplace.gif"><img class="aligncenter size-full wp-image-2982" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/findReplace.gif" alt="findReplace" width="500" height="150" /></a></p>

<p align="left">On peut appliquer le <em>find and replace</em> sur un <em>range. </em>Exemple la commande<strong><em> :%s/oldWorld/newWorld/g</em></strong> remplace toutes les occurrences de <em>oldWorld</em> par <em>newWorld</em> sur l'ensemble du texte. Sans l'option g le remplacement ne portera que sur les premières occurrences de oldWorld des différentes lignes du texte.</p>

<p align="left"><b>Les filtres</b></p>

<p align="left"><i><b>:r !{cmd}</b></i> : Insérer le résultat d'une commande juste en dessous de la position du curseur</p>

<p align="left">Exemple avec <i><b>:r !ls</b></i> ou <i><b>:r !git statu</b></i><i><b>s</b></i></p>

<p align="left"><b>La recherche</b></p>

<p align="left"><strong>/{pattern}&lt;CR&gt;</strong> : depuis le mode normal nous permet de faire une recherche sur un mot
<strong>n</strong> : pour passer à l'occurrence suivante
<strong>N</strong> : pour passer à l'occurrence précédente</p>

<p align="left">Exemple avec <b>/line</b></p>

<p style="text-align: center;" align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/find.gif"><img class="aligncenter size-full wp-image-2983" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/find.gif" alt="find" width="500" height="150" /></a></p>

<p align="left"><span style="font-size: x-large;"><b>Le Mode <i>Visual</i></b></span></p>

<p align="justify">En mode <i>visual</i>, comme son nom l'indique, on travaille sur une sélection de manière intuitive. La zone de sélection peut porter sur des mots (commande <strong>v</strong>), des lignes ( commande <strong>V</strong>), ou un block (ensemble de colonnes) avec la commande <strong>&lt;Ctrl-V&gt;</strong>. Une fois la sélection effectuée on peut exécuter une commande du mode normal, faire &lt;<strong>ESC</strong>&gt; et observer le résultat.</p>

<p align="left">Exemple avec &lt;<i><b>Ctrl-V</b></i>&gt;</p>

<p style="text-align: center;" align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/CV.gif"><img class="aligncenter size-full wp-image-2985" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/CV.gif" alt="CV" width="500" height="150" /></a></p>

<p align="left"><b>&lt;Ctrl-V&gt;</b> pour activer le mode <i>visual</i>
<b>lll</b>... pour sélectionner les colonnes
<b>jjj</b>... pour sélectionner les lignes
<i> </i><i><b>gU</b></i><i> pour transformer en majuscule</i></p>

<p align="left">La commnde gv : Revenir en mode visual, avec la dernière sélection.</p>

<p style="text-align: center;" align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/gv.gif"><img class="aligncenter size-full wp-image-2986" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/gv.gif" alt="gv" width="500" height="150" /></a></p>

<p align="left"><b>&lt;Ctrl-V&gt;</b> pour activer le mode <i>visual</i>
<b>lll</b>... pour sélection les colonnes
<b>jjj</b>... pour sélectionner les lignes
<b>gU</b> pour transformer en majuscule
<b>gv</b> pour revenir sur la sélection
<b>d</b> pour supprimer le block sélectionné</p>

<p align="left">La commande  <b>o</b> : pour modifier la sélection</p>

<p style="text-align: center;" align="left"><a href="http://www.arolla.fr/blog/wp-content/uploads/2015/01/o.gif"><img class="aligncenter size-full wp-image-2987" src="http://www.arolla.fr/blog/wp-content/uploads/2015/01/o.gif" alt="o" width="500" height="150" /></a></p>

<p align="left"><b>&lt;Ctrl-V&gt;</b> pour activer le mode <i>visual</i>
<b>lll</b>... pour sélection les colonnes
<b>jjj</b>... pour sélectionner les lignes
<b>o</b> pour modifier la sélection
<b>lll</b>... pour désélectionner les colonnes
<b>jjj</b>... pour désélectionner les lignes</p>

<p align="left"><strong><span style="font-size: x-large;">What's next ... ?</span></strong></p>

<p align="justify">Pour ce qui est de la magie de VIM on aurait pu s'en tenir à ce qu'on vient de voir, les différents modes de VIM avec les commandes de base qui nous accompagnent au quotidien et les quelques cas d'utilisation. Des fonctionnalités qu'on ne trouve pas ailleurs… Mais pour le plus timide des développeurs il en faudra encore plus pour s'intéresser à un nouvel outil. Certaines commandes peuvent être fastidieuses à taper. La tâche sera d'autant plus difficile qu'on peut avoir à utiliser ces commandes tous les jours. Peut-on toujours se permettre de faire des aller-retours entre le mode <em>Insert</em> et le mode <em>Normal</em> juste pour enregistrer, supprimer des lignes, mettre en majuscule, … ? <a href="http://www.arolla.fr/blog/2015/03/dompter-vim-en-trois-temps-23/" target="_blank">On verra, dans le prochain article, à travers le fichier de configuration .vimrc, comment on peut personnaliser notre éditeur pour simplifier les tâches courantes.</a></p>
