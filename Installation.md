# Laravel und Vue

Eine Installationsanleitung um Laravel und vue.js gemeinsam zu nutzen. Laravel dient dabei als Backend und kann über eine API mit vue.js verwendet werden.

## Installation

### 1. Laravel mit Sail installieren

Um Laravel für die Verwendung mit Docker (Laravel Sail) zu verwenden, kannst Du den folgenden Befehl in Dein Terminal kopieren und ausführen lassen. Navigiere zuerst in Deinen Projektordner. Der lettze Teil der URL (Hier: laravel-vue) ist nach dem Ausführen des Befehls der name Deines Projekts. Du kannst das beliebig ändern.

```bash
curl -s https://laravel.build/laravel-vue | bash
```

1.2. Öffne das Projekt in VS Code  
1.3. Öffne das Terminal in VS Code  
1.4. Verwende den Befehl npm install um alle Dependencies zu installieren

### 2. Vuejs installieren

2.1. Zuerst kopiere folgenden Befehl ins Terminal

```bash
npm install vue@latest vue-router@4 @vitejs/plugin-vue
```

2.2. Öffne das File **vite.config.js** im Rootfolder  
2.3. Ersetze den Code mit folgendem

```javascript
import { defineConfig } from "vite";
import laravel from "laravel-vite-plugin";
import vue from "@vitejs/plugin-vue";

export default defineConfig({
    plugins: [
        vue(),
        laravel({
            input: ["resources/css/app.css", "resources/js/app.js"],
            refresh: true,
        }),
    ],
    resolve: {
        alias: {
            vue: "vue/dist/vue.esm-bundler.js",
        },
    },
});
```

2.4. Gehe in den Ordner **./resources/js**  
2.5. Erstelle ein File mit dem Namen **App.vue** und füge diesen Code hinzu:

```html
<template>
    <h1>Opportunity rocks! 🤘</h1>
</template>
```

2.6. Öffne im selben Ordner **app.js** und füge folgendes hinzu (Dieser Code rendert die Vue-App in das index.html File)

```javascript
import "./bootstrap";
import { createApp } from "vue";

import App from "./App.vue";

createApp(App).mount("#app");
```

2.7. Öffne **./resources/views/welcome.blade.php**, **lösche den Inhalt**, erstelle eine HTML Grundstruktur, füge folgendes in den Body-Tag

```html
<div id="app"></div>
```

2.8. Füge folgendes Snippet in den Headtag

```html
@vite(['resources/js/app.js'])
```

### 3. Starte Sail (Docker) mit folgendem Befehl

```bash
sail up
```

3.1. Wenn die Container nicht laufen, gehe zu Fehlerbehebungen

### 4. Starte NPM mit dem Befehl

```bash
npm run dev
```

### 5. Öffne den Browser und besuche localhost (ohne Port)

```bash
http://localhost
```

### 6. Router hinzufügen

Das Routing wird momentan noch von Laravel übernommen. Wir wollen jedoch eine SPA erstellen und dass das Routing von vue.js gemacht wird.

6.1. Gehe zu **./resources/js** und erstelle ein File mit dem Namen **router.js**  
6.2. Kopiere den folgenden Code hinein

```javascript
import { createRouter, createWebHistory } from "vue-router";

const routes = [];

export default createRouter({
    history: createWebHistory(),
    routes,
});
```

### 7. Importiere den Router innerhalb von **./resources/app.js**

```javascript
import router from "./router";
```

7.1 Und füge ihn folgendermassen zum **createApp()** Statement hinzu. Damit teilst Du vue mit, dass Du in Deiner App den Router verwenden willst.

```javascript
createApp(App).use(router).mount("#app");
```

### 8. Erstelle im **./resources** Ordner einen neuen Ordner mit dem Namen **pages**

### 9. Füge zwei Files im neu erstellten Ordner hinzu: **Home.vue und Test.vue**

### 10. Füge folgendes in die **Home.vue** File ein:

```html
<template>
    <div>
        <h1>HOME</h1>
        <router-link to="/test">Link zur Test Seite</router-link>
    </div>
</template>
```

### 11. Füge folgendes in die **Test.vue** File ein:

```html
<template>
    <h1>TESTSEITE</h1>
    <router-link to="/">Link zur Home Seite</router-link>
</template>
```

### 12. Überprüfe,

ob alles funktioniert, wie es soll. Du solltest nun zwischen den beiden Seiten hin und her navigieren können

### 13. Ersetze

den Inhalt von **./resources/router.js** mit folgendem Code

```javascript
import { createRouter, createWebHistory } from "vue-router";

const routes = [
    {
        path: "/",
        component: () => import("./pages/HomeRoute.vue"),
    },
    {
        path: "/test",
        component: () => import("./pages/TestRoute.vue"),
    },
];

export default createRouter({
    history: createWebHistory(),
    routes,
});
```

### 14. Passe App.vue an

Ersetze den Inhalt in **App.vue** mit folgendem Code. Mit diesem Snippet wird automatisch die korrekte Komponente, die in **router.js** definiert wurde geladen

```html
<template>
    <router-view v-slot="{ Component, route }">
        <div :key="route.name">
            <Component :is="Component" />
        </div>
    </router-view>
</template>
```

### 15. Ändere ./routes/web.php

Füge folgenden Code unter **./routes/web.php** ein und ersetze damit alles, was darin steht. Dieses Snippet stellt sicher, dass das Routing von vue.js übernommen wird.

```php
<?php
use Illuminate\Support\Facades\Route;

Route::get('/{vue_capture?}', function () {
    return view('index');
})->where('vue_capture', '[\/\w\.-]*');
```

### 16. Öffne **./routes/api.php**

Hier werden die API Routes verwaltet. Ersetze alles, was bereits darin steht mit folgendem:

```php
<?php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;

Route::get("/test-me", function () {
    return 'Hallo vom Laravel Backend!';
});
```

### 17. Home.vue anpassen

Füge folgendes in **Home.vue** hinzu um zu testen, ob alles funktioniert (Wenn Du den Button klickst, lädt es Dein **Return Statement** aus **./routes/api.php**)

```javascript
<template>
    <div>
        <h1>HOME</h1>
        <router-link to="/test">Link zur Test Seite</router-link>
        <button @click.prevent="getValue">Endpoint aufrufen</button>
        <p v-if="response">{{ response.data }}</p>
    </div>
</template>

<script setup>
    import axios from "axios";
    import { ref } from "vue";

    const response = ref();

    const getValue = async () => {
        try {
            response.value = await axios.get("/api/test-me");
        } catch (error) {
            // Do something with the error
            console.log(error);
        }
    };
</script>
```

### 18. Abschluss

Du hast das Setup nun abgeschlossen und Deine Applikation ist bereit um mit vue.js im Frontend und Laravel im Backend zu arbeiten.

## Quellen

-   https://laravel.com/docs/10.x/installation#sail-on-linux
-   https://vueschool.io/articles/vuejs-tutorials/the-ultimate-guide-for-using-vue-js-with-laravel/
