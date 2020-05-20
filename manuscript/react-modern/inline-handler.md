## Inline Handler in JSX

Bei der `stories`-Liste handelt es sich um eine statuslose Variable. Wir filtern diese mit mithilfe der Suchfunktion. Dabei bleibt die Liste selbst erhalten. Wenn wir den Filter entfernen, werden erneut alle Elemente angezeigt. Der Filter bewirkt nur eine vorübergehende Änderung. Bisher manipulieren wir die Liste selbst nicht.

Um die Kontrolle über die Liste zu haben, weisen wir ihr im nächsten Schritt einen Status zu. Hierzu verwenden wir den `useState` Hook. Die zurückgegebenen Werte sind der aktuelle Status (`stories`) und die Statusaktualisierungsfunktion (`setStories`). Wir verwenden den benutzerdefinierten Hook `useSemiPersistentState` nicht, da wir die Liste nur innerhalb eines Aufrufs zwischenspeichern. Bei jedem neuen Aufruf greifen wir auf die initiale Liste zu.

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const initialStories = [
  {
    title: 'React',
    ...
  },
  {
    title: 'Redux',
    ...
  },
];
# leanpub-end-insert

const useSemiPersistentState = (key, initialState) => { ... };

const App = () => {
  const [searchTerm, setSearchTerm] = ...

# leanpub-start-insert
  const [stories, setStories] = React.useState(initialStories);
# leanpub-end-insert

  ...
};
~~~~~~~

Die Anwendung verhält sich zunächst unverändert. Die jetzt von `useState` zurückgegebenen `stories` werden weiterhin in `searchedStories` gefiltert und angezeigt. Als Nächstes bearbeiten wir die Liste --- wir entfernen ein Element:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, setStories] = React.useState(initialStories);

# leanpub-start-insert
  const handleRemoveStory = item => {
    const newStories = stories.filter(
      story => item.objectID !== story.objectID
    );

    setStories(newStories);
  };
# leanpub-end-insert

  ...

  return (
    <div>
      <h1>My Hacker Stories</h1>

      ...

      <hr />

# leanpub-start-insert
      <List list={searchedStories} onRemoveItem={handleRemoveStory} />
# leanpub-end-insert
    </div>
  );
};
~~~~~~~

Der Callback-Handler `handleRemoveStory` in der App-Komponente empfängt ein Element, um es zu entfernen. Außerdem filtert er weiterhin die aktuelle Liste. Die zurückgegebenen `stories` werden dabei als neuer Status festgelegt. Hinzugekommen ist, dass die List-Komponente die Funktion `onRemoveItem` an untergeordnete übergibt --- genutzt wird `onRemoveItem` bisher nicht:

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const List = ({ list, onRemoveItem }) =>
# leanpub-end-insert
  list.map(item => (
    <Item
      key={item.objectID}
      item={item}
# leanpub-start-insert
      onRemoveItem={onRemoveItem}
# leanpub-end-insert
    />
  ));
~~~~~~~

Letztendlich verwenden wir die Funktion `onRemoveItem` in der Item-Komponente. Auf diese Art und Weise wird das `item` über die List-Komponente an den Handler `handleRemoveStory` übergeben. Ausgelöst wird das eigentliche Ereignis über eine Schaltfläche.

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const Item = ({ item, onRemoveItem }) => {
  const handleRemoveItem = () => {
    onRemoveItem(item);
  };
# leanpub-end-insert

  return (
    <div>
      <span>
        <a href={item.url}>{item.title}</a>
      </span>
      <span>{item.author}</span>
      <span>{item.num_comments}</span>
      <span>{item.points}</span>
# leanpub-start-insert
      <span>
        <button type="button" onClick={handleRemoveItem}>
          Dismiss
        </button>
      </span>
# leanpub-end-insert
    </div>
  );
};
~~~~~~~

Eine Alternative wäre es, nur die `objectID` des Elements zu übergeben. Das ist alles, was wir im Callback-Handler der App-Komponente momentan benötigen. Dies wäre aber nicht zukunftssicher. Unter Umständen benötigt der Handler später einmal mehr Informationen, um das zu löschende Item zu löschen. Deshalb übergeben wir schon jetzt das ganze Element und nicht nur die Kennung `objectID`, wenn wir den Handler `onRemoveItem` aufrufen.

Bisher haben wir folgenden umgesetzt: Wir haben die Liste `stories` mithilfe von **Reacts useState Hook** mit einem Status versehen, das Suchwort als Eigenschaft (props) an die List-Komponente weitergegeben, einen Callback-Handler (`handleRemoveStory`) und die Funktion (`handleRemoveItem`) implementierte. Da ein Handler eine Funktion ist und in diesem Fall nichts zurückgibt, entfernen wir der Vollständigkeit halber den Blockkörper und nutzen die knappe Schreibweise.

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => {
# leanpub-start-insert
  const handleRemoveItem = () =>
    onRemoveItem(item);
# leanpub-end-insert

  ...
};
~~~~~~~

Die knappe Schreibweise verbessert den Quellcode an dieser Stelle nicht. Manchmal überarbeite ich Handler in einer Funktionskomponente von einer Pfeilfunktion zurück zur normalen Blockansicht, um die Komponente weiterhin unkompliziert zu bearbeiten:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => {
# leanpub-start-insert
  function handleRemoveItem() {
    onRemoveItem(item);
  }
# leanpub-end-insert

  ...
};
~~~~~~~

In diesem Abschnitt haben wir Eigenschaften (props), Handler, Callback-handler und Status angewendet. Soweit ist nichts Neues hinzugekommen. Jetzt werden wir uns mit **Inline-Handlern** befassen. Mit diesen rufen wir eine Funktion direkt in JSX auf. Es gibt zwei Möglichkeiten, um `onRemoveItem` als Inline-Handler zu verwenden. Sehen wir uns zunächst die JavaScript-Methode `bind()` an:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
  <div>
    <span>
      <a href={item.url}>{item.title}</a>
    </span>
    <span>{item.author}</span>
    <span>{item.num_comments}</span>
    <span>{item.points}</span>
    <span>
# leanpub-start-insert
      <button type="button" onClick={onRemoveItem.bind(null, item)}>
# leanpub-end-insert
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

Die [JavaScript-Methode `bind()`](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_objects/Function/bind) ermöglicht es uns, Argumente direkt an eine Funktion zu binden. Diese Argumente werden angewendet, wenn die Funktion aufgerufen wird. Die bind-Methode gibt eine neue Funktion mit dem angehängten gebundenen Argument zurück.

Die zweite und beliebteste Alternative ist die Verwendung einer umgebenden Pfeilfunktion. Mit dieser ist es möglich, Argumente wie `item` zu übergeben:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
  <div>
    <span>
      <a href={item.url}>{item.title}</a>
    </span>
    <span>{item.author}</span>
    <span>{item.num_comments}</span>
    <span>{item.points}</span>
    <span>
# leanpub-start-insert
      <button type="button" onClick={() => onRemoveItem(item)}>
# leanpub-end-insert
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

Nachfolgend zeige ich dir eine vermeintlich schnelle Lösung. Es kommt vor, dass wir den knappen Funktionskörper einer Funktionskomponente nicht in einen Blockkörper umgestalten, um einen Handler zwischen Funktionssignatur und return-Anweisung einzufügen. Diese Methode ist knapper. Das ist ein Vorteil. Nachteilig ist, dass sie schwieriger zu debuggen ist, denn die JavaScript-Logik ist unter Umständen in JSX verborgen. Dies wird in vollem Umfang deutlich, wenn die umgebende Pfeilfunktion mehr als eine Zeile kapselt, indem ein Blockkörper anstelle eines knappen Körpers verwendet wird. Vermeide dies:

{title="Code Playground",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
  <div>
    ...
    <span>
      <button
        type="button"
        onClick={() => {
          // Erledige eine Aufgabe

          // Hinweis: Vermeide die Verwendung komplexer Logik innerhalb von JSX

          onRemoveItem(item);
        }}
      >
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

Alle drei Handler-Versionen sind geeignete Lösungen. Der normale Handler nutzt für die Implementierungsdetails den Blockkörper der Funktionskomponente. Die beiden Inline-Handler verwenden JSX.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Inline-Handler-in-JSX).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Imperative-React...hs/Inline-Handler-in-JSX?expand=1).
* Halte eine Rückschau auf Handler, Callback-Handler und Inline-Handler.
