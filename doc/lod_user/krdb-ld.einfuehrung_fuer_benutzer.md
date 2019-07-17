# RDF-Modell der Zürcher Kantonsratsdatenbank

## Thema

[Die Zürcher Kantonsratsdatenbank](https://wahlen-abstimmungen.zh.ch/internet/justiz_inneres/wahlen-abstimmungen/de/wahlen/krdaten_staatsarchiv.html) modelliert die personelle Zusammensetzung der Zürcher Legislative (Grosser Rat / Kantonsrat) und Exekutive (Kleiner Rat / Regierungsrat) im zeitlichen Verlauf seit 1803.  "Im zeitlichen Verlauf" heisst, dass alle wichtigen Modell-Elemente mit einer Zeitspanne parametriert sind, die den Gültigkeitsbereich der Existenz bzw. Aussage präzisiert.

In seiner jetzigen Form fokussiert sich das Modell auf die Zusammensetzung des jeweiligen Rates.  Der Vorgang, durch den diese Zusammensetzung zustandekommt, z.B. eine Wahl, wird nicht modelliert.


## Herkunft

Um die Integration der Kantonsratsdatenbank mit anderen offenen Daten zu erleichtern, wurde die KRDB ins [RDF](https://www.w3.org/TR/rdf11-primer/)-Format konvertiert, [dessen Ökosystem](http://dbis.informatik.uni-freiburg.de/lehre/WS1011/Spezialvorlesung/Webbasierte+Informationssysteme/folien.html) besonders geeignet ist,

* Bedeutung greifbar zu machen
* verteilte Daten {eigentlich besser: verteiltes Wissen} zu integrieren

.  

Das RDF-Modell der Zürcher Kantonsratsdatenbank ist entstanden aus einer Konvertierung der relationalen Kantonsratsdatenbank auf dem "Umweg" über einen [xlsx-Export](https://www.web.statistik.zh.ch:8443/KRRR/app?show_page=EXCEL&operation=EXCEL) und csv.

Im ersten Schritt wurde eine 1:1-Konvertierung vorgenommen:

* jeder Tabelle entspricht eine Klasse
* jeder Tabellenzeile entspricht eine Instanz dieser Klasse
* jeder Zelle in einer Zeile entspricht ein Objekt, auf das durch die Beziehung {Property}, die durch den Spaltennamen gegeben ist, verwiesen wird.

Im zweiten Schritt dieser Konvertierung wurden

* implizite Entitäten explizit gemacht ( = verdinglicht, objektifiziert, reifiziert)
* Beziehungen explizit gemacht, die vorher nur als Foreign-Key-"Matches" vorlagen
* implizite Typen als Sub-Typen dargestellt (Funktion, Zivilstand, Geschlecht)

Die Ergebnisse beider Schritte sind auf dem Triplestore verfügbar.

* das Ergebnis des ersten Schritts wird als Reifegrad "00" bezeichnet
* das Ergebnis des zweiten Schritts wird als Reifegrand "10" bezeichnet.


## Entwurfsprinzipien

Bevor im nächsten Abschnitt das Modell vorgestellt wird, erläutern wir zunächst einige Design-Entscheidungen, da mit ihrer Hilfe das Verständnis für die Gestalt des Modells deutlich erleichtert wird.  Wer möchte, kann diesen Abschnitt überspringen und später bei Bedarf zurückkehren.


### Trennung der Meta-Ebenen

Das wichtigste Modularisierungs-Kriterium ist die Trennung der Meta-Ebenen.  Das Wort "Meta" wird dabei im speziellen Sinn der Meta-Modellierung verwendet, wie wir das von der [Meta Object Facility der OMG](https://www.omg.org/mof/) kennen  {siehe z.B. Folie 6 in [Uwe Assmann: Metamodelling and Metaprogramming](st.inf.tu-dresden.de/files/teaching/ss11/cbse/slides/02-metamodelling.pdf)}.

Auch wenn uns die technologische Basis des RDF-Oekosystems nicht dazu zwingt, bleibt es eine gute Idee, [Modell-Meta-Ebenen](https://flylib.com/books/en/1.508.1.61/1/) getrennt zu halten.

Die beiden wichtigsten Meta-Ebenen sind

* die Instanz-Ebene {die grosse Masse der konkreten Dinge}
* die Klassen- bzw. Schema- Ebene, auf der Repräsentanten gleich*artig*er Instanz-Dinge "leben".


### Modularisierung

Als Modularisierungs-Mittel dienen Namespace-Prefices, wie man sie von XML kennt und sogenannte "Named-Graph"en. 
Die einzelnen Teil-Modelle werden in separate Named-Graphen abgelegt.

#### Identifier-"Syntax"

Identifier werden systematisch konstruiert und einmalig bei der Entstehung des jeweiligen Modell-Elements vergeben.
Diese Systematik hat globale Eindeutigkeit zum Ziel.

In der RDF-Welt ist es üblich, globale Eindeutigkeit von Identifiern dadurch zu erreichen, dass man Eindeutigkeit separat in einem vorangestellten globalen und einem angehängten lokalen Teil des Identifier-Strings gewährleistet.

Zur globalen Eindeutigkeit setzt man auf die Strukturen des ICANN-gemanageten DNS-Namensraums auf [und kehrt unter den Teppich, dass auch DNS-Domänen nicht ewig gelten...].

Wer eine DNS-Domäne gebucht hat, hat darunter die Hoheit über eine Namensraum-Hierarchie seiner Wahl.

Wir haben uns hier vorläufig für <tt>https://ke.ji.zh.ch</tt> als Kürzel für "Knowledge-Engineering" unterhalb von "Justiz und Inneres" unterhalb von "Zürich" in der Schweiz entschieden.

Unter dieser Sub-Domain geht es mit "krdbld/\${reifegrad}/\${meta}/\${klasse}" weiter.

Die Werte für "reifegrad" sind bisher

* "<tt>00</tt>" für das Ergebnis der 1:1-Konvertierung der relationalen KRDB
* "<tt>10</tt>" für das Ergebnis der ersten semantischen Modell-zu-Modell-Transformation
.

Für "meta" lauten die Werte:

* "<tt>res</tt>" für die Instanz-Ebene
* "<tt>genus</tt>" für Klassen
* "<tt>rel</tt>" für Relationen|Beziehungen|Properties auf Klassen-Ebene
* "<tt>meta</tt>" für andere Aussagen *über* Instanzen, die nicht Struktur-Aussagen {"Meta-Modell"} sind
* "<tt>prov</tt>" für Herkunfts-Aussagen
* "<tt>copula</tt>" für Ontologie-Mapping-Aussagen
* "<tt>affix</tt>" für Aussagen, wie eigene Modell-Elemente zu externen Modell-Elementen in Beziehung stehen {typischerweise Subsumption externer Dinglichkeiten}

"klasse" nennt bei Instanzen deren [Haupt-]Typ, auf Klassen-|Typ-|Schema-Ebene ist der einzige Wert "omnes".

##### Beispiele

###### Die Personen-Instanz <tt>\<https://ke.ji.zh.ch/krdb/ld/10/res/person/10003\></tt>

"<tt>https://ke.ji.zh.ch/</tt>" spezifiziert den Verantwortungsbereich.  \
Top-down, von hinten nach vorn gelesen, wie bei DNS-Namen üblich:\
"<tt>ch</tt>" ist die Top-Level-Domain der Schweiz, \
"<tt>zh</tt>" als Second-Level-DNS-Domain ist vom Kanton Zürich registriert, \
"<tt>ji</tt>" steht darunter für "Justiz und Inneres" und \
"<tt>ke</tt>" für das Thema "Knowledge Engineering" 

"<tt>krdb</tt>" steht für das Thema "Kantonsrats-Datenbank" \
"<tt>ld</tt>" steht für die Linked-Data-Version der KRDB \
"<tt>10</tt>" steht für die in ein assoziations-klassen-basiertes Domänen-Modell konvertierte Variante der LD-KRDB \
"<tt>res</tt>" umfasst die Instanz-Ebene des Modells \
"<tt>person</tt>" umfasst die Instanzen der Klasse "Person" \
"<tt>10003</tt>" ist der lokale Identifier der Personen-Instanz  {Emil Strehler-Hess}

###### Übung

Lassen Sie sich mit der SPARQL-Abfrage \
<blockquote>
[<tt>SELECT ?p ?o \
WHERE \
{ \
&nbsp;&nbsp;GRAPH ?g \
&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;\<https://ke.ji.zh.ch/krdb/ld/10/res/person/10003\> ?p ?o . \
&nbsp;&nbsp;} \
</tt>}](https://test.lindas-data.ch:8443/zhkrdb#!/query/SELECT%20%3Fp%20%3Fo%0AWHERE%0A{%0A%20%20GRAPH%20%3Fg%20%0A%20%20{%0A%20%20%20%20%3Chttps%3A%2F%2Fke.ji.zh.ch%2Fkrdb%2Fld%2F10%2Fres%2Fperson%2F10003%3E%20%3Fp%20%3Fo%20.%0A%20%20}%0A})\
</blockquote>
alle Attribute der Personen-Instanz mit de Emil Strehler-Hess anzeigen.


###### Die Buergerort-Klasse <tt>\<https://ke.ji.zh.ch/krdb/ld/10/genus/omnes/Buergerort\></tt>

"<tt>genus</tt>" steht für die Klassen auf Schema-Ebene des Modells \
"<tt>omnes</tt>" ist der einzige gültige Wert für Klassen.  Selbstverständlich ist es nicht sinnvoll, auf Schema-Ebene den Bestand weiter in Klassen zu zersplittern. \
"<tt>Buergerort</tt>" ist der lokale Identifier der Bürgerort-Klasse 

###### Die buergerort-Beziehung <tt>\<https://ke.ji.zh.ch/krdb/ld/10/relatio/omnes/buergerort\></tt>

"<tt>relatio</tt>" steht für die Beziehungs-Typen auf Schema-Ebene des Modells \
"<tt>omnes</tt>" ist der einzige gültige Wert für Beziehungs-Typen.  Selbstverständlich ist es nicht sinnvoll, auf Schema-Ebene den Bestand weiter in Beziehungs-Typen zu zersplittern. \
"<tt>buergerort</tt>" ist der lokale Identifier des buergerort-Beziehungs-Typs

###### Bemerkungen zu den beiden vorangegangenen Beispielen

Es gibt also zwei Modell-Elemente, die ähnlich aussehende IDs haben: die Entität <tt>Buergerort</tt> und die Beziehung "<tt>buergerort</tt>".  Selbst wenn sie im gleichen Namensraum lägen, könnte man sie unterscheiden, da Gross- und Klein-Schreibung bei IRIs signifikant sind.  Da lokale Identifier von Entitäten und Beziehungen im Allgemeinen durchaus gleich sein können  können wir sie aber unterscheiden, da im Namensraum einmal "<tt>relatio</tt>" und einmal "<tt>genus</tt>" vorkommt.


#### Named Graphen

Named Graphen sind Container, die Mengen von Tripeln zur Laufzeit voneinander getrennt beherbergen.  Man nennt Triple-Stores, die dieses Modularisierungs-Mittel anbieten, oft auch Quad-Stores, da die Zugehörigkeit eines Tripels zu einem Named-Graphen dadurch implementiert wird, dass man das Tripel mit dem Graphen-Identifier als viertem Element erweitert wird.

Da LINDAS in diesem Vorhaben die Publikations-Plattform ist, übernehmen wir deren Graphen-Namens-Konvention:

* Prefix: "<tt>https://linked.opendata.swiss/graph/zhkrdb/ld/</tt>" \
  Das "<tt>zhkrdb/ld</tt>" wurde mit den LINDAS-Betreibern abgestimmt um Eindeutigkeit zu garantieren und Namensraum-Wildwuchs zu reduzieren
* darunter: "<tt>\${reifegrad}/\${meta}/\${klasse}</tt>" \
  die Unterteilung unterhalb des Praefices ist den KRDB-LD-Owner ueberlassen.  Wir verwenden die gleiche "Hierarchie" wie bei der Identifier-"Syntax".  Beziehung kommen in denjenigen Named-Graphen, in dem auch deren Quell-Entität liegt.
.

##### Übungen

Lassen Sie sich mit der SPARQL-Abfrage \
<blockquote>
[<tt>SELECT DISTINCT ?g \
WHERE \
{ \
&nbsp;&nbsp;GRAPH ?g \
&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;?s ?p ?o . \
&nbsp;&nbsp;} \
}</tt>](https://test.lindas-data.ch:8443/zhkrdb#!/query/SELECT%20DISTINCT%20%3Fg%20%0AWHERE%0A{%20%0A%20%20GRAPH%20%3Fg%20%0A%20%20{%20%0A%20%20%20%20%3Fs%20%3Fp%20%3Fo%20.%0A%20%20}%20%0A}%0A)\
</blockquote>
alle Named-Graphen anzeigen.

Lassen Sie sich mit der SPARQL-Abfrage \
<blockquote>
[<tt>SELECT * \
WHERE \
{ \
&nbsp;&nbsp;GRAPH \<https://linked.opendata.swiss/graph/zhkrdb/ld/10/res/rat\> \
&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;?s ?p ?o . \
&nbsp;&nbsp;} \
}</tt>](https://test.lindas-data.ch:8443/zhkrdb#!/query/SELECT%20*%0AWHERE%20%0A{%20%0A%20%20GRAPH%20%3Chttps%3A%2F%2Flinked.opendata.swiss%2Fgraph%2Fzhkrdb%2Fld%2F10%2Fres%2Frat%3E%20%0A%20%20{%20%0A%09%3Fs%20%3Fp%20%3Fo%20.%0A%20%20}%20%0A})\
</blockquote>
alle Tripel im Named-Graphen <tt>\<https://linked.opendata.swiss/graph/zhkrdb/ld/10/res/rat\></tt> anzeigen.

Normalerweise werden Tripel in den ihrem Namens-Präfices entsprechenden Named-Graphen abgelegt.  Hiervon gibt es zwei Ausnahmen:

* auf Klassen-|Typ-|Schema-Ebene werden sowohl Klassen {mit der Meta-Kategorie "<tt>genus</tt>" im Identifier} als auch Relationen im Named-Graphen "<tt>genus</tt>" abgelegt.
* sowohl Herkunfts-Aussagen {"<tt>prov</tt>" im Identifier} als auch andere Meta-Aussagen {"<tt>meta</tt>" im Identifier} werden im Named-Graphen "<tt>meta</tt>" abgelegt.


### Separierung nicht-fachlicher Aspekte

Ein fachliches Modell sollte nichts anderes enthalten, als eine Theorie der fachlichen Domäne.

Enthielte dieses Modell auch Anderes, könnte eine Maschine {ein Reasoner z.B.} nicht von sich aus zwischen Fachlichkeit und diesem Anderen unterscheiden.

Deswegen ist es eine wichtige Strategie, die nicht-fachlichen Belange pro Belang jeweils in einen separates Modul, bei uns: einen separaten Named-Graphen, abzuspalten. 


#### Syntax und Semantik

Grob kann man sagen: die Syntax {wie kommuniziert man über etwas?} steckt in den Strings, die Semantik {was ist es?} in den Bezügen {also im Graphen}.

Dies ist bereits im Design von RDF verankert:

* es gibt eine spezielle Beziehung {"Property"} namens "<tt>rdfs:label</tt>", die von einem semantischen Element {Knoten oder Kante} auf ein String-Literal verweist.  Pro semantisches Element können mehrere solcher "Labels", z.B. in verschiedenen Sprachen, vorhanden sein.
* [fast, dh. bis auf <tt>comment</tt>s, s.u.] alles andere sind semantische Aussagen über das Wesen der modellierten Dinge.

Auch im Kantonsrats-Modell gibt es viele Klassen, die ein Attribut namens "<tt>\*bezeichnung</tt>" tragen.  Das ist ein String-Literal, auf das via eine Property namens "<tt>\*bezeichnung</tt>" verwiesen wird.  All diese "<tt>\*bezeichnung</tt>"en sind [im "<tt>affix</tt>"-Named-Graphen deklariert als] "<tt>subPropertyOf</tt>" "<tt>rdfs:label</tt>". 


##### Übung

Lassen Sie sich mit der SPARQL-Abfrage \
<blockquote>
[<tt>SELECT * \
WHERE \
{ \
&nbsp;&nbsp;GRAPH \<https://linked.opendata.swiss/graph/zhkrdb/ld/10/genus/omnes\> \
&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;?s rdfs:label ?label . \
&nbsp;&nbsp;} \
}</tt>](https://test.lindas-data.ch:8443/zhkrdb#!/query/SELECT%20*%0AWHERE%20%0A{%20%0A%20%20GRAPH%20%3Chttps%3A%2F%2Flinked.opendata.swiss%2Fgraph%2Fzhkrdb%2Fld%2F10%2Fgenus%2Fomnes%3E%20%0A%20%20{%20%0A%09%3Fs%20rdfs%3Alabel%20%3Flabel%20.%0A%20%20}%20%0A})\
</blockquote>
alle Labels im Schema-Graphen <tt>\<https://linked.opendata.swiss/graph/zhkrdb/ld/10/genus/omnes\></tt> anzeigen.


#### Herkunft (z.B. Literatur- und Quellen-Nachweise) 

Ein typischer nicht-fachlicher Belang vieler Modelle sind Angaben über die Herkunft einzelner Modell-Bestandteile.  Dabei werden typischerweise Aussagen gemacht, in welchem Kontext {wann, wo, von wem} ein Modell-Element erzeugt wurde. 


#### Ontologie-Mapping

Wenn wir philosophische Spitzfindigkeiten aussen vor lassen, können wir sagen: es gibt *eine* Realität und *viele* Modelle davon.  Jedes Modell ist eine stark vereinfachte Perspektive auf die Realität.  

Im Allgemeinen entwickeln sich nach Bedarf und unkoordiniert {woher sollten die Modellierer auch vor der Publikation schon voneinander wissen} verschiedene Modelle derselben Dinge der Realität.

Später erfahren die Modellierer von dem Tatbestand, dass es auch andere Modelle derselben Dinge gibt.  Aus diesem Muster hat sich die Disziplin des sogenannten Ontology-Mapping entwickelt.  Im Kern repräsentieren alle Ontology-Mapping-Ansätze das reale Ding durch einen Knoten im Modell, der als Attribute Verweise auf die anderen Modell-Elemente enthält, die das gleiche Real-Ding modellieren.  

Da die verschiedenen Modelle ein und derselben Real-Sache nichts miteinander gemein haben, wird solch ein Mapping-Angel-Punkt typischerweise nur für Navigations-Zwecke, aber nicht für semantische Zwecke {z.B. zum Inferieren} benutzt.


#### Dokumentation

Wie in jeder Programmier-Sprache gibt es auch in RDF eine Möglichkeit, das Vorliegende zu dokumentieren.  Dies wird durch eine spezielle, vordefinierte Beziehung namens "<tt>rdfs:comment</tt>" ausgedrückt. \
Obwohl es im Wortsinn eine Meta-Aussage über ein Modell-Element ist, und damit eigentlich keinem fachlichen Zweck dient -- man es unserer oben formulierten Strategie nach also dem fachlichen Modell weg-separieren sollte -- haben wir darauf verzichtet, dies zu tun, da "der Rest der Welt" es auch nicht tut, was in der Praxis dann mangels entsprechender Convention dazu führen würde, dass man nur nach der Dokumentation suchen würde [oder nicht mal das, da man sie evtl. für gar nicht existent hielte].

##### Übung

Lassen Sie sich mit der SPARQL-Abfrage \
<blockquote>
[<tt>SELECT * \
WHERE \
{ \
&nbsp;&nbsp;GRAPH ?g \
&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;?s rdfs:comment ?comment . \
&nbsp;&nbsp;} \
}</tt>](https://test.lindas-data.ch:8443/zhkrdb#!/query/SELECT%20*%0AWHERE%20%0A{%20%0A%20%20GRAPH%20%3Fg%20%0A%20%20{%20%0A%09%3Fs%20rdfs%3Acomment%20%3Fcomment%20.%0A%20%20}%20%0A})\
</blockquote>
alle Kommentare unserer Bestände anzeigen.


## Integration mit anderen Wissens-Quellen

Zur Integration mit anderen Quellen gibt es im RDF-Ökosystem mindestens die folgenden Mechanismen:

### Direkte Benutzung

Die einfachste und natürlichste Form der Wiederbenutzung ist die direkte Verwendung.  Dazu gibt man einfach den Identifier des Modell-Elements, das man benutzt, an.

Selbstverständlich holt man sich damit die ganze Semantik, die an diesem Modell-Element hängt, "ins Haus".  Das ist gut so -- sollte aber auch der eigenen Absicht entsprechen.

Das aktuelle KRDB-LD-Modell nutzt keine Fremd-Modell-Elemente im semantischen Sinn direkt.  Lediglich im Ontologie-Mapping-Graphen werden Fremd-Identifier benutzt, im folgenden Beispiel die der Gemeinsamen Norm-Datei:

<blockquote>
[<tt>SELECT * \
WHERE \
{ \	
&nbsp;&nbsp;GRAPH	\<https://linked.opendata.swiss/graph/zhkrdb/ld/10/copula/person\> \
&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;?s \<https://ke.ji.zh.ch/krdb/ld/10/relatio/omnes/eq_real_gnd\>  ?o . \
&nbsp;&nbsp;} \
} \
</tt>](https://test.lindas-data.ch:8443/zhkrdb#!/query/select%09*%0Awhere%0A{%09%0A%09graph%09%3Chttps%3A%2F%2Flinked.opendata.swiss%2Fgraph%2Fzhkrdb%2Fld%2F10%2Fcopula%2Fperson%3E%09%0A%09{%09%0A%20%20%20%09%09%3Fs%20%3Chttps%3A%2F%2Fke.ji.zh.ch%2Fkrdb%2Fld%2F10%2Frelatio%2Fomnes%2Feq_real_gnd%3E%20%20%3Fo%20.%0A%20%20%20%20}%09%0A}%0A)
</blockquote>


### Federated Queries

Mit einer "federated Query" kann man an einen SPARQL-Endpoint eine "<tt>SERVICE</tt>"-Teil-Abfrage stellen, die er an einen anderen SPARQL-Endpoint weitergibt und dessen Ergebnisse in den lokalen Abfrage-Teil integriert:

<blockquote>
<tt>SELECT ?a \
FROM \<mybooks.rdf\> \
{ \
&nbsp;&nbsp;?b dc:title ?title . \
&nbsp;&nbsp;SERVICE \<http://sparql.org/books\> \
&nbsp;&nbsp;&nbsp;&nbsp;{ ?s dc:title ?title . ?s dc:creator ?a } \
}</tt>
</blockquote>


### Theorie-Alignment

Hier nutzen mehrere Instanz-Ressourcen die selbe Ontologie {Modell auf Klassen-Ebene, Theorie}.  Werden mehrere solche Instanz-Ressourcen parallel zusammen genutzt, fühlen sie sich bei der Nutzung, z.B. SPARQL-Queries an als wären sie eins.  Sie haben ja die gleiche Struktur und die Muster im Bedingungsteil von Abfragen sind immer am [gemeinsamen] Meta-Modell entlang gebaut.


### "Einhängen" in andere Ontologien

Modelle wachsen [leider] meistens von unten nach oben und tragen ihre eigenen aus dem lokalen ad-hoc-Kontext entstandenen Strukturen.  Stellt man später fest, dass Teil-Strukturen zu anderen, evtl. schon verbreiteteren Modellen passen, kann man durch einen simplen Trick {die Idee dahinter ist die gleiche wie beim ["Adapter"-Pattern des GoF-Buchs](https://www.geeksforgeeks.org/adapter-pattern/)} Kompatibilität herbeiführen:

Man behauptet einfach, eine eigene Struktur sei Sub-Struktur einer bestimmten Fremdstruktur, die ähnlich genug aussieht.  

Der Zweck dieses Einhaengens ist die Vereinfachung von SPARQL-Queries und anderen Meta-Modell-spezifischen Traversier-Taetigkeiten.  

Generell muessen SPARQL-Queries an der Struktur des befragten Materials ausgerichtet sein.  Werden mehrere Quellen unterschiedlicher Struktur gleichzeitig befragt, fuehrt dies zu Abfragen, die die gesamte [benutzte] Strukturvielfalt beinhalten.  Der Anteil der Struktur-Vielfalt, der keine fachliche Ursache hat, sondern Syntactic-Sugar der Modellier-Beliebigkeit des Materials darstellt, kann wegreduziert werden, wenn man bedeutungsgleiche Struktur-Elemente von einem gemeinsamen externen "Aufhaenger" subsumieren (=erben) laesst.  Das RDFS-Inferencing des Triple-Stores sorgt dann dafuer, dass man fuer jeden Aufhaenger auch all dessen Sub-Strukturen (Sub-Klasse, Sub-Property) geliefert bekommt.  Damit blickt ein Abfrager sozusagen mit der Brille der Einhaenge-Ontologie auf das Material, das "die da, in der Zuercher Enge" gebastelt haben und braucht sich nur insoweit um unsere lokalen Modellier-Details kuemmern, wie sie sich vom Subsumierten unterscheiden [sofern das fuer ihn relevant ist].


Ein häufig verwendeter Fall ist, eine Beziehung im eigenen Modell, die auf ein Literal zeigt, zur <tt>subPropertyOf</tt> <tt>rdfs:label</tt> zu machen.

Sucht dann jemand nach allen Labels, bekommt er automatisch auch die eigenen Literal-Attribute mitgeliefert.


### Ontologie-Mapping

Wie oben schon gesagt, sind Ontologie-Mapping-Informationen ihrem Wesen nach nicht semantisch.  Trotzdem sind die Verweis-Kanten ein Hilfsmittel um Redundanzen im Grossen zu verringern und die anderen, gerade genannten Intergrations-Mechanismen zu unterstützen.


## Die inhaltlichen Kernkonzepte des Modells

<img style="uml_diagram" src="classdiagram_10_03.svg" alt="KRDB-Klassen-Modell in UML-Form" width="100%" />


### Der Rat und seine Mitglieder

#### Entitäten

Die beiden zentralen Entitäten des Modells sind "Rat" und "Person".


##### Rat

Im obigem UML-Diagram liegt die Rats-Entität ganz links, da sie das komplexeste Objekt ist.

Diese Abhängigkeits-Richtung ist auch ganz natürlich, da man erwartet, den Rat nach seinen Mitgliedern befragen zu können, während es unangemessen anstrengend wäre, jede Person des "Universums" danach zu befragen, welcher Räte Mitglied sie ist.


##### Person

Die zweite essentielle Entität ist die Person.  Das Kantonsrats-Modell zeichnet hierfür das Bild eines Bürgers, der Beziehungen zu anderen Personen {via Zivilstand} und zu Institutionen haben kann, irgendwo wohnt, einen Beruf, ggf. akademischen Titel, eine Religionszugehörigkeit, ein Geschlecht und als schweizer Spezialität einen Bürgerort "hat".  

Die Menge an hier so beschriebenen Personen-Instanzen umfasst bisher Menschen, die irgendwann einmal Mitglied eines Zürcher Rates waren sowie deren Ehepartner.  Es spräche aber nichts dagegen, eine Personen-"Liste" in Anspruch zu nehmen, die umfassender, also nicht auf Ratsmitglieder beschränkt ist.

Gewissermassen ist "<tt>Person</tt>" eine abstrakte Klasse, da sie eine geschlechtslose Person modelliert.  Davon gibt es typischerweise keine Instanzen.

Das Geschlecht einer Person ist als entsprechende Subklasse von Person modelliert:

* "<tt>Person\_w</tt>" für weibliche Personen
* "<tt>Person\_m</tt>" für männliche Personen
.

#### Beziehungen

Bereits in obigem UML-Diagramm sehen wir die einfachen Beziehungen, die "<tt>Person</tt>" zu ihren Attributen hat {<tt>Wohnort</tt>, <tt>Beruf</tt>, ...}.  Diese Beziehungen werden weiter unten kurz erläutert.

Die wichtigen Beziehungen in unserem Modell sind aber in Wirklichkeit komplexer als wir sie in obigem UML-Diagramm dargestellt haben.  Sie tragen vielmehr als Nutzlast ein kleines Modell, das die Art der Beziehung im Detail beschreibt.  Ein Mittel, diese Nutzlast an eine Beziehung zu hängen, ist, die direkte Kante durch eine Assoziationsklasse zu unterbrechen, an der dieses kleine Beziehungs-Modell hängt.  In unserem Fall lauten diese Assoziationsklassen "<tt>Einsitz</tt>", "<tt>Bindung</tt>" und "<tt>Zivilstand</tt>":

<img style="uml_diagram" src="classdiagram_10_02.svg" alt="KRDB-Klassen-Modell in UML-Form" width="100%" />


##### Person in der Mitgliedschafts-Rolle

Die zentrale Beziehung des Modells ist die der Mitgliedschaft, die beschreibt, welche Personen dem Rat während welcher Zeitspannen angehört haben.  Eine Person steht also in der Rolle eines Mitglieds in Beziehung zum Rat.

An dieser Beziehung hängt über die Assoziationsklasse "<tt>Einsitz</tt>" als "Nutzlast" ein kleines Modell, das den Charakter der Mitgliedschaft genauer beschreibt:

* ausser der einfachen Mitgliedschaft, gibt es Unterarten {"<tt>Funktion</tt>"}, die speziellere Rollen beschreiben, z.B. verschiedene Arten von Präsident-schaft.
* die Mitgliedschaft ist fallweise einer politischen Interessensvertretung (<tt>Partei</tt>, <tt>Fraktion</tt>) zugeordnet
* die Mitgliedschaft ist einem <tt>Wahlkreis</tt> zugeordnet


##### Person in der Rolle der Gebundenheit an die Interessen einer Institution

Um mögliche Interessenskonflikte, in denen ein Ratsmitglied stehen könnte, transparenter zu machen, werden Institutionen modelliert, die an eine Person Erwartungen an sein Verhalten haben könnten.  Z.B. sind das wirtschaftliche Verpflichtungen, die eine Person -- insbesondere ein Ratsmitglied -- gegenüber einer Institution haben kann, von der er Geld erhält, z.B. Unternehmensbeteiligungen.

Seit Beginn dieses Jahrhunderts ist die Transparenz sogar als Offenlegungspflicht für Interessensbindungen gesetzlich festgeschrieben, im Kt. ZH zB im [Kantonsratsgesetz LS 171.1 . 5a](http://www2.zhlex.zh.ch/Appl/zhlex_r.nsf/0/08C56C56E70A348AC12581F4002999EC/$file/171.1_5.4.81_99.pdf), ebenso für den [Regierungsrat \(RRB 2017/112\)](https://www.zh.ch/bin/ktzh/rrb/beschluss.pdf?rrbNr=112&name=RRB-2017-112&year=2017&_charset_=UTF-8). Zumindestens die polit. Bindungen wurden hier in der KRDB auch fallweise auch für ältere Mitglieder erhoben, z.B. "Gemeinderat Meilen - Präsident, von-bis"...


##### Person in der Rolle als Zivilstands-Partner

Interessenskonflikte können sich aber nicht nur durch wirtschaftliche Beziehungen ergeben.  Auch soziale Bindungen eines Rates können sein Verhalten beeinflussen.  Die herausragende aktenkundige soziale Beziehung ist die eheliche Beziehung zu einem Partner.  Diese Beziehung ist durch den <tt>Zivilstand</tt> modelliert.


## Die Modell-Elemente im Detail

Bisher haben wir das Kern-Skelett des Modells skizziert.  Das komplette fachliche Modell enthält aber noch weitere Feinheiten:

<img style="uml_diagram" src="classdiagram_10_01.svg" alt="KRDB-Klassen-Modell in UML-Form" width="100%" />

Insbesondere zeigt es, dass die meisten Modell-Sachverhalte nur während bestimmter Zeit-Intervalle gültig waren.  Das UML-Modell oben stellt dies als Zeit-Intervall-Klasse {ganz oben} dar.  Im KRDB-Modell sind Zeitintervalle aber nicht errerbt, sondern "inline" in den jeweiligen Entitäten selbst enthalten.

Ausserdem sind nun die möglichen Werte Spezialisierungen für "<tt>Funktion</tt>" und "<tt>Zivilstand</tt>" ausmodelliert. 


### Die Haupt-Beziehungen im Modell

Von der Person zu unterscheiden sind die Rollen, in denen eine Person in einer Situation auftreten kann.  Diese Rollen sind beschrieben als die Beziehungen, die auf eine Person[en-Entität] verweisen.


#### Einsitz

Der genauere Charakter der Mitgliedschaft einer Person in einem Rat ist von mehreren "Parametern" geprägt.  Es ist üblich, solche reichhaltigen Beziehungen sozusagen zu verdinglichen.  Man unterbricht dabei die Beziehung durch einen Knoten, der die ganze Nutzlast der Characteristica der Beziehung trägt.  Einen solchen Zwischen-Knoten nennt man Assoziations-Klasse.

Die Assoziationsklasse, die die Characteristica der Mitgliedschaft einer Person in einem Rat trägt, heisst in unserem Modell "<tt>Einsitz</tt>".

##### Funktion

Normalerweise ist eine Person während eines Einsitzes in einem Rat einfaches Mitglied.

Sie kann aber während bestimmter Perioden eine spezieller Art der Mitgliedschaft bekleiden, z.B. als Rats-Präsident.  Im UML-Diagramm oben sind diese Unterarten als Subklassen von "Funktion" dargestellt.  Im RDF-Modell sind die Unterarten als Sub-Properties der allgemeinen Mitglieds-Property modelliert.  

##### ParteiAssociation

Eine Person kann während eines Einsitzes zu bestimmten Perioden als Vertreter einer Partei und als Vertreter einer Fraktion Mitglied des Rates sein.

"<tt>ParteiAssociation</tt>" ist eine Assoziationsklasse, die die Beziehung von <tt>Einsitz</tt> zu <tt>Partei</tt> und <tt>Fraktion</tt> genauer beschreibt.


##### Partei

Partei ist das, was auch der Bürger "draussen auf der Strasse" unter Partei versteht.

Im gegebenen Modell hat diese Entität keine weitere Struktur.


##### Fraktion

Fraktion ist das, was auch der Bürger "draussen auf der Strasse" unter Fraktion versteht.

Im gegebenen Modell hat diese Entität keine weitere Struktur.


##### Wahlkreis

Diese Angabe ist der <tt>Wahlkreis</tt>, als Vertreter dessen ein Mitglied in einem Rat einsitzt.

###### Übung

Die folgende SPARQL-Abfrage listet alle Personen auf, die irgendwann einmal als Mitglied einer Partei mit der Bezeichnung "CVP" Präsidiums-Mitglied waren: 
<blockquote>
[<tt>SELECT * \
WHERE \
{ \
&nbsp;&nbsp;GRAPH ?g_einsitz \
&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;?einsitz rdf:type kr_10_genus:Einsitz . \
&nbsp;&nbsp;&nbsp;&nbsp;?einsitz kr_10_rel:person ?person . \
&nbsp;&nbsp;&nbsp;&nbsp;?einsitz kr_10_rel:einsitz_parteiassoc ?pa . \
&nbsp;&nbsp;} \
&nbsp;&nbsp;GRAPH ?g_pa \
&nbsp;&nbsp;&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;?pa kr_10_rel:assoc_partei ?partei . \
&nbsp;&nbsp;&nbsp;&nbsp;} \
&nbsp;&nbsp;GRAPH ?g_partei \
&nbsp;&nbsp;&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;?partei kr_10_rel:partei_bezeichnung "CVP" . \
&nbsp;&nbsp;&nbsp;&nbsp;} \
&nbsp;&nbsp;GRAPH ?g_person \
&nbsp;&nbsp;&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;?person rdf:type kr_10_genus:Person . \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;?person rdfs:label ?label . \
&nbsp;&nbsp;&nbsp;&nbsp;} \
&nbsp;&nbsp;GRAPH ?g_einsitz \
&nbsp;&nbsp;&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;?einsitz kr_10_rel:funktion ?fkt . \
&nbsp;&nbsp;&nbsp;&nbsp;} \
&nbsp;&nbsp;GRAPH \<https://linked.opendata.swiss/graph/zhkrdb/ld/10/res/funktion\> \
&nbsp;&nbsp;&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;?fkt rdf:type kr_10_genus:Funktion__Präsidium . \
&nbsp;&nbsp;&nbsp;&nbsp;} \
}</tt>](https://test.lindas-data.ch:8443/zhkrdb#!/query/SELECT%20*%20%0AWHERE%20%0A{%20%0A%20%20GRAPH%20%3Fg_einsitz%0A%20%20{%20%0A%20%20%20%20%3Feinsitz%20rdf%3Atype%20kr_10_genus%3AEinsitz%20.%0A%20%20%20%20%3Feinsitz%20kr_10_rel%3Aperson%20%3Fperson%20.%0A%20%20%20%20%3Feinsitz%20kr_10_rel%3Aeinsitz_parteiassoc%20%3Fpa%20.%0A%20%20}%20%0AGRAPH%20%3Fg_pa%0A%20%20{%20%0A%09%3Fpa%20kr_10_rel%3Aassoc_partei%20%3Fpartei%20.%0A%20%20}%20%0AGRAPH%20%3Fg_partei%0A%20%20{%20%0A%09%3Fpartei%20kr_10_rel%3Apartei_bezeichnung%20%22CVP%22%20.%0A%20%20}%20%0AGRAPH%20%3Fg_person%0A%20%20{%20%0A%09%3Fperson%20rdf%3Atype%20kr_10_genus%3APerson%20.%0A%20%20%20%20%3Fperson%20rdfs%3Alabel%20%3Flabel%20.%0A%20%20}%20%0AGRAPH%20%3Fg_einsitz%20%0A%20%20{%20%0A%20%20%20%20%3Feinsitz%20kr_10_rel%3Afunktion%20%3Ffkt%20.%0A%20%20}%20%0AGRAPH%20%3Chttps%3A%2F%2Flinked.opendata.swiss%2Fgraph%2Fzhkrdb%2Fld%2F10%2Fres%2Ffunktion%3E%20%0A%20%20{%20%0A%20%20%20%20%3Ffkt%20rdf%3Atype%20kr_10_genus%3AFunktion__Pr%C3%A4sidium%20.%20%0A%20%20}%20%0A}%0A%0A/reasoning/true)\
</blockquote>


#### Bindung

Eine Person, die Ratsmitglied ist, steht üblicherweise nicht isoliert in der Gesellschaft, sondern hat weitere Beziehungen zu anderen Personen und Institutionen.  Die Kantonsratsdatenbank modelliert die Beziehung zwischen Institutionen und Personen um transparent zu machen, welche anderen, ausser Partei- und Fraktions-Interessen eine Rats-Mitglieds-Person möglicherweise verfolgt.

"<tt>Bindung</tt>" ist eine Assoziationsklasse, auf der Beziehung zwischen Institutionen und Personen.  Die Art der Bindung ist durch die Angabe der Bindungskategorie parametriert, die die spezielle Art der Bindung zwischen einer Person und einer Institution ausdrückt.  Der einzige Wert, der momentan im Datenbestand vorkommt, ist "politisches Amt".


#### Zivilstand

Eine besonders gut fassbare enge Beziehung zu anderen Personen ist Ehe.

Deshalb modelliert die KRDB die Lebensphasen, während der und mit wem die Räte verheiratet waren.

Mit dem "neuen" KRDB-LD-Modell könnten auch [Interessens-]Bindungen von Ehe-Partnern erfasst werden.  Der bisherige Datenbestand bringt solche Angaben nicht mit.


### Die Attribute von Person

#### Name und Vorname

Name und Vorname sind zwei spezielle Labels mit denen man über eine Person spricht.

UML-technisch hängt ein String-Literal über eine Beziehung {"Property"} "<tt>name</tt>" bzw. "<tt>vorname</tt>" an Personen.  Die Beziehungen "<tt>name</tt>" und "<tt>vorname</tt>" werden im "<tt>affix</tt>"-Named-Graphen als <tt>subPropertyOf</tt> "<tt>rdfs:label</tt>" deklariert.


#### Wohnort

Von einer bürgerlichen Person soll man wissen, wann sie wo offiziell gewohnt hat.

Der Datentyp von <tt>Wohnort</tt> ist eigentlich eine Adresse {kein Postfach}, das in der Rolle eines Wohnorts zu einer Person steht.  

Strasse und Hausnummer fehlen im aktuellen KRDB-Modell.

<!-- eigentlich sollte die "Wohnort"-Klasse in "Adresse" umbenannt werden.  Die Wohnort-Rolle der Beziehung bleibt.  Die Gültigkeits-Zeitspanne kommt dann an die Beziehung -->


#### Bürgerort

Der Bürgerort ist eine Spezialität des schweizer Bürger-Personen-Modells.  Der Bürgerort ist eine Gemeinde, die langfristig grundlegende Bürgerdaten verwaltet und bis 1926 auch armenrechtlich eine grosse Rolle spielte.  Auch der Bürgerort einer Person kann sich aber von Zeit zu Zeit ändern.

<!-- eigentlich sollte die "Bürgerort"-Klasse in "Gemeinde" umbenannt werden.  Die Bürgerort-Rolle der Beziehung bleibt.  Die Gültigkeits-Zeitspanne kommt dann an die Beziehung -->


#### Beruf, Akademischer Titel, Dienstgrad, Konfession

Auch Beruf, Akademischer Titel, Dienstgrad und Konfession werden als prägend für das Wesen einer [Rats-]Person erachtet.

Die Instanzen dieser Angaben sind durch Objektifizierung von String-Attributen des ursprünglichen Datenbestandes entstanden.  Dort wurden sie größtenteils treu von den Originalquellen ("offiziellen" Publikationen Regierungsetats, Staatskalender und Kantonsratslisten) übernommen und bisher nicht mit heutigen "offiziellen" Listen abgeglichen.  Auch Schreibweisen-Variationen von Dingen, die eigentlich das Gleiche bedeuten, sind bisher noch nicht zusammengezogen.

Auch diese durch Objektifizierung entstandenen Klassen enthalten den Klassen-Namen im Prefix des Identifiers: ...<tt>/10/res/${klassen\_name}/</tt>, z.B. ...<tt>/10/res/beruf/Önologin</tt>

##### Übung

Führen Sie noch einmal die im Abschnitt 'Identifier-"Syntax"' angegebene SPARQL-Abfrage durch.  Ersetzen Sie den dort konkret angegebenen Personen-Identifier durch die Variable "?person".  Evtl. können Sie an die Abfrage ein "<tt>ORDER BY ?person</tt>" anhängen, um die Ergebnisse nach Personen-ID zu sortieren.


## Nicht-fachliche Aspekte

<img style="uml_diagram" src="classdiagram_10_00.svg" alt="KRDB-Klassen-Modell in UML-Form" width="100%" />


### Literatur- und Quellen-Nachweise

Für die beiden Entitäten "<tt>Einsitz</tt>" und "<tt>Person</tt>" werden Herkunfts-Angaben {"<tt>*quelle</tt>"} gepflegt.  Sie sind in den links unten hinzugekommenen Modell-Sektionen zu sehen.

In Anlehnung an die Strukturen von "[Prov-O](http://dcevents.dublincore.org/IntConf/dc-2013/paper/viewFile/197/120)" betrachten wir Modell-Elemente als das Ergebnis einer Erzeugungs-Aktivität.  Dh. man geht davon aus, dass ein [meistens menschlicher] Aktor im Rahmen eines Erzeugungs-Prozesses ein Modell-Element unter Hinzuziehung von Quell-Material hervorgebracht hat.  Eine solche Aktivität {"<tt>Einsitz\_generation\_activity</tt>", "<tt>Person\_generation\_activity</tt>"} bezieht sich auf das, was erzeugt wurde {"<tt>Einsitz</tt>", "<tt>Person</tt>"}.  Sie nennt ausserdem die hinzugezogene Quelle.  Typischerweise sagen Erzeugungs-Aktivitäten auch, wann sie begonnen und beendet wurden.  Entsprechende Angaben fehlen in unserem Modell.

Im UML-Modell finden sich die entsprechenden Klassen links unten.

Im Identifier-Prefix enthalten die Aktivitäten den Teil-String "<tt>prov</tt>".  Sie werden in einem Named-Graphen mit dem Teil-String "<tt>meta</tt>" im Identifier abgelegt.

#### Übung

Die folgende Beispiel-SPARQL-Abfrage liefert zu allen Personen die vorhandenen Quell-Angaben:

<blockquote>
[<tt>\# von einem Person-Modell-Element kommt man zu seiner Quelle ueber die ?person_erzeugung-Aktivitaet:
\# Sinn dieses "Umwegs" ist die Angleichung an die Modellierung von Prov-O, wo Dinge (Dokumente, Modell-Elemente) aus Aktivitaeten (Erzeugung, Aenderung) entstehen.  Diese Aktivitaeten benutzen ursprueglicheres Material, in unserem Fall die ?person_quelle, die mit "person_quellen_angabe" verbal beschreibt, wo das Person-Modell-Element herkommt. \
SELECT	?person ?person_erzeugung ?person_quelle ?p ?o \
WHERE \
{ \	
&nbsp;&nbsp;graph \<https://linked.opendata.swiss/graph/zhkrdb/ld/10/meta/person\> \
&nbsp;&nbsp;{ \
&nbsp;&nbsp;&nbsp;&nbsp;?person_erzeugung kr_10_rel:generated ?person . \
&nbsp;&nbsp;&nbsp;&nbsp;?person_erzeugung kr_10_rel:person_quelle ?person_quelle . \
&nbsp;&nbsp;&nbsp;&nbsp;?person_quelle ?p ?o . \
&nbsp;&nbsp;} \
} \
ORDER BY ?person</tt>](https://test.lindas-data.ch:8443/zhkrdb#!/query/%23%20von%20einem%20Person-Modell-Element%20kommt%20man%20zu%20seiner%20Quelle%20ueber%20die%20%3Fperson_erzeugung-Aktivitaet%3A%0A%23%20Sinn%20dieses%20%22Umwegs%22%20ist%20die%20Angleichung%20an%20die%20Modellierung%20von%20Prov-O%2C%20wo%20Dinge%20%28Dokumente%2C%20Modell-Elemente%29%20aus%20Aktivitaeten%20%28Erzeugung%2C%20Aenderung%29%20entstehen.%20%20Diese%20Aktivitaeten%20benutzen%20ursprueglicheres%20Material%2C%20in%20unserem%20Fall%20die%20%3Fperson_quelle%2C%20die%20mit%20%22person_quellen_angabe%22%20verbal%20beschreibt%2C%20wo%20das%20Person-Modell-Element%20herkommt.%0Aselect%09%3Fperson%20%3Fperson_erzeugung%20%3Fperson_quelle%20%3Fp%20%3Fo%0Awhere%0A{%09%0A%09graph%09%3Chttps%3A%2F%2Flinked.opendata.swiss%2Fgraph%2Fzhkrdb%2Fld%2F10%2Fmeta%2Fperson%3E%09%0A%09{%09%0A%0A%20%20%20%09%09%3Fperson_erzeugung%20kr_10_rel%3Agenerated%20%3Fperson%20.%0A%09%09%3Fperson_erzeugung%20kr_10_rel%3Aperson_quelle%20%3Fperson_quelle%20.%0A%20%20%20%20%20%20%20%20%3Fperson_quelle%20%3Fp%20%3Fo%20.%0A%20%20%20%20}%09%0A}%0AORDER%20BY%20%3Fperson%0A)
</blockquote>


### Ontologie-Mapping

#### Die Real-Personen-"Nabe"

Unsere Artgenossen sind für uns so ungeheuer wichtig, dass sich gerade bei ihrer Modellierung eine endlose Vielfalt von Modellen entwickelt hat.  Selbst für recht abstrakte Bürger-Modelle ist die Vielfalt noch gross.  Die Modellierer der Kantonsratsdatenbank waren sich dessen auch bewusst.  Sie haben, soweit ihnen bekannt, Bezüge {Identifier} zu einigen anderen Personen-Modellen in ihr Modell eingebaut.  Das Bündel dieser Bezüge ist im Modell durch die "Klasse" "<tt>copula:Person</tt>" verkörpert, die in obigem Diagramm mit den Fremd-Personen-Klassen unterhalb der Klasse "<tt>Person</tt>" zu finden ist.

Wie in obigem Abschnitt "Bezüge zu anderen Modellen" schon erwähnt, tragen solche Referenzen keine fachliche "Nutzlast", dh. sie machen keine Seins-Aussagen über den Charakter von Personen.  Deshalb wurde für jede hier bekannte Real-Person ein Modell-Knoten erzeugt, der keine ontologischen Aussagen macht.  Von diesem Knoten gehen nur Bezüge zu den uns bekannten Entitäten aus, die die gleiche Real-Person in unserem Modell und anderswo modellieren.  Eigentlich wäre hierfür ein sogenannter [Blank-Node](https://en.wikipedia.org/wiki/Blank_node) {ein Modell-Knoten ohne Identifier} das angemessene Ausdrucksmittel.  Wir haben ihm dennoch einen Identifier "spendiert", da er für das ressourcen-übergreifende Ansprechen nötig ist.  Dieser Identifier trägt als "<tt>meta</tt>"-Sektion den String "<tt>copula</tt>".

Man kann über diese Real-Personen-Nabe *zu anderen Modellen*, die Aussagen über die gleiche Real-Person machen, *navigieren*.  Diese anderen Modelle der gleichen Real-Person werden, da sie ganz unabhängig von unserem Modell entstanden sind, andere Aspekte dieser Person modellieren.  Man sollte also nicht erwarten, dass diese anderen Modelle die gleichen Aussagen über die gleiche Person machen!  Allerdings würde es Klärungsbedarf provozieren, wenn die Aussagen nicht ergänzend, sondern widersprüchlich wären.

In obigem UML-Klassen-Diagramm liegt die Klasse, die die Real-Person repräsentiert, etwas links unterhalb der Personen-Klasse.


## Integration mit anderen semantischen Ressourcen

### Direkte Benutzung

Alle Klassen, die in obigem Klassen-Diagramm nur eingehende "Pfeile" haben, also nur von anderen Entitäten "benutzt" werden, sind eher einfache Dinge.  

Einfache Dinge sind aber hochgradig wiederbenutzbar.  Wir werden also nicht lange suchen müssen, um andernorts bereits, vermutlich sogar besser gepflegte, Modelle für diese Dinge zu finden.

Parteien, Fraktionen, Wahlkreise, Zivilstände, [Wohn|Bürger]orte, Berufe, akademische Titel, Dienstgrade und Konfessionen sind solche Dinge, bei denen wir danach trachten sollten, sie zukünftig von einer authentischeren Quelle zu beziehen.  


### Föderierte SPARQL-Abfragen

Als Zwischenlösung kann man symptomatisch mit federated SPARQL-Queries entsprechendes Fremd-Material ansprechen.


### Theorie-Alignment

Im Zuge des KRDB-LODifizierungs-Projekts haben wir nach Fremd-Ontologien gesucht und einige Parlaments-, Personen- und Grundlagen-Ontologien davon im Hinblick auf die direkte Verwendung in unserem Modell evaluiert.

Leider fanden sich dabei keine derart passenden Referenz-Ontologien, dass wir unsere Räte und Personen zu *direkten* Instanzen dortiger Typen hätten machen wollen.

Wir haben uns für den oben schon angedeuteten etablierten Mechanismus einer etwas loseren Kopplung entschieden und vorgesehen, dass man sie in eine dieser Ontologien nach Wahl einhängen kann.


<!-- Hier muesste man noch den Einhäng-Mechanismus in Fremd-Ontologien diskutieren... -->

### "Einhängen" in andere Ontologien

#### Namen und Vornamen als Spezialisierungen von rdfs:label

Wir haben oben bereits im gleichnamigen Theorie-Abschnitt angedeutet, wie Namen und Vornamen als Spezialisierungen von "<tt>rdfs:label</tt>" benutzt werden können.  Konkret implementiert sind diese beiden Subsumptionen durch zwei Tripel im Named-Graphen "<tt>\<https://linked.opendata.swiss/graph/zhkrdb/ld/10/affix/omnes\></tt>": 

<tt>
kr_10_rel:nachname	rdfs:subPropertyOf	rdfs:label	. \
kr_10_rel:vorname	rdfs:subPropertyOf	rdfs:label	. \
</tt>

Damit haben wir unsere eigenen Beziehungen "<tt>vorname</tt>" und "<tt>nachname</tt>" in eine entsprechende Überbeziehung "<tt>label</tt>" der RDFS-Ontologie eingehängt.

Mit dieser SPARQL-Abfrage können Sie das überprüfen:
<blockquote>
[<tt>
SELECT * \
WHERE \
{ \
	# alle Label im Graphen "person": \
	GRAPH \<https://linked.opendata.swiss/graph/zhkrdb/ld/10/res/person\> \
	{ \
		?s rdfs:label ?label . \
	} \
} \
ORDER BY ?s \
# \
# Sie werden sehr wahrscheinlich eine leere Ergebnis-Liste erhalten \
# Schalten Sie nun Reasoning ein und wiederholen Sie die Abfrage! \
# welche Labels erhalten Sie jetzt? \
# Warum erhalten Sie pro Subjekt mehrere Labels? \
# Welches sind die Properties, die die erhaltenen Subjekte mit den erhaltenen Objekten verbinden? \
# In welchem Graphen befindet sich die verantwortliche "subPropertyOf"? \
# Wie lange hat der Triplestore jeweils mit seiner Antwort gebraucht? \
</tt>
](https://test.lindas-data.ch:8443/zhkrdb#!/query/SELECT%20*%0AWHERE%20%0A{%0A%09%23%20alle%20Label%20im%20Graphen%20%22person%22%3A%0A%09GRAPH%20%3Chttps%3A%2F%2Flinked.opendata.swiss%2Fgraph%2Fzhkrdb%2Fld%2F10%2Fres%2Fperson%3E%20%0A%09{%20%0A%09%09%3Fs%20rdfs%3Alabel%20%3Flabel%20.%0A%09}%20%0A}%0AORDER%20BY%20%3Fs%0A%23%0A%23%20Sie%20werden%20sehr%20wahrscheinlich%20eine%20leere%20Ergebnis-Liste%20erhalten%0A%23%20Schalten%20Sie%20nun%20Reasoning%20ein%20und%20wiederholen%20Sie%20die%20Abfrage!%0A%23%20welche%20Labels%20erhalten%20Sie%20jetzt%3F%0A%23%20Warum%20erhalten%20Sie%20pro%20Subjekt%20mehrere%20Labels%3F%0A%23%20Welches%20sind%20die%20Properties%2C%20die%20die%20erhaltenen%20Subjekte%20mit%20den%20erhaltenen%20Objekten%20verbinden%3F%0A%23%20In%20welchem%20Graphen%20befindet%20sich%20die%20verantwortliche%20%22subPropertyOf%22%3F%0A%23%20Wie%20lange%20hat%20der%20Triplestore%20jeweils%20mit%20seiner%20Antwort%20gebraucht%3F%0A)
</blockquote>


#### Unsere Personen als Spezialisierung anderer Personen

##### http://www.w3.org/ns/person\#Person

Phil Archer und Andrea Perego haben für die EU-Kommission eine [Personen-Klasse](https://joinup.ec.europa.eu/site/core_person/rdfs.html) modelliert, die ähnlich der KRDB-Person die Aspekte eines Menschen in seiner Rolle in einer bürgerlichen Gesellschaft in den Vordergrund stellt.  Wir haben im Named-Graphen "<tt>\<https://linked.opendata.swiss/graph/zhkrdb/ld/10/affix/omnes\></tt>" mit dem Tripel 
<blockquote>
<tt>
    kr_10_genus:Person			rdfs:subClassOf		\<http://www.w3.org/ns/person#Person\> .
</tt>
</blockquote>
testweise die KRDB-Person zur Subklasse dieser Person gemacht.

Wenn Sie mit einschaltetem Reasoning nach den Instanzen der Klasse \<http://www.w3.org/ns/person#Person\> suchen, werden Sie damit auch die KRDB-Personen-Instanzen als Ergebnisse geliefert bekommen.


##### http://www.cidoc-crm.org/cidoc-crm/E21_Person

Die in CIDOC-CRM modellierte Person stellt eher biologische Aspekte in den Kern der Betrachtungen.  

Auch diese Person haben wir testweise im Named-Graphen "<tt>\<https://linked.opendata.swiss/graph/zhkrdb/ld/10/affix/omnes\></tt>" mit dem Tripel
<blockquote>
<tt>
    kr_10_genus:Person			rdfs:subClassOf		\<http://www.cidoc-crm.org/cidoc-crm/E21_Person\> .
</tt>
</blockquote>
zu einer Superklasse unserer KRDB-Person gemacht.

Auch hier werden mit eingeschaltetem Reasoning KRDB-Personen zu Instanzen der CIDOC-Personen-klasse.

In beiden Fällen tun wir mit dem Einhängen einen Dienst an den jeweiligen Communities, da sie nun auch unsere Personen in "ihren" Beständen finden [wenn ihre Queries auch den KRDB-Tripel-Bestand erfassen].

Suchen Sie jetzt mal [mit eingeschaltetetem Reasoning natürlich] nach Instanzen von foaf:Person ...  .  Warum bekommen Sie Resultate?


## Die LOD-KRDB auf Lindas

<!-- kann man das so auf github publizieren?  Da ist es ja weltweit lesbar.  Soll der Zugriff nur über die Credentials geregelt werden? -->

Momentan kann man mit dem KRDB-LD-Modell auf der [Test-Instanz von LINDAS](https://test.lindas-data.ch:8443/) zu Test-Zwecken arbeiten.  Der Inhalt kann sich aber noch ändern.

Auf dem openZH/krdb-lod-github-Bereich findet man [einige Beispiel-Abfragen](https://github.com/openZH/krdb-lod/tree/master/examples) zum bequemeren Einstieg.


