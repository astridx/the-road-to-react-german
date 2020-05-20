## Memoized Handler in React (Fortgeschrittene Anleitung)

In den vorherigen Abschnitten hast du einiges über Handler allgemein, Callback-Handler und Inline-Handler erfahren. In diesem Abschnitt stelle ich dir **Memoized Handler** vor, die auf Handler und Callback-Handler angewendet werden. [Memoisation](https://de.wikipedia.org/wiki/Memoisation) ist eine Technik zur Beschleunigung von Software, indem Rückgabewerte von Funktionen zwischengespeichert anstatt neu berechnet werden. Zu Lernzwecken verschieben wir die gesamte Datenabruflogik in eine eigenständige Funktion außerhalb des Seiteneffekts (A). Umgeben diese mit einem `useCallback`-Hook (B) und rufe sie im `useEffect`-Hook (C) auf:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  // A
# leanpub-start-insert
  const handleFetchStories = React.useCallback(() => { // B
# leanpub-end-insert
    if (!searchTerm) return;

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
  }, [searchTerm]); // E
# leanpub-end-insert

  React.useEffect(() => {
# leanpub-start-insert
    handleFetchStories(); // C
  }, [handleFetchStories]); // D
# leanpub-end-insert

  ...
};
~~~~~~~

Das Verhalten der Anwendung ändert sich nicht. Ausschließlich die Implementierungslogik wurde überarbeitet. Vorher war die Datenabruflogik anonym als Seiteneffekt implementiert. Jetzt ist sie als Funktion für die Anwendung verfügbar.

Untersuchen wir als Nächstes, ob der `useCallback`-Hook weiterhin benötigt wird. Dieser Hook erstellt jedes Mal eine `memoized`-Funktion, wenn sich das Abhängigkeitsarray (E) ändert. Als Folge dessen wird der `useEffect`-Hook erneut aufgerufen (C), da er von der neuen Funktion (D) abhängt:

{title="Visualization",lang="javascript"}
~~~~~~~
1. change: searchTerm
2. implicit change: handleFetchStories
3. run: side-effect
~~~~~~~

Wenn wir mit dem `useCallback`-Hook keine `memoized`-Funktion erstellten, würde mit jeder App-Komponente eine neue `handleFetchStories`-Funktion erstellt und im `useEffect`-Hook aufgerufen, um Daten abzurufen. Die abgerufenen Daten werden dann als Status in der Komponente gespeichert. Da sich der Status der Komponente geändert hat, wird diese neu gerendert und eine neue Funktion `handleFetchStories` erstellt. Der Seiteneffekt würde ausgelöst, um Daten abzurufen, und wir finden uns in einer Endlosschleife wieder:

{title="Visualization",lang="javascript"}
~~~~~~~
1. define: handleFetchStories
2. run: side-effect
3. update: state
4. re-render: component
5. re-define: handleFetchStories
6. run: side-effect
...
~~~~~~~

Der `useCallback`-Hook erstellt nur dann eine neue `memoized`-Funktion, wenn sich der Suchbegriff ändert. In diesem Fall ist es uns wichtig, dass die Daten erneut abgerufen werden, damit die gerenderte Liste jederzeit zum Suchwort passt.

Durch Verschieben der Datenabruffunktion `handleFetchStories` an eine Stelle außerhalb des `useEffect`-Hook ist diese für andere Teile der Anwendung wiederverwendet. Wir verwenden sie bisher nicht, aber es wäre möglich. Der `useEffect`-Hook wird implizit aufgerufen, wenn sich `searchTerm` ändert, da `handleFetchStories` immer dann neu definiert wird. Da der `useEffect`-Hook von `handleFetchStories` abhängt, wird der Nebeneffekt bei jedem Datenabruf aufgerufen.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Memoized-Handler-in-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Data-Re-Fetching-in-React...hs/Memoized-Handler-in-React?expand=1).
* Lese mehr über [Reacts useCallback Hook](https://de.reactjs.org/docs/hooks-reference.html#usecallback).
