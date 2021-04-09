---
title: Samouczek — przygotowywanie aplikacji sieci Web dla usług Azure Communications Services (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Dowiedz się, jak utworzyć podstawową aplikację sieci Web, która obsługuje usługi Azure Communications Services
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a5a23d6a06c8cdff4deabac5251597b7ffe0c833
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728049"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Samouczek: przygotowywanie aplikacji sieci Web dla usług Azure Communications Services (Node.js)

Usługi Azure Communications Services umożliwiają dodawanie komunikacji w czasie rzeczywistym do aplikacji. W tym samouczku dowiesz się, jak skonfigurować aplikację sieci Web, która obsługuje usługi Azure Communications Services. Jest to samouczek wprowadzający dla nowych deweloperów, którzy chcą zacząć korzystać z komunikacji w czasie rzeczywistym.

Po zakończeniu tego samouczka będziesz mieć podstawową aplikację sieci Web, która została skonfigurowana przy użyciu zestawów SDK usług Azure Communication Services. Następnie możesz użyć tej aplikacji, aby rozpocząć tworzenie rozwiązania do komunikacji w czasie rzeczywistym.

Możesz odwiedzić [stronę usługi Azure Communication Services w serwisie GitHub](https://github.com/Azure/communication) , aby przekazać opinię.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Skonfiguruj środowisko programistyczne.
> * Skonfiguruj lokalny serwer sieci Web.
> * Dodaj pakiety usług Azure Communication Services do witryny sieci Web.
> * Opublikuj witrynę internetową w usłudze Azure static websites.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie konta bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Bezpłatne konto to $200 na kredyty na korzystanie z platformy Azure w celu wypróbowania dowolnej kombinacji usług.
- [Visual Studio Code](https://code.visualstudio.com/) edytowania kodu w lokalnym środowisku programistycznym.
- [pakiet WebPack](https://webpack.js.org/) do pakietu i lokalnego hostowania kodu.
- [Node.js](https://nodejs.org/en/) zainstalować zależności, takie jak zestawy SDK usług Azure Communication Services i pakiet WebPack, oraz zarządzać nimi.
- [NVM i npm](/windows/nodejs/setup-on-windows) do obsługi kontroli wersji.
- [Rozszerzenie usługi Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) dla Visual Studio Code. To rozszerzenie jest potrzebne do opublikowania aplikacji w usłudze Azure Storage. [Dowiedz się więcej na temat hostowania statycznych witryn sieci Web w usłudze Azure Storage](../../storage/blobs/storage-blob-static-website.md).
- [Rozszerzenie Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Rozszerzenie umożliwia wdrażanie witryn sieci Web przy użyciu opcji konfigurowania w pełni zarządzanej ciągłej integracji i ciągłego dostarczania (CI/CD).
- [Rozszerzenie Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) do tworzenia własnych aplikacji bezserwerowych. Na przykład możesz hostować aplikację uwierzytelniania w Azure Functions.
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Dowiedz się, jak utworzyć zasób usług komunikacyjnych](../quickstarts/create-communication-resource.md).
- Token dostępu użytkownika. Aby uzyskać instrukcje, zobacz [Przewodnik Szybki Start dotyczący tworzenia i zarządzania tokenami dostępu](../quickstarts/access-tokens.md?pivots=programming-language-javascript) lub [Samouczek dotyczący tworzenia zaufanej usługi uwierzytelniania](./trusted-service-tutorial.md).


## <a name="configure-your-development-environment"></a>Konfigurowanie środowiska programistycznego

Lokalne środowisko programistyczne zostanie skonfigurowane w następujący sposób:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Diagram przedstawiający architekturę środowiska deweloperskiego.":::


### <a name="install-nodejs-nvm-and-npm"></a>Zainstaluj Node.js, NVM i npm

Będziemy używać Node.js, aby pobierać i instalować różne zależności, które są potrzebne dla aplikacji po stronie klienta. Będziemy używać go do generowania plików statycznych, które będą następnie hostowane na platformie Azure, dzięki czemu nie musisz martwić się o konfigurowanie go na serwerze.

Deweloperzy systemu Windows mogą skorzystać z [tego samouczka Node.js](/windows/nodejs/setup-on-windows) , aby skonfigurować węzły, NVM i npm. 

Ten samouczek jest oparty na wersji LTS 12.20.0. Po zainstalowaniu programu NVM należy użyć następującego polecenia programu PowerShell, aby wdrożyć wersję, która ma być używana:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Zrzut ekranu przedstawiający polecenia wdrażania wersji węzła.":::

### <a name="configure-visual-studio-code"></a>Konfigurowanie programu Visual Studio Code

[Visual Studio Code](https://code.visualstudio.com/) można pobrać na jednej z [obsługiwanych platform](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Tworzenie obszaru roboczego dla projektów usług Azure Communications Services

Utwórz folder do przechowywania plików projektu, w tym: `C:\Users\Documents\ACS\CallingApp` . W Visual Studio Code wybierz pozycję **plik**  >  **Dodaj folder do obszaru roboczego** i Dodaj folder do obszaru roboczego.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Zrzut ekranu pokazujący opcje dodawania pliku do obszaru roboczego.":::

Przejdź do **Eksploratora** w okienku po lewej stronie, a folder zostanie wyświetlony `CallingApp` w obszarze roboczym **bez tytułu** .

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Zrzut ekranu pokazujący Eksploratora i obszar roboczy bez tytułu.":::

Zaktualizuj nazwę obszaru roboczego. Aby sprawdzić wersję Node.js, kliknij prawym przyciskiem myszy `CallingApp` folder i wybierz polecenie **Otwórz w zintegrowanym terminalu**.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Zrzut ekranu pokazujący wybór otwierania folderu w zintegrowanym terminalu.":::

W terminalu wprowadź następujące polecenie, aby sprawdzić poprawność wersji Node.js zainstalowanej w poprzednim kroku:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Zrzut ekranu, który pokazuje sprawdzanie poprawności wersji węzła.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Instalowanie rozszerzeń platformy Azure dla Visual Studio Code

Zainstaluj [rozszerzenie usługi Azure Storage](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) , korzystając z witryny Visual Studio Marketplace lub Visual Studio Code (**Widok**  >    >  **usługi Azure Storage**).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Zrzut ekranu pokazujący przycisk służący do instalowania rozszerzenia usługi Azure Storage.":::

Wykonaj te same czynności dla rozszerzeń [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) i [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) .


## <a name="set-up-a-local-web-server"></a>Konfigurowanie lokalnego serwera sieci Web

### <a name="create-an-npm-package"></a>Tworzenie pakietu npm

W terminalu z ścieżki do folderu obszaru roboczego wpisz:

``` console
npm init -y
```

To polecenie inicjuje nowy pakiet npm i dodaje `package.json` do folderu głównego projektu.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Zrzut ekranu pokazujący pakiet J S O N.":::

Aby uzyskać więcej dokumentacji na temat programu `npm init` , zobacz [stronę npm docs dla tego polecenia](https://docs.npmjs.com/cli/v6/commands/npm-init).

### <a name="install-webpack"></a>Zainstaluj pakiet WebPack

[Pakiet WebPack](https://webpack.js.org/) służy do łączenia kodu z plikami statycznymi, które można wdrożyć na platformie Azure. Ma również serwer programistyczny, który zostanie skonfigurowany do użycia z przykładem wywołującym.

W terminalu wprowadź następujące polecenie, aby zainstalować pakiet WebPack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Ten samouczek został przetestowany z wersjami określonymi w poprzednim poleceniu. Określenie `-dev` informuje Menedżera pakietów, że ta zależność jest przeznaczona do celów deweloperskich i nie powinna być uwzględniona w kodzie wdrażanym na platformie Azure.

Zostaną wyświetlone dwa nowe pakiety dodane do `package.json` pliku jako `devDependencies` . Pakiety zostaną zainstalowane w `./CallingApp/node_modules/` katalogu.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Zrzut ekranu pokazujący konfigurację pakietu WebPack.":::

### <a name="configure-the-development-server"></a>Konfigurowanie serwera deweloperskiego

Uruchomienie aplikacji statycznej (na przykład `index.html` pliku) z przeglądarki korzysta z `file://` protokołu. Aby moduły npm działały prawidłowo, wymagany jest protokół HTTP przy użyciu pakietu WebPack jako lokalnego serwera deweloperskiego.

Utworzysz dwie konfiguracje: jeden do programowania i dla środowiska produkcyjnego. Pliki przygotowane do środowiska produkcyjnego będą zminimalizowanego, co oznacza, że usuniesz nieużywane odstępy i znaki. Ta konfiguracja jest odpowiednia dla scenariuszy produkcyjnych, w których opóźnienie powinno być zminimalizowane lub gdzie kod powinien zostać zasłonięty.

Użyjesz `webpack-merge` Narzędzia do pracy z [różnymi plikami konfiguracji dla pakietu WebPack](https://webpack.js.org/guides/production/).

Zacznijmy od środowiska deweloperskiego. Najpierw należy zainstalować program `webpack merge` . W terminalu uruchom następujące polecenie:

```Console
npm install --save-dev webpack-merge
```

W `package.json` pliku może zostać wyświetlona jedna większa zależność dodana do programu `devDependencies` .

Następnie utwórz plik o nazwie `webpack.common.js` i Dodaj następujący kod:

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

Następnie Dodaj dwa więcej plików, po jednej dla każdej konfiguracji:

* `webpack.dev.js`
* `webpack.prod.js`

Teraz zmodyfikuj `webpack.dev.js` plik, dodając do niego następujący kod:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
W tej konfiguracji należy zaimportować typowe parametry z `webpack.common.js` , scalić dwa pliki, ustawić tryb na `development` i skonfigurować mapę źródłową jako `inline-source-map` .

Tryb programistyczny instruuje pakiet WebPack, aby nie zminifikować plików i nie generował zoptymalizowanych plików produkcyjnych. Szczegółowa dokumentacja dotycząca trybów pakietu WebPack można znaleźć na [stronie sieci Web trybu pakietu WebPack](https://webpack.js.org/configuration/mode/).

Opcje mapy źródłowej są wymienione na [stronie internetowej WebPack devtool](https://webpack.js.org/configuration/devtool/#root). Ustawienie mapy źródłowej ułatwia debugowanie za pomocą przeglądarki.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Zrzut ekranu pokazujący kod służący do konfigurowania pakietu WebPack.":::

Aby uruchomić serwer programistyczny, przejdź do `package.json` i Dodaj następujący kod w obszarze `scripts` :

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

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Zrzut ekranu, na którym jest wyświetlana modyfikacja package.js.":::

### <a name="test-the-development-server"></a>Testowanie serwera deweloperskiego

 W Visual Studio Code Utwórz trzy pliki w projekcie:

* `index.html`
* `app.js`
* `app.css` (opcjonalnie, aby nadawanie stylu aplikacji)

Wklej ten kod do `index.html` :

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
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Zrzut ekranu pokazujący plik H T M L.":::

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
Nie zapomnij zapisać pracy. Niezapisany plik jest wskazywany przez białe kropki obok nazw plików w Eksploratorze.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="Zrzut ekranu pokazujący plik App.js z kodem JavaScript.":::

Gdy otworzysz Tę stronę, w konsoli przeglądarki powinien pojawić się komunikat z alertem.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Zrzut ekranu pokazujący plik App. css.":::

Aby przetestować konfigurację programistyczną, użyj następującego polecenia terminalu:

```Console
npm run build:dev
```

Konsola programu pokazuje, gdzie działa serwer. Domyślnie jest to `http://localhost:8080` . `build:dev`Polecenie to polecenie, które zostało dodane `package.json` wcześniej.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Zrzut ekranu pokazujący uruchamianie serwera deweloperskiego.":::
 
Przejdź do adresu w przeglądarce i zobaczysz stronę i alert skonfigurowany w poprzednich krokach.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Zrzut ekranu strony H T M L.":::
  
 
Gdy serwer jest uruchomiony, można zmienić kod i serwer. Strona HTML zostanie automatycznie załadowana ponownie. 

Następnie przejdź do `app.js` pliku w Visual Studio Code i Usuń `alert('Hello world alert!');` . Zapisz plik i sprawdź, czy alert zniknie z przeglądarki.

Aby zatrzymać serwer, możesz uruchomić program `Ctrl+C` w terminalu. Aby uruchomić serwer, wprowadź `npm run build:dev` w dowolnym momencie.

## <a name="add-the-azure-communication-services-packages"></a>Dodawanie pakietów usług Azure Communications Services

Użyj `npm install` polecenia, aby zainstalować zestaw SDK wywoływania usługi Azure Communications Services dla języka JavaScript.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Ta akcja powoduje dodanie pakietów wspólnych i wywołujących usługi Azure Communications jako zależności pakietu. Zostaną wyświetlone dwa nowe pakiety dodane do `package.json` pliku. Więcej informacji można znaleźć `npm install` na [stronie npm docs dla tego polecenia](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Zrzut ekranu pokazujący kod służący do instalowania pakietów usług Azure Communications Services.":::

Te pakiety są udostępniane przez zespół usługi Azure Communication Services i obejmują uwierzytelnianie i biblioteki wywołujące. `--save`Polecenie sygnalizuje, że aplikacja zależy od tych pakietów do użycia w środowisku produkcyjnym i zostanie uwzględniona w `devDependencies` `package.json` pliku. Podczas kompilowania aplikacji dla środowiska produkcyjnego pakiety zostaną uwzględnione w kodzie produkcyjnym.


## <a name="publish-your-website-to-azure-static-websites"></a>Publikowanie witryny sieci Web w usłudze Azure static websites

### <a name="create-a-configuration-for-production-deployment"></a>Utwórz konfigurację wdrożenia produkcyjnego

Dodaj następujący kod do pliku `webpack.prod.js`:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Ta konfiguracja zostanie scalona z `webpack.common.js` (gdzie został określony plik wejściowy i miejsce, w którym mają być przechowywane wyniki). Konfiguracja ustawi również tryb na `production` .
 
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
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Zrzut ekranu pokazujący skonfigurowane pliki.":::


W terminalu uruchom polecenie:

```Console
npm run build:prod
```

Polecenie tworzy `dist` folder i gotowy do produkcji `app.js` plik statyczny. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Zrzut ekranu przedstawiający kompilację produkcyjną.":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Wdrażanie aplikacji w usłudze Azure Storage

Skopiuj `index.html` i `app.css` do `dist` folderu.

W `dist` folderze Utwórz plik i nadaj mu nazwę `404.html` . Skopiuj następujące znaczniki do tego pliku:

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

Kliknij prawym przyciskiem myszy `dist` folder i wybierz polecenie **Wdróż do statycznej witryny sieci Web za pośrednictwem usługi Azure Storage**.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Zrzut ekranu przedstawiający opcje rozpoczęcia wdrażania na platformie Azure.":::
 
W obszarze **Wybierz subskrypcję** wybierz pozycję **Zaloguj się do platformy Azure** (lub **Utwórz bezpłatne konto platformy Azure** , jeśli subskrypcja nie została wcześniej utworzona).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Zrzut ekranu pokazujący wybory logowania do platformy Azure.":::
 
Wybierz pozycję **Utwórz nowe konto magazynu**  >  **Zaawansowane**.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Zrzut ekranu pokazujący opcje tworzenia grupy kont magazynu.":::
 
Podaj nazwę grupy magazynów.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Zrzut ekranu pokazujący Dodawanie nazwy konta.":::
 
W razie konieczności Utwórz nową grupę zasobów.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Zrzut ekranu pokazujący wybór tworzenia nowej grupy zasobów.":::
  
**Czy chcesz włączyć obsługę statycznej witryny sieci Web?** wybierz opcję **tak**.

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Zrzut ekranu pokazujący wybór opcji włączenia obsługi statycznej witryny sieci Web.":::
  
W **polu Wprowadź nazwę dokumentu indeksu** zaakceptuj domyślną nazwę pliku. Plik został już utworzony `index.html` .

W **polu wprowadź ścieżkę do dokumentu błędu 404** wprowadź **404.html**.  
  
Wybierz lokalizację aplikacji. Wybrana lokalizacja określi, który procesor multimedialny będzie używany w przyszłych aplikacjach wywołujących w ramach wywołań grup. 

Usługi komunikacyjne Azure wybierają procesor multimediów na podstawie lokalizacji aplikacji.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Zrzut ekranu przedstawiający listę lokalizacji.":::
  
Zaczekaj na utworzenie zasobu i witryny sieci Web. 
 
Wybierz pozycję **Przejdź do witryny sieci Web**.

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Zrzut ekranu pokazujący komunikat, że wdrażanie zostało ukończone, z przyciskiem służącym do przeglądania witryny sieci Web.":::
 
Z narzędzi programistycznych przeglądarki można sprawdzić źródło i wyświetlić plik przygotowany do produkcji.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Zrzut ekranu przedstawiający Źródło witryny sieci Web z plikiem.":::

Przejdź do [Azure Portal](https://portal.azure.com/#home), wybierz grupę zasobów i wybierz utworzoną aplikację. Następnie wybierz pozycję **Ustawienia**  >  **statyczna witryna sieci Web**. Można sprawdzić, czy są włączone statyczne witryny sieci Web. Zanotuj podstawowy punkt końcowy, nazwę dokumentu indeksu i ścieżkę dokumentu błędu.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Zrzut ekranu przedstawiający wybór statycznej witryny sieci Web.":::

W obszarze **Blob service** wybierz pozycję **Kontenery**. Lista zawiera dwa kontenery utworzone, jeden dla dzienników ( `$logs` ) i jeden dla zawartości witryny sieci Web ( `$web` ).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Zrzut ekranu pokazujący konfigurację kontenera.":::

Jeśli otworzysz `$web` kontener, zobaczysz pliki utworzone w programie Visual Studio i wdrożone na platformie Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Zrzut ekranu pokazujący pliki wdrożone na platformie Azure.":::

W dowolnym momencie możesz ponownie wdrożyć aplikację z Visual Studio Code.

Teraz można przystąpić do kompilowania pierwszej aplikacji sieci Web usług Azure Communications Services.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie połączenia głosowego do aplikacji](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Możesz również chcieć:

- [Dodawanie czatu do aplikacji](../quickstarts/chat/get-started.md)
- [Utwórz tokeny dostępu użytkowników](../quickstarts/access-tokens.md)
- [Informacje o architekturze klienta i serwera](../concepts/client-and-server-architecture.md)
- [Informacje o uwierzytelnianiu](../concepts/authentication.md)
