## Callback-Handler in JSX

Als Nächstes konzentrieren wir uns auf das Eingabefeld und das Label, indem wir eine eigenständige Komponente für die Suche erstellen. Im Komponentenbaum wird die Suchkomponente zu einem Blatt auf der gleichen Ebene, quasi einem Geschwisterblatt der Listenkomponente – und umgekehrt. Außerdem verschieben wir den Handler und den Status in die Suchkomponente, um die Funktionalität aufrechtzuerhalten.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

  return (
    <div>
      <h1>My Hacker Stories</h1>

# leanpub-start-insert
      <Search />
# leanpub-end-insert

      <hr />

      <List list={stories} />
    </div>
  );
};

# leanpub-start-insert
const Search = () => {
  const [searchTerm, setSearchTerm] = React.useState('');

  const handleChange = event => {
    setSearchTerm(event.target.value);
  };

  return (
    <div>
      <label htmlFor="search">Search: </label>
      <input id="search" type="text" onChange={handleChange} />

      <p>
        Searching for <strong>{searchTerm}</strong>.
      </p>
    </div>
  );
};
# leanpub-end-insert
~~~~~~~

Wir haben eine für sich abgeschlossene Suchkomponente, die den Status verwaltet. Die Komponente zeigt das `searchTerm` als Text an, teilt diese Informationen aber nicht mit übergeordneten oder gleichrangigen. Da die Suchkomponente nur den Suchbegriff anzeigt, wird sie für die anderen Komponenten unbrauchbar. Es sei denn, es gäbe doch eine Möglichkeit Informationen bei ihr anzufragen.

Auf den ersten Blick gibt es keine Möglichkeit, Informationen an übergeordnete oder gleichrangige Komponenten zu übergeben, da Eigenschaften (Props) ausschließlich nach unten weitergegeben werden. Deshalb schauen wir ein zweites Mal und führen den **Rückruf-Handler** als Funktion ein: Dies geschieht wie folgt: Eine Rückruffunktion wird eingeführt (A), diese wird an einer anderen Stelle verwendet (B) und gibt das Ergebnis an den Ort zurück, an dem sie eingeführt wurde (C).

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  const stories = [ ... ];

# leanpub-start-insert
  // A
  const handleSearch = event => {
    // C
    console.log(event.target.value);
  };
# leanpub-end-insert

  return (
    <div>
      <h1>My Hacker Stories</h1>

# leanpub-start-insert
      <Search onSearch={handleSearch} />
# leanpub-end-insert

      <hr />

      <List list={stories} />
    </div>
  );
};

# leanpub-start-insert
const Search = props => {
# leanpub-end-insert
  const [searchTerm, setSearchTerm] = React.useState('');

  const handleChange = event => {
    setSearchTerm(event.target.value);

# leanpub-start-insert
    // B
    props.onSearch(event);
# leanpub-end-insert
  };

  return ( ... );
};
~~~~~~~

Schauen wir uns das Konzept des Callback-Handlers Schritt für Schritt an: Wir übergeben eine Funktion von einer Komponente (App) an eine andere Komponente (Search). Wir nennen diese in der zweiten Komponente `onSearch`. Die eigentliche Implementierung des Funktionsaufrufs ist in der ersten Komponente (App) in der Funktion `handleSearch`. Auf diese Weise kommunizieren wir im Komponentenbaum. Eine in einer Komponente verwendete Handler-Funktion wird zu einem Callback-Handler, der über React-Props an andere Komponenten weitergegeben wird. Callback-Handler werden als Funktionen in den Eigenschaften (Props) übergeben und zur Kommunikation in der Komponentenhierarchie verwendet.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Callback-Handler-in-JSX).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-State...hs/Callback-Handler-in-JSX?expand=1).
* Wiederhole und durchdenke die Konzepte von Handler und Callback-Handler so oft wie nötig.
