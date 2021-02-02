---
title: Samouczek — przygotowywanie aplikacji sieci Web dla usług Azure Communications Services (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Dowiedz się, jak utworzyć podstawową aplikację sieci Web, która obsługuje usługi Azure Communications Services
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 01/03/2012
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 60b5a2bf5c0aed3d1a4621e179429a157c2a0962
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99421578"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Samouczek: przygotowywanie aplikacji sieci Web dla usług Azure Communications Services (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Usługi komunikacyjne platformy Azure umożliwiają dodawanie komunikacji w czasie rzeczywistym do aplikacji. W tym samouczku dowiesz się, jak skonfigurować aplikację sieci Web, która obsługuje usługi Azure Communications Services. Jest to samouczek wprowadzający przeznaczony dla nowych deweloperów, którzy chcą zacząć korzystać z komunikacji w czasie rzeczywistym.

Po zakończeniu tego samouczka będziesz mieć podstawową aplikację sieci Web skonfigurowaną z bibliotekami klienckimi usługi Azure Communications Services, których można użyć do rozpoczęcia tworzenia rozwiązania do komunikacji w czasie rzeczywistym.

Możesz odwiedzić stronę [usługi Azure Communication Services w serwisie GitHub](https://github.com/Azure/communication) , aby przekazać opinię.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> * Konfigurowanie środowiska programistycznego
> * Konfigurowanie lokalnego serwera WebServer
> * Dodawanie pakietów usług Azure Communications Services do witryny sieci Web
> * Publikowanie witryny sieci Web w usłudze Azure static websites

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Należy pamiętać, że bezpłatne konto zapewnia $200 na kredyty na korzystanie z platformy Azure w celu wypróbowania dowolnej kombinacji usług.
- [Visual Studio Code](https://code.visualstudio.com/): użyjemy tego do edycji kodu w lokalnym środowisku programistycznym.
- [WebPack](https://webpack.js.org/): zostanie użyty do podzielenia i lokalnego hostowania kodu.
- [Node.js](https://nodejs.org/en/): Ta usługa zostanie użyta do zainstalowania zależności, takich jak biblioteki klienckie usługi Azure Communication Services i pakiet WebPack, oraz zarządzania nimi.
- [NVM i npm](https://docs.microsoft.com/windows/nodejs/setup-on-windows) do obsługi kontroli wersji.
- [Rozszerzenie usługi Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) dla Visual Studio Code. To rozszerzenie jest konieczne do opublikowania aplikacji w usłudze Azure Storage. [Dowiedz się więcej na temat hostowania statycznych witryn sieci Web w usłudze Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website)
- [Rozszerzenie Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Rozszerzenie umożliwia wdrażanie witryn sieci Web (podobnie jak w przypadku poprzedniej), ale z opcją skonfigurowania w pełni zarządzanej ciągłej integracji i ciągłego dostarczania (CI/CD).
- [Rozszerzenie funkcji platformy Azure służące](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) do tworzenia własnych aplikacji bezserwerowych. Na przykład możesz hostować aplikację uwierzytelniania w usłudze Azure Functions.
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../quickstarts/create-communication-resource.md).
- Token dostępu użytkownika. Instrukcje można znaleźć w samouczku [dostęp do przewodnika Szybki Start](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-javascript) lub [zaufanej usługi](https://docs.microsoft.com/azure/communication-services/tutorials/trusted-service-tutorial) .


## <a name="configure-your-development-environment"></a>Konfigurowanie środowiska programistycznego

Lokalne środowisko programistyczne zostanie skonfigurowane w następujący sposób:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Architektura środowiska deweloperskiego":::


### <a name="install-nodejs-nvm-and-npm"></a>Zainstaluj Node.js, NVM i npm

Będziemy używać Node.js, aby pobierać i instalować różne zależności, które są potrzebne dla aplikacji po stronie klienta. Będziemy używać go do generowania plików statycznych, które będą następnie hostowane na platformie Azure, dzięki czemu nie musisz martwić się o konfigurowanie go na serwerze.

Deweloperzy systemu Windows mogą skorzystać z [tego samouczka NodeJS](https://docs.microsoft.com/windows/nodejs/setup-on-windows) , aby skonfigurować węzły, NVM i npm. 

Przetestowano ten samouczek przy użyciu wersji LTS 12.20.0. Po zainstalowaniu programu NVM należy użyć następującego polecenia programu PowerShell, aby wdrożyć wersję, która ma być używana:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Praca z usługą NVM w celu wdrożenia Node.js":::

### <a name="configure-visual-studio-code"></a>Konfigurowanie programu Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/) można pobrać na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Tworzenie obszaru roboczego dla projektów usług Azure Communications Services

Utwórz nowy folder do przechowywania plików projektu, takich jak: `C:\Users\Documents\ACS\CallingApp` . W Visual Studio Code kliknij pozycję "plik", "Dodaj folder do obszaru roboczego" i Dodaj folder do obszaru roboczego.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Tworzenie nowego miejsca pracy":::

Przejdź do okna "Eksplorator" w Visual Studio Code w okienku po lewej stronie, a w obszarze roboczym "bez tytułu" zobaczysz folder "CallingApp".

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Eksplorator":::

Zaktualizuj nazwę obszaru roboczego. Aby sprawdzić wersję Node.js, kliknij prawym przyciskiem myszy folder "CallingApp" i wybierz pozycję "Otwórz w zintegrowanym terminalu".

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Otwieranie terminalu":::

W terminalu wpisz następujące polecenie, aby sprawdzić, czy wersja node.js zainstalowana w poprzednim kroku:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Weryfikowanie wersji Node.js":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Instalowanie rozszerzeń platformy Azure dla Visual Studio Code

Zainstaluj [rozszerzenie usługi Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) za pomocą programu Visual Studio Marketplace lub z Visual Studio Code (wyświetl rozszerzenia > > usługi Azure Storage).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Instalowanie rozszerzenia usługi Azure Storage 1":::

Wykonaj te same czynności dla rozszerzeń [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) i [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .


## <a name="set-up-a-local-webserver"></a>Konfigurowanie lokalnego serwera WebServer

### <a name="install-webpack"></a>Zainstaluj pakiet WebPack

[pakiet WebPack](https://webpack.js.org/) umożliwia łączenie kodu z plikami statycznymi, które można wdrożyć na platformie Azure. Ma również serwer programistyczny, który zostanie skonfigurowany do użycia z przykładem wywołującym.

Wpisz następujące w otwartym terminalu, aby zainstalować pakiet WebPack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Ten samouczek został przetestowany przy użyciu powyższych określonych wersji. Określenie `-dev` informuje Menedżera pakietów, że ta zależność jest przeznaczona do celów deweloperskich i nie powinna być uwzględniona w kodzie wdrażanym na platformie Azure.

Zostaną wyświetlone dwa nowe pakiety dodane do `package.json` pliku jako "devDependencies". Pakiety zostaną zainstalowane w `./CallingApp/node_modules/` katalogu.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Konfiguracja pakietu WebPack":::

### <a name="configure-the-development-server"></a>Konfigurowanie serwera deweloperskiego

Uruchomienie aplikacji statycznej (na przykład `index.html` pliku) z przeglądarki korzysta z `file://` protokołu. Aby moduły npm działały prawidłowo, wymagany jest protokół HTTP przy użyciu pakietu WebPack jako lokalnego serwera deweloperskiego.

Utworzymy dwie konfiguracje: jeden do programowania i dla środowiska produkcyjnego. Pliki przygotowane do produkcji będą zminimalizowanego, co oznacza, że usuniemy nieużywane znaki i cyfry. Jest to odpowiednie dla scenariuszy produkcyjnych, w których opóźnienie powinno być zminimalizowane lub gdzie kod powinien zostać zasłonięty.

Użyjemy `webpack-merge` Narzędzia do pracy z [różnymi plikami konfiguracji dla pakietu WebPack](https://webpack.js.org/guides/production/)

Zacznijmy od środowiska deweloperskiego. Najpierw musimy zainstalować program `webpack merge` . W terminalu uruchom następujące polecenie:

```Console
npm install --save-dev webpack-merge
```

W `package.json` pliku można zobaczyć jedną większą zależność dodaną do "devDependencies".

W następnym kroku musimy utworzyć nowy plik `webpack.common.js` i dodać następujący kod:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }     
}
```

Następnie dodamy dwa więcej plików, po jednej dla każdej konfiguracji:

* webpack.dev.js
* webpack.prod.js

W następnym kroku musimy zmodyfikować `webpack.dev.js` plik. Dodaj następujący kod do tego pliku:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
W tej konfiguracji zaimportujemy typowe parametry z `webpack.common.js` , scalasz dwa pliki, ustawisz tryb na "Programowanie" i skonfigurujesz mapy źródła jako "inline-Source-map".

Tryb programistyczny instruuje pakiet WebPack, aby nie zminifikować plików i nie generował zoptymalizowanych plików produkcyjnych. Szczegółowa dokumentacja dotycząca trybów pakietu WebPack można znaleźć [tutaj](https://webpack.js.org/configuration/mode/).

Opcje mapy źródłowej są wymienione [tutaj](https://webpack.js.org/configuration/devtool/#root). Ustawienie mapy źródłowej ułatwia debugowanie za pomocą przeglądarki.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Konfigurowanie pakietu WebPack":::

Aby uruchomić serwer programistyczny, przejdź do `package.json.js` i Dodaj następujący kod w obszarze skrypty:

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

Plik powinien teraz wyglądać następująco:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

Dodano polecenie, które może być używane z npm. 

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Modyfikowanie package-json.js":::

### <a name="testing-the-development-server"></a>Testowanie serwera deweloperskiego

 W Visual Studio Code Utwórz trzy pliki w projekcie:

* `index.html`
* `app.js`
* `app.css` (opcjonalnie, to pozwala na styl aplikacji)

Wklej do `index.html` :

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Plik HTML":::

Dodaj następujący kod do pliku `app.js`:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Dodaj następujący kod do pliku `app.css`:

```CSS
html {
    font-family: sans-serif;
  }
```

 :::image type="content" source="./media/step-one-pic-14.png" alt-text="App.js pliku z kodem JS":::

Po otwarciu tej strony powinien zostać wyświetlony komunikat z alertem i w konsoli przeglądarki.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Plik App. CSS":::

Aby przetestować konfigurację programistyczną, użyj następującego polecenia terminalu:

```Console
npm run build:dev
```

W konsoli programu zostanie wyświetlony stan uruchomiony na serwerze programu. Domyślnie jest to `http://localhost:8080` . Kompilacja: dev polecenie to polecenie, które zostało dodane `package-json.js` wcześniej.

 :::image type="content" source="./media/step-one-pic-16.png" alt-text="Uruchamianie serwera deweloperskiego":::
 
 Przejdź do adresu w przeglądarce i zobaczysz stronę i alert skonfigurowany w poprzednich krokach.
 
  :::image type="content" source="./media/step-one-pic-17.png" alt-text="Strona HTML":::
  
 
Gdy serwer jest uruchomiony, można zmienić kod, a serwer i strona HTML zostaną automatycznie Załaduj ponownie. 

Następnie przejdź do `app.js` pliku w Visual Studio Code i Usuń `alert('Hello world alert!');` . Zapisz plik i sprawdź, czy alert zniknie z przeglądarki.

Aby zatrzymać serwer, możesz uruchomić program `Ctrl+C` w terminalu. Aby uruchomić serwer, wpisz `npm run build:dev` w dowolnym momencie.

## <a name="add-the-azure-communication-services-packages"></a>Dodawanie pakietów usług Azure Communications Services

Użyj `npm install` polecenia, aby zainstalować bibliotekę klienta wywołującego usługi Azure Communications Services dla języka JavaScript.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Ta akcja spowoduje dodanie pakietów wspólnych i wywołujących usługi Azure Communications jako zależności pakietu. Zostaną wyświetlone dwa nowe pakiety dodane do `package.json` pliku. Więcej informacji o tym `npm install` poleceniu można znaleźć [tutaj](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Instalowanie pakietów usług Azure Communications Services":::

Te pakiety są udostępniane przez zespół usługi Azure Communication Services i obejmują uwierzytelnianie i biblioteki wywołujące. Polecenie "--Save" sygnalizuje, że nasza aplikacja zależy od tych pakietów do użycia w środowisku produkcyjnym i zostanie uwzględniona w `dependencies` naszym `package-json.js` pliku. Po skompilowaniu aplikacji dla środowiska produkcyjnego pakiety zostaną uwzględnione w naszym kodzie produkcyjnym.


## <a name="publish-your-website-to-azure-static-websites"></a>Publikowanie witryny sieci Web w usłudze Azure static websites

### <a name="create-a-new-npm-package"></a>Utwórz nowy pakiet npm

W terminalu w polu Ścieżka folderu obszaru roboczego wpisz:

``` console
npm init -y
```

To polecenie inicjuje nowy pakiet npm i dodaje `package.json` do folderu głównego projektu.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Plik JSON pakietu":::

Dodatkową dokumentację dotyczącą polecenia init npm można znaleźć [tutaj](https://docs.npmjs.com/cli/v6/commands/npm-init)

 
### <a name="create-a-configuration-for-production-deployment"></a>Utwórz konfigurację wdrożenia produkcyjnego

Dodaj następujący kod do `webpack.prod.js` :

```JavaScript
const { merge } = require('webpack-merge');
 const common = require('./webpack.common.js');

 module.exports = merge(common, {
   mode: 'production',
 });
 ```

Zauważ, że ta konfiguracja zostanie scalona z webpack.common.js (w którym został określony plik wejściowy i miejsce, w którym mają być przechowywane wyniki), a tryb zostanie ustawiony na "produkcja".
 
W pliku `package.json` dodaj następujący kod:

```JavaScript
"build:prod": "webpack --config webpack.prod.js" 
```

Plik powinien wyglądać następująco:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js" 
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.3",
    "@azure/communication-common": "^1.0.0-beta.3"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

 :::image type="content" source="./media/step-one-pic-20.png" alt-text="Skonfigurowane pliki":::


W przebiegu terminalu:

```Console
npm run build:prod
```

Polecenie spowoduje utworzenie `dist` w nim folderu i gotowego do produkcji `app.js` pliku statycznego. 

 :::image type="content" source="./media/step-one-pic-21.png" alt-text="Kompilacja produkcyjna":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Wdrażanie aplikacji w usłudze Azure Storage
 
Skopiuj `index.html` i `app.css` do `dist` folderu.

`dist`Utwórz nowy plik w folderze, a następnie nadaj mu nazwę `404.html` . Skopiuj następujące znaczniki do tego pliku:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Zapisz plik (Ctrl + S).

Kliknij prawym przyciskiem myszy i wybierz pozycję Wdróż do statycznej witryny sieci Web za pośrednictwem usługi Azure Storage.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Rozpocznij wdrażanie na platformie Azure":::
 
W `Select subscription` polu Wybierz opcję "Zaloguj się do platformy Azure (lub utwórz bezpłatne konto platformy Azure", jeśli subskrypcja nie została wcześniej utworzona)
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Logowanie do platformy Azure":::
 
Wybierz `Create new Storage Account`  >  `Advanced` :

 :::image type="content" source="./media/step-one-pic-24.png" alt-text="Tworzenie grupy kont magazynu":::
 
 Podaj nazwę grupy magazynów:
 
 :::image type="content" source="./media/step-one-pic-25.png" alt-text="Dodawanie nazwy konta":::
 
W razie konieczności Utwórz nową grupę zasobów:
 
  :::image type="content" source="./media/step-one-pic-26.png" alt-text="Tworzenie nowej grupy":::
  
  Odpowiedz "tak", aby włączyć obsługę statycznej witryny sieci Web?
  
  :::image type="content" source="./media/step-one-pic-27.png" alt-text="Wybieranie opcji włączania obsługi statycznej witryny sieci Web":::
  
Zaakceptuj domyślną nazwę pliku w polu "Wprowadź nazwę dokumentu indeksu" podczas tworzenia pliku `index.html` .

Wpisz wartość w polu `404.html` "wprowadź ścieżkę do dokumentu błędu 404".  
  
Wybierz lokalizację aplikacji. Wybrana lokalizacja określi, który procesor multimedialny będzie używany w przyszłych aplikacjach wywołujących w ramach wywołań grup. 

Usługi komunikacyjne Azure wybierają procesor multimediów na podstawie lokalizacji aplikacji.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Wybierz lokalizację":::
  
Zaczekaj na utworzenie zasobu i witryny sieci Web. 
 
Kliknij przycisk "przejdź do witryny sieci Web":

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Ukończono wdrażanie":::
 
Z narzędzi programistycznych przeglądarki możesz sprawdzić źródło i zobaczyć nasz plik przygotowany do produkcji.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Witryna internetowa":::

Przejdź do [Azure Portal](https://portal.azure.com/#home), wybierz grupę zasobów, wybierz utworzoną aplikację i przejdź do `Settings`  >  `Static website` . Można sprawdzić, czy statyczne witryny sieci Web są włączone i zanotować podstawowy punkt końcowy, dokument indeksu i pliki dokumentu ścieżki błędu.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Wybór statycznej witryny sieci Web":::

W obszarze "Blob service" Wybierz "kontenery" i zobaczysz dwa kontenery utworzone, jeden dla dzienników ($logs) i zawartość witryny sieci Web ($web)

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Konfiguracja kontenera":::

Jeśli przejdziesz do `$web` Ciebie, zobaczysz pliki utworzone w programie Visual Studio i wdrożone na platformie Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Wdrożenie":::

W dowolnym momencie możesz ponownie wdrożyć aplikację z Visual Studio Code.

Teraz można przystąpić do kompilowania pierwszej aplikacji sieci Web usług Azure Communications Services.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie połączenia głosowego do aplikacji](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Możesz również chcieć:

- [Dodawanie czatu do aplikacji](../quickstarts/chat/get-started.md)
- [Tworzenie tokenów dostępu użytkowników](../quickstarts/access-tokens.md)
- [Informacje o architekturze klienta i serwera](../concepts/client-and-server-architecture.md)
- [Informacje o uwierzytelnianiu](../concepts/authentication.md)
