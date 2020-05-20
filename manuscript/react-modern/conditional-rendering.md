## Bedingte Darstellung in React

Das Arbeiten mit asynchronen Daten in React führt zu bedingten Zustände: Die Daten sind abrufbar oder es liegen keine Daten `null` vor. Da der Ausgangszustand in unserer Anwendung eine leere Liste ist und nicht `null`, betrifft der letzte Fall uns nicht. Wenn `null` möglich wäre, müssten wir dieses Problem in JSX lösen. Da es sich um `[]` handelt, filtern wir ein leeres Array in der Suchfunktion `filter()`. Übrig bleibt wie erwartet ein leeres Array. Dies führt dazu, dass in der Funktion `map()` der List-Komponente nichts angezeigt wird.

In einer realen Anwendung gibt es mehr als zwei bedingte Zustände bei asynchrone Daten. Zum Beispiel `schwebend oder pending`. In diesem Falle blendest du idealerweise einen Ladeindikator ein:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, setStories] = React.useState([]);
# leanpub-start-insert
  const [isLoading, setIsLoading] = React.useState(false);
# leanpub-end-insert

  React.useEffect(() => {
# leanpub-start-insert
    setIsLoading(true);
# leanpub-end-insert

    getAsyncStories().then(result => {
      setStories(result.data.stories);
# leanpub-start-insert
      setIsLoading(false);
# leanpub-end-insert
    });
  }, []);

  ...
};
~~~~~~~

Mit [dem ternären Operator von JavaScript](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) binden wir diesen als **bedingte Darstellung** in JSX ein:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

      <hr />

# leanpub-start-insert
      {isLoading ? (
        <p>Loading ...</p>
      ) : (
# leanpub-end-insert
        <List
          list={searchedStories}
          onRemoveItem={handleRemoveStory}
        />
# leanpub-start-insert
      )}
# leanpub-end-insert
    </div>
  );
};
~~~~~~~

Asynchrone Daten kommen zusammen mit Datenübertragungsfehlern. In unserer simulierten Umgebung tritt dies nicht auf. In der Realität sind Fehler unvermeidbar. Insbesondere dann, wenn Daten von der API eines Drittanbieters abgerufen werden. Führe deshalb einen eigenen Status für die Fehlerbehandlung ein und fange Fehler im Block `catch()` des Promis Objektes ab:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const [stories, setStories] = React.useState([]);
  const [isLoading, setIsLoading] = React.useState(false);
# leanpub-start-insert
  const [isError, setIsError] = React.useState(false);
# leanpub-end-insert

  React.useEffect(() => {
    setIsLoading(true);

    getAsyncStories()
      .then(result => {
        setStories(result.data.stories);
        setIsLoading(false);
      })
# leanpub-start-insert
      .catch(() => setIsError(true));
# leanpub-end-insert
  }, []);

  ...
};
~~~~~~~

Gib den Benutzern als nächstes ein Feedback, falls ein Fehler aufgetreten ist. Dieses Mal wird entweder etwas oder nichts gerendert. Verwende anstelle eines ternären Operators, bei dem eine Seite `null` zurückgibt, den logischen Operator `&&` als Kurzform:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
    <div>
      ...

      <hr />

# leanpub-start-insert
      {isError && <p>Something went wrong ...</p>}
# leanpub-end-insert

      {isLoading ? (
        <p>Loading ...</p>
      ) : (
        ...
      )}
    </div>
  );
};
~~~~~~~

In JavaScript wird `true && 'Hallo Welt'` immer als `'Hallo Welt'` ausgewertet. `false && 'Hallo Welt'` wird immer mit `false` bewertet. In React nutzen wir dieses Verhalten zu unserem Vorteil. Wenn die erste Bedingung erfüllt ist, wird der Ausdruck nach dem logischen Operator `&&` ausgegeben. Im Falle von `false` überspringt React den Ausdruck.

Bedingtes Rendern ist nicht nur für asynchrone Daten sinnvoll. Das einfachste Beispiel ist eine boolesche Variable, die mit einer Schaltfläche umgeschaltet wird. Wenn die Variable `true` ist, wird etwas gerendert. Ist sie mit `false` belegt, bleibt die Anzeige leer.

Dieses Feature ist insbesondere deshalb leistungsfähig, weil es JSX bedingt rendert. Es handelt sich um ein weiteres Tool in React, um die Benutzeroberfläche dynamisch zu gestalten. Und, wie wir festgestellt haben, ist dies für komplexere Steuerungsabläufe mit asynchronen Daten unerlässlich.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Conditional-Rendering).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Asynchronous-Data...hs/React-Conditional-Rendering?expand=1).
* Lese mehr über die bedingte Darstellung in React([0](https://www.robinwieruch.de/conditional-rendering-react/), [1](https://de.reactjs.org/docs/conditional-rendering.html)).
