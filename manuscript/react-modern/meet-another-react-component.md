## Lerne eine andere React-Komponente kennen

Bisher haben wir ausschließlich die App-Komponente zum Erstellen unserer Anwendungen genutzt. Du hast die App im letzten Abschnitt verwendet, um alles durchzuführen, was zum Rendern einer Liste in JSX erforderlich ist. Vorgesehen war es, dass die App mit Ihren Anforderungen wächst. Damit die App bei komplexeren Aufgaben übersichtlich bleibt, teilen wir die Hauptkomponente in Unterkomponenten auf. Wir erstellen eine eigenständige Listenkomponente:

{title="src/App.js",lang="javascript"}
~~~~~~~
const list = [ ... ];

function App() { ... }

# leanpub-start-insert
function List() {
  return list.map(function(item) {
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
  });
}
# leanpub-end-insert
~~~~~~~

Optional: Verwende ein umgebendes HTML-Element, wenn die Komponente seltsam auf dich wirkt, weil der äußerste Teil des zurückgegebenen JSX JavaScript-Syntax ähnelt. Im weiteren Verlauf werde ich dessen ungeachtet die vorherige Version nutzen.

{title="src/App.js",lang="javascript"}
~~~~~~~
function List() {
# leanpub-start-insert
  return (
    <div>
      {list.map(function(item) {
# leanpub-end-insert
        return (... );
# leanpub-start-insert
      })}
    </div>
  );
# leanpub-end-insert
}
~~~~~~~

Verwende jetzt die neue List-Komponente in der App-Komponente:

{title="src/App.js",lang="javascript"}
~~~~~~~
function App() {
  return (
    <div>
      <h1>My Hacker Stories</h1>

      <label htmlFor="search">Search: </label>
      <input id="search" type="text" />

      <hr />

# leanpub-start-insert
      <List />
# leanpub-end-insert
    </div>
  );
}
~~~~~~~

Voilà! Du hast deine erste React-Komponente erstellt! Das Beispiel gibt dir einen kleinen Einblick wie diese komplexe Aufgaben in größeren React-Anwendungen übernehmen.

Komplexe React-Anwendungen verfügen über **Komponentenhierarchien** oder **Komponentenbäume**. Es gibt normalerweise eine **Einstiegspunktkomponente** (zum Beispiel App), die einen Baum darunter überspannt. Die App ist die **übergeordnete Komponente** der Liste. Andersherum ist die Liste eine **untergeordnete Komponente** der App. In einem Komponentenbaum ist die App normalerweise die **Stammkomponente**. Die Komponenten, die keine anderen rendern, werden als **Blattkomponenten** (zum Beispiel Liste) bezeichnet. Es ist möglich, dass die App mehrere Komponenten unter sich hat. Wenn die App eine andere untergeordnete Komponente hat, bezeichnen wir diese als **Geschwisterkomponente** der Liste.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Meet-another-React-Component).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Lists-in-React...hs/Meet-another-React-Component?expand=1).
* Stelle die Komponenten der Beispielanwendung grafisch dar. Zeichnen die App und die Listenkomponente als Baum auf ein Blatt Papier. Erweitere diesen Komponentenbaum mit möglichen Komponenten (beispielsweise einer Suchkomponente für das Eingabefeld und das Label). Versuche herauszufinden, welche anderen Teile als eigenständige Komponenten integrierbar sind.
* Wenn eine Suchkomponente in der App verwendet wird, wäre diese eine Geschwister-, Eltern- oder Kindkomponente für die Listenkomponente?
* Frage dich, welche Probleme auftreten, wenn wir `list` weiterhin als globale Variable behandeln. Wie wir mit diesem umgehen ist Thema in den nächsten Kapiteln.
