---
title: Kompilowanie i wdrażanie aplikacji node. js Express na platformie Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Kompilowanie i wdrażanie aplikacji Express. js w języku Node. js na platformie Azure Cloud Services
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.openlocfilehash: 79a998930a384420b22add8825ee4b2269eb4539
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75360756"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Kompilowanie i wdrażanie aplikacji sieci Web w języku Node. js przy użyciu języka Express na platformie Azure Cloud Services

Środowisko Node. js obejmuje minimalny zestaw funkcji w środowisku uruchomieniowym Core.
Deweloperzy często używają modułów innych firm, aby zapewnić dodatkową funkcjonalność podczas tworzenia aplikacji node. js. W tym samouczku utworzysz nową aplikację przy użyciu modułu [Express](https://github.com/expressjs/express) , który udostępnia strukturę MVC do tworzenia aplikacji sieci Web Node. js.

Zrzut ekranu ukończonej aplikacji jest poniżej:

![Przeglądarka sieci Web przedstawiająca Witaj na platformie Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Tworzenie projektu usługi w chmurze
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Wykonaj następujące kroki, aby utworzyć nowy projekt usługi w chmurze o nazwie "expressapp":

1. W **menu Start** lub **Start ekranu**Wyszukaj program **Windows PowerShell**. Na koniec kliknij prawym przyciskiem myszy program **Windows PowerShell** i wybierz polecenie **Uruchom jako administrator**.
   
    ![Ikona Azure PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Zmień katalogi na katalog **c:\\Node** , a następnie wprowadź następujące polecenia, aby utworzyć nowe rozwiązanie o nazwie **expressapp** i rolę sieci Web o nazwie **WebRole1**:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Domyślnie polecenie **Add-AzureNodeWebRole** używa starszej wersji środowiska Node. js. Powyższa instrukcja **Set-AzureServiceProjectRole** instruuje platformę Azure, aby korzystała z 0.10.21 węzła.  Należy pamiętać, że w parametrach jest rozróżniana wielkość liter.  Można sprawdzić, czy wybrano poprawną wersję środowiska Node. js, sprawdzając Właściwość **Engines** w **WebRole1\package.JSON**.
    > 
    > 

## <a name="install-express"></a>Instalowanie platformy Express
1. Zainstaluj Generator ekspresowy, wydając następujące polecenie:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Dane wyjściowe polecenia npm powinny wyglądać podobnie do poniższego wyniku. 
   
    ![Program Windows PowerShell wyświetla dane wyjściowe polecenia npm Install Express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Zmień katalogi na katalog **WebRole1** i użyj polecenia Express, aby wygenerować nową aplikację:
   
        PS C:\node\expressapp\WebRole1> express
   
    Zostanie wyświetlony monit o zastąpienie wcześniejszej aplikacji. Wprowadź wartość **y** lub **tak** , aby kontynuować. W programie Express zostanie wygenerowany plik App. js i struktura folderów do kompilowania aplikacji.
   
    ![Dane wyjściowe polecenia ekspresowego](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Aby zainstalować dodatkowe zależności zdefiniowane w pliku Package. JSON, wprowadź następujące polecenie:
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Dane wyjściowe polecenia instalacji npm](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Użyj poniższego polecenia, aby skopiować plik **bin/www** do pliku **Server. js**. Jest tak dlatego, że usługa w chmurze może znaleźć punkt wejścia dla tej aplikacji.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Po zakończeniu wykonywania tego polecenia powinien znajdować się plik **Server. js** w katalogu WebRole1.
5. Zmodyfikuj **serwer. js** , aby usunąć jeden z znaków "." z następującego wiersza.
   
       var app = require('../app');
   
   Po dokonaniu tej modyfikacji wiersz powinien wyglądać w następujący sposób.
   
       var app = require('./app');
   
   Ta zmiana jest wymagana, ponieważ plik został przeniesiony (dawniej **bin/www**) do tego samego katalogu co wymagany plik aplikacji. Po wprowadzeniu tej zmiany Zapisz plik **Server. js** .
6. Użyj następującego polecenia, aby uruchomić aplikację w emulatorze platformy Azure:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Strona sieci Web zawierająca Welcome to Express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Modyfikowanie widoku
Zmodyfikuj teraz widok, aby wyświetlić komunikat "Witaj do Express na platformie Azure".

1. Wprowadź następujące polecenie, aby otworzyć plik index. Jade:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Zawartość pliku index. Jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade jest domyślnym aparatem wyświetlania używanym przez aplikacje Express. Aby uzyskać więcej informacji na temat aparatu widoku Jade, [http://jade-lang.com][http://jade-lang.com]Zobacz.
2. Modyfikuj ostatni wiersz tekstu przez dołączenie **na platformie Azure**.
   
   ![Plik index. Jade, Ostatni odczyt wiersza: p Witamy w \#{title} na platformie Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Zapisz plik i zamknij Notatnik.
4. Odśwież przeglądarkę i zobaczysz zmiany.
   
   ![Okno przeglądarki, Strona zawiera Witaj na platformie Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Po przetestowaniu aplikacji należy użyć polecenia cmdlet **stop-AzureEmulator** , aby zatrzymać emulator.

## <a name="publishing-the-application-to-azure"></a>Publikowanie aplikacji na platformie Azure
W oknie Azure PowerShell Użyj polecenia cmdlet **Publish-AzureServiceProject** , aby wdrożyć aplikację w usłudze w chmurze.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Po zakończeniu operacji wdrażania zostanie otwarta przeglądarka i zostanie wyświetlona strona sieci Web.

![Przeglądarka sieci Web wyświetlająca stronę Express. Adres URL wskazuje, że jest teraz hostowany na platformie Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, odwiedź stronę [Centrum deweloperów środowiska Node.js](https://docs.microsoft.com/azure/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com





