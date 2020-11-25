---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006881"
---
Kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Tak|[ApiKey](#apikey-configuration-setting)|Śledzi informacje o rozliczeniach.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodawanie obsługi telemetrii [usługi Azure Application Insights](/azure/application-insights) do kontenera.|
|Tak|[Rozliczenia](#billing-configuration-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Tak|[Umowy](#eula-setting)| Wskazuje, że licencja dla kontenera została zaakceptowana.|
|Nie|[Pozostało](#fluentd-settings)|Zapisuje dziennik i, opcjonalnie, dane metryki na serwerze z systemem.|
|Nie|Serwer proxy HTTP|Konfiguruje serwer proxy HTTP do wykonywania żądań wychodzących.|
|Nie|[Rejestrowanie](#logging-settings)|Zapewnia obsługę rejestrowania ASP.NET Core dla kontenera. |
|Nie|[Instaluje](#mount-settings)|Odczytuje i zapisuje dane z komputera hosta do kontenera oraz z kontenera z powrotem do komputera hosta.|