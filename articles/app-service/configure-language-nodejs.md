---
title: Konfigurowanie aplikacji Node.js systemu Windows
description: Dowiedz się, jak skonfigurować aplikację Node.js w natywnych wystąpieniach systemu Windows App Service. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 9f4ccdd04b8d57784f452dc28fa4507fb7ea94c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908146"
---
# <a name="configure-a-windows-nodejs-app-for-azure-app-service"></a>Konfigurowanie aplikacji Node.js systemu Windows dla Azure App Service

Aplikacje Node.js muszą być wdrożone ze wszystkimi wymaganymi zależnościami NPM. Aparat wdrażania App Service jest automatycznie uruchamiany `npm install --production` podczas wdrażania [repozytorium git](deploy-local-git.md)lub [pakietu zip](deploy-zip.md) z włączonym automatyzacją kompilacji. W przypadku wdrażania plików przy użyciu [protokołu FTP/S](deploy-ftp.md)należy jednak ręcznie przekazać wymagane pakiety. Aby uzyskać informacje na temat aplikacji systemu Linux, zobacz [Konfigurowanie aplikacji PHP w systemie Linux dla Azure App Service](containers/configure-language-nodejs.md).

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dla Node.js deweloperów, którzy wdrażają program w celu App Service. Jeśli nie korzystasz z Azure App Service, postępuj zgodnieNode.js z przewodnikiem [Szybki Start](app-service-web-get-started-nodejs.md) i [Node.js z samouczkiem MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md) .

## <a name="show-nodejs-version"></a>Pokaż wersję Node.js

Aby wyświetlić bieżącą wersję Node.js, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Aby wyświetlić wszystkie obsługiwane wersje Node.js, uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

## <a name="set-nodejs-version"></a>Ustaw wersję Node.js

Aby ustawić [obsługiwaną wersję Node.js](#show-nodejs-version), uruchom następujące polecenie w [Cloud Shell](https://shell.azure.com) , aby ustawić `WEBSITE_NODE_DEFAULT_VERSION` obsługiwaną wersję:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

To ustawienie określa wersję Node.js, która ma być używana zarówno w czasie wykonywania, jak i podczas zautomatyzowanego przywracania pakietów podczas App Service automatyzacji kompilacji.

> [!NOTE]
> Należy ustawić wersję Node.js w projekcie `package.json` . Aparat wdrażania działa w osobnym procesie zawierającym wszystkie obsługiwane wersje Node.js.

## <a name="access-environment-variables"></a>Uzyskiwanie dostępu do zmiennych środowiskowych

W App Service można [ustawić ustawienia aplikacji](configure-common.md) poza kodem aplikacji. Następnie możesz uzyskać do nich dostęp przy użyciu standardowego wzorca Node.js. Aby na przykład uzyskać dostęp do ustawienia aplikacji o nazwie `NODE_ENV`, użyj następującego kodu:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Uruchom grunt/Bower/Gulp

Domyślnie program App Service Build Automation jest uruchamiany, `npm install --production` gdy rozpoznaje aplikację Node.js wdrożoną za pomocą usługi git (lub wdrożenie zip z włączonym automatyzacją kompilacji). Jeśli aplikacja wymaga dowolnego ze popularnych narzędzi do automatyzacji, takich jak grunt, Bower lub Gulp, należy podać [niestandardowy skrypt wdrożenia](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) , aby go uruchomić.

Aby umożliwić repozytorium uruchamianie tych narzędzi, należy dodać je do zależności w *package.js.* Przykład:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

W oknie terminalu lokalnego Zmień katalog na katalog główny repozytorium i uruchom następujące polecenia:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Katalog główny repozytorium ma teraz dwa dodatkowe pliki: *. Deployment* i *Deploy.sh*.

Otwórz *Deploy.sh* i Znajdź `Deployment` sekcję, która wygląda następująco:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Ta sekcja kończy się z uruchomionym programem `npm install --production` . Dodaj sekcję kodu, która będzie potrzebna do uruchomienia wymaganego narzędzia *na końcu* `Deployment` sekcji:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Zapoznaj się z [przykładem w przykładzie MEAN.js, w](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)którym skrypt wdrażania również uruchamia `npm install` polecenie niestandardowe.

### <a name="bower"></a>Bower

Ten fragment kodu jest uruchamiany `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Ten fragment kodu jest uruchamiany `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Ten fragment kodu jest uruchamiany `grunt` .

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

W usłudze App Service [kończenie żądań SSL](https://wikipedia.org/wiki/TLS_termination_proxy) odbywa się w modułach równoważenia obciążenia sieciowego, dzięki czemu wszystkie żądania HTTPS docierają do aplikacji jako niezaszyfrowane żądania HTTP. Jeśli logika aplikacji musi sprawdzać, czy żądania użytkownika są szyfrowane, czy nie, zbadaj nagłówek `X-Forwarded-Proto`.

Popularne platformy internetowe umożliwiają dostęp do informacji `X-Forwarded-*` w standardowym wzorcu aplikacji. W programie [Express](https://expressjs.com/)można używać [zaufanych serwerów proxy](https://expressjs.com/guide/behind-proxies.html). Przykład:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Gdy działająca aplikacja Node.js działa inaczej w App Service lub zawiera błędy, spróbuj wykonać następujące czynności:

- [Dostęp do strumienia dzienników](#access-diagnostic-logs).
- Przetestuj aplikację lokalnie w trybie produkcyjnym. App Service uruchamia aplikacje Node.js w trybie produkcyjnym, dlatego należy się upewnić, że projekt działa zgodnie z oczekiwaniami w trybie produkcyjnym lokalnie. Przykład:
    - W zależności od *package.jsw systemie*można zainstalować różne pakiety dla trybu produkcyjnego ( `dependencies` vs. `devDependencies` ).
    - Niektóre platformy sieci Web mogą wdrażać pliki statyczne inaczej w trybie produkcyjnym.
    - Niektóre platformy sieci Web mogą używać niestandardowych skryptów uruchamiania podczas pracy w trybie produkcyjnym.
- Uruchom aplikację w App Service w trybie tworzenia. Na przykład w [MEAN.js](https://meanjs.org/)można ustawić aplikację na tryb programistyczny w czasie wykonywania, [ustawiając `NODE_ENV` ustawienie aplikacji](configure-common.md).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Node.js aplikacji za pomocą MongoDB](app-service-web-tutorial-nodejs-mongodb-app.md)

