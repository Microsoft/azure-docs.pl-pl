---
title: Zapoznaj się z usługą Azure Application Insights | Microsoft Docs
description: Po wprowadzeniem do Application Insights poniżej przedstawiono podsumowanie funkcji, które można eksplorować.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 6d30833840b62412ae03c761baacf54c939c9d33
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540030"
---
# <a name="more-telemetry-from-application-insights"></a>Więcej danych telemetrycznych z Application Insights
Po [dodaniu Application Insights do kodu ASP.NET](../../azure-monitor/app/asp-net.md)istnieje kilka rzeczy, które można zrobić, aby uzyskać jeszcze więcej danych telemetrycznych. 

| Akcja | Co otrzymujemy|
|---|---|
|(Serwery IIS) [Zainstaluj Monitor stanu](https://go.microsoft.com/fwlink/?LinkId=506648) na każdym komputerze serwera.<br/>(Aplikacje sieci Web platformy Azure) W panelu sterowania platformy Azure dla aplikacji sieci Web otwórz blok Application Insights.| [**Liczniki wydajności**](../../azure-monitor/app/performance-counters.md)<br/>[**Wyjątki**](asp-net-exceptions.md) — szczegółowe dane śledzenia stosu<br/>[**Zależności**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Dodaj fragment kodu JavaScript do stron sieci Web](../../azure-monitor/app/javascript.md)|[Wydajność strony](../../azure-monitor/app/usage-overview.md), wyjątki przeglądarki i wydajność AJAX. Niestandardowa Telemetria po stronie klienta.|
|[Tworzenie testów dostępności sieci Web](../../azure-monitor/app/monitor-web-app-availability.md)|Otrzymywanie alertów, jeśli witryna jest niedostępna|
|[Upewnij się, że buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) jest generowana przez MSBuild|[Tworzenie adnotacji na wykresach metryk](./annotations.md)
|[Zapisz niestandardowe zdarzenia i metryki](../../azure-monitor/app/api-custom-events-metrics.md)|Zliczaj zdarzenia biznesowe i metryki, śledź szczegółowe użycie i nie tylko.|
|[Profilowanie aktywnej witryny](https://aka.ms/AIProfilerPreview)|Szczegółowe terminy funkcji z działającej aplikacji sieci Web|
