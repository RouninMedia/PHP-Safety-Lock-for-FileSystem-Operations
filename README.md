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
  updateFilesNowURL += '?data-object='.JSON.stringify(myDataObject);
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

### Autumn 2014

### Spring 2019
