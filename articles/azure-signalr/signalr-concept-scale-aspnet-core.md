---
title: Skalowanie usługi ASP.NET Core SignalR przy użyciu usługi Azure SignalR
description: Omówienie sposobu użycia usługi Azure SignalR Service na potrzeby skalowania aplikacji ASP.NET Core SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: d104e0f1f2c6a978a5fce2c046a36e50a7056970
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88928504"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Skalowanie aplikacji ASP.NET Core SignalR za pomocą usługi Azure SignalR Service

## <a name="developing-signalr-apps"></a>Tworzenie aplikacji SignalR

Obecnie istnieją [dwie wersje](https://docs.microsoft.com/aspnet/core/signalr/version-differences) sygnalizującego, których można używać z aplikacjami sieci Web: sygnalizujący for ASP.NET, i ASP.NET Core sygnalizujący, który jest najnowszą wersją. Usługa Azure SignalR Service jest usługą zarządzaną przez platformę Azure, zbudowaną na podstawie biblioteki ASP.NET Core SignalR.

ASP.NET Core SignalR to ponownie napisana poprzednia wersja tej biblioteki. W związku z tym biblioteka ASP.NET Core SignalR nie jest zgodna z wcześniejszą wersją biblioteki SignalR. Interfejsy API i działanie różnią się. Zestaw ASP.NET Core SignalR SDK jest zgodny ze specyfikacją .NET Standard, więc nadal można z niego korzystać z programem .NET Framework. Należy jednak używać nowych interfejsów API zamiast starych. Jeśli używasz biblioteki SignalR i chcesz przejść na bibliotekę ASP.NET Core SignalR lub usługę Azure SignalR Service, musisz zmodyfikować swój kod tak, aby uwzględnić różnice w interfejsach API.

W przypadku usługi Azure SignalR Service składnik biblioteki ASP.NET Core SignalR po stronie serwera jest hostowany na platformie Azure. Jednak ponieważ technologia jest oparta na platformie ASP.NET Core, masz możliwość uruchamiania rzeczywistej aplikacji internetowej na wielu platformach (Windows, Linux i MacOS), hostując ją w usłudze [Azure App Service](../app-service/overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) lub [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). Możesz też użyć własnego hostingu w ramach swojego procesu.

Jeśli cele Twojej aplikacji obejmują obsługę najnowszej funkcji aktualizowania klientów internetowych o aktualizacje zawartości w czasie rzeczywistym, uruchamianie na wielu platformach (Azure, Windows, Linux i macOS) oraz hostowanie w różnych środowiskach, najlepszym wyborem będzie skorzystanie z usługi Azure SignalR Service.

## <a name="why-not-deploy-signalr-myself"></a>Dlaczego samodzielnie nie wdrażać biblioteki SignalR?

Wdrażanie własnej aplikacji internetowej platformy Azure obsługującej bibliotekę ASP.NET Core SignalR jako składnik zaplecza całej aplikacji internetowej jest nadal prawidłowym podejściem.

Jednym z najważniejszych argumentów za korzystaniem z usługi Azure SignalR Service jest prostota. W przypadku usługi Azure SignalR Service nie trzeba martwić się o takie rzeczy jak wydajność, skalowalność i dostępność. Tymi kwestiami zajmujemy się my, dając umowę SLA gwarantującą czas dostępności na poziomie 99,9%.

Ponadto preferowaną techniką aktualizacji zawartości w czasie rzeczywistym jest protokół WebSocket. Jednak równoważenie obciążenia dużą liczbą trwałych połączeń protokołu WebSocket staje się problemem niełatwym do rozwiązania podczas skalowania. Jego typowe rozwiązania obejmują równoważenie obciążenia DNS, sprzętowe moduły równoważenia obciążenia i programowe równoważenie obciążenia. Usługa Azure SignalR Service rozwiązuje ten problem za Ciebie.

Innym argumentem może być to, że w ogóle nie potrzebujesz hostować aplikacji internetowej. Logika Twojej aplikacji internetowej może wykorzystywać [obliczenia bezserwerowe](https://azure.microsoft.com/overview/serverless-computing/). Na przykład Twój kod może być hostowany i wykonywany jedynie na żądanie przy użyciu wyzwalaczy usługi [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). Ten scenariusz może być niełatwy, ponieważ Twój kod jest wykonywany tylko na żądanie i nie utrzymuje długich połączeń z klientami. Usługa Azure SignalR Service może obsłużyć taką sytuację, ponieważ już zarządza połączeniami za Ciebie. Aby uzyskać więcej szczegółów, zobacz [omówienie sposobu użycia usługi SignalR Service z usługą Azure Functions](signalr-concept-azure-functions.md).

## <a name="how-does-it-scale"></a>Jak wykonywane jest skalowanie?

Częstą sytuacją jest skalowanie biblioteki SignalR za pomocą programu SQL Server, usługi Azure Service Bus lub usługi Azure Cache for Redis. Usługa Azure SignalR Service obsługuje podejście ze skalowaniem za Ciebie. Wydajność i koszty są porównywalne jak w powyższym podejściu, ale bez złożoności związanej z obsługą tych innych usług. Wszystko, co trzeba zrobić, to zaktualizować liczbę jednostek dla swojej usługi. Każda jednostka obsługuje do 1000 połączeń z klientami.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie pokoju rozmów za pomocą usługi Azure SignalR](signalr-quickstart-dotnet-core.md)