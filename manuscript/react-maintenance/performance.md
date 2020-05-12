# Wartung in React

Ist die Anwendung fertig eingerichtet und kommen mehr und mehr Nutzer hinzu, spielt die Wartung eine immer größere Rolle. Um den laufend Aufwand so klein wie möglich zu halten, werden wir uns mit Leistungsoptimierung, Typensicherheit, Tests und Projektstruktur befassen. Lese in diesem Kapitel, wie du die Grundlagen für eine pflegeleichte Anwendung legst, ohne dabei Qualitätseinbußen in Kauf zu nehmen.

Die Leistungsoptimierung verhindert, dass Anwendungen langsamer werden, indem eine effiziente Nutzung der verfügbaren Ressourcen sichergestellt wird. Typisierte Programmiersprachen wie TypeScript erkennen Fehler früherzeitig. Automatische Tests geben uns bei Änderungen Sicherheit, dass der ursprüngliche Code weiterhin funktioniert. Und, last but not least unterstützt die Projektstruktur eine organisierte Verwaltung, was in Szenarien nützlich ist, in denen unterschiedliche Teams aus verschiedenen Bereichen zusammen arbeiten.

## Performanz in React (Fortgeschrittene Anleitung)

In diesem Abschnitt zeige ich dir, wie du dir einen Überblick über die Performance deiner Applikation verschaffst. Du wirst feststellen: React-Anwendungen sind von Hause aus leistungsstark. Es gibt eine Vielzahl unterschiedlicher Monitoring und Reporting-Tools. Ich habe mich dazu entschieden, dir ein grundlegendendes Werkzeug zu zeigen: `console.log()` in Verbindung mit den Entwicklertools deines Browsers.

### Das erste Rendern

Wir haben uns bereits mit Reacts useEffect Hook befasst, der für Seiteneffekte verwendet wird. Dieser wird beim ersten Rendern (Mounten) einer Komponente und dann bei jedem Aktualisieren aufgerufen. Indem wir ihm als zweites Argument ein leeres Abhängigkeitsarray übergeben, wird der Hook nur beim ersten Rendern ausgelöst. Standardmäßig gibt es keine Möglichkeit, den Hook so einzustellen, dass er bei jedem Aktualisieren aber nicht beim ersten Rendern (Mounten) aufgerufen wird. Sieh dir dies beispielsweise für die Statusverwaltung an. Wir verwalten den Status mit dem `useState`-Hook. Das dieser semi-persistenten ist erreichen wir, indem wir mithilfe des `useEffect`-Hook den aktuellen Wert im lokalen Speicher des Browsers ablegen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const useSemiPersistentState = (key, initialState) => {
  const [value, setValue] = React.useState(
    localStorage.getItem(key) || initialState
  );

  React.useEffect(() => {
# leanpub-start-insert
    console.log('A');
# leanpub-end-insert
    localStorage.setItem(key, value);
  }, [value, key]);

  return [value, setValue];
};
~~~~~~~

Nutze die Anweisung console.log(‚A‘); um dir über die Konsole der Entwicklertools des Browsers anzusehen, wann der Status in den lokalen Speicher gespeichert wird. Erstmals geschieht dies, wenn die Komponente das erste Mal gerendert wird. Dies ist nicht sinnvoll, da zu diesem Zeitpunkt der Anfangswert aktiv ist. Erforderlich ist das Speichern nur bei einem erneuten Rendern der Komponente – nur dann ist es möglich, dass der Wert sich geändert hat.

Wie erwähnt, gibt es keinen React-Hook, der bei jedem Rendern aufgerufen wird, den ersten Aufruf aber auslässt. Durch die Verwendung des `useRef`-Hooks, bei dem die Eigenschaft `ref.current` beim erneuten Rendern erhalten bleibt, erstellen wir uns diese Lösung selbst.

{title="src/App.js",lang="javascript"}
~~~~~~~
const useSemiPersistentState = (key, initialState) => {
# leanpub-start-insert
  const isMounted = React.useRef(false);
# leanpub-end-insert

  const [value, setValue] = React.useState(
    localStorage.getItem(key) || initialState
  );

  React.useEffect(() => {
# leanpub-start-insert
    if (!isMounted.current) {
      isMounted.current = true;
    } else {
# leanpub-end-insert
      console.log('A');
      localStorage.setItem(key, value);
# leanpub-start-insert
    }
# leanpub-end-insert
  }, [value, key]);

  return [value, setValue];
};
~~~~~~~

Wir nutzen `ref` und seine veränderbare `current`-Eigenschaft für die imperative Zustandsverwaltung, die kein erneutes Rendern auslöst. Sobald der Hook zum ersten Mal von seiner Komponente aufgerufen wird, wird `current` mit einem Booleschen Wert namens `isMounted` initialisiert, der mit `false` belegt ist. So wird der Seiteneffekt in `useEffect` nicht ausgelöst. Nur das boolesche Flag für `isMounted` wird auf `true` umgeschaltet. Bei jedem erneuten Aufruf wird das Flag im Seiteneffekt ausgewertet. Da es `true` ist, wird der Status im lokalen Speicher gespeichert. Während der Lebensdauer der Komponente bleibt `isMounted` mit `true` belegt. So wird vermieden, dass die Nebenwirkung ausgelöst wird, wenn unser benutzerdefinierter Hook verwendet wird.

Dieses Beispiel ist nicht verhältnismäßig. Der Aufwand lohnt sich nicht, für die kleine Optimierung. Bedenke aber: Es gibt React-Anwendungen, mit komplizierten Berechnungen, in ihren Seiteneffekten. Dann ist es praktischer, diese Technik einzusetzen, um unnötige Funktionsaufrufe zu vermeiden.

*Hinweis: Diese Technik wird nicht nur zur Leistungsoptimierung verwendet, sondern, um einen Nebeneffekt nur dann auszuführen, wenn eine Komponente erneut gerendert wird. Ich habe es mehrmals benutzt und ich vermute, dass du irgendwann auf den einen oder anderen Anwendungsfall stoßen wirst.*

### Don't re-render if not needed

Zuvor haben wir den Re-Rendering-Mechanismus von React untersucht. Wiederhole dies für die App- und List-Komponente. Füge für beide eine Protokollierungsanweisung hinzu, um in der Konsole deines Browsers die Ausführung zu mitzuverfolgen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  console.log('B:App');
# leanpub-end-insert

  return ( ... );
};

const List = ({ list, onRemoveItem }) =>
# leanpub-start-insert
  console.log('B:List') ||
# leanpub-end-insert
  list.map(item => (
    <Item
      key={item.objectID}
      item={item}
      onRemoveItem={onRemoveItem}
    />
  ));
~~~~~~~

Da die List-Komponente keinen Funktionskörper hat, verwendet die List-Komponente stattdessen den Operator `||`. Dies ist ein Trick, um einer Funktionskomponente ohne Funktionskörper eine Protokollierungsanweisung hinzuzufügen. Da die Datei `console.log()` auf der linken Seite des Operators als falsch ausgewertet wird, wird die rechte immer aufgerufen:

{title="Code Playground",lang="javascript"}
~~~~~~~
function getTheTruth() {
  if (console.log('B:List')) {
    return true;
  } else {
    return false;
  }
}

console.log(getTheTruth());
// B:List
// false
~~~~~~~

Konzentrieren wir uns auf die Protokollierung unserer Anwendung in den Entwicklertools des Browsers. Zuerst wird die App-Komponente gerendert, gefolgt von ihren untergeordneten Komponenten (beispielsweise der List-Komponente).

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
B:App
B:App
B:List
~~~~~~~

Da ein Seiteneffekt das Abrufen von Daten nach dem ersten Rendern auslöst, wird nur die App-Komponente gerendert, da die List-Komponente in einem bedingten Rendering durch einen Ladeindikator ersetzt wird. Sobald die Daten eintreffen, werden beide erneut gerendert.

Since a side-effect triggers data fetching after the first render, only the App component renders, because the List component is replaced by a loading indicator in a conditional rendering. Once the data arrives, both components render again.

{title="Visualization",lang="text"}
~~~~~~~
// initial render
B:App
B:List

// data fetching with loading
B:App

// re-rendering with data
B:App
B:List
~~~~~~~

Bisher ist dieses Verhalten in Ordnung. Alles wird zum passenden Zeitpunkt gerendert. Jetzt erweitern wir unsere Protokollierung, wir nehmen das Eingabefeld der SearchForm-Komponente hinzu. In der Konsole siehst du daraufhin einen Eintrag für jedes Zeichen, welches du in das Suchfeld eingibst:

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
~~~~~~~

But the List component shouldn't re-render. The search feature isn't executed via its button, so the `list` passed to the List component should remain the same. This is React's default behavior, which surprises many people.

If a parent component re-renders, its child components re-render as well. React does this by default, because preventing a re-render of child components could lead to bugs, and the re-rendering mechanism of React is still fast.

Sometimes we want to prevent re-rendering, however. For example, huge data sets displayed in a table shouldn't re-render if they are not affected by an update. It's more efficient to perform an equality check if something changed for the component. Therefore, we can use React's memo API to make this equality check for the props:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const List = React.memo(
# leanpub-end-insert
  ({ list, onRemoveItem }) =>
    console.log('B:List') ||
    list.map(item => (
      <Item
        key={item.objectID}
        item={item}
        onRemoveItem={onRemoveItem}
      />
    ))
# leanpub-start-insert
);
# leanpub-end-insert
~~~~~~~

However, the output stays the same when typing into the SearchForm's input field:

{title="Visualization",lang="text"}
~~~~~~~
B:App
B:List
~~~~~~~

The `list` passed to the List component is the same, but the `onRemoveItem` callback handler isn't. If the App component re-renders, it always creates a new version of this callback handler. Earlier, we used React's useCallacbk Hook to prevent this behavior, by creating a function only on a re-render (if one of its dependencies has changed).

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const handleRemoveStory = React.useCallback(item => {
# leanpub-end-insert
    dispatchStories({
      type: 'REMOVE_STORY',
      payload: item,
    });
# leanpub-start-insert
  }, []);
# leanpub-end-insert

  ...

  console.log('B:App');

  return (... );
};
~~~~~~~

Since the callback handler gets the `item` passed as an argument in its function signature, it doesn't have any dependencies and is declared only once when the App component initially renders. None of the props passed to the List component should change now. Try it with the combination of `memo` and `useCallback`, to search via the SearchForm's input field. The "B:List" output disappears, and only the App component re-renders with its "B:App" output.

While all props passed to a component stay the same, the component renders again if its parent component is forced to re-render. That's React's default behavior, which works most of the time because the re-rendering mechanism is fast enough. However, if re-rendering decreases the performance of a React application, `memo` helps prevent re-rendering.

Sometimes `memo` alone doesn't help, though. Callback handlers are re-defined each time in the parent component and passed as *changed* props to the component, which causes another re-render. In that case, `useCallback` is used for making the callback handler only change when its dependencies change.

### Don't rerun expensive computations

Sometimes we'll have performance-intensive computations in our React components -- between a component's function signature and return block -- which run on every render. For this scenario, we must create a use case in our current application first.

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const getSumComments = stories => {
  console.log('C');

  return stories.data.reduce(
    (result, value) => result + value.num_comments,
    0
  );
};
# leanpub-end-insert

const App = () => {
  ...

# leanpub-start-insert
  const sumComments = getSumComments(stories);
# leanpub-end-insert

  return (
    <div>
# leanpub-start-insert
      <h1>My Hacker Stories with {sumComments} comments.</h1>
# leanpub-end-insert

      ...
    </div>
  );
};
~~~~~~~

If all arguments are passed to a function, it's acceptable to have it outside the component. It prevents creating the function on every render, so the `useCallback` hook becomes unnecessary. The function still computes the value of summed comments on every render, which becomes a problem for more expensive computations.

Each time text is typed in the input field of the SearchForm component, this computation runs again with an output of "C". This may be fine for a non-heavy computation like this one, but imagine this computation would take more than 500ms. It would give the re-rendering a delay, because everything in the component has to wait for this computation. We can tell React to only run a function if one of its dependencies has changed. If no dependency changed, the result of the function stays the same. React's useMemo Hook helps us here:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const sumComments = React.useMemo(() => getSumComments(stories), [
    stories,
  ]);
# leanpub-end-insert

  return ( ... );
};
~~~~~~~

For every time someone types in the SearchForm, the computation shouldn't run again. It only runs if the dependency array, here `stories`, has changed. After all, this should only be used for cost expensive computations which could lead to a delay of a (re-)rendering of a component.

Now, after we went through these scenarios for `useMemo`, `useCallback`, and `memo`, remember that these shouldn't necessarily be used by default. Apply these performance optimization only if you run into a performance bottlenecks. Most of the time this shouldn't happen, because React's rendering mechanism is pretty efficient by default. Sometimes the check for utilities like `memo` can be more expensive than the re-rendering itself.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Performance-in-React).
  * Bestätige die [Änderungen](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/Performance-in-React?expand=1).
* Lese mehr zum Thema [React's memo API](https://reactjs.org/docs/react-api.html#reactmemo).
* Lese mehr zum Thema [React's useCallback Hook](https://reactjs.org/docs/hooks-reference.html#usecallback).
* Download *React Developer Tools* as an extension for your browser. Open it for your application in the browser via the browser's developer tools and try its various features. For example, you can use it to visualize React's component tree and its updating components.
* Does the SearchForm re-render when removing an item from the List with the "Dismiss" button? If it's the case, apply performance optimization techniques to prevent re-rendering.
* Does each Item re-render when removing an item from the List with the "Dismiss" button? If it's the case, apply performance optimization techniques to prevent re-rendering.
* Remove all performance optimizations to keep the application simple. Our current application doesn't suffer from any performance bottlenecks. Try to avoid [premature optimzations](https://en.wikipedia.org/wiki/Program_optimization). Use this section as reference, in case you run into performance problems.