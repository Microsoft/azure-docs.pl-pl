---
title: Utwórz indeks wyszukiwania w Azure Portal
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak utworzyć indeks dla platformy Azure Wyszukiwanie poznawcze przy użyciu wbudowanego projektanta indeksów portalu.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74112848"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Tworzenie indeksu usługi Azure Wyszukiwanie poznawcze w portalu

Usługa Azure Wyszukiwanie poznawcze obejmuje wbudowany Projektant indeksów w portalu, który jest przydatny do prototypów lub tworzenia [indeksu wyszukiwania](search-what-is-an-index.md) hostowanego w usłudze Azure wyszukiwanie poznawcze. To narzędzie jest używane do konstruowania schematów. Po zapisaniu definicji pusty indeks zostanie w pełni wyrażony w Wyszukiwanie poznawcze platformy Azure. Sposób ładowania zawartości z możliwością przeszukiwania to do Ciebie.

Projektant indeksów jest tylko jednym podejściem do tworzenia indeksu. Alternatywnie można utworzyć i załadować indeks za pomocą [Kreatora importu danych](search-get-started-portal.md). Kreator działa tylko z indeksami tworzonymi przez siebie. Można programowo utworzyć indeks przy użyciu interfejsów API [platformy .NET](search-create-index-dotnet.md) lub interfejsu [rest](search-create-index-rest-api.md) .

## <a name="start-index-designer"></a>Uruchom projektanta indeksów

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i otwórz pulpit nawigacyjny usługi. Możesz kliknąć pozycję **Wszystkie usługi** na pasku dostępu, aby poszukać istniejących usług wyszukiwania w bieżącej subskrypcji. 

2. Kliknij link **Dodaj indeks** na pasku poleceń w górnej części strony.

   ![Dodaj łącze indeksu na pasku poleceń](media/search-create-index-portal/add-index.png "Dodaj łącze indeksu na pasku poleceń")

3. Nazwij swój indeks Wyszukiwanie poznawcze platformy Azure. Nazwy indeksów są przywoływane w operacjach indeksowania i wykonywania zapytań. Nazwa indeksu stanie się częścią adresu URL punktu końcowego używanego w połączeniach z indeksem i wysyłania żądań HTTP w interfejsie API REST platformy Azure Wyszukiwanie poznawcze.

   * Wpisywanie zacznij od litery.
   * Użyj tylko małych liter, cyfr i łączników („-”).
   * Ogranicz długość nazwy do 60 znaków.

## <a name="add-fields"></a>Dodawanie pól

Kompozycja indeksu obejmuje *kolekcję pól*, która definiuje dane z możliwością wyszukiwania w indeksie. Całkowicie kolekcja Fields określa strukturę dokumentów, które są przesyłane osobno. Kolekcja Fields zawiera pola wymagane i opcjonalne, nazwane i wpisane, z atrybutami indeksu, które określają, jak można użyć pola.

1. Dodaj pola, aby w pełni określić przekazane dokumenty, ustawiając dla nich [Typ danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) . Na przykład, jeśli dokumenty składają się z *identyfikatora hotelu*, *hotelu-Name*, *adresu*, *miasta*i *regionu*, Utwórz odpowiednie pole dla każdego z nich w indeksie. Przejrzyj [wskazówki dotyczące projektowania w poniższej sekcji,](#design) Aby uzyskać pomoc dotyczącą ustawiania atrybutów.

1. Jeśli dane przychodzące mają charakter hierarchiczny, schemat powinien zawierać [typy złożone](search-howto-complex-data-types.md) reprezentujące zagnieżdżone struktury. Wbudowany zestaw danych przykładowych, Hotele, ilustruje złożone typy przy użyciu adresu (zawiera wiele podpól), które mają relację jeden do jednego z każdym hotelem, oraz złożoną kolekcję pokojów, w której wiele pokojów jest skojarzonych z każdym hotelem. 

1. Określ pole *klucza* typu EDM. String. Pole klucza jest obowiązkowe dla każdego indeksu usługi Azure Wyszukiwanie poznawcze i musi być ciągiem. Wartości dla tego pola muszą jednoznacznie identyfikować każdy dokument. Domyślnie pole nosi nazwę *id*, ale można ją zmienić, o ile ciąg spełnia [reguły nazewnictwa](https://docs.microsoft.com/rest/api/searchservice/Naming-rules). Na przykład jeśli kolekcja Fields zawiera *Identyfikator hotelu*, należy wybrać tę opcję dla klucza. 

1. Ustaw atrybuty dla każdego pola. Projektant indeksów wyklucza wszystkie atrybuty, które są nieprawidłowe dla typu danych, ale nie sugeruje, co należy uwzględnić. Zapoznaj się ze wskazówkami w następnej sekcji, aby zrozumieć, do czego służą atrybuty.

    Dokumentacja interfejsu API usługi Azure Wyszukiwanie poznawcze zawiera przykłady kodu z prostym indeksem *hoteli* . Na poniższym zrzucie ekranu można zobaczyć definicję indeksu, w tym Analizator języka francuskiego określony podczas definiowania indeksu, który można odtworzyć jako ćwiczenie w portalu.

    ![Indeks demonstracyjny hoteli](media/search-create-index-portal/field-definitions.png "Indeks demonstracyjny hoteli")

1. Po zakończeniu kliknij przycisk **Utwórz** , aby zapisać i utworzyć indeks.

<a name="design"></a>

## <a name="set-attributes"></a>Ustawianie atrybutów

Chociaż możesz w dowolnym momencie dodać nowe pola, istniejące definicje pól są zablokowane przez cały czas istnienia indeksu. Z tego powodu deweloperzy zazwyczaj używają portalu do tworzenia prostych indeksów, testowania pomysłów lub używania stron portalu w celu wyszukania ustawień. Częsta iteracja po projekcie indeksu jest bardziej wydajna, jeśli stosujesz podejście oparte na kodzie, które pozwala na odbudowanie indeksu w prosty sposób.

Analizatory i sugestory są kojarzone z polami przed zapisaniem indeksu. Pamiętaj, aby dodać analizatory języka lub sugestie do definicji indeksu podczas ich tworzenia.

Pola ciągów są często oznaczone jako **Możliwość sortowania** i **Możliwość pobierania**. Pola używane w celu zawężenia wyników wyszukiwania zawierają oznaczenia **Możliwość sortowania**, **Możliwość filtrowania** i **Możliwość tworzenia aspektów**.

Atrybuty pól określają, jak używane jest dane pole, np. czy jest używane w wyszukiwaniu pełnotekstowym, nawigacji aspektowej, operacjach sortowania i tak dalej. Następująca tabela zawiera opis każdego atrybutu.

|Atrybut|Opis|  
|---------------|-----------------|  
|**możliwością wyszukiwania**|Wyszukiwanie pełnotekstowe, które można poddawać analizie leksykalnej, takiej jak dzielenie wyrazów podczas indeksowania. Ustawienie wartości „sunny day” dla pola z możliwością wyszukiwania spowoduje podział na dwa oddzielne tokeny „sunny” i „day”. Aby uzyskać więcej informacji, zobacz [Jak działa wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md).|  
|**Filtrowanie**|Odwołania do tego atrybutu znajdują się w zapytaniach **$filter**. Pola z możliwością filtrowania typu `Edm.String` lub `Collection(Edm.String)` nie są poddawane dzieleniu wyrazów, więc porównania dotyczą tylko dokładnych dopasowań. Ustawienie pola filtrowania na wartość „sunny day” spowoduje, że pole typu `$filter=f eq 'sunny'` nie wyświetli żadnych wyników, ale `$filter=f eq 'sunny day'` już tak. |  
|**sortowan**|Domyślnie system sortuje pozycje według wyników, ale można również ustawić sortowanie według poszczególnych pól w dokumentach. Pola typu `Collection(Edm.String)` nie mogą być **sortowane**. |  
|**Tworzenie aspektów**|Zwykle używany w prezentacji wyników wyszukiwania, która zawiera liczbę trafień według kategorii (na przykład hotele znajdujące się w określonym mieście). Nie można używać tej opcji z polami typu `Edm.GeographyPoint`. Pola typu `Edm.String` z możliwością **filtrowania**, **sortowania** lub **tworzenia aspektów** mogą mieć maksymalnie 32 kilobajty długości. Aby uzyskać więcej informacji, zobacz [Create Index (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) (Tworzenie indeksu [interfejs REST API]).|  
|**głównych**|Unikatowy identyfikator dokumentów w indeksie. Można wybrać tylko jedno pole klucza i musi ono być typu `Edm.String`.|  
|**Pobieranie**|Określa, czy pole może być zwracane w wynikach wyszukiwania. Jest przydatny, jeśli chcesz użyć pola (np. *marża zysku*) z możliwością filtrowania, sortowania lub oceniania, ale nie chcesz, aby to pole było widoczne dla użytkownika końcowego. Ten atrybut musi przyjmować wartość `true` dla pól typu `key`.|  

## <a name="next-steps"></a>Następne kroki

Po utworzeniu indeksu usługi Azure Wyszukiwanie poznawcze można przejść do następnego kroku: [Przekaż dane z możliwością wyszukiwania do indeksu](search-what-is-data-import.md).

Alternatywnie można również bardziej szczegółowo zapoznać się z [indeksami](search-what-is-an-index.md). Oprócz kolekcji pól indeks określa również analizatory, sugestory, profile oceniania i ustawienia specyfikacji CORS. Portal zawiera strony z kartami umożliwiające definiowanie najczęściej występujących elementów: pól, analizatorów i sugestorów. Aby utworzyć lub zmodyfikować inne elementy, można użyć interfejsu API REST lub zestawu .NET SDK.

## <a name="see-also"></a>Zobacz także

 [Jak działa wyszukiwanie pełnotekstowe](search-lucene-query-architecture.md)  
 [Interfejs API REST usługi Search](https://docs.microsoft.com/rest/api/searchservice/) [Zestaw .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

