---
title: Authentication
titleSuffix: Azure Cognitive Services
description: 'Istnieją trzy sposoby uwierzytelniania żądania do zasobu usługi Azure Cognitive Services: klucza subskrypcji, tokenu okaziciela lub subskrypcji wielousługowej. Ten artykuł zawiera informacje o każdej z tych metod oraz o sposobie wykonywania żądania.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: c7aeb9e9f4de7b4de62f9b5a8da6d997e32a2399
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94363327"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Uwierzytelnianie żądań w usłudze Azure Cognitive Services

Każde żądanie do usługi poznawczej platformy Azure musi zawierać nagłówek uwierzytelniania. Ten nagłówek przekazuje swój klucz subskrypcji lub token dostępu, który służy do weryfikowania subskrypcji usługi lub grupy usług. W tym artykule omówiono trzy sposoby uwierzytelniania żądań i wymagania dotyczące każdego z nich.

* Uwierzytelnianie za pomocą [pojedynczego](#authenticate-with-a-single-service-subscription-key) lub [wielousługowego](#authenticate-with-a-multi-service-subscription-key) klucza subskrypcji
* Uwierzytelnianie za pomocą [tokenu](#authenticate-with-an-authentication-token)
* Uwierzytelnianie za pomocą [Azure Active Directory (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Wymagania wstępne

Przed wysłaniem żądania musisz mieć konto platformy Azure i subskrypcję usługi Azure Cognitive Services. Jeśli masz już konto, przejdź do następnej sekcji. Jeśli nie masz konta, zawieramy Przewodnik pozwalający skonfigurować w ciągu kilku minut: [Utwórz konto Cognitive Services dla platformy Azure](cognitive-services-apis-create-account.md).

Możesz uzyskać klucz subskrypcji z [Azure Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) po [utworzeniu konta](https://azure.microsoft.com/free/cognitive-services/).

## <a name="authentication-headers"></a>Nagłówki uwierzytelniania

Szybko Przejrzyj nagłówki uwierzytelniania dostępne do użycia z usługą Azure Cognitive Services.

| Nagłówek | Opis |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Ten nagłówek służy do uwierzytelniania za pomocą klucza subskrypcji dla określonej usługi lub klucza subskrypcji wielousługowej. |
| OCP-APIM-Subscription-region | Ten nagłówek jest wymagany tylko w przypadku korzystania z klucza subskrypcji wieloserviceowej z [usługą translator](./Translator/reference/v3-0-reference.md). Użyj tego nagłówka, aby określić region subskrypcji. |
| Autoryzacja | Użyj tego nagłówka, jeśli używasz tokenu uwierzytelniania. Kroki umożliwiające przeprowadzenie wymiany tokenów opisano szczegółowo w poniższych sekcjach. Podana wartość jest zgodna z następującym formatem: `Bearer <TOKEN>` . |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Uwierzytelnianie za pomocą klucza subskrypcji pojedynczego usługi

Pierwsza opcja polega na uwierzytelnianiu żądania przy użyciu klucza subskrypcji dla określonej usługi, takiej jak translator. Klucze są dostępne w Azure Portal dla każdego utworzonego zasobu. Aby można było uwierzytelnić żądanie przy użyciu klucza subskrypcji, musi on być przesłany razem jako `Ocp-Apim-Subscription-Key` nagłówek.

Te przykładowe żądania pokazują, jak używać `Ocp-Apim-Subscription-Key` nagłówka. Należy pamiętać, że w przypadku korzystania z tego przykładu należy uwzględnić prawidłowy klucz subskrypcji.

Jest to przykładowe wywołanie do interfejs API wyszukiwania w sieci Web Bing:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

To jest przykładowe wywołanie do usługi translatora:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Poniższy film wideo demonstruje użycie klucza Cognitive Services.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Uwierzytelnianie za pomocą klucza subskrypcji wielousługowej

>[!WARNING]
> W tej chwili te usługi **nie** obsługują kluczy wielousługowych: QNA Maker, usługi mowy, Custom Vision i wykrywania anomalii.

Ta opcja używa także klucza subskrypcji do uwierzytelniania żądań. Główną różnicą jest to, że klucz subskrypcji nie jest powiązany z określoną usługą, a zamiast tego można użyć jednego klucza do uwierzytelniania żądań dla wielu Cognitive Services. Zobacz [cennik Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) , aby uzyskać informacje o dostępności regionalnej, obsługiwanych funkcjach i cenach.

Klucz subskrypcji jest udostępniany w każdym żądaniu jako `Ocp-Apim-Subscription-Key` nagłówek.

[![Demonstracja wielousługowego klucza subskrypcji dla Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Obsługiwane regiony

W przypadku korzystania z klucza subskrypcji wieloserviceowej w celu zgłoszenia żądania do programu `api.cognitive.microsoft.com` należy uwzględnić region w adresie URL. Na przykład: `westus.api.cognitive.microsoft.com`.

W przypadku korzystania z wielousługowego klucza subskrypcji z usługą translatora należy określić region subskrypcji z `Ocp-Apim-Subscription-Region` nagłówkiem.

Uwierzytelnianie wieloskładnikowe jest obsługiwane w następujących regionach:

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

### <a name="sample-requests"></a>Przykładowe żądania

Jest to przykładowe wywołanie do interfejs API wyszukiwania w sieci Web Bing:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

To jest przykładowe wywołanie do usługi translatora:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Uwierzytelnianie przy użyciu tokenu uwierzytelniania

Niektóre Cognitive Services platformy Azure akceptują, a w niektórych przypadkach wymagają tokenu uwierzytelniania. Obecnie te usługi obsługują tokeny uwierzytelniania:

* Interfejs API tłumaczenia tekstu
* Usługi mowy: interfejs API REST zamiany mowy na tekst
* Usługi mowy: interfejs API REST zamiany tekstu na mowę

>[!NOTE]
> QnA Maker używa również nagłówka autoryzacji, ale wymaga klucza punktu końcowego. Aby uzyskać więcej informacji, zobacz [QNA Maker: uzyskiwanie odpowiedzi z bazy wiedzy](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

>[!WARNING]
> Usługi obsługujące tokeny uwierzytelniania mogą ulec zmianie z upływem czasu, przed użyciem tej metody uwierzytelniania należy sprawdzić odwołanie do interfejsu API dla usługi.

Do tokenów uwierzytelniania można wymieniać zarówno klucze subskrypcji pojedynczej usługi, jak i wiele usług. Tokeny uwierzytelniania są prawidłowe przez 10 minut.

Tokeny uwierzytelniania są uwzględniane w żądaniu jako `Authorization` nagłówek. Podana wartość tokenu musi być poprzedzona przez `Bearer` , na przykład: `Bearer YOUR_AUTH_TOKEN` .

### <a name="sample-requests"></a>Przykładowe żądania

Użyj tego adresu URL, aby wymienić klucz subskrypcji dla tokenu uwierzytelniania: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken` .

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Te wielousługowe regiony obsługują wymianę tokenów:

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

Po otrzymaniu tokenu uwierzytelniania należy przekazać go w każdym żądaniu jako `Authorization` nagłówek. To jest przykładowe wywołanie do usługi translatora:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Zobacz też

* [Co to są usługi poznawcze Cognitive Services?](./what-are-cognitive-services.md)
* [Cennik usług Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Niestandardowe poddomeny](cognitive-services-custom-subdomains.md)