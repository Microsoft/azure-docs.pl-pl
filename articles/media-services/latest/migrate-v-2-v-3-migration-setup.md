---
title: Konfiguracja migracji Media Services V2 do wersji v3
description: Ten artykuł pomoże Ci skonfigurować środowisko do migracji z Azure Media Services V2 do wersji v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: usługa Azure Media Services, migracja, przesyłanie strumieniowe, transmisja, na żywo, zestaw SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 1f9e0816a3533008a01f26ca7c0e712abfa23e8e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946237"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>Krok 3 — Konfigurowanie do migracji do interfejsu API REST v3 lub zestawu SDK klienta

![logo przewodnika migracji](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroki migracji 2](./media/migration-guide/steps-3.svg)

Poniżej opisano kroki, które należy wykonać w celu skonfigurowania środowiska do korzystania z interfejsu API Media Services v3.

## <a name="sdk-model"></a>Model zestawu SDK

W interfejsie API v2 istniały dwa różne zestawy SDK klienta — jeden dla interfejsu API zarządzania, który umożliwia programowe tworzenie kont i jeden dla zarządzania zasobami.

Wcześniej deweloperzy współpracują z IDENTYFIKATORem klienta nazwy głównej usługi platformy Azure i kluczem tajnym klienta oraz z określonym punktem końcowym interfejsu API REST w wersji 2 dla konta usługi AMS.

Interfejs API v3 to oparta na usłudze Azure Resource Management (ARM). W celu nawiązania połączenia z interfejsem API używa identyfikatorów i kluczy głównych usługi Azure Active Directory (Azure AD). Deweloperzy będą musieli utworzyć jednostki usługi lub tożsamości zarządzane, aby połączyć się z interfejsem API. W interfejsie API v3 interfejs API używa standardowych punktów końcowych usługi ARM, a następnie stosuje model podobny i spójny do wszystkich innych usług platformy Azure.

Klienci, którzy wcześniej korzystali z wersji 2015-10-01 interfejsu API zarządzania ARM do zarządzania kontami, powinni używać wersji 2020-05-01 interfejsu API zarządzania ARM obsługiwanego dla dostępu do interfejsu API v3.

## <a name="create-a-new-media-services-account-for-testing"></a>Utwórz nowe konto usługi Media Services na potrzeby testowania

Wykonaj kroki przewodnika Szybki Start dotyczące [konfigurowania środowiska](how-to-set-azure-subscription.md?tabs=portal) przy użyciu Azure Portal. Wybierz pozycję dostęp do interfejsu API i uwierzytelnianie główne usługi, aby wygenerować nowy identyfikator i wpisy tajne aplikacji usługi Azure AD do użycia z tym kontem testowym.

[Utwórz konto usługi Media Services](create-account-howto.md?tabs=portal).
[Uzyskaj poświadczenia umożliwiające dostęp do interfejsu API Media Services](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Pobierz wybrany zestaw SDK klienta i Skonfiguruj swoje środowisko

- Zestawy SDK dostępne dla [platform .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet&preserve-view=true), .NET Core, [Node.js](https://docs.microsoft.com/javascript/api/overview/azure/mediaservices/management?view=azure-node-latest&preserve-view=true), [Python](https://docs.microsoft.com/python/api/overview/azure/mediaservices/management?view=azure-python&preserve-view=true), [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices/management?view=azure-java-stable&preserve-view=true), [go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media)i [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md).
- [Interfejs wiersza polecenia](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest&preserve-view=true)   platformy Azure Integracja z obsługą obsługi skryptów prostych.

> [!NOTE]
> Zestaw SDK języka PHP dla społeczności nie jest już dostępny dla Azure Media Services w wersji 3. Jeśli używasz języka PHP w wersji 2, należy przeprowadzić migrację do interfejsu API REST bezpośrednio w kodzie.

## <a name="open-api-specifications"></a>Specyfikacje Open API

- V3 jest oparta na ujednoliconej powierzchni interfejsu API, która udostępnia funkcje zarządzania i działania oparte na Azure Resource Manager. Przy użyciu szablonów Azure Resource Manager można tworzyć i wdrażać transformacje, punkty końcowe przesyłania strumieniowego, zdarzenia na żywo i wiele innych.

- W dokumencie [Specyfikacja openapi](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (dawniej Swagger) objaśniono schemat wszystkich składników usługi.

- Wszystkie zestawy SDK klienta są wyprowadzane i generowane na podstawie specyfikacji Open API Specification opublikowanej w serwisie GitHub. W chwili opublikowania tego artykułu Najnowsza Specyfikacja otwartych interfejsów API jest ogólnie obsługiwana w serwisie GitHub. Wersja 2020-05-01 jest [najnowszą stabilną](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01)wersją.

## <a name="rest"></a>[REST](#tab/rest)

Użyj programu [Poster](https://docs.microsoft.com/azure/media-services/latest/media-rest-apis-with-postman) dla wywołań interfejsu API REST usługi Media Services v3.
Przeczytaj [strony dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/media/).

Należy użyć ciągu wersji 2020-05-01 w kolekcji Poster.

## <a name="net"></a>[.NET](#tab/net)

Zapoznaj się z artykułem [łączenie się z interfejsem API Media Services v3 przy użyciu platformy .NET](configure-connect-dotnet-howto.md) w celu skonfigurowania środowiska.

Jeśli po prostu chcesz zainstalować najnowszy zestaw SDK przy użyciu pakietu Packagemanager, użyj następującego polecenia:

```Install-Package Microsoft.Azure.Management.Media```

Lub aby zainstalować najnowszy zestaw SDK przy użyciu interfejsu wiersza polecenia platformy .NET, użyj następującego polecenia:

```dotnet add package Microsoft.Azure.Management.Media```

Ponadto pełne przykłady platformy .NET są dostępne na [platformie Azure — przykłady/Media-Services-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) dla różnych scenariuszy. Projekty w tym repozytorium pokazują, jak zaimplementować różne scenariusze Azure Media Services przy użyciu wersji v3.

### <a name="get-started-adjusting-your-code"></a>Wprowadzenie do dostosowywania kodu

Przeszukaj bazę kodu w poszukiwaniu wystąpień `CloudMediaContext` użycia, aby rozpocząć proces uaktualniania do interfejsu API v3.

Poniższy kod przedstawia, w jaki sposób dostęp do interfejsu API v2 został poprzednio uzyskany przy użyciu zestawu .NET SDK w wersji 2. Deweloperzy zaczynają tworzenie `CloudMediaContext` i tworzenie wystąpienia z `AzureAdTokenCredentials` obiektem.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Zapoznaj się z artykułem [łączenie się z interfejsem API programu Media Services v3 przy użyciu języka Java](configure-connect-java-howto.md) w celu skonfigurowania środowiska.

## <a name="python"></a>[Python](#tab/python)

Zapoznaj się z artykułem [łączenie z Azure Media Services v3 API-Python](configure-connect-python-howto.md) w celu skonfigurowania środowiska.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

Zapoznaj się z artykułem [łączenie się z interfejsem API Azure Media Services v3 Node.js](configure-connect-nodejs-howto.md) w celu skonfigurowania środowiska.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Pobierz zestaw [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK w witrynie GitHub.

## <a name="go"></a>[Przejdź](#tab/go)

Pobierz zestaw SDK języka [go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) .

---

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]