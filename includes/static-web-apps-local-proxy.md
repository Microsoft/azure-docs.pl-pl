---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597233"
---
#### <a name="local-proxy"></a>Lokalny serwer proxy

Można skonfigurować serwer proxy dla rozszerzenia Visual Studio Code na żywo, które kieruje wszystkie żądania do `/api` uruchomionego punktu końcowego interfejsu API w `http://127.0.0.1:7071/api` .

1. Otwórz plik _. programu vscode/Settings. JSON_ .

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
