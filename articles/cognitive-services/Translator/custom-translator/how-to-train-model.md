---
title: Uczenie modelu — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Uczenie modelu jest ważnym krokiem podczas kompilowania modelu tłumaczenia. Szkolenia odbywają się w oparciu o dokumenty wybrane dla tych szkoleń.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 5c17f842b93fb99a6a06b94f84ae26126794b776
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896430"
---
# <a name="train-a-model"></a>Szkolenie modelu

Uczenie modelu to pierwszy i najważniejszy krok tworzenia modelu tłumaczenia, w przeciwnym razie nie można skompilować modelu. Szkolenia odbywają się w oparciu o dokumenty wybrane dla szkoleń. W przypadku wybrania opcji dokumenty typu "szkolenia" należy zastanowić się nad minimalnym wymaganiem 10 000 równoległych zdań. Po wybraniu opcji dokumenty zostanie wyświetlona łączna liczba zdań szkoleniowych. Ten wymóg nie ma zastosowania, gdy wybierasz tylko dokumenty typu dokumentu słownika do uczenia modelu.

Aby nauczyć model:

1. Wybierz projekt, w którym chcesz skompilować model.

2. Na karcie dane dla projektu zostaną wyświetlone wszystkie odpowiednie dokumenty dla pary języka projektu. Ręcznie wybierz dokumenty, których chcesz użyć do uczenia modelu. Na tym ekranie można wybrać opcję uczenie, dostrajanie i testowanie dokumentów. Po prostu wybierasz zestaw szkoleniowy i masz do niego możliwość utworzenia przez Ciebie zestawu dostrajania i zestawów testów.

    - Nazwa dokumentu: nazwa dokumentu.

    - Parowanie: Jeśli ten dokument jest dokumentem równoległym lub jednojęzykowym. Dokumenty z obsługą wielu języków nie są obecnie obsługiwane w przypadku szkoleń.

    - Typ dokumentu: mogą to być szkolenia, dostrajanie, testowanie lub słownik.

    - Para językowa: pokazuje język źródłowy i docelowy dla projektu.

    - Zdania źródłowe: pokazuje liczbę zdań wyodrębnionych z pliku źródłowego.

    - Zdania docelowe: pokazuje liczbę zdań wyodrębnionych z pliku docelowego.

    ![Trenowanie modelu](media/how-to/how-to-train-model.png)

3. Kliknij przycisk "Utwórz model".

4. W oknie dialogowym Określ nazwę modelu. Domyślnie jest wybierana wartość "Uczenie natychmiastowe", aby uruchomić potok szkoleniowy po kliknięciu przycisku "Utwórz model". Możesz wybrać pozycję "Zapisz jako wersję roboczą", aby utworzyć metadane modelu i umieścić model w wersji roboczej, ale nie można uruchomić szkolenia modelu. W późniejszym czasie trzeba ręcznie wybierać modele w wersji roboczej w celu uczenia się.

5. Kliknij przycisk "Utwórz model".

    ![Okno dialogowe uczenie modelu](media/how-to/how-to-train-model-2.png)

6. Tłumaczenie niestandardowe przekaże szkolenie i wyświetli stan szkolenia na karcie modele.

    ![Strona uczenie modelu](media/how-to/how-to-train-model-3.png)

>[!Note]
>Translator niestandardowy obsługuje 10 współbieżnych szkoleń w obszarze roboczym w dowolnym momencie.

## <a name="modify-a-model-name"></a>Modyfikowanie nazwy modelu

Nazwę modelu można zmodyfikować na stronie Szczegóły modelu.

1. Na stronie projekty kliknij nazwę projektu, w którym istnieje model.
2. Kliknij kartę Model.
3. Kliknij nazwę modelu, aby wyświetlić szczegóły modelu.
4. Kliknij ikonę ołówka.

    ![Edytuj model](media/how-to/how-to-edit-model.png)

5. W oknie dialogowym Zmień nazwę modelu i nadaj modelowi zrozumiałą nazwę.

    ![Edytuj Więcej okna dialogowego](media/how-to/how-to-edit-model-dialog.png)

6. Kliknij pozycję Zapisz.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak wyświetlić szczegóły modelu](how-to-view-model-details.md).
