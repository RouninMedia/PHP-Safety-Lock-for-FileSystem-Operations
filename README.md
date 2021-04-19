# PHP Safety Lock for FileSystem Operations
The **PHP Safety Lock** ensures that whenever a page with a PHP script including FileSystem Operations loads or reloads, no files within the filesystem can be *accidentally* overwritten or deleted.

## How the PHP Safety Lock works

## Example of the PHP Safety Lock

```html
// HTML

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



```
______

## Evolution of the PHP Safety Lock

### Spring 2014

### Autumn 2014

### Spring 2019
