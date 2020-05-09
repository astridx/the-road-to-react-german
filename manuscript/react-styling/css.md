# Styling in React

Es gibt viele Möglichkeiten, eine React-Anwendung zu gestalten, und es gibt ausgiebige Debatten über die effektivste **Styling-Strategie** und den unübertrefflichsten **Styling-Ansatz**. Wir werden einige dieser Strategien und Ansätze durchgehen. Wie überall im Leben gibt es für jede Sichtweise Vor- und Nachteile. Hier gehe ich darauf ein, wie Entwickler und Teams herausfinde, was für sie am besten passt.

Wir fangen mit allgemeinem CSS an und sehen uns dann fortgeschrittene Alternativen an: **CSS-in-CSS** (**CSS Modules**) und **CSS-in-JS** (**Styled Components**). Dies sind nur zwei von vielen Strategien/Ansätzen. Außerdem lernst du, wie du skalierbare Vektorgrafiken (SVGs) in deine React-Anwendung integrierst, zum Beispiel ein Logo oder Icons.

Wähle eine [beliebte React-Bibliothek für React](https://www.robinwieruch.de/react-libraries), wenn du planst allgemeine UI-Komponenten (beispielsweise eine Schaltfläche, ein Dialog oder ein Auswahlfeld) nicht selbst einzufügen. Bedenke dabei aber: Du befindest dich auf einer soliden Basis, wenn du React lernst und diese Komponenten eigenhändig erstellst. Daher werden wir hier im Buch keine der UI-Komponentenbibliotheken nutzen.

Die nachfolgenden Ansätze für das Styling sind in der `create-react-app` vorkonfiguriert. Wenn du die Build-Tools (beispielsweise Webpack) selbst verwaltest, sind unter Umständen weitere Konfigurationsschritte erforderlich. Da wir die `create-react-app` verwenden, nutzen wir alles als Assets.

## CSS in React

Allgemeines CSS in React ähnelt dem Standard-CSS. Jede Standard-Webanwendung gibt HTML-Elementen ein Attribut `class` (in React ist es `className`), das später in einer CSS-Datei mit Eigenschaften konkretisiert wird.

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  return (
# leanpub-start-insert
    <div className="container">
      <h1 className="headline-primary">My Hacker Stories</h1>
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

Das HTML-Element `<hr />` wurde entfernt, da CSS sich zukünftig um die Randbereich kümmern wird. Wir importieren die CSS-Datei mithilfe von `create-react-app`:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
import axios from 'axios';

# leanpub-start-insert
import './App.css';
# leanpub-end-insert
~~~~~~~

Diese CSS-Datei definiert die CSS-Klassen, die wir in der App-Komponente verwenden. Definiere sie in deiner *src/App.css*-Datei wie folgt:

{title="src/App.css",lang="css"}
~~~~~~~
.container {
  height: 100vw;
  padding: 20px;

  background: #83a4d4; /* fallback for old browsers */
  background: linear-gradient(to left, #b6fbff, #83a4d4);

  color: #171212;
}

.headline-primary {
  font-size: 48px;
  font-weight: 300;
  letter-spacing: 2px;
}
~~~~~~~

Du siehst die ersten Stile in deiner Anwendung, wenn du sie erneut startest. Als Nächstes stylen wir die Item-Komponente. Einige ihrer Elemente erhalten das Attribut `className`. Wir verwenden hier eine neue Styling-Technik:

{title="src/App.js",lang="javascript"}
~~~~~~~
const Item = ({ item, onRemoveItem }) => (
# leanpub-start-insert
  <div className="item">
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
        className="button button_small"
# leanpub-end-insert
      >
        Dismiss
      </button>
    </span>
  </div>
);
~~~~~~~

Wie du siehst, ist es möglich, das style Attribut für HTML-Elemente zu verwenden. JSX übergibt den Stil als Inline-JavaScript-Objekt. So definieren wir dynamische Stileigenschaften in JavaScript-Dateien anstatt in statischen CSS-Dateien. Dieser Ansatz wird als **Inline-Stil** bezeichnet. Verwende den Inline-Stil sparsam, da eine separate Stildefinition dein JSX präziser hält.

Definiere in der Datei *src/App.css* die neuen CSS-Klassen. Erweiterte CSS-Funktionen (zum Beispiel Verschachtelung) von CSS-Erweiterungen (beispielsweise Sass) sind in diesem Beispiel nicht enthalten, da es sich um [optionale Konfigurationen](https://create-react-app.dev/docs/adding-a-sass-stylesheet/) handelt. 

{title="src/App.css",lang="css"}
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

The button style from the previous component is still missing, so we'll define a base button style and two more specific specific button styles (small and large). One of the button specifications has been used, the other will be used in the next steps.

{title="src/App.css",lang="css"}
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

.button_small {
  padding: 5px;
}

.button_large {
  padding: 10px;
}
~~~~~~~

Apart from styling approaches in React, naming conventions ([CSS guidelines](https://developer.mozilla.org/en-US/docs/MDN/Contribute/Guidelines/Code_guidelines/CSS)) are a whole other topic. The last CSS snippet followed BEM rules by defining modifications of a class with an underscore (`_`). Choose whatever naming convention suits you and your team. Without further ado, we will style the next React component:

{title="src/App.js",lang="javascript"}
~~~~~~~
const SearchForm = ({ ... }) => (
# leanpub-start-insert
  <form onSubmit={onSearchSubmit} className="search-form">
# leanpub-end-insert
    <InputWithLabel ... >
      <strong>Search:</strong>
    </InputWithLabel>

    <button
      type="submit"
      disabled={!searchTerm}
# leanpub-start-insert
      className="button button_large"
# leanpub-end-insert
    >
      Submit
    </button>
  </form>
);
~~~~~~~

We can also pass the `className` attribute as a prop to React components. For example, we can use this option to pass the SearchForm component a flexible style with a `className` prop from a range of predefined classes from a CSS file. Lastly, style the InputWithLabel component:

{title="src/App.js",lang="javascript"}
~~~~~~~
const InputWithLabel = ({ ... }) => {
  ...

  return (
    <>
# leanpub-start-insert
      <label htmlFor={id} className="label">
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
        className="input"
# leanpub-end-insert
      />
    </>
  );
};
~~~~~~~

In your *src/App.css* file, add the remaining classes:

{title="src/App.css",lang="css"}
~~~~~~~
.search-form {
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

For simplicity, we styled elements like label and input individually in the *src/App.css* file. However, in a real application it may be better to define these elements once in the *src/index.css* file globally. As React components are split into multiple files, sharing style becomes a necessity.

This is the basic CSS most of us have already learned, written with an inline style that is more dynamic. Without CSS extensions like Sass (Syntactically Awesome Style Sheets) inline styles can become burdensome, though, because features like CSS nesting are not available in native CSS.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/CSS-in-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/react-modern-final...hs/CSS-in-React?expand=1).
* Lese mehr zum Thema [CSS stylesheets in create-react-app](https://create-react-app.dev/docs/adding-a-stylesheet).
* Lese mehr zum Thema [Sass in create-react-app](https://create-react-app.dev/docs/adding-a-sass-stylesheet) for taking advantage of more advanced CSS features like nesting.
* Try to pass `className` prop from App to SearchForm component, either with the value `button_small` or `button_large` and use this as `className` for the button element.