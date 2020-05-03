## Bibliotheken von Drittanbietern in React

Wir haben zuvor die native Fetch-API eingeführt, um die Hacker News-API abzufragen. Diese wird nicht von allen Browsern unterstützt. Sobald du deine Anwendung in einer [Headless-Browser-Umgebung](https://en.wikipedia.org/wiki/Headless_browser) testest, werden Probleme mit der Fetch-API auftreten. Es gibt verschiedene Möglichkeiten, das Abrufen in älteren Browsern mithilfe von ([Polyfills](https://de.wikipedia.org/wiki/Polyfill)) und in Tests (somorphic fetch) zum Laufen zu bringen. Die genauere Erklärung der Konzepte würde den Rahmen dieses Buchs sprengen.

Eine Alternative besteht darin, die native Fetch-API durch eine stabile Bibliothek wie [axios](https://github.com/axios/axios) zu ersetzen, welche asynchrone Anforderungen an Remote-APIs ausführt. In diesem Abschnitt lernst du, wie du eine Bibliothek - in unserem Fall eine native API des Browsers - durch ein Node.js-Module ersetzt. Installiere zunächst axios mithilfe der Befehlszeile:

{title="Command Line",lang="text"}
~~~~~~~
npm install axios
~~~~~~~

Second, import axios in your App component's file:
Importieren danach Axios in deine App-Komponente:

{title="src/App.js",lang="javascript"}
~~~~~~~
import React from 'react';
# leanpub-start-insert
import axios from 'axios';
# leanpub-end-insert

...
~~~~~~~

You can use `axios` instead of `fetch`, and its usage looks almost identical to the native fetch API. It takes the URL as an argument and returns a promise. You don't have to transform the returned response to JSON anymore, since axios wraps the result into a data object in JavaScript. Just make sure to adapt your code to the returned data structure:

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleFetchStories = React.useCallback(() => {
    dispatchStories({ type: 'STORIES_FETCH_INIT' });

# leanpub-start-insert
    axios
      .get(url)
# leanpub-end-insert
      .then(result => {
        dispatchStories({
          type: 'STORIES_FETCH_SUCCESS',
# leanpub-start-insert
          payload: result.data.hits,
# leanpub-end-insert
        });
      })
      .catch(() =>
        dispatchStories({ type: 'STORIES_FETCH_FAILURE' })
      );
  }, [url]);

  ...
};
~~~~~~~

In this code, we call axios `axios.get()` for an explicit [HTTP GET request](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/GET), which is the same HTTP method we used by default with the browser's native fetch API. You can use other HTTP methods such as HTTP POST with `axios.post()`as well.

We can see with these examples that axios is a powerful library for performing requests to remote APIs. I recommend over the native fetch API when requests become complex, working with older browser, or for testing.

### Übungen:

* Begutachte den [Quellcode dieses Abschnittes](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/Third-Party-Libraries-in-React).
  * Bestätige die [Änderungen gegenüber dem letzten Abschnitt](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Explicit-Data-Fetching-with-React...hs/Third-Party-Libraries-in-React?expand=1).
* Informiere dich über [beliebte Bibliotheken in React](https://www.robinwieruch.de/react-libraries).
* Lese [warum Frameworks wichtig sind](https://www.robinwieruch.de/why-frameworks-matter).
* Lese mehr zum Thema [axios](https://github.com/axios/axios).