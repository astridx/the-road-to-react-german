## Benutzerdefinierte React Hooks (Fortgeschrittene Anleitung)

Bisher haben wir die beiden beliebtesten Hooks in React behandelt: `useState` und `useEffect`. Mithilfe von `useState` wird die Anwendung interaktiv – wechselseitig beeinflussend. Mit `useEffect` nimmst du Einfluss auf den Lebenszyklus deiner Komponente.

Sei gespannt, in diesem Buch wirst du weitere React Hooks kennenlernen. Als Nächstes befassen wir uns mit **Reacts Custom Hooks**. Anhand des Namens vermutest du es sicher schon: Wir entwickeln selbst einen Hook.

Wir erstellen einen benutzerdefinierten Hook. Diesen nennen wir `useSemiPersistentState`. Dabei bauen wir auf den beiden vorhandenen Hooks auf. Seine Aufgabe wird es sein, den Status zu verwaltet und den lokalen Speicher zu synchronisieren. Da durch das Löschen des Browser-Caches relevante Daten gelöscht werden ist der lokale Speicher nicht vollständig persistent. Verschiebe als Erstes alle wichtigen Inhalte von der App-Komponente in den neuen Hook:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const useSemiPersistentState = () => {
  const [searchTerm, setSearchTerm] = React.useState(
    localStorage.getItem('search') || ''
  );

  React.useEffect(() => {
    localStorage.setItem('search', searchTerm);
  }, [searchTerm]);
};
# leanpub-end-insert

const App = () => {
...
};
~~~~~~~

Bisher enthält der neue Hook nichts anderes als `useState` und `useEffect`. Um ihn zu nutzen geben wir die Werte zurück, die in unserer App-Komponente von diesem erwartet werden:

{title="src/App.js",lang="javascript"}
~~~~~~~
const useSemiPersistentState = () => {
  const [searchTerm, setSearchTerm] = React.useState(
    localStorage.getItem('search') || ''
  );

  React.useEffect(() => {
    localStorage.setItem('search', searchTerm);
  }, [searchTerm]);

# leanpub-start-insert
  return [searchTerm, setSearchTerm];
# leanpub-end-insert
};
~~~~~~~

Wir befolgen hier zwei Konventionen in React. Erstens die Namenskonvention, bei der das Präfix "use" vor jedem Hook-Namen steht. Zweitens werden die Werte als Array zurückgegeben. Im nächsten Beispiel verwenden wir den benutzerdefinierten Hook in der App-Komponente. Hierbei nutzen wir die Array-Destrukturierung:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

# leanpub-start-insert
  const [searchTerm, setSearchTerm] = useSemiPersistentState();
# leanpub-end-insert

  const handleSearch = event => {
    setSearchTerm(event.target.value);
  };

  const searchedStories = stories.filter(story =>
    story.title.toLowerCase().includes(searchTerm.toLowerCase())
  );

  return (
    ...
  );
};
~~~~~~~

Ein weiteres Ziel eines benutzerdefinierten Hooks ist die Wiederverwendbarkeit. Die Variablennamen in diesem Hooks beziehen sich auf die Suchfunktion. Der Hook ist besser wiederverwendbar, wenn er neutrale Namen verwendet. Deshalb optimieren wir im nächsten Schritt die Benennung. Im Hook beziehen wir uns nachfolgend auf einen neutralen Wert, der im Status festgelegt ist und über den lokalen Speicher synchronisiert wird:

{title="src/App.js",lang="javascript"}
~~~~~~~
const useSemiPersistentState = () => {
# leanpub-start-insert
  const [value, setValue] = React.useState(
    localStorage.getItem('value') || ''
# leanpub-end-insert
 );

  React.useEffect(() => {
# leanpub-start-insert
    localStorage.setItem('value', value);
  }, [value]);
# leanpub-end-insert

# leanpub-start-insert
  return [value, setValue];
# leanpub-end-insert
};
~~~~~~~

Wir nutzen den abstrahierten Wert "value" innerhalb des benutzerdefinierten Hooks. Wenn wir diesen in der App-Komponente verwenden, ist es möglich, den zurückgegebenen aktuellen Status und die Statusaktualisierungsfunktion für alle Elemente (zum Beispiel `searchTerm` and `setSearchTerm`) mit Array-Destrukturierung verwenden.

Unser Hook ist weiterhin verbesserungsfähig. Die mehrmalige Verwendung des benutzerdefinierten Hooks in der React-Anwendung führt zu einem Überschreiben des mit „Wert“ zugewiesenen Elements im lokalen Speicher. Dies korrigieren wir mithilfe eines Schlüssels:  

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const useSemiPersistentState = key => {
# leanpub-end-insert
  const [value, setValue] = React.useState(
# leanpub-start-insert
    localStorage.getItem(key) || ''
# leanpub-end-insert
 );

  React.useEffect(() => {
# leanpub-start-insert
    localStorage.setItem(key, value);
  }, [value, key]);
# leanpub-end-insert

  return [value, setValue];
};

const App = () => {
  ...

  const [searchTerm, setSearchTerm] = useSemiPersistentState(
# leanpub-start-insert
    'search'
# leanpub-end-insert
  );

  ...
};
~~~~~~~

Da der Schlüssel von außen stammt, nimmt der benutzerdefinierte Hook an, dass er sich ändert. Er wird daher in das Abhängigkeitsarray des Hooks `useEffect` aufgenommen. Ohne dies ist der Seiteneffekt mit einem veralteten Schlüssel (*stale*) machbar, wenn dieser zwischen den Renderings geändert wird.

Eine weitere Verbesserung besteht darin, dem benutzerdefinierten Hook den Ausgangszustand mitzugeben:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const useSemiPersistentState = (key, initialState) => {
# leanpub-end-insert
  const [value, setValue] = React.useState(
# leanpub-start-insert
    localStorage.getItem(key) || initialState
# leanpub-end-insert
  );

  ...
};

const App = () => {
  ...

  const [searchTerm, setSearchTerm] = useSemiPersistentState(
# leanpub-start-insert
    'search',
    'React'
# leanpub-end-insert
  );

  ...
};
~~~~~~~

Glückwunsch! Du hast deinen ersten benutzerdefinierten Hook erstellt. Mach die Änderungen rückgängig, wenn du mit dieser Art Hook nicht vertraut bist. Verwende dann weiterhin `useState` und `useEffect`.

Wenn du die Herausforderung annimmst und dich in das Thema benutzerdefinierte Hooks einarbeitest, öffnen sich dir viele neue Optionen. Es ist möglich, komplizierte Implementierungen umzusetzen und diese gleichzeitig von deiner Komponente fernzuhalten (abzukapseln). Der Code ist dabei in mehreren Komponenten wiederverwendbar. Und nicht nur das, er ist als externe Bibliothek in einer Open-Source-Version für andere nutzbar. Wenn du in der Suchmaschine deines Vertrauens eine Suche startest, wirst du eine Menge React-Hooks finden. Jeder einzelne ist in React-Anwendungen verwendbar. Dabei ist es nicht notwendig, dass man die Implementierungsdetails kennt.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Custom-Hooks).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Side-Effects...hs/React-Custom-Hooks?expand=1).
* Lese mehr zum Thema [React Hooks](https://www.robinwieruch.de/react-hooks). Hooks sind das A und O in Funktionskomponenten, daher ist es wichtig, das Konzept zu verstehen ([0](https://de.reactjs.org/docs/hooks-overview.html), [1](https://de.reactjs.org/docs/hooks-custom.html)).
