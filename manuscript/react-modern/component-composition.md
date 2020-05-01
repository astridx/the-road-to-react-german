## Komponenten Komposition in React

In diesem Abschnitt lernst du, wie du ein React-Element auf dieselbe Weise wie ein HTML-Element mit einem öffnenden und schließenden Tag verwendest:

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
        onInputChange={handleSearch}
# leanpub-start-insert
      >
        Search
      </InputWithLabel>
# leanpub-end-insert

      ...
    </div>
  );
};
~~~~~~~

Im obigen Beispiel fügen wir den Text „Search:“ jetzt direkt zwischen die Tags der Komponente ein. In der InputWithLabel-Komponente hast du inzwischen über die Eigenschaft **children** Zugriff auf diese Information. Verwende anstelle von `label` die Eigenschaft `children`, um alles, was von oben weitergegeben wird, an der gewünschten Stelle anzuzeigen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
  id,
  value,
  type = 'text',
  onInputChange,
# leanpub-start-insert
  children,
# leanpub-end-insert
}) => (
  <>
# leanpub-start-insert
    <label htmlFor={id}>{children}</label>
# leanpub-end-insert
    &nbsp;
    <input
      id={id}
      type={type}
      value={value}
      onChange={onInputChange}
    />
  </>
);
~~~~~~~

Jetzt verhalten sich die Elemente der React-Komponente ähnlich wie natives HTML. Alles, was innerhalb der Komponente eingefügt ist, ist als Kind `children` verwendbar und somit renderbar. Du wirst sehen, du freust dich bei der Verwendung einer React-Komponente oft über Einflussmöglichkeiten auf das, was in ihr passiert:

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
        onInputChange={handleSearch}
      >
# leanpub-start-insert
        <strong>Search:</strong>
# leanpub-end-insert
      </InputWithLabel>

      ...
    </div>
  );
};
~~~~~~~

Mit diesem Feature ist es möglich React-Komponenten je nach Wunsch zusammenzustellen. Im Beispiel nutzen wir einen JavaScript-String und einen String, der in ein HTML-Element `<strong>` eingeschlossen ist. Dies ist nur ein kleiner Ausblick auf das Machbare. Es ist möglich ganze Komponenten mithilfe von React-`children` zu übergeben.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Component-Composition).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Reusable-React-Component...hs/React-Component-Composition?expand=1).
* Lese mehr über die Komposition von Komponenten in React ([0](https://www.robinwieruch.de/react-component-composition), [1](https://de.reactjs.org/docs/composition-vs-inheritance.html)).
* Erstelle eine Textkomponente, die einen String rendert und übergib diese mittels `children` an die InputWithLabel-Komponente.
