---
title: Wzorce projektowe tabeli usługi Azure Storage | Microsoft Docs
description: Przejrzyj wzorce projektowe, które są odpowiednie do użycia z rozwiązaniami Table service na platformie Azure. Problemy dotyczące adresów i ich zalet, które zostały omówione w innych artykułach.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 2eb109078728b8a9070b3991733450c1da790d9e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879599"
---
# <a name="table-design-patterns"></a>Wzorce projektowe tabel
W tym artykule opisano niektóre wzorce odpowiednie do użycia z rozwiązaniami Table service. Ponadto zobaczysz, jak można praktycznie rozwiązać niektóre problemy i wady, które omówiono w innych artykułach dotyczących projektowania magazynów tabel. Poniższy diagram podsumowuje relacje między różnymi wzorcami:  

![Aby wyszukać powiązane dane](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Powyższa Mapa wzorca podświetla niektóre relacje między wzorcami (Blue) i antywzorców (pomarańczowy), które są opisane w tym przewodniku. Należy wziąć pod uwagę wiele innych wzorców. Na przykład jeden z najważniejszych scenariuszy usługi Table Service to użycie [wzorca widoku materiałowego](/previous-versions/msp-n-p/dn589782(v=pandp.10)) z wzorca [podziału odpowiedzialności (CQRS)](/previous-versions/msp-n-p/jj554200(v=pandp.10)) .  

## <a name="intra-partition-secondary-index-pattern"></a>Wzorzec indeksu pomocniczego wewnątrz partycji
Przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości **RowKey** (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey** . Aktualizacje między kopiami można utrzymywać spójnie przy użyciu EGTs.  

### <a name="context-and-problem"></a>Kontekst i problem
Table service automatycznie indeksuje jednostki przy użyciu wartości **PartitionKey** i **RowKey** . Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu tych wartości. Na przykład przy użyciu struktury tabeli pokazanej poniżej, aplikacja kliencka może użyć zapytania punkt do pobrania indywidualnej jednostki pracownika przy użyciu nazwy działu i identyfikatora pracownika (wartości **PartitionKey** i **RowKey** ). Klient może również pobrać jednostki posortowane według identyfikatora pracownika w poszczególnych działach.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Jeśli chcesz również znaleźć jednostkę pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, musisz użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Wynika to z faktu, że usługa Table Service nie udostępnia indeksów pomocniczych. Ponadto nie ma możliwości zażądania listy pracowników posortowanych w innej kolejności niż kolejność **RowKey** .  

### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii poszczególnych jednostek z każdą kopią przy użyciu innej wartości **RowKey** . Jeśli przechowujesz jednostkę z poniższymi strukturami, możesz efektywnie pobrać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksów **RowKey**"empid_" i "email_" umożliwiają wykonywanie zapytań dotyczących jednego pracownika lub zakresu pracowników przy użyciu zakresu adresów E-mail lub identyfikatorów pracowników.  

![Jednostki pracowników](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Poniższe dwa kryteria filtrowania (jeden wyszukiwany według identyfikatora pracownika i jeden z nich w adresie e-mail) określają kwerendy punktowe:  

* $filter = (PartitionKey EQ "Sales") i (RowKey EQ "empid_000223")  
* $filter = (PartitionKey EQ "Sales") i (RowKey EQ " email_jonesj@contoso.com ")  

W przypadku wykonywania zapytań dotyczących zakresu jednostek pracowników można określić zakres posortowany w kolejności identyfikatorów pracowników lub zakres posortowany w kolejności adresów e-mail przez wyszukanie jednostek z odpowiednim prefiksem w **RowKey**.  

* Aby znaleźć wszystkich pracowników działu sprzedaży z IDENTYFIKATORem pracownika z zakresu od 000100 do 000199, użyj: $filter = (PartitionKey EQ "Sales") i (RowKey GE "empid_000100") i (RowKey Le "empid_000199")  
* Aby znaleźć wszystkich pracowników działu sprzedaży przy użyciu adresu e-mail rozpoczynającego się od litery "a", użyj: $filter = (PartitionKey EQ "Sales") i (RowKey GE "email_a") i (RowKey lt "email_b")  
  
  Składnia filtru użyta w powyższych przykładach pochodzi z interfejsu API REST Table service, aby uzyskać więcej informacji, zobacz temat [jednostki zapytań](/rest/api/storageservices/Query-Entities).  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Użycie magazynu tabel jest stosunkowo niedrogie, więc obciążenie kosztem przechowywania zduplikowanych danych nie powinno być istotnym problemem. Należy jednak zawsze oszacować koszt projektu w oparciu o przewidywane wymagania dotyczące magazynu i dodawać tylko zduplikowane jednostki do obsługi zapytań, które będą wykonywane w aplikacji klienckiej.  
* Ponieważ pomocnicze jednostki indeksu są przechowywane w tej samej partycji co oryginalne jednostki, należy upewnić się, że elementy docelowe skalowalności dla pojedynczej partycji nie zostaną przekroczone.  
* Zduplikowane jednostki można zachować spójnie ze sobą za pomocą EGTs, aby zaktualizować dwie kopie jednostki jako niepodzielne. Oznacza to, że należy przechowywać wszystkie kopie jednostki w tej samej partycji. Aby uzyskać więcej informacji, zobacz sekcję [Korzystanie z transakcji grupy jednostek](table-storage-design.md#entity-group-transactions).  
* Wartość używana dla **RowKey** musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Uzupełnienie wartości liczbowych w **RowKey** (na przykład identyfikator pracownika 000223) umożliwia poprawne sortowanie i filtrowanie na podstawie górnych i dolnych granic.  
* Nie trzeba duplikować wszystkich właściwości obiektu. Na przykład jeśli zapytania wyszukujące jednostki przy użyciu adresu e-mail w **RowKey** nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:

   ![Struktura jednostki pracownika](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Zazwyczaj lepszym rozwiązaniem jest przechowywanie zduplikowanych danych i upewnienie się, że można pobrać wszystkie potrzebne dane za pomocą pojedynczego zapytania, niż w celu znalezienia jednostki i drugiej, aby wyszukać wymagane dane.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy, gdy klient musi pobrać jednostki w różnych zamówieniach sortowania i gdzie można zidentyfikować poszczególne jednostki przy użyciu różnych unikatowych wartości. Należy jednak pamiętać, że nie przekracza limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostek przy użyciu różnych wartości **RowKey** .  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec indeksu pomocniczego między partycjami](#inter-partition-secondary-index-pattern)
* [Wzorzec klucza złożonego](#compound-key-pattern)
* Transakcje grupy jednostek
* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Wzorzec indeksu pomocniczego między partycjami
Przechowuj wiele kopii każdej jednostki przy użyciu różnych wartości **RowKey** w oddzielnych partycjach lub w oddzielnych tabelach, aby włączyć szybkie i wydajne wyszukiwania oraz alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey** .  

### <a name="context-and-problem"></a>Kontekst i problem
Table service automatycznie indeksuje jednostki przy użyciu wartości **PartitionKey** i **RowKey** . Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu tych wartości. Na przykład przy użyciu struktury tabeli pokazanej poniżej, aplikacja kliencka może użyć zapytania punkt do pobrania indywidualnej jednostki pracownika przy użyciu nazwy działu i identyfikatora pracownika (wartości **PartitionKey** i **RowKey** ). Klient może również pobrać jednostki posortowane według identyfikatora pracownika w poszczególnych działach.  

![Identyfikator pracownika](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Jeśli chcesz również znaleźć jednostkę pracownika na podstawie wartości innej właściwości, takiej jak adres e-mail, musisz użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowanie. Wynika to z faktu, że usługa Table Service nie udostępnia indeksów pomocniczych. Ponadto nie ma możliwości zażądania listy pracowników posortowanych w innej kolejności niż kolejność **RowKey** .  

Przewidujesz dużą liczbę transakcji w odniesieniu do tych jednostek i chcesz zminimalizować ryzyko Table service dławienia klienta.  

### <a name="solution"></a>Rozwiązanie
Aby obejść brak indeksów pomocniczych, można przechowywać wiele kopii każdej jednostki przy użyciu różnych wartości **PartitionKey** i **RowKey** . Jeśli przechowujesz jednostkę z poniższymi strukturami, możesz efektywnie pobrać jednostki pracowników na podstawie adresu e-mail lub identyfikatora pracownika. Wartości prefiksów **PartitionKey**"empid_" i "email_" umożliwiają zidentyfikowanie indeksu, który ma być używany w zapytaniu.  

![Indeks podstawowy i pomocniczy](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Poniższe dwa kryteria filtrowania (jeden wyszukiwany według identyfikatora pracownika i jeden z nich w adresie e-mail) określają kwerendy punktowe:  

* $filter = (PartitionKey EQ "empid_Sales") i (RowKey EQ "000223")
* $filter = (PartitionKey EQ "email_Sales") i (RowKey EQ " jonesj@contoso.com ")  

W przypadku wykonywania zapytań dotyczących zakresu jednostek pracowników można określić zakres posortowany w kolejności identyfikatorów pracowników lub zakres posortowany w kolejności adresów e-mail przez wyszukanie jednostek z odpowiednim prefiksem w **RowKey**.  

* Aby znaleźć wszystkich pracowników działu sprzedaży z IDENTYFIKATORem pracownika z zakresu od **000100** do **000199** posortowanego w kolejności identyfikatorów pracowników: $Filter = (PartitionKey EQ "empid_Sales") i (RowKey GE "000100") i (RowKey Le "000199")  
* Aby znaleźć wszystkich pracowników działu sprzedaży przy użyciu adresu e-mail rozpoczynającego się od "a" posortowanego w kolejności adresów e-mail Użyj: $filter = (PartitionKey EQ "email_Sales") i (RowKey GE "a") i (RowKey lt "b")  

Składnia filtru użyta w powyższych przykładach pochodzi z interfejsu API REST Table service, aby uzyskać więcej informacji, zobacz temat [jednostki zapytań](/rest/api/storageservices/Query-Entities).  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Zduplikowane jednostki można zachować ze sobą spójnie, korzystając ze [wzorca ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern) , aby zachować jednostki indeksu podstawowego i pomocniczego.  
* Użycie magazynu tabel jest stosunkowo niedrogie, więc obciążenie kosztem przechowywania zduplikowanych danych nie powinno być istotnym problemem. Należy jednak zawsze oszacować koszt projektu w oparciu o przewidywane wymagania dotyczące magazynu i dodawać tylko zduplikowane jednostki do obsługi zapytań, które będą wykonywane w aplikacji klienckiej.  
* Wartość używana dla **RowKey** musi być unikatowa dla każdej jednostki. Rozważ użycie wartości klucza złożonego.  
* Uzupełnienie wartości liczbowych w **RowKey** (na przykład identyfikator pracownika 000223) umożliwia poprawne sortowanie i filtrowanie na podstawie górnych i dolnych granic.  
* Nie trzeba duplikować wszystkich właściwości obiektu. Na przykład jeśli zapytania wyszukujące jednostki przy użyciu adresu e-mail w **RowKey** nigdy nie potrzebują wieku pracownika, te jednostki mogą mieć następującą strukturę:
  
   ![Jednostka pracownika (indeks pomocniczy)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Zazwyczaj lepszym rozwiązaniem jest przechowywanie zduplikowanych danych i upewnienie się, że można pobrać wszystkie potrzebne dane za pomocą pojedynczego zapytania niż w celu wyszukania jednostki przy użyciu indeksu pomocniczego, a drugi do przeszukiwania wymaganych danych w indeksie podstawowym.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy aplikacja kliencka musi pobrać jednostki przy użyciu różnych kluczy, gdy klient musi pobrać jednostki w różnych zamówieniach sortowania i gdzie można zidentyfikować poszczególne jednostki przy użyciu różnych unikatowych wartości. Użyj tego wzorca, jeśli chcesz uniknąć przekroczenia limitów skalowalności partycji podczas przeprowadzania wyszukiwania jednostek przy użyciu różnych wartości **RowKey** .  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec indeksu pomocniczego wewnątrz partycji](#intra-partition-secondary-index-pattern)  
* [Wzorzec klucza złożonego](#compound-key-pattern)  
* Transakcje grupy jednostek  
* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Wzorzec ostatecznie spójnych transakcji
Włącz ostatecznie spójne zachowanie między granicami partycji lub granicami systemu magazynu za pomocą kolejek platformy Azure.  

### <a name="context-and-problem"></a>Kontekst i problem
EGTs Włącz transakcję niepodzielną w wielu jednostkach, które współużytkują ten sam klucz partycji. Ze względu na wydajność i skalowalność można zdecydować się na przechowywanie jednostek, które mają wymagania spójności w oddzielnych partycjach lub w osobnym systemie magazynu: w takiej sytuacji nie można użyć EGTs do zachowania spójności. Na przykład może istnieć wymóg zachowania spójności ostatecznej między:  

* Jednostki przechowywane w dwóch różnych partycjach w tej samej tabeli, w różnych tabelach lub na różnych kontach magazynu.  
* Jednostka przechowywana w Table service i obiekt BLOB przechowywany w Blob service.  
* Jednostka przechowywana w Table service i pliku w systemie plików.  
* Jednostka przechowywana w Table service nie została jeszcze zindeksowana przy użyciu usługi Azure Wyszukiwanie poznawcze.  

### <a name="solution"></a>Rozwiązanie
Korzystając z kolejek platformy Azure, można zaimplementować rozwiązanie, które zapewnia spójność ostateczną na co najmniej dwóch partycjach lub systemach magazynowania.
Aby zilustrować to podejście, Załóżmy, że wymagane jest umożliwienie archiwizowania starych jednostek pracowników. Stare jednostki pracowników są rzadko badane i powinny być wykluczone z wszelkich działań, które zajmują się bieżącymi pracownikami. Aby zaimplementować to wymaganie, należy przechowywać aktywnych pracowników w **bieżącej** tabeli i starych pracowników w tabeli **archiwum** . Archiwizowanie pracownika wymaga usunięcia jednostki z **bieżącej** tabeli i dodania jednostki do tabeli **archiwum** , ale nie można użyć EGT do wykonania tych dwóch operacji. Aby uniknąć ryzyka, że błąd powoduje, że jednostka pojawia się w obu tabelach lub żadnej z nich, operacja archiwizowania musi być ostatecznie spójna. Poniższy diagram sekwencji przedstawia kroki tej operacji. Więcej szczegółów na temat ścieżek wyjątków znajduje się w tekście poniżej.  

![Rozwiązanie usługi Azure Queues](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Klient inicjuje operację archiwizowania, umieszczając komunikat w kolejce platformy Azure, w tym przykładzie, aby zarchiwizować #456 pracownika. Rola procesu roboczego sonduje kolejkę pod kątem nowych komunikatów; gdy go znajdzie, odczytuje komunikat i pozostawia ukrytą kopię w kolejce. Następnie rola proces roboczy pobiera kopię jednostki z **bieżącej** tabeli, wstawia kopię w tabeli **archiwum** , a następnie usuwa oryginalną z **bieżącej** tabeli. Na koniec w przypadku braku błędów z poprzednich kroków rola proces roboczy usuwa ukryty komunikat z kolejki.  

W tym przykładzie krok 4 wstawia pracownika do tabeli **archiwum** . Może dodać pracownika do obiektu BLOB w Blob service lub pliku w systemie plików.  

### <a name="recovering-from-failures"></a>Odzyskiwanie po awarii
Należy pamiętać, że operacje w krokach **4** i **5** muszą być *idempotentne* w przypadku, gdy rola proces roboczy wymaga ponownego uruchomienia operacji archiwizowania. Jeśli używasz Table service, w kroku **4** należy użyć operacji INSERT lub Replace. w kroku **5** należy użyć operacji "Usuń Jeśli istnieje" w używanej bibliotece klienckiej. W przypadku korzystania z innego systemu magazynu należy użyć odpowiedniej operacji idempotentne.  

Jeśli rola proces roboczy nigdy nie kończy krok **6**, po upływie limitu czasu komunikat pojawi się ponownie w kolejce gotowej do przetworzenia przez rolę procesu roboczego. Rola proces roboczy może sprawdzić, ile razy wiadomość w kolejce została odczytana i w razie potrzeby flagą jest komunikat "Trująca" w celu zbadania przez wysłanie go do oddzielnej kolejki. Aby uzyskać więcej informacji o odczytywaniu komunikatów w kolejce i sprawdzaniu liczby odgałęzień, zobacz [pobieranie komunikatów](/rest/api/storageservices/Get-Messages).  

Niektóre błędy usług tabel i kolejek są błędami przejściowymi, a aplikacja kliencka powinna zawierać odpowiednią logikę ponawiania, aby je obsłużyć.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie nie zapewnia izolacji transakcji. Na przykład klient może odczytać **bieżące** i **archiwalne** tabele, gdy rola proces roboczy przeprowadzono między krokami **4** i **5**, i wyświetlić niespójny widok danych. Dane będą ostatecznie spójne.  
* Musisz się upewnić, że kroki 4 i 5 są idempotentne w celu zapewnienia spójności ostatecznej.  
* Możesz skalować rozwiązanie przy użyciu wielu kolejek i wystąpień roli procesu roboczego.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz zagwarantowania ostatecznej spójności między jednostkami, które istnieją w różnych partycjach lub tabelach. Ten wzorzec można rozwinąć, aby zapewnić spójność ostateczną operacji dla Table service i Blob service i innych źródeł danych niezwiązanych z usługą Azure Storage, takich jak baza danych lub system plików.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek  
* [Scal lub Zamień](#merge-or-replace)  

> [!NOTE]
> Jeśli izolacja transakcji jest ważna dla Twojego rozwiązania, należy rozważyć przeprojektowanie tabel, aby umożliwić korzystanie z EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Wzorzec jednostek indeksu
Zachowaj jednostki indeksu, aby włączyć wydajne wyszukiwania zwracające listy jednostek.  

### <a name="context-and-problem"></a>Kontekst i problem
Table service automatycznie indeksuje jednostki przy użyciu wartości **PartitionKey** i **RowKey** . Dzięki temu aplikacja kliencka może efektywnie pobrać jednostkę przy użyciu zapytania punktowego. Na przykład przy użyciu struktury tabeli pokazanej poniżej, aplikacja kliencka może efektywnie pobrać konkretną jednostkę pracownika przy użyciu nazwy działu i identyfikatora pracownika ( **PartitionKey** i **RowKey**).  

![Jednostka Employee](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Jeśli chcesz również uzyskać listę jednostek pracowników na podstawie wartości innej nieunikatowej właściwości, takiej jak jej nazwisko, musisz użyć mniej wydajnego skanowania partycji, aby znaleźć dopasowania zamiast używać indeksu do bezpośredniego wyszukiwania. Wynika to z faktu, że usługa Table Service nie udostępnia indeksów pomocniczych.  

### <a name="solution"></a>Rozwiązanie
Aby włączyć wyszukiwanie według nazwiska przy użyciu struktury jednostki pokazanej powyżej, należy zachować listy identyfikatorów pracowników. Jeśli chcesz pobrać jednostki pracownika z określoną nazwiskiem, np. Nowak, należy najpierw znaleźć listę identyfikatorów pracowników dla pracowników, którzy mają nazwisko Kowalski, a następnie pobrać te jednostki pracowników. Istnieją trzy główne opcje przechowywania list identyfikatorów pracowników:  

* Użyj magazynu obiektów BLOB.  
* Utwórz jednostki indeksu w tej samej partycji co jednostki pracownika.  
* Utwórz jednostki indeksu w oddzielnej partycji lub tabeli.  

<u>Opcja #1: Użyj magazynu obiektów BLOB</u>  

Dla pierwszej opcji utworzysz obiekt BLOB dla każdego unikatowej nazwy, a w każdym z nich znajduje się lista wartości **PartitionKey** (dział) i **RowKey** (identyfikator pracownika) dla pracowników, którzy mają tę ostatnią nazwę. Po dodaniu lub usunięciu pracownika należy upewnić się, że zawartość odpowiedniego obiektu BLOB jest ostatecznie spójna z jednostkami pracowników.  

<u>#2 opcji:</u> Utwórz jednostki indeksu w tej samej partycji  

Dla drugiej opcji Użyj obiektów index, które przechowują następujące dane:  

![Jednostka indeksu pracownika](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

Właściwość **idpracownikas** zawiera listę identyfikatorów pracowników dla pracowników, których nazwisko jest przechowywane w **RowKey**.  

Poniższe kroki przedstawiają proces, który należy wykonać, gdy dodajesz nowego pracownika, jeśli używasz drugiej opcji. W tym przykładzie dodajemy pracownika o IDENTYFIKATORze 000152 i nazwisko Nowak w dziale sprzedaży:  

1. Pobierz jednostkę indeksu z wartością **PartitionKey** "Sales" i wartością **RowKey** "Nowak". Zapisz element ETag tej jednostki do użycia w kroku 2.  
2. Utwórz transakcję grupy jednostek (czyli operację wsadową), która wstawia nową jednostkę pracownika (**PartitionKey** wartość "Sales" i **RowKey** Value "000152"), a następnie aktualizuje jednostkę indeksu (wartość **PartitionKey** "Sales" i **RowKey** Value "Nowak"), dodając nowy identyfikator pracownika do listy w polu idpracownikas. Aby uzyskać więcej informacji o transakcjach grupy jednostek, zobacz transakcje grupy jednostek.  
3. Jeśli transakcja grupy jednostek nie powiedzie się z powodu optymistycznego błędu współbieżności (ktoś inny zmodyfikował jednostkę indeksu), należy ponownie zacząć od początku krok 1.  

Jeśli używasz drugiej opcji, możesz użyć podobnej metody do usuwania pracownika. Zmiana nazwiska pracownika jest nieco bardziej złożona, ponieważ należy wykonać transakcję grupy jednostek, która aktualizuje trzy jednostki: jednostki pracownika, jednostki indeksu dla starej nazwy i jednostki indeksu dla nowej nazwy. Każdą jednostkę należy pobrać przed wprowadzeniem jakichkolwiek zmian w celu pobrania wartości ETag, których można użyć do przeprowadzenia aktualizacji przy użyciu optymistycznej współbieżności.  

Poniższe kroki przedstawiają proces, który należy wykonać, gdy konieczne jest wyszukanie wszystkich pracowników z daną ostatnią nazwą w dziale, jeśli używasz drugiej opcji. W tym przykładzie szukamy wszystkich pracowników o nazwisku Nowak w dziale sprzedaży:  

1. Pobierz jednostkę indeksu z wartością **PartitionKey** "Sales" i wartością **RowKey** "Nowak".  
2. Przeanalizuj listę identyfikatorów pracowników w polu Idpracownikas.  
3. Jeśli potrzebujesz dodatkowych informacji na temat każdego z tych pracowników (takich jak adresy e-mail), Pobierz każdą jednostkę pracownika przy użyciu wartości **PartitionKey** "Sales" i wartości **RowKey** z listy pracowników uzyskanych w kroku 2.  

<u>#3 opcji:</u> Tworzenie jednostek indeksu w oddzielnej partycji lub tabeli  

Dla trzeciej opcji Użyj obiektów index, które przechowują następujące dane:  

![Jednostka indeksu pracownika w oddzielnej partycji](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


Właściwość **idpracownikas** zawiera listę identyfikatorów pracowników dla pracowników, których nazwisko jest przechowywane w **RowKey**.  

Z trzecią opcją nie można używać EGTs, aby zachować spójność, ponieważ jednostki indeksu znajdują się w oddzielnej partycji od jednostek pracowników. Upewnij się, że jednostki indeksu są ostatecznie spójne z jednostkami pracowników.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* To rozwiązanie wymaga co najmniej dwóch zapytań, aby pobrać pasujące jednostki: jeden do zapytania o jednostki indeksu w celu uzyskania listy wartości **RowKey** , a następnie wysyła zapytanie w celu pobrania każdej jednostki na liście.  
* Mając na względzie, że indywidualna jednostka ma maksymalny rozmiar 1 MB, opcja #2 i #3 opcji w rozwiązaniu założono, że lista identyfikatorów pracowników dla danego nazwiska nie jest nigdy większa niż 1 MB. Jeśli lista identyfikatorów pracowników może mieć rozmiar większy niż 1 MB, użyj opcji #1 i Zapisz dane indeksu w usłudze BLOB Storage.  
* Jeśli używasz opcji #2 (za pomocą EGTs, aby obsługiwać Dodawanie i usuwanie pracowników oraz zmieniać nazwisko pracownika), należy sprawdzić, czy liczba transakcji zbliża się do ograniczeń skalowalności w danej partycji. W takim przypadku należy wziąć pod uwagę ostatecznie spójne rozwiązanie (opcja #1 lub #3 opcji), które korzysta z kolejek do obsługi żądań aktualizacji i umożliwia przechowywanie jednostek indeksu w oddzielnej partycji od jednostek pracowników.  
* Opcja #2 w tym rozwiązaniu założono, że chcesz wyszukać według nazwiska w ramach działu: na przykład chcesz pobrać listę pracowników o nazwisku Kowalski w dziale sprzedaży. Jeśli chcesz mieć możliwość wyszukania wszystkich pracowników o nazwisku Nowak w całej organizacji, użyj opcji #1 lub #3 opcji.
* Możesz zaimplementować rozwiązanie oparte na kolejce, które zapewnia spójność ostateczną (Aby uzyskać więcej informacji, zobacz [wzorzec spójnych transakcji](#eventually-consistent-transactions-pattern) ).  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz wyszukać zestaw jednostek, które współużytkują wspólną wartość właściwości, na przykład wszystkich pracowników o nazwisku Nowak.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* Transakcje grupy jednostek  
* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Wzorzec denormalizacji
Połącz powiązane dane razem w pojedynczej jednostce, aby umożliwić pobieranie wszystkich potrzebnych danych przy użyciu zapytania pojedynczego punktu.  

### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych zazwyczaj normalizuje dane w celu usunięcia duplikatów, co powoduje, że zapytania pobierające dane z wielu tabel. Jeśli normalizuje dane w tabelach platformy Azure, musisz wykonać wiele rund z klienta do serwera, aby pobrać powiązane dane. Na przykład w przypadku struktury tabeli pokazanej poniżej potrzebne są dwie rundy, aby pobrać szczegóły działu: jeden do pobrania jednostki działu, która zawiera identyfikator Menedżera, a następnie inne żądanie pobrania szczegółów kierownika w jednostce pracownika.  

![Jednostka działu i jednostka Employee](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Rozwiązanie
Zamiast przechowywania danych w dwóch osobnych jednostkach, należy deznormalizować dane i zachować kopię szczegółów kierownika w jednostce działu. Na przykład:  

![Jednostka działu](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Za pomocą jednostek działu przechowywanych z tymi właściwościami można teraz pobrać wszystkie szczegóły potrzebne do działu przy użyciu zapytania punkt.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Istnieje kilka kosztów związanych z magazynowaniem danych. Korzyść wydajności (wynikająca z mniejszej liczby żądań do usługi magazynu) zazwyczaj zmniejsza krańcowy wzrost kosztów magazynowania (a koszt jest częściowo przesunięty o zmniejszenie liczby transakcji wymaganych do pobrania szczegółów działu).  
* Należy zachować spójność dwóch jednostek, które przechowują informacje o menedżerach. Problem ze spójnością można obsłużyć, korzystając z EGTs do aktualizowania wielu jednostek w jednej transakcji niepodzielnej: w tym przypadku jednostka działu i Jednostka pracownika dla menedżera działu są przechowywane w tej samej partycji.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy często potrzebujesz wyszukać powiązane informacje. Ten wzorzec zmniejsza liczbę zapytań, które klient musi wykonać, aby pobrać wymagane dane.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* Transakcje grupy jednostek  
* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Wzorzec klucza złożonego
Użyj złożonych wartości **RowKey** , aby umożliwić klientowi wyszukiwanie powiązanych danych za pomocą pojedynczego punktu zapytania.  

### <a name="context-and-problem"></a>Kontekst i problem
W relacyjnej bazie danych można używać sprzężeń w zapytaniach, aby zwrócić powiązane fragmenty danych do klienta w jednym zapytaniu. Na przykład możesz użyć identyfikatora pracownika, aby wyszukać listę powiązanych jednostek, które zawierają dane dotyczące wydajności i przeglądu dla tego pracownika.  

Załóżmy, że w Table service są przechowywane jednostki pracowników, korzystając z następującej struktury:  

![Zrzut ekranu pokazujący, jak można przechowywać jednostki pracowników w Table service.](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Musisz również przechowywać dane historyczne dotyczące przeglądów i wydajności dla każdego roku, dla których pracownik pracował w organizacji, i musisz mieć dostęp do tych informacji według roku. Jedną z opcji jest utworzenie innej tabeli, która przechowuje jednostki o następującej strukturze:  

![Alternatywna Struktura jednostek pracowników](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Należy zauważyć, że w tym podejściu można zdecydować się na zduplikowanie niektórych informacji (takich jak imię i nazwisko) w nowej jednostce, aby umożliwić pobieranie danych za pomocą pojedynczego żądania. Nie można jednak zachować silnej spójności, ponieważ nie można użyć EGT do aktualizowania dwóch jednostek.  

### <a name="solution"></a>Rozwiązanie
Zapisz nowy typ jednostki w oryginalnej tabeli przy użyciu jednostek o następującej strukturze:  

![Rozwiązanie dla struktury jednostki pracownika](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Zwróć uwagę na to, jak **RowKey** jest teraz kluczem złożonym składającym się z identyfikatora pracownika i roku danych przeglądu, który umożliwia pobieranie wydajności pracownika i przeglądanie danych za pomocą pojedynczego żądania dla jednej jednostki.  

Poniższy przykład przedstawia, w jaki sposób można pobrać wszystkie dane dotyczące przeglądu określonego pracownika (na przykład Employee 000123 w dziale sprzedaży):  

$filter = (PartitionKey EQ "Sales") i (RowKey GE "empid_000123") i (RowKey lt "000123_2012") &$select = RowKey, klasyfikacji Menedżera, klasyfikacji równorzędnej, komentarzy  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Należy użyć odpowiedniego znaku separatora, który ułatwia przeanalizowanie wartości **RowKey** : na przykład **000123_2012**.  
* Ta jednostka jest również przechowywana w tej samej partycji co inne jednostki, które zawierają powiązane dane dla tego samego pracownika, co oznacza, że można użyć EGTs do utrzymania silnej spójności.
* Należy zastanowić się, jak często będą wykonywane zapytania o dane, aby określić, czy ten wzorzec jest odpowiedni.  Na przykład, jeśli będziesz uzyskiwać dostęp do danych przeglądu rzadko, a główne dane pracowników często powinny być przechowywane jako osobne jednostki.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Tego wzorca należy używać, gdy konieczne jest przechowywanie co najmniej jednej powiązanej jednostki.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek  
* [Praca z heterogenicznymi typami jednostek](#working-with-heterogeneous-entity-types)  
* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Wzorzec końca dziennika
Pobierz *n* obiektów ostatnio dodanych do partycji przy użyciu wartości **RowKey** , która jest sortowana w odwrotnej kolejności daty i godziny.  

### <a name="context-and-problem"></a>Kontekst i problem
Typowym wymaganiem jest możliwość pobrania ostatnio utworzonych jednostek, na przykład 10 ostatnich oświadczeń wydatków przesłanych przez pracownika. Zapytania tabeli obsługują **$Top** operacji zapytania, aby zwrócić pierwsze *n* jednostek z zestawu: nie istnieje równoważna operacja zapytania, która nie zwraca ostatnich n jednostek w zestawie.  

### <a name="solution"></a>Rozwiązanie
Przechowuj jednostki przy użyciu **RowKey** , który w sposób naturalny sortuje w odwrotnej kolejności daty/godziny przy użyciu, tak aby najnowszy wpis był zawsze pierwszym z nich w tabeli.  

Na przykład, aby można było pobrać 10 ostatnich oświadczeń wydatków przesłanych przez pracownika, można użyć odwrotnej wartości osi pochodnej od bieżącej daty/godziny. Poniższy przykładowy kod w języku C# przedstawia jeden ze sposobów tworzenia odpowiednich "odwróconych" znaczników dla **RowKey** , które są sortowane od najnowszych do najstarszych:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Można wrócić do wartości daty i godziny przy użyciu następującego kodu:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Zapytanie tabeli wygląda następująco:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zapewnić, że wartość ciągu będzie sortowana zgodnie z oczekiwaniami, należy uzupełnić wartość odwrotnej osi.  
* Należy pamiętać o obiektach docelowych skalowalności na poziomie partycji. Należy zachować ostrożność, aby nie tworzyć partycji punktu aktywnego.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy musisz uzyskać dostęp do jednostek w odwrotnej kolejności daty/godziny lub gdy potrzebujesz dostępu do ostatnio dodanych jednostek.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Poprzedź/Dołącz Antywzorzec](#prepend-append-anti-pattern)  
* [Pobieranie jednostek](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Wzorzec usuwania dużych ilości
Włącz usuwanie dużej liczby jednostek przez przechowywanie wszystkich jednostek do jednoczesnego usuwania w ich własnej osobnej tabeli; obiekty można usunąć, usuwając tabelę.  

### <a name="context-and-problem"></a>Kontekst i problem
Wiele aplikacji usuwa stare dane, które nie muszą już być dostępne dla aplikacji klienckiej, lub że aplikacja została zarchiwizowana na innym nośniku magazynowania. Dane te są zwykle identyfikowane według daty: na przykład wymaga się usunięcia rekordów wszystkich żądań logowania, które są starsze niż 60 dni.  

Jednym z możliwych wzorów jest użycie daty i godziny żądania logowania w **RowKey**:  

![Data i godzina próby zalogowania](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

To podejście pozwala uniknąć hotspotów partycji, ponieważ aplikacja może wstawiać i usuwać jednostki logowania dla każdego użytkownika w oddzielnej partycji. Jednak takie podejście może być kosztowne i czasochłonne, jeśli masz dużą liczbę jednostek, ponieważ najpierw należy przeprowadzić skanowanie tabeli w celu zidentyfikowania wszystkich jednostek do usunięcia, a następnie usunąć każdą starą jednostkę. Można zmniejszyć liczbę operacji okrężnych do serwera wymaganego do usunięcia starych jednostek przez przetwarzanie wsadowe wielu żądań Delete do EGTs.  

### <a name="solution"></a>Rozwiązanie
Użyj oddzielnej tabeli dla każdego dnia prób logowania. Możesz użyć powyższego projektu jednostki, aby uniknąć hotspotów podczas wstawiania jednostek, a usunięcie starych jednostek jest teraz jednym z pytań dotyczących usuwania jednej tabeli codziennie (pojedynczej operacji magazynu) zamiast znajdowania i usuwania setek i tysięcy poszczególnych jednostek logowania każdego dnia.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy projekt obsługuje inne sposoby używania danych przez aplikację, takich jak wyszukiwanie konkretnych jednostek, łączenie z innymi danymi lub generowanie zagregowanych informacji?  
* Czy projekt nie pozwala na aktywne plamy podczas wstawiania nowych jednostek?  
* Należy spodziewać się opóźnienia, jeśli chcesz użyć tej samej nazwy tabeli po jej usunięciu. Lepiej jest zawsze używać unikatowych nazw tabel.  
* Należy oczekiwać pewnej ograniczania przy pierwszym użyciu nowej tabeli, podczas gdy Table service nauczy wzorców dostępu i dystrybuuje partycje między węzłami. Należy zastanowić się, jak często należy utworzyć nowe tabele.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Tego wzorca należy używać w przypadku dużej ilości jednostek, które należy usunąć w tym samym czasie.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek
* [Modyfikowanie jednostek](#modifying-entities)  

## <a name="data-series-pattern"></a>Wzorzec serii danych
Przechowuj kompletne serie danych w jednej jednostce, aby zminimalizować liczbę żądań, które należy wykonać.  

### <a name="context-and-problem"></a>Kontekst i problem
Typowy scenariusz polega na tym, że aplikacja przechowuje serię danych, które zazwyczaj muszą zostać pobrane jednocześnie. Na przykład aplikacja może rejestrować liczbę wiadomości BŁYSKAWICZNych wysyłanych przez każdego pracownika co godzinę, a następnie użyć tych informacji do wykreślania liczby komunikatów wysyłanych przez każdego użytkownika w ciągu ostatnich 24 godzin. Jednym z projektów może być przechowywanie 24 jednostek dla każdego pracownika:  

![Przechowuj 24 jednostki dla każdego pracownika](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Dzięki temu projektowi można łatwo zlokalizować i zaktualizować jednostkę do aktualizacji dla każdego pracownika, gdy aplikacja musi zaktualizować wartość liczby komunikatów. Aby jednak pobrać informacje w celu wykreślenia wykresu aktywności przez poprzednie 24 godziny, należy pobrać 24 jednostki.  

### <a name="solution"></a>Rozwiązanie
Użyj następującego projektu z osobną właściwością do przechowywania liczby komunikatów dla każdej godziny:  

![Jednostka statystyk komunikatów](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

W tym projekcie możesz użyć operacji scalania, aby zaktualizować liczbę komunikatów dla pracownika przez określoną godzinę. Teraz można pobrać wszystkie informacje potrzebne do wykreślenia wykresu przy użyciu żądania dla pojedynczej jednostki.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Jeśli kompletna seria danych nie mieści się w pojedynczej jednostce (jednostka może mieć do 252 właściwości), użyj alternatywnego magazynu danych, takiego jak obiekt BLOB.  
* Jeśli wielu klientów jednocześnie aktualizuje jednostkę, należy użyć elementu **ETag** do zaimplementowania optymistycznej współbieżności. Jeśli masz wielu klientów, może wystąpić wysoka rywalizacja.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy musisz zaktualizować i pobrać serie danych skojarzone z konkretną jednostką.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec dużych jednostek](#large-entities-pattern)  
* [Scal lub Zamień](#merge-or-replace)  
* [Wzorzec z ostatecznie spójnymi transakcjami](#eventually-consistent-transactions-pattern) (Jeśli przechowujesz serie danych w obiekcie BLOB)  

## <a name="wide-entities-pattern"></a>Wzorzec szerokiej jednostki
Użyj wielu jednostek fizycznych do przechowywania jednostek logicznych z więcej niż 252 właściwościami.  

### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka może mieć nie więcej niż 252 właściwości (z wyłączeniem obowiązkowych właściwości systemu) i nie może zawierać więcej niż 1 MB danych. W relacyjnej bazie danych zwykle uzyskuje się zaokrąglanie wszelkich limitów rozmiaru wiersza przez dodanie nowej tabeli i wymuszenie relacji 1-do-jednego między nimi.  

### <a name="solution"></a>Rozwiązanie
Za pomocą Table service można przechowywać wiele jednostek do reprezentowania pojedynczego dużego obiektu biznesowego, który ma więcej niż 252 właściwości. Na przykład jeśli chcesz przechowywać liczbę wiadomości BŁYSKAWICZNych wysyłanych przez każdego pracownika przez ostatnie 365 dni, możesz użyć następującego projektu, który używa dwóch jednostek z różnymi schematami:  

![Wiele jednostek](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Jeśli musisz wprowadzić zmianę, która wymaga aktualizacji obu jednostek, aby zachować ich synchronizację ze sobą, możesz użyć EGT. W przeciwnym razie można użyć pojedynczej operacji scalania, aby zaktualizować liczbę komunikatów w określonym dniu. Aby pobrać wszystkie dane dla danego pracownika, należy pobrać obie jednostki, które można wykonać przy użyciu dwóch wydajnych żądań, które używają zarówno **PartitionKey** , jak i wartości **RowKey** .  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Pobieranie kompletnej jednostki logicznej obejmuje co najmniej dwie transakcje magazynu: jeden do pobrania każdej jednostki fizycznej.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, gdy potrzebujesz przechowywać jednostki, których rozmiar lub liczba właściwości przekraczają limity dla poszczególnych jednostek w Table service.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* Transakcje grupy jednostek
* [Scal lub Zamień](#merge-or-replace)

## <a name="large-entities-pattern"></a>Wzorzec dużych jednostek
Przechowywanie dużych wartości właściwości przy użyciu magazynu obiektów BLOB.  

### <a name="context-and-problem"></a>Kontekst i problem
Pojedyncza jednostka nie może przechowywać więcej niż 1 MB danych. Jeśli jedna lub kilka właściwości magazynu wartości, które powodują, że całkowity rozmiar jednostki przekracza tę wartość, nie można zapisać całej jednostki w Table service.  

### <a name="solution"></a>Rozwiązanie
Jeśli rozmiar jednostki przekracza 1 MB, ponieważ co najmniej jedna z właściwości zawiera dużą ilość danych, można przechowywać dane w Blob service a następnie przechowywać adres obiektu BLOB we właściwości w jednostce. Na przykład można przechowywać zdjęcie pracownika w usłudze BLOB Storage i przechowywać link do zdjęcia we właściwości **Fotografia** jednostki pracownika:  

![Właściwość Zdjęcia](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Aby zachować spójność ostateczną między jednostką w Table service i danymi w Blob service, użyj [wzorca stałego spójnych transakcji](#eventually-consistent-transactions-pattern) do obsługi jednostek.
* Pobieranie kompletnej jednostki obejmuje co najmniej dwie transakcje magazynu: jeden do pobrania jednostki i jeden w celu pobrania danych obiektów BLOB.  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Użyj tego wzorca, jeśli chcesz przechowywać jednostki, których rozmiar przekracza limity dla pojedynczej jednostki w Table service.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec ostatecznie spójnych transakcji](#eventually-consistent-transactions-pattern)  
* [Wzorzec szerokiej jednostki](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Poprzedź/Dołącz Antywzorzec
Zwiększenie skalowalności w przypadku dużej ilości operacji wstawiania poprzez rozłożenie operacji wstawiania na wiele partycji.  

### <a name="context-and-problem"></a>Kontekst i problem
Oczekiwanie na lub dołączenie jednostek do przechowywanych jednostek zwykle powoduje, że aplikacja dodaje nowe jednostki do pierwszej lub ostatniej partycji sekwencji partycji. W takim przypadku wszystkie operacje wstawiania w dowolnym momencie są wykonywane w tej samej partycji, co powoduje utworzenie hotspotu, który uniemożliwia użycie usługi tabeli w ramach równoważenia obciążenia w wielu węzłach, i może spowodować, że aplikacja osiągnie elementy docelowe skalowalności dla partycji. Na przykład jeśli masz aplikację, która rejestruje dostęp do sieci i zasobów przez pracowników, wówczas struktura jednostki, jak pokazano poniżej, może spowodować, że partycja bieżącej godziny staje się punktem aktywny, jeśli ilość transakcji osiągnie miejsce docelowe skalowalności dla pojedynczej partycji:  

![Struktura jednostki](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Rozwiązanie
Następująca alternatywna Struktura jednostek pozwala uniknąć hotspotu na określonej partycji, gdy aplikacja rejestruje zdarzenia:  

![Alternatywna Struktura jednostek](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Zwróć uwagę, że w tym przykładzie zarówno **PartitionKey** , jak i **RowKey** są kluczami złożonymi. **PartitionKey** używa zarówno identyfikatora działu, jak i pracownika do dystrybuowania rejestrowania między wieloma partycjami.  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas podejmowania decyzji o sposobie wdrożenia tego wzorca należy rozważyć następujące punkty:  

* Czy struktura klucza alternatywnego, która pozwala uniknąć tworzenia aktywnych partycji w przypadku wstawiania efektywnie obsługuje zapytania, które udostępnia aplikacja kliencka?  
* Czy Przewidywana ilość transakcji oznacza, że najkorzystniej osiągniesz cele skalowalności dla danej partycji i zostanie ona ograniczona przez usługę magazynu?  

### <a name="when-to-use-this-pattern"></a>Kiedy używać tego wzorca
Należy unikać dodawania lub dołączania antywzorców, gdy ilość transakcji prawdopodobnie spowoduje ograniczenie przepustowości przez usługę magazynu podczas uzyskiwania dostępu do partycji aktywnej.  

### <a name="related-patterns-and-guidance"></a>Powiązane wzorce i wskazówki
Podczas implementowania tego wzorca mogą być istotne następujące wzorce i wskazówki:  

* [Wzorzec klucza złożonego](#compound-key-pattern)  
* [Wzorzec końca dziennika](#log-tail-pattern)  
* [Modyfikowanie jednostek](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Antywzorzec danych dziennika
Zazwyczaj należy używać Blob service zamiast Table service do przechowywania danych dziennika.  

### <a name="context-and-problem"></a>Kontekst i problem
Typowym przypadkiem użycia danych dziennika jest pobranie wybranych wpisów dziennika dla określonego zakresu dat/godzin: na przykład, chcesz znaleźć wszystkie komunikaty o błędach i krytyczne, które zostały zarejestrowane przez aplikację między 15:04 i 15:06 w określonym dniu. Nie chcesz używać daty i godziny komunikatu dziennika, aby określić partycję, do której zapisywane są jednostki dziennika: spowoduje to, że w danym momencie wszystkie jednostki dziennika będą współużytkować tę samą wartość **PartitionKey** (zobacz sekcję [poprzedź/dołączanie antywzorców](#prepend-append-anti-pattern)). Na przykład poniższy schemat jednostki dla komunikatu dziennika tworzy partycję gorącą, ponieważ aplikacja zapisuje wszystkie komunikaty dziennika na partycji dla bieżącej daty i godziny:  

![Jednostka komunikatu dziennika](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

W tym przykładzie **RowKey** obejmuje datę i godzinę komunikatu dziennika, aby upewnić się, że komunikaty dziennika są przechowywane w kolejności daty/godziny i zawiera identyfikator komunikatu w przypadku, gdy wiele komunikatów dziennika ma taką samą datę i godzinę.  

Innym podejściem jest użycie **PartitionKey** , która zapewnia, że aplikacja zapisuje komunikaty w różnych partycjach. Na przykład, jeśli źródło komunikatu dziennika zapewnia sposób dystrybucji komunikatów w wielu partycjach, można użyć następującego schematu jednostki:  

![Alternatywna jednostka komunikatu dziennika](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Jednak problem z tym schematem polega na tym, że pobranie wszystkich komunikatów dziennika dla określonego przedziału czasu wymaga przeszukania każdej partycji w tabeli.

### <a name="solution"></a>Rozwiązanie
W poprzedniej sekcji został wyróżniony problem polegający na tym, że próba użycia Table service do przechowywania wpisów dziennika i sugerowanych dwóch, niezadowalających projektów. Jedno rozwiązanie prowadzi do gorącej partycji z ryzykiem, w którym są zapisywane komunikaty dziennika zapisu wydajności. inne rozwiązanie spowodowało niską wydajność zapytań, ponieważ wymaga skanowania każdej partycji w tabeli w celu pobrania komunikatów dziennika dla określonego przedziału czasu. Magazyn obiektów BLOB oferuje lepsze rozwiązanie tego typu, dlatego w Azure Storage Analytics są przechowywane zbierane dane dziennika.  

W tej sekcji opisano sposób, w jaki analityka magazynu przechowuje dane dziennika w usłudze BLOB Storage jako ilustrację tego podejścia do przechowywania danych, które są zwykle wykonywane przez wiele zapytań.  

Analityka magazynu przechowuje komunikaty dzienników w formacie rozdzielanym w wielu obiektach Blob. Format rozdzielany ułatwia aplikacji klienckiej analizowanie danych w komunikacie dziennika.  

Analityka magazynu używa konwencji nazewnictwa obiektów blob, które umożliwiają znalezienie obiektów BLOB (lub obiektów BLOB) zawierających komunikaty dziennika, dla których odbywa się wyszukiwanie. Na przykład obiekt BLOB o nazwie "queue/2014/07/31/1800/000001. log" zawiera komunikaty dziennika, które odnoszą się do usługi kolejki przez godzinę, rozpoczynając od 18:00 31 lipca 2014. "000001" wskazuje, że jest to pierwszy plik dziennika dla tego okresu. Analityka magazynu rejestruje także sygnatury czasowe pierwszych i ostatnich komunikatów dziennika przechowywanych w pliku w ramach metadanych obiektu BLOB. Interfejs API usługi BLOB Storage umożliwia lokalizowanie obiektów BLOB w kontenerze na podstawie prefiksu nazwy: aby zlokalizować wszystkie obiekty blob zawierające dane dziennika kolejki dla godziny rozpoczynającej się o 18:00, można użyć prefiksu "queue/2014/07/31/1800".  

Analityka magazynu buforuje komunikaty dzienników wewnętrznie, a następnie okresowo aktualizuje odpowiedni obiekt BLOB lub tworzy nowy z najnowszymi partiami wpisów dziennika. Pozwala to zmniejszyć liczbę zapisów, które muszą zostać wykonane w usłudze BLOB Service.  

Jeśli wdrażasz podobne rozwiązanie w swojej aplikacji, musisz wziąć pod uwagę sposób zarządzania wymianą zapasową między niezawodnością (zapisanie każdego wpisu dziennika do magazynu obiektów BLOB) i obniżeniem kosztów i skalowalności (buforowanie aktualizacji w aplikacji i zapisywanie ich w magazynie obiektów BLOB w partiach).  

### <a name="issues-and-considerations"></a>Problemy i kwestie do rozważenia
Podczas decydowania o sposobie przechowywania danych dzienników należy wziąć pod uwagę następujące kwestie:  

* W przypadku utworzenia projektu tabeli, który pozwala uniknąć potencjalnych partycji, może się okazać, że nie można wydajnie uzyskać dostępu do danych dziennika.  
* Aby przetwarzać dane dziennika, klient często musi załadować wiele rekordów.  
* Mimo że dane dziennika są często strukturalne, usługa BLOB Storage może być lepszym rozwiązaniem.  

## <a name="implementation-considerations"></a>Istotne informacje dotyczące implementacji
W tej sekcji omówiono niektóre zagadnienia, które należy wziąć pod uwagę podczas implementowania wzorców opisanych w poprzednich sekcjach. W większości tej sekcji są używane przykłady napisane w języku C#, które używają biblioteki klienta magazynu (wersja 4.3.0 w trakcie pisania).  

## <a name="retrieving-entities"></a>Pobieranie jednostek
Jak opisano w temacie projekt sekcji dotyczącej wykonywania zapytań, najbardziej wydajnym zapytaniem jest zapytanie punktowe. Jednak w niektórych scenariuszach może być konieczne pobranie wielu jednostek. W tej sekcji opisano niektóre typowe podejścia do pobierania jednostek przy użyciu biblioteki klienta usługi Storage.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Wykonywanie zapytania punktu przy użyciu biblioteki klienta usługi Storage
Najprostszym sposobem wykonania zapytania punktu jest użycie operacji **Pobierz** tabelę, jak pokazano w poniższym fragmencie kodu w języku C#, który pobiera jednostkę z **PartitionKey** wartości "Sales" i **RowKey** wartość "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Zwróć uwagę, jak w tym przykładzie oczekiwana jest jednostka pobierana z typu **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Pobieranie wielu jednostek przy użyciu LINQ
Za pomocą LINQ można pobrać wiele jednostek z Table service podczas pracy z biblioteką standardowa tabeli Microsoft Azure Cosmos. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Aby poniższe przykłady działały, należy uwzględnić przestrzenie nazw:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

Element Employees jest obiektem w chmurze, który implementuje \<ITableEntity> metodę metody @ Query (), która zwraca TableQuery \<ITableEntity> . Obiekty tego typu implementują interfejs IQueryable i zezwalają na używanie zarówno wyrażeń zapytania LINQ, jak i składni notacji z kropką.

Pobieranie wielu jednostek i osiąganie przez określenie zapytania z klauzulą **WHERE** . Aby uniknąć skanowania tabeli, należy zawsze uwzględnić wartość **PartitionKey** w klauzuli WHERE, a jeśli to możliwe, wartość **RowKey** w celu uniknięcia skanowania tabeli i partycji. Usługa Table Service obsługuje ograniczony zbiór operatorów porównania (większe niż, większe niż lub równe, mniejsze niż, mniejsze niż lub równe, równe i nierówne) do użycia w klauzuli WHERE. 

Poniższy fragment kodu w języku C# znajduje wszystkich pracowników, których nazwisko zaczyna się od "B" (przy założeniu, że **RowKey** przechowuje nazwisko) w dziale sprzedaży (przy założeniu, że **PartitionKey** przechowuje nazwę działu):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Zwróć uwagę, jak zapytanie określa zarówno **RowKey** , jak i **PartitionKey** , aby zapewnić lepszą wydajność.  

Poniższy przykład kodu przedstawia równoważne funkcje bez użycia składni LINQ:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Przykład zagnieżdża wiele metod **CombineFilters** , aby uwzględnić trzy warunki filtru.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Pobieranie dużej liczby jednostek z zapytania
Optymalne zapytanie zwraca indywidualną jednostkę na podstawie wartości **PartitionKey** i wartości **RowKey** . Jednak w niektórych scenariuszach może istnieć wymóg zwrócenia wielu jednostek z tej samej partycji lub nawet z wielu partycji.  

Należy zawsze w pełni przetestować wydajność aplikacji w takich scenariuszach.  

Zapytanie dotyczące usługi Table Service może zwrócić maksymalnie 1 000 jednostek jednocześnie i może być wykonywane przez maksymalnie pięć sekund. Jeśli zestaw wyników zawiera więcej niż 1 000 jednostek, jeśli zapytanie nie zostało ukończone w ciągu pięciu sekund lub jeśli zapytanie przecina granicę partycji, Table service zwraca token kontynuacji, aby umożliwić aplikacji klienckiej żądanie następnego zestawu jednostek. Aby uzyskać więcej informacji na temat działania tokenów kontynuacji, zobacz [limit czasu zapytania i podział na strony](/rest/api/storageservices/Query-Timeout-and-Pagination).  

Jeśli używasz biblioteki klienta usługi Storage, może ona automatycznie obsługiwać tokeny kontynuacji, ponieważ zwraca jednostki z Table service. Następujący przykładowy kod w języku C# przy użyciu biblioteki klienta usługi Storage automatycznie obsługuje tokeny kontynuacji, jeśli usługa tabeli zwróci je w odpowiedzi:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

Poniższy kod w języku C# obsługuje jawnie tokeny kontynuacji:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Używając jawnie tokenów kontynuacji, można kontrolować, kiedy aplikacja pobiera następny segment danych. Na przykład, jeśli aplikacja kliencka umożliwia użytkownikom dostęp do stron za pomocą jednostek przechowywanych w tabeli, użytkownik może zrezygnować ze strony przez wszystkie jednostki pobrane przez zapytanie, tak aby aplikacja korzystała z tokenu kontynuacji do pobierania następnego segmentu, gdy użytkownik zakończył stronicowanie przez wszystkie jednostki w bieżącym segmencie. Takie podejście ma kilka zalet:  

* Umożliwia ograniczenie ilości danych do pobrania z Table service i przechodzenie przez sieć.  
* Umożliwia wykonywanie asynchronicznych operacji we/wy na platformie .NET.  
* Umożliwia ona Serializowanie tokenu kontynuacji do magazynu trwałego, dzięki czemu można kontynuować w przypadku awarii aplikacji.  

> [!NOTE]
> Token kontynuacji zwykle zwraca segment zawierający 1 000 jednostek, chociaż może być mniej. Jest to również w przypadku ograniczenia liczby wpisów zwracanych przez zapytanie przy użyciu funkcji **Take** , aby zwrócić pierwsze n jednostek, które pasują do kryteriów wyszukiwania: usługa Table może zwrócić segment zawierający mniej niż n jednostek wraz z tokenem kontynuacji, aby umożliwić pobranie pozostałych jednostek.  
> 
> 

Poniższy kod w języku C# pokazuje, jak zmodyfikować liczbę jednostek zwróconych wewnątrz segmentu:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projekcja po stronie serwera
Jedna jednostka może mieć do 255 właściwości i mieć rozmiar maksymalnie 1 MB. Podczas wykonywania zapytania dotyczącego tabeli i pobierania jednostek mogą nie być potrzebne wszystkie właściwości i mogą uniknąć niepotrzebnych transferów danych (aby pomóc w zmniejszeniu opóźnień i kosztów). Możesz użyć projekcji po stronie serwera, aby przetransferować tylko potrzebne właściwości. W poniższym przykładzie jest pobierana tylko właściwość **poczty e-mail** (wraz **z PartitionKey**, **RowKey**, **timestamp** i **ETag**) z jednostek wybranych przez zapytanie.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Zauważ, że wartość **RowKey** jest dostępna, nawet jeśli nie została uwzględniona na liście właściwości do pobrania.  

## <a name="modifying-entities"></a>Modyfikowanie jednostek
Biblioteka klienta usługi Storage umożliwia modyfikowanie jednostek przechowywanych w usłudze Table Service przez wstawianie, usuwanie i aktualizowanie jednostek. Za pomocą EGTs można wsadowo wiele operacji wstawiania, aktualizacji i usuwania, aby zmniejszyć liczbę wymaganych podróży i zwiększyć wydajność rozwiązania.  

Wyjątki zgłaszane, gdy biblioteka klienta magazynu wykonuje EGT zazwyczaj zawiera indeks jednostki, która spowodowała niepowodzenie wykonywania partii. Jest to przydatne w przypadku debugowania kodu korzystającego z EGTs.  

Należy również rozważyć, jak projekt ma wpływ na sposób, w jaki Twoja aplikacja kliencka obsługuje operacje współbieżności i aktualizacji.  

### <a name="managing-concurrency"></a>Zarządzanie współbieżnością
Domyślnie usługa Table Service implementuje optymistyczne kontrole współbieżności na poziomie poszczególnych jednostek dla operacji **wstawiania**, **scalania** i **usuwania** , chociaż istnieje możliwość, aby klient wymusił ominięcie tych sprawdzeń przez klienta. Aby uzyskać więcej informacji o tym, jak usługa Table zarządza współbieżnością, zobacz  [Zarządzanie współbieżnością w Microsoft Azure Storage](../blobs/concurrency-manage.md).  

### <a name="merge-or-replace"></a>Scal lub Zamień
Metoda **replace** klasy **TableOperation** zawsze zastępuje kompletną jednostkę w Table Service. Jeśli nie dołączysz właściwości w żądaniu, gdy ta właściwość istnieje w przechowywanej jednostce, żądanie usunie tę właściwość z przechowywanej jednostki. Jeśli nie chcesz usunąć właściwości jawnie z przechowywanej jednostki, musisz dołączyć każdą właściwość w żądaniu.  

Można użyć metody **merge** klasy **TableOperation** , aby zmniejszyć ilość danych wysyłanych do Table Service, gdy chcesz zaktualizować jednostkę. Metoda **merge** zastępuje wszystkie właściwości w przechowywanej jednostce wartościami właściwości z jednostki zawartej w żądaniu, ale pozostawia nienaruszone właściwości w jednostce przechowywanej, które nie są uwzględnione w żądaniu. Jest to przydatne, jeśli masz duże jednostki i potrzebujesz jedynie zaktualizować niewielką liczbę właściwości w żądaniu.  

> [!NOTE]
> Metody **replace** i **merge** kończą się niepowodzeniem, jeśli jednostka nie istnieje. Alternatywnie można użyć metod **InsertOrReplace** i **InsertOrMerge** , które tworzą nową jednostkę, jeśli nie istnieje.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Praca z heterogenicznymi typami jednostek
Table service to magazyn tabel bez *schematu* , który oznacza, że pojedyncza tabela może przechowywać jednostki wielu typów zapewniające dużą elastyczność w projekcie. Poniższy przykład ilustruje tabelę przechowującą jednostki pracownika i działu:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Każda jednostka musi nadal mieć wartości **PartitionKey**, **RowKey** i **timestamp** , ale może mieć dowolny zestaw właściwości. Ponadto nie ma nic do wskazania typu jednostki, chyba że użytkownik zdecyduje się przechowywać te informacje w innym miejscu. Dostępne są dwie opcje identyfikacji typu jednostki:  

* Dołącz typ jednostki do **RowKey** (lub prawdopodobnie **PartitionKey**). Na przykład **EMPLOYEE_000123** lub **DEPARTMENT_SALES** jako wartości **RowKey** .  
* Użyj oddzielnej właściwości, aby zarejestrować typ jednostki, jak pokazano w poniższej tabeli.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ obiektu</th>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td>Pracownik</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ obiektu</th>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td>Pracownik</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ obiektu</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Dział</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Typ obiektu</th>
<th>FirstName (Imię)</th>
<th>LastName (Nazwisko)</th>
<th>Wiek</th>
<th>E-mail</th>
</tr>
<tr>
<td>Pracownik</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Pierwsza opcja, w zależności od typu obiektu do **RowKey**, jest przydatna, jeśli istnieje możliwość, że dwie jednostki różnych typów mogą mieć taką samą wartość klucza. Grupuje również jednostki tego samego typu razem w partycji.  

Techniki omówione w tej sekcji znajdują się szczególnie w odniesieniu do [relacji dziedziczenia](table-storage-design-modeling.md#inheritance-relationships) dyskusji wcześniej w tym przewodniku w [relacji modelowania](table-storage-design-modeling.md)artykułu.  

> [!NOTE]
> Należy rozważyć uwzględnienie numeru wersji w wartości typ jednostki, aby umożliwić aplikacjom klienckim rozwijanie obiektów POCO i współpracują z różnymi wersjami.  
> 
> 

W pozostałej części tej sekcji opisano niektóre funkcje w bibliotece klienta magazynu, które ułatwiają pracę z wieloma typami jednostek w tej samej tabeli.  

### <a name="retrieving-heterogeneous-entity-types"></a>Pobieranie typów jednostek heterogenicznych
W przypadku korzystania z biblioteki klienta usługi Storage dostępne są trzy opcje pracy z wieloma typami jednostek.  

Jeśli znasz typ jednostki przechowywanej przy użyciu określonych wartości **RowKey** i **PartitionKey** , możesz określić typ jednostki podczas pobierania jednostki, jak pokazano w poprzednich dwóch przykładach, które pobierają jednostki typu **EmployeeEntity**: [wykonywanie zapytania punktu przy użyciu biblioteki klienta magazynu](#executing-a-point-query-using-the-storage-client-library) i [pobieranie wielu jednostek przy użyciu LINQ](#retrieving-multiple-entities-using-linq).  

Druga opcja to użycie typu **dynamictableentity lub** (zbiór właściwości) zamiast konkretnego typu jednostki poco (Ta opcja może również zwiększyć wydajność, ponieważ nie ma potrzeby serializacji i deserializacji jednostki do typów .NET). Poniższy kod w języku C# potencjalnie umożliwia pobranie wielu jednostek różnych typów z tabeli, ale zwraca wszystkie jednostki jako wystąpienia **dynamictableentity lub** . Następnie używa właściwości **EntityType** do określenia typu każdej jednostki:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
    
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
    EntityProperty entityTypeProperty;
    if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
    {
        if (entityTypeProperty.StringValue == "Employee")
        {
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Aby pobrać inne właściwości, należy użyć metody **TryGetValue** we właściwości **Właściwości** klasy **dynamictableentity lub** .  

Trzecia opcja polega na połączeniu przy użyciu typu **dynamictableentity lub** i wystąpienia **EntityResolver** . Pozwala to na rozpoznanie wielu typów POCO w tym samym zapytaniu. W tym przykładzie delegat **EntityResolver** używa właściwości **EntityType** do rozróżnienia między dwoma typami jednostek, które zwraca zapytanie. Metoda **Rozwiązuj** używa delegata programu **rozpoznawania nazw** do rozwiązywania wystąpień **dynamictableentity lub** z wystąpieniami **klasy tableentity** .  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{
    TableEntity resolvedEntity = null;
    if (props["EntityType"].StringValue == "Department")
    {
        resolvedEntity = new DepartmentEntity();
    }
    else if (props["EntityType"].StringValue == "Employee")
    {
        resolvedEntity = new EmployeeEntity();
    }
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Modyfikowanie typów jednostek heterogenicznych
Nie musisz znać typu jednostki, aby ją usunąć, i zawsze znasz typ jednostki podczas jej wstawiania. Można jednak użyć typu **dynamictableentity lub** do zaktualizowania jednostki bez znajomości jej typu i bez użycia klasy jednostki poco. Poniższy przykład kodu pobiera pojedynczą jednostkę i sprawdza, czy właściwość **EmployeeCount** istnieje przed jej aktualizacją.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Kontrolowanie dostępu za pomocą sygnatur dostępu współdzielonego
Można użyć tokenów sygnatury dostępu współdzielonego (SAS), aby umożliwić aplikacjom klienckim modyfikowanie (i tworzenie zapytań) jednostek tabeli bez konieczności uwzględniania klucza konta magazynu w kodzie. Zazwyczaj istnieją trzy główne korzyści związane z używaniem sygnatury dostępu współdzielonego w aplikacji:  

* Nie jest konieczne dystrybuowanie klucza konta magazynu do niezabezpieczonej platformy (na przykład urządzenia przenośnego) w celu umożliwienia temu urządzeniu dostępu do jednostek w Table service i modyfikowania ich.  
* Można odciążyć część pracy wykonywanej przez role sieci Web i proces roboczy w celu zarządzania jednostkami na urządzeniach klienckich, takich jak komputery użytkowników końcowych i urządzenia przenośne.  
* Do klienta można przypisać ograniczony i bezterminowy zestaw uprawnień (na przykład Zezwalanie na dostęp tylko do odczytu do określonych zasobów).  

Aby uzyskać więcej informacji na temat używania tokenów SAS z Table service, zobacz [using Shared Access Signatures (SAS)](../../storage/common/storage-sas-overview.md).  

Należy jednak nadal generować tokeny sygnatury dostępu współdzielonego, które przydzielą aplikację kliencką jednostkom w usłudze Table Service: należy to zrobić w środowisku, które ma bezpieczny dostęp do kluczy konta magazynu. Zwykle w celu wygenerowania tokenów SAS i dostarczenia ich do aplikacji klienckich, które wymagają dostępu do jednostek, należy użyć roli sieci Web lub procesu roboczego. Ze względu na to, że nadal występują obciążenia związane z generowaniem i dostarczaniem tokenów SAS klientom, należy wziąć pod uwagę, jak najlepiej zredukować ten koszt, szczególnie w scenariuszach o dużej pojemności.  

Istnieje możliwość wygenerowania tokenu SAS, który udziela dostępu do podzbioru jednostek w tabeli. Domyślnie można utworzyć token sygnatury dostępu współdzielonego dla całej tabeli, ale można również określić, że token SAS udziela dostępu do zakresu wartości **PartitionKey** lub zakresu wartości **PartitionKey** i **RowKey** . Można generować tokeny sygnatury dostępu współdzielonego dla poszczególnych użytkowników systemu w taki sposób, aby token SAS każdego użytkownika zezwalał na dostęp do ich własnych jednostek w usłudze Table Service.  

## <a name="asynchronous-and-parallel-operations"></a>Operacje asynchroniczne i równoległe
Pod warunkiem, że są rozłożone żądania na wielu partycjach, można zwiększyć przepływność i czas odpowiedzi klienta przy użyciu zapytań asynchronicznych lub równoległych.
Na przykład może istnieć co najmniej dwa wystąpienia roli procesu roboczego, które uzyskują dostęp do tabel równolegle. Mogą istnieć poszczególne role procesów roboczych odpowiedzialne za określone zestawy partycji lub po prostu ma wiele wystąpień roli procesu roboczego, z których każdy może uzyskać dostęp do wszystkich partycji w tabeli.  

W ramach wystąpienia klienta można zwiększyć przepływność przez wykonywanie operacji magazynu asynchronicznie. Biblioteka klienta magazynu ułatwia pisanie asynchronicznych zapytań i modyfikacji. Na przykład możesz zacząć od metody synchronicznej, która pobiera wszystkie jednostki w partycji, jak pokazano w poniższym kodzie C#:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Możesz łatwo zmodyfikować ten kod, aby zapytanie zostało uruchomione asynchronicznie w następujący sposób:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

W tym przykładzie asynchronicznym można zobaczyć następujące zmiany w wersji synchronicznej:  

* Sygnatura metody obejmuje teraz modyfikator **Async** i zwraca wystąpienie **zadania** .  
* Zamiast wywoływania metody **ExecuteSegmented** w celu pobierania wyników metoda wywołuje teraz metodę **ExecuteSegmentedAsync** i używa modyfikatora **await** , aby pobrać wyniki asynchronicznie.  

Aplikacja kliencka może wywołać tę metodę wiele razy (z różnymi wartościami dla parametru **działu** ), a każde zapytanie zostanie uruchomione w osobnym wątku.  

Brak asynchronicznej wersji metody **Execute** w klasie **TableQuery** , ponieważ interfejs **IEnumerable** nie obsługuje asynchronicznego wyliczania.  

Można także wstawiać, aktualizować i usuwać jednostki asynchronicznie. W poniższym przykładzie w języku C# przedstawiono prostą, synchroniczną metodę wstawiania lub zastępowania jednostki pracownika:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Możesz łatwo zmodyfikować ten kod, aby aktualizacja działała asynchronicznie w następujący sposób:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

W tym przykładzie asynchronicznym można zobaczyć następujące zmiany w wersji synchronicznej:  

* Sygnatura metody obejmuje teraz modyfikator **Async** i zwraca wystąpienie **zadania** .  
* Zamiast wywoływania metody **Execute** w celu zaktualizowania jednostki, metoda wywołuje teraz metodę **wywoływanie ExecuteAsync** i używa modyfikatora **await** do pobierania wyników asynchronicznie.  

Aplikacja kliencka może wywoływać wiele metod asynchronicznych, takich jak ta, i każde wywołanie metody zostanie uruchomione w osobnym wątku.  

## <a name="next-steps"></a>Następne kroki

- [Modelowanie relacji](table-storage-design-modeling.md)
- [Projektowanie pod kątem wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfrowanie danych tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)