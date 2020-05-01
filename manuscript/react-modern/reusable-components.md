## Wiederverwendebare React Komponenten

Schaue dir die Suchkomponente genauer an. Das Label-Element hat den Text "Search: ". Das `id/htmlFor-Attribut` hat die Kennung `search`. Der Wert heißt `search` und der Callback-Handler heißt `onSearch`. Die Komponente ist in hohem Maße mit der Suchfunktion gekoppelt. Das macht sie für den Rest der Anwendung und für nichtsuchbezogene Aufgaben wertlos. Außerdem besteht die Gefahr, dass Fehler auftreten, wenn zwei dieser Suchkomponenten nebeneinander gerendert werden, da die Kombination aus `htmlFor` und `id` dupliziert wird. Im Übrigen wird der Fokus nicht immer korrekt gesetzt, wenn der Benutzer auf eines der Labels klickt.

Da die Suchkomponente keine tatsächliche "Such"-Funktionalität hat, ist sie leicht verallgemeinerbar. In einer allgemeineren Form wäre sie für den Rest der Anwendung verwendbar. Deshalb übergeben wir der Suchkomponente zusätzlich die Eigenschaften (props) `id` und `label`, geben dem Wert und dem Callback-Handler einen allgemeineren Namen und benennen die Komponente um:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      <h1>My Hacker Stories</h1>

# leanpub-start-insert
      <InputWithLabel
        id="search"
        label="Search"
# leanpub-end-insert
        value={searchTerm}
# leanpub-start-insert
        onInputChange={handleSearch}
# leanpub-end-insert
      />

      ...
    </div>
  );
};

# leanpub-start-insert
const InputWithLabel = ({ id, label, value, onInputChange }) => (
# leanpub-end-insert
  <>
# leanpub-start-insert
    <label htmlFor={id}>{label}</label>
    &nbsp;
# leanpub-end-insert
    <input
# leanpub-start-insert
      id={id}
# leanpub-end-insert
      type="text"
      value={value}
# leanpub-start-insert
      onChange={onInputChange}
# leanpub-end-insert
    />
  </>
);
~~~~~~~

Unsere Komponente ist nicht vollständig wiederverwendbar. Falls wir ein Eingabefeld für [Daten](https://developer.mozilla.org/de/docs/Web/HTML/Element/input#Arten_des_%3Cinput%3E-Elements) wie eine E-Mail-Adresse (`email`) oder eine Telefonnummer (`tel`) wünschen, ist es notwendig, dass das Attribut `type` des Eingabefelds von außen zugänglich ist:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({
  id,
  label,
  value,
# leanpub-start-insert
  type = 'text',
# leanpub-end-insert
  onInputChange,
}) => (
  <>
    <label htmlFor={id}>{label}</label>
    &nbsp;
    <input
      id={id}
# leanpub-start-insert
      type={type}
# leanpub-end-insert
      value={value}
      onChange={onInputChange}
    />
  </>
);
~~~~~~~

Von der App-Komponente wird keine `type`-Eigenschaft (props) an die InputWithLabel-Komponente übergeben, sodass dies nicht von außen festgelegt ist. Das Eingabefeld übernimmt den  [Standardparameter](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Functions/Default_parameters) aus der Funktionssignatur.

Mit nur wenigen Änderungen haben wir eine spezialisierte Suchkomponente in eine wiederverwendbarere Komponente verwandelt. Wir haben Namen verallgemeinert und die API erweitert. So werden alle erforderlichen Informationen von außen bereitgestellt. Wir verwenden die Komponente bisher an keiner anderen Stelle. Ab jetzt wäre dies unkompliziert möglich, weil alle notwendigen Voraussetzungen gegeben sind.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Reusable-React-Component).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Fragments...hs/Reusable-React-Component?expand=1).
* Lese mehr zum Thema [Reusable React Components](https://www.robinwieruch.de/react-reusable-components).
* Ist es dir aufgefallen: Bisher haben wir den Text "Search:" mit einem ":" verwendet. Welche Möglichkeiten haben wir jetzt? Würdest du `label="Search:"` als Eigenschaft (props) an die InputWithLabel-Komponente übergeben oder den Doppelpunkt in der InputWithLabel-Komponente mit `<label htmlFor={id}>{label}:</label>` fest codieren? Wir werden diese Frage später im Buch erneut aufgreifen und beantworten.
