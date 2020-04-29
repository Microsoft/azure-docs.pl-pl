---
title: Wykonywanie zapytań dotyczących danych ze środowiska GA przy użyciu kodu C# — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak wykonywać zapytania dotyczące danych ze środowiska Azure Time Series Insights przy użyciu aplikacji niestandardowej nadanej w języku C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 754d1b80236d138693987cccee7a218ccd96b16b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383882"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>Wykonywanie zapytań dotyczących danych ze środowiska Azure Time Series Insights GA przy użyciu języka C #

W tym przykładzie w języku C# pokazano, jak używać [interfejsów API zapytania ga](https://docs.microsoft.com/rest/api/time-series-insights/ga-query) do wykonywania zapytań dotyczących danych w środowiskach Azure Time Series Insights.

> [!TIP]
> Wyświetl przykłady kodu w języku C# [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)w lokalizacji.

## <a name="summary"></a>Podsumowanie

Poniższy przykładowy kod ilustruje następujące funkcje:

* Jak uzyskać token dostępu za pośrednictwem Azure Active Directory przy użyciu [programu Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Jak przekazać token uzyskany dostęp w `Authorization` nagłówku kolejnych żądań interfejsu API zapytań. 

* Przykład wywołuje każdy interfejs API GA zapytania, pokazujący, jak są wysyłane żądania HTTP do:
    * [Uzyskaj interfejs API środowisk](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api) , aby zwrócić środowiska, do których użytkownik ma dostęp
    * [Pobierz interfejs API dostępności środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [Pobierz interfejs API metadanych środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api) , aby pobrać metadane środowiska
    * [Interfejs API zdarzeń pobierania środowisk](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [Pobierz interfejs API agregacji środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* Jak korzystać z interfejsów API zapytań GA przy użyciu programu WSS do wysyłania komunikatów do:

   * [Pobierz strumień interfejsu API zdarzeń środowiska](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [Pobierz strumień API usługi agregowania środowiskowego](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Wymagania wstępne i Instalator

Przed skompilowaniem i uruchomieniem przykładowego kodu wykonaj następujące czynności:

1. [Zainicjuj obsługę środowiska Azure Time Series Insightsowego](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started) .
1. Skonfiguruj środowisko Azure Time Series Insights dla Azure Active Directory zgodnie z opisem w temacie [uwierzytelnianie i autoryzacja](time-series-insights-authentication-and-authorization.md). 
1. Zainstaluj wymagane zależności projektu.
1. Edytuj przykładowy kod poniżej, zastępując każdy **#DUMMY #** odpowiednim identyfikatorem środowiska.
1. Wykonaj kod w programie Visual Studio.

## <a name="project-dependencies"></a>Zależności projektu

Zalecamy użycie najnowszej wersji programu Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) — wersja 16.4.2 +

Przykładowy kod ma dwie wymagane zależności:

* [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9 Package.
* [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) — pakiet 9.0.1.

Pobierz pakiety w programie Visual Studio 2019, wybierając opcję **Kompiluj** > **kompilację rozwiązania** .

Alternatywnie Dodaj pakiety przy użyciu narzędzia [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Przykładowy kod w języku C#

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o wysyłaniu zapytań, Przeczytaj [Informacje o interfejsie API zapytań](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api).

- Dowiedz się [, jak połączyć aplikację JavaScript przy użyciu zestawu SDK klienta,](https://github.com/microsoft/tsiclient) aby Time Series Insights.
