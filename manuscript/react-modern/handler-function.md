## Handler in JSX

Unsere App-Komponente verfügt nach wie vor über das Eingabefeld inklusive Label, das wir bisher nur angezeigt und nicht weiter verwendet haben. In HTML verfügen Eingabefelder über einen [onchange-Handler](https://developer.mozilla.org/de/docs/Web/API/GlobalEventHandlers/onchange). Ein solcher Handler wäre in JSX ebenfalls hilfreich. Deshalb erarbeiten wir als Nächstes, wie ein onchange-Handler mit JSX in einer React-Komponente anwendbar ist. Überarbeite zunächst die App. Nutze anstelle des [knappen Körpers einen gewöhnlichen Blockkörper](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Functions/Pfeilfunktionen#Funktionsk%C3%B6rper), um dann im nächsten Schritt weitere Implementierungsdetails hinzufügen.

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const App = () => {
  // do something in between

  return (
# leanpub-end-insert
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />

      <hr />

      <List />
    </div>
# leanpub-start-insert
  );
};
# leanpub-end-insert
~~~~~~~

Definiere jetzt eine Funktion, die aufgerufen wird, wenn sich der Wert im Eingabefeld ändert – ergo: wenn das Änderungsereignis ausgelöst wird. Verwende eine normale oder eine Arrow-Funktion – je nachdem welche Variante du bevorzugst. In React wird eine solche Funktion als **(Ereignis-)Handler** bezeichnet. Übergib diese im nächsten Schritt an das Attribut `onChange` des Eingabefelds. Beachte: Bei `onChange` handelt es sich um ein JSX-Attribut. Dies erkennst du insbesondere an der Schreibweise in Camel-Case.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
# leanpub-start-insert
  const handleChange = event => {
    console.log(event);
  };
# leanpub-end-insert

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
# leanpub-start-insert
      <input id="search" type="text" onChange={handleChange} />
# leanpub-end-insert

      <hr />

      <List />
    </div>
  );
};
~~~~~~~

Öffne jetzt zunächst deine Anwendung in einem Webbrowser und danach wechsele in die Entwicklertools deines Browsers. Gib daraufhin ein paar Zeichen in das Eingabefeld ein und prüfe, ob deine Eingaben mitprotokolliert werden. Dies wird als **synthetisches Ereignis** bezeichnet, welches vom JavaScript-Objekt definiert wird. Über dieses Objekt greifen wir auf den Wert des Eingabefelds zu:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const handleChange = event => {
# leanpub-start-insert
    console.log(event.target.value);
# leanpub-end-insert
  };

  return ( ... );
};
~~~~~~~

Das [synthetische Ereignis](https://de.reactjs.org/docs/events.html) ist ein browserübergreifender Wrapper für das [native Eventobjekt des Browsers](https://developer.mozilla.org/de/docs/Web/Events). Es beinhaltet weitere Funktionen, die nützlich sind, um das Standardverhalten des nativen Browsers zu verhindern – zum Beispiel das Aktualisieren einer Seite, nachdem der Benutzer auf die Schaltfläche zum Senden eines Formulars geklickt hat. Du wirst dieses Ereignis nicht immer nutzen. Andere Male ist es sinnvoll.

Auf diese Weise erreichen wir es, dass HTML-Elemente über JSX-Handlerfunktionen auf Benutzerinteraktionen reagieren. Übergib immer die Funktion selbst an einen Handler und nicht den Rückgabewert – es sei denn, bei diesem handelt es sich um eine Funktion:

{title="Code Playground",lang="javascript"}
~~~~~~~
// Bitte nicht so.
<input
  id="search"
  type="text"
# leanpub-start-insert
  onChange={handleChange()}
# leanpub-end-insert
/>

// Stattdessen
<input
  id="search"
  type="text"
# leanpub-start-insert
  onChange={handleChange}
# leanpub-end-insert
/>
~~~~~~~

HTML und JavaScript arbeiten in JSX zielführend zusammen. Javascript zeigt Objekte mithilfe von HTML im Browser an, übergibt Grundelemente (zum Beispiel `<a>`) an HTML-Attribute (zum Beispiel `href`) und Funktionen an die Attribute eines Elements, um Ereignisse zu behandeln.

Ich bevorzuge Pfeilfunktionen aufgrund ihrer Prägnanz als Ereignishandler. In einer größeren React-Komponente verwende ich teilweise normale Funktionsanweisungen, da diese im Gegensatz zu anderen Variablendeklarationen im Hauptteil einer Komponente sichtbarer sind.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Handler-Function-in-JSX).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Component-Definition...hs/Handler-Function-in-JSX?expand=1).
* Lese mehr zum Thema [React's events](https://reactjs.org/docs/events.html).
