---
title: Wyświetlanie szczegółów modelu — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Karta modele w obszarze dowolnego projektu pokazuje szczegóły każdego modelu, takie jak nazwa modelu, stan modelu, BLEU oceny, uczenie, dostrajanie, liczba zdań testowania.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 52e305f9d50ab0f383810778be681ac41be97c72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88509800"
---
# <a name="view-model-details"></a>Wyświetlanie szczegółów modelu

Karta modele w obszarze projekt zawiera wszystkie modele w tym projekcie. Na tej karcie są wyświetlane wszystkie modele przeszkolone dla tego projektu.

Dla każdego modelu w projekcie są wyświetlane te szczegóły.

1. Nazwa modelu: pokazuje nazwę modelu danego modelu.

2. Stan: pokazuje stan danego modelu. Twoje nowe szkolenie będzie miało status przesłany do momentu jego zaakceptowania. Stan zmieni się na przetwarzanie danych, podczas gdy usługa szacuje zawartość dokumentów. Po zakończeniu oceny dokumentów stan zmieni się na uruchomiony i będzie można zobaczyć liczbę zdań, które są częścią szkolenia, w tym zestawy dostrajania i testowania, które są tworzone automatycznie. Poniżej znajduje się lista stan modelu opisująca stan modeli.

    - Przesłane: określa, że zaplecze przetwarza dokumenty dla tego modelu.

    - TrainingQueued: określa, że szkolenia są umieszczane w kolejce do MT dla tego modelu.

    - Uruchomione: określa, że szkolenie działa w systemie MT dla tego modelu.

    - Sukces: określa, że szkolenie powiodło się w systemie MT i dostępny jest model. W tym stanie zostanie wyświetlony wynik BLEU dla tego modelu.

    - Wdrożono: określa, że pomyślnie szkolony model jest przesyłany do komputera MT w celu wdrożenia.

    - Trwa rozmieszczenie: określa, że wdrożony model jest wdrażany.

    - Niewdrożone: określa, że proces wdrażania w modelu został ukończony pomyślnie.

    - Szkolenie nie powiodło się: określa, że szkolenie nie powiodło się. Jeśli wystąpi awaria szkoleniowa, spróbuj ponownie wykonać zadanie szkoleniowe. Jeśli błąd będzie się powtarzać, skontaktuj się z nami. Nie usuwaj modelu, który uległ awarii.

    - DataProcessingFailed: określa, że przetwarzanie danych nie powiodło się dla co najmniej jednego dokumentu należącego do modelu.

    - DeploymentFailed: określa, że wdrożenie modelu nie powiodło się.

    - MigratedDraft: określa, że model jest w stanie wersji roboczej po migracji z centrum do translatora niestandardowego.

3. BLEU Score: pokazuje wynik BLEU (analiza dwujęzyczna) dla modelu, wskazujący jakość systemu tłumaczenia. Ten wynik zawiera informacje o tym, jak ściśle tłumaczenia wykonywane przez system tłumaczenia wynikający z tego szkolenia pasują do zdań odniesienia w zestawie danych testowych. Wynik BLEU pojawia się, jeśli szkolenie zostało pomyślnie zakończone. Jeśli szkolenie nie zostało ukończone/nie zakończyło się niepowodzeniem, nie zobaczysz żadnego wyniku BLEU.

4. Liczba zdań szkoleniowych: pokazuje łączną liczbę zdań użytych jako zestaw szkoleniowy.

5. Liczba zdań strojenia: pokazuje łączną liczbę zdań używanych jako zestaw dostrajania.

6.  Liczba zdań szkoleniowych: pokazuje łączną liczbę zdań użytych jako zestaw testowy.

7.  Liczba zdań mono: pokazuje łączną liczbę zdań użytych jako zestaw mono.

8.  Przycisk wdrożenia akcji: dla modelu, który został pomyślnie przeszkolony, wyświetla przycisk "wdróż", jeśli nie został wdrożony. Jeśli model został wdrożony, wyświetlany jest przycisk "Rozmieść".

9. Usuń: można użyć tego przycisku, jeśli chcesz usunąć model. Usunięcie modelu nie spowoduje usunięcia żadnego z dokumentów użytych do utworzenia tego modelu.

    ![Wyświetlanie szczegółów modelu](media/how-to/how-to-view-model-details.png)

>[!Note]
>Aby porównać kolejne szkolenia dla tych samych systemów, ważne jest, aby zachować zestaw strojenia i stały zestaw testów.

## <a name="view-model-training-details"></a>Wyświetl szczegóły szkolenia modelu

Po zakończeniu szkolenia możesz zapoznać się ze szczegółowymi informacjami na temat szkolenia ze strony szczegółów. Wybierz projekt, Znajdź i wybierz kartę modele i wybierz model.

Strona model ma dwie karty: szczegóły i test szkolenia.

1.  **Szczegóły szkolenia:** Ta karta zawiera listę dokumentów użytych w szkoleniu:

    -  Nazwa dokumentów: to pole zawiera nazwę dokumentu

    -  Typ dokumentu: to pole pokazuje, czy ten dokument jest równoległy/mono.

    -  Liczba zdań w języku źródłowym: to pole pokazuje liczbę zdań w postaci części języka źródłowego.

    -  Liczba zdań w języku docelowym: to pole pokazuje liczbę zdań w postaci części języka docelowego.

    -  Wyrównane zdania: to pole pokazuje liczbę zdań wyrównanych przez translatora niestandardowego podczas procesu wyrównania.

    -  Używane zdania: to pole pokazuje liczbę zdań używanych przez translatora niestandardowego podczas tego szkolenia.

    ![Szczegóły szkolenia modelu](media/how-to/how-to-model-training-details.png)

2.  **Test:** Na tej karcie są wyświetlane szczegóły testu dotyczące pomyślnego szkolenia.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wyniki testów](how-to-view-system-test-results.md) i Przeanalizuj wyniki szkolenia.
