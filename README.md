# ds-ressource-manager
## Test
### Specific hierarchy test
#### Dropdown Test setup
  
<details>
<summary>One Last Test</summary>
<br>
Its me, Mario!
<pre>{
   "url_api_detail":"http://127.0.0.1:8001/services/manage_apis/ /",
   "id":" ",
   "function_name":"stock_price",
   "name":"Yahoo Finance (stock/get-detail by Name)",
   "url":"https://apidojo-yahoo-finance-v1.p.rapidapi.com/stock/get-detail",
   "header":{
      "x-rapidapi-key": FOR SECURITY REASONS THE KEY IS SENT VIA PRIVATE CHAT,
      "x-rapidapi-host":"apidojo-yahoo-finance-v1.p.rapidapi.com"
   },
   "request_params_template":{
      " lang":"en",
      "region":"US",
      "symbol":"§1§"
   },
   "request_body_template":{

   },
   "response_result_path":"price.regularMarketOpen.raw",
   "request_method":"GET",
   "priority":1,
   "enabled":true,
   "placeholders":[
      {
         "id":1,
         "value":{
            "function_name":"retrieve_company_symbol",
            "apply_function":true,
            "function_fields":[
               {
                  "name":"search_name",
                  "value":"§company_name§"
               }
            ]
         },
         "replace_as_string":true
      }
   ]
}
</pre>
</details>

#### Another one
Another one

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
