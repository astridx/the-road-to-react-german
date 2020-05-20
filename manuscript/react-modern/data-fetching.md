## Datenabruf mit React

Wir rufen derzeit Daten ab, dabei handelt es sich um Pseudodaten, die aus einem selbsterstellten Promise-Objekt stammen. Die bisherigen Lektionen zu asynchronem Datenmanagement und erweiterter Statusverwaltung stellend eine Grundlage für diesen Abschnitt dar. Als Nächstes rufen wir Daten von einer echten Drittanbieter-API ab. Dazu verwenden wir die zuverlässige und informative [Hacker News API](https://hn.algolia.com/api).

Anstatt das Array `initialStories` und die Funktion `getAsyncStories` zu verwenden, rufen wir die Daten direkt von der API ab.

{title="src/App.js",lang="javascript"}
~~~~~~~
// A
# leanpub-start-insert
const API_ENDPOINT = 'https://hn.algolia.com/api/v1/search?query=';
# leanpub-end-insert

const App = () => {
  ...

  React.useEffect(() => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    fetch(`${API_ENDPOINT}react`) // B
      .then(response => response.json()) // C
# leanpub-end-insert
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
# leanpub-start-insert
          payload: result.hits, // D
# leanpub-end-insert
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
  }, []);

  ...
};
~~~~~~~

Zunächst rufen wir mit `API_ENDPOINT` (A) beliebten Tech-Storys ab. Weil es nahe liegt, wählen wir als Thema `React` (B). Dafür verwenden wir die [Fetch-API des nativen Browsers](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) (B). Die Daten werden über die API im Datenformat [JSON](https://developer.mozilla.org/de/docs/Learn/JavaScript/Objects/JSON) gesendet und von uns übersetzt (C). Letztendlich senden wir das Ergebnis der Abfrage in der passenden Datenstruktur als `payload` an den Status unserer Komponente (D).

Im Codebeispiel verwenden wir mit `${API_ENDPOINT}react` einen [Template-String](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Template_literals). Wäre diese Funktion in JavaScript nicht verfügbar, nutzten wir die klassische Konkatenation mit dem `+`-Operator: 


{title="Code Playground",lang="javascript"}
~~~~~~~
const greeting = 'Hello';

// + operator
const welcome = greeting + ' React';
console.log(welcome);
// Hello React

// template literals
const anotherWelcome = `${greeting} React`;
console.log(anotherWelcome);
// Hello React
~~~~~~~

Öffne die Anwendung im Browser, um die Änderungen zu begutachten. Da sich die Struktur der Daten nicht geändert hat, ist es weiterhin möglich, die Liste nach dem Abrufen mit der Suchfunktion zu filtern und Element zu entfernen. Wir werden diesen Zugriff auf die Drittanbieter-API in einem der nächsten Abschnitte wieder zurücksetzen. Für das Lernen mit unserer App-Komponente ist dieser nicht weiter erforderlich. Wir haben ihn einzig und allein für die Experimente zum Verwalten von asynchronen Daten bei der Statusverwaltung in React benötigt.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Data-Fetching-with-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Impossible-States...hs/Data-Fetching-with-React?expand=1).
* Informiere dich über [Hacker News](https://news.ycombinator.com/) und deren [API](https://hn.algolia.com/api).
* Lese mehr über die [Fetch API in JavaScript](https://developer.mozilla.org/de/docs/Web/API/Fetch_API) zum Herstellen einer Verbindung zu Remote-APIs.
* Lese mehr zum Thema [JavaScript Template-Strings](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Template_literals).
