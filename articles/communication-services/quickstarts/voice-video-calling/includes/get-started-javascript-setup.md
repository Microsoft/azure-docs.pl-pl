---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: mikben
ms.openlocfilehash: 90bc9def9c1a78799f4931b58ad0a661602985e3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013196"
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

Użyj `npm install` polecenia, aby zainstalować bibliotekę klienta wywołującego usługi Azure Communications Services dla języka JavaScript.

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
