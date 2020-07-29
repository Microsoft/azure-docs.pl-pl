---
title: Zapoznaj się z usługą Azure Application Insights | Microsoft Docs
description: Po wprowadzeniem do Application Insights poniżej przedstawiono podsumowanie funkcji, które można eksplorować.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: c0be377636159dbb2bdb628af6a0bc9077942397
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321347"
---
# <a name="more-telemetry-from-application-insights"></a>Więcej danych telemetrycznych z Application Insights
Po [dodaniu Application Insights do kodu ASP.NET](./asp-net.md)istnieje kilka rzeczy, które można zrobić, aby uzyskać jeszcze więcej danych telemetrycznych. 

| Akcja | Co otrzymujemy|
|---|---|
|(Serwery IIS) [Zainstaluj Monitor stanu](https://go.microsoft.com/fwlink/?LinkId=506648) na każdym komputerze serwera.<br/>(Aplikacje sieci Web platformy Azure) W panelu sterowania platformy Azure dla aplikacji sieci Web otwórz blok Application Insights.| [**Liczniki wydajności**](./performance-counters.md)<br/>[**Wyjątki**](asp-net-exceptions.md) — szczegółowe dane śledzenia stosu<br/>[**Zależności**](./asp-net-dependencies.md)|
|[Dodaj fragment kodu JavaScript do stron sieci Web](./javascript.md)|[Wydajność strony](./usage-overview.md), wyjątki przeglądarki i wydajność AJAX. Niestandardowa Telemetria po stronie klienta.|
|[Tworzenie testów dostępności sieci Web](./monitor-web-app-availability.md)|Otrzymywanie alertów, jeśli witryna jest niedostępna|
|[Upewnij się, że buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) jest generowana przez MSBuild|[Tworzenie adnotacji na wykresach metryk](./annotations.md)
|[Zapisz niestandardowe zdarzenia i metryki](./api-custom-events-metrics.md)|Zliczaj zdarzenia biznesowe i metryki, śledź szczegółowe użycie i nie tylko.|
|[Profilowanie aktywnej witryny](https://aka.ms/AIProfilerPreview)|Szczegółowe terminy funkcji z działającej aplikacji sieci Web|

