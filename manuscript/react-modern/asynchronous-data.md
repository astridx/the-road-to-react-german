## Asynchrone Daten in React

Es gibt bisher zwei Interaktionen in unserer Anwendung: das Durchsuchen der Liste und das Entfernen von Elementen. Im ersten Fall ist der Zustand der List-Komponente schwankend und wird über die externe Funktion (`searchTerm`) beeinflusst. Der zweite Fall beschreibt das nicht änderbare Löschen eines Listen-Elements.

In der Realität ist es manchmal erforderlich, eine Komponente zu rendern, bevor Daten von einer Drittanbieter-API zur Verfügung stehen. Im Folgenden simulieren wir diese Art von asynchronen Daten in unserer Anwendung. In einem Echtsystem werden die von einer realen Remote-API abgerufen. Zunächst erstellen wir eine Funktion, die ein Promise-Objekt zurückgibt. Dieses steht für das Ergebnis einer asynchronen Operation. Es enthält bei erfolgreicher Ausführung den vorherigen Inhalt von `stories`:

{title="src/App.js",lang="javascript"}
~~~~~~~
const initialStories = [ ... ];

# leanpub-start-insert
const getAsyncStories = () =>
  Promise.resolve({ data: { stories: initialStories } });
# leanpub-end-insert
~~~~~~~

Wir verwenden in der App-Komponente anstelle von `initialStories` ein leeres Array. So initialisieren wir unsere Anwendung mit einer inhaltslosen Liste, um im nächsten Schritt den asynchronen Abrufen zu simulieren. Rufe in einem neuen `useEffect`-Hook die Funktion `getAsyncStories` auf und fordere so das Promise-Objekt auf die Daten asynchron abzufragen. Da das zweite Argument in `useEffect` (Array mit abhängigen Variablen) leer ist, wird der Seiten-Effekt `useEffect` erstmals ausgelöst, wenn die App-Komponente gerendert wird:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const [stories, setStories] = React.useState([]);
# leanpub-end-insert

# leanpub-start-insert
  React.useEffect(() => {
    getAsyncStories().then(result => {
      setStories(result.data.stories);
    });
  }, []);
# leanpub-end-insert

  ...
};
~~~~~~~

In der Theorie treffen die Daten beim ersten Aufruf der Anwendung asynchron ein. In der Praxis ist dies nicht sichtbar. Die Liste wird sofort gerendert. Lass uns dies ändern, indem wir eine Verzögerung einbauen. So simulieren wir die Übertragung der Daten über ein Netzwerk. Erweitere zu diesem Zweck erst das Promise-Objekt --- fügen Sie eine Zeitüberschreitung hinzu:

{title="src/App.js",lang="javascript"}
~~~~~~~
const getAsyncStories = () =>
# leanpub-start-insert
  new Promise(resolve =>
    resolve({ data: { stories: initialStories } })
  );
# leanpub-end-insert
~~~~~~~

Und im zweiten Schritt verzögerst du das Promise-Objekt um einige Sekunden::

{title="src/App.js",lang="javascript"}
~~~~~~~
const getAsyncStories = () =>
  new Promise(resolve =>
# leanpub-start-insert
    setTimeout(
      () => resolve({ data: { stories: initialStories } }),
      2000
    )
# leanpub-end-insert
  );
~~~~~~~

Wenn du die Anwendung mit den Ergänzungen dieses Kapitels aufrufst, wird die Liste verzögert gerendert. Unmittelbar beim Start wird nichts angezeigt, denn der Anfangszustand der List-Komponente ist ein leeres Array. Beim Rendern von App wird der Seiten-Effekt-Hook `useEffect` aufgerufen, um die Listendaten über das Promise-Objekt asynchron abzurufen. Nach Ankunft der Daten und dem Setzen des Status wird die Komponente erneut gerendert und die Liste ist sichtbar.

### Übungen:

* Begutachte den [Quellcode dieses Abschnitts](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Asynchronous-Data).
  * Reflektiere die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Inline-Handler-in-JSX...hs/React-Asynchronous-Data?expand=1).
* Lese mehr zum Thema [Promises in JavaScript](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Promise).
