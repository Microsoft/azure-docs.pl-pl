---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 4a493d5f0d34cd4621d55c0371036c03e267c466
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108296"
---
## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

Otwórz terminal lub okno poleceń Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Uruchom `npm init -y` , aby utworzyć **package.jsw** pliku z ustawieniami domyślnymi.

```console
npm init -y
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Użyj `npm install` polecenia, aby zainstalować zestaw SDK wywoływania usługi Azure Communications Services dla języka JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Następujące wersje programu WebPack są zalecane w ramach tego przewodnika Szybki Start:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

`--save`Opcja zawiera listę bibliotek jako zależność w **package.js** pliku.

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Ten przewodnik Szybki Start używa pakietu WebPack do łączenia zasobów aplikacji. Uruchom następujące polecenie, aby zainstalować pakiety WebPack, WebPack-CLI i WebPack — dev-Server npm i wyświetlić je jako zależności deweloperskie w **package.jsna**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Utwórz plik **index.html** w katalogu głównym projektu. Ten plik zostanie użyty do skonfigurowania podstawowego układu, który zezwoli użytkownikowi na umieszczenie wywołania.
