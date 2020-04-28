---
title: Przykłady interfejsu wiersza polecenia platformy Azure — usługa Azure SignalR Service
description: Obserwuj prawdziwe przykłady, aby uzyskać informacje na temat interfejsu wiersza polecenia platformy Azure dla usługi Azure Signal Service. Dowiesz się, jak utworzyć usługę sygnalizującą z większą liczbą usług platformy Azure.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3c28781362e60d22b0d9a4e3754daaa24e5b61ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74158127"
---
# <a name="azure-cli-reference"></a>Dokumentacja interfejsu wiersza polecenia platformy Azure

Poniższa tabela zawiera linki do skryptów powłoki systemowej dla usługi Azure SignalR Service używających interfejsu wiersza polecenia platformy Azure.

| | |
|-|-|
|**Utwórz**||
| [Tworzenie nowej usługi SignalR Service i grupy zasobów](scripts/signalr-cli-create-service.md) | Tworzy nowy zasób usługi Azure SignalR Service w nowej grupie zasobów o losowej nazwie.  |
|**Integracja**||
| [Tworzenie nowej usługi SignalR Service i aplikacji internetowej skonfigurowanej na potrzeby korzystania z usługi SignalR](scripts/signalr-cli-create-with-app-service.md) | Tworzy nowy zasób usługi Azure SignalR Service w nowej grupie zasobów o losowej nazwie. Dodaje również nową aplikację sieci Web i plan App Service, aby hostować aplikację sieci Web ASP.NET Core, która używa usługi sygnalizującej. Aplikację internetową skonfigurowano za pomocą ustawień aplikacji, aby nawiązać połączenie z nowym zasobem usługi SignalR Service. |
| [Tworzenie nowej usługi SignalR Service i aplikacji internetowej skonfigurowanej na potrzeby korzystania z usług SignalR i GitHub OAuth](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Tworzy nowy zasób usługi Azure SignalR Service w nowej grupie zasobów o losowej nazwie. Dodaje również nową aplikację sieci Web platformy Azure i plan hostingu do hostowania aplikacji sieci Web ASP.NET Core, która korzysta z usługi sygnalizującej. Aplikację internetową skonfigurowano za pomocą ustawień aplikacji, aby parametry połączenia z nowym zasobem usługi SignalR i klucze tajne klientów obsługiwały [uwierzytelnianie GitHub](https://developer.github.com/v3/guides/basics-of-authentication/), jak pokazano w [samouczku uwierzytelniania](signalr-concept-authenticate-oauth.md). Aplikacja internetowa jest również skonfigurowana do używania lokalnego źródła wdrożenia repozytorium git. |
| | |
