---
title: Uwierzytelnianie i autoryzacja interfejsu API — Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji dla aplikacji niestandardowej, która wywołuje interfejs API Azure Time Series Insights.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 88fd575d40cc31f12f052158bda0aed9a5335555
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009270"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja na potrzeby interfejsu API usługi Azure Time Series Insights

W zależności od potrzeb firmy Twoje rozwiązanie może zawierać co najmniej jedną aplikację kliencką używaną do współpracy z [interfejsami API](/rest/api/time-series-insights/reference-data-access-overview)środowiska Azure Time Series Insights. Azure Time Series Insights wykonuje uwierzytelnianie przy użyciu [tokenów zabezpieczeń usługi Azure AD opartych na protokole OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Aby uwierzytelnić klientów, należy uzyskać token okaziciela z właściwymi uprawnieniami i przekazać go wraz z wywołaniami interfejsu API. W tym dokumencie opisano kilka metod uzyskiwania poświadczeń, których można użyć w celu uzyskania tokenu okaziciela i uwierzytelnienia, w tym za pomocą tożsamości zarządzanej i Azure Active Directory rejestracji aplikacji.

## <a name="managed-identities"></a>Tożsamości zarządzane

W poniższych sekcjach opisano sposób używania tożsamości zarządzanej z usługi Azure Active Directory (Azure AD) w celu uzyskania dostępu do interfejsu API Azure Time Series Insights. Na platformie Azure tożsamości zarządzane eliminują konieczność zarządzania poświadczeniami przy użyciu tożsamości dla zasobów platformy Azure w usłudze Azure AD i uzyskiwania tokenów Azure Active Directory (Azure AD). Oto niektóre korzyści wynikające z używania tożsamości zarządzanych:

- Nie musisz zarządzać poświadczeniami. Poświadczenia nie są jeszcze dostępne dla Ciebie.
- Przy użyciu tożsamości zarządzanych można uwierzytelniać się w dowolnej usłudze platformy Azure, która obsługuje uwierzytelnianie usługi Azure AD, w tym Azure Key Vault.
- Tożsamości zarządzane mogą być używane bez dodatkowych kosztów.

Aby uzyskać więcej informacji na temat dwóch typów zarządzanych tożsamości, zobacz, [jakie są zarządzane tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Tożsamości zarządzane można używać z:

- Maszyny wirtualne platformy Azure
- Azure App Services
- Azure Functions
- Azure Container Instances
- i nie tylko...

Aby uzyskać pełną listę, zobacz [usługi platformy Azure, które obsługują zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) .

## <a name="azure-active-directory-app-registration"></a>Rejestracja aplikacji Azure Active Directory

Jeśli to możliwe, zalecamy używanie zarządzanych tożsamości, aby nie trzeba było zarządzać poświadczeniami. Jeśli aplikacja kliencka nie jest hostowana w usłudze platformy Azure, która obsługuje zarządzane tożsamości, możesz zarejestrować aplikację w dzierżawie usługi Azure AD. Po zarejestrowaniu aplikacji w usłudze Azure AD tworzysz konfigurację tożsamości dla aplikacji, która umożliwia integrację z usługą Azure AD. Po zarejestrowaniu aplikacji w [Azure Portal](https://portal.azure.com/)należy określić, czy jest to jedyna dzierżawa (dostępna tylko w dzierżawie) czy wiele dzierżawców (dostępną w innych dzierżawcach) i opcjonalnie może ustawić identyfikator URI przekierowania (do którego jest wysyłany token dostępu).

Po zakończeniu rejestracji aplikacji masz globalne, unikatowe wystąpienie aplikacji (obiektu aplikacji), które znajdują się w dzierżawie lub katalogu głównym. Masz również globalnie unikatowy identyfikator dla aplikacji (Identyfikator aplikacji lub klienta). W portalu możesz dodać wpisy tajne lub certyfikaty i zakresy, aby umożliwić działanie aplikacji, dostosować znakowanie aplikacji w oknie dialogowym logowania i nie tylko.

Jeśli aplikacja zostanie zarejestrowana w portalu, obiekt aplikacji oraz obiekt główny usługi są tworzone automatycznie w dzierżawie głównej. Jeśli zarejestrujesz/utworzysz aplikację przy użyciu Microsoft Graph interfejsów API, tworzenie obiektu głównego usługi jest osobnym krokiem. Do żądania tokenów wymagany jest obiekt główny usługi.

Pamiętaj, aby przejrzeć listę kontrolną [zabezpieczeń](../active-directory/develop/identity-platform-integration-checklist.md#security) dla swojej aplikacji. Najlepszym rozwiązaniem jest użycie [poświadczeń certyfikatów](../active-directory/develop/active-directory-certificate-credentials.md), a nie poświadczeń hasła (kluczy tajnych klienta).

Aby uzyskać więcej informacji, zobacz temat [obiekty główne aplikacji i usługi w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) .

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Krok 1. Tworzenie tożsamości zarządzanej lub Rejestracja aplikacji

Po ustaleniu, czy będziesz używać tożsamości zarządzanej, czy rejestracji aplikacji, następnym krokiem jest zainicjowanie obsługi administracyjnej.

### <a name="managed-identity"></a>Tożsamość zarządzana

Kroki, które będą używane do tworzenia tożsamości zarządzanej, będą się różnić w zależności od tego, gdzie znajduje się kod, oraz od tego, czy jest tworzona przypisana przez system czy tożsamość przypisana do użytkownika. Odczytaj [zarządzane typy tożsamości](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) , aby zrozumieć różnicę. Po wybraniu typu tożsamości Znajdź i postępuj zgodnie z poprawnym samouczkiem w [dokumentacji](../active-directory/managed-identities-azure-resources/index.yml)dotyczącej tożsamości zarządzanych przez usługę Azure AD. Znajdziesz instrukcje dotyczące konfigurowania tożsamości zarządzanych dla programu:

- [Maszyny wirtualne platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service i Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- i nie tylko...

### <a name="application-registration"></a>Rejestracja aplikacji

Wykonaj kroki opisane w temacie [Rejestrowanie aplikacji](../active-directory/develop/quickstart-register-app.md#register-an-application).

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Krok 2. udzielanie dostępu

Gdy środowisko Azure Time Series Insights odbiera żądanie, najpierw zostanie sprawdzony token okaziciela osoby wywołującej. Jeśli walidacja kończy się powodzeniem, obiekt wywołujący został uwierzytelniony, a następnie zostanie wykonane inne sprawdzenie w celu upewnienia się, że obiekt wywołujący jest autoryzowany do wykonania żądanej akcji. Aby autoryzować dowolnego użytkownika lub jednostkę usługi, należy najpierw przyznać im dostęp do środowiska, przypisując im rolę czytelnik lub współautor.

- Aby udzielić dostępu za pośrednictwem interfejsu użytkownika [Azure Portal](https://portal.azure.com/) , postępuj zgodnie z instrukcjami wymienionymi w artykule [udzielanie dostępu do danych do środowiska](concepts-access-policies.md) . Po wybraniu użytkownika można wyszukać tożsamość zarządzaną lub rejestrację aplikacji według jej nazwy lub identyfikatora.

- Aby udzielić dostępu przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenie. Zapoznaj się z dokumentacją w [tym miejscu](/cli/azure/ext/timeseriesinsights/tsi/access-policy) , aby zapoznać się z pełną listą poleceń dostępnych do zarządzania dostępem.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Rozszerzenie timeseriesinsights dla interfejsu wiersza polecenia platformy Azure wymaga wersji 2.11.0 lub nowszej. Rozszerzenie zostanie automatycznie zainstalowane podczas pierwszego uruchomienia polecenia AZ TSI Access-Policy. [Dowiedz się więcej](/cli/azure/azure-cli-extensions-overview) o rozszerzeniach.

## <a name="step-3-requesting-tokens"></a>Krok 3. żądanie tokenów

Gdy zarządzana tożsamość lub Rejestracja aplikacji została zainicjowana i przypisana do roli, możesz rozpocząć korzystanie z niej w celu żądania tokenów okaziciela OAuth 2,0. Metoda używana do uzyskiwania tokenu będzie różnić się w zależności od tego, gdzie jest hostowany kod, oraz wybranego języka. Podczas określania zasobu (znanego również jako "odbiorcy") można zidentyfikować Azure Time Series Insights za pomocą adresu URL lub identyfikatora GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Jeśli adres URL jest używany jako identyfikator zasobu, token musi być wystawiony dokładnie do `https://api.timeseries.azure.com/` . Końcowy ukośnik jest wymagany.

> * W przypadku korzystania z programu [Poster](https://www.getpostman.com/) **AuthURL** będzie: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` jest prawidłowy, ale `https://api.timeseries.azure.com` nie jest.

### <a name="managed-identities"></a>Tożsamości zarządzane

Podczas uzyskiwania dostępu do Azure App Service lub funkcji postępuj zgodnie ze wskazówkami zawartymi w temacie [Uzyskiwanie tokenów dla zasobów platformy Azure](../app-service/overview-managed-identity.md).

W przypadku aplikacji i funkcji platformy .NET Najprostszym sposobem pracy z zarządzaną tożsamością jest użycie [biblioteki Azure Identity Client Library](/dotnet/api/overview/azure/identity-readme) dla platformy .NET. Ta Biblioteka kliencka jest popularna ze względu na prostotę i bezpieczeństwo. Deweloperzy mogą napisać kod raz i pozwolić bibliotece klienta ustalić sposób uwierzytelniania w oparciu o środowisko aplikacji — czy na stacji roboczej dewelopera korzysta z konta dewelopera lub wdrożonego na platformie Azure przy użyciu tożsamości usługi zarządzanej. Aby uzyskać wskazówki dotyczące migracji z biblioteki AppAuthentication poprzednik, Przeczytaj [AppAuthentication na platformie Azure. wskazówki dotyczące migracji tożsamości](/dotnet/api/overview/azure/app-auth-migration).

Zażądaj tokenu Azure Time Series Insights przy użyciu języka C# i biblioteki klienta tożsamości platformy Azure dla platformy .NET:

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>Rejestrowanie aplikacji

* Deweloperzy mogą używać [biblioteki Microsoft Authentication Library](../active-directory/develop/msal-overview.md) (MSAL), aby uzyskiwać tokeny dla rejestracji aplikacji.

MSAL można używać w wielu scenariuszach aplikacji, w tym między innymi z:

* [Aplikacje jednostronicowe (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Podpisywanie aplikacji sieci Web przez użytkownika i wywoływanie internetowego interfejsu API w imieniu użytkownika](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Interfejs API sieci Web wywołujący inny podrzędny interfejs API sieci Web w imieniu zalogowanego użytkownika](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Aplikacja klasyczna wywołująca internetowy interfejs API w imieniu zalogowanego użytkownika](../active-directory/develop/scenario-desktop-overview.md)
* [Aplikacja mobilna wywołuje internetowy interfejs API w imieniu użytkownika, który jest zalogowany interaktywnie](../active-directory/develop/scenario-mobile-overview.md).
* [Aplikacja demona pulpitu/usługi wywołująca internetowy interfejs API w imieniu siebie](../active-directory/develop/scenario-daemon-overview.md)

Przykładowy kod w języku C# pokazujący, jak uzyskać token jako rejestrację aplikacji i dane zapytań ze środowiska Gen2, wyświetlić przykładową aplikację w witrynie [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Azure Active Directory w przypadku korzystania z [biblioteki MSAL Authentication Library (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) Przeczytaj informacje [na temat migrowania do programu](../active-directory/develop/msal-net-migration.md).

## <a name="common-headers-and-parameters"></a>Wspólne nagłówki i parametry

W tej sekcji opisano typowe nagłówki i parametry żądań HTTP służące do wykonywania zapytań dotyczących interfejsów API Azure Time Series Insights Gen1 i Gen2. Wymagania dotyczące interfejsu API są szczegółowo opisane w [dokumentacji dotyczącej interfejsu API REST Azure Time Series Insights](/rest/api/time-series-insights/).

> [!TIP]
> Przeczytaj informacje o [interfejsie API REST platformy Azure](/rest/api/azure/) , aby dowiedzieć się więcej na temat korzystania z interfejsów API REST, wykonywania żądań HTTP i obsługi odpowiedzi HTTP.

### <a name="http-headers"></a>Nagłówki HTTP

Wymagane nagłówki żądań są opisane poniżej.

| Wymagany nagłówek żądania | Opis |
| --- | --- |
| Autoryzacja | Aby uwierzytelnić się za pomocą Azure Time Series Insights, należy przesłać prawidłowy token okaziciela OAuth 2,0 w [nagłówku autoryzacji](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Zapoznaj się z [przykładową wizualizacją zestawu SDK klienta](https://tsiclientsample.azurewebsites.net/) hostowanego Azure Time Series Insights, aby dowiedzieć się, jak uwierzytelniać się za pomocą interfejsu API Azure Time Series Insights programowo przy użyciu [zestawu SDK klienta JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) oraz wykresów i grafów

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

| Release | Wartości wersji interfejsu API |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Opcjonalne parametry ciągu zapytania URL obejmują ustawianie limitu czasu dla czasów wykonywania żądań HTTP.

| Opcjonalny parametr zapytania | Opis | Wersja |
| --- |  --- | --- |
| `timeout=<timeout>` | Limit czasu po stronie serwera dla wykonywania żądania HTTP. Dotyczy tylko [zdarzeń Get Environment](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) i [Get Environment](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) API. Wartość limitu czasu powinna mieć format czasu trwania ISO 8601, na przykład `"PT20S"` i musi należeć do zakresu `1-30 s` . Wartość domyślna to `30 s`. | Gen1 |
| `storeType=<storeType>` | W przypadku środowisk Gen2 z włączonym rozgrzanym magazynem zapytanie można wykonać przy użyciu `WarmStore` lub `ColdStore` . Ten parametr w zapytaniu definiuje magazyn, dla którego ma zostać wykonane zapytanie. Jeśli nie zostanie zdefiniowana, zapytanie zostanie wykonane w chłodnym magazynie. Aby zbadać magazyn ciepły, należy ustawić wartość **magazynutype** na `WarmStore` . Jeśli nie zostanie zdefiniowana, zapytanie zostanie wykonane względem zimnego magazynu. | Gen2 |

## <a name="next-steps"></a>Następne kroki

* W przypadku przykładowego kodu, który wywołuje interfejs API Azure Time Series Insights Gen1, Odczytaj [dane Gen1 zapytania przy użyciu języka C#](./time-series-insights-query-data-csharp.md).

* W przypadku przykładowego kodu, który wywołuje przykłady kodu interfejsu API Gen2 Azure Time Series Insights, Odczytaj [dane Gen2 zapytania przy użyciu języka C#](./time-series-insights-update-query-data-csharp.md).

* Informacje referencyjne dotyczące interfejsu API można znaleźć w dokumentacji dotyczącej [interfejsu API zapytań](/rest/api/time-series-insights/reference-query-apis) .
