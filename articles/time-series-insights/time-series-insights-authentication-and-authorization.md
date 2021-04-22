---
title: Uwierzytelnianie i autoryzacja interfejsu API — Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania uwierzytelniania i autoryzacji dla aplikacji niestandardowej, która wywołuje Azure Time Series Insights API.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref, devx-track-azurecli
ms.openlocfilehash: 8e50b650eaffe3d0ec8d3d2cd1841bd139d33750
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867516"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Uwierzytelnianie i autoryzacja na potrzeby interfejsu API usługi Azure Time Series Insights

W zależności od potrzeb biznesowych rozwiązanie może obejmować co najmniej jedną aplikacje klienckie, których używasz do interakcji z interfejsami API Azure Time Series Insights [środowiska.](/rest/api/time-series-insights/reference-data-access-overview) Azure Time Series Insights przeprowadza uwierzytelnianie przy użyciu [tokenów zabezpieczających usługi Azure AD na podstawie protokołu OAUTH 2.0.](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) Aby uwierzytelnić klientów, musisz uzyskać token okaziciela z właściwymi uprawnieniami i przekazać go wraz z wywołaniami interfejsu API. W tym dokumencie opisano kilka metod uzyskiwania poświadczeń, których można użyć do uzyskania tokenu okaziciela i uwierzytelnienia, w tym przy użyciu tożsamości zarządzanej i Azure Active Directory rejestracji aplikacji.

## <a name="managed-identities"></a>Tożsamości zarządzane

W poniższych sekcjach opisano sposób używania tożsamości zarządzanej z usługi Azure Active Directory (Azure AD) w celu uzyskania dostępu do Azure Time Series Insights API. Na platformie Azure tożsamości zarządzane eliminują konieczność zarządzania poświadczeniami przez deweloperów przez podanie tożsamości dla zasobu platformy Azure w usłudze Azure AD i użycie jej do uzyskania tokenów usługi Azure Active Directory (Azure AD). Poniżej podano niektóre korzyści wynikające z używania tożsamości zarządzanych:

- Nie musisz zarządzać poświadczeniami. Poświadczenia nie są nawet dostępne dla Ciebie.
- Tożsamości zarządzanych można używać do uwierzytelniania w dowolnej usłudze platformy Azure, która obsługuje uwierzytelnianie usługi Azure AD, w tym w Azure Key Vault.
- Tożsamości zarządzane mogą być używane bez dodatkowych kosztów.

Aby uzyskać więcej informacji na temat dwóch typów tożsamości zarządzanych, przeczytaj [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Tożsamości zarządzanych można używać z:

- Maszyny wirtualne platformy Azure
- Azure App Services
- Azure Functions
- Wystąpienia kontenerów platformy Azure
- i nie tylko...

Aby uzyskać [pełną listę, zobacz](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) Usługi platformy Azure, które obsługują tożsamości zarządzane dla zasobów platformy Azure.

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory rejestracji aplikacji

Zalecamy używanie tożsamości zarządzanych zawsze, gdy jest to możliwe, aby nie trzeba było zarządzać poświadczeniami. Jeśli twoja aplikacja kliency nie jest hostowana w usłudze platformy Azure, która obsługuje tożsamości zarządzane, możesz zarejestrować aplikację w dzierżawie usługi Azure AD. Podczas rejestrowania aplikacji w usłudze Azure AD tworzysz konfigurację tożsamości dla aplikacji, która umożliwia jej integrację z usługą Azure AD. Podczas rejestrowania aplikacji w u użytkownika Azure Portal możesz wybrać, czy jest [to](https://portal.azure.com/)pojedyncza dzierżawa (dostępna tylko w Twojej dzierżawie), czy wielodostępna (dostępna w innych dzierżawach) i opcjonalnie ustawić adres URI przekierowania (do którego jest wysyłany token dostępu).

Po ukończeniu rejestracji aplikacji masz globalnie unikatowe wystąpienie aplikacji (obiektu aplikacji), które znajduje się w twojej głównej dzierżawie lub katalogu. Masz również globalnie unikatowy identyfikator aplikacji (identyfikator aplikacji lub klienta). W portalu można następnie dodawać wpisy tajne lub certyfikaty i zakresy, aby aplikacja działała, dostosowywać znakowanie aplikacji w oknie dialogowym logowania i nie tylko.

Jeśli zarejestrujesz aplikację w portalu, w dzierżawie głównej zostanie automatycznie utworzony obiekt aplikacji oraz obiekt jednostki usługi. Jeśli zarejestrujesz/utworzysz aplikację przy użyciu interfejsów API Microsoft Graph, utworzenie obiektu jednostki usługi jest osobnym krokiem. Obiekt jednostki usługi jest wymagany do żądania tokenów.

Pamiętaj, aby zapoznać się z [listą](../active-directory/develop/identity-platform-integration-checklist.md#security) kontrolną zabezpieczeń dla aplikacji. Najlepszym rozwiązaniem jest użycie poświadczeń [certyfikatu,](../active-directory/develop/active-directory-certificate-credentials.md)a nie poświadczeń haseł (wpisów tajnych klienta).

Aby uzyskać więcej informacji, zobacz [Application and service principal objects in Azure Active Directory (Obiekty](../active-directory/develop/app-objects-and-service-principals.md) aplikacji i jednostki usługi w usłudze).

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>Krok 1. Tworzenie tożsamości zarządzanej lub rejestracji aplikacji

Po zidentyfikowaniu, czy będziesz używać tożsamości zarządzanej, czy rejestracji aplikacji, następnym krokiem jest aprowizować tożsamość.

### <a name="managed-identity"></a>Tożsamość zarządzana

Kroki, które należy wykonać w celu utworzenia tożsamości zarządzanej, różnią się w zależności od tego, gdzie znajduje się kod i czy tworzysz tożsamość przypisaną przez system, czy przypisaną przez użytkownika. Zapoznaj [się z tematem Typy tożsamości](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) zarządzanych, aby zrozumieć różnicę. Po wybraniu typu tożsamości znajdź i postępuj zgodnie z prawidłowym samouczkiem w dokumentacji tożsamości zarządzanych [przez](../active-directory/managed-identities-azure-resources/index.yml)usługę Azure AD. Znajdziesz tam instrukcje dotyczące konfigurowania tożsamości zarządzanych dla:

- [Maszyny wirtualne platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service i Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- i nie tylko...

### <a name="application-registration"></a>Rejestracja aplikacji

Wykonaj kroki opisane w [te tematu Register an application (Rejestrowanie aplikacji).](../active-directory/develop/quickstart-register-app.md#register-an-application)

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>Krok 2. Udzielanie dostępu

Gdy środowisko Azure Time Series Insights odbiera żądanie, najpierw sprawdzany jest token użytkownika wywołującego. Jeśli weryfikacja przebiegnie pomyślnie, wywołujący został uwierzytelniony, a następnie dokonano kolejnego sprawdzenia w celu upewnienia się, że wywołujący jest autoryzowany do wykonania żądanej akcji. Aby autoryzować dowolnego użytkownika lub jednostkę usługi, musisz najpierw przyznać im dostęp do środowiska, przypisując im rolę Czytelnik lub Współautor.

- Aby udzielić dostępu za [pośrednictwem interfejsu Azure Portal](https://portal.azure.com/) użytkownika, postępuj zgodnie z instrukcjami podanymi w artykule Udzielanie dostępu do danych [w środowisku.](concepts-access-policies.md) Po wybraniu użytkownika możesz wyszukać tożsamość zarządzaną lub rejestrację aplikacji według jego nazwy lub identyfikatora.

- Aby udzielić dostępu przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenie. Zapoznaj się z [dokumentacją dostępną](/cli/azure/tsi/access-policy) tutaj, aby uzyskać pełną listę poleceń dostępnych do zarządzania dostępem.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Rozszerzenie timeseriesinsights dla interfejsu wiersza polecenia platformy Azure wymaga wersji 2.11.0 lub wyższej. Rozszerzenie zostanie automatycznie zainstalowane przy pierwszym uruchomieniu polecenia az tsi access-policy. [Dowiedz się więcej](/cli/azure/azure-cli-extensions-overview) o rozszerzeniach.

## <a name="step-3-requesting-tokens"></a>Krok 3. Żądanie tokenów

Gdy tożsamość zarządzana lub rejestracja aplikacji zostanie aprowizowana i przypisana rola, możesz rozpocząć korzystanie z tej tożsamości w celu żądania tokenów okaziciela OAuth 2.0. Metoda uzyskiwania tokenu różni się w zależności od miejsca hostowania kodu i wybranego języka. Podczas określania zasobu (nazywanego również "odbiorcą" tokenu) można zidentyfikować Azure Time Series Insights jego adresu URL lub identyfikatora GUID:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Jeśli używasz adresu URL jako identyfikatora zasobu, token musi być wystawiony dokładnie `https://api.timeseries.azure.com/` dla . Ukośnik na końcowej stronie jest wymagany.

> * W związku z tym w przypadku korzystania z narzędzia [Postman](https://www.getpostman.com/) **adres AuthURL** będzie mieć: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` jest prawidłowy, ale `https://api.timeseries.azure.com` nie.

### <a name="managed-identities"></a>Tożsamości zarządzane

Podczas uzyskiwania dostępu z usługi Azure App Service lub Functions postępuj zgodnie ze wskazówkami w te [tematach Obtain tokens for Azure resources](../app-service/overview-managed-identity.md)(Uzyskiwanie tokenów dla zasobów platformy Azure).

W przypadku aplikacji i funkcji platformy .NET najprostszym sposobem pracy z tożsamością zarządzaną jest korzystanie z [biblioteki klienta](/dotnet/api/overview/azure/identity-readme) tożsamości platformy Azure dla platformy .NET. Ta biblioteka klienta jest popularna ze względu na prostotę i bezpieczeństwo. Deweloperzy mogą napisać kod raz i pozwolić bibliotece klienta określić sposób uwierzytelniania na podstawie środowiska aplikacji — na stacji roboczej dewelopera przy użyciu konta dewelopera lub wdrożonej na platformie Azure przy użyciu tożsamości usługi zarządzanej. Aby uzyskać wskazówki dotyczące migracji z poprzedniej biblioteki AppAuthentication, przeczytaj [AppAuthentication to Azure.Identity Migration Guidance](/dotnet/api/overview/azure/app-auth-migration)(Wskazówki dotyczące migracji usługi AppAuthentication do usługi Azure.Identity).

Zażądaj tokenu na Azure Time Series Insights języka C# i biblioteki klienta tożsamości platformy Azure dla platformy .NET:

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

* Deweloperzy mogą używać [biblioteki Microsoft Authentication Library](../active-directory/develop/msal-overview.md) (MSAL) do uzyskiwania tokenów na użytek rejestracji aplikacji.

MsAL może być używany w wielu scenariuszach aplikacji, w tym między innymi w:

* [Aplikacje jednostronicowe (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Logowanie użytkownika i wywoływanie internetowego interfejsu API w imieniu użytkownika przez aplikację internetową](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Internetowy interfejs API wywołujący inny nadrzędny internetowy interfejs API w imieniu zalogowago użytkownika](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Aplikacja desktop wywołująca internetowy interfejs API w imieniu zalogowaowego użytkownika](../active-directory/develop/scenario-desktop-overview.md)
* [Aplikacja mobilna wywołująca internetowy interfejs API w imieniu użytkownika, który zalogował się interaktywnie.](../active-directory/develop/scenario-mobile-overview.md)
* [Aplikacja demona pulpitu/usługi wywołująca internetowy interfejs API w swoim imieniu](../active-directory/develop/scenario-daemon-overview.md)

Aby uzyskać przykładowy kod w języku C# przedstawiający sposób uzyskania tokenu jako rejestracji aplikacji i wykonywania zapytań o dane ze środowiska Gen2, wyświetl przykładową aplikację w witrynie [GitHub](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Jeśli używasz [biblioteki Azure Active Directory Authentication Library (ADAL),](../active-directory/azuread-dev/active-directory-authentication-libraries.md) przeczytaj o [migracji do biblioteki MSAL.](../active-directory/develop/msal-net-migration.md)

## <a name="common-headers-and-parameters"></a>Typowe nagłówki i parametry

W tej sekcji opisano typowe nagłówki żądań HTTP i parametry używane do zapytań względem interfejsów API Azure Time Series Insights Gen1 i Gen2. Wymagania specyficzne dla interfejsu API zostały szczegółowo opisane w dokumentacji Azure Time Series Insights [REST API](/rest/api/time-series-insights/).

> [!TIP]
> Przeczytaj dokumentacja [interfejsu API REST](/rest/api/azure/) platformy Azure, aby dowiedzieć się więcej na temat sposobu korzystanie z interfejsów API REST, żądań HTTP i obsługi odpowiedzi HTTP.

### <a name="http-headers"></a>Nagłówki HTTP

Wymagane nagłówki żądań zostały opisane poniżej.

| Wymagany nagłówek żądania | Opis |
| --- | --- |
| Autoryzacja | Aby uwierzytelnić Azure Time Series Insights za pomocą usługi , należy podać prawidłowy token OAuth 2.0 Bearer w [nagłówku autoryzacji](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Przeczytaj przykładową wizualizację Azure Time Series Insights [SDK](https://tsiclientsample.azurewebsites.net/) klienta usługi Azure Time Series Insights, aby dowiedzieć się, jak programowo uwierzytelniać się za pomocą interfejsów API usługi Azure Time Series Insights przy użyciu zestawu SDK klienta [javaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) wraz z wykresami i grafami.

Opcjonalne nagłówki żądań zostały opisane poniżej.

| Opcjonalny nagłówek żądania | Opis |
| --- | --- |
| Typ zawartości | Jest `application/json` obsługiwana tylko. |
| x-ms-client-request-id | Identyfikator żądania klienta. Usługa rejestruje tę wartość. Umożliwia usłudze śledzenie operacji między usługami. |
| x-ms-client-session-id | Identyfikator sesji klienta. Usługa rejestruje tę wartość. Umożliwia usłudze śledzenie grupy powiązanych operacji między usługami. |
| x-ms-client-application-name | Nazwa aplikacji, która wygenerowała to żądanie. Usługa rejestruje tę wartość. |

Opcjonalne, ale zalecane nagłówki odpowiedzi zostały opisane poniżej.

| Nagłówek odpowiedzi | Opis |
| --- | --- |
| Typ zawartości | Obsługiwane `application/json` są tylko te urządzenia. |
| x-ms-request-id | Identyfikator żądania wygenerowanego przez serwer. Może służyć do kontaktowania się z firmą Microsoft w celu zbadania żądania. |
| x-ms-property-not-found-behavior | Opcjonalny nagłówek odpowiedzi interfejsu API w wersji gał. Możliwe wartości to `ThrowError` (wartość domyślna) lub `UseNull` . |

### <a name="http-parameters"></a>Parametry HTTP

> [!TIP]
> Więcej informacji na temat wymaganych i opcjonalnych informacji o zapytaniu można znaleźć w [dokumentacji referencyjnej](/rest/api/time-series-insights/)programu .

Wymagane parametry ciągu zapytania adresu URL zależą od wersji interfejsu API.

| Release | Wartości wersji interfejsu API |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Opcjonalne parametry ciągu zapytania adresu URL obejmują ustawianie limitu czasu dla czasu wykonywania żądania HTTP.

| Opcjonalny parametr zapytania | Opis | Wersja |
| --- |  --- | --- |
| `timeout=<timeout>` | Limit czasu po stronie serwera dla wykonywania żądania HTTP. Dotyczy tylko interfejsów [API Pobierz zdarzenia środowiska](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) i Pobierz [zagregowane](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) środowisko. Wartość limitu czasu powinna być na przykład w formacie czasu trwania ISO 8601 i powinna `"PT20S"` być w zakresie `1-30 s` . Wartość domyślna to `30 s`. | Gen1 |
| `storeType=<storeType>` | W środowiskach Gen2 z włączonym ciepłym magazynem zapytanie może być wykonywane na stronie `WarmStore` lub `ColdStore` . Ten parametr w zapytaniu definiuje magazyn, w którym ma zostać wykonane zapytanie. Jeśli nie zdefiniowano, zapytanie zostanie wykonane w zimnym magazynie. Aby można było odpytować ciepły magazyn, dla ustawienia **storeType** należy ustawić wartość `WarmStore` . Jeśli nie zostanie zdefiniowana, zapytanie zostanie wykonane względem zimnego magazynu. | Gen2 |

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać przykładowy kod, który wywołuje interfejs API Azure Time Series Insights Gen1, przeczytaj Query Gen1 data using C# (Wykonywanie zapytań dotyczących danych [1. generacji przy użyciu języka C#).](./time-series-insights-query-data-csharp.md)

* Aby uzyskać przykładowy kod, który wywołuje przykłady kodu interfejsu API Azure Time Series Insights Gen2, przeczytaj Query [Gen2 data using C#](./time-series-insights-update-query-data-csharp.md)(Wykonywanie zapytań o dane gen2 przy użyciu języka C#).

* Aby uzyskać informacje referencyjne dotyczące interfejsu API, zapoznaj się z [dokumentacją interfejsu API](/rest/api/time-series-insights/reference-query-apis) zapytań.
