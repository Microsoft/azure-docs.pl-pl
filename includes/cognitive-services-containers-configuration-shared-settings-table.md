---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73484106"
---
Kontener ma następujące ustawienia konfiguracji:

|Wymagane|Ustawienie|Przeznaczenie|
|--|--|--|
|Tak|[ApiKey](#apikey-configuration-setting)|Śledzi informacje o rozliczeniach.|
|Nie|[ApplicationInsights](#applicationinsights-setting)|Umożliwia dodawanie obsługi telemetrii [usługi Azure Application Insights](https://docs.microsoft.com/azure/application-insights) do kontenera.|
|Tak|[Rozliczenia](#billing-configuration-setting)|Określa identyfikator URI punktu końcowego zasobu usługi na platformie Azure.|
|Tak|[Umowy](#eula-setting)| Wskazuje, że licencja dla kontenera została zaakceptowana.|
|Nie|[Pozostało](#fluentd-settings)|Zapisuje dziennik i, opcjonalnie, dane metryki na serwerze z systemem.|
|Nie|Serwer proxy HTTP|Konfiguruje serwer proxy HTTP do wykonywania żądań wychodzących.|
|Nie|[Rejestrowanie](#logging-settings)|Zapewnia obsługę rejestrowania ASP.NET Core dla kontenera. |
|Nie|[Instaluje](#mount-settings)|Odczytuje i zapisuje dane z komputera hosta do kontenera oraz z kontenera z powrotem do komputera hosta.|
