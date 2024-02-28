# Laravel Vue

Eine Vorlage für die Verwendung von Laravel mit vue.js.

## Vorbereiten

### 1. Repository herunterladen oder Projekt selbst einrichten

#### 1. Herunterladen

Lade das Repository auf Deinen Computer herunter, benenne es um und verschiebe es in Deinen Projektordner.

#### 2. Selber einrichten

Wenn Du selber alles einrichten willst, um einen genaueren Überblick zu erhalten, kannst Du Dir die [Installation.md](https://github.com/opportunity-zh/laravel-vue/blob/main/Installation.md) anschauen und die Schritte selber durchgehen.

### 2. Dependencies installieren

Öffne das Projekt in VS Code, öffne das Terminal und verwende den folgedenden Befehl um alle Dependencies (Packages) zu installieren.

```bash
npm install
```

### 3. Environment Variablen anpassen

Nimm das **.env.example** File und benenne es um in **.env**. Wenn Du es öffnest, siehst Du, dass der **APP_KEY noch leer** ist. Mit folgendem Befehl kannst Du dir einen Key generieren, damit alles funktioniert.

```bash
php artisan key:generate
```

## Starten

### 1. Docker Container hochfahren

Wenn Du in Laravel mit Docker arbeitest, kannst du statt dem Befehl docker compose den Befehl './vendor/bin/sail up' verwenden. Evtl. ist auf Deinem Computer bereits ein alias hinzugefügt worden, deshalb kannst Du die Kurzform davon verwenden:

```bash
sail up
```

### 2. NPM Server starten

```bash
npm run dev
```

### 3. Website aufrufen

Unter localhost kannst Du nun die Website anschauen.

```bash
http://localhost
```

### 4. PHPMyAdmin aufrufen

Unter localhost:8080 findest Du PHPMyAdmin. Logindaten findest Du im .env File.

```bash
http://localhost:8080
```

## Fehlerbehebung

### 1. Ports besetzt

Das kann passieren, wenn die Ports, die Docker in den Containern benutzen will, diese jedoch vom System bereits besetzt sind.

Beispielsweise: listen tcp4 0.0.0.0:80: bind: address already in use

1. Prozess finden
   Dann musst Du herausfinden, von welchem Prozess diese verwendet werden und diesen Prozess dann beenden. Das machst Du mit folgenden Befehlen. Ersetze dabei **PORT** durch den besetzten Port. Im oberen Beispiel wäre das **80**

```bash
sudo netstat -laputen | grep ':PORT'
```

2. Prozess beenden
   Wenn Du den Prozess gefunden hast, welcher den Port besetzt, findest Du neben dem Namen des Prozesses eine Zahl, welches die **Prozess-ID** ist. Den Prozess kannst Du mit folgendem Befehl beenden. Ersetze <id> mit der tatsächlichen Prozess-ID

```bash
sudo kill <id>
```

Wenn Du das gemacht hast, verwenden zuerst den folgenden Befehl und versuche es erst dann wieder mit sail up

```bash
sail down
```

### 2. Dockerprobleme

Wenn Du Probleme mit Docker bzw. noch laufenden Containern hast, kannst Du diese Container beenden und auch direkt aus dem ... löschen.

Das machst Du mit folgenden Befehlen

1. Alle laufenden Container beenden

```bash
docker stop $(docker ps -a -q)
```

2. Alle gestoppten Container entfernen

```bash
docker rm $(docker ps -a -q)
```

### Storage Folder Permission Problem

1. Versuche sail up ohne cache zu starten

```bash
sail build --no-cache
```

Falls das nicht funktioniert, versuche die Berechtigungen des storage Ordners zu ändern. Im Projektordner:

```bash
sudo chmod -R 777 storage
```
