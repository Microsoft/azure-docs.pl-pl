---
title: Wykonywanie zapytań dotyczących danych ze środowiska Gen1 przy użyciu kodu C# — Azure Time Series Insights Gen1 | Microsoft Docs
description: Dowiedz się, jak wykonywać zapytania dotyczące danych ze środowiska Azure Time Series Insights Gen1 przy użyciu aplikacji niestandardowej nadanej w języku C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 724bc80f8887ff2c47db93ecfc4cd2e20be7e7f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020048"
---
# <a name="query-data-from-the-azure-time-series-insights-gen1-environment-using-c-sharp"></a>Wykonywanie zapytań dotyczących danych ze środowiska Azure Time Series Insights Gen1 przy użyciu języka C Sharp

> [!CAUTION]
> To jest artykuł Gen1.

W tym przykładzie w języku C# pokazano, jak używać [interfejsów API zapytań Gen1](/rest/api/time-series-insights/gen1-query) do wykonywania zapytań dotyczących danych ze środowisk Azure Time Series Insights Gen1.

> [!TIP]
> Wyświetl przykłady kodu w języku C# Gen1 w [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Podsumowanie

Poniższy przykładowy kod ilustruje następujące funkcje:

* Jak uzyskać token dostępu za pośrednictwem Azure Active Directory przy użyciu [programu Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

* Jak przekazać token uzyskany dostęp w `Authorization` nagłówku kolejnych żądań interfejsu API zapytań.

* Przykład wywołuje każdy interfejs API zapytania Gen1 pokazujący, jak są wysyłane żądania HTTP do:
  * [Uzyskaj interfejs API środowisk](/rest/api/time-series-insights/gen1-query-api#get-environments-api) , aby zwrócić środowiska, do których użytkownik ma dostęp
  * [Pobierz interfejs API dostępności środowiska](/rest/api/time-series-insights/gen1-query-api#get-environment-availability-api)
  * [Pobierz interfejs API metadanych środowiska](/rest/api/time-series-insights/gen1-query-api#get-environment-metadata-api) , aby pobrać metadane środowiska
  * [Interfejs API zdarzeń pobierania środowisk](/rest/api/time-series-insights/gen1-query-api#get-environment-events-api)
  * [Pobierz interfejs API agregacji środowiska](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api)

* Jak korzystać z interfejsów API zapytań Gen1 przy użyciu programu WSS do wysyłania komunikatów do:

  * [Pobierz strumień interfejsu API zdarzeń środowiska](/rest/api/time-series-insights/gen1-query-api#get-environment-events-streamed-api)
  * [Pobierz strumień API usługi agregowania środowiskowego](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>Wymagania wstępne i Instalator

Przed skompilowaniem i uruchomieniem przykładowego kodu wykonaj następujące czynności:

1. [Zapewnij Gen1 środowisko Azure Time Series Insights](./time-series-insights-get-started.md) .
1. Skonfiguruj środowisko Azure Time Series Insights dla Azure Active Directory zgodnie z opisem w temacie [uwierzytelnianie i autoryzacja](time-series-insights-authentication-and-authorization.md).
1. Zainstaluj wymagane zależności projektu.
1. Edytuj przykładowy kod poniżej, zastępując każdy **#DUMMY #** odpowiednim identyfikatorem środowiska.
1. Wykonaj kod w programie Visual Studio.

## <a name="project-dependencies"></a>Zależności projektu

Zalecamy użycie najnowszej wersji programu Visual Studio:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) — wersja 16.4.2 +

Przykładowy kod ma dwie wymagane zależności:

* [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) -3.13.9 Package.
* [Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json) pakiet 9.0.1.

Pobierz pakiety w programie Visual Studio 2019, wybierając opcję **Kompiluj**  >  **kompilację rozwiązania** .

Alternatywnie Dodaj pakiety przy użyciu narzędzia [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>Przykładowy kod w języku C#

Zapoznaj się z repozytorium [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)], aby uzyskać dostęp do przykładowego kodu w języku C#.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wysyłaniu zapytań, Przeczytaj [Informacje o interfejsie API zapytań](/rest/api/time-series-insights/gen1-query-api).

* Dowiedz się [, jak połączyć aplikację JavaScript przy użyciu zestawu SDK klienta,](https://github.com/microsoft/tsiclient) aby Time Series Insights.
Azure-Samples/Azure-Time-Series-Insights/Gen1-Sample/CSharp-TSI-Gen1-Sample/program. cs