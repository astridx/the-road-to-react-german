## Den Status in der Hierarchie nach oben verschieben

Die Suchkomponente in der Beispiel-Anwendung verwaltet bisher ihren internen Status. Wir haben im letzten Abschnitt einen Callback-Handler eingerichtet, um Informationen an die App-Komponente weiterzuleiten, verwenden diesen bisher noch nicht für die Filterung der Liste. Hierfür ist es wichtig, herauszufinden, wie der Status der Suchkomponente für mehrere Komponenten freizugänglich ist.

Der Suchbegriff wird in der App benötigt, um die Liste zu filtern, bevor diese als Eigenschaft (props) an die List-Komponente übergeben wird. Damit die App-Komponente auf den Status – und folglich auf die gefilterte Liste – Zugriff hat, ist es erforderlich, diesen von der Suchkomponente zur App-Komponente zu verschieben, ergo: **diesen in der Hierarchie nach oben rücken**.

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

Wir haben uns im vorherigen Abschnitt die Rückruffunktion angesehen. Diese unterstützt uns dabei, eine Verbindung von der Such- zur App-Komponente zu erstellen. Jetzt verwaltet die Suchkomponente den Status nicht mehr. Diese Aufgabe hat die App-Komponente übernommen. Nachdem Text in das Eingabefeld eingegeben wurde, gibt die Suchkomponente diese Information an die App-Komponente weiter. Es ist weiterhin möglich den `searchTerm` in der App-Komponente oder der Suchkomponente anzuzeigen, indem du ihn als Eigenschaft (props) weitergibst.

Verwalte den Status immer mithilfe einer Komponente, die entweder selbst am Inhalt interessiert ist oder der alle anderen, die den Status verwenden, in der Hierarchie folgen. Im ersten Fall liest die Komponente den Status direkt aus, im zweiten Fall holt sie alle Informationen aus den Eigenschaften (props) die übergeben werden. Damit es möglich ist, dass eine untergeordnete Komponente den Status aktualisiert, übergibst du einen Rückruf-Handler an sie (siehe Suchkomponente). Wenn diese den Status nicht ändert, sondern ausschließlich verwendet, zum Beispiel für die Anzeige am Bildschirm, reicht die Übergabe als Eigenschaft (Props) aus.

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

Hier verwenden wir die [integrierte Filterfunktion des JavaScript-Arrays](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Array/filter), um eine gefilterte Liste zu erstellen. Die Filterfunktion verwendet eine Funktion als Argument, die auf jedes Element im Array zugreift und `true` oder `false` zurückgibt. Wenn diese `true` erwidert, was bedeutet, dass die Bedingung erfüllt ist, bleibt das Element im neu erstellten Array. Antwortet sie mit `false`, wird es entfernt:

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

Die Filterfunktion prüft, ob `searchTerm` im Titel unseres Story-Elements vorkommt. Dabei ist Groß- und Kleinschreibung eine Besonderheit. Wenn wir nach „react“ suchen, bleibt die Ergebnisliste leer. Um dieses Problem zu beheben, konvertieren wir den Titel der Geschichte und den `searchTerm` in Kleinbuchstaben. So ist die Schreibweise egal, für die Suche gibt es ausschließlich kleine Buchstaben.

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

Jetzt ist es möglich, nach „eact“, „React“ oder „react“ zu suchen und eine von zwei angezeigten Elementen zu sehen. Du hast deiner Anwendung eine interaktive Funktion hinzugefügt.

Der verbleibende Abschnitt enthält nichts Neues. Er beinhaltet der Überarbeitung des aktuellen Codes. Wir werden am Ende die überarbeitete Version verwenden, daher ist es sinnvoll, diese Schritte nachzuvollziehen und sie zu verstehen. Zunächst präzisieren wir die Funktion mithilfe einer JavaScript-Pfeilfunktion:

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

Darüber hinaus könnten wir die return-Anweisung in eine sofortige Rückgabe umwandeln, da vorher keine andere Aufgabe anfällt:

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

So weit zur Überarbeitung der Filterfunktion. Es gibt viele unterschiedliche Herangehensweisen – und es ist nicht immer trivial zu entscheiden, wie das Gleichgewicht zwischen Übersichtlichkeit und Genauigkeit am besten ist. Ich tendiere dazu, den Code möglichst kurz zu belassen. Meiner Meinung nach bleibt er so ebenfalls lesbar.

Jetzt verwalten wir den Status über den Callback-Handler der Suchkomponente in der App-Komponente. Der aktuelle Status wird als Filter für die Liste verwendet. Mit dem Callback-Handler verwenden wir Informationen aus der Suchkomponente in der App-Komponente, um den gemeinsam genutzten Status zu verändern. Indirekt nutzen wir die Informationen in der Listenkomponente, um die gefilterte Liste auf dem neuesten Stand zu halten.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Lifting-State-in-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Callback-Handler-in-JSX...hs/Lifting-State-in-React?expand=1).