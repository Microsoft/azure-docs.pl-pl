---
title: Pojęcia dotyczące debugowania sesji (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Sesje debugowania, do których dostęp odbywa się za pomocą Azure Portal, udostępnia środowisko IDE podobne do, w którym można identyfikować i naprawiać błędy, weryfikować zmiany i wypchnąć zmiany do umiejętności w potoku wzbogacania AI. Sesje debugowania są w wersji zapoznawczej.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: f666cc2f9d11b1e05d11ba266897e6fca3cd287e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92057624"
---
# <a name="debug-sessions-in-azure-cognitive-search"></a>Debugowanie sesji na platformie Azure Wyszukiwanie poznawcze

Sesje debugowania to edytor wizualny, który współpracuje z istniejącym zestawu umiejętności w Azure Portal. W ramach sesji debugowania można identyfikować i rozwiązywać błędy, sprawdzać poprawność zmian i wypchnąć zmiany do zestawu umiejętności produkcyjnego w potoku wzbogacenia AI.

> [!Important]
> Sesje debugowania to funkcja w wersji zapoznawczej, która jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="using-debug-sessions"></a>Korzystanie z sesji debugowania

Po uruchomieniu sesji Usługa tworzy kopię zestawu umiejętności, indeksatora i indeksu, gdzie jest używany pojedynczy dokument do testowania zestawu umiejętności. Zmiany wprowadzone w sesji są zapisywane w sesji. Zmiany wprowadzone w sesji debugowania nie wpłyną na zestawu umiejętności produkcyjny, chyba że zmiany zostaną zatwierdzone. Zatwierdzenie zmian spowoduje zastąpienie produkcji zestawu umiejętności.

Podczas sesji debugowania można edytować zestawu umiejętności, sprawdzać i sprawdzać poprawność każdego węzła w drzewie wzbogacania w kontekście określonego dokumentu. Po rozwiązaniu problemów z potokiem wzbogacania zmiany mogą zostać zapisane w sesji i przekazane do zestawu umiejętności w środowisku produkcyjnym. 

Jeśli potok wzbogacania nie zawiera żadnych błędów, sesja debugowania może być używana do przyrostowego wzbogacania dokumentu, testowania i weryfikowania każdej zmiany przed zatwierdzeniem zmian.

## <a name="creating-a-debug-session"></a>Tworzenie sesji debugowania

Aby rozpocząć sesję debugowania, musisz mieć istniejący potok wzbogacania AI, włącznie z; Źródło danych, zestawu umiejętności, indeksator i indeks. Aby skonfigurować sesję debugowania, należy nazwać sesję i podać konto magazynu ogólnego przeznaczenia, które będzie używane do buforowania wykonywania umiejętności podczas uruchamiania indeksatora. Konieczne będzie również wybranie indeksatora, który będzie uruchomiony. Indeksator ma odwołania przechowywane do źródła danych, zestawu umiejętności i index. Sesja debugowania zostanie domyślnie wykorzystana z pierwszego dokumentu w źródle danych lub można określić dokument w źródle danych, aby wykonać krok.

> :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="Tworzenie sesji debugowania":::

## <a name="debug-session-features"></a>Funkcje debugowania sesji

Sesja debugowania rozpoczyna się od wykonania zestawu umiejętności w wybranym dokumencie. Sesja debugowania spowoduje zapisanie dodatkowych metadanych skojarzonych z każdą operacją w zestawu umiejętności. Metadane utworzone przez wykonania potoku, informuje następujący zestaw funkcji, które są następnie używane do identyfikowania i rozwiązywania problemów z zestawu umiejętności.

## <a name="ai-enrichments"></a>Wzbogacanie AI

Ponieważ umiejętności wykonują drzewo wzbogacania, reprezentujące dokument. Użycie drzewa do wizualizacji danych wyjściowych umiejętności lub wzbogacania zapewnia kompleksowy wygląd wszystkich przeprowadzonych operacji wzbogacania. Można wyszukać cały dokument i zbadać każdy węzeł drzewa wzbogacania. Ta perspektywa ułatwia kształtowanie obiektów. Ten format udostępnia również podpowiedzi wizualne do typu, ścieżki i zawartości każdego węzła w drzewie.

## <a name="skill-graph"></a>Wykres umiejętności

Widok **Wykres umiejętności** zawiera hierarchiczną, wizualną reprezentację zestawu umiejętności. Wykres jest górną reprezentacją kolejności, w której są wykonywane umiejętności. Umiejętności, które są zależne od danych wyjściowych innych umiejętności, będą widoczne na wykresie poniżej. Umiejętności na tym samym poziomie w hierarchii mogą być wykonywane równolegle. 

Wybranie umiejętności na grafie spowoduje wyróżnienie związanych z nim umiejętności, węzłów, które tworzą dane wejściowe i węzły, które akceptują swoje wyjście. Każdy węzeł umiejętności wyświetla jego typ, błędy lub ostrzeżenia oraz liczby wykonań. Na **wykresie umiejętności** można wybrać umiejętność debugowania lub udoskonalania. Po wybraniu kwalifikacji jego szczegóły będą wyświetlane w okienku Szczegóły umiejętności po prawej stronie wykresu.

> :::image type="content" source="media/cognitive-search-debug/skills-graph.png" alt-text="Wykres umiejętności":::

## <a name="skill-details"></a>Szczegóły kwalifikacji

W okienku Szczegóły umiejętności zostanie wyświetlony zestaw obszarów do pracy z konkretną umiejętnością, gdy ta umiejętność zostanie wyróżniona na **grafie umiejętności**. Możesz przeglądać i edytować szczegóły ustawień umiejętności. Podano definicję JSON umiejętności. Wyświetlane są również szczegóły dotyczące wykonywania umiejętności oraz błędy i ostrzeżenia. Karta **Ustawienia umiejętności** & w **Edytorze JSON kwalifikacji** można umożliwić bezpośrednie modyfikacje umiejętności. [`</>`](#expression-evaluator)Otwiera okno umożliwiające wyświetlanie i Edytowanie wyrażeń danych wejściowych i wyjściowych umiejętności.

Zagnieżdżone kontrolki wejściowe w oknie Ustawienia umiejętności mogą służyć do tworzenia złożonych kształtów dla projekcji, mapowania pól wyjściowych dla pola typu złożonego lub dane wejściowe do umiejętności. Gdy jest używany z **ewaluatora wyrażeń**, zagnieżdżone dane wejściowe zapewniają łatwy test i sprawdzanie poprawności konstruktora wyrażeń.

## <a name="skill-execution-history"></a>Historia wykonywania kwalifikacji

Umiejętność można wykonać wiele razy w zestawu umiejętności dla jednego dokumentu. Na przykład umiejętność OCR będzie wykonywana jeden raz dla każdego obrazu wyodrębnionego z każdego dokumentu. W okienku Szczegóły umiejętności na karcie **wykonania** zostanie wyświetlona historia wykonywania umiejętności, która zapewnia dokładniejsze zapoznaj się z każdym wywołaniem umiejętności. 

Historia wykonywania umożliwia śledzenie określonego wzbogacania z powrotem do umiejętności, która go wygenerowała. Kliknięcie danych wejściowych kwalifikacji przenosi do umiejętności, która wygenerowała te dane wejściowe. Pozwala to na identyfikację głównej przyczyny problemu, który może być manifestem z poziomu umiejętności podrzędnej. 

W przypadku zidentyfikowania potencjalnego problemu historia wykonywania wyświetla linki do umiejętności, które wygenerowały określone dane wejściowe, zapewniając funkcję śledzenia stosu. Klikając umiejętność skojarzoną z danymi wejściowymi, przejdź do umiejętności, aby naprawić wszelkie usterki, lub Kontynuuj śledzenie określonego problemu.

Podczas kompilowania niestandardowej umiejętności lub debugowania błędu z niestandardową umiejętnością istnieje możliwość wygenerowania żądania dotyczącego wywołania umiejętności w historii wykonywania.

## <a name="enriched-data-structure"></a>Ulepszona struktura danych

Okienko **ulepszona struktura danych** pokazuje wzbogacenia dokumentu za pomocą zestawu umiejętności, szczegółowo kontekst dla każdego wzbogacania i źródłową umiejętność. **Ewaluatora wyrażeń** można także użyć do wyświetlania zawartości dla każdego wzbogacania.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-display.png" alt-text="Ulepszona struktura danych":::

## <a name="expression-evaluator"></a>Ewaluatora wyrażeń

**Ewaluatora wyrażeń** umożliwia szybkie wgląd do wartości dowolnej ścieżki. Umożliwia edytowanie ścieżki i testowanie wyników przed aktualizacją jakichkolwiek wejść lub kontekstu dla umiejętności lub projekcji.

## <a name="errorswarnings"></a>Błędy/ostrzeżenia

W tym oknie są wyświetlane wszystkie błędy i ostrzeżenia, które zestawu umiejętności produkuje, gdy jest wykonywane względem dokumentu w sesji debugowania.

## <a name="limitations"></a>Ograniczenia

Debuguj sesisons pracy ze wszystkimi ogólnie dostępnymi źródłami danych AMD w większości źródeł danych. Interfejs API MongoDB (wersja zapoznawcza) i interfejs API Cassandra (wersja zapoznawcza) Cosmos DB nie są obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy zrozumiesz elementy sesji debugowania, wypróbuj samouczek, aby poznać praktyczne środowisko pracy.

> [!div class="nextstepaction"]
> [Samouczek dotyczący funkcji eksplorowania sesji debugowania](./cognitive-search-tutorial-debug-sessions.md)