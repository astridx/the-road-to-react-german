## Lists in React

Bisher haben wir primitive Variablen in JSX gerendert. Als Nächstes rendern wir eine Liste, die aus mehreren Elementen besteht. Wir werden zuerst mit Beispieldaten experimentieren und diese dann anwenden, um Daten von einer Remote-API abzurufen. Definieren wir zunächst die Liste als Array. Ich hatte es erwähnt: Es ist möglich, eine Variable außerhalb oder innerhalb einer Komponente zu definieren. Das folgende Beispiel zeigt dir die externe Definition:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';

# leanpub-start-insert
const list = [
  {
    title: 'React',
    url: 'https://reactjs.org/',
    author: 'Jordan Walke',
    num_comments: 3,
    points: 4,
    objectID: 0,
  },
  {
    title: 'Redux',
    url: 'https://redux.js.org/',
    author: 'Dan Abramov, Andrew Clark',
    num_comments: 2,
    points: 5,
    objectID: 1,
  },
];
# leanpub-end-insert

function App() { ... }

export default App;
~~~~~~~

Im Beispiel verwendete ich drei Punkte `...` als Platzhalter, um das Code-Schnipsel übersichtlich zu halten, und mich auf den wesentlichen Teil zu konzentrieren: die „list“-Variable außerhalb der App-Komponente. Ich werde die drei Punkte `...` im ganzen Buch als Platzhalter für Codeblöcke verwenden, die ich in früheren Übungen erstellt habe. Wenn dir an einer Stelle nicht klar ist, was anstelle der Punkte steht, überprüfe deinen Code bitte mithilfe des CodeSandbox-Links, den ich am Ende eines jeden Kapitels mit Code-Beispielen angegeben habe.

Jedes Element in der Liste hat einen Titel, eine URL, einen Autor, eine Kennung (`objectID`), Punkte (die die Beliebtheit anzeigen) und eine Anzahl von Kommentaren. Als Nächstes rendern wir die Liste in JSX dynamisch:

{title="src/App.js",lang="javascript"}
~~~~~~~
function App() {
  return (
    <div>
# leanpub-start-insert
      <h1>My Hacker Stories</h1>
# leanpub-end-insert

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />

# leanpub-start-insert
      <hr />
# leanpub-end-insert

# leanpub-start-insert
      {/* render the list here */}
# leanpub-end-insert
    </div>
  );
}
~~~~~~~

Verwende [die JavaScript-Zuordnungsmethode `map()` für Arrays](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Array/map), um über jedes Element der Liste zu iterieren und so eine neue Liste zurückzugeben:

{title="Code Playground",lang="javascript"}
~~~~~~~
const numbers = [1, 4, 9, 16];

const newNumbers = numbers.map(function(number) {
  return number * 2;
});

console.log(newNumbers);
// [2, 8, 18, 32]
~~~~~~~

Im vorherigen Beispiel haben wir eine JavaScript-Variable auf eine andere abgebildet. Wir gehen nun einen Schritt weiter. Wir geben ein JSX-Fragment zurück, das jedes Element der Liste rendert:

{title="src/App.js",lang="javascript"}
~~~~~~~
function App() {
  return (
    <div>
      ...

      <hr />

# leanpub-start-insert
      {list.map(function(item) {
        return <div>{item.title}</div>;
      })}
# leanpub-end-insert
    </div>
  );
}
~~~~~~~

Einer meiner ersten React „Aha“-Momente war es, einer Liste von JavaScript-Objekten HTML-Elementen ohne zuzuordnen, zusätzliche HTML-Template-Syntax. Erforderlich war ausschließlich JavaScript in HTML.

React zeigt jetzt jedes Element an. Verbessere die Ausgabe weiter, so dass React erweiterte dynamische Listen effizienter handhabt. Durch Zuweisen eines Schlüsselattributs zu jedem Listenelement identifiziert React einzelne geänderte Elemente, wenn sich die Liste ändert – zum Beispiel bei einer Änderung der Reihenfolge. Glücklicherweise verfügen unsere Listenelemente über eine ID:

{title="src/App.js",lang="javascript"}
~~~~~~~
function App() {
  return (
    <div>
      ...

      <hr />

      {list.map(function(item) {
# leanpub-start-insert
        return (
          <div key={item.objectID}>
# leanpub-end-insert
            {item.title}
# leanpub-start-insert
          </div>
        );
# leanpub-end-insert
      })}
    </div>
  );
}
~~~~~~~

Vermeide es, den Index des Elements im Array zu verwenden. So stellst du sicher, dass die ID eine stabile Kennung ist. React identifiziert die Elemente andernfalls nicht korrekt, wenn die Liste beispielsweise ihre Reihenfolge ändert:

{title="Code Playground",lang="javascript"}
~~~~~~~
// Bitte nicht so.
{list.map(function(item, index) {
  return (
    <div key={index}>
      ...
    </div>
  );
})}
~~~~~~~

Bisher wird für jeden Artikel nur der Titel angezeigt. Experimentiere mit der Anzeige weiterer Eigenschaften des Elements:

{title="src/App.js",lang="javascript"}
~~~~~~~
function App() {
  return (
    <div>
      ...

      <hr />

# leanpub-start-insert
      {list.map(function(item) {
        return (
          <div key={item.objectID}>
            <span>
              <a href={item.url}>{item.title}</a>
            </span>
            <span>{item.author}</span>
            <span>{item.num_comments}</span>
            <span>{item.points}</span>
          </div>
        );
      })}
# leanpub-end-insert
    </div>
  );
}
~~~~~~~

Die Funktion `map` ist Bestandteil von JSX. Innerhalb dieser Funktion haben wir Zugriff auf jedes Element und seine Eigenschaften. Die Eigenschaft `url` jedes Elements wird als dynamisches Attribut `href` für das Ankertag verwendet. JavaScript in JSX kann nicht nur zum Anzeigen von Elementen verwendet werden, sondern auch zum dynamischen Zuweisen von HTML-Attributen.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Lists-in-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-JSX...hs/Lists-in-React?expand=1).
* Informiere dich warum das Schlüsselattribut (ID) in React benötigt wird ([0](https://dev.to/jtonzing/the-significance-of-react-keys---a-visual-explanation--56l7), [1](https://www.robinwieruch.de/react-list-key), [2](https://reactjs.org/docs/lists-and-keys.html)). Es ist nicht tragisch, wenn du die Implementierung nicht vollständig verstehst. Konzentrieren dich in diesem Fall auf die Anwendung in dynamischen Listen.
* Wiederhole die [standardmäßig integrierten Array-Methoden](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/) - insbesondere *map*, *filter*, und *reduce* --- die in nativem JavaScript verfügbar sind.
* Was passiert, wenn du `null` anstelle von JSX zurückgibst?
* Erweitere die Liste um einige weitere Elemente, um das Beispiel realistischer zu gestalten.
* Verwende zur Übung verschiedene JavaScript-Ausdrücke in JSX.
