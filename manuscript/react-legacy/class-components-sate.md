## Legacy-React Klassenkomponenten: Status

Vor der Einführung der React Hooks waren die Klassenkomponenten den Funktionskomponenten überlegen, da nur sie den Status verwalteten. Mit einem Klassenkonstruktor wird der Anfangszustand festgelegt. Außerdem gibt die Instanz der Komponente (`this`) jederzeit Zugriff auf den aktuellen Status (`this.state`) und die Statusaktualisierungsmethode der Komponente (`this.setState`):

{title="Code Playground",lang="javascript"}
~~~~~~~
class App extends React.Component {
  constructor(props) {
    super(props);

# leanpub-start-insert
    this.state = {
      searchTerm: 'React',
    };
# leanpub-end-insert
  }

  render() {
# leanpub-start-insert
    const { searchTerm } = this.state;
# leanpub-end-insert

    return (
      <div>
        <h1>My Hacker Stories</h1>

        <SearchForm
          searchTerm={searchTerm}
# leanpub-start-insert
          onSearchInput={() => this.setState({
            searchTerm: event.target.value
          })}
# leanpub-end-insert
        />
      </div>
    );
  }
}
~~~~~~~

Wenn der Status mehr als eine Eigenschaft in seinem Statusobjekt verwaltet, führt `setState` eine vereinfachte Aktualisierung durch. Nur die an `setState` übergebenen Eigenschaften werden aktualisiert. Alles andere bleibt, wie es ist. Für Frontend-Anwendungen ist die Statusverwaltung wichtig. Deshalb führte in der Vergangenheit ohne Hooks kein Weg an Klassenkomponenten vorbei.

In einer React-Klassenkomponente gibt es zur Statusverwaltung zwei dedizierte APIs (`this.state` und `this.setState`). In einer Funktionskomponente übernehmen heute die Hooks `useState` und `useReducer` diese Aufgabe. Verwandte Elemente werden in einen Status-Hook gepackt, während eine Klassenkomponente eine allgemeine Status-API verwendet. Dies war einer der Hauptgründe, React Hooks einzuführen und sich von Klassenkomponenten zu verabschieden.

### Übungen:

* Erstelle eine statusbehaftete Klassenkomponente (beispielsweise ein Eingabefeld mit einem Label).
