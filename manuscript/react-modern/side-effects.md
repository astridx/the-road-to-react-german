## Seiteneffekte in React

Lasst uns jetzt unser Beispiel weiterentwickeln, und einen Seiteneffekt mit [React's useEffect Hook](https://de.reactjs.org/docs/hooks-effect.html) verwenden. Ziel ist es, dass die Suchkomponente die vorherige Suche speichert, sodass diese letzte Aktion als Status quo bei einem neuen Aufruf im Browser geöffnet wird.

Verwende dafür zunächst das [lokale Storage-Objekt](https://developer.mozilla.org/de/docs/Web/API/Window/localStorage) des Browsers, um den `searchTerm` zu speichern. Nutze daraufhin diesen Wert, um den Anfangszustand festzulegen. Falls bis dato kein Wert gespeichert wurde, bleibt alles so, wie es vorher war – bisher verwendeten wir "React" als initialen Wert hartkodiert.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [searchTerm, setSearchTerm] = React.useState(
# leanpub-start-insert
    localStorage.getItem('search') || 'React'
# leanpub-end-insert
  );

  const handleSearch = event => {
    setSearchTerm(event.target.value);

# leanpub-start-insert
    localStorage.setItem('search', event.target.value);
# leanpub-end-insert
  };

  ...
);
~~~~~~~

Wenn du nach dieser Ergänzung im Eingabefeld einen Text eingibst und daraufhin die Ansicht in deinem Webbrowser aktualisieren wirst du feststellen, dass dein Browser sich den den neuesten Suchbegriff gemerkt hat. Probiere es aus! Die Verwendung des lokalen Speichers in React stellt einen **Seiteneffekt** dar, da wir mithilfe der API des Browsers außerhalb von React interagieren.

Leider gibt es eine Unrichtigkeit. Die Handlerfunktion ist für die Aktualisierung des Status zuständig, hat aber jetzt einen Seiteneffekt. Wenn wir die Funktion `setSearchTerm` an einer anderen Stelle in unserer Anwendung verwenden, wird der lokale Speicher unter Umständen nicht aktualisiert. Wir beheben diesen Mangel, indem wir **Reacts useEffect Hook** verwenden. So erreichen wir, dass der Seiteneffekt jedes Mal ausgelöst wird, wenn sich die Variable `searchTerm` ändert:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [searchTerm, setSearchTerm] = React.useState(
    localStorage.getItem('search') || 'React'
  );

# leanpub-start-insert
  React.useEffect(() => {
    localStorage.setItem('search', searchTerm);
  }, [searchTerm]);
# leanpub-end-insert

# leanpub-start-insert
  const handleSearch = event => {
    setSearchTerm(event.target.value);
  };
# leanpub-end-insert

  ...
);
~~~~~~~

Reacts `useEffect` Hook verwendet zwei Argumente: Das erste ist eine Funktion, bei der der Seiteneffekt auftritt. In unserem Fall passiert dies, wenn der Benutzer den „searchTerm“ eingibt und dieser daraufhin im lokalen Speicher des Browsers abgelegt wird. Das optionale zweite Argument ist ein Array. Wenn sich eine Variable im Array ändert, löst dies die Funktion für den Seiteneffekt ebenfalls aus. In unserem Fall wird diese jedes Mal aufgerufen, wenn sich das `searchTerm` ändert --- und beim initialen Aufruf, wenn die Komponente zum ersten Mal gerendert wird.

Wenn du das zweite Argument (Array) weglässt, wird die Funktion für den Seiteneffekt bei jedem Rendern der Komponente aufgerufen. Im Gegensatz dazu wird die Funktion für den Seiteneffekt nach dem ersten Rendern der Komponente nur einmal aufgerufen, wenn du ein leeres Array mitgibst. Mit dem Hook nehmen wir Einfluss auf den Komponentenlebenszyklus. Du hast es in der Hand, wann eine Aktualisierung ausgelöst wird --- nur dann, wenn die Komponente zum ersten Mal bereitgestellt wird oder immer, wenn eine abhängige Variable aktualisiert wird.

Durch die Verwendung von Reacts `useEffect` ist die Anwendung robuster. Immer *wenn* und *wo* `searchTerm` über` setSearchTerm` aktualisiert wird, werden `searchTerm` und lokale Storage-Objekte synchronisiert.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Side-Effects).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt]( ).
* Lese mehr zu React's `useEffect` Hooks([0](https://de.reactjs.org/docs/hooks-effect.html), [1](https://de.reactjs.org/docs/hooks-reference.html#useeffect)).
* Verändere die Funktion im ersten Argument des `useEffect Hooks` so, dass mithilfe von `console.log()` Ausgaben von der Konsole deines Browsers mitprotokolliert werden. Experimentiere mit dem Array. Was passiert, wenn du ein leeres oder gar kein Array verwendest?
