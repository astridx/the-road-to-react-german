## Eigenschaften (Props) in React

Wir verwenden derzeit die Variable `list` als globale Variable in der aktuellen Beispiel-Anwendung. Diese haben wir direkt aus dem globalen Bereich in der App-Komponente und erneut in der List-Komponente verwendet. Dies funktioniert, wenn du nur eine Variable nutzt. Wenn zahlreiche Variablen über mehrere Komponenten genutzt werden, wirst du an Grenzen stoßen. Eine Lösungsmöglichkeit ist die Verwendung von Eigenschaften (Props).

Mithilfe von Eigenschaften übergeben wir Variablen als Informationen von einer Komponente an eine andere. Damit alles seine Ordnung hat, verschieben wir im Beispiel vorher die Liste aus dem globalen Bereich in die App-Komponente und geben ihr einen passenden Namen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
# leanpub-start-insert
  const stories = [
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

  const handleChange = event => { ... };

  return ( ... );
};
~~~~~~~

Jetzt verwenden wir **React Eigenschaften**, um das Array an die List-Komponente zu übergeben:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

  const handleChange = event => { ... };

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" onChange={handleChange} />

      <hr />

# leanpub-start-insert
      <List list={stories} />
# leanpub-end-insert
    </div>
  );
};
~~~~~~~

Die Variable heißt in der App-Komponente `stories` und wird unter diesem Namen an die List-Komponente übergeben. In der Instanziierung der List-Komponente wird sie dem Attribut `list` zugewiesen. Wir greifen als `list` über das `props`-Objekt in der Funktionssignatur der Listenkomponente darauf zu:

The variable is called `stories` in the App component, and we pass it under this name to the List component. In the List component's instantiation, however, it is assigned to the `list` attribute. We access it as `list` from the `props` object in the List component's function signature:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const List = props =>
  props.list.map(item => (
# leanpub-end-insert
    <div key={item.objectID}>
      <span>
        <a href={item.url}>{item.title}</a>
      </span>
      <span>{item.author}</span>
      <span>{item.num_comments}</span>
      <span>{item.points}</span>
    </div>
  ));
~~~~~~~

So haben wir verhindert, dass die Variable list/stories den globalen Bereich in der App-Komponente nutzt. Dies ist nicht notwendig, da `stories` nicht direkt in der App verwendet wird. Sie wird in der untergeordneten Listen-Komponenten verwendet. Deshalb übergeben wir die Variable an diese. Dort greifen wir dann über das Argument der ersten Funktionssignatur namens `props` darauf zu.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Props).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Handler-Function-in-JSX...hs/React-Props?expand=1).
* Lese mehr zum Thema: [Eigenschaften in React an Komponenten übergeben](https://www.robinwieruch.de/react-pass-props-to-component).