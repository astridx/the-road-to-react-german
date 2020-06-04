## CSS-Module in React

CSS-Module stellen einen fortschrittlicheren **CSS-in-CSS**-Ansatz dar. Die CSS-Datei bleibt grundsätzlich unverändert. Es kommen nur Funktion hinzu. Verwende sie, um normales CSS zu schreiben, oder nutze Techniken wie Sass. Im nachfolgenden erkläre ich dir, wie du CSS-Module zusammen mit React-Komponenten verwendest. Benenne als Erstes die Datei *src/App.css* in *src/App.module.css* um. So aktivierst du CSS-Module in der *Create React App*. Öffne dazu das Projekt-Verzeichnis über die Befehlszeile des Betriebssystems. Führe folgende Anweisung aus, wenn du Linux verwendest --- passe andernfalls den Befehl an deine Arbeitsumgebung an:

{title="Command Line",lang="text"}
~~~~~~~
mv src/App.css src/App.module.css
~~~~~~~

In die umbenannte Datei *src/App.module.css* fügst du deine CSS-Klassendefinitionen ein, genauso wie vorher in die Datei *src/App.css*:

{title="src/App.module.css",lang="css"}
~~~~~~~
.container {
  height: 100vw;
  padding: 20px;

  background: #83a4d4; /* fallback for old browsers */
  background: linear-gradient(to left, #b6fbff, #83a4d4);

  color: #171212;
}

.headlinePrimary {
  font-size: 48px;
  font-weight: 300;
  letter-spacing: 2px;
}
~~~~~~~

Importiere die Datei *src/App.module.css* mithilfe eines relativen Pfades. Dieses Mal geschieht der Import als JavaScript-Objekt, wobei die Wahl des Names (hier `styles`) dir überlassen bleibt:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';

# leanpub-start-insert
import styles from './App.module.css';
# leanpub-end-insert
~~~~~~~

Anstatt den `className` als eine Zeichenfolge zu definieren, die einer CSS-Datei zugeordnet ist, greifst du direkt über das Objekt `styles` auf die CSS-Klasse zu und weist diese deinem Element mit einem JavaScript im JSX-Ausdruck zu. Das hört sich kompliziert an. Hier siehst du, dass es unkompliziert ist:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
# leanpub-start-insert
    <div className={styles.container}>
      <h1 className={styles.headlinePrimary}>My Hacker Stories</h1>
# leanpub-end-insert

      <SearchForm
        searchTerm={searchTerm}
        onSearchInput={handleSearchInput}
        onSearchSubmit={handleSearchSubmit}
      />

      {stories.isError && <p>Something went wrong ...</p>}

      {stories.isLoading ? (
        <p>Loading ...</p>
      ) : (
        <List list={stories.data} onRemoveItem={handleRemoveStory} />
      )}
    </div>
  );
};
~~~~~~~

Es gibt verschiedene Möglichkeiten, mehrere CSS-Klassen über das Objekt `styles` zum einzelnen `className`-Attribut des Elements hinzuzufügen. Hier verwenden wir Template-Strings:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <div className={styles.item}>
    <span style={{ width: '40%' }}>
# leanpub-end-insert
      <a href={item.url}>{item.title}</a>
    </span>
# leanpub-start-insert
    <span style={{ width: '30%' }}>{item.author}</span>
    <span style={{ width: '10%' }}>{item.num_comments}</span>
    <span style={{ width: '10%' }}>{item.points}</span>
    <span style={{ width: '10%' }}>
# leanpub-end-insert
      <button
        type="button"
        onClick={() => onRemoveItem(item)}
# leanpub-start-insert
        className={`${styles.button} ${styles.buttonSmall}`}
# leanpub-end-insert
      >
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

Es ist möglich Inline-Stile als dynamischere Styles in JSX hinzufügen oder eine CSS-Erweiterung wie Sass hinzuzufügen, um erweiterte Funktionen wie CSS-Verschachtelung zu aktivieren. Hier verwenden wir natives CSS:

{title="src/App.module.css",lang="css"}
~~~~~~~
.item {
  display: flex;
  align-items: center;
  padding-bottom: 5px;
}

.item > span {
  padding: 0 5px;
  white-space: nowrap;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.item > span > a {
  color: inherit;
}
~~~~~~~

Dann implementieren wir die CSS-Klassen für die Schaltfläche in der Datei *src/App.module.css*:

{title="src/App.module.css",lang="css"}
~~~~~~~
.button {
  background: transparent;
  border: 1px solid #171212;
  padding: 5px;
  cursor: pointer;

  transition: all 0.1s ease-in;
}

.button:hover {
  background: #171212;
  color: #ffffff;
}

.buttonSmall {
  padding: 5px;
}

.buttonLarge {
  padding: 10px;
}
~~~~~~~

Im Gegensatz zu `button_small` und `button_large` aus dem vorherigen Kapitel nutzen wir hier eine Pseudo-BEM-Namenskonvention. Beim Import als CSS-Modul sind nicht alle CSS-Klassen in BEM-Namenskonvention gültige JavaScript-Bezeichner. Die Verwendung der [Klammernotation](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Property_Accessors#Klammernotation) wäre erforderlich: `styles['button_small']`. Das Gleiche gilt für CSS-Klassen, die mit einem Bindestrich (`-`) definiert sind. Deshalb verwenden wir jetzt zum Beispiel `buttonLarge` und greifen mit `styles.buttonLarge` auf den Stil zu. Beispielsweise in der Item-Komponente:

{title="src/App.js",lang="javascript"}
~~~~~~~
const SearchForm = ({ ... }) => (
# leanpub-start-insert
  <form onSubmit={onSearchSubmit} className={styles.searchForm}>
# leanpub-end-insert
    <InputWithLabel ... >
      <strong>Search:</strong>
    </InputWithLabel>

    <button
      type="submit"
      disabled={!searchTerm}
# leanpub-start-insert
      className={`${styles.button} ${styles.buttonLarge}`}
# leanpub-end-insert
    >
      Submit
    </button>
  </form>
);
~~~~~~~

Der SearchForm-Komponente ordnen wir ebenfalls zwei CSS-Klassen zu. Wie du siehst, ist es umständlich, zwei Stile in einem Element über Template-Strings zu verwenden. Eine praktischer alternative bietet die Bibliothek [classnames](https://github.com/JedWatson/classnames), mit der CSS-Klassen sogar [dynamisch und bedingt](https://github.com/JedWatson/classnames#usage-with-reactjs) zuordenbar sind. Installiere die Bibliothek im nächsten Schritt über die Befehlszeile als Projektabhängigkeit und verwende sie, um der Schaltfläche die CSS-Klassen zuzuordnen:

{title="src/App.js",lang="javascript"}
~~~~~~~
import cs from 'classnames';

...

className={cs(styles.button, styles.buttonLarge)}
~~~~~~~

Die Bibliothek bietet bedingtes Styling. Die linke Seite der Objekteigenschaft wird dabei als [berechneter Eigenschaftsname]((https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Operators/Object_initializer)) verwendet und nur angewendet, wenn die rechte Seite den Wert `true` hat:

{title="src/App.js",lang="javascript"}
~~~~~~~
import cs from 'classnames';

...

// Irgendwo in einem className-Attribut
className={cs(styles.button, { [styles.buttonLarge]: isLarge })}
~~~~~~~

Fahre abschließend mit der InputWithLabel-Komponente fort. Verwende die Bibliothek **classnames**, um dieser das `className`-Attribut zuzuordnen:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
  ...

  return (
    <>
# leanpub-start-insert
      <label htmlFor={id} className={styles.label}>
# leanpub-end-insert
        {children}
      </label>
      &nbsp;
      <input
        ref={inputRef}
        id={id}
        type={type}
        value={value}
        onChange={onInputChange}
# leanpub-start-insert
        className={styles.input}
# leanpub-end-insert
      />
    </>
  );
};
~~~~~~~

Ergänze abschließend die fehlenden Stile in der Datei *src/App.module.css*:

{title="src/App.module.css",lang="css"}
~~~~~~~
.searchForm {
  padding: 10px 0 20px 0;
  display: flex;
  align-items: baseline;
}

.label {
  border-top: 1px solid #171212;
  border-left: 1px solid #171212;
  padding-left: 5px;
  font-size: 24px;
}

.input {
  border: none;
  border-bottom: 1px solid #171212;
  background-color: transparent;

  font-size: 24px;
}
~~~~~~~

Einiges von dem, was ich im vorhergehenden Kapitel geschrieben hatte, trifft ebenfalls auf CSS-Module zu: Beispielsweise: Stile wie `input` und `label` sind in einer globalen *src/index.css*-Datei unter Umständen effizienter zu verwenden. 

Wie jeder andere CSS-in-CSS-Ansatz unterstützen CSS-Module Sass, um CSS-Funktionen wie das Verschachteln zu verwenden. Nutze dies zur Strukturierung deiner Stile, ich habe dies hier im Abschnitt außen vor gelassen. Mein Ziel war es, dir die Anwendung von CSS-Modulen näher zu bringen, denn diese habe im Gegensatz zu purem CSS einen weiteren Vorteil: Jedes Mal, wenn ein verwendeter Stil nicht definiert ist, wird ein Fehler über die Konsole deines Browsers ausgegeben. Beim Standard-CSS-Ansatz bleibt ein solcher Tippfehler in der Regel unbemerkt und führt nicht selten einige Zeit später zu unerwartetem Verhalten und einer langwierigen Fehlersuche.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/CSS-Modules-in-React).
  * Bestätige die [Änderungen](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/CSS-Modules-in-React?expand=1).
* Lese mehr zum Thema [CSS Modules in der Create React App](https://create-react-app.dev/docs/adding-a-css-modules-stylesheet).
