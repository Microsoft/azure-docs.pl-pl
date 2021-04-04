---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96001194"
---
To `ApplicationInsights` ustawienie umożliwia dodanie obsługi telemetrii [usługi Azure Application Insights](/azure/application-insights) do kontenera. Application Insights zapewnia szczegółowe monitorowanie kontenera. Możesz łatwo monitorować kontener pod kątem dostępności, wydajności i użycia. Można również szybko identyfikować i diagnozować błędy w kontenerze.

W poniższej tabeli opisano ustawienia konfiguracji obsługiwane w `ApplicationInsights` sekcji.

|Wymagane| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Nie| `InstrumentationKey` | Ciąg | Klucz Instrumentacji wystąpienia Application Insights, do którego są wysyłane dane telemetryczne dla kontenera. Aby uzyskać więcej informacji, zobacz [Application Insights ASP.NET Core](../articles/azure-monitor/app/asp-net-core.md). <br><br>Przykład:<br>`InstrumentationKey=123456789`|