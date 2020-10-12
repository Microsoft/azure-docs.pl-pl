---
title: Przykłady interfejsu wiersza polecenia platformy Azure — usługa Azure SignalR Service
description: Obserwuj prawdziwe przykłady, aby uzyskać informacje na temat interfejsu wiersza polecenia platformy Azure dla usługi Azure Signal Service. Dowiesz się, jak utworzyć usługę sygnalizującą z większą liczbą usług platformy Azure.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 99ebe9c078ef4775e8f0df0545fb0af4b3d1057b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86515244"
---
# <a name="azure-cli-reference"></a>Dokumentacja interfejsu wiersza polecenia platformy Azure

Poniższa tabela zawiera linki do skryptów powłoki systemowej dla usługi Azure SignalR Service używających interfejsu wiersza polecenia platformy Azure.

| Skrypt | Znajduje |
|-|-|
|**Utwórz**||
| [Tworzenie nowej usługi SignalR Service i grupy zasobów](scripts/signalr-cli-create-service.md) | Tworzy nowy zasób usługi Azure SignalR Service w nowej grupie zasobów o losowej nazwie.  |
|**Integracja**||
| [Tworzenie nowej usługi SignalR Service i aplikacji internetowej skonfigurowanej na potrzeby korzystania z usługi SignalR](scripts/signalr-cli-create-with-app-service.md) | Tworzy nowy zasób usługi Azure SignalR Service w nowej grupie zasobów o losowej nazwie. Dodaje również nową aplikację sieci Web i plan App Service, aby hostować aplikację sieci Web ASP.NET Core, która używa usługi sygnalizującej. Aplikację internetową skonfigurowano za pomocą ustawień aplikacji, aby nawiązać połączenie z nowym zasobem usługi SignalR Service. |
| [Tworzenie nowej usługi SignalR Service i aplikacji internetowej skonfigurowanej na potrzeby korzystania z usług SignalR i GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Tworzy nowy zasób usługi Azure SignalR Service w nowej grupie zasobów o losowej nazwie. Dodaje również nową aplikację sieci Web platformy Azure i plan hostingu do hostowania aplikacji sieci Web ASP.NET Core, która korzysta z usługi sygnalizującej. Aplikację internetową skonfigurowano za pomocą ustawień aplikacji, aby parametry połączenia z nowym zasobem usługi SignalR i klucze tajne klientów obsługiwały [uwierzytelnianie GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), jak pokazano w [samouczku uwierzytelniania](signalr-concept-authenticate-oauth.md). Aplikacja internetowa jest również skonfigurowana do używania lokalnego źródła wdrożenia repozytorium git. |
| | |
