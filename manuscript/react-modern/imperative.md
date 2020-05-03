## Imperative React

React ist von Natur aus deklarativ. Das gilt für JSX genauso wie für die Hooks. In JSX teilen wir React mit, *was* gerendert wird und nicht *wie* es gerendert wird. Mit einem React-Seiteneffekts (useEffect) drücken wir aus, *was* statt *wie* etwas erreicht wird. Es gibt Szenarien, in denen wir gerne auf imperative Art und Weise auf ein Element zugreifen. zum Beispiel:

* Lese- / Schreibzugriff auf Elemente über die DOM-API:
  * Messen (Lesen) der Breite oder Höhe eines Elements
  * Einstellen (Schreiben) des Fokusstatus eines Eingabefelds
* Implementierung komplexerer Animationen:
  * Übergänge einstellen.
  * Übergängen aufeinander abstimmen.
* Integration von Bibliotheken von Drittanbietern:
  * [D3] (https://d3js.org/) ist eine beliebte imperative Bibliothek zur Bearbeitung von Diagrammen.

Die imperative Programmierung in React ist ausführlich und nicht immer intuitiv. In diesem Abschnitt durchdenken wir ein kleines Beispiel. Ziel ist es, den Fokus eines Eingabefelds zu setzen. Bei der deklarativen Methode reicht es auf den ersten Blick aus, das Autofokus-Attribut des Eingabefelds festzulegen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => (
  <>
    <label htmlFor={id}>{children}</label>
    &nbsp;
    <input
      id={id}
      type={type}
      value={value}
# leanpub-start-insert
      autoFocus
# leanpub-end-insert
      onChange={onInputChange}
    />
  </>
);
~~~~~~~

Auf den zweiten Blick funktioniert dies nur, wenn eine Komponente einmal gerendert wird. Wenn die App-Komponente aber beispielsweise zwei InputWithLabel-Komponenten rendert, erhält die zuletzt gerenderte Komponente den Autofokus. Da es sich um eine wiederverwendbare React-Komponente handelt, übergeben wir eine Eigenschaft (props) und lassen den Entwickler entscheiden, ob sein Eingabefeld den Autofokus erhält oder nicht:

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
# leanpub-start-insert
        isFocused
# leanpub-end-insert
        onInputChange={handleSearch}
      >
        <strong>Search:</strong>
      </InputWithLabel>

      ...
    </div>
  );
};
~~~~~~~

`isFocused` als Attribut entspricht `isFocused={true}`. Verwende innerhalb der Komponente die neue Eigenschaft (props)  anstelle von `autoFocus` für das Eingabefeld:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
  id,
  value,
  type = 'text',
  onInputChange,
# leanpub-start-insert
  isFocused,
# leanpub-end-insert
  children,
}) => (
  <>
    <label htmlFor={id}>{children}</label>
    &nbsp;
    <input
      id={id}
      type={type}
      value={value}
# leanpub-start-insert
      autoFocus={isFocused}
# leanpub-end-insert
      onChange={onInputChange}
    />
  </>
);
~~~~~~~

Die Funktion erfüllt ihren Zweck, es handelt sich noch immer um eine deklarative Implementierung. Wir teilen React mit, *was* zu erledigen ist und nicht *wie* es zu erledigen ist. Auch wenn dies mit dem deklarativen Ansatz möglich ist, gestalten wir dieses Szenario in eine imperative Version um. Denn: Unser Ziel ist es, die Methode `focus()` programmgesteuert über die DOM-API des Eingabefelds auszuführen, sobald dieses gerendert ist:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
  id,
  value,
  type = 'text',
  onInputChange,
  isFocused,
  children,
}) => {
# leanpub-start-insert
  // A
  const inputRef = React.useRef();
# leanpub-end-insert

# leanpub-start-insert
  // C
  React.useEffect(() => {
    if (isFocused && inputRef.current) {
      // D
      inputRef.current.focus();
    }
  }, [isFocused]);
# leanpub-end-insert

  return (
    <>
      <label htmlFor={id}>{children}</label>
      &nbsp;
# leanpub-start-insert
       {/* B */}
# leanpub-end-insert
      <input
# leanpub-start-insert
        ref={inputRef}
# leanpub-end-insert
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
      />
    </>
  );
};
~~~~~~~

Alle wesentlichen Punkte habe ich mit Kommentaren im obigen Codebeispiel gekennzeichnet und nachfolgend Schritt für Schritt erklärt:

* (A) Erstelle zuerst ein `ref`-Objekt mit Hilfe von **Reacts useRef Hook**. Dieses `ref`-Objekt ist ein persistenter Wert, der über die Lebensdauer einer React-Komponente hinaus erhalten bleibt. Es kommt mit einer Eigenschaft namens `current`, die im Gegensatz zum `ref`-Objekt änderbar ist.
* (B) Dann wird das `ref`-Objekt an das JSX-reservierte `ref`-Attribut des Eingabefelds übergeben und die Elementinstanz wird der veränderbaren `current`-Eigenschaft zugewiesen.
* (C) Du hast jetzt die Option**Reacts useRef Hook** für den Lebenszyklus zu nutzen. Setzte den Fokus auf das Eingabefeld, wenn die Komponente gerendert wird (oder wenn sich ihre Abhängigkeiten ändern).
* (D) Da das `ref`-Objekt an das `ref`-Attribut des Eingabefelds übergeben wird, ermöglicht die `current`-Eigenschaft den Zugriff auf das Element. Setzte den Fokus programmgesteuert als Seiteneffekt, wenn `isFocused` festgelegt ist und die Eigenschaft `current` vorhanden ist.

Dies war ein Beispiel für den Wechsel von der deklarativen zur imperativen Programmierung in React. Es ist nicht immer möglich, den deklarativen Weg zu nutzen. Wenn der Fall eintritt, ist der imperative Ansatz unter Umständen eine gangbare Alternative. Dieser Abschnitt dient dazu, die DOM-API in React kennenzulernen.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Imperative-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Component-Composition...hs/Imperative-React?expand=1).
* Lese mehr über [Reacts useRef Hook](https://de.reactjs.org/docs/hooks-reference.html#useref).