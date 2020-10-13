---
title: Projektowanie usługi Azure Table Storage na potrzeby zapytań | Microsoft Docs
description: Projektowanie tabel dla zapytań w usłudze Azure Table Storage. Wybierz odpowiedni klucz partycji, Optymalizuj zapytania i Sortuj dane dla Table service.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: a15415ab7f5e01619a4a022d7254ef3995a825b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88236339"
---
# <a name="design-for-querying"></a>Projektowanie pod kątem wykonywania zapytań
Rozwiązania Table service mogą być bardzo czasochłonne, duże ilościowe lub mieszane. Ten artykuł koncentruje się na zagadnieniach, które należy wziąć pod uwagę podczas projektowania Table service w celu wydajnej obsługi operacji odczytu. Zwykle projekt obsługujący operacje odczytu wydajnie jest również wydajny w przypadku operacji zapisu. Istnieją jednak dodatkowe zagadnienia, które należy wziąć pod uwagę podczas projektowania programu w celu obsługi operacji zapisu, omówione w artykule [projektowanie artykułu na potrzeby modyfikacji danych](table-storage-design-for-modification.md).

Dobrym punktem początkowym do projektowania rozwiązania Table service, aby umożliwić odczytywanie danych w sposób efektywny, jest zaproszenie "jakie zapytania należy wykonać moja aplikacja, aby pobrać potrzebne dane z Table service?".  

> [!NOTE]
> Mając Table service, ważne jest, aby projekt został skorygowany z góry, ponieważ jest trudny i kosztowny do jego późniejszej zmiany. Na przykład w relacyjnej bazie danych często możliwe jest rozwiązanie problemów z wydajnością po prostu przez dodanie indeksów do istniejącej bazy danych: nie jest to opcja z Table service.  
> 
> 

W tej sekcji omówiono kluczowe problemy, które należy rozwiązać podczas projektowania tabel do wykonywania zapytań. Tematy omówione w tej sekcji obejmują:

* [Jak wybór PartitionKey i RowKey wpływa na wydajność zapytań](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Wybieranie odpowiedniego PartitionKey](#choosing-an-appropriate-partitionkey)
* [Optymalizowanie zapytań dla Table service](#optimizing-queries-for-the-table-service)
* [Sortowanie danych w Table service](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Jak wybór PartitionKey i RowKey wpływa na wydajność zapytań
W poniższych przykładach przyjęto założenie, że usługa Table Service zapisuje jednostki pracowników przy użyciu następującej struktury (większość przykładów pomija Właściwość **timestamp** dla jasności):  

| *Nazwa kolumny* | *Typ danych* |
| --- | --- |
| **PartitionKey** (Nazwa działu) |Ciąg |
| **RowKey** (identyfikator pracownika) |Ciąg |
| **FirstName (Imię)** |Ciąg |
| **LastName (Nazwisko)** |Ciąg |
| **Age** |Liczba całkowita |
| **EmailAddress (Adres e-mail)** |Ciąg |

Artykuł [Omówienie usługi Azure Table Storage](table-storage-overview.md) zawiera opis niektórych kluczowych funkcji usługi Azure Table Service, które mają bezpośredni wpływ na projektowanie zapytań. Poniżej przedstawiono ogólne wytyczne dotyczące projektowania zapytań Table service. Należy zauważyć, że składnia filtru użyta w poniższych przykładach pochodzi z interfejsu API REST Table service, aby uzyskać więcej informacji, zobacz [jednostki zapytań](https://docs.microsoft.com/rest/api/storageservices/Query-Entities).  

* ***Zapytanie punktowe*** jest najbardziej wydajnym wyszukiwaniem do użycia i jest zalecane do użycia w odnośnikach lub wyszukiwaniach o wysokim poziomie, które wymagają najmniejszego opóźnienia. Takie zapytanie może używać indeksów, aby bardzo wydajnie lokalizować poszczególne jednostki przez określenie wartości **PartitionKey** i **RowKey** . Na przykład: $filter = (PartitionKey EQ "Sales") i (RowKey EQ "2")  
* Druga Najlepsza to ***zapytanie zakresowe*** używające **PartitionKey** i filtrów dla zakresu wartości **RowKey** , aby zwrócić więcej niż jedną jednostkę. Wartość **PartitionKey** identyfikuje konkretną partycję, a wartości **RowKey** identyfikują podzestaw jednostek w tej partycji. Na przykład: $filter = PartitionKey EQ "Sales" i RowKey GE "i RowKey lt"  
* Trzecia Najlepsza to ***skanowanie partycji*** korzystające z **PartitionKey** i filtrów dla innej właściwości niebędącej kluczem, które może zwrócić więcej niż jedną jednostkę. Wartość **PartitionKey** identyfikuje konkretną partycję, a wartości właściwości wybierają podzbiór jednostek w tej partycji. Na przykład: $filter = PartitionKey EQ "Sales" i LastName EQ "Kowalski"  
* ***Skanowanie tabeli*** nie obejmuje **PartitionKey** i jest bardzo wydajne, ponieważ przeszukuje wszystkie partycje, które składają się na tabelę, z kolei w przypadku wszystkich pasujących jednostek. Przeprowadzi skanowanie tabeli niezależnie od tego, czy filtr używa **RowKey**. Na przykład: $filter = LastName EQ "Nowak"  
* Zapytania zwracające wiele jednostek zwracają je posortowane w kolejności **PartitionKey** i **RowKey** . Aby uniknąć tworzenia jednostek w kliencie, wybierz **RowKey** , który definiuje najbardziej typowy porządek sortowania.  

Należy zauważyć, że użycie "**lub**" do określenia filtru opartego na wartościach **RowKey** skutkuje przeskanowaniem partycji i nie jest traktowane jako zapytanie o zakres. W związku z tym należy unikać zapytań, które używają filtrów takich jak: $filter = PartitionKey EQ "Sales" i (RowKey EQ "121" lub RowKey EQ "322")  

Aby zapoznać się z przykładami kodu po stronie klienta, które używają biblioteki klienta usługi Storage do wykonywania wydajnych zapytań, zobacz:  

* [Wykonywanie zapytania punktu przy użyciu biblioteki klienta usługi Storage](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Pobieranie wielu jednostek przy użyciu LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projekcja po stronie serwera](table-storage-design-patterns.md#server-side-projection)  

Aby zapoznać się z przykładami kodu po stronie klienta, które mogą obsługiwać wiele typów jednostek przechowywanych w tej samej tabeli, zobacz:  

* [Współpraca z niejednorodnymi typami jednostek](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Wybieranie odpowiedniego PartitionKey
Wybór **PartitionKey** powinien zrównoważyć potrzebę umożliwienia używania EGTs (w celu zapewnienia spójności) przed wymaganiem dystrybucji jednostek na wielu partycjach (aby zapewnić skalowalne rozwiązanie).  

Z jednej najwyższej klasy można przechowywać wszystkie jednostki w jednej partycji, ale może to ograniczyć skalowalność rozwiązania i uniemożliwić usłudze tabel możliwość równoważenia obciążenia żądań. Z drugiej najwyższej wartości można przechowywać jedną jednostkę na partycję, która byłaby wysoce skalowalna, co umożliwia usłudze tabel Równoważenie obciążenia żądań, ale co uniemożliwi korzystanie z transakcji grupy jednostek.  

Idealny **PartitionKey** to taki, który umożliwia korzystanie z wydajnych zapytań i zawiera wystarczające partycje, aby zapewnić skalowalność rozwiązania. Zazwyczaj można stwierdzić, że jednostki będą mieć odpowiednią właściwość, która dystrybuuje jednostki w wystarczającej partycji.

> [!NOTE]
> Na przykład w systemie, w którym są przechowywane informacje o użytkownikach lub pracownikach, UserID może być dobry PartitionKey. Może istnieć kilka jednostek, które używają danego identyfikatora użytkownika jako klucza partycji. Każda jednostka, która przechowuje dane dotyczące użytkownika, jest zgrupowana w jedną partycję, więc te jednostki są dostępne za pośrednictwem transakcji grupy jednostek, przy czym nadal są wysoce skalowalne.
> 
> 

Dostępne są dodatkowe zagadnienia dotyczące wyboru **PartitionKey** , które odnoszą się do sposobu wstawiania, aktualizowania i usuwania jednostek. Aby uzyskać więcej informacji, zobacz [Projektowanie tabel do modyfikacji danych](table-storage-design-for-modification.md).  

## <a name="optimizing-queries-for-the-table-service"></a>Optymalizowanie zapytań dla Table service
Table service automatycznie indeksuje jednostki przy użyciu wartości **PartitionKey** i **RowKey** w pojedynczym indeksie klastrowanym, w związku z czym przyczyny tego zapytania są najbardziej wydajne. Jednak żadne indeksy nie są inne niż w indeksie klastrowanym w **PartitionKey** i **RowKey**.

Wiele projektów musi spełniać wymagania, aby umożliwić wyszukiwanie jednostek na podstawie wielu kryteriów. Na przykład lokalizowanie jednostek pracowników na podstawie poczty e-mail, identyfikatora pracownika lub nazwiska. Wzorce opisane w [wzorach projektu tabeli](table-storage-design-patterns.md) dotyczą tego typu wymagania i opisują sposoby obejścia tego faktu, że Table Service nie udostępnia indeksów pomocniczych:  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości **RowKey** (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey** .  
* [Wzorzec indeksu pomocniczego między partycjami](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości **RowKey** w oddzielnych partycjach lub w oddzielnych tabelach, aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości **RowKey** .  
* [Indeks jednostek indeksu](table-storage-design-patterns.md#index-entities-pattern) — utrzymuje jednostki indeksu, aby umożliwić efektywne wyszukiwanie, które zwracają listy jednostek.  

## <a name="sorting-data-in-the-table-service"></a>Sortowanie danych w Table service
Table service zwraca jednostki posortowane w kolejności rosnącej na podstawie **PartitionKey** , a następnie według **RowKey**. Te klucze są wartościami ciągu i aby zapewnić prawidłowe sortowanie wartości liczbowych, należy przekonwertować je na stałą długość i uzupełnić je zerami. Na przykład jeśli wartość identyfikatora pracownika, która jest używana jako **RowKey** , jest wartością całkowitą, należy przekonwertować pracownika o identyfikatorze **123** do **00000123**.  

Wiele aplikacji ma wymagania dotyczące korzystania z danych posortowanych w różnych zamówieniach: na przykład sortowanie pracowników według nazwy lub dołączanie daty. Następujące wzorce dotyczą sposobu wykonywania alternatywnych zamówień sortowania dla jednostek:  

* [Wzorzec indeksu pomocniczego wewnątrz partycji](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości RowKey (w tej samej partycji), aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości RowKey.  
* [Wzorzec indeksu pomocniczego między partycjami](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) — przechowywanie wielu kopii każdej jednostki przy użyciu różnych wartości RowKey w oddzielnych partycjach w oddzielnych tabelach, aby umożliwić szybkie i wydajne wyszukiwanie oraz alternatywne zamówienia sortowania przy użyciu różnych wartości RowKey.
* [Wzorzec końca dziennika](table-storage-design-patterns.md#log-tail-pattern) — pobranie *n* jednostek, które zostały ostatnio dodane do partycji przy użyciu wartości **RowKey** , która jest sortowana w odwrotnej kolejności daty i godziny.  

## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowe tabel](table-storage-design-patterns.md)
- [Modelowanie relacji](table-storage-design-modeling.md)
- [Szyfruj dane tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
