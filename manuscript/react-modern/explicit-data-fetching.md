## Explizite Datenabrufe in React

Das erneute Abrufen aller Daten bei jeder Eingabe in das Eingabefeld ist nicht optimal. Da wir hierzu eine Drittanbieter-API verwenden, sind deren Interna nicht in Einflussbereich. Wenn wir Pech haben, wird uns aufgrund einer [Durchsatzratenbegrenzung] (https://de.wikipedia.org/wiki/Durchsatzratenbegrenzung), anstelle der Daten eine Fehlermeldung zurückgeben.

Um dieses Problem zu lösen, verändern wir die Art des Datenabrufs von implizit zu explizit. Mit anderen Worten, die Anwendung ruft Daten nur dann erneut ab, wenn jemand auf eine Bestätigungsschaltfläche klickt. Fügen wir zunächst ein Schaltflächenelement für die Bestätigung mittels JSX zu unserer Benutzeroberfläche hinzu:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <InputWithLabel
        id="search"
        value={searchTerm}
        isFocused
# leanpub-start-insert
        onInputChange={handleSearchInput}
# leanpub-end-insert
      >
        <strong>Search:</strong>
      </InputWithLabel>

# leanpub-start-insert
      <button
        type="button"
        disabled={!searchTerm}
        onClick={handleSearchSubmit}
      >
        Submit
      </button>
# leanpub-end-insert

      ...
    </div>
  );
};
~~~~~~~

Im Anschluss daran implementieren wir bei den Handlern für das Eingabefeld und die Schaltfläche alles Notwendige, um den Status der Komponente zu aktualisieren. Der Eingabefeld-Handler aktualisiert weiterhin den `searchTerm`. Der Schaltflächen-Handler setzt die `url`, die sich aus dem *aktuellen* `searchTerm` und der hartcodierten statischen API-URL ableitet, als neuen Status:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const [searchTerm, setSearchTerm] = useSemiPersistentState(
    'search',
    'React'
  );

# leanpub-start-insert
  const [url, setUrl] = React.useState(
    `${API_ENDPOINT}${searchTerm}`
  );
# leanpub-end-insert

  ...

# leanpub-start-insert
  const handleSearchInput = event => {
# leanpub-end-insert
    setSearchTerm(event.target.value);
  };

# leanpub-start-insert
  const handleSearchSubmit = () => {
    setUrl(`${API_ENDPOINT}${searchTerm}`);
  };
# leanpub-end-insert

  ...
};
~~~~~~~

Am Ende wird der Nebeneffekt des Datenabrufs nicht mehr bei jeder Änderung von `searchTerm` aufgerufen. Anstelle davon verwenden wir die `url` um die Liste zu aktualisieren. Diese wird vom Benutzer explizit festgelegt, wenn er die Suche über die neue Schaltfläche bestätigt:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleFetchStories = React.useCallback(() => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    fetch(url)
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
# leanpub-start-insert
  }, [url]);
# leanpub-end-insert

  React.useEffect(() => {
    handleFetchStories();
  }, [handleFetchStories]);

  ...
};
~~~~~~~

`searchTerm` hatte bisher zwei Aufgaben: Zum einen war die Variable für die Aktualisierung des Status des Eingabefelds zuständig. Zum andere war sie für den Abruf der Daten verantwortlich --- ihre Aufgabe war es, den Seiteneffekt auszulösen. Hier waren die [Zuständigkeiten nicht getrennt](https://wiki.selfhtml.org/index.php?title=Separation_of_concerns&oldid=66463). Jetzt wird `searchTerm` nur für die Aktualisierung des Eingabefelds verwendet. Eine zweite statusbehaftete Variable namens `url` wurde eingeführt, um den Seiteneffekt beim Abrufen von Daten auszulösen. Dieser tritt nur dann auf, wenn ein Benutzer auf die Bestätigungsschaltfläche klickt.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Explicit-Data-Fetching-with-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Memoized-Handler-in-React...hs/Explicit-Data-Fetching-with-React?expand=1).
* Warum wird `useState` anstelle von `useSemiPersistentState` für die `url`-Statusverwaltung verwendet?
* Warum wird in der Funktion `handleFetchStories` nicht mehr nach einem leeren `searchTerm` gesucht?
