## Den Status in React in der Hierarchie nach oben verschieben

Die Suchkomponente hat ihren internen Status. Wir haben zwar einen Callback-Handler eingerichtet, um Informationen an die App-Komponente weiterzuleiten, verwenden diesen aktuell jedoch nicht. Zunächst ist es wichtig, herauszufinden, wie der Status der Suchkomponente für mehrere Komponenten freigegeben wird.

Der Suchbegriff wird in der App benötigt, um die Liste zu filtern, bevor sie als Eigenschaft (Props) an die List-Komponente übergeben wird. Es ist erforderlich, den Status von der Suchkomponente zur App-Komponente zu verlegen, ergo: **in der Hierarchie nach oben zu setzen**, um den Status für weitere Komponenten freizugeben.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

# leanpub-start-insert
  const [searchTerm, setSearchTerm] = React.useState('');
# leanpub-end-insert

  const handleSearch = event => {
# leanpub-start-insert
    setSearchTerm(event.target.value);
# leanpub-end-insert
  };

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <Search onSearch={handleSearch} />

      <hr />

      <List list={stories} />
    </div>
  );
};

const Search = props => (
  <div>
    <label htmlFor="search">Search: </label>
# leanpub-start-insert
    <input id="search" type="text" onChange={props.onSearch} />
# leanpub-end-insert
  </div>
);
~~~~~~~

Wir haben uns im vorherigen Abschnitt die Rückruffunktion angesehen. Diese unterstützt uns dabei, einen offenen Kommunikationskanal von der Such- zur App-Komponente aufrechtzuerhalten. Die Suchkomponente verwaltet den Status nicht mehr, sondern leitet das Ereignis erst an die App-Komponente weiter, nachdem Text in das Eingabefeld eingegeben wurde. Mithilfe einer Rückruffunktion ist es dir außerdem möglich den `searchTerm` zusätzlich in der App-Komponente oder der Suchkomponente anzuzeigen, indem du ihn als Eigenschaft (Prop) weitergibst.

Always manage the state at a component where every component that's interested in it is one that either manages the state (using information directly from state) or a component below the managing component (using information from props). If a component below needs to update the state, pass a callback handler down to it (see Search component). If a component needs to use the state (e.g. displaying it), pass it down as props.

Mithilfe des Suchfunktionsstatus in der App-Komponente filtern wir die Liste mit dem statusbehafteten `searchTerm`, bevor wir `list` an die Listenkomponente übergeben:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

  const [searchTerm, setSearchTerm] = React.useState('');

  const handleSearch = event => {
    setSearchTerm(event.target.value);
  };

# leanpub-start-insert
  const searchedStories = stories.filter(function(story) {
    return story.title.includes(searchTerm);
  });
# leanpub-end-insert

  return (
    <div>
      <h1>My Hacker Stories</h1>

      <Search onSearch={handleSearch} />

      <hr />

# leanpub-start-insert
      <List list={searchedStories} />
# leanpub-end-insert
    </div>
  );
};
~~~~~~~

Here, the [JavaScript array's built-in filter function](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) is used to create a new filtered array. The filter function takes a function as an argument, which accesses each item in the array and returns true or false. If the function returns true, meaning the condition is met, the item stays in the newly created array; if the function returns false, it's removed:

{title="Code Playground",lang="javascript"}
~~~~~~~
const words = [
  'spray',
  'limit',
  'elite',
  'exuberant',
  'destruction',
  'present'
];

const filteredWords = words.filter(function(word) {
  return word.length > 6;
});

console.log(filteredWords);
// ["exuberant", "destruction", "present"]
~~~~~~~

The filter function checks whether the `searchTerm` is present in our story item's title, but it's still too opinionated about the letter case. If we search for "react", there is no filtered "React" story in your rendered list. To fix this problem, we have to lower case the story's title and the `searchTerm`.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const searchedStories = stories.filter(function(story) {
# leanpub-start-insert
    return story.title
      .toLowerCase()
      .includes(searchTerm.toLowerCase());
# leanpub-end-insert
  });

  ...
};
~~~~~~~

Now you should be able to search for "eact", "React", or "react" and see one of two displayed stories. You have just added an interactive feature to your application.

The remaining section shows a couple of refactoring steps. We will be using the final refactored version in the end, so it makes sense to understand these steps and keep them. As learned before, we can make the function more concise using a JavaScript arrow function:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const searchedStories = stories.filter(story => {
# leanpub-end-insert
    return story.title
      .toLowerCase()
      .includes(searchTerm.toLowerCase());
 });

  ...
};
~~~~~~~

In addition, we could turn the return statement into an immediate return, because no other task (business logic) happens before the return:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const searchedStories = stories.filter(story =>
    story.title.toLowerCase().includes(searchTerm.toLowerCase())
  );
# leanpub-end-insert

  ...
};
~~~~~~~

So weit zur Überarbeitung der Filterfunktion. Es gibt viele unterschiedliche Herangehensweisen – und es ist nicht immer trivial zu entscheiden, wie das Gleichgewicht zwischen Übersichtlichkeit und Genauigkeit am besten umzusetzen ist. Ich tendiere dazu, den Code möglichst kurz zu belassen. Meiner Meinung nach bleibt er so ebenfalls lesbar.

Now we can manipulate state in React, using the Search component's callback handler in the App component to update it. The current state is used as a filter for the list. With the callback handler, we used information from the Search component in the App component to update the shared state and indirectly in the List component for the filtered list.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Lifting-State-in-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Callback-Handler-in-JSX...hs/Lifting-State-in-React?expand=1).