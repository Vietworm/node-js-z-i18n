# Multi language z-i18n

Translation module with dynamic json storage and suport npm: express, z-form...

# Install z-i18n

```
sudo npm install z-i18n
```

# Default language

* Current language: `current_lang : en_GB`
* Default language: `default_lang : en_GB`

# Express with Redis

### Languages folder/content sample

* Folder construct
```
project_name/
└── languages
    ├── en_GB
    │   └── moduleA.en_GB.json
    └── nl_NL
        └── moduleA.nl_NL.json
```

* Content `languages/en_GB/moduleA.en_GB.json`
```
{
  "m_user_name_label" : "User name",
  "m_user_name" : "Enter your user name!",
  "hello_user_name" : "Hello, %s %s year old"
}
```

* Content `languages/nl_NL/moduleA.nl_NL.json`
```
{
    "m_user_name_label" : "Gebruikersnaam",
    "m_user_name" : "Voer uw gebruikersnaam!",
    "hello_user_name" : "Hallo,% s% s jaar oud"
}
```

### Code: project_folder/app.js

```
var app = express();

// Install Redis: sudo npm install redis
// Require redis
var redis = require('redis').createClient();

// Require z-i18n
var i18n = require('z-i18n');

// Set current 
i18n.init({current_lang : 'en_GB'}); //default_lang = en_GB

// Or set current_lang/default_lang
// i18n.init({current_lang : 'en_GB', default_lang : 'en_GB'});

// Translations into the dutch language.
// i18n.init({current_lang : 'nl_NL', default_lang : 'en_GB'});

var languageRedisCache = 'LANGUAGE_CACHE_REDIS';

redis.get(langRedisCache, function(error, result){
    if(result != null){
        i18n.setTranslation(result);
        global.i18n = i18n;
    }else{
        i18n.add('languages/en_GB/moduleA.en_GB.json', 'en_GB');
        i18n.add('languages/nl_NL/moduleA.nl_NL.json', 'nl_NL');
        global.i18n = i18n;
        redis.set(languageRedisCache, JSON.stringify(i18n.getTranslation()), redis.print)
    }
});
```

### Code Router: project_folder/routes/index.js

```
var hello = i18n.__('hello_user_name', 'ZaiChi', 26);
console.log(hello);

```