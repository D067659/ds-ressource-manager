# ds-ressource-manager
## Test
### Specific hierarchy test
### Dropdown Test setup
  
<details><summary>Code</summary>

$input = '<script>alert(1);</script>';
echo htmlspecialchars($input);
echo htmlentities($input);
?>

</details>

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

### Deploymnet
The DS Ressource Manager is hosted on a free heroku plan.
The file ```static.json``` provides an entry point. Note that the ```index2.html``` does not exist, thereby the main vue file is loaded.
Check out the live heroku app [here](https://ds-resource-manager.herokuapp.com/).
