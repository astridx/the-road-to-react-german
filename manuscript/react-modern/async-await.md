## Async/Await in React (Fortgeschrittene Anleitung)

Du wirst in React häufig mit asynchronen Daten arbeiten. Daher ist es hilfreich, wenn du die alternative Syntax für die Behandlung von Promise-Objekten kennst: async/await. Aus diesem Grund habe ich die Funktion `handleFetchStories` in unserem Beispiel überarbeitet. Mein Refactoring zeigt dir, wie du idealerweise vorgehst. Eine Fehlerbehandlung lasse ich im ersten Schritt der Übersicht halber außen vor:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const handleFetchStories = React.useCallback(async () => {
# leanpub-end-insert
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    const result = await axios.get(url);
# leanpub-end-insert

# leanpub-start-insert
    dispatchStories({
      type: 'STORIES_FETCH_SUCCESS',
      payload: result.data.hits,
    });
# leanpub-end-insert
  }, [url]);

  ...
};
~~~~~~~

Unsere Funktion verwendet das Schlüsselwort `async`. Dies schafft die Grundlage für die Verwendung von async/await. Sobald du `await` einsetzt, verhält sich alles analog zu synchronem Code. Die Anweisungen, die hinter `await` steht, werden erst aufgerufen, wenn das (in axios gekapselte) Promise-Objekt aufgelöst wurde und somit sein Ergebnis vorliegt. Dies bedeutet, dass der Code wartet – wie das Wort `await` vermuten lässt.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleFetchStories = React.useCallback(async () => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    try {
# leanpub-end-insert
      const result = await axios.get(url);

      dispatchStories({
        type: 'STORIES_FETCH_SUCCESS',
        payload: result.data.hits,
      });
# leanpub-start-insert
    } catch {
      dispatchStories({ type: 'STORIES_FETCH_FAILURE' });
    }
# leanpub-end-insert
  }, [url]);

  ...
};
~~~~~~~

Mithilfe von `try` und `catch` haben wir am Ende die Fehlerbehandlung integriert. Wenn im `try`-Block etwas nicht korrekt abläuft, springt der Code in den` catch`-Block, um den Fehler zu behandeln. Neben [async/await mit `try`/`catch`-Blöcken](https://developer.mozilla.org/de/docs/Web/JavaScript/Guide/Kontrollfluss_und_Fehlerbehandlung#try-catch) ist [die then()-Methode](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) für die Behandlung von Fehlern in asynchronen Daten möglich.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Async-Await-in-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Third-Party-Libraries-in-React...hs/Async-Await-in-React?expand=1).
* Lese mehr zum Thema [Data-Fetching in React](https://www.robinwieruch.de/react-hooks-fetch-data).
* Lese mehr zum Thema [Async/Await in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function).