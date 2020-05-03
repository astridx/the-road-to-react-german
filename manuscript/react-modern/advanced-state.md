## React Status (fFortgeschrittene Techniken)

Das gesamte Statusmanagement in unserer Anwendung verwendet den `useState` Hook von React. Eine ausgefeilte Statusverwaltung nutzt zusätzlich unter Umständen **Reacts useReducer Hook**. Da sich beim Konzept der Reduzierungen in JavaScript die Geister scheiden, werden ich hier nicht ausführlich darauf eingehen. Unbeachtet lasse ich das Thema aber ebenfalls nicht. Die Übungen am Ende dieses Abschnitts geben dir genug Material, um dir deine eigene Meinung zu bilden.

Wir werden die Statusverwaltung der `stories` vom `useState`-Hook in einen neuen `useReducer`-Hook verschieben. Führe zunächst eine Reduzier-Funktion außerhalb der App-Komponenten ein. Eine solche Funktion empfängt immer einen `state` und eine `action`. Basierend auf diesen beiden Argumenten gibt ein Reduzierer einen neuen Status zurück:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const storiesReducer = (state, action) => {
  if (action.type === 'SET_STORIES') {
    return action.payload;
  } else {
    throw new Error();
  }
};
# leanpub-end-insert
~~~~~~~

Eine Reduzierer-Aktion wird oft mit einem Typ `type` assoziiert. Wenn dieser Typ einer Bedingung im Reduzierer entspricht, dann führe eine Aktion aus. Wenn dies nicht so ist, dann gib einen Fehler aus. So erinnerst du dich selbst daran, dass hier die Implementierung lückenhaft ist. Die Funktion `storiesReducer` deckt einen Typ `type` ab und gibt dann die Nutzdaten `payload` der eingehenden Aktion zurück, ohne den aktuellen Status zur Berechnung des neuen Status zu verwenden. Der neue Zustand ist `payload`.

Tausche in der App-Komponente `useState` gegen `useReducer` aus, um den Status von `stories` zu verwalten. Der neue Hook erhält eine Reduzierer-Funktion und einen Anfangszustand als Argumente und gibt ein Array mit zwei Elementen zurück. Das erste ist der *aktuelle Status*, beim zweiten handelt es sich um die *Statusaktualisierungsfunktion* (*Dispatcher*):

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
    []
  );
# leanpub-end-insert

  ...
};
~~~~~~~

Nachfolgend verwenden wir die Dispatcher-Funktion `dispatchStories` anstelle der Funktion `setStories`, die bisher von `useState` zurückgegeben wurde. Anstatt den Status explizit mit der Statusaktualisierungsfunktion von `useState` festzulegen, löst die `useReducer` Statusaktualisierungsfunktion eine Aktion für den Reduzierer aus. Die Aktion beinhaltet einen Typ und die optionalen Nutzdaten `payload`:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  React.useEffect(() => {
    setIsLoading(true);

    getAsyncStories()
      .then(result => {
# leanpub-start-insert
        dispatchStories({
          type: 'SET_STORIES',
          payload: result.data.stories,
        });
# leanpub-end-insert
        setIsLoading(false);
      })
      .catch(() => setIsError(true));
  }, []);

  ...

  const handleRemoveStory = item => {
    const newStories = stories.filter(
      story => item.objectID !== story.objectID
    );

# leanpub-start-insert
    dispatchStories({
      type: 'SET_STORIES',
      payload: newStories,
    });
# leanpub-end-insert
  };

  ...
};
~~~~~~~

Wenn du die Anwendung im Browser öffnest, wirst du keinen Unterschied feststellen, obwohl ein Reduzierer und der `useReducer`-Hook von React jetzt den Status der Liste `stories` verwalten. Lass uns das Konzept eines Reduzierers auf eine minimale Version bringen, indem wir mehr als einen Zustandsübergang analysieren.

Bisher verwaltet der Handler `handleRemoveStory` unsere `stories`-Liste. Es ist möglich, diese Logik in die Reduzierer-Funktion zu verschieben und den Reduzierer mit einer Aktion zu verwalten. Dies ist ein weiteres Beispiel für den Wechsel von der imperativen zur deklarativen Programmierung. Anstatt den Code selbst zu schreiben und so dem Reduzierer genau zu beschreiben, *wie etwas zu erledigen ist*, teilen wir ihm mit *was zu erledigen ist*. Alle Implementierungsdetails sind im Reduzierer gekapselt.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleRemoveStory = item => {
# leanpub-start-insert
    dispatchStories({
      type: 'REMOVE_STORY',
      payload: item,
    });
# leanpub-end-insert
  };

  ...
};
~~~~~~~

Jetzt deckt die Reduzierer-Funktion diesen Fall in einem neuen bedingten Zustandsübergang ab. Wenn die Bedingung zum Entfernen eines Elementes erfüllt ist, verfügt der Reduzierer über alle notwendigen Implementierungsdetails. Die Aktion enthält alle Informationen, die Kennung eines Elements, um es aus dem aktuellen Status zu entfernen und eine neue Liste gefilterter Storys als Status zurückzugeben.

{title="src/App.js",lang="javascript"}
~~~~~~~
const storiesReducer = (state, action) => {
  if (action.type === 'SET_STORIES') {
    return action.payload;
# leanpub-start-insert
  } else if (action.type === 'REMOVE_STORY') {
    return state.filter(
      story => action.payload.objectID !== story.objectID
    );
  } else {
# leanpub-end-insert
    throw new Error();
  }
};
~~~~~~~

Wenn du mehr Zustandsübergänge zur Reduzierer-Funktion hinzufügst, werden `if`-Anweisungen unübersichtlich. Verbessere die Lesbarkeit, indem du in deinen Code `switch`-Anweisung für alle Statusübergänge verwendest:

{title="src/App.js",lang="javascript"}
~~~~~~~
const storiesReducer = (state, action) => {
# leanpub-start-insert
  switch (action.type) {
    case 'SET_STORIES':
      return action.payload;
    case 'REMOVE_STORY':
      return state.filter(
        story => action.payload.objectID !== story.objectID
      );
    default:
      throw new Error();
  }
# leanpub-end-insert
};
~~~~~~~

In diesem Abschnitt haben wir in das JavaScript Reduzierer-Konzept hineingeschnuppert. Wir implementierten zwei Zustandsübergängen und probierten aus, wie der aktuelle Zustand mithilfe einer Aktion in einen neuen umgewandelt wird. Neben der technischen Implementierung vernachlässigen wir die eigentliche Aufgabe unsere Anwendung nicht: das Verwalten der `stories`-Liste. Am Ende dieses Abschnitts legen wir eine `stories`-Liste als Status für die asynchron ankommenden Daten fest und entfernen ein Element aus dieser, wobei nur ein Status für den Reduzierer und den zugehörigen `useReducer`-Hook notwendig ist.

Sieh dir die in den Übungen verlinkten Websites an, um das Reduzierer-Konzept in JavaScript und die Verwendung von **Reacts useReducer Hook** vollends zu verstehen.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Advanced-State).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt]( ).
* Lese mehr zum Thema [reducers in JavaScript](https://www.robinwieruch.de/javascript-reducer).
* Lese mehr zum Thema reducers and useReducer in React ([0](https://www.robinwieruch.de/react-usereducer-hook), [1](https://de.reactjs.org/docs/hooks-reference.html#usereducer)).