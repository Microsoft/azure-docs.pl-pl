---
title: Application Insights omówienie dostępności
description: Konfigurowanie cyklicznych testów sieci Web w celu monitorowania dostępności i czasu odpowiedzi aplikacji lub witryny internetowej.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: c3b7a1d0bf8c50c77e5062a702bcdd7600d98d7a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520738"
---
# <a name="availability-tests-overview"></a>Omówienie testów dostępności

Po wdrożeniu aplikacji internetowej/witryny internetowej można skonfigurować testy cykliczne w celu monitorowania dostępności i czasu odpowiedzi. [Application Insights](./app-insights-overview.md) wysyła żądania internetowe do aplikacji w regularnych odstępach czasu z punktów na całym świecie. Może ona powiadamiać, jeśli aplikacja nie odpowiada lub reaguje zbyt wolno.

Testy dostępności możesz skonfigurować dla dowolnego punktu końcowego protokołów HTTP lub HTTPS, który jest dostępny za pośrednictwem publicznej sieci Internet. Nie musisz wprowadzać żadnych zmian w testowej witrynie internetowej. W rzeczywistości nie musi to być nawet witryna, która należy do Ciebie. Możesz przetestować dostępność interfejsu API REST, od których zależy Twoja usługa.

## <a name="types-of-availability-tests"></a>Typy testów dostępności

Istnieją cztery typy testów dostępności:

* [Test ping adresu URL:](monitor-web-app-availability.md)ta kategoria ma dwa proste testy, które można utworzyć za pośrednictwem portalu.
    - Podstawowy test ping: prosty test, który można utworzyć w Azure Portal.
    - Standardowy test ping: bardziej zaawansowany standardowy test ping z funkcjami, takich jak używanie dowolnych metod żądania HTTP (na przykład `GET` , , itp.) lub dodawanie `HEAD` `POST` nagłówków niestandardowych.
* [Wieloetapowy test sieci](availability-multistep.md)Web: rejestrowanie sekwencji żądań internetowych, które można odtworzyć w celu przetestowania bardziej złożonych scenariuszy. Wieloetapowe testy sieci Web są tworzone Visual Studio Enterprise i przekazywane do portalu w celu wykonania.
* [Niestandardowe testy dostępności śledzenia:](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)jeśli zdecydujesz się utworzyć niestandardową aplikację do uruchamiania testów dostępności, możesz użyć metody do wysłania wyników `TrackAvailability()` do Application Insights.

> [!IMPORTANT]
> Zarówno test [ping adresu URL,](monitor-web-app-availability.md) jak i wieloetapowy [test](availability-multistep.md) sieci Web polegają na publicznej internetowej infrastrukturze DNS w celu rozpoznania nazw domen przetestowanych punktów końcowych. Oznacza to, że w przypadku korzystania z usługi Prywatna strefa DNS należy upewnić się, że każda nazwa domeny testu jest również rozpoznawana przez [](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) serwery nazw domen publicznych lub, jeśli nie jest to możliwe, można zamiast tego użyć niestandardowych testów dostępności śledzenia.

**Można utworzyć maksymalnie 100 testów dostępności na Application Insights zasobów.**

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Dedykowany [artykuł na temat rozwiązywania problemów.](troubleshoot-availability.md)

## <a name="next-step"></a>Następny krok

* [Alerty dostępności](availability-alerts.md)
* [Wieloetapowe testy sieci Web](availability-multistep.md)
* [Testy adresów URL](monitor-web-app-availability.md)
* [Tworzenie i uruchamianie niestandardowych testów dostępności przy użyciu Azure Functions.](availability-azure-functions.md)