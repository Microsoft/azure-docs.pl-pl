---
title: Utwórz zasób personalizacji
description: Konfiguracja usługi zawiera informacje o tym, jak usługa traktuje korzyści, jak często bada usługa, jak często jest on przełączany i jak dużo danych jest przechowywanych.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80336062"
---
# <a name="create-a-personalizer-resource"></a>Utwórz zasób personalizacji

Zasób personalizacji jest tym samym elementem co pętla szkoleniowa programu Personalizacja. Pojedynczy zasób lub pętla szkoleniowa jest tworzona dla każdej domeny podmiotu lub obszaru zawartości. Nie używaj wielu obszarów zawartości w tej samej pętli, ponieważ spowoduje to pomylić pętli uczenia i dostarczenie słabych prognoz.

Jeśli chcesz, aby Personalizował wybór najlepszej zawartości dla więcej niż jednego obszaru zawartości strony sieci Web, użyj innej pętli szkoleniowej dla każdej z nich.


## <a name="create-a-resource-in-the-azure-portal"></a>Utwórz zasób w Azure Portal

Utwórz zasób personalizacji dla każdej pętli opinii.

1. Zaloguj się do [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Poprzedni link prowadzi do strony **Tworzenie** dla usługi personalizacji.
1. Wprowadź nazwę usługi, wybierz subskrypcję, lokalizację, warstwę cenową i grupę zasobów.

    > [!div class="mx-imgBorder"]
    > ![Użyj Azure Portal, aby utworzyć zasób personalizacji, nazywany również pętlą uczenia.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Wybierz pozycję **Utwórz** , aby utworzyć zasób.

1. Po wdrożeniu zasobu wybierz przycisk **Przejdź do zasobu** , aby przejść do zasobu personalizowania.

1. Wybierz stronę **Szybki Start** dla zasobu, a następnie skopiuj wartości dla swojego punktu końcowego i klucza. Do korzystania z interfejsów API rangi i nagrody potrzebny jest zarówno punkt końcowy zasobu, jak i klucz.

1. Wybierz stronę **konfiguracji** nowego zasobu, aby [skonfigurować pętlę uczenia](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Tworzenie zasobu za pomocą interfejsu wiersza polecenia platformy Azure

1. Zaloguj się w interfejsie wiersza polecenia platformy Azure przy użyciu następujące polecenie:

    ```azurecli-interactive
    az login
    ```

1. Utwórz grupę zasobów, czyli grupowanie logiczne służące do zarządzania wszystkimi zasobami platformy Azure, które mają być używane z zasobem narzędzia Personalizacja.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Utwórz nowy zasób personalizacji, _pętlę uczenia_przy użyciu następującego polecenia dla istniejącej grupy zasobów.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Zwraca obiekt JSON, który zawiera **punkt końcowy zasobu**.

1. Aby uzyskać **klucz zasobu**, użyj następującego polecenia platformy Azure.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Do korzystania z interfejsów API rangi i nagrody potrzebny jest zarówno punkt końcowy zasobu, jak i klucz.

## <a name="next-steps"></a>Następne kroki

* [Konfiguruj](how-to-settings.md) Pętla szkoleniowa personalizacji