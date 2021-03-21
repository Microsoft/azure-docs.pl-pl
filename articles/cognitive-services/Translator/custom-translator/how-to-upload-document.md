---
title: Jak przekazać dokument — translator niestandardowy
titleSuffix: Azure Cognitive Services
description: Funkcja przekazywania dokumentów przekazuje równoległe dokumenty (dwa dokumenty, w których jeden jest źródłem, a drugi to tłumaczenie) do usługi.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 83cce31d3eee9b5cca7b54e996e41a270fec0030
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896398"
---
# <a name="upload-a-document"></a>Przekazywanie dokumentu

W usłudze [translator niestandardowy](https://portal.customtranslator.azure.ai)można przekazać równoległe dokumenty umożliwiające uczenie modeli tłumaczenia. [Dokumenty równoległe](what-are-parallel-documents.md) to pary dokumentów, w których jeden jest tłumaczy innych. Jeden dokument w parze zawiera zdania w języku źródłowym, a drugi dokumentu zawiera te zdania tłumaczone na język docelowy.

Przed przekazaniem dokumentów zapoznaj się ze [wskazówkami dotyczącymi formatów dokumentów i konwencji nazewnictwa](document-formats-naming-convention.md) , aby upewnić się, że format pliku jest obsługiwany przez translatora niestandardowego.

## <a name="how-to-upload-document"></a>Jak przekazać dokument?

W portalu usługi [tłumaczenia niestandardowego](https://portal.customtranslator.azure.ai) kliknij kartę "dokumenty", aby przejść do strony dokumenty.

![Link przekazywania dokumentów](media/how-to/how-to-upload-1.png)


1.  Kliknij przycisk Przekaż pliki na stronie dokumenty.

    ![Strona przekazywania dokumentu](media/how-to/how-to-upload-2.png)

2.  W oknie dialogowym wprowadź następujące informacje:

    a.  Typ dokumentu:

    -  Szkolenie: te dokumenty będą używane na potrzeby zestawu szkoleniowego.
    -  Dostrajanie: te dokumenty będą używane na potrzeby zestawu strojenia.
    -  Testowanie: te dokumenty będą używane do zestawu testów.
    -  Słownik fraz: te dokumenty będą używane dla słownika fraz.
    -  Słownik zdań: te dokumenty będą używane dla słownika zdania

    b.  Para językowa

    c.  Zastąp dokument, jeśli istnieje: zaznacz to pole wyboru, jeśli chcesz zastąpić wszystkie istniejące dokumenty o tej samej nazwie.

    d.  Wypełnij odpowiednie sekcje dla danych równoległych lub danych kombi.

    -  Dane równoległe:
        -  Plik źródłowy: wybierz z komputera lokalnego plik języka źródłowego.
        -  Plik docelowy: Wybierz docelowy plik języka z komputera lokalnego.
        -  Nazwa dokumentu: jest używana tylko w przypadku przekazywania plików równoległych.

    - Dane kombi:
        -  Plik kombi: Wybierz plik kombi z komputera lokalnego. Plik kombi zawiera oba zdania języka źródłowego i docelowego. [Konwencja nazewnictwa](document-formats-naming-convention.md) jest ważna dla plików kombi.

    e.  Kliknij pozycję Przekaż.

    ![Okno dialogowe przekazywania dokumentu](media/how-to/how-to-upload-dialog.png)

3.  W tym momencie przetwarzamy dokumenty i próbujemy wyodrębnić zdania. Możesz kliknąć pozycję "Wyświetl postęp przekazywania", aby sprawdzić stan dokumentów w miarę ich przetwarzania.

    ![Okno dialogowe przekazywania dokumentu](media/how-to/how-to-upload-processing-dialog.png)

4.  Na tej stronie zostanie wyświetlony stan oraz wszystkie błędy poszczególnych plików w ramach przekazywania. W dowolnym momencie możesz wyświetlić poprzedni stan przekazywania, klikając kartę "Historia przekazywania".

    ![Okno dialogowe przekazywania historii dokumentu](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Wyświetl historię przekazywania

Na stronie Historia przekazywania można wyświetlić historię wszystkich szczegółów przekazywania dokumentów, takich jak typ dokumentu, para językowa, stan przekazywania itd.

1. W portalu [translatora niestandardowego](https://portal.customtranslator.azure.ai) kliknij kartę Historia przekazywania, aby wyświetlić historię.

    ![Karta historia przekazywania](media/how-to/how-to-upload-history-1.png)

2. Na tej stronie jest wyświetlany stan wszystkich przeszłych operacji przekazywania. Przedstawia operacje przekazywania od najnowszych do najmniejszych najnowszych. Dla każdego przekazania wyświetlana jest nazwa dokumentu, stan przekazywania, Data przekazania, Liczba przekazanych plików, typ przekazanego pliku oraz para językowa pliku.

    ![Strona przekazywania historii](media/how-to/how-to-document-history-2.png)

3. Kliknij dowolny rekord historii przekazywania. Na stronie szczegóły historii przekazywania można wyświetlić pliki przekazane w ramach przekazywania, przekazanego stanu pliku, języka pliku i komunikatu o błędzie (Jeśli wystąpi błąd podczas przekazywania).

## <a name="next-steps"></a>Następne kroki

- Na [stronie szczegóły dokumentu](how-to-view-document-details.md) można przejrzeć listę wyodrębnionych zdań.
- [Jak szkolić model](how-to-train-model.md).
