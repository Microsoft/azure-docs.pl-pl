---
title: Utwórz zasób LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80879216"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Tworzenie zasobów LUIS w Azure Portal

1. Użyj [tego linku](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) , aby rozpocząć tworzenie zasobów LUIS w Azure Portal.

1. Wprowadź wszystkie wymagane ustawienia:

    |Nazwa|Przeznaczenie|
    |--|--|
    |Nazwa subskrypcji| subskrypcja, która będzie rozliczana za zasób.|
    |Grupa zasobów| Nazwa niestandardowej grupy zasobów, która została wybrana lub utworzona. Grupy zasobów umożliwiają grupowanie zasobów platformy Azure w celu uzyskania dostępu i zarządzania.|
    |Nazwa| Wybrana niestandardowa nazwa używana jako niestandardowa poddomena dla zapytań dotyczących tworzenia i przewidywania punktów końcowych.|
    |Lokalizacja autorstwa|Region skojarzony z Twoim modelem.|
    |Tworzenie warstwy cenowej|Warstwa cenowa określa maksymalną liczbę transakcji na sekundę i miesiąc.|
    |Lokalizacja środowiska uruchomieniowego|Region skojarzony z opublikowanym środowiskiem uruchomieniowym przewidywanego punktu końcowego.|
    |Warstwa cenowa środowiska uruchomieniowego|Warstwa cenowa określa maksymalną liczbę transakcji na sekundę i miesiąc.|

    > [!div class="mx-imgBorder"]
    > [![Tworzenie zasobu interpretacji języka](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Kliknij przycisk **Przegląd + Utwórz** i poczekaj na utworzenie zasobu.
1. Po utworzeniu obu zasobów nadal w Azure Portal wybierz nowy zasób tworzenia, a następnie **Szybki Start** , aby uzyskać PROGRAMISTYCZNY adres URL i **klucz** **punktu końcowego** tworzenia.

> [!TIP]
> Aby korzystać z zasobów, w portalu LUIS [Przypisz zasoby](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).