---
layout: post
title: Comment tester une méthode privée ?
date: 2016-01-12 09:31
author: fabien maury
comments: true
categories: [Bonnes pratiques de dév, clean code, private, Programmation, test, TU]
---
Dans presque toutes mes missions, il arrive ce moment où quelqu'un vient me poser ces questions:

<ul>
<li>"Est-ce que je dois tester une méthode privée ?"</li>
<li>"Je dois tester une méthode privée, comment faire ?"</li>
</ul>

Pour la première question, la réponse est simple: <strong>oui, mais non</strong>.

<em>Ha, ok...et pour la deuxième ?</em>

Et bien pour la deuxième question, ça dépend car il n'y a pas qu'une seule recette. Mais je vais ici essayer de vous donner les outils pour choisir la bonne solution.

<h3>Un peu de concret</h3>

En général, je propose à la personne de pair-programmer sur son problème car la réponse dépend essentiellement du code en question.

Admettons ce code-ci:

[java]
    private double pricePerKilometers;
    private int degressionLevel = 500;
    private IUserDao userDao;
    private IPriceFormatter priceFormatter;
    private ITicketDao ticketDao;
    private TicketValidator validatorTicket;
    
    
    public Ticket book(Trajet trajet){
        check(trajet);
        double price = computePrice(trajet);
        return generateTickets(trajet,price);
    }

    private Ticket generateTickets(Trajet trajet, double price) {
        User user = userDao.retrieveById(trajet.getUserId());
        String priceToPrint=priceFormatter.format(price,user.getLocale());
        Ticket ticket = new Ticket();
        ticket.setLastName(user.getLastName());
        ticket.setFirstName(user.getFirstName());
        ticket.setPrice(priceToPrint);
        ticket.setTrajet(trajet.getDepart()+&quot; - &quot;+trajet.getArrivee());
        validatorTicket.validate(ticket);
        ticketDao.save(ticket);
        return ticket;
    }

    private double computePrice(Trajet trajet) {
        long distance = getDistance(trajet);
        if(hasMileageDegression(distance)){
            return (pricePerKilometers * degressionLevel) + (pricePerKilometers * (distance-degressionLevel) * 0.5);
        }else{
            return pricePerKilometers * distance;
        }
    }

    private boolean hasMileageDegression(long distance) {
        return distance&gt; degressionLevel;
    }

    private long getDistance(Trajet trajet) {
        Coord origin = new Coord(trajet.getDepartLongitude(),trajet.getDepartLatitude());
        Coord destination = new Coord(trajet.getDestinationLongitude(),trajet.getDestinationLatitude());
        return origin.distanceFrom(destination);
    }

    private void check(Trajet trajet) {
        if(trajet==null || isBlank(trajet.getDepart()) || isBlank(trajet.getArrivee())){
            throw new IllegalArgumentException();
        }
    }

    private boolean isBlank(String string){
        return string==null || string.isEmpty();
    }

[/java]

<strong>Notre méthode publique à tester est :</strong>
[java]
public Ticket book(Trajet trajet)
[/java]

Nous pouvons ici trouver <strong>2 catégories de méthode privée :</strong>

<h3>les méthodes privées qui rendent lisible le code:</h3>

[java] private double computePrice(Trajet trajet) [/java]
[java] private boolean hasMileageDegression(long distance) [/java]
[java] private long getDistance(Trajet trajet) [/java]

Il s'agit de pur java SE, qui ne fait appel à aucune dépendance, c'est un détail d'implémentation.
Il ne faut ni mocker ces méthodes, ni les tester une deuxième fois (car oui, ces méthodes seront déjà testées au travers de notre méthode publique).

<blockquote>
  Vos tests doivent échouer pour les bonnes raisons
</blockquote>

Tout ce qui fige vos détails d'implémentations est <strong>contre-productif</strong>:
- exposer une méthode privée pour la tester
- utiliser trop de mock et pas assez de stub dans un test (les stubs sont plus permissifs et concernent les interactions nécessaires au test mais qui sont sans rapport avec la règle de gestion testée)
- mocker une méthode privée: c'est une interdiction de refactorer. Dans ce cas vous ne pouvez plus modifier les détails d'implémentations sans casser les tests.

Il faut laisser une porte ouverte au refactoring.

<blockquote>
  Nous souhaitons tester afin de refactorer, pour ne plus avoir peur des modifications.
</blockquote>

<h3>2ème type de méthode privée: les collaborateurs en devenir</h3>

Voici un cas intéressant:
[java]private Ticket generateTickets(Trajet trajet, double price)[/java]

Cette méthode fait appel à un dao pour retrouver un utilisateur, construit un objet puis le sauvegarde après l'avoir validé.
Elle nous fait rajouter à elle seule <strong>4 dépendances</strong>, qui ne sont utilisées nulle part ailleurs, et dont on a du mal à voir le rapport avec le booking d'un billet d'aéroglisseur.

<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/12/corporate-parenting.jpg"><img src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/corporate-parenting-300x141.jpg" alt="corporate-parenting" width="300" height="141" class="aligncenter size-medium wp-image-3616" /></a>

C'est en général cette méthode que vous souhaitez tester car on y a trouvé un bug. Mais pour arriver dans cette méthode dans l'état souhaité, vous allez devoir vous retourner le cerveau et construire des objets qui n'ont aucun rapport avec votre cas.

L'exemple est édulcoré, mais sur un cas réel elle se trouverait 4 niveaux de profondeur plus bas, et manipulerait une librairie de génération de PDF.
Vous connaissez cette méthode, c'est une amie proche, elle hante vos projets.

Et oui vous <strong>souhaitez la tester</strong> car on attend d'elle des <strong>comportements spécifiques</strong>, indépendamment des comportements de la méthode publique.
Je considère que cette méthode privée est une violation de <strong>SRP</strong> (single responsibility principle) et qu'elle n'a rien à faire ici.
Nous allons donc la déplacer dans une classe TicketGenerator (ou une autre classe mieux nommée), qui aura ses propres tests car a ses propres comportements.

En déplaçant cette méthode dans un nouveau collaborateur, vous allez éliminer plusieurs dépendances de la première classe, épurant votre initialisation de mocks, et gagner ainsi en lisibilité dans vos tests.

<h3>Oui mais sur mon projet.....</h3>

En effet parfois c'est plus compliqué: on se retrouve dans un cas similaire, mais vous ne comprenez pas ce que fait cette méthode.
<a href="http://www.arolla.fr/blog/wp-content/uploads/2015/12/Touched-By-His-Noodly-Appendage.jpg"><img src="http://www.arolla.fr/blog/wp-content/uploads/2015/12/Touched-By-His-Noodly-Appendage-300x225.jpg" alt="Touched-By-His-Noodly-Appendage" width="300" height="225" class="aligncenter size-medium wp-image-3617" /></a>

L'extraire, c'est devoir la mocker dans votre test de la méthode publique de la première classe, et <strong>pour simuler le comportement d'une classe, il faut la comprendre</strong>.
Si votre application en est à ce stade de dégradation, vous aurez du mal à vous en sortir avec des tests unitaires classiques, et vous devrez sans doute vous tourner vers des tests end-to-end fonctionnels ou des outils de génération de TU à base de record/replay pour faire un harnais temporaire suffisant.

<h3>A vous de jouer</h3>

Concrètement il n'y a pas de recette miracle, et c'est à vous de déterminer le bon choix à faire en fonction de votre niveau de confiance, de la couverture de tests déjà en place, de votre connaissance de cette partie de l'application etc. Normalement, extraire une méthode dans une nouvelle classe comporte peu de risques, mais rien ne vaut quelques tests end-to-end pour être serein (ne pas hésiter à tester manuellement si besoin).
Le pair-programming n'est jamais un luxe dans ce type de cas, donc n'hésitez pas à faire appel à un ami.
