---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
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
