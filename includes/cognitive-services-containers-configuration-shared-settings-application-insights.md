---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001194"
---
To `ApplicationInsights` ustawienie umożliwia dodanie obsługi telemetrii [usługi Azure Application Insights](/azure/application-insights) do kontenera. Application Insights zapewnia szczegółowe monitorowanie kontenera. Możesz łatwo monitorować kontener pod kątem dostępności, wydajności i użycia. Można również szybko identyfikować i diagnozować błędy w kontenerze.

W poniższej tabeli opisano ustawienia konfiguracji obsługiwane w `ApplicationInsights` sekcji.

|Wymagane| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Nie| `InstrumentationKey` | Ciąg | Klucz Instrumentacji wystąpienia Application Insights, do którego są wysyłane dane telemetryczne dla kontenera. Aby uzyskać więcej informacji, zobacz [Application Insights ASP.NET Core](../articles/azure-monitor/app/asp-net-core.md). <br><br>Przykład:<br>`InstrumentationKey=123456789`|