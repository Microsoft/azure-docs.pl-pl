---
title: Testowanie i ponowne uczenie modelu — Custom Vision Service
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak przetestować obraz, a następnie użyć go do ponownego nauczenia modelu w usłudze Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 5a3aacd1d07ff068fe50312b2c1d47ac080e5c2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85391727"
---
# <a name="test-and-retrain-a-model-with-custom-vision-service"></a>Testowanie i ponowne uczenie modelu z Custom Vision Service

Po przeprowadzeniu szkolenia modelu można szybko przetestować go przy użyciu lokalnie przechowywanego obrazu lub obrazu w trybie online. Test korzysta z ostatnio przeszkolonej iteracji.

## <a name="test-your-model"></a>Testowanie modelu

1. Na [stronie sieci web Custom Vision](https://customvision.ai)wybierz projekt. Wybierz pozycję **szybka próba** po prawej stronie górnego paska menu. Ta akcja powoduje otwarcie okna z etykietą **szybki test**.

    ![Przycisk Szybki test jest wyświetlany w prawym górnym rogu okna.](./media/test-your-model/quick-test-button.png)

2. W oknie **szybkie testy** kliknij pole **Prześlij obraz** , a następnie wprowadź adres URL obrazu, który ma być używany dla testu. Jeśli zamiast tego chcesz użyć lokalnie przechowywanego obrazu, kliknij przycisk **Przeglądaj pliki lokalne** i wybierz plik obrazu lokalnego.

    ![Obraz strony przesyłania obrazu](./media/test-your-model/submit-image.png)

Wybrany obraz pojawia się w środku strony. Następnie wyniki są wyświetlane poniżej obrazu w postaci tabeli zawierającej dwie kolumny z etykietami i **pewnością**. **Tags** Po wyświetleniu wyników możesz zamknąć okno **szybkie testy** .

Teraz możesz dodać ten obraz testowy do modelu, a następnie ponownie przeprowadzić uczenie modelu.

## <a name="use-the-predicted-image-for-training"></a>Używanie przewidywanego obrazu do szkolenia

Aby użyć obrazu przesłanego wcześniej do szkolenia, wykonaj następujące czynności:

1. Aby wyświetlić obrazy przesłane do klasyfikatora, Otwórz [stronę sieci web Custom Vision](https://customvision.ai) i wybierz kartę __przewidywania__ .

    ![Obraz karty przewidywania](./media/test-your-model/predictions-tab.png)

    > [!TIP]
    > Widok domyślny pokazuje obrazy z bieżącej iteracji. Pole listy rozwijanej __iteracji__ służy do wyświetlania obrazów przesłanych podczas poprzednich iteracji.

2. Umieść kursor na obrazie, aby zobaczyć znaczniki, które zostały przewidziane przez klasyfikator.

    > [!TIP]
    > Obrazy są klasyfikowane, dzięki czemu obrazy, które mogą przynieść największe zyski do klasyfikatora, znajdują się u góry. Aby wybrać inne sortowanie, użyj sekcji __Sortuj__ .

    Aby dodać obraz do danych szkoleniowych, wybierz obraz, wybierz tag, a następnie wybierz pozycję __Zapisz i Zamknij__. Obraz jest usuwany z __prognoz__ i dodawany do obrazów szkoleniowych. Możesz ją wyświetlić, wybierając kartę __obrazy szkoleniowe__ .

    ![Obraz strony tagowania](./media/test-your-model/tag-image.png)

3. Użyj przycisku __uczenie__ , aby ponownie przeprowadzić szkolenie klasyfikatora.

## <a name="next-steps"></a>Następne kroki

[Poprawianie klasyfikatora](getting-started-improving-your-classifier.md)
