---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83597233"
---
#### <a name="local-proxy"></a>Lokalny serwer proxy

Można skonfigurować serwer proxy dla rozszerzenia Visual Studio Code na żywo, które kieruje wszystkie żądania do `/api` uruchomionego punktu końcowego interfejsu API w `http://127.0.0.1:7071/api` .

1. Otwórz plik _. programu vscode/settings.jsw_ pliku.

1. Dodaj następujące ustawienia, aby określić serwer proxy dla serwera na żywo.

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   Ta konfiguracja jest najlepiej zapisywana w pliku ustawień projektu, w przeciwieństwie do pliku ustawień użytkownika.

   Użycie ustawień projektu gwarantuje, że serwer proxy nie zostanie zastosowany do wszystkich innych projektów otwartych w Visual Studio Code.
