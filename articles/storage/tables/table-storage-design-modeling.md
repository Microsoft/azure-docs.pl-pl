---
title: Relacje modelowania w projekcie usługi Azure Table Storage | Microsoft Docs
description: Zapoznaj się z procesem modelowania podczas projektowania rozwiązania Azure Table Storage. Zapoznaj się z relacjami "jeden do wielu", jeden-do-jednego i dziedziczenie.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 3023b478ef7a4aaf6d9239e997bdf63282b56210
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88271196"
---
# <a name="modeling-relationships"></a>Modelowanie relacji
W tym artykule omówiono proces modelowania ułatwiający projektowanie rozwiązań usługi Azure Table Storage.

Tworzenie modeli domen to kluczowy etap projektowania złożonych systemów. Zazwyczaj proces modelowania jest używany do identyfikowania jednostek i relacji między nimi w celu zrozumienia domeny biznesowej i poinformowania o projekcie systemu. Ta sekcja koncentruje się na sposobach przekształcania niektórych typowych typów relacji znalezionych w modelach domen na projekty dla Table service. Proces mapowania z logicznego modelu danych na fizyczny model danych oparty na NoSQL różni się od tego, który jest używany podczas projektowania relacyjnej bazy danych. Projekt relacyjnych baz danych zazwyczaj przyjmuje proces normalizacji danych zoptymalizowany pod kątem minimalizowania nadmiarowości — oraz możliwość wykonywania zapytań deklaratywnych, która stanowi streszczenie sposobu działania bazy danych.  

## <a name="one-to-many-relationships"></a>Relacje jeden do wielu
Relacja jeden do wielu między obiektami domeny biznesowej występuje często: na przykład jeden dział ma wielu pracowników. Istnieje kilka sposobów implementacji relacji jeden-do-wielu w Table service każdy ze specjalistami i wadami, które mogą mieć zastosowanie w konkretnym scenariuszu.  

Rozważmy przykład dużej firmy wielonarodowej z dziesiątki tysięcy działów i jednostek pracowników, gdzie każdy dział ma wielu pracowników i każdego pracownika jako skojarzony z jednym określonym działem. Jednym z metod jest przechowywanie oddzielnych jednostek działu i pracowników, takich jak następujące:  


![Przechowywanie odrębnych jednostek działu i pracowników](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Ten przykład pokazuje niejawną relację "jeden do wielu" między typami opartymi na wartości **PartitionKey** . Każdy dział może mieć wielu pracowników.  

Ten przykład pokazuje również jednostkę działu i powiązane jednostki pracowników w tej samej partycji. Można użyć różnych partycji, tabel, a nawet kont magazynu dla różnych typów jednostek.  

Alternatywnym podejściem jest denormalizowanie danych i przechowywanie tylko jednostek pracowników z nieznormalizowanymi danymi działu, jak pokazano w poniższym przykładzie. W tym konkretnym scenariuszu takie nieznormalizowane podejście może nie być najlepszym rozwiązaniem, jeśli istnieje wymóg, aby można było zmienić szczegóły menedżera działu, ponieważ w tym celu należy zaktualizować każdego pracownika w dziale.  

![Jednostka Employee](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Aby uzyskać więcej informacji, zobacz [wzorzec denormalizacji](table-storage-design-patterns.md#denormalization-pattern) w dalszej części tego przewodnika.  

Poniższa tabela zawiera podsumowanie specjalistów i wad każdego z podejść przedstawionych powyżej do przechowywania jednostek pracowników i działów, które mają relację jeden do wielu. Należy również wziąć pod uwagę, jak często mają być wykonywane różne operacje: może być możliwe posiadanie projektu, który obejmuje kosztowną operację, jeśli ta operacja jest wykonywana tylko rzadko.  

<table>
<tr>
<th>Podejście</th>
<th>Zalety</th>
<th>Wady</th>
</tr>
<tr>
<td>Oddzielne typy jednostek, ta sama partycja, ta sama tabela</td>
<td>
<ul>
<li>Można zaktualizować jednostkę działu przy użyciu jednej operacji.</li>
<li>Można użyć transakcji grupy jednostek * (EGT), aby zachować spójność, jeśli istnieje potrzeba zmodyfikowania jednostki działu przy każdej aktualizacji/wstawieniu/usunięciu jednostki pracownika. Na przykład w przypadku utrzymania liczby pracowników działu dla poszczególnych działów.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie pracownika i jednostki działu dla niektórych działań klientów.</li>
<li>Operacje magazynu są wykonywane w tej samej partycji. W przypadku dużych ilości transakcji może to spowodować powstanie punktu aktywnego.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Oddzielne typy jednostek, różne partycje lub tabele lub konta magazynu</td>
<td>
<ul>
<li>Można zaktualizować jednostkę działu lub jednostkę pracownika przy użyciu jednej operacji.</li>
<li>W przypadku dużych ilości transakcji może to ułatwić rozproszenie obciążenia na więcej partycji.</li>
</ul>
</td>
<td>
<ul>
<li>Może być konieczne pobranie pracownika i jednostki działu dla niektórych działań klientów.</li>
<li>Nie można użyć EGTs, aby zachować spójność podczas aktualizowania/wstawiania/usuwania pracownika i aktualizowania działu. Na przykład w jednostce działu można aktualizować liczbę pracowników.</li>
<li>Nie można przenieść pracownika do nowego działu przy użyciu EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormalizowanie do typu pojedynczej jednostki</td>
<td>
<ul>
<li>Wszystkie potrzebne informacje można pobrać przy użyciu pojedynczego żądania.</li>
</ul>
</td>
<td>
<ul>
<li>Zachowanie spójności może być kosztowne, jeśli trzeba zaktualizować informacje działu (wymaga to zaktualizowania wszystkich pracowników w dziale).</li>
</ul>
</td>
</tr>
</table>

* Aby uzyskać więcej informacji, zobacz [transakcje grupy jednostek](table-storage-design.md#entity-group-transactions)  


Wybór między tymi opcjami oraz to, które z zalet i wad są najbardziej znaczące, zależy od konkretnych scenariuszy aplikacji. Na przykład jak często modyfikujesz jednostki działu; wszystkie zapytania pracowników potrzebują dodatkowych informacji działu; jak blisko ograniczeń skalowalności w partycjach lub na koncie magazynu?  

## <a name="one-to-one-relationships"></a>Relacje jeden do jednego
Modele domeny mogą zawierać relacje jeden do jednego między jednostkami. Jeśli zachodzi potrzeba zaimplementowania relacji jeden-do-jednego w Table service, należy również wybrać, jak połączyć dwie powiązane jednostki, gdy trzeba je pobrać. Ten link może być niejawny, na podstawie Konwencji w wartościach klucza lub jawnie przez przechowywanie linku w postaci wartości **PartitionKey** i **RowKey** w każdej jednostce względem powiązanej jednostki. Aby zapoznać się z omówieniem, czy należy przechowywać powiązane jednostki w tej samej partycji, zobacz [relacje jeden do wielu](#one-to-many-relationships).  

Istnieją także uwagi dotyczące implementacji, które mogą prowadzić do wdrożenia relacji jeden-do-jednego w Table service:  

* Obsługa dużych jednostek (Aby uzyskać więcej informacji, zobacz [wzorzec dużych jednostek](table-storage-design-patterns.md#large-entities-pattern)).  
* Implementowanie kontroli dostępu (Aby uzyskać więcej informacji, zobacz Kontrolowanie dostępu za pomocą sygnatur dostępu współdzielonego).  

## <a name="join-in-the-client"></a>Dołącz do klienta
Chociaż istnieją metody do modelowania relacji w Table service, nie należy zapominać, że dwa podstawowe przyczyny używania Table service są skalowalne i wydajnościowe. Jeśli okaże się, że modelowanie ma wiele relacji, które naruszają wydajność i skalowalność rozwiązania, należy poproszony o to, jeśli konieczne jest skompilowanie wszystkich relacji danych w projekcie tabeli. Może być możliwe uproszczenie projektowania i zwiększenie skalowalności i wydajności rozwiązania, Jeśli zezwolisz aplikacji klienckiej na wykonywanie dowolnych koniecznych sprzężeń.  

Na przykład jeśli masz małe tabele zawierające dane, które nie zmieniają się często, możesz pobrać te dane raz i umieścić je w pamięci podręcznej na kliencie. Może to uniknąć powtarzanych roundtrip, aby pobrać te same dane. W przykładach, które zostały przedstawione w tym przewodniku, zestaw działów w małej organizacji może być niewielki i często zmieniany, dzięki czemu jest to dobry kandydat dla danych, które aplikacja kliencka może pobrać raz i buforować jako dane wyszukiwania.  

## <a name="inheritance-relationships"></a>Relacje dziedziczenia
Jeśli aplikacja kliencka korzysta z zestawu klas, które stanowią część relacji dziedziczenia reprezentującej jednostki biznesowe, można łatwo utrzymać te jednostki w Table service. Na przykład w aplikacji klienckiej można określić następujący zestaw klas, gdzie **osoba** jest klasą abstrakcyjną.

![Klasa osoby abstrakcyjnej](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Można utrzymywać wystąpienia dwóch konkretnych klas w Table service przy użyciu pojedynczej tabeli osób, która będzie wyglądać następująco:  

![Tabela osób](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Więcej informacji o pracy z wieloma typami jednostek w tej samej tabeli w kodzie klienta znajduje się w sekcji Praca z typami jednostek heterogenicznych w dalszej części tego przewodnika. Zawiera przykłady sposobu rozpoznawania typu jednostki w kodzie klienta.  


## <a name="next-steps"></a>Następne kroki

- [Wzorce projektowe tabel](table-storage-design-patterns.md)
- [Projektowanie pod kątem wykonywania zapytań](table-storage-design-for-query.md)
- [Szyfruj dane tabeli](table-storage-design-encrypt-data.md)
- [Projektowanie pod kątem modyfikacji danych](table-storage-design-for-modification.md)
