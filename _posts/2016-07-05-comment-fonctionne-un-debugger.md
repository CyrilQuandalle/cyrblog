---
layout: post
title: Comment fonctionne un debugger ?
date: 2016-07-05 14:54
author: jerome prudent
comments: true
categories: [Linux, Outils, Programmation, Programmation système]
---
<h1>Motivations</h1>

Un debugger est un outil fabuleux : cette sensation de contrôle divin ! La possibilité de figer l'exécution d'un process et d'inspecter les arcanes de sa mémoire.

C'était les deux phrases lyriques de cet article :) Nous verrons que le divin n'est qu'une machinerie bien huilée.

Le debugger est un outil que j'utilise quotidiennement. Je trouve important d'en comprendre les mécanismes sous-jacents. Ecrire un concurrent à <a href="https://en.wikipedia.org/wiki/GNU_Debugger">GDB</a> n'est certainement pas la meilleur façon d'utiliser son temps libre. En revanche, écrire un
<a href="https://en.wikipedia.org/wiki/Proof_of_concept">POC</a> de debugger est certainement la manière la plus didactique d'apprendre ! Et c'est ce que je vous propose aujourd’hui : écrire un petit debugger pas super pratique mais fonctionnel.

Concernant le fond, cet article ne traite que de Linux sous architecture x86_64. Il part du principe que vous avez de vagues notions sur ce qu'est :

<ul>
<li>l'<a href="https://en.wikibooks.org/wiki/X86_Assembly/X86_Architecture">architecture x86</a></li>
<li>le langage <a href="https://en.wikipedia.org/wiki/X86_instruction_listings">assembleur x86</a></li>
<li>le système Linux</li>
<li>un <a href="https://en.wikipedia.org/wiki/Process_%28computing%29">process</a></li>
<li>un <a href="https://en.wikipedia.org/wiki/Unix_signal">signal Unix</a></li>
<li>le langage C</li>
</ul>

Concernant la forme, cet article est en franglish (parce que je trouve étrange d'écrire <em>deboggueur</em>). Les exemples sont en langage C et spécifiques à l'architecture x86_64 (ne fonctionneront pas en 32 bits). N'étant pas codeur C, il y aura certainement plein de choses à redire sur mon code, n'hésitez pas à le faire.

Pour toute question, n'hésitez pas à me contacter par <a href="https://twitter.com/jeromeprudent">Twitter</a> ou par <a href="mailto:jprudent@arolla.fr">email</a>.

<h1>Rapide rappel sur les syscalls et les interruptions</h1>

Si vous savez déjà ce qu'est un syscall, vous pouvez sauter cette section.

Le processeur a plusieurs niveaux d'exécution :

<ul>
<li>Typiquement le noyau Linux tourne dans un mode dit <em>privilégié</em>. Dans ce mode, il peut accéder à toute la mémoire, lire et écrire sur disque, ...</p></li>
<li>Les autres process, comme votre navigateur, tournent dans un mode <em>non privilégié</em>. Ils n'ont accès qu'à une certaine partie de la mémoire et ne peuvent pas écrire directement sur disque.</p></li>
</ul>

<p>Tant qu'un process se contente de faire des calculs et de lire et écrire en mémoire, il est autonome. Mais dès qu'il décide d'agir sur son environnement (a.k.a. <em>side effect</em>), comme écrire sur le disque, il doit utiliser un <em>appel système</em> a.k.a <em>syscall</em>.

Le process effectuant un <em>syscall</em> donne la main au noyau et <strong><em>bloque</em></strong> jusqu'à ce que le <em>syscall</em> ait été effectué. Un <em>syscall</em> est en général une opération coûteuse en temps.

Linux implémente le standard <a href="https://en.wikipedia.org/wiki/POSIX">POSIX</a> qui définit un ensemble d'appels système.

En voici un extrait :

<table>
<thead>
<tr>
  <th>%rax</th>
  <th>syscall</th>
  <th>%rdi</th>
  <th>%rsi</th>
  <th>%rdx</th>
</tr>
</thead>
<tbody>
<tr>
  <td>0</td>
  <td>read</td>
  <td><code>unsigned int file_descriptor</code></td>
  <td><code>char * buffer</code></td>
  <td><code>size_t length</code></td>
</tr>
<tr>
  <td>1</td>
  <td>write</td>
  <td><code>unsigned int file_descriptor</code></td>
  <td><code>char * buffer</code></td>
  <td><code>size_t length</code></td>
</tr>
<tr>
  <td>57</td>
  <td>fork</td>
  <td></td>
  <td></td>
  <td></td>
</tr>
<tr>
  <td>59</td>
  <td>execve</td>
  <td><code>const char *filename</code></td>
  <td><code>const char *const argv[]</code></td>
  <td><code>const char *const envp[]</code></td>
</tr>
<tr>
  <td>60</td>
  <td>exit</td>
  <td><code>int error_code</code></td>
  <td></td>
  <td></td>
</tr>
<tr>
  <td>62</td>
  <td>kill</td>
  <td><code>pid_t pid</code></td>
  <td><code>int signal</code></td>
  <td></td>
</tr>
<tr>
  <td>101</td>
  <td>ptrace</td>
  <td><code>long request</code></td>
  <td><code>long pid</code></td>
  <td><code>unsigned long data</code></td>
</tr>
</tbody>
</table>

Allez voir la <a href="http://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/" target="_blank">liste complète </a>.

Chaque <em>syscall</em> a un identifiant qui est placé dans le registre <code>RAX</code> et peut avoir jusqu'à 6 paramètres passés par convention dans les registres <code>RDI</code>, <code>RSI</code>, <code>RDX</code>, <code>RCX</code>, <code>R8</code>, <code>R9</code>.

<code>read</code> et <code>write</code> permettent de lire et d'écrire dans un fichier. Nous aborderons les autres un peu plus tard.

L'exemple suivant est un typique "hello world" qui illustre un appel au  <em>syscall</em> <code>write</code> :

[code lang="asm"]
mov    $0x1,%rax
mov    $0x1,%rdi
mov    $0x4000fe,%rsi
mov    $0xd,%rdx
syscall
[/code]

Traduit en français, cela donne : "Appel du syscall <code>sys_write</code> (RAX=1) pour écrire dans le file descriptor 1 (RDI=1), alias la sortie standard, la chaîne de caractère à l'adresse 0x4000fe (RSI=0x4000fe) de longueur 13 (RDX=0xd). Notez l'instruction <code>syscall</code> qui est une vraie instruction assembleur x86_64.

Il existe un utilitaire très pratique, <code>strace</code>, qui permet de tracer tous les <em>syscall</em> effectués par un process. Par exemple pour tracer tous les <em>syscall</em> <code>write</code> de la commande <code>echo</code> :

<pre><code>$strace -o '| grep write' echo "Hello"
write(1, "Hello\n", 6)                  = 6
</code></pre>

<ul>
<li>On écrit dans le file descriptor 1 (sortie standard),</li>
<li>Une chaine de caractère qui contient "Hello\n"</li>
<li>On écrit 6 caractères</li>
<li><code>write</code> a bien écrit 6 caractères</li>
</ul>

Un process prépare les paramètres du <em>syscall</em> dans les registres du CPU et fait exécuter l'instruction <code>syscall</code> au CPU. Et là magiquement l'exécution du process s'arrête (bloque) et ne reprend que lorsque le <em>syscall</em> a été réalisé.

La tuyauterie permettant cela s'appelle une <em>interruption</em>. Une <em>interruption</em> permet au CPU d'appeler une fonction du kernel. Donc quand le CPU exécute l'instruction <code>syscall</code>, il redonne la main au noyau qui se débrouille pour mettre en pause le process appelant, exécuter la commande <em>syscall</em> demandée avec les paramètres, et relancer le process.

<h1>Salut fiston, c'est papa !</h1>

Si vous savez déjà ce qu'est un <code>fork</code>, vous pouvez sauter cette section.

On peut imaginer qu'un debugger a une certaine emprise sur le process déboggé. Sous Linux, ce genre d'abus de position s'exprime par une relation père / fils.

Si vous avez une console à proximité et que vous tapez <code>pstree</code> vous remarquerez que les process sont organisés hiérarchiquement. La racine commune à tous est <code>systemd</code> (ou <code>init</code> sur des systèmes plus anciens) et votre navigateur est une feuille de l'arbre.

Pour créer un process fils, un futur père utilise le syscall <code>fork</code>. C'est d'ailleurs la seule façon de créer des process. Voici un code typique :

[code lang="C"]
int main()
{   pid_t child = fork();
    if(child == 0) {
        printf(&amp;quot;I am the child&amp;quot;)
    }
    else {
        printf(&amp;quot;I am the father of %d&amp;quot;, child);
    }
    return 0;
}
[/code]

<code>fork</code> procède à une copie presque intégrale du processus appelant (mémoire, registres CPU, ...). L'appelant devient le processus père du clone qui est donc son fils.
Quand <code>fork</code> rend la main, les 2 processus continuent leurs exécution juste après l'appel à <code>fork</code>, sur le <code>if</code>.

Je disais copie <em>presque</em> intégrale car dans le process père, <code>fork</code> renvoie le PID du fils, et dans le process fils il renvoie 0. Le fils affichera donc "I am the child" et le père "I am the father of 1234".

En extrapolant, on peut voir le <code>fork</code> comme une <a href="https://fr.wikipedia.org/wiki/Mitose">mitose</a> cellulaire. Avant la mitose on a 1 cellule et après la mitose on a 2 cellules qui partagent exactement le même ADN (le code).

<h1>Trace-moi si tu peux</h1>

Linux fournit un syscall appelé <code>ptrace</code> qui permet d'implémenter un debugger.

Dorénavant nous parlerons de <em>tracer</em> (le debugger) et de <em>tracee</em> (le process  à débugger), c'est le vocabulaire employé dans la page de <code>man</code> de <code>ptrace</code>.

Le <em>tracee</em> fait appel à la commande <code>TRACEME</code> pour signaler qu'il souhaite être tracé par son père. Dans ce mode, le process peut être dans deux états possibles : soit il est actif, dans l'état <code>RUNNING</code>, soit il est inactif, dans l'état <code>STOPPED</code>.

En mode <code>TRACEME</code>, le <em>tracee</em> passe à l'état <code>STOPPED</code> quand il reçoit <strong><em>n'importe quel signal</em></strong>.

Le <em>tracee</em> passe à l'état <code>RUNNING</code> quand le père lance la commande <code>ptrace</code> <code>CONT</code> (continue).

Le code suivant illustre ce principe :

[code lang="C"]
int main() {
    pid_t child = fork();
    if(child == 0) {
        ptrace(PTRACE_TRACEME, 0, NULL, NULL);
        child = getpid();
        printf(&amp;quot;I am about to get STOPPEDn&amp;quot;)
        kill(child, SIGUSR1);
        printf(&amp;quot;I am RUNNING againn&amp;quot;);
    }
    else {
        printf(&amp;quot;Waiting for the child to stopn&amp;quot;)
        waitpid(child, NULL, 0);
        printf(&amp;quot;The tracee is stoppedn&amp;quot;)
        ptrace(PTRACE_CONT, child, NULL, NULL);
        // wait for the child to exit
        waitpid(child, NULL, 0);
    }
    return 0;
}
[/code]

Le <em>tracee</em> récupère son pid avec la fonction <code>getpid</code> et s'envoie un signal SIGUSR1 via <code>kill</code>. Notons que <code>kill</code> est un <em>syscall</em> qui permet d'envoyer des signaux à un process. <code>kill</code> prend un PID comme premier paramètre. Le second paramètre est le signal à envoyer. On ne peut pas accompager des données supplémentaires à un signal. A la réception de ce signal, le <em>tracee</em>  passe à l'état <code>STOPPED</code> car il est en mode <code>TRACEME</code>.

Le <em>tracer</em> fait un premier <code>waitpid</code>. <code>waitpid</code> permet d'attendre un changement d'état de son processus fils. Ici, il attend que son fils passe à l'état <code>STOPPED</code>. Notons que <code>wait</code> est un <em>syscall</em> également.

Une fois que <code>wait</code> redonne la main, le <em>tracer</em> utilise <code>PTRACE_CONT</code> pour que le <em>tracee</em> repasse à l'état <code>RUNNING</code> et continue de s'exécuter.

Le père fait un ultime <code>wait</code>. C'est un peu hors propos mais cela permet au <em>tracee</em> de terminer proprement son exécution, sans rester à l'état zombie.

Nous venons d'illustrer le mécanisme de signaux et de commandes <code>ptrace</code> qui permettent de changer l'état (<code>RUNNING</code> / <code>STOPPED</code>) du <em>tracee</em>.

<h1>Traçons</h1>

Lorsque le <em>tracee</em> est à l'état <code>STOPPED</code>, <code>ptrace</code> fournit au <em>tracer</em> des commandes qui permettent de l'inspecter et de l'exécuter pas à pas.

<ul>
<li><code>PEEKUSER</code> permet d'inspecter les registres du CPU. Les valeurs de registre ne sont pas lues en live depuis le CPU. En fait, quand le kernel stoppe le tracee il enregistre le contexte du processus, dont les registres, afin que ce dernier puisse reprendre son exécution plus tard, comme si de rien n'était. Les valeurs renvoyées par <code>ptrace</code> sont issues de cet enregistrement.

</li>
<li>

<code>PEEKTEXT</code> permet d'examiner la mémoire.

</li>
<li>

<code>SINGLESTEP</code> exécute l'instruction pointée par le registre <code>RIP</code> et repasse
le <em>tracee</em> à l'état <code>STOPPED</code>.

</li>
</ul>

Le fonctionnement de ces deux commandes est illustré par le code suivant.
Il s'agit de compter le nombre d'<a href="https://en.wikipedia.org/wiki/Branch_%28computer_science%29#Examples">embranchement</a> sur lequel est passé le <em>tracee</em>.

[code lang="C"]
void fizzbuzz() {
    for(int i = 0; i &amp;lt; 100; i++) {
        int fizz = i % 3 == 0;
        if(fizz) printf(&amp;quot;Fizz&amp;quot;);
        int buzz = i % 5 == 0;
        if(buzz) printf(&amp;quot;Buzz&amp;quot;);
        if(!(fizz||buzz)) printf(&amp;quot;%d&amp;quot;, i);
        printf(&amp;quot;, &amp;quot;);
    }
}

int waitchild(pid_t pid) {
    int status;
    waitpid(pid, &amp;amp;status, 0);
    if(WIFSTOPPED(status)) {
        return 0;
    }
    else if (WIFEXITED(status)) {
        return 1;
    }
    else {
        printf(&amp;quot;%d raised an unexpected status %d&amp;quot;, pid, status);
        return 1;
    }
}

void trace(pid_t child) {
  unsigned long instruction, opcode1, opcode2, ip;
  unsigned long jmps = 0;
  do {
    ip = ptrace(PTRACE_PEEKUSER, child, 8 * RIP, NULL);
    instruction = ptrace(PTRACE_PEEKTEXT, child, ip, NULL);
    opcode1 = instruction &amp;amp; 0x00000000000000FF;
    opcode2 = (instruction &amp;amp; 0x000000000000FF00) &amp;gt;&amp;gt; 8;
    if((opcode1 &amp;gt;= 0x70 &amp;amp;&amp;amp; opcode1 &amp;lt;= 0x7F) ||
       (opcode1 == 0x0F &amp;amp;&amp;amp; (opcode2 &amp;gt;= 0x83 &amp;amp;&amp;amp; opcode2 &amp;lt;= 0x87))) {
         jmps = jmps + 1;
    }
    ptrace(PTRACE_SINGLESTEP, child, NULL, NULL);
  } while(waitchild(child) &amp;lt; 1);
  printf(&amp;quot;n=&amp;gt; There are %lu jumpsn&amp;quot;, jmps);
}

int main() {
    long instruction;
    pid_t child = fork();
    if(child == 0) {
        ptrace(PTRACE_TRACEME, 0, NULL, NULL);
        child = getpid();
        kill(child, SIGUSR1);
        fizzbuzz();
    }
    else {
        // wait for the child to stop
        waitchild(child);
        trace(child);
    }
    return 0;
}
[/code]

Le fichier <a href="https://gist.github.com/jprudent/437338b32a54bc37d232f5430ee42f87">compilable est ici</a>.

A l'exécution on a :

<pre><code>FizzBuzz, 1, 2, Fizz, 4, Buzz, Fizz, 7, 8, Fizz, Buzz, 11, Fizz, 13, 14, FizzBuzz, 16, 17, Fizz, 19, Buzz, Fizz, 22, 23, Fizz, Buzz, 26, Fizz, 28, 29, FizzBuzz, 31, 32, Fizz, 34, Buzz, Fizz, 37, 38, Fizz, Buzz, 41, Fizz, 43, 44, FizzBuzz, 46, 47, Fizz, 49, Buzz, Fizz, 52, 53, Fizz, Buzz, 56, Fizz, 58, 59, FizzBuzz, 61, 62, Fizz, 64, Buzz, Fizz, 67, 68, Fizz, Buzz, 71, Fizz, 73, 74, FizzBuzz, 76, 77, Fizz, 79, Buzz, Fizz, 82, 83, Fizz, Buzz, 86, Fizz, 88, 89, FizzBuzz, 91, 92, Fizz, 94, Buzz, Fizz, 97, 98, Fizz,
=&gt; There are 23037 jumps
</code></pre>

Plusieurs exécutions du programme retournent toujours le même nombre, ce qui est assez rassurant.

Détaillons le programme :

La fonction <code>main</code> reprend le même schéma que les exemples précédents :

<ol>
<li><code>fork</code> du process

</li>
<li>

le <em>tracee</em> se met en mode <code>TRACEME</code> et passe à l'état <code>STOPPED</code> en s'envoyant n'importe quel signal, puis exécutera <code>fizzbuzz</code> quand il passera à l'état <code>RUNNING</code>.

</li>
<li>

Le <em>tracer</em> attend que le <em>tracee</em> passe à l'état <code>STOPPED</code> puis exécute <code>trace</code>

</li>
</ol>

<code>fizzbuzz</code> est une simple fonction qui implémente le célèbre <a href="https://en.wikipedia.org/wiki/Fizz_buzz">FizzBuzz</a>. C'est cette fonction qui sera auditée par le <em>tracer</em>.

<code>waitchild</code> encapsule un appel à <code>waitpid</code>. Si le <em>tracee</em> passe à l'état <code>STOPPED</code>, elle renvoie 0. Et si le <em>tracee</em> passe à l'état <code>TERMINATED</code>, elle renvoie 1.

<code>trace</code> est une boucle dont la condition d'arrêt est le <em>tracee</em> qui passe à l'état <code>TERMINATED</code>. Dans cette boucle, le <em>tracer</em> :

<ol>
<li>Utilise la commande <code>PEEKUSER</code> afin de récupérer l'adresse de l'instruction courante stockée dans le registre <code>RIP</code>. <code>PEEKUSER</code> permet d'inspecter les registres du CPU.

</li>
<li>

Lit en mémoire, à l'adresse stockée dans <code>RIP</code>, l'instruction sur laquelle le <em>tracee</em> est arrêté, via la commande <code>PEEKTEXT</code>.

</li>
<li>

<code>PEEKTEXT</code> écrit les octets en mémoire dans un <code>long</code> de 8 octets. Notons que l'archi x86 est en <a href="https://en.wikipedia.org/wiki/Endianness">little endian</a>, cela signifie que l'octet à l'adresse pointée par <code>RIP</code> est récupéré dans l'octet de poids de plus faible du <code>long</code>. D'où les calculs binaires pour récupérer les deux premiers octets pointés par <code>RIP</code>.

</li>
<li>

On vérifie si l'instruction correspond à une instruction de <a href="https://en.wikipedia.org/wiki/X86_instruction_listings#Original_8086.2F8088_instructions">saut conditionnel</a> (instructions Jcc), auquel cas, on incrémente la variable <code>jmps</code>.

</li>
<li>

On exécute la commande <code>SINGLESTEP</code> qui exécute <strong><em>une seule</em></strong> instruction du <em>tracee</em> et lui envoie un signal <code>SIGTRAP</code> pour qu'il passe immédiatement à l'état <code>STOPPED</code>.

</li>
<li>

Après l'exécution de la boucle, on affiche le résultat.

</li>
</ol>

<em>23000</em> sauts conditionnels est assez hallucinant, cela en fait 2300 par itération. <code>fizzbuzz</code> est assez simple, mais je pense que <code>printf</code> doit être assez compliqué et alourdir l'addition.

<h1>Tracer n'importe quoi</h1>

Jusqu'ici, le <em>tracee</em> était un process bien connu, que nous avions codé nous même. Ce que nous aimerions, c'est tracer n'importe quel programme.

Le <em>syscall</em> <code>excecve</code> permet de remplacer l'image du process appelant par un autre. A l'issu du <em>syscall</em> <code>execve</code>, le process n'a plus rien à voir avec le code d'origine, il est complètement remplacé par le programme passé à <code>execve</code>. D'ailleurs, il n'y a aucun moyen de récupérer le résultat d'<code>execve</code>.

<code>execve</code> a 3 paramètres :
- le chemin du programme
- les arguments à passer au programme
- les variables d'environnement à passer au programme

Une subtilité d'<code>execve</code> intéressante dans notre cas, est qu'un signal <code>SIGTRAP</code> est automatiquement envoyé après l'exécution d'<code>execve</code> si le process est en mode <code>TRACEME</code>. Ce qui siginifie que l'on peut se passer d'envoyer manuellement un signal dans le <em>tracee</em>. Lorsque <code>waitpid</code> donne la main au <em>tracer</em>, l'image du <em>tracee</em> a été remplacée par celle du programme passé en paramètre d'<code>execve</code>.

[code lang="C"]
int waitchild(pid_t pid) {
    int status;
    waitpid(pid, &amp;amp;status, 0);
    if(WIFSTOPPED(status)) {
        return 0;
    }
    else if (WIFEXITED(status)) {
        return 1;
    }
    else {
        printf(&amp;quot;%d raised an unexpected status %d&amp;quot;, pid, status);
        return 1;
    }
}

void trace(pid_t child) {
  unsigned long instruction, opcode1, opcode2, ip;
  unsigned long jmps = 0;
  do {
    ip = ptrace(PTRACE_PEEKUSER, child, 8 * RIP, NULL);
    instruction = ptrace(PTRACE_PEEKTEXT, child, ip, NULL);
    opcode1 = instruction &amp;amp; 0x00000000000000FF;
    opcode2 = (instruction &amp;amp; 0x000000000000FF00) &amp;gt;&amp;gt; 8;
    if((opcode1 &amp;gt;= 0x70 &amp;amp;&amp;amp; opcode1 &amp;lt;= 0x7F) ||
       (opcode1 == 0x0F &amp;amp;&amp;amp; (opcode2 &amp;gt;= 0x83 &amp;amp;&amp;amp; opcode2 &amp;lt;= 0x87))) {
         jmps = jmps + 1;
    }
    ptrace(PTRACE_SINGLESTEP, child, NULL, NULL);
  } while(waitchild(child) &amp;lt; 1);
  printf(&amp;quot;n=&amp;gt; There are %lu jumpsn&amp;quot;, jmps);
}

int main(int argc, char ** argv) {
    long instruction;
    pid_t child = fork();
    if(child == 0) {
        ptrace(PTRACE_TRACEME, 0, NULL, NULL);
        execve(argv[1], argv + 1, NULL);
    }
    else {
        // wait for the child to stop
        waitchild(child);
        trace(child);
    }
    return 0;
}
[/code]

Le fichier <a href="https://gist.github.com/jprudent/bdcafa9622784c72cf95839a0fcf55f7">compilable est ici</a>.

<code>waitpid</code> et <code>trace</code> n'ont pas été modifiés, <code>fizzbuzz</code> a été supprimé.

<code>main</code> a subi quelques altérations :

<ol>
<li>Le <em>tracee</em> s'attend à ce que soient passés le chemin du programme à tracer dans <code>argv[1]</code> et les arguments du programme à tracer dans <code>argv[2]</code>, <code>argv[3]</code>, etc.

</li>
<li>

Le <em>tracee</em> ne s'envoie plus de signal lui-même pour passer à l'état <code>STOPPED</code>

</li>
<li>

Le <em>tracee</em> appel <code>execve</code> qui envoie un signal <code>SIGTRAP</code> implicitement.

</li>
</ol>

Le code du <em>tracer</em> n'a absolument pas changé.

A l'exécution, cela donne :

<pre><code>./ptrace_ex4 /usr/bin/ls /      
bin   dev  home  lib64       media  opt   root  sbin  sys  usr
boot  etc  lib   lost+found  mnt    proc  run   srv   tmp  var

=&gt; There are 44633 jumps
</code></pre>

<h1>Breakpoints</h1>

Jusqu'ici, le <em>tracer</em> se contente de faire quelques calculs préprogrammés. L'une des fonctionnalités attendues d'un debugger est de pouvoir poser des points d'arrêt à une adresse particulière.

La théorie est simple : il faut que le <em>tracee</em> passe à l'état <code>STOPPED</code> au moment où il exécute l'instruction à l'adresse choisie.

La pratique ressemble à un gros hack. Le <em>tracer</em> <strong><em>modifie</em></strong> le code du <em>tracee</em> pour qu'à l'adresse du breakpoint le <em>tracee</em> reçoive un signal qui le mette à l'état <code>STOPPED</code>.

Rappelez vous, la plus petite instruction assembleur peut faire un seul octet. Il faut donc que le code qui déclenche le signal fasse 1 octet afin de ne pas modifier plusieurs instructions.

<code>int 3</code> a pour opcode 0xCC et lève une <em>interruption</em> spécialement cablée dans le kernel pour envoyer un signal <code>SIGTRAP</code> à qui la lève.

Pour écrire dans la mémoire du <em>tracee</em>, <code>ptrace</code> fournit la commande <code>POKETEXT</code>. Nous verrons aussi la commande <code>POKEUSER</code> qui permet d'écrire dans un registre du CPU.

Voici à quoi ressemble une implémentation de breakpoint.

[code lang="C"]
int waitchild(pid_t pid) {
    int status;
    waitpid(pid, &amp;amp;status, 0);
    if(WIFSTOPPED(status)) {
        return 0;
    }
    else if (WIFEXITED(status)) {
        return 1;
    }
    else {
        printf(&amp;quot;%d raised an unexpected status %d&amp;quot;, pid, status);
        return 1;
    }
}

unsigned long to_ulong(char * s) {
  return strtol(s, NULL, 16);
}

unsigned long readMemoryAt(pid_t tracee, unsigned long address) {
  return ptrace(PTRACE_PEEKTEXT, tracee, address, NULL);
}

void writeMemoryAt(pid_t tracee, unsigned long address, unsigned long instruction) {
  ptrace(PTRACE_POKETEXT, tracee, address, instruction);
}

unsigned long readRegister(pid_t tracee, int reg) {
  return ptrace(PTRACE_PEEKUSER, tracee, 8 * reg, NULL);
}

void writeRegister(pid_t tracee, int reg, unsigned long value) {
  ptrace(PTRACE_POKEUSER, tracee, 8 * reg, value);
}

unsigned long setbp(pid_t tracee, unsigned long address) {
    unsigned long original = readMemoryAt(tracee, address);
    unsigned long int3 = (original &amp;amp; 0xFFFFFFFFFFFFFF00) | 0x00000000000000CC;
    writeMemoryAt(tracee, address, int3);
    printf(&amp;quot;Set breakpoint at %lx, new instruction is %lx instead of %lxn&amp;quot;,
          address, readMemoryAt(tracee, address), original);
    return original;
}

void removebp(pid_t tracee, unsigned long address, unsigned long original) {
  unsigned long previously = readMemoryAt(tracee, address);
  writeMemoryAt(tracee, address, original);
  printf(&amp;quot;Unset breakpoint at %lx, new instruction is %lx, instead of %lxn&amp;quot;,
       address, readMemoryAt(tracee, address), previously);
}

void showregisters(pid_t tracee) {
  printf(&amp;quot;RIP = %lxn&amp;quot;,
        readRegister(tracee, RIP));
}

void setIp(pid_t tracee, unsigned long address) {
  writeRegister(tracee, RIP, address);
}

void presskey() {
  getchar();
}

int main(int argc, char ** argv) {
    setbuf(stdout, NULL);
    unsigned long bpAddress = to_ulong(argv[1]);
    pid_t child = fork();
    if(child == 0) {
        ptrace(PTRACE_TRACEME, 0, NULL, NULL);
        execve(argv[2], argv + 2, NULL);
    }
    else {
        // wait for the child to stop
        waitchild(child);

        unsigned long originalInstruction = setbp(child, bpAddress);
        ptrace(PTRACE_CONT, child, NULL, NULL);

        while(waitchild(child) &amp;lt; 1) {
          printf(&amp;quot;Breakpoint hit !n&amp;quot;);
          showregisters(child);
          presskey();

          removebp(child, bpAddress, originalInstruction);
          setIp(child, bpAddress);

          ptrace(PTRACE_SINGLESTEP, child, NULL, NULL);
          waitchild(child);

          setbp(child, bpAddress);

          ptrace(PTRACE_CONT, child, NULL, NULL);
        }
    }
    return 0;
}

[/code]

Le fichier <a href="https://gist.github.com/jprudent/f3965b5e9a658d5088c090a9f0aab2b2">compilable est ici</a>

Woh! Ca commence à être gros ! Décortiquons tout ça.

<code>main</code> suis le même pattern que d'habitude: <code>fork</code>, et <code>TRACEME</code>, <code>execve</code> pour le <em>tracee</em>. En revanche le code du <em>tracer</em> a pas mal changé :
- Comme d'habitude, <code>waitchild</code> attend que le <em>tracee</em> passe à l'état <code>STOPPED</code>.
- <code>setbp</code> pose le point d'arrêt à l'adresse demandée. Concrètement, il s'agit d'utiliser la commande <code>POKETEXT</code> pour écrire l'opcode 0xCC (<code>int 3</code>) à l'adresse mémoire du breakpoint. <code>setbp</code> retourne l'instruction originale (très important!).
- La commande <code>CONT</code> permet de continuer l'exécution du <em>tracee</em> jusqu'à ce qu'il exécute cette fameuse <code>int 3</code> et passe à l'état <code>STOPPED</code>
- La boucle <code>while</code> termine quand le <em>tracee</em> passe à l'état <code>TERMINATED</code>
- Si on est rentré dans la boucle, cela signifie que l'instruction <code>int 3</code> correspondant au breakpoint a été exécutée.
- On affiche quelques registres et on bloque tant que l'utilisateur n'a pas pressé Ctrl+D au clavier pour lui laisser le temps de lire.
- A ce moment le registre <em>RIP</em> vaut <code>bpAddress + 1</code>, car on a exécuté <code>int 3</code> qui fait un octet. Si on laisse filer le <em>tracee</em> il n'exécutera jamais l'instruction qui était prévue à l'adresse <code>bpAddress</code>. De plus, <code>bpAddress + 1</code> contient certainement une instruction inintelligible.
- Donc le <em>tracer</em> remet l'instruction originale à l'adresse <code>bpAddress</code> via <code>POKETEXT</code>.
- Puis le <em>tracer</em> <strong><em>rembobine</em></strong> le fil d'exécution du <em>tracee</em> en mettant le registre <em>RIP</em> à <code>bpAddress</code> pour qu'il pointe sur l'instruction prévue. Pour cela, on utilise la commande <code>POKEUSER</code> pour affecter une valeur à un registre du CPU.
- Avec la commande <code>SINGLESTEP</code>, le <em>tracer</em> commande au <em>tracee</em> d'exécuter l'instruction à <code>bpAddress</code>, la fameuse instruction qui avait été zappée au profit de <code>int 3</code>.
- Enfin, le <em>tracer</em> <strong><em>repose le breakpoint</em></strong> et laisse filer le <em>tracee</em> jusqu'à ce que ce dernier passe à <code>TERMINATED</code> ou à <code>STOPPED</code> (s'il réexécute <code>int 3</code> à l'adresse <code>bpAddress</code>)

Testons ce nouveau <em>tracer</em> sur le programme <code>fizzbuzz</code> suivant :

[code lang="C"]
void fizzbuzz() {
    for(int i = 0; i &amp;lt; 100; i++) {
        int fizz = i % 3 == 0;
        if(fizz) printf(&amp;quot;Fizz&amp;quot;);
        int buzz = i % 5 == 0;
        if(buzz) printf(&amp;quot;Buzz&amp;quot;);
        if(!(fizz||buzz)) printf(&amp;quot;%d&amp;quot;, i);
        printf(&amp;quot;, &amp;quot;);
        fflush(stdout);
    }
}

int main() {
    fizzbuzz();
}


[/code]

On peut décompiler le programme et chercher la fonction <code>fizzbuzz</code> :

<pre><code>$ objdump -d fizzbuzz | grep -A200 '&lt;fizzbuzz&gt;:' | less

0000000000400576 &lt;fizzbuzz&gt;:
  400576:   55                      push   %rbp
  400577:   48 89 e5                mov    %rsp,%rbp
  40057a:   48 83 ec 10             sub    $0x10,%rsp
  40057e:   c7 45 fc 00 00 00 00    movl   $0x0,-0x4(%rbp)
  400585:   e9 c3 00 00 00          jmpq   40064d &lt;fizzbuzz+0xd7&gt;
  40058a:   ...
  ... BLA BLA FIZZ BUZZ BLA BLA ...
  400649:   83 45 fc 01             addl   $0x1,-0x4(%rbp)
  40064d:   83 7d fc 63             cmpl   $0x63,-0x4(%rbp)
  400651:   0f 8e 33 ff ff ff       jle    40058a &lt;fizzbuzz+0x14&gt;
  400657:   90                      nop
  400658:   c9                      leaveq
  400659:   c3                      retq   
</code></pre>

La fonction <code>fizzbuzz</code> est mappée en mémoire à l'adresse 0x4004e6.

On reconnait notre boucle :

<ol>
<li>en 0x40057e la variable <code>i</code> est initialisée à 0

</li>
<li>

en 0x400585 on saute en 0x40064d

</li>
<li>

en 0x40064d on compare <code>i</code> à 99

</li>
<li>

en 0x400651 si <code>i</code> &lt;= 99 on entre dans la boucle en 0x40058a, sinon la fonction se termine

</li>
<li>

en 0x400649 qui est la dernière instruction de la boucle, <code>i</code> est incrémenté et retour en 4)

</li>
</ol>

Lançons <code>fizzbuzz</code> avec un point d'arrêt sur l'adresse 0x400651 :

<pre><code>./ptrace_ex5 400651 ./fizzbuzz
Set breakpoint at 400651, new instruction is c990ffffff338ecc instead of c990ffffff338e0f
Breakpoint hit !
RIP = 400652

Unset breakpoint at 400651, new instruction is c990ffffff338e0f, instead of c990ffffff338ecc
Set breakpoint at 400651, new instruction is c990ffffff338ecc instead of c990ffffff338e0f
FizzBuzz, Breakpoint hit ! // On a passé la première itération
RIP = 400652

Unset breakpoint at 400651, new instruction is c990ffffff338e0f, instead of c990ffffff338ecc
Set breakpoint at 400651, new instruction is c990ffffff338ecc instead of c990ffffff338e0f
1, Breakpoint hit ! // 2ème itération
RIP = 400652

...
</code></pre>

Après avoir pressé 100 fois la touche entrée, le <em>tracer</em> et le <em>tracee</em> terminent leur exécution sans problème. Ouf!

Cette implémentation des breakpoints est assez extraordinaire je trouve. Elle a l'avantage de ne pas ralentir le <em>tracee</em> en dehors des moments où il est à l'état <code>STOPPED</code>.

Il ne devrait pas être compliqué d'implémenter des breakpoints conditionnels. Je vous laisse faire ça chez vous tranquillement.

<h1>Breakpoints sans modifier le <em>tracee</em></h1>

Il est possible d'implémenter les breakpoints sans devoir modifier le code du <em>tracee</em>.

[code lang="C"]
&amp;lt;br /&amp;gt;int waitchild(pid_t pid) {
    int status;
    waitpid(pid, &amp;amp;status, 0);
    if(WIFSTOPPED(status)) {
        return 0;
    }
    else if (WIFEXITED(status)) {
        return 1;
    }
    else {
        printf(&amp;quot;%d raised an unexpected status %d&amp;quot;, pid, status);
        return 1;
    }
}

unsigned long to_ulong(char * s) {
  return strtol(s, NULL, 16);
}

unsigned long readRegister(pid_t tracee, int reg) {
  return ptrace(PTRACE_PEEKUSER, tracee, 8 * reg, NULL);
}

void showregisters(pid_t tracee) {
  printf(&amp;quot;RIP = %lxn&amp;quot;,
        readRegister(tracee, RIP));
}

void presskey() {
  getchar();
}

int main(int argc, char ** argv) {
    unsigned long bpAddress = to_ulong(argv[1]);
    pid_t child = fork();
    unsigned long rip;
    if(child == 0) {
        ptrace(PTRACE_TRACEME, 0, NULL, NULL);
        execve(argv[2], argv + 2, NULL);
    }
    else {
        // wait for the child to stop
        waitchild(child);
        do {
          rip = readRegister(child, RIP);
          if(rip == bpAddress) {
            showregisters(child);
            presskey();
          }
          ptrace(PTRACE_SINGLESTEP, child, NULL, NULL);
        } while(waitchild(child) &amp;lt; 1);
    }
    return 0;
}
[/code]

Le fichier <a href="https://gist.github.com/jprudent/05d3a2d2ed26e9d1b12a2770ebcdb5f3">compilable est ici</a>.

Seul le code du <em>tracer</em> a changé. L'idée est de dérouler le <em>tracee</em> uniquement en pas à pas et de s'arrêter quand <em>RIP</em> vaut l'adresse du breakpoint.

<pre><code> ./ptrace_ex6 400651 ./fizzbuzz
 RIP = 400651
 FizzBuzz, RIP = 400651
 1, RIP = 400651
 2, RIP = 400651

 ...
</code></pre>

C'est extrêmement simple comparé à l'autre implémentation mais cela ralentit beaucoup trop le <em>tracee</em>. En effet, pour chaque instruction exécutée il faut faire 2 <em>syscall</em>:

<ol>
<li><code>ptrace</code> <code>SINGLESTEP</code> qui fait passer le <em>tracee</em> de l'état <code>STOPPED</code>
à l'état <code>RUNNING</code> à l'état <code>STOPPED</code>

</li>
<li>

<code>wait</code> pour attendre que le <em>tracee</em> soit à l'état <code>STOPPED</code>

</li>
</ol>

Sachant, comme nous l'avons vu, qu'un <em>syscall</em> passe par une interruption pour redonner la main au kernel, cette méthode ne fonctionne en pratique que sur des petits programmes comme <code>fizzbuzz</code>.

<h1>Conclusion</h1>

C'était une bonne aventure ! Avant de m'y intéresser, un debugger était un outil magique aux mécanismes impalpables.

Je connais désormais les rouages d'un debugger sous Linux. Je pense que pour OSX, on doit avoir quelque chose de très similaire.

Ecrire cet article me permet de mieux comprendre la <a href="https://www.gnu.org/software/gdb/documentation/">documentation de GDB</a>.

Aussi, j'ai une bien meilleure cartographie des interactions entre un process et le noyau. Je comprends beaucoup mieux pourquoi on dit qu'un process bloque quand on fait de l'I/O, et j'en comprends le mécanisme.

<h1>Aller plus loin</h1>

Il y a deux commandes <code>ptrace</code> que je n'ai pas présentées :

<ul>
<li><code>ATTACH</code> permet de débugger un process existant, donc sans utiliser le mécanisme de <code>fork</code></li>
<li><code>SYSCALL</code> arrête le <em>tracee</em> à chaque <em>syscall</em>. Cela permet d'implémenter la commande <code>strace</code>.</li>
</ul>

Il existe aussi une troisième façon de poser des breakpoints: <em>hard breakpoints</em>. Ce mécanisme est implémenté directement au niveau du CPU via des registres dédiés.

<h1>Références</h1>

Cet article n'est pas tout à fait original. Ces quelques sources m'ont accompagnées. Si le sujet vous a intéressé, je vous en conseille vivement la lecture.

<ul>
<li><p><code>man 2 ptrace</code></p></li>
<li><p>Playing with ptrace <a href="http://www.linuxjournal.com/article/6100">Part I</a>, <a href="http://www.linuxjournal.com/article/6210">Part II</a></p></li>
<li><p>Nice <a href="https://mikecvet.wordpress.com/2010/08/14/ptrace-tutorial/">ptrace tutorial</a></p></li>
<li><p>Why <a href="http://lwn.net/Articles/371501/">ptrace is aweful</a> ?</p></li>
<li><p>How debuggers work <a href="http://eli.thegreenplace.net/2011/01/23/how-debuggers-work-part-1">part1</a>,   <a href="http://eli.thegreenplace.net/2011/01/27/how-debuggers-work-part-2-breakpoints">part2</a>,   <a href="http://eli.thegreenplace.net/2011/02/07/how-debuggers-work-part-3-debugging-information">part3</a>. En plus l'auteur a mis des références en bas de ses articles, ce que j'apprécie beaucoup ;)</p></li>
<li><p>Explication sur l'implémentation des <a href="https://www.kernel.org/doc/ols/2009/ols2009-pages-149-158.pdf">hardware breakpoints</a></p></li>
<li><p>Un autre article sur <a href="http://www.alexonlinux.com/how-debugger-works">le fonctionnement d'un debugger</a></p></li>
<li><p><a href="http://mainisusuallyafunction.blogspot.fr/2011/01/implementing-breakpoints-on-x86-linux.html">Implémentation d'un breakpoint</a></p></li>
</ul>
