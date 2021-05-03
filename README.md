# PHP Safety Lock for FileSystem Operations
The **PHP Safety Lock** ensures that whenever a page including a FileSystem Operations PHP script loads or reloads, that script cannot be initiated accidentally and no files within the filesystem can be *accidentally* overwritten or deleted.

## How the PHP Safety Lock works

The **PHP Safety Lock** hinges on the use of the PHP `header()` function:

    header('Location: https://mydomain.com/my/path/?my-query-string=my-data');

If **PHP** detects a ***specific*** `$_GET` parameter in the `queryString` or `$_POST` parameter in the document headers (inserted, in either case, by **Javascript**) the PHP Script will run its *FileSystem Operations* and *then* send a **raw HTTP Redirect Header** to the client, redirecting the client request to a the same URL but *without* that specific `$_GET` or `$_POST` parameter.

This means the interstitial URL at which the *FileSystem Operations* are run is ***never seen***.

The `$_GET` or `$_POST` parameter is added by **Javascript**.

After the *FileSystem Operations* have run, the same `$_GET` or `$_POST` parameter is removed by **PHP**.

At no point does the `$_GET` or `$_POST` parameter ever appear, visibly, in the `queryString` in the URL bar or anywhere else.

This prevents the *FileSystem Operations* from ever being run accidentally.

_______

## Example of the PHP Safety Lock using the `GET` parameter: `updateFilesNow=true`

```html
<!-- HTML -->

<button type="button" class="my-action-button">Action Button</button>
```

```javascript
// JAVASCRIPT (at bottom of file)

const myActionButton = document.getElementsByClassName('my-action-button')[0];

const updateFiles = () => {

  // SET GET PARAMETERS
  let updateFilesNowURL = '';
  updateFilesNowURL += window.location.href.split('?')[0];
  updateFilesNowURL += '?';
  updateFilesNowURL += 'data-object-1='.JSON.stringify(myDataObject1);
  updateFilesNowURL += '&';
  updateFilesNowURL += 'data-object-2='.JSON.stringify(myDataObject2);
  updateFilesNowURL += '&';
  updateFilesNowURL += 'updateFilesNow=true';

  window.location.href = updateFilesNowURL;
}

myActionButton.addEventListener('click', updateFiles, false);
```

```php
// PHP (at top of file)

if ((isset($_GET['updateFilesNow'])) && ($_GET['updateFilesNow'] === 'true')) {

  /* [... PHP CODE UPDATES FILES HERE...] */

  $Protocol = 'https://';
  $Domain = $_SERVER['HTTP_HOST'];
  $Path = $_SERVER['SCRIPT_NAME'];
  $Query_String = str_replace('update-files', 'confirm-updates', $_SERVER['QUERY_STRING']);
  $Query_String = explode('&updateFilesNow', $Query_String)[0];

  header('Location: '.$Protocol.$Domain.$Path.$Query_String);
}

```

_______

## Example of the PHP Safety Lock using the `POST` parameter: `updateFilesNow=true`

```html
<!-- HTML -->

<button type="button" class="my-action-button">Action Button</button>
```

```javascript
// JAVASCRIPT (at bottom of file)

const myActionButton = document.getElementsByClassName('my-action-button')[0];

const updateFiles = () => {
  
  // SET GET PARAMETERS
  let updateFilesNowURL = '';
  updateFilesNowURL += window.location.href.split('?')[0];
  updateFilesNowURL += '?';
  updateFilesNowURL += 'appView=update-files';  

  // CREATE HTML FORM
  const form = document.createElement('form');
  const method = 'post';
  const action = updateFilesNowURL;
  Object.assign(form, {method, action});

  // SET POST PARAMETERS
  const updateFilesNow = 'true';
  const data = {dataObject1, dataObject2, updateFilesNow};

  for (let entry in data) {

    let input = document.createElement('input');
    input.setAttribute('name', entry);
    input.setAttribute('value', data[entry]);
    form.appendChild(input);
  }

  document.body.appendChild(form);
  form.submit();
}

myActionButton.addEventListener('click', updateFiles, false);
```

```php
// PHP (at top of file)

if (($_GET['appView'] === 'update-files) && (isset($_POST['updateFilesNow'])) && ($_POST['updateFilesNow'] === 'true')) {

  /* [... PHP CODE UPDATES FILES HERE...] */
  
  $Protocol = 'https://';
  $Domain = $_SERVER['HTTP_HOST'];
  $Path = $_SERVER['SCRIPT_NAME'];
  $Query_String = str_replace('update-files', 'confirm-updates', $_SERVER['QUERY_STRING']);
  
  unset($_POST['updateFilesNow']);
  header('HTTP/1.1 307 Temporary Redirect');
  header('Location: '.$Protocol.$Domain.$Path.'?'.$Query_String);
}

```

______

## Evolution of the PHP Safety Lock

### Spring 2014

In May 2014, having learned about PHP Filesystem functions in late summer 2013, and made some tentative progress during early Autumn 2013, I set about writing a series of self-contained FileSystem Management tools, accessible through the browser, intended to create, update and delete pages, sitemaps etc. on **attitudeTravel Mobile**. Each tool comprised a form with an `action` attribute. Clicking the `<input type="submit" />` at the bottom of the form sent the browser to *the new URL* declared by the `action` attribute, which executed the PHP Filesystem functions.

### Autumn 2014

In October 2014, I set about building the **Destinations Generator** (my most advanced tool yet for **attitudeTravel Mobile**). I was distantly familiar with the term ***Single** Page App* (though uncomprehending of what it actually meant or entailed) and it struck me that rather than the `action` attribute of the form declaring a *new URL*, it might declare the *current URL* (via: `action="http://'.$_SERVER['HTTP_HOST'].$_SERVER['SCRIPT_NAME']"`).

Then, the app's state of progress might be tracked via `$_POST` variables which PHP could read. (I was clearly happy with this approach because I used a more elaborate variant of the same pattern in Jan 2016, when building the original single-file-setup for the *website initialiser* which evolved into **CMS3**). 

### Spring 2019

In late April 2019, I set out to write a PHP Filesystem Script which would edit *all* **Ashiva PageManifests** *en bloc*, so that I no longer had to edit and upload each *PageManifest* individually and manually.

See: [**Kubaru**](https://github.com/RouninMedia/kubaru) \[**配る**\] - the Japanese verb for *to deal (cards)*

**N.B.** The script was only half-built and lacked a UI, so the edits needed to be hard-coded into the script.

I needed for the script to not run by accident if ever I shut down and restarted the browser and then inadvertently focused on the tab.

This time, rather than signalling the state using `$_POST` variables (which are harder for a human user to verify), I used `$_GET` variables (ie. the `queryString`) to indicate one of two states:

  1. *initial*
  2. *ready-to-run*

The script's *initial* state would display a **Run Kubaru** button, which, when pressed, would run the **Kubaru** script.

The script's *ready-to-run* state would display a different button, **Initialise** instead (which, when pressed, would return the app to its initial state) ***but before that*** it would **also** run the **Kubaru** script.

The idea was that the user would:

 - press the **Run Kubaru** button
 - run the **Kubaru** script
 - read the results to confirm the script had run normally
 - *then* press the **Initialise** button to return the app to its *initial* state where it couldn't automatically run the script

Of course this setup *very much depended* on the user pressing the **Initialise** button and *not* leaving the app in a *live **ready-to-run** state*.

And... this is where the **PHP Safety Lock for FileSystem Operations** comes in.

### Spring 2021

In **April 2021**, I developed two versions of the **PHP Safety Lock for FileSystem Operations**:

 - one straightforwardly using `GET` parameters in the `queryString`
 - the other using `POST` parameters and maintaining whichever `POST` parameters are still needed using `307` and `308` HTTP Redirects 
