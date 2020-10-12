---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67712558"
---
To `ApplicationInsights` ustawienie umożliwia dodanie obsługi telemetrii [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do kontenera. Application Insights zapewnia szczegółowe monitorowanie kontenera. Możesz łatwo monitorować kontener pod kątem dostępności, wydajności i użycia. Można również szybko identyfikować i diagnozować błędy w kontenerze.

W poniższej tabeli opisano ustawienia konfiguracji obsługiwane w `ApplicationInsights` sekcji.

|Wymagane| Nazwa | Typ danych | Opis |
|--|------|-----------|-------------|
|Nie| `InstrumentationKey` | Ciąg | Klucz Instrumentacji wystąpienia Application Insights, do którego są wysyłane dane telemetryczne dla kontenera. Aby uzyskać więcej informacji, zobacz [Application Insights ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Przykład:<br>`InstrumentationKey=123456789`|

