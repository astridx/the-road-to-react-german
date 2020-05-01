## React Fragments

Ein Manko in JSX ist es, dass für benachbarte JSX-Elemente ein umschließendes HTML-Tag zum Rendern notwendig ist:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Search = ({ search, onSearch }) => (
# leanpub-start-insert
  <div>
# leanpub-end-insert
    <label htmlFor="search">Search: </label>
    <input
      id="search"
      type="text"
      value={search}
      onChange={onSearch}
    />
# leanpub-start-insert
  </div>
# leanpub-end-insert
);
~~~~~~~

Normalerweise benötigt der von einer React-Komponente zurückgegebene JSX-Code ein umhüllendes Element. Mit Hilfe eines Arrays ist es möglich, mehrere Elemente nebeneinander zu rendern. Wenn wir mit einer Liste von Elementen arbeiten, ist es unumgänglich, dass wir jedem Geschwisterelement einen Schlüssel `key` zuweisen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Search = ({ search, onSearch }) => [
  <label key="1" htmlFor="search">
    Search:{' '}
  </label>,
  <input
    key="2"
    id="search"
    type="text"
    value={search}
    onChange={onSearch}
  />,
];
~~~~~~~

Das vorausgehende Beispiel ist eine Möglichkeit, mehrere Elemente in JSX zu verwenden. Aufgrund des zusätzlichen Schlüsselattributs leidet die Übersichtlichkeit. **React Fragmente** lösen dieses Problem:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Search = ({ search, onSearch }) => (
# leanpub-start-insert
  <>
# leanpub-end-insert
    <label htmlFor="search">Search: </label>
    <input
      id="search"
      type="text"
      value={search}
      onChange={onSearch}
    />
# leanpub-start-insert
  </>
# leanpub-end-insert
);
~~~~~~~

Ein Fragment gruppiert Kindelementen, ohne dem DOM einen zusätzlichen Knoten hinzuzufügen. Beide Suchelemente sind jetzt in deinem Browser mit Eingabefeld und Label sichtbar. Wenn du es bevorzugst, die umschließenden Elemente `<div>` oder `<span>` im resultierenden HTML nicht auszugeben, ersetze sie durch ein leeres Tag. Dies ist in JSX zulässig.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Fragments).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Custom-Hooks...hs/React-Fragments?expand=1).
* Lese mehr zum Thema [React Fragments](https://de.reactjs.org/docs/fragments.html).
