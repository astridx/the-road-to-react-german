## Erneutes Abrufen von Daten in React

Bisher ruft die App-Komponente eine Liste über ein hartcodiertes Suchwort ab (`react`). Danach ist es möglich, dass Benutzer clientseitigen die Suchfunktion nutzen. Jetzt verschieben wir diese Funktion vom Client zum Server. Dabei verwenden wir `searchTerm` als dynamisches Suchwort bei der API-Anfrage.

Tausche zuerst `searchedStories` gegen `stories.data`, da wir ohnehin die Suchergebnisse von der API abfragen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
# leanpub-start-insert
        <List list={stories.data} onRemoveItem={handleRemoveStory} />
# leanpub-end-insert
      )}
    </div>
  );
};
~~~~~~~

Im zweiten Schritt stellst du sicher, dass anstelle des hartcodierten Suchwortes `searchTerm` aus dem Status der App-Komponente für die Anfrage verwendet wird. Wenn `searchTerm` eine leere Zeichenfolge ist, geschieht keine Abfrage:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  React.useEffect(() => {
# leanpub-start-insert
    if (searchTerm === '') return;
# leanpub-end-insert

    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    fetch(`${API_ENDPOINT}${searchTerm}`)
# leanpub-end-insert
      .then(response => response.json())
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
          payload: result.hits,
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
  }, []);

  ...
};
~~~~~~~

The initial search respects the search term now, so we'll implement data refetching. If the `searchTerm` changes, run the side-effect for the data fetching again. If `searchTerm` is not present (e.g. null, empty string, undefined), do nothing (as a more generalized condition):

Die anfängliche Suche berücksichtigt jetzt den Suchbegriff für den Status der Liste. Daher implementieren wir das erneute Abrufen von Daten, indem wir `searchTerm` in den  bisher leeren Array im zweiten Argument der Funktion `useEffect` einfügen. Wenn sich `searchTerm` ändert, wird somit der Seiteneffekt aufgerufen. Außerdem verallgemeinern wir den Umgang mit einem problematischen Suchwort: Wenn `searchTerm` nicht vorhanden ist (null, leere Zeichenfolge, undefiniert), passiert nichts:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  React.useEffect(() => {
# leanpub-start-insert
    if (!searchTerm) return;
# leanpub-end-insert

    dispatchStories({ type: 'STORIES_FETCH_INIT' });

    fetch(`${API_ENDPOINT}${searchTerm}`)
      .then(response => response.json())
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
          payload: result.hits,
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
# leanpub-start-insert
  }, [searchTerm]);
# leanpub-end-insert

  ...
};
~~~~~~~

Wir haben die Funktion von einer clientseitigen auf eine serverseitige Suche geändert. Anstatt eine vordefinierte Liste auf dem Client zu filtern, wird mit `searchTerm` eine serverseitig gefilterte abgerufen. Das anfängliche und das Abrufen von Daten, wenn sich das `searchTerm` ändert, geschieht serverseitig. Die Funktion ist jetzt vollständig serverseitig.

Das erneute Abrufen von Daten bei jeder Eingabe in das Eingabefeld ist nicht optimal. Wir werden dies daher bald korrigieren. Da diese Implementierung die API belastet, treten Fehler auf, wenn die Anfragen verdichtet eingehen.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Data-Re-Fetching-in-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Data-Fetching-with-React...hs/Data-Re-Fetching-in-React?expand=1).
  