## Voraussetzungen

Um diesem Buch geistig zu folgen, ist es ideal, wenn du mit den Grundlagen der Webentwicklung vertraut bist, damit meine ich HTML, CSS und JavaScript. Es ist hilfreich, [APIs](https://www.robinwieruch.de/what-is-an-api-javascript/) zu verstehen, da sie ausführlich behandelt werden.

### Editor and Terminal

Ich habe [eine Anleitung](https://www.robinwieruch.de/developer-setup/) auf meiner Website veröffentlicht, um dir den Einstieg in die allgemeine Webentwicklung zu erleichtern. Für die Bearbeitung der Inhalte dieses Buches benötigst du einen Texteditor (Sublime Text) und ein Befehlszeilenprogramm (iTerm) oder eine IDE (Visual Studio Code,  VS Code). Ich empfehle dir Letzteres, da dieses Programm eine praktische Kombi-Lösung darstellt, die einen erweiterten Editor mit einem integrierten Befehlszeilentool vereint. VS Code ist bei Webentwicklern beliebt.

Hier im Buch werde ich den Begriff *Befehlszeile* verwenden, der synonym für *Befehlszeilentool*, *Terminal*, *Kommandozeile* und *integriertes Terminal* verwendet wird. Gleiches gilt für die Begriffe *Editor*, *Texteditor* und *IDE*, je nachdem, welche Programme du verwendest.

Optional empfehle ich dir, deine Projekte mit GitHub zu verwalten. Ich habe eine [Kurzanleitung](https://www.robinwieruch.de/git-essential-commands/) erstellt, die dir den Einstieg in dieses Werkzeug erleichtert. Github bietet dir eine Versionskontrolle. So hast du immer den Überblick über Änderungen und es ist unkompliziert auf einen Stand zurück zu wechseln. Github ist nebenbei die ideale Möglichkeit, deinen Code später mit anderen Personen zu teilen.

Eine Alternative zur lokalen Installation stellt der Online-Editor [Codesandbox](https://codesandbox.io/) dar. Während Codesandbox ein ausgezeichnetes Tool zum Online-Teilen von Code ist, ist ein lokales Computer-Setup ein besseres Tool zum Erlernen der verschiedenen Möglichkeiten. Wenn du planst, Anwendungen professionell zu entwickeln, ist ein lokales Setup unumgänglich.

### Node.js and NPM

Um mit React zu arbeiten, benötigst du [Node.js](https://nodejs.org/de/) und [npm](https://www.npmjs.com/). Beide Programme werden zum Verwalten von Bibliotheken verwendet, die wir in der Beispielanwendung benötigen. Bei diesen handelt es sich um Node-Pakete in Form von kleinen Codeeinheiten oder oder ganzen Frameworks. Wir installieren sie mithilfe von `npm` (Node Package Manager).

Überprüfe ob und in welcher Version `Node.js` und `npm` auf deinem Rechner installiert sind. Verwende dazu die Befehlszeile und die Befehle `node --Version` und `npm --Version`.

{title="Command Line",lang="text"}
~~~~~~~
node --version
*vXX.YY.ZZ
npm --version
*vXX.YY.ZZ
~~~~~~~

Wird dir im Terminal angezeigt, welche Version installiert ist? Andernfalls installiere die Programme. 

Stelle sicher, dass du immer über aktuelle Versionen verfügst. Kennst du die Programme bisher nicht oder wünschst du dir einen kurzen Überblick? Sieh dir meinen [npm-Crashkurs](https://www.robinwieruch.de/npm-crash-course) an.
