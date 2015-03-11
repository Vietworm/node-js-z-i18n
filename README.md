# Multi language z-i18n

* Download and install test project at (New version 1.0.2): https://github.com/kimthangatm/test-node-js-z-i18n/
* Translation module with dynamic json storage and suport npm: express, z-form...
* When APP Node JS firt run, translation loaded loaded from file or Redis and never reload => Fast

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
  "hello_user_name" : "Hallo, %s %s jaar oud"
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

redis.get(languageRedisCache, function(error, result){
    if(result == null){
        i18n.add('languages/nl_NL/moduleA.nl_NL.json', 'nl_NL');
        console.log(i18n.getTranslation());
        i18n.add('languages/en_GB/moduleA.en_GB.json', 'en_GB');
        console.log(i18n.getTranslation());
        global._t = i18n.__;
        //global._ = i18n.__;

        redis.set(languageRedisCache, JSON.stringify(i18n.getTranslation()), redis.print);
    }else{
        i18n.setTranslation(result);
        global._t = i18n.__;
        //global._ = i18n.__;
    }
});

//Use middleware to set current language
//Eg: Now I use basic not use middleware
var userA = {
    name :' ZaiChi',
    language : 'nl_NL'
};
//=>
i18n.setCurrentLang(userA.language);
```

### Code Router: project_folder/routes/index.js

```
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function (req, res, next) {
    var hello = _t('hello_user_name', 'ZaiChi', 25);
    //var hello = _('hello_user_name', 'ZaiChi', 25);
    res.render('index', {title: 'Express', hello : hello});
});

module.exports = router;
```

### Code View: project_folder/views/index.jade
```
extends layout
block content
    h1= title
    p Welcome to #{title}

    p "hello_user_name = Hallo, %s %s jaar oud"
    p "var hello = _t('hello_user_name', 'ZaiChi', 26);"
    p "============================"
    p Result => #{hello}
  
```

### Result

```
Express

Welcome to Express

"hello_user_name = Hallo, %s %s jaar oud"

"var hello = _t('hello_user_name', 'ZaiChi', 25);"

"============================"

Result => Hallo, ZaiChi 25 jaar oud
```

