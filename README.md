# PHP Safety Lock for FileSystem Operations
The **PHP Safety Lock** ensures that whenever a page with a PHP script including FileSystem Operations loads or reloads, no files within the filesystem can be *accidentally* overwritten or deleted.

## How the PHP Safety Lock works

## Example of the PHP Safety Lock

```html
<!-- HTML -->

<button type="button" class="my-action-button">Action Button</button>
```

```javascript
// JAVASCRIPT (at bottom of file)

const myActionButton = document.getElementsByClassName('my-action-button')[0];

const updateFiles = () => {

  let updateFilesNowURL = '';
  updateFilesNowURL += window.location.href.split('?')[0];
  updateFilesNowURL += '?';
  updateFilesNowURL += 'data-object-1='.JSON.stringify(myDataObject1);
  updateFilesNowURL += 'data-object-2='.JSON.stringify(myDataObject2);
  updateFilesNowURL += '&updateFilesNow=true';

  window.location.href = updateFilesNowURL;
}

myActionButton.addEventListener('click', updateFiles, false);
```

```php
// PHP (at top of file)

if ((isset($_GET['updateFilesNow'])) && ($_GET['updateFilesNow'] === 'true')) {

  /* [... FILE UPDATING CODE HERE...] */

  $Protocol = 'https://';
  $Domain = $_SERVER['HTTP_HOST'];
  $Path = $_SERVER['SCRIPT_NAME'];
  $Query_String = explode('&updateFilesNow', $_SERVER['QUERY_STRING'])[0];

  header('Location: '.$Protocol.$Domain.$Path.$Query_String);
}

```
______

## Evolution of the PHP Safety Lock

### Spring 2014

In May 2014, having learned about PHP Filesystem functions in late summer 2013, and made some tentative progress during early Autumn 2013, I set about writing a series of self-contained FileSystem Management tools, accessible through the browser, intended to create, update and delete pages, sitemaps etc. on **attitudeTravel Mobile**. Each tool comprised a form with an `action` attribute. Clicking the `<input type="submit" />` at the bottom of the form sent the browser to *the new URL* declared by the `action` attribute, which executed the PHP Filesystem functions.

### Autumn 2014

In October 2014, while building the **Destinations Generator** for **attitudeTravel Mobile** (my most advanced tool yet) and being distantly familiar with the term ***Single** Page App* (though uncomprehending of what it actually meant or entailed), it struck me that rather than the `action` attribute of the form declaring a *new URL*, it might declare the *current URL* (via: `action="http://'.$_SERVER['HTTP_HOST'].$_SERVER['SCRIPT_NAME']"`).

Then, the app's state of progress might be tracked via `$_POST` variables which PHP could read. (I was clearly happy with this approach because I used a more elaborate variant of the same pattern in Jan 2016, when building the original single-file-setup for the *website initialiser* which evolved into **CMS3**). 

### Spring 2019

I set out to write a PHP Filesystem Script which would edit *all* **Ashiva PageManifests** *en bloc*, so that I no longer had to edit and upload each *PageManifest* individually and manually.

**Kubaru** \[**配る**\] - the Japanese verb for *to deal (cards)*
