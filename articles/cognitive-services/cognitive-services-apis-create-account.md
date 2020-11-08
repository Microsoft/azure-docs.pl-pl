---
title: Utwórz zasób Cognitive Services w Azure Portal
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z usługą Azure Cognitive Services, tworząc i subskrybując zasób w Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
keywords: usługi poznawcze, analiza poznawcze, rozwiązania poznawcze, usługi AI
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 3fd1d9e708a5f1a500440f20c2947ddfe4bc7460
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368920"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Szybki Start: Tworzenie zasobu Cognitive Services przy użyciu Azure Portal

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z usługi Azure Cognitive Services. Po utworzeniu zasobu usługi poznawczej w Azure Portal uzyskasz punkt końcowy i klucz do uwierzytelniania aplikacji.

Usługa Azure Cognitive Services to usługi oparte na chmurze, które udostępniają interfejsy API REST i zestawy SDK klienta dostępne, aby ułatwić deweloperom tworzenie analiz poznawczych w aplikacjach, bez konieczności bezpośredniego sztucznej analizy lub znajomości informacji lub wiedzy. Dzięki platformie Azure Cognitive Services deweloperzy mogą łatwo dodawać funkcje poznawcze do swoich aplikacji przy użyciu rozwiązań poznawczych, które widzą, słyszą, mówią, wiedzą, a nawet z przyczyn.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Tworzenie nowego zasobu usługi Azure Cognitive Services

1. Utworzyć zasób

    #### <a name="multi-service-resource"></a>[Zasób obejmujący wiele usług](#tab/multiservice)

    Zasób Wielousługowy ma nazwę **Cognitive Services** w portalu. [Utwórz zasób Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

    W tej chwili zasób Wielousługowy umożliwia dostęp do następujących Cognitive Services:

    - Przetwarzanie obrazów
    - Content Moderator
    - Rozpoznawanie twarzy
    - Language Understanding (LUIS)
    - Analiza tekstu
    - Translator
    - Wyszukiwanie Bing wersji 7 <br>(Sieć Web, obrazy, wiadomości, wideo, wizualizacje)
    - Wyszukiwanie niestandardowe Bing
    - Wyszukiwanie jednostek Bing
    - Automatyczne sugerowanie Bing
    - Sprawdzanie pisowni Bing

    #### <a name="single-service-resource"></a>[Zasób pojedynczego usługi](#tab/singleservice)

    Użyj poniższych linków, aby utworzyć zasób dla dostępnych Cognitive Services:

    | Wizja                      | Mowa                  | Język                          | Decyzja             | Wyszukiwanie                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Przetwarzanie obrazów](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Usługi mowy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Czytnik immersyjny](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Narzędzie do wykrywania anomalii](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Wyszukiwanie Bing API wersji 7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Usługa Custom Vision Service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Rozpoznawanie osoby mówiącej](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Wyszukiwanie niestandardowe Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Rozpoznawanie twarzy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizacja](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Wyszukiwanie jednostek Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Rozpoznawanie pisma odręcznego](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Analiza tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    | [Sprawdzanie pisowni Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Automatyczne sugerowanie Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |

    **_

3. Na stronie _ *Create* * podaj następujące informacje:

    #### <a name="multi-service-resource"></a>[Zasób obejmujący wiele usług](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług poznawczej. Na przykład *MyCognitiveServicesResource*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Lokalizacja** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność zasobu przez środowisko uruchomieniowe. |
    | **Warstwa cenowa** | Koszt konta Cognitive Services zależy od wybranych opcji i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API.
    | **Grupa zasobów** | Grupa zasobów platformy Azure, która będzie zawierać zasób Cognitive Services. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

    ![Ekran tworzenia zasobów zasobów na wiele usług](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Kliknij pozycję **Utwórz**.

    #### <a name="single-service-resource"></a>[Zasób pojedynczego usługi](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług poznawczej. Na przykład *TextAnalyticsResource*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Lokalizacja** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność zasobu przez środowisko uruchomieniowe. |
    | **Warstwa cenowa** | Koszt konta Cognitive Services zależy od wybranych opcji i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API.
    | **Grupa zasobów** | Grupa zasobów platformy Azure, która będzie zawierać zasób Cognitive Services. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

    ![Ekran tworzenia zasobów dla jednej usługi](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Kliknij pozycję **Utwórz**.

    **_

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>Pobierz klucze dla zasobu

1. Po pomyślnym wdrożeniu zasobu kliknij _ *Przejdź do zasobu* * w obszarze **następne kroki**.

    ![Wyszukaj Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. W otwartym okienku szybkiego startu możesz uzyskać dostęp do klucza i punktu końcowego.

    ![Pobierz klucz i punkt końcowy](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich innych zasobów znajdujących się w grupie.

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Grupy zasobów** , aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów zawierającą zasób do usunięcia
3. Kliknij prawym przyciskiem myszy na liście grup zasobów. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.

## <a name="see-also"></a>Zobacz też

* [Uwierzytelnianie żądań w usłudze Azure Cognitive Services](authentication.md)
* [Co to jest platforma Azure Cognitive Services?](./what-are-cognitive-services.md)
* [Tworzenie nowego zasobu przy użyciu biblioteki klienta zarządzania Azure](.\cognitive-services-apis-create-account-client-library.md)
* [Obsługa języka naturalnego](language-support.md)
* [Obsługa kontenerów platformy Docker](cognitive-services-container-support.md)