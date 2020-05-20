## React Anwendungen testen

Das Testen des Quellcodes wird oft vernachlässigt. Dabei ist es wesentlich und als  zwingend erforderlich anzusehen. Es ist unentbehrlich, die Qualität und Funktionalität des Quellcodes automatisch zu überprüfen, bevor dieser im Echtsystem angewendet wird. Die [Testpyramide](https://martinfowler.com/articles/practical-test-pyramid.html) dient als Grundregel.

Die Testpyramide umfasst End-to-End-Tests, Integrationstests und Komponententests. Unit-Tests werden für kleine, isolierte Codeblöcke verwendet, z. B. eine einzelne Funktion oder Komponente. Integrationstests helfen uns, herauszufinden, ob diese Einheiten zusammenarbeiten. Ein End-to-End-Test simuliert ein reales Szenario, beispielsweise den Anmeldevorgang in einer Webanwendung. Unit-Tests sind unkompliziert zu schreiben und zu warten. Man betrachtet hierbei einen Teilaspekt herausgelöst. Auf End-to-End-Tests trifft das Gegenteil zu.

Scheiben wir zuerst Unit-Tests, die unsere Funktionen und Komponenten abdecken. Danach verwenden wir Integrationstests, um sicherzustellen, dass die einzelnen mit den Unit-Tests unabhängig geprüften Einheiten, wie erwartet zusammenarbeiten. Am Ende benötigen wir einige End-to-End-Tests, um kritische Szenarien zu simulieren. In diesem Kapitel behandeln wir **Unit und Integrationstests** sowie eine komponentenspezifische Testtechnik namens **Schnappschusstests** oder **Snapshot Tests**. **E2E-Tests** werden Teil der Übungen sein.

Da es viele [Test-Bibliotheken](https://www.robinwieruch.de/react-testing-tutorial) gibt, ist es als Anfänger schwierig, die passende auszuwählen. Wir werden [Jest](https://jestjs.io/) von Facebook als Test-Framework verwenden. Die meisten React Entwickler nutzten Jest als Grundlage. Wir befinden uns in guter Gesellschaft. So ist dies eine geeignete Einführung, denn du lernst Dinge, mit denen du in jedem Fall konfrontiert wirst, wenn du länger mit React arbeitest.

### Unit Tests und Integrationstests

Oft sind die Grenzen zwischen Unit- und Integrationstests unklar. Die List-Komponente mit ihrer Item-Komponente ist ein Beispiel dafür. Testet man diese, handelt es sich um einen Integrationstest oder einen Unit Test für zwei eng gekoppelte Komponenten. Diese Einordnung ist meiner Meinung nach nebensächlich. Wichtig ist, dass du im vorherigen Abschnitt verstanden hast, warum Unit Tests und Integrationstests unterschieden werden. In diesem Abschnitt behandeln wir erst Unit-Tests und später Integrationstests. 

Wir wagen uns langsam an die Materie heran. Als Erstes schreiben wir einen Pseudotest, den wir in die Datei *src/App.test.js*-Datei einfügen:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('something truthy', () => {
  it('true to be true', () => {
    expect(true).toBe(true);
  });
});
~~~~~~~

Glücklicherweise ist *Jest* in die **create-react-App** integriert. Rufe den Test über die Befehlszeile auf. Die Ausgabe für alle Testfälle wird dir unmittelbar danach angezeigt.

{title="Command Line",lang="text"}
~~~~~~~
npm test
~~~~~~~

*Jest* sucht in alle Dateien mit dem Suffix *test.js* im Dateinamen nach Testmethoden. Erfolgreiche Tests werden grün angezeigt. Fehlgeschlagene in roter Farbe ausgegeben:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('something truthy', () => {
  it('true to be true', () => {
    expect(true).toBe(false);
  });
});
~~~~~~~

Tests in *Jest* bestehen aus **Testsuiten** (`describe` - `beschreiben`), die aus **Testfällen** (`it`) zusammengesetzt sind, die wiederum **Behauptungen** (`expect` - `erwarten`) aufstellen, die sich als wahr (grün) oder falsch (rot) herausstellen:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
// test suite
describe('truthy and falsy', () => {
  // test case
  it('true to be true', () => {
    // test assertion
    expect(true).toBe(true);
  });

  // test case
  it('false to be false', () => {
    // test assertion
    expect(false).toBe(false);
  });
});
~~~~~~~

Der "it"-Block beschreibt einen Testfall. Er besteht aus einer Testbeschreibung. Der Test ist erfolgreich oder er schlägt fehl. Binde diesen Block in einen "describe"-Block ein, der die Testsuite zusammen mit anderen "it"-Blöcken für eine Komponente definiert. Beide Arten werden verwendet, um Testfälle zu organisieren. Beachte, dass die Funktion `it` in der JavaScript-Community als Einzeltestfallfunktion bekannt ist. In Jest wird `it` häufig als Alias `test`-Funktion verwendet:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('something truthy', () => {
# leanpub-start-insert
  test('true to be true', () => {
# leanpub-end-insert
    expect(true).toBe(false);
  });
});
~~~~~~~

Um React-Komponenten in *Jest* zu verwenden, benötigen wir eine Bibibliothek zum Rendern von Komponenten in einer Testumgebung. Deshalb installieren wir **react-test-renderer** über die Befehlszeile:

{title="Command Line",lang="text"}
~~~~~~~
npm install react-test-renderer --save-dev
~~~~~~~

Bevor du deine ersten Komponenten testest, ist es erforderlich, dass du diese aus deiner *src/App.js*-Datei exportierst:

{title="src/App.js",lang="javascript"}
~~~~~~~
...

export default App;

# leanpub-start-insert
export { SearchForm, InputWithLabel, List, Item };
# leanpub-end-insert
~~~~~~~

Importiere die Komponente dann zusammen mit der zuvor installierten Bibliothek **react-test-renderer** in die Datei *src/App.test.js*:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
import React from 'react';
import renderer from 'react-test-renderer';

import App, { Item, List, SearchForm, InputWithLabel } from './App';
# leanpub-end-insert
~~~~~~~

Jetzt endlich: Schreibe deinen ersten Komponententest. Wähle dazu die Item-Komponente. Der Testfall rendert diese mit einem `item` unter Verwendung der Bibliothek **react-test-renderer**:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
import React from 'react';
import renderer from 'react-test-renderer';

import App, { Item, List, SearchForm, InputWithLabel } from './App';

# leanpub-start-insert
describe('Item', () => {
  const item = {
    title: 'React',
    url: 'https://reactjs.org/',
    author: 'Jordan Walke',
    num_comments: 3,
    points: 4,
    objectID: 0,
  };

  it('renders all properties', () => {
    const component = renderer.create(<Item item={item} />);

    expect(component.root.findByType('a').props.href).toEqual(
      'https://reactjs.org/'
    );
  });
});
# leanpub-end-insert
~~~~~~~

Informationen zu den Attributen einer Komponente oder eines Elements sind über die Eigenschaft `props` verfügbar. In der zu testenden Annahme befindet sich das Ankertag (`a`) und sein Attribut `href`. Wir führen eine Gleichheitsprüfung durch. Wenn der Test grün ausfällt, sind wir sicher, dass `href` auf die korrekte Eigenschaft `url` des Elements `item` gesetzt ist.

Im selben Testfall ergänzen wir eine weitere Testannahme:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('Item', () => {
  const item = {
    title: 'React',
    url: 'https://reactjs.org/',
    author: 'Jordan Walke',
    num_comments: 3,
    points: 4,
    objectID: 0,
  };

  it('renders all properties', () => {
    const component = renderer.create(<Item item={item} />);

    expect(component.root.findByType('a').props.href).toEqual(
      'https://reactjs.org/'
    );

# leanpub-start-insert
    expect(
      component.root.findAllByType('span')[1].props.children
    ).toEqual('Jordan Walke');
# leanpub-end-insert
  });
});
~~~~~~~

Da es mehrere `span`-Elemente gibt, suchen wir nach allen, wählen das zweite aus (Index ist `1`, da wir bei `0` zu zählen beginnen) und vergleichen dessen `children`-Eigenschaft mit der `author`-Eigenschaft des Test-Item. Dieser Test ist nicht zukunftssicher. Sobald sich die Reihenfolge der `span`-Elemente in der Item-Komponente ändert, schlägt er fehl. 

Vermeide Tests mit einem solchen Unsicherheitsfaktor. Das bringt auf lange Sicht nur Probleme. Ändere die Test-Behauptung beispielsweise wie folgt:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('Item', () => {
  const item = { ... };

  it('renders all properties', () => {
    ...

# leanpub-start-insert
    expect(
      component.root.findAllByProps({ children: 'Jordan Walke' })
        .length
    ).toEqual(1);
# leanpub-end-insert
  });
});
~~~~~~~

Die Testbehauptung ist nach der Änderung allgemeiner. Es wird getestet, ob es ein Test-Item mit irgendeiner Eigenschaft gibt, die mit *Jordan Walke* belegt ist. Diese Vorgehensweise ist nicht 100% sicher, mit ihr werden aber viele mögliche Probleme aufgedeckt. Die Besonderheit, dass die `author`-Eigenschaft gar nicht vorkommt dafür eine `titel`-Eigenschaft mit dem Wert *Jordan Walke* vorhanden ist, würde uns mit diesem Test durchgehen. Aber, wie überall im Leben ist der gesunde Mittelweg die beste Herangehensweise. Wende die Technik für alle anderen Eigenschaften selbst an oder hebe dir dies als Übung für später auf, wenn du es bevorzugst erst einmal weiter zu lesen.

Wir haben mit einem Test sichergestellt, dass die Item-Komponente als Text- oder HTML-Attribut (`href`) gerendert wird, aber wir haben den Callback-Handler bisher nicht getestet. Der folgende Testfall stellt für diesen eine Behauptung auf, indem er ein Klickereignis über das Attribut `onClick` des `button`-Elements simuliert:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('Item', () => {
  const item = { ... };

  it('renders all properties', () => {
    ...
  });

# leanpub-start-insert
  it('calls onRemoveItem on button click', () => {
    const handleRemoveItem = jest.fn();

    const component = renderer.create(
      <Item item={item} onRemoveItem={handleRemoveItem} />
    );

    component.root.findByType('button').props.onClick();

    expect(handleRemoveItem).toHaveBeenCalledTimes(1);
    expect(handleRemoveItem).toHaveBeenCalledWith(item);

    expect(component.root.findAllByType(Item).length).toEqual(1);
  });
# leanpub-end-insert
});
~~~~~~~

Mit *Jest* übergeben wir testspezifische Funktionen als Eigenschaft an die Item-Komponente. Diese heißen **spy**, **stub** oder **mock**; Jede wird für verschiedene Testszenarien verwendet. `jest.fn()` gibt uns ein *mock* für die eigentliche Funktion zurück, mit dem wir erfassen können, wann sie aufgerufen wird. So verwenden wir Testannahmen in *Jest* wie `toHaveBeenCalledTimes`, mit denen wir die Anzahl der Aufrufe der Funktion bestätigen und `toHaveBeenCalledWith`, um die an ihn übergebenen Argumente zu überprüfen.

Der Komponententest der Item-Komponente ist abgeschlossen, da wir die Eingabe (`item`) und die Ausgabe (`onRemoveItem`) getestet haben. Verwechsele die beiden nicht mit Eingabe (Argumente) und Ausgabe (JSX) der Funktionskomponente, die ebenfalls getestet wurden. Eine letzte Verbesserung macht die Testsuite für die Item-Komponente präziser, indem sie eine gemeinsame Setup-Funktion integriert:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('Item', () => {
  const item = { ... };
# leanpub-start-insert
  const handleRemoveItem = jest.fn();
# leanpub-end-insert

# leanpub-start-insert
  let component;
# leanpub-end-insert

# leanpub-start-insert
  beforeEach(() => {
    component = renderer.create(
      <Item item={item} onRemoveItem={handleRemoveItem} />
    );
  });
# leanpub-end-insert

  it('renders all properties', () => {
    expect(component.root.findByType('a').props.href).toEqual(
      'https://reactjs.org/'
    );

    ...
  });

  it('calls onRemoveItem on button click', () => {
    component.root.findByType('button').props.onClick();

    ...
  });
});
~~~~~~~

A common setup (or teardown) function in tests removes duplicated code. Since the component must be rendered for both test cases, and the props are the same for both renderings, we can share this code in a common setup function. From there, we'll move on to testing the List component:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
...

describe('Item', () => {
  ...
});

# leanpub-start-insert
describe('List', () => {
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

  it('renders two items', () => {
    const component = renderer.create(<List list={list} />);

    expect(component.root.findAllByType(Item).length).toEqual(2);
  });
});
# leanpub-end-insert
~~~~~~~

The test checks straightforward whether two Item components are rendered for the two items in the list. You could continue testing the List component by checking whether each callback handler (`onRemoveItem`) is called for each Item component, which would have a similar solution to the previous Item component's test. Is this test still a unit test or already an integration test?

Keeping this question in the room, we will move on to the SearchForm with InputWithLabel component:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
describe('SearchForm', () => {
  const searchFormProps = {
    searchTerm: 'React',
    onSearchInput: jest.fn(),
    onSearchSubmit: jest.fn(),
  };

  let component;

  beforeEach(() => {
    component = renderer.create(<SearchForm {...searchFormProps} />);
  });

  it('renders the input field with its value', () => {
    const value = component.root.findByType(InputWithLabel).props
      .value;

    expect(value).toEqual('React');
  });
});
# leanpub-end-insert
~~~~~~~

In this test, we assert whether the InputWithLabel component receives the correct prop from the SearchForm component. Essentially the test stops before the InputWithLabel component, because it only tests the interface (props) of it. Arguably it's still a unit test, because the underlying implementation details of the InputWithLabel component could change without changing the interface. You can change the test to make it work through to the InputWithLabel component's input field, because all child components and its elements are rendered too:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('SearchForm', () => {
  ...

  it('renders the input field with its value', () => {
# leanpub-start-insert
    const value = component.root.findByType('input').props.value;
# leanpub-end-insert

    expect(value).toEqual('React');
  });
});
~~~~~~~

This is our first integration test between the SearchForm and InputWithLabel components, which aren't as tightly coupled as the List and Item components. The InputWithLabel component can be used in other components (highly reusable), whereas the Item component is essentially a non-reusable part of the List component.

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('SearchForm', () => {
  const searchFormProps = {
    searchTerm: 'React',
    onSearchInput: jest.fn(),
    onSearchSubmit: jest.fn(),
  };

  ...

# leanpub-start-insert
  it('changes the input field', () => {
    const pseudoEvent = { target: 'Redux' };

    component.root.findByType('input').props.onChange(pseudoEvent);

    expect(searchFormProps.onSearchInput).toHaveBeenCalledTimes(1);
    expect(searchFormProps.onSearchInput).toHaveBeenCalledWith(
      pseudoEvent
    );
  });

  it('submits the form', () => {
    const pseudoEvent = {};

    component.root.findByType('form').props.onSubmit(pseudoEvent);

    expect(searchFormProps.onSearchSubmit).toHaveBeenCalledTimes(1);
    expect(searchFormProps.onSearchSubmit).toHaveBeenCalledWith(
      pseudoEvent
    );
  });
# leanpub-end-insert
});
~~~~~~~

Like the Item component, the last two tests asserted the component's callback handler(s). All input (non function props) and output (callback handler function) props are tested for the SearchForm component's interface and integration with the InputWithLabel component.

You can test edge cases  like a disabled button as well. The `update()` method on the rendered test component helps us provide new props to the component at stake:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('SearchForm', () => {
  const searchFormProps = {
    searchTerm: 'React',
    onSearchInput: jest.fn(),
    onSearchSubmit: jest.fn(),
  };

  let component;

  beforeEach(() => {
    component = renderer.create(<SearchForm {...searchFormProps} />);
  });

  ...

# leanpub-start-insert
  it('disables the button and prevents submit', () => {
    component.update(
      <SearchForm {...searchFormProps} searchTerm="" />
    );

    expect(
      component.root.findByType('button').props.disabled
    ).toBeTruthy();
  });
# leanpub-end-insert
});
~~~~~~~

Now we'll move one level higher in our application's component hierarchy. The App component fetches the list data, which is provided to the List component. After importing the App component, a naive test would look like this:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
describe('App', () => {
  it('succeeds fetching data with a list', () => {
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

    const component = renderer.create(<App />);

    expect(component.root.findByType(List).props.list).toEqual(list);
  });
});
# leanpub-end-insert
~~~~~~~

In the actual App component, a third-party library (axios) is used to make a request to a remote API. This API returns data we can't foresee in the test, so we have to mock it instead. Jest provides mechanisms that mock entire libraries and their methods. In this case, we want  to mock the `get()` method of axios to return our desired data:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
import React from 'react';
import renderer from 'react-test-renderer';
# leanpub-start-insert
import axios from 'axios';
# leanpub-end-insert

# leanpub-start-insert
jest.mock('axios');
# leanpub-end-insert

...

describe('App', () => {
  it('succeeds fetching data with a list', () => {
    const list = [ ... ];

# leanpub-start-insert
    const promise = Promise.resolve({
      data: {
        hits: list,
      },
    });
# leanpub-end-insert

# leanpub-start-insert
    axios.get.mockImplementationOnce(() => promise);
# leanpub-end-insert

    const component = renderer.create(<App />);

    expect(component.root.findByType(List).props.list).toEqual(list);
  });
});
~~~~~~~

The test reads synchronously, but we still have to deal with the asynchronous data. The component should re-render when its state updates. We can perform this with our utility library and async/await:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {
# leanpub-start-insert
  it('succeeds fetching data with a list', async () => {
# leanpub-end-insert
    const list = [ ... ];

    const promise = Promise.resolve({
      data: {
        hits: list,
      },
    });

    axios.get.mockImplementationOnce(() => promise);

# leanpub-start-insert
    let component;

    await renderer.act(async () => {
      component = renderer.create(<App />);
    });
# leanpub-end-insert

    expect(component.root.findByType(List).props.list).toEqual(list);
  });
});
~~~~~~~

Instead of rendering the App component, we mocked the response from the remote API by mocking the method that fetches the data. To stay on the *happy path*, we told the test to treat the component as an asynchronously updating component. You can apply a similar strategy to the *unhappy path*:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('App', () => {
  it('succeeds fetching data with a list', async () => {
    ...
  });

# leanpub-start-insert
  it('fails fetching data with a list', async () => {
    const promise = Promise.reject();

    axios.get.mockImplementationOnce(() => promise);

    let component;

    await renderer.act(async () => {
      component = renderer.create(<App />);
    });

    expect(component.root.findByType('p').props.children).toEqual(
      'Something went wrong ...'
    );
  });
# leanpub-end-insert
});
~~~~~~~

The data fetching and integration with a remote API is tested now. We moved from focused unit tests for single components to tests with multiple components and their integration with third-parties like axios and remote APIs.

### Snapshot Testing

Jest also lets you take a **snapshot** of your rendered component, run it against future captures, and be notified of changes. Changes can then be accepted or denied depending on the desired outcome. This mechanism complements unit and integration tests well, since it only tests the differences of the rendered output without heavy maintenance costs. To see it in action, extend the Item component test suite with your first snapshot test:

{title="src/App.test.js",lang="javascript"}
~~~~~~~
describe('Item', () => {
  ...

  beforeEach(() => {
    component = renderer.create(
      <Item item={item} onRemoveItem={handleRemoveItem} />
    );
  });

  ...

# leanpub-start-insert
  test('renders snapshot', () => {
    let tree = component.toJSON();
    expect(tree).toMatchSnapshot();
  });
# leanpub-end-insert
});
~~~~~~~

Run your tests again and observe how they succeed or fail. Once we change the output of the render block in Item component in the *src/App.js* file, by changing the structure of the returned HTML, the snapshot test fails. We can then decide whether to update the snapshot or to investigate the Item component.

Jest stores snapshots in a folder so it can validate the difference against future snapshot tests. Users can share these snapshots across teams for version control (e.g. git). Running a snapshot test for the first time creates the snapshot file in your project's folder. When the test is run again, the snapshot is expected to match the version from the latest test run. This is how we make sure the DOM stays the same.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/react-testing).
  * Bestätige die [Änderungen gegenüber dem Stand der Anwendung am Ende des ersten Kapitels](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/react-testing?expand=1).
* Add one snapshot test for each of all the other components.
* Lese mehr zum Thema [testing React components](https://www.robinwieruch.de/react-testing-tutorial).
  * Lese mehr zum Thema [Jest](https://jestjs.io/) and [Jest for React](https://www.robinwieruch.de/react-testing-jest/) for unit, integration and snapshot tests.
* Lese mehr zum Thema [E2E tests in React](https://www.robinwieruch.de/react-testing-cypress).
* While you continue with the learning experience in the upcoming sections, keep your tests green and add new tests whenever you feel the need for it.