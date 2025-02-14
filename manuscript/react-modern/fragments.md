## Fragmente in React

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

Normalerweise benötigt der von einer React-Komponente zurückgegebene JSX-Code ein umhüllendes Element. Mit Hilfe eines Arrays ist es möglich, mehrere Elemente nebeneinander zu rendern. Wenn wir mit einer Liste arbeiten, ist es unumgänglich, dass wir jedem Geschwisterelement einen Schlüssel `key` zuweisen. Ich zeige dir den Code exemplarisch. Wenn du ihn ausprobierst, wirst du auf den ersten Blick keinen Unterschied feststellen. Genauso wie bei einer [Liste](https://www.robinwieruch.de/react-list-component) ist es möglich, `key` wegzulassen. Sobald du ein Element entfernst oder hinzufügst, oder die Reihenfolge dynamisch änderst, wird du dann aber auf Problemen stoßen. Deshalb verwende `key` wie im nachfolgenden Code:

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

Das vorausgehende Beispiel ist eine Möglichkeit, mehrere Elemente in JSX zu verwenden. Aufgrund des zusätzlichen Schlüsselattributs `key` leidet die Übersichtlichkeit. **Fragmente** lösen dieses Problem:

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

Ein Fragment gruppiert Kind-Elementen, ohne dem DOM einen zusätzlichen Knoten hinzuzufügen. Label und Eingabefeld sind jetzt in deinem Browser sichtbar. Wenn du es bevorzugst, die umschließenden Elemente `<div>` oder `<span>` im resultierenden HTML nicht auszugeben, ersetze sie im Code mit einem leeres Tag `<> ... </>`. Dies ist in JSX zulässig.

### Übungen:

* Begutachte den [Quellcode dieses Abschnitts](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Fragments).
  * Reflektiere die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Custom-Hooks...hs/React-Fragments?expand=1).
* Lese mehr zum Thema [Fragmente in React](https://de.reactjs.org/docs/fragments.html).
