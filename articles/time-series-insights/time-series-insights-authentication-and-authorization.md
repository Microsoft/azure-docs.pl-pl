---
title: Uwierzytelnianie i autoryzacja interfejsu API — Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji dla aplikacji niestandardowej, która wywołuje interfejs API Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: d1bd3c5796658663b6111723829cbe620346002c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016245"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja na potrzeby interfejsu API usługi Azure Time Series Insights

W tym dokumencie opisano sposób rejestrowania aplikacji w Azure Active Directory przy użyciu bloku nowy Azure Active Directory. Aplikacje zarejestrowane w Azure Active Directory umożliwiają użytkownikom uwierzytelnianie w usłudze i Zezwalanie na korzystanie z interfejsu API usługi Azure Time Series Insights skojarzonego ze środowiskiem Azure Time Series Insights.

## <a name="service-principal"></a>Jednostka usługi

W poniższych sekcjach opisano sposób konfigurowania aplikacji w celu uzyskiwania dostępu do interfejsu API Azure Time Series Insights w imieniu aplikacji. Aplikacja może następnie badać lub publikować dane referencyjne w środowisku Azure Time Series Insights przy użyciu własnych poświadczeń aplikacji za pośrednictwem Azure Active Directory.

## <a name="summary-and-best-practices"></a>Podsumowanie i najlepsze rozwiązania

Przepływ rejestracji aplikacji Azure Active Directory obejmuje trzy główne kroki.

1. [Zarejestruj aplikację](#azure-active-directory-app-registration) w Azure Active Directory.
1. Autoryzuj aplikację w taki sposób, aby miała [dostęp do danych środowiska Azure Time Series Insights](#granting-data-access).
1. Użyj **identyfikatora aplikacji** i **klucza tajnego klienta** , aby uzyskać token z `https://api.timeseries.azure.com/` w [aplikacji klienckiej](#client-app-initialization). Tokenu można następnie użyć do wywołania interfejsu API Azure Time Series Insights.

Na **krok 3** oddzielenie poświadczeń aplikacji i użytkownika pozwala:

* Przypisywanie uprawnień do tożsamości aplikacji, które różnią się od własnych uprawnień. Zazwyczaj te uprawnienia są ograniczone tylko do wymaganej aplikacji. Można na przykład zezwolić aplikacji na odczytywanie danych tylko z określonego środowiska Azure Time Series Insights.
* Odizolowanie zabezpieczeń aplikacji od tworzenia poświadczeń uwierzytelniania użytkownika przy użyciu **klucza tajnego klienta** lub certyfikatu zabezpieczeń. W związku z tym poświadczenia aplikacji nie są zależne od poświadczeń określonego użytkownika. Jeśli rola użytkownika ulegnie zmianie, aplikacja nie musi wymagać nowych poświadczeń lub dalszej konfiguracji. Jeśli użytkownik zmieni hasło, cały dostęp do aplikacji nie wymaga nowych poświadczeń ani kluczy.
* Uruchom skrypt nienadzorowany przy użyciu **klucza tajnego klienta** lub certyfikatu zabezpieczeń, a nie poświadczeń określonego użytkownika (wymaganie ich obecności).
* Użyj certyfikatu zabezpieczeń zamiast hasła, aby zabezpieczyć dostęp do interfejsu API Azure Time Series Insights.

> [!IMPORTANT]
> Postępuj zgodnie z zasadami **rozdzielenia problemów** (opisanymi w tym scenariuszu) podczas konfigurowania zasad zabezpieczeń Azure Time Series Insights.

> [!NOTE]

> * Artykuł koncentruje się na aplikacji z jedną dzierżawą, w której aplikacja jest przeznaczona do działania tylko w jednej organizacji.
> * Zwykle używasz aplikacji z jedną dzierżawą dla aplikacji biznesowych, które działają w organizacji.

## <a name="detailed-setup"></a>Szczegółowa konfiguracja

### <a name="azure-active-directory-app-registration"></a>Rejestracja aplikacji Azure Active Directory

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Udzielanie dostępu do danych

1. Dla środowiska Azure Time Series Insights wybierz pozycję **zasady dostępu do danych** i wybierz pozycję **Dodaj**.

   [![Dodawanie nowych zasad dostępu do danych do środowiska Azure Time Series Insights](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. W oknie dialogowym **Wybieranie użytkownika** wklej **nazwę aplikacji** lub **Identyfikator aplikacji** z sekcji Azure Active Directory rejestracji aplikacji.

   [![Znajdź aplikację w oknie dialogowym Wybieranie użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Wybierz rolę. Wybierz opcję **czytelnik** , aby wykonać zapytanie dotyczące danych lub **współautora** , aby wykonać zapytanie o dane i zmienić dane referencyjne Wybierz przycisk **OK**.

   [![Wybieranie czytnika lub współautora w oknie dialogowym Wybieranie roli użytkownika](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Zapisz zasady, wybierając **przycisk OK**.

   > [!TIP]
   > Aby uzyskać zaawansowane opcje dostępu do danych, należy przeczytać artykuł [udzielanie dostępu do danych](./concepts-access-policies.md).

### <a name="client-app-initialization"></a>Inicjowanie aplikacji klienta

* Deweloperzy mogą używać biblioteki Microsoft Authentication Library (MSAL) do uwierzytelniania za pomocą Azure Time Series Insights.

* Aby uwierzytelnić się za pomocą MSAL:

   1. Użyj **identyfikatora aplikacji** i klucza **tajnego klienta** (klucza aplikacji) z sekcji Azure Active Directory rejestracji aplikacji, aby uzyskać token w imieniu aplikacji.

   1. W języku C# Poniższy kod może uzyskać token w imieniu aplikacji. Aby zapoznać się z kompletnym przykładem na temat wykonywania zapytań dotyczących danych ze środowiska Gen1, Przeczytaj [dane zapytania przy użyciu języka C#](time-series-insights-query-data-csharp.md).

        Zobacz repozytorium [Azure Time Series Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen1-sample/csharp-tsi-gen1-sample/Program.cs)], aby uzyskać dostęp do kodu w języku C#.

   1. Token można następnie przesłać do `Authorization` nagłówka, gdy aplikacja wywoła interfejs API Azure Time Series Insights.

> [!IMPORTANT]
> Azure Active Directory w przypadku korzystania z [biblioteki MSAL Authentication Library (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) Przeczytaj informacje [na temat migrowania do programu](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Wspólne nagłówki i parametry

W tej sekcji opisano typowe nagłówki i parametry żądań HTTP służące do wykonywania zapytań dotyczących interfejsów API Azure Time Series Insights Gen1 i Gen2. Wymagania dotyczące interfejsu API są szczegółowo opisane w [dokumentacji dotyczącej interfejsu API REST Azure Time Series Insights](/rest/api/time-series-insights/).

> [!TIP]
> Przeczytaj informacje o [interfejsie API REST platformy Azure](/rest/api/azure/) , aby dowiedzieć się więcej na temat korzystania z interfejsów API REST, wykonywania żądań HTTP i obsługi odpowiedzi HTTP.

### <a name="authentication"></a>Authentication

Aby wykonać uwierzytelnione zapytania dotyczące [Azure Time Series Insights interfejsów API REST](/rest/api/time-series-insights/), należy przesłać prawidłowy token okaziciela OAuth 2,0 w [nagłówku autoryzacji](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) za pomocą wybranego przez siebie klienta REST (Poster, JavaScript, C#).

> [!TIP]
> Zapoznaj się z [przykładową wizualizacją zestawu SDK klienta](https://tsiclientsample.azurewebsites.net/) hostowanego Azure Time Series Insights, aby dowiedzieć się, jak uwierzytelniać się za pomocą interfejsu API Azure Time Series Insights programowo przy użyciu [zestawu SDK klienta JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) oraz wykresów i grafów

### <a name="http-headers"></a>Nagłówki HTTP

Wymagane nagłówki żądań są opisane poniżej.

| Wymagany nagłówek żądania | Opis |
| --- | --- |
| Autoryzacja | Aby uwierzytelnić się za pomocą Azure Time Series Insights, należy przesłać prawidłowy token okaziciela OAuth 2,0 w nagłówku **autoryzacji** . |

> [!IMPORTANT]
> Token musi być wystawiony dokładnie dla `https://api.timeseries.azure.com/` zasobu (znanego również jako "odbiorcy" tokenu).

> * W związku z tym **AuthURL** Twojego [ogłaszania](https://www.getpostman.com/) będzie:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` jest prawidłowy, ale `https://api.timeseries.azure.com` nie jest.

Opcjonalne nagłówki żądań są opisane poniżej.

| Opcjonalny nagłówek żądania | Opis |
| --- | --- |
| Typ zawartości | `application/json`obsługiwane są tylko. |
| x-MS-Client-Request-ID | Identyfikator żądania klienta. Usługa rejestruje tę wartość. Umożliwia usłudze śledzenie operacji między usługami. |
| x-MS-Client-Session-ID | Identyfikator sesji klienta. Usługa rejestruje tę wartość. Umożliwia usłudze Śledzenie grupy powiązanych operacji między usługami. |
| x-MS-Client-Application-Name | Nazwa aplikacji, która wygenerowała to żądanie. Usługa rejestruje tę wartość. |

Opcjonalne, ale zalecane nagłówki odpowiedzi są opisane poniżej.

| Nagłówek odpowiedzi | Opis |
| --- | --- |
| Typ zawartości | `application/json`Obsługiwane są tylko. |
| x-MS-Request-ID | Identyfikator żądania wygenerowany przez serwer. Można go użyć do skontaktowania się z firmą Microsoft w celu zbadania żądania. |
| zachowanie x-MS-Property-not----------- | Opcjonalny nagłówek odpowiedzi interfejsu API usługi GA. Możliwe wartości to `ThrowError` (domyślne) lub `UseNull` . |

### <a name="http-parameters"></a>Parametry HTTP

> [!TIP]
> Więcej informacji na temat wymaganych i opcjonalnych informacji o zapytaniach znajduje się w [dokumentacji referencyjnej](/rest/api/time-series-insights/).

Parametry ciągu zapytania wymaganego adresu URL są zależne od wersji interfejsu API.

| Release | Możliwe wartości wersji interfejsu API |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31` i `api-version=2018-11-01-preview`|

> [!IMPORTANT]
>
> `api-version=2018-11-01-preview`Wersja zostanie wkrótce wycofana. Zalecamy przełączenie się do nowszej wersji.

Opcjonalne parametry ciągu zapytania URL obejmują ustawianie limitu czasu dla czasów wykonywania żądań HTTP.

| Opcjonalny parametr zapytania | Opis | Wersja |
| --- |  --- | --- |
| `timeout=<timeout>` | Limit czasu po stronie serwera dla wykonywania żądania HTTP. Dotyczy tylko [zdarzeń Get Environment](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) i [Get Environment](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) API. Wartość limitu czasu powinna mieć format czasu trwania ISO 8601, na przykład `"PT20S"` i musi należeć do zakresu `1-30 s` . Wartość domyślna to `30 s`. | Gen1 |
| `storeType=<storeType>` | W przypadku środowisk Gen2 z włączonym rozgrzanym magazynem zapytanie można wykonać przy użyciu `WarmStore` lub `ColdStore` . Ten parametr w zapytaniu definiuje magazyn, dla którego ma zostać wykonane zapytanie. Jeśli nie zostanie zdefiniowana, zapytanie zostanie wykonane w chłodnym magazynie. Aby zbadać magazyn ciepły, należy ustawić wartość **magazynutype** na `WarmStore` . Jeśli nie zostanie zdefiniowana, zapytanie zostanie wykonane względem zimnego magazynu. | Gen2 |

## <a name="next-steps"></a>Następne kroki

* W przypadku przykładowego kodu, który wywołuje interfejs API Azure Time Series Insights Gen1, Odczytaj [dane Gen1 zapytania przy użyciu języka C#](./time-series-insights-query-data-csharp.md).

* W przypadku przykładowego kodu, który wywołuje przykłady kodu interfejsu API Gen2 Azure Time Series Insights, Odczytaj [dane Gen2 zapytania przy użyciu języka C#](./time-series-insights-update-query-data-csharp.md).

* Informacje referencyjne dotyczące interfejsu API można znaleźć w dokumentacji dotyczącej [interfejsu API zapytań](/rest/api/time-series-insights/gen1-query-api) .

* Dowiedz się, jak [utworzyć jednostkę usługi](../active-directory/develop/howto-create-service-principal-portal.md).