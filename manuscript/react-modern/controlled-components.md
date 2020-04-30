## Kontrollierte Komponenten in React

**Kontrollierte Komponenten** sind nicht zwingen React Komponenten, es handelt sich aber immer um HTML-Elemente. In diesem Abschnitt erfährst du, wie du die Suchkomponente und ihr Eingabefeld in eine kontrollierte Komponente verwandelst.

Lass uns ein Szenario durchdenken, das dir zeigt, warum es sinnvoll ist, dass wir das Konzept der kontrollierten Komponenten in unserer gesamten React-Anwendung einsetzen. Erkennst du den Fehler, nachdem du die folgende Änderung vorgenommen hast, die dem „searchTerm“ einen Anfangszustand verleiht?

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

# leanpub-start-insert
  const [searchTerm, setSearchTerm] = React.useState('React');
# leanpub-end-insert

 ...
};
~~~~~~~

Während die Liste anhand des anfänglich gesetzten Suchstrings gefiltert wurde, zeigt das Eingabefeld den `searchTerm` nicht an. Uns ist wichtig, dass das Eingabefeld jederzeit den `searchTerm` widerspiegelt, der aktuelle für die Anzeige verwendet wird. Nur so ist die Ausgabe korrekt. Aber dieser ist nur durch die gefilterte Liste erkennbar, das Eingabefeld bleibt leer.

Um unser Ziel zu erreichen, verwandeln wir die Suchkomponente mit ihrem Eingabefeld in eine kontrollierte Komponente. Bisher weiß das Eingabefeld nichts über den `searchTerm`. Das Änderungsereignis wird nur verwendet, um uns über eine Änderung zu informieren. Aber: Das Eingabefeld hat ein Attribut namens `value`.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

  const [searchTerm, setSearchTerm] = React.useState('React');

  ...

  return (
    <div>
      <h1>My Hacker Stories</h1>

# leanpub-start-insert
      <Search search={searchTerm} onSearch={handleSearch} />
# leanpub-end-insert

      ...
    </div>
  );
};

const Search = props => (
  <div>
    <label htmlFor="search">Search: </label>
    <input
      id="search"
      type="text"
# leanpub-start-insert
      value={props.search}
# leanpub-end-insert
      onChange={props.onSearch}
    />
  </div>
);
~~~~~~~

Jetzt zeigt das Eingabefeld beim Start den korrekten Anfangswert. Dabei verwendet des `searchTerm` aus dem React-Status. Wenn wir den „searchTerm“ ändern, erzwingen wir außerdem, dass das Eingabefeld den Wert aus dem React-Status verwendet (über props). Zuvor verwaltete das Eingabefeld seinen eigenen internen Status nativ nur mit HTML.

In diesem Abschnitt haben wir kontrollierte Komponenten kennengelernt ein neues Konzept entdeckt, das **unidirektionaler Datenfluss** genannt wird:

{title="Visualization",lang="javascript"}
~~~~~~~
UI -> Side-Effect -> State -> UI -> ...
~~~~~~~

Eine React-Anwendung und ihre Komponenten haben beim ersten Aufruf einen Anfangszustand, der gegebenenfalls als Eigenschaft (props) an andere Komponenten weitergegeben wird. Beim ersten Aufruf wird eine Benutzeroberfläche gerendert. Sobald ein Ereignis wie zum Beispiel eine Benutzereingabe oder das Laden von Daten von einer Remote-API eintritt, wird die Änderung im Status von React erfasst. Wenn dann der Status geändert wurde, werden alle vom veränderten Status oder den implizit geänderten Eigenschaften (props) betroffenen Komponenten erneut gerendert. Dies bedeutet, dass die Komponentenfunktionen nochmals aufgerufen werden. 

A React application and its components start with an initial state, which may be passed down as props to other components. It's rendered for the first time as a UI. Once a side-effect occurs, like user input or data loading from a remote API, the change is captured in React's state. Once state has been changed, all the components affected by the modified state or the implicitly modified props are re-rendered (the component functions runs again).

In den vorherigen Abschnitten haben wir unter anderem den **Komponentenlebenszyklus** von React kennengelernt. Zunächst werden alle Komponenten von oben nach unten in der Komponentenhierarchie erstellt. Dies schließt alle Hooks (zum Beispiel `useState`) ein, die mit ihren Anfangswerten instanziiert werden. Ab dann wartet die Benutzeroberfläche auf Ereignisse wie Benutzerinteraktionen. Sobald der Status geändert wurde – der aktuelle Status wurde beispielsweise über die Statusaktualisierungsfunktion von `useState` verändert –  werden alle von dem geänderten Status oder den geänderten Eigenschaften (props) betroffenen Komponenten erneut gerendert.

Bei jedem Durchlauf der Funktion einer Komponente wird der *aktuelle Wert* (zum Beispiel der aktuelle Status) aus den Hooks entnommen und *nicht* erneut initialisiert. Erscheint dir das seltsam? Du meinst, es wäre intuitiv die Hook-Funktion `useState` mit ihrem Anfangswert erneut zu initialisiert. Aber: Hooks werden nur einmal initialisiert, wenn die Komponente zum ersten Mal gerendert wird. Danach verfolgt React sie intern und hält so die Werte aktuell.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Controlled-Components).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Lifting-State-in-React...hs/React-Controlled-Components?expand=1).
* Lese mehr zum Thema [kontrollierte Komponenten in React](https://www.robinwieruch.de/react-controlled-components/).
* Experimentiere mit `console.log ()` und beobachten wie deine Eingaben gerendert werden – anfangs und nach Änderungen im Eingabefeld. 
