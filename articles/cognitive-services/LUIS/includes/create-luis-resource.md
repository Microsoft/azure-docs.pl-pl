---
title: Utwórz zasób LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2020
ms.author: aahi
ms.openlocfilehash: ee7fd384a198c5eff672b14b6cb479aac26cfe54
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972520"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>Tworzenie zasobów LUIS w Azure Portal

1. Użyj [tego linku](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) , aby rozpocząć tworzenie zasobów LUIS w Azure Portal.

1. Wprowadź wszystkie wymagane ustawienia:

    |Nazwa|Przeznaczenie|
    |--|--|
    |Subskrypcja | Subskrypcja, która będzie rozliczana za zasób.|
    |Grupa zasobów| Niestandardowa nazwa grupy zasobów, która została wybrana lub utworzona. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.|
    |Nazwa| Wybrana nazwa niestandardowa. Jest ona używana jako niestandardowa poddomena dla zapytań dotyczących tworzenia i przewidywania punktów końcowych.|
    |Lokalizacja autorstwa|Region skojarzony z Twoim modelem.|
    |Tworzenie warstwy cenowej|Określa maksymalną liczbę transakcji na sekundę i miesiąc.|
    |Lokalizacja przewidywania|Region skojarzony z opublikowanym środowiskiem uruchomieniowym przewidywanego punktu końcowego.|
    |Warstwa cenowa predykcyjna|Określa maksymalną liczbę transakcji na sekundę i miesiąc.|

    > [!div class="mx-imgBorder"]
    > [![Zrzut ekranu przedstawiający kartę podstawowe w obszarze Tworzenie.](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Wybierz pozycję **Recenzja + Utwórz** i poczekaj na utworzenie zasobu.
1. Po utworzeniu obu zasobów nadal w Azure Portal wybierz nowy zasób tworzenia. Następnie wybierz pozycję **klucze i punkt końcowy** , aby uzyskać PROGRAMISTYCZNY adres URL i **klucz** **punktu końcowego** tworzenia.

> [!TIP]
> Aby korzystać z zasobów, w portalu LUIS [Przypisz zasoby](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).
