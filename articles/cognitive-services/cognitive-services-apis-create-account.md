---
title: Utwórz zasób Cognitive Services w Azure Portal
titleSuffix: Azure Cognitive Services
description: Rozpocznij pracę z usługą Azure Cognitive Services, tworząc i subskrybując zasób w Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219479"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Tworzenie zasobu Cognitive Services przy użyciu Azure Portal

Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z usługi Azure Cognitive Services. Po utworzeniu zasobu usługi poznawczej w Azure Portal uzyskasz punkt końcowy i klucz do uwierzytelniania aplikacji.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Prawidłowa subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Tworzenie nowego zasobu usługi Azure Cognitive Services

1. Utworzyć zasób

    #### <a name="multi-service-resource"></a>[Zasób obejmujący wiele usług](#tab/multiservice)
    
    Zasób Wielousługowy ma nazwę **Cognitive Services** w portalu. [Utwórz zasób Cognitive Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    W tej chwili zasób Wielousługowy umożliwia dostęp do następujących Cognitive Services:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Przetwarzanie obrazów  | Content Moderator                                    | Rozpoznawanie twarzy               | Language Understanding (LUIS) | Analiza tekstu   |
    | Tekst usługi Translator  | Wyszukiwanie Bing wersji 7 <br>(Sieć Web, obrazy, wiadomości, wideo, wizualizacje) | Wyszukiwanie niestandardowe Bing | Wyszukiwanie jednostek Bing            | Automatyczne sugerowanie Bing |
    | Sprawdzanie pisowni Bing |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[Zasób pojedynczego usługi](#tab/singleservice)

    Użyj poniższych linków, aby utworzyć zasób dla dostępnych Cognitive Services:

    | Obraz                      | Mowa                  | Język                          | Decyzja             | Wyszukiwanie                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Przetwarzanie obrazów](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Usługi mowy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Czytnik immersyjny](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Narzędzie do wykrywania anomalii](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Wyszukiwanie Bing API wersji 7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Usługa Custom Vision Service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Rozpoznawanie osoby mówiącej](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Wyszukiwanie niestandardowe Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Rozpoznawanie twarzy](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizacja](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Wyszukiwanie jednostek Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Rozpoznawanie pisma odręcznego](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Analiza tekstu](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Sprawdzanie pisowni Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Tekst usługi Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Automatyczne sugerowanie Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Na stronie **Tworzenie** podaj następujące informacje:

    #### <a name="multi-service-resource"></a>[Zasób obejmujący wiele usług](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług poznawczej. Na przykład *MyCognitiveServicesResource*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Lokalizacja** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność zasobu przez środowisko uruchomieniowe. |
    | **Warstwa cenowa** | Koszt konta Cognitive Services zależy od wybranych opcji i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API.
    | **Grupa zasobów** | Grupa zasobów platformy Azure, która będzie zawierać zasób Cognitive Services. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Kliknij przycisk **Utwórz**.

    #### <a name="single-service-resource"></a>[Zasób pojedynczego usługi](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nazwa** | Opisowa nazwa zasobu usług poznawczej. Na przykład *TextAnalyticsResource*. |
    | **Subskrypcja** | Wybierz jedną z dostępnych subskrypcji platformy Azure. |
    | **Lokalizacja** | Lokalizacja wystąpienia usługi poznawczej. Różne lokalizacje mogą wprowadzać opóźnienia, ale nie mają wpływu na dostępność zasobu przez środowisko uruchomieniowe. |
    | **Warstwa cenowa** | Koszt konta Cognitive Services zależy od wybranych opcji i użycia. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/cognitive-services/)interfejsu API.
    | **Grupa zasobów** | Grupa zasobów platformy Azure, która będzie zawierać zasób Cognitive Services. Można utworzyć nową grupę lub dodać ją do istniejącej grupy. |

    ![Ekran tworzenia zasobów](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Kliknij przycisk **Utwórz**.

    ***


## <a name="get-the-keys-for-your-resource"></a>Pobierz klucze dla zasobu

1. Po pomyślnym wdrożeniu zasobu kliknij pozycję **Przejdź do zasobu** w obszarze **następne kroki**.

    ![Wyszukaj Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. W otwartym okienku szybkiego startu możesz uzyskać dostęp do klucza i punktu końcowego.

    ![Pobierz klucz i punkt końcowy](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich innych zasobów znajdujących się w grupie.

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Grupy zasobów**, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów zawierającą zasób do usunięcia
3. Kliknij prawym przyciskiem myszy na liście grup zasobów. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.

## <a name="see-also"></a>Zobacz także

* [Uwierzytelnianie żądań w usłudze Azure Cognitive Services](authentication.md)
* [Co to jest platforma Azure Cognitive Services?](Welcome.md)
* [Obsługa języka naturalnego](language-support.md)
* [Obsługa kontenerów platformy Docker](cognitive-services-container-support.md)
