---
title: Jak zaktualizować projekt do interfejsu API 3,0
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak aktualizować Custom Vision projekty z poprzedniej wersji interfejsu API do interfejsu API 3,0.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "73647501"
---
# <a name="update-to-the-30-api"></a>Aktualizacja interfejsu API 3,0

Custom Vision ma teraz ogólnie dostępna dostępność i została poddana aktualizacji interfejsu API.
Ta aktualizacja obejmuje kilka nowych funkcji i, co ważne, pewne zmiany:

* Interfejs API przewidywania jest teraz podzielony na dwa w oparciu o typ projektu.
* Opcja eksportu programu Vision AI Developer Kit (VAIDK) wymaga utworzenia projektu w określony sposób.
* Domyślne iteracje zostały usunięte na korzyść publikacji/anulowania publikacji nazwanej iteracji.

W tym przewodniku pokazano, jak zaktualizować projekty do pracy z nową wersją interfejsu API. Pełną listę zmian można znaleźć w [informacjach o wersji](release-notes.md) .

## <a name="use-the-updated-prediction-api"></a>Korzystanie z zaktualizowanego interfejsu API przewidywania

Interfejsy API 2. x używały tego samego wywołania prognozowania dla obu klasyfikatorów obrazów i projektów czujnika obiektów. Oba typy projektów zostały akceptowalne dla wywołań **PredictImage** i **PredictImageUrl** . Począwszy od 3,0, został podzielony ten interfejs API, dzięki czemu należy dopasować typ projektu do wywołania:

* Użyj **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** i **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** , aby uzyskać prognozy dla projektów klasyfikacji obrazów.
* Użyj **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** i **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** , aby uzyskać prognozy dla projektów wykrywania obiektów.

## <a name="use-the-new-iteration-publishing-workflow"></a>Użyj nowego przepływu pracy publikowania iteracji

Interfejsy API 2. x używały domyślnej iteracji lub określonego identyfikatora iteracji, aby wybrać iterację do użycia na potrzeby przewidywania. Począwszy od 3,0, przyjęto przepływ publikowania, który najpierw publikuje iterację pod określoną nazwą z poziomu interfejsu API szkolenia. Następnie przekaż nazwę do metod przewidywania, aby określić, która iteracja ma być używana.

> [!IMPORTANT]
> Interfejsy API 3,0 nie używają domyślnej funkcji iteracji. Dopóki nie zostaną zaniechane starsze interfejsy API, można nadal używać interfejsów API 2. x, aby przełączać iterację jako domyślną. Te interfejsy API będą przechowywane przez pewien czas i można wywołać metodę **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** , aby oznaczyć iterację jako domyślną.

### <a name="publish-an-iteration"></a>Publikowanie iteracji

Po przeszkoleniu iteracji można udostępnić ją do przewidywania przy użyciu metody **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** . Aby opublikować iterację, będzie potrzebny identyfikator zasobu przewidywania, który jest dostępny na stronie ustawień witryny sieci Web CustomVision.

![Strona Ustawienia witryny sieci Web Custom Vision z zakreślonym IDENTYFIKATORem zasobu predykcyjnego.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Te informacje można również uzyskać w [witrynie Azure Portal](https://portal.azure.com) , przechodząc do zasobu przewidywania Custom Vision i wybierając pozycję **Właściwości**.

Po opublikowaniu iteracji aplikacje mogą używać jej do przewidywania przez określenie nazwy w wywołaniu interfejsu API prognozowania. Aby iteracja była niedostępna dla wywołań prognozowania, użyj interfejsu API **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** .

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja referencyjna interfejsu API szkoleń (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Dokumentacja referencyjna interfejsu API przewidywania (REST)](https://go.microsoft.com/fwlink/?linkid=865445)