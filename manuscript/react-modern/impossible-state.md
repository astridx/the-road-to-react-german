## Nicht mögliche Status

Hast du eine Trennung zwischen den einzelnen Zuständen in der App-Komponente bemerkt? Diese scheinen aufgrund der `useState`-Hooks eine Einheit zu bilden. Technisch gesehen gehören alle Zustände, die sich auf die asynchronen Daten beziehen, zusammen. Wobei ich nicht nur die `stories` als Echtdaten, sondern ebenfalls ihre Lade- und Fehlerzustände meine.

Mehrere `useState`-Hooks in einer React-Komponente stellen grundsätzlich kein Problem dar. Sei aber vorsichtig, wenn du mehrere Statusaktualisierungsfunktionen hintereinander siehst. Diese bedingten Zustände führen unter Umständen zu **nicht möglichen Status** und unerwünschtem Verhalten in der Benutzeroberfläche. Ändere die Funktion zum Abrufen von Pseudodaten wie folgt, um die Fehlerbehandlung zu simulieren:

{title="src/App.js",lang="javascript"}
~~~~~~~
const getAsyncStories = () =>
  new Promise((resolve, reject) => setTimeout(reject, 2000));
~~~~~~~

Ein nicht mögliche Zustand tritt im Falle eines Fehlers beim Abruf der asynchronen Daten auf. Der Status für die Fehlermeldung wird festgelegt, aber der Status für die Ladeanzeige wird nicht widerrufen. In der Benutzeroberfläche führt dies zu einer unendliche langen Anzeige der Ladeinfo bei gleichzeitigem Einblenden des Fehlerhinweises. Korrekt wäre es, die Ladeanzeige auszublenden, wenn die Fehlermeldung eingeblendet wird. Nicht mögliche Zustände sind schwer zu erkennen. Deshalb verursachen sie nicht selten Fehler im Bereich der Benutzeroberfläche.

Glücklicherweise vermeiden wir viele Fehler, indem wir Zustände, die aus mehreren `useState`- und `useReducer`-Hooks bestehen, in einem einzigen `useReducer`-Hook vereinen. Sieh dir diesbezüglich den nachfolgenden `useState`-Hook:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
    []
  );
  const [isLoading, setIsLoading] = React.useState(false);
  const [isError, setIsError] = React.useState(false);

  ...
};
~~~~~~~

Vereine die beiden `useState`-Hooks im `useReducer`-Hook. So erreichst du eine einheitliche Statusverwaltung und verfügst über ein komplexes Status-Objekt:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
# leanpub-start-insert
    { data: [], isLoading: false, isError: false }
# leanpub-end-insert
  );

  ...
};
~~~~~~~

Alles, was mit dem asynchronen Datenabruf zusammenhängt, verwendet die Dispatch-Funktion für Statusübergänge:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
    { data: [], isLoading: false, isError: false }
  );

  React.useEffect(() => {
# leanpub-start-insert
    dispatchStories({ type: 'STORIES_FETCH_INIT' });
# leanpub-end-insert

    getAsyncStories()
      .then(result => {
        dispatchStories({
# leanpub-start-insert
          type: 'STORIES_FETCH_SUCCESS',
# leanpub-end-insert
          payload: result.data.stories,
        });
      })
      .catch(() =>
# leanpub-start-insert
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
# leanpub-end-insert
      );
  }, []);

  ...
};
~~~~~~~

Da wir neue Typen für Zustandsübergänge eingeführt haben, müssen wir diese in der Reduzier-Funktion `storiesReducer` behandeln:

{title="src/App.js",lang="javascript"}
~~~~~~~
const storiesReducer = (state, action) => {
  switch (action.type) {
# leanpub-start-insert
    case 'STORIES_FETCH_INIT':
      return {
        ...state,
        isLoading: true,
        isError: false,
      };
    case 'STORIES_FETCH_SUCCESS':
      return {
        ...state,
        isLoading: false,
        isError: false,
        data: action.payload,
      };
    case 'STORIES_FETCH_FAILURE':
      return {
        ...state,
        isLoading: false,
        isError: true,
      };
    case 'REMOVE_STORY':
      return {
        ...state,
        data: state.data.filter(
          story => action.payload.objectID !== story.objectID
        ),
      };
# leanpub-end-insert
    default:
      throw new Error();
  }
};
~~~~~~~

In er jetzigen Version unserer Anwendung geben wir für jeden Statusübergang ein *neues Status*-Objekt zurück. Dieses enthält alle Schlüssel/Wert-Paare des *aktuellen Status*-Objekts (über den [Spread-Operator](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Spread_operator) von Javascript) und die neuen Möglichkeiten Eigenschaften zu überschreiben. Zum Beispiel setzt `STORIES_FETCH_FAILURE` die Variable `isLoading` zurück. Im Gegensatz dazu behält die booleschen Variable `isError` alle Zustände bei (zum Beispiel `stories`). So umgehen wir den zuvor eingeführten Mangel, bei dem der Ladeindikator im Fehlerfall weiterhin angezeigt wurde.

Beachte, wie sich die Aktion `REMOVE_STORY` geändert hat. Sie nutzt `state.data`, anstelle von `state`. Der Status ist jetzt ein komplexes Objekt mit Daten-, Lade- und Fehlerzuständen. Vorher enthielt er nichts weiter als die Liste von Storys. Überarbeiten wir den restlichen Code im Hinblick auf diese Änderung:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
    { data: [], isLoading: false, isError: false }
  );

  ...

# leanpub-start-insert
  const searchedStories = stories.data.filter(story =>
# leanpub-end-insert
    story.title.toLowerCase().includes(searchTerm.toLowerCase())
  );

  return (
    <div>
      ...

# leanpub-start-insert
      {stories.isError && <p>Something went wrong ...</p>}
# leanpub-end-insert

# leanpub-start-insert
      {stories.isLoading ? (
# leanpub-end-insert
        <p>Loading ...</p>
      ) : (
        <List
          list={searchedStories}
          onRemoveItem={handleRemoveStory}
        />
      )}
    </div>
  );
};
~~~~~~~

Try to use the erroneous data fetching function again and check whether everything works as expected now:

{title="src/App.js",lang="javascript"}
~~~~~~~
const getAsyncStories = () =>
  new Promise((resolve, reject) => setTimeout(reject, 2000));
~~~~~~~

Wir haben unsere Anwendung insofern verbessert, dass wir unzuverlässige Zustandsübergänge mit mehreren `useState`-Hooks in vorhersehbaren mit Reacts useReducer Hook abgeändert haben. Das vom Reduzierer verwaltete Statusobjekt kapselt alles, was mit den Storys zusammenhängt, einschließlich Lade- und Fehlerstatus und Implementierungsdetails wie das Entfernen eines Elements aus der Liste. Wir haben nicht alle Zustände behandelt. Die Liste dieser ist unendlich. Aber wir sind unserem Ziel einen Schritt näher gekommen und haben ein vorhersehbares Ereignis im Zustandsmanagement aufgenommen.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Impossible-States).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Advanced-State...hs/React-Impossible-States?expand=1).
* Lese die zuvor verlinkten Tutorials zu Reduzierern in JavaScript und React.
* Lese mehr zum Thema ["Wann sollte `useState` oder `useReducer` in React verwendet werden?"](https://www.robinwieruch.de/react-usereducer-vs-usestate).