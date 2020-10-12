---
title: Przyrostowe koncepcje wzbogacania (wersja zapoznawcza)
titleSuffix: Azure Cognitive Search
description: Buforuj zawartość pośrednią oraz przyrostowe zmiany z potoku wzbogacania AI w usłudze Azure Storage, aby zachować inwestycje w istniejące przetworzone dokumenty. Ta funkcja jest obecnie w publicznej wersji zapoznawczej.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 9fb76c5c96795b8092c86e22acbab4ea5963b42e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971640"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Przyrostowe wzbogacanie i buforowanie na platformie Azure Wyszukiwanie poznawcze

> [!IMPORTANT] 
> Wzbogacanie przyrostowe jest obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> [Wersje zapoznawcze interfejsu API REST](search-api-preview.md) zapewniają tę funkcję. W tej chwili nie ma obsługi portalu lub zestawu SDK platformy .NET.

*Wzbogacanie przyrostowe* jest funkcją, która jest przeznaczona dla [umiejętności](cognitive-search-working-with-skillsets.md). Korzysta ona z usługi Azure Storage, aby zapisać dane wyjściowe przetwarzania emitowane przez potok wzbogacania do ponownego użycia w przyszłych uruchomieniach indeksatora. Gdy jest to możliwe, indeksator ponownie używa wszystkich buforowanych danych wyjściowych, które są nadal ważne. 

Nie tylko to, że przyrostowe wzbogacanie zachowuje inwestycje pieniężne w przetwarzaniu (w szczególności w przypadku przetwarzania OCR i obrazów), ale również zapewnia wydajniejszy system. Gdy struktury i zawartość są buforowane, indeksator może ustalić, które umiejętności uległy zmianie, i uruchamiać tylko te, które zostały zmodyfikowane, a także wszelkie podrzędne umiejętności zależne. 

Przepływ pracy korzystający z buforowania przyrostowego obejmuje następujące kroki:

1. [Utwórz lub określ konto usługi Azure Storage](../storage/common/storage-account-create.md) do przechowywania pamięci podręcznej.
1. [Włącz wzbogacanie przyrostowe](search-howto-incremental-index.md) w indeksatorze.
1. [Utwórz indeksator](/rest/api/searchservice/create-indexer) -Plus [zestawu umiejętności](/rest/api/searchservice/create-skillset) -, aby wywołać potok. Podczas przetwarzania etapy wzbogacania są zapisywane dla każdego dokumentu w usłudze BLOB Storage do użytku w przyszłości.
1. Przetestuj swój kod i po wprowadzeniu zmian Użyj [Update zestawu umiejętności](/rest/api/searchservice/update-skillset) , aby zmodyfikować definicję.
1. [Uruchom indeksator](/rest/api/searchservice/run-indexer) , aby wywołać potok, pobierając buforowane dane wyjściowe w celu szybszego i bardziej wydajnego przetwarzania.

Aby uzyskać więcej informacji o krokach i zagadnieniach podczas pracy z istniejącym indeksatorem, zobacz [set up Incremental wzbogacanie](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Pamięć podręczna indeksatora

Wzbogacanie przyrostowe dodaje pamięć podręczną do potoku wzbogacania. Indeksator buforuje wyniki z krakingu dokumentu oraz dane wyjściowe każdej umiejętności dla każdego dokumentu. Gdy zestawu umiejętności zostanie zaktualizowana, nastąpi ponowne uruchomienie tylko zmienionych lub podrzędnych. Zaktualizowane wyniki są zapisywane w pamięci podręcznej, a dokument zostanie zaktualizowany w indeksie wyszukiwania lub w sklepie merytorycznym.

Fizycznie pamięć podręczna jest przechowywana w kontenerze obiektów BLOB na koncie usługi Azure Storage. Pamięć podręczna używa również magazynu tabel do wewnętrznego rekordu przetwarzania aktualizacji. Wszystkie indeksy w ramach usługi wyszukiwania mogą współużytkować to samo konto magazynu dla pamięci podręcznej indeksatora. Każdy indeksator ma przypisany unikatowy i niezmienny identyfikator pamięci podręcznej do kontenera, którego używa.

## <a name="cache-configuration"></a>Konfiguracja pamięci podręcznej

Musisz ustawić `cache` Właściwość indeksatora, aby rozpocząć korzystając z wzbogacania przyrostowego. Poniższy przykład ilustruje indeksator z włączonym buforowaniem. Określone części tej konfiguracji są opisane w poniższych sekcjach. Aby uzyskać więcej informacji, zobacz [Konfigurowanie stopniowego wzbogacania](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Ustawienie tej właściwości na istniejącym indeksatorze wymaga zresetowania i ponownego uruchomienia indeksatora, co spowoduje ponowne przetworzenie wszystkich dokumentów w źródle danych. Ten krok jest niezbędny do wyeliminowania dokumentów wzbogaconych przez poprzednie wersje zestawu umiejętności. 

## <a name="cache-management"></a>Zarządzanie pamięcią podręczną

Cykl życia pamięci podręcznej jest zarządzany przez indeksator. Jeśli `cache` Właściwość w indeksatorze ma wartość null lub parametry połączenia są zmieniane, istniejąca pamięć podręczna zostanie usunięta z następnego uruchomienia indeksatora. Cykl życia pamięci podręcznej jest również powiązany z cyklem życia indeksatora. Jeśli indeksator zostanie usunięty, skojarzona pamięć podręczna również zostanie usunięta.

Chociaż przyrostowe wzbogacanie jest przeznaczone do wykrywania i reagowania na zmiany bez interwencji w Twojej części, istnieją parametry, których można użyć do przesłonięcia zachowań domyślnych:

+ Ustalanie priorytetów nowych dokumentów
+ Pomiń testy zestawu umiejętności
+ Pomiń sprawdzanie źródła danych
+ Wymuś Obliczanie zestawu umiejętności

### <a name="prioritize-new-documents"></a>Ustalanie priorytetów nowych dokumentów

Ustaw `enableReprocessing` Właściwość w celu kontrolowania przetwarzania dokumentów przychodzących, które są już reprezentowane w pamięci podręcznej. Gdy `true` (domyślnie), dokumenty znajdujące się już w pamięci podręcznej są ponownie przetwarzane po ponownym uruchomieniu indeksatora, przy założeniu, że aktualizacja umiejętności ma wpływ na ten dokument. 

Gdy `false` istniejące dokumenty nie są ponownie przetwarzane, efektywne Ustawianie priorytetów nowej, przychodzącej zawartości do istniejącej zawartości. Należy ustawić tylko `enableReprocessing` `false` na chwilę. Aby zapewnić spójność w korpus, `enableReprocessing` należy w `true` większości przypadków zapewnić, że wszystkie dokumenty, zarówno nowe, jak i istniejące, są prawidłowe dla bieżącej definicji zestawu umiejętności.

### <a name="bypass-skillset-evaluation"></a>Pomiń Obliczanie zestawu umiejętności

Modyfikowanie zestawu umiejętności i przetwarzanie tego zestawu umiejętności zwykle jest dostępne. Jednak niektóre zmiany w zestawu umiejętności nie powinny powodować ponownego przetwarzania (na przykład w celu wdrożenia niestandardowej umiejętności w nowej lokalizacji lub z nowym kluczem dostępu). Najprawdopodobniej są to modyfikacje peryferyjne, które nie mają oryginalnego wpływu na istotę zestawu umiejętności. 

Jeśli wiesz, że zmiana zestawu umiejętności jest rzeczywiście równa, należy zastąpić ocenę zestawu umiejętności, ustawiając `disableCacheReprocessingChangeDetection` parametr na `true` :

1. Zadzwoń do zestawu umiejętności Update i zmodyfikuj definicję zestawu umiejętności.
1. Dołącz `disableCacheReprocessingChangeDetection=true` parametr do żądania.
1. Prześlij zmianę.

Ustawienie tego parametru gwarantuje, że tylko aktualizacje definicji zestawu umiejętności są zatwierdzane, a zmiana nie zostanie oceniona pod kątem efektów istniejących korpus.

Poniższy przykład przedstawia żądanie aktualizacji zestawu umiejętności z parametrem:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2020-06-30-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Pomiń sprawdzanie poprawności źródła danych

Większość zmian w definicji źródła danych spowoduje unieważnienie pamięci podręcznej. Jednak w przypadku scenariuszy, w których wiadomo, że zmiana nie powinna unieważnić pamięci podręcznej, takich jak zmiana parametrów połączenia lub obracanie klucza na koncie magazynu, należy dołączyć `ignoreResetRequirement` parametr do aktualizacji źródła danych. Ustawienie tego parametru `true` pozwala na przechodzenie przez proces zatwierdzania, bez wyzwalania warunku resetowania, który spowodowałaby odbudowanie wszystkich obiektów i wypełnienie ich od podstaw.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2020-06-30-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Wymuś Obliczanie zestawu umiejętności

Celem pamięci podręcznej jest uniknięcie niepotrzebnego przetwarzania, ale Załóżmy, że wprowadzasz zmiany w umiejętności, którą indeksator nie wykrywa (na przykład w przypadku zmiany elementu w kodzie zewnętrznym, np. z niestandardową umiejętnością).

W takim przypadku można użyć możliwości [resetowania](/rest/api/searchservice/preview-api/reset-skills) w celu wymuszenia przetworzenia konkretnej umiejętności, w tym wszelkich umiejętności podrzędnych, które są zależne od danych wyjściowych tej umiejętności. Ten interfejs API akceptuje żądanie POST z listą umiejętności, które powinny być unieważnione i oznaczone do ponownego przetworzenia. Po zresetowaniu umiejętności Uruchom indeksator w celu wywołania potoku.

## <a name="change-detection"></a>Wykrywanie zmian

Po włączeniu pamięci podręcznej indeksator oblicza zmiany w kompozycji potoku, aby określić, która zawartość może zostać ponownie użyta i które wymaga ponownego przetworzenia. Ta sekcja wylicza zmiany, które unieważnią pamięć podręczną, a następnie zmiany wyzwalające przetwarzanie przyrostowe. 

### <a name="changes-that-invalidate-the-cache"></a>Zmiany, które unieważnią pamięć podręczną

Zmiana na unieważnienie jest taka, gdzie cała pamięć podręczna nie jest już prawidłowa. Przykładem zmiany unieważnienia jest to, gdzie źródło danych jest aktualizowane. Oto pełna lista zmian, które spowodują unieważnienie pamięci podręcznej:

* Zmień typ źródła danych
* Zmień na kontener źródła danych
* Poświadczenia dostępu do źródła danych
* Zasady wykrywania zmian źródła danych
* Zasady wykrywania usuwania źródeł danych
* Mapowania pól indeksatora
* Parametry indeksatora
    * Tryb analizowania
    * Wykluczone rozszerzenia nazw plików
    * Indeksowane rozszerzenia nazw plików
    * Indeksuj metadane magazynu tylko dla powiększonych dokumentów
    * Rozdzielane nagłówki tekstu
    * Rozdzielany ogranicznik tekstu
    * Katalog główny dokumentu
    * Akcja obrazu (zmiany w sposobie wyodrębniania obrazów)

### <a name="changes-that-trigger-incremental-processing"></a>Zmiany wyzwalające przetwarzanie przyrostowe

Przetwarzanie przyrostowe szacuje definicję zestawu umiejętności i decyduje o tym, które umiejętności należy ponownie uruchomić, co umożliwi wybiórcze aktualizowanie odpowiednich części drzewa dokumentów. Oto kompletna lista zmian wynikających z wzbogacania przyrostowego:

* Umiejętność w zestawu umiejętności ma inny typ. Typ OData kwalifikacji został zaktualizowany
* Zaktualizowane parametry dotyczące umiejętności, na przykład adres URL, wartości domyślne lub inne parametry
* Zmiany w danych wyjściowych kwalifikacji, umiejętność zwraca dodatkowe lub różne dane wyjściowe
* Aktualizacje umiejętności, które różnią się od pochodzenie, zostały zmienione w łańcuchu umiejętności, tj. dane wejściowe kwalifikacji
* Wszelkie unieważnienia kwalifikacji dla wszystkich nadrzędnych, jeśli umiejętność dostarczająca dane wejściowe do tej umiejętności zostanie zaktualizowana
* Aktualizacje lokalizacji projekcji magazynu wiedzy, wyniki w dokumencie
* Zmiany w projekcjach w sklepie wiedzy, wyniki dotyczące przeprojektowania dokumentów
* Mapowania pól wyjściowych zostały zmienione w indeksatorze w wyniku przeprojektowania dokumentów do indeksu

## <a name="api-reference"></a>Dokumentacja interfejsu API

Wersja interfejsu API REST `2020-06-30-Preview` zapewnia przyrostowe wzbogacanie za poorednictwem dodatkowych właściwości indeksatorów. Umiejętności i źródła danych mogą korzystać z ogólnie dostępnej wersji. Oprócz dokumentacji referencyjnej zapoznaj się z artykułem  [Konfigurowanie buforowania dla wzbogacania przyrostowego](search-howto-incremental-index.md) , aby uzyskać szczegółowe informacje na temat wywoływania interfejsów API.

+ [Tworzenie indeksatora (API-Version = 2020-06 -30 — wersja zapoznawcza)](/rest/api/searchservice/create-indexer) 

+ [Update indeksator (API-Version = 2020-06 -30 — wersja zapoznawcza)](/rest/api/searchservice/update-indexer) 

+ [Update zestawu umiejętności (API-Version = 2020-06-30)](/rest/api/searchservice/update-skillset) (nowy parametr URI żądania)

+ [Resetowanie umiejętności (API-Version = 2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ Indeksatory baz danych (Azure SQL, Cosmos DB). Niektóre indeksatory pobierają dane za poorednictwem zapytań. W przypadku zapytań, które pobierają dane, [Aktualizacja źródła danych](/rest/api/searchservice/update-data-source) obsługuje nowy parametr na żądanie **ignoreResetRequirement**, które należy ustawić, `true` gdy akcja aktualizacji nie powinna unieważniać pamięci podręcznej. 

  Używaj **ignoreResetRequirement** oszczędnie, ponieważ może to doprowadzić do niezamierzonej niespójności danych, które nie zostaną łatwo wykryte.

## <a name="next-steps"></a>Następne kroki

Przyrostowe wzbogacanie to zaawansowana funkcja, która rozszerza śledzenie zmian na umiejętności i wzbogacanie AI. Przyrostowe wzbogacanie umożliwia ponowne użycie istniejącej przetworzonej zawartości podczas iteracji nad projektem zestawu umiejętności.

W następnym kroku należy włączyć buforowanie w istniejącym indeksatorze lub dodać pamięć podręczną podczas definiowania nowego indeksatora.

> [!div class="nextstepaction"]
> [Skonfiguruj buforowanie na potrzeby wzbogacania przyrostowego](search-howto-incremental-index.md)