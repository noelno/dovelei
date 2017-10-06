# Etude du site « Firefox is up to date | Mozilla Firefox 56 »
https://www.mozilla.org/en-US/firefox/56.0/whatsnew/?oldversion=55.0.3  
C'est la page qui s'affiche une fois la mise à jour du navigateur effectuée.

## Head

Il existe plusieurs versions de cette page, en plusieurs langues.   

Chaque version alternative est listée dans l'entête sous forme d'un élément `link`, avec un attribut `rel="alternate"` et un autre attribut `hreflang=""` pour spécifier la langue du lien.  

La version canonique (version américaine) figure en haut de cette liste avec un attribut `rel="canonical"`.  

On remarquera que bien que l'url de la page en cours contienne un paramètre (`oldversion`), ce paramètre n'a pas été repris dans l'url canonique ou dans les versions alternatives, probablement car la présence de ce paramètre n'a aucune incidence sur le contenu ou la présentation de la page, et sert uniquement à des fins statistiques.  

    <link rel="canonical" hreflang="en" href="https://www.mozilla.org/en-US/firefox/56.0/whatsnew/">
    <link rel="alternate" hreflang="x-default" href="https://www.mozilla.org/en-US/firefox/56.0/whatsnew/">
    <link rel="alternate" hreflang="bg" href="https://www.mozilla.org/bg/firefox/56.0/whatsnew/" title="Български">
    <link rel="alternate" hreflang="fa" href="https://www.mozilla.org/fa/firefox/56.0/whatsnew/" title="فارسی"> 
    <link rel="alternate" hreflang="fr" href="https://www.mozilla.org/fr/firefox/56.0/whatsnew/" title="Français">

On découvre au passage la valeur `x-default` pour l'attribut `hreflang`, qui existe pourtant depuis 2013 ! [Cf. l'annonce de Google Webmasters à ce sujet](https://webmasters.googleblog.com/2013/04/x-default-hreflang-for-international-pages.html)    
Cette valeur s'utilise pour :
* une page par défaut quand la langue de l'utilisateur n'est pas disponible
* une page qui charge automatiquement la bonne traduction en fonction des paramètres de l'utilisateur (entête `Accept-Language` envoyée par l'utilisateur dans ses requêtes).

On remarquera aussi que de tous les métas de l'entête du site, seuls les assets (js, css, favicons) ont des adresses relatives. C'est parce qu'il est impératif de déclarer l'url complète (absolue) des versions canoniques et alternatives, protocole inclus (cf. [les consignes de Google au sujet des urls canoniques](https://support.google.com/webmasters/answer/139066?hl=fr)).

Les developpeurs se sont permis de charger du JS directement après `<meta charset="utf-8">`, et avant même le `<title>`.   
Le fichier pèse seulement 6ko, suffisamment peu pour être chargé dès la connexion établie (14kb environ selon [cet article sur le critical rendering path sur le blog de Bocoup](https://bocoup.com/blog/performance-under-pressure)). 
Plusieurs fichiers js utilitaires y sont concaténés et minifiés.  

Note : le site de Mozilla est open source, comme tout ce qu'ils font. Leur bundle js de base non compressé est consultable ici : https://github.com/mozilla/bedrock/tree/master/media/js/base

Pour les métas réseaux sociaux, [des twitter cards de type `summary` et `app`](https://developer.twitter.com/en/docs/tweets/optimize-with-cards/guides/getting-started), un lien vers la page facebook de Mozilla et des propriétés opengraph sont déclarées : type, site_name, locale, url, image, title et description. 
Sur cette page les metas ne servent à rien (en tout cas pour la Twitter Card, je n'ai pas testé le reste) car l'indexation de la page est bloquée par la déclaration `<meta name="robots" content="noindex">`

    <meta property="og:type" content="website">
    <meta property="og:site_name" content="Mozilla">
    <meta property="og:locale" content="en_US">
    <meta property="og:url" content="https://www.mozilla.org/en-US/firefox/">
    <meta property="og:image" content="https://www.mozilla.org/media/img/firefox/template/page-image.af8027a425de.png">
    <meta property="og:title" content="See what’s new with Firefox">
    <meta property="og:description" content="Faster, more responsive and powerfully private. Check out the latest from the browser that has your back.">
    <meta property="fb:page_id" content="14696440021">
    <meta name="twitter:card" content="summary">
    <meta name="twitter:site" content="@firefox">
    <meta name="twitter:domain" content="mozilla.org">
    <meta name="twitter:app:name:googleplay" content="Firefox">
    <meta name="twitter:app:id:googleplay" content="org.mozilla.firefox">
    <meta name="twitter:app:name:iphone" content="Firefox">
    <meta name="twitter:app:id:iphone" content="989804926">
    <meta name="twitter:app:name:ipad" content="Firefox">
    <meta name="twitter:app:id:ipad" content="989804926">

Par curiosité j'ai été vérifier si ces metas apparaîssaient sur toutes les pages de leur site ou seulement sur les pages "What's new", et elles sont bien sur tout le site.  
Plus précisément, toutes les metas y sont sauf la `"noindex"`, dans le même ordre mais parfois avec des valeurs différentes. C'est probablement parce que le header est global qu'ils ont laissé ces metas OpenGraph et Twitter inutiles.

Il y a peu de favicons par-rapport [au nombre ahurissant de metas propriétaires disponibles](https://stackoverflow.com/a/26423923/6796546) : le `.ico` traditionnel, une icône taille maximum (196x196) et une icône Apple.

    <link rel="apple-touch-icon" type="image/png" sizes="180x180" href="/media/img/firefox/ios-icon-180.7a8401f21915.png">
    <link rel="icon" type="image/png" sizes="196x196" href="/media/img/firefox/favicon-196.223e1bcaf067.png">
    <link rel="shortcut icon" href="/media/img/firefox/favicon.dc6635050bf5.ico">

De toutes les metas "historiques", la seule à être utilisée ici est la description :

	<meta name="description" content="Built for speed. Primed for performance.">

Cette description varie pour chaque page du site mozilla.org.


Le site semble suivre une architecture modulaire, avec une feuille de style par module : une pour les styles globaux du site, une pour cette page (uniquement pour la version 56 de Firefox donc), une juste pour une modale et les styles pour les versions de IE inférieures à 9 dans un commentaire conditionnel. Ces feuilles sont chargées en fin du `head`.

## Body

### HTML

Le code est un très bon exemple en termes de sémantique.

Le site fait allègrement appel aux balises de section html5 plutôt qu'à de simples `div`s : un `header`, quelques `section`s, un `footer` contenant lui-même une `nav` qui contient plusieurs sections.  

La règle semble être d'utiliser une balise `section` uniquement si celle-ci contient un titre, dans le respect des [spécifications sur les sections](https://developers.whatwg.org/sections.html#headings-and-sections).  

La liste de nouvelles fonctionnalités, bien que très stylisée et écrite en gros comme un titre, est bien balisée comme une simple liste.

En ce qui concerne les liens internes, ils sont toujours relatifs.

On y trouve un exemple d'utilisation de l'attribut `rel="license"`, sur leur texte de copyright :

          <p class="license">Portions of this content are ©1998–2017 by individual mozilla.org contributors. Content available under a <a rel="license" href="/en-US/foundation/licensing/website-content/">Creative Commons license</a>.</p>

En bas à droite du site, il y a un menu déroulant de choix de langues. Là encore les développeurs ont pensé à tout :
* spécifier dans l'attribut `lang` de chaque option de quelle langue il s'agit (car le contenu et la valeur de l'option ne suffisent pas)
* une alternative no-js. Normalement le changement de page s'opère quand la valeur séléctionnée change. Mais lorsqu'il n'y a pas de javascript, un bouton permet d'envoyer le paramètre `lang` à l'url de la page en cours, et donc d'effectuer en back les traitements qui vont gérer la redirection vers la version de la langue choisie. Ingénieux.

```html
	<form id="lang_form" method="get" action="#">
	  <label for="page-language-select">Language</label>
	  <select id="page-language-select" name="lang" dir="ltr">
		
		  <option lang="ast" value="ast">Asturianu</option>      
		  <option lang="zh-TW" value="zh-TW">正體中文 (繁體)</option>
		
	  </select>
	  <noscript>
		<button type="submit">Go</button>
	  </noscript>
	</form>
```

Plusieurs choses m'étonnent par rapport à leur footer, plus précisément dans la navigation secondaire : 
* que des liens vers leur propre site portent l'attribut `rel="nofollow"`
* qu'ils n'aient pas utilisé de balise `small` pour baliser le texte de copyright maintenant que cette balise est sémantiquement lié aux « petites lettres » (disclaimer, copyright, termes de contrats écrits en tout petit en bas de la page…). C'est d'autant plus étonnant qu'ils ont appelé le bloc parent `small-links`, et donc qu'ils y ont pensé…

Je remarque aussi que le texte de copyright est inclus dans la navigation. J'en déduis que c'est uniquement dans un but pratique car vu où le texte est placé il aurait fallu fractionner la balise `nav` pour en extraire le texte de copyright, c'aurait été inutilement complexe.

Globalement le code est très lisible, simple et bien indenté, comme s'il avait été fait avec l'idée d'être relu par quelqu'un (cf. les commentaires HTML, notamment un message de recrutement pour les développeurs qui passeraient par là !)


### CSS

Pas de méthodologie complexe type BEM, on a droit à des noms de classes tout à fait classiques comme `.content`, `.colophon`, `.primary`, `.secondary`… et à l'application de style directement sur certains éléments (balises `h1` à `h6`, paragraphes…). 

Les noms de classes se rapportent quasiment tous au contenu et pas à la présentation, parfois de manière très précise : `.whats-next`, `.mozilla-links`, `.lang-switcher`… trop précise pour permettre une réutilisation de ces éléments sur d'autres blocs plus tards.
 

### JS

Contrairement au CSS les scripts ne sont pas tous chargés dans le pied de page mais à divers endroits du code.  

Il y a 7 scripts donc deux pour d'anciennes versions d'IE (notamment le polyfill [matchmedia](https://github.com/paulirish/matchMedia.js/) pour IE9).

Mozilla utilise Google Analytics, ce qui peut surprendre vu leur militantisme sur la protection des données personnelles, mais [il s'agit selon eux d'une version customisée et anonymisée](https://bugzilla.mozilla.org/show_bug.cgi?id=1122305#c8). 
