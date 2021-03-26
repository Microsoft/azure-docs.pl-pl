---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543565"
---
| Właściwość | Opis | Przykład | Wymagane |
|---|---|---|---|
| `app_location` | Lokalizacja kodu aplikacji. | Wprowadź `/` Jeśli kod źródłowy aplikacji znajduje się w katalogu głównym repozytorium lub `/app` Jeśli kod aplikacji znajduje się w katalogu o nazwie `app` . | Tak |
| `api_location` | Lokalizacja kodu Azure Functions. | Wprowadź `/api` Jeśli kod aplikacji znajduje się w folderze o nazwie `api` . Jeśli w folderze nie zostanie wykryta żadna aplikacja Azure Functions, kompilacja nie powiedzie się, a przepływ pracy założono, że nie potrzebujesz interfejsu API. | Nie |
| `output_location` | Lokalizacja katalogu wyjściowego kompilacji względem `app_location` . | Jeśli kod źródłowy aplikacji znajduje się w lokalizacji `/app` i skrypt kompilacji wyprowadza pliki do `/app/build` folderu, a następnie ustawi `build` jako `output_location` wartość. | Nie |