---
title: Migrowanie setek terabajtów danych do usługi Azure Cosmos DB
description: W tym dokumencie opisano sposób migrowania setek terabajtów danych do Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: 1e48b2ff6e469a5f792b64c20631e4bd64fb9fd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85263548"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Migrowanie setek terabajtów danych do usługi Azure Cosmos DB 

Usługa Azure Cosmos DB może przechowywać terabajty danych. Można przeprowadzić migrację danych na dużą skalę, aby przenieść obciążenie produkcyjne do usługi Azure Cosmos DB. W tym artykule opisano wyzwania związane z przenoszeniem danych na dużą skalę do usługi Azure Cosmos DB oraz przedstawiono narzędzie, które pomaga sprostać tym wyzwaniom i migruje dane do usługi Azure Cosmos DB. W tej analizie przypadku klient użył interfejsu SQL API usługi Cosmos DB.  

Przed przeprowadzeniem migracji całego obciążenia do Azure Cosmos DB można migrować podzestaw danych w celu weryfikacji niektórych aspektów, takich jak wybór klucza partycji, wydajność zapytań i modelowanie danych. Po sprawdzeniu poprawności koncepcji można przenieść całe obciążenie do Azure Cosmos DB.  

## <a name="tools-for-data-migration"></a>Narzędzia do migracji danych 

Azure Cosmos DB strategie migracji różnią się obecnie w zależności od wybranego interfejsu API i rozmiaru danych. Aby przeprowadzić migrację mniejszych zestawów danych — do sprawdzania poprawności modelowania, wydajności zapytań, wyboru klucza partycji itp. — można wybrać [Narzędzie do migracji danych](import-data.md) lub [Łącznik Azure Cosmos DB Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Jeśli znasz już platformę Spark, możesz również użyć [łącznika Azure Cosmos DB Spark](spark-connector.md) do migracji danych.

## <a name="challenges-for-large-scale-migrations"></a>Wyzwania dla migracji na dużą skalę 

Istniejące narzędzia do migrowania danych do Azure Cosmos DB mają pewne ograniczenia, które są szczególnie widoczne w dużych skalach:

 * **Ograniczone możliwości skalowania w poziomie**: w celu przeprowadzenia migracji terabajtów danych do Azure Cosmos DB tak szybko, jak to możliwe, i efektywnego wykorzystania całej przewidzianej przepływności, klienci migracji powinni mieć możliwość skalowania w poziomie nieokreślonym.  

* **Brak śledzenia postępu i sprawdzenia wskazującego**: ważne jest, aby śledzić postęp migracji i sprawdzać, czy są migrowane duże zestawy danych. W przeciwnym razie wszelkie błędy występujące podczas migracji przestaną być migrowane i konieczne będzie rozpoczęcie procesu od podstaw. Ponowne uruchomienie całego procesu migracji nie będzie możliwe, gdy 99% już zostało zakończone.  

* **Brak kolejki utraconych wiadomości**: w przypadku dużych zestawów danych w niektórych przypadkach mogą wystąpić problemy z częściami danych źródłowych. Ponadto mogą występować przejściowe problemy z klientem lub siecią. Każdy z tych przypadków nie powinien spowodować awarii całej migracji. Mimo że większość narzędzi migracji ma niezawodne funkcje ponawiania prób, które chronią przed sporadycznymi problemami, nie zawsze są wystarczające. Na przykład jeśli mniej niż 0,01% dokumentów danych źródłowych ma rozmiar większy niż 2 MB, spowoduje to niepowodzenie zapisu dokumentu w Azure Cosmos DB. W idealnym przypadku jest to przydatne w przypadku, gdy narzędzie migracji będzie utrwalać te dokumenty "Niepowodzenie" w innej kolejce utraconych wiadomości, która może być przetworzona po migracji. 

Wiele z tych ograniczeń jest naprawionych w przypadku narzędzi takich jak Azure Data Factory, Azure Data Migration Services. 

## <a name="custom-tool-with-bulk-executor-library"></a>Niestandardowe narzędzie z biblioteką modułu wykonującego zbiorczo 

Wyzwania opisane w powyższej sekcji można rozwiązać przy użyciu niestandardowego narzędzia, które można łatwo skalować w wielu wystąpieniach i odporne na błędy przejściowe. Ponadto narzędzie niestandardowe może wstrzymywać i wznawiać migrację w różnych punktach kontrolnych. Azure Cosmos DB już udostępnia [bibliotekę wykonawców zbiorczych](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) , która obejmuje niektóre z tych funkcji. Na przykład biblioteka wykonawczy Bulk ma już funkcjonalność do obsługi błędów przejściowych i można skalować w poziomie wątki w jednym węźle, aby użyć około 500 K jednostek ru na węzeł. Biblioteka wykonawców zbiorczych również dzieli źródłowy zestaw danych na mikropartie, które są obsługiwane niezależnie jako forma tworzenia punktów kontrolnych.  

Narzędzie niestandardowe używa biblioteki wykonawców zbiorczych i obsługuje skalowanie w poziomie między wieloma klientami oraz śledzenie błędów w procesie pozyskiwania. Aby można było używać tego narzędzia, dane źródłowe powinny być partycjonowane na różne pliki w Azure Data Lake Storage (ADLS), dzięki czemu różne procesy robocze migracji mogą pobrać każdy plik i pozyskiwać je do Azure Cosmos DB. Narzędzie niestandardowe korzysta z oddzielnej kolekcji, która przechowuje metadane dotyczące postępu migracji każdego pliku źródłowego w programie ADLS i śledzi wszelkie związane z nimi błędy.  

Na poniższej ilustracji opisano proces migracji przy użyciu tego niestandardowego narzędzia. Narzędzie jest uruchamiane na zestawie maszyn wirtualnych, a każda maszyna wirtualna wysyła zapytanie do kolekcji śledzenia w Azure Cosmos DB, aby uzyskać dzierżawę na jednej z partycji danych źródłowych. Po wykonaniu tej czynności partycja danych źródłowych zostanie odczytana przez narzędzie i przeprowadzona do Azure Cosmos DB przy użyciu biblioteki modułu wykonawczego BULK. Następnie kolekcja śledzenia jest aktualizowana, aby rejestrować postęp pozyskiwania danych i napotkane błędy. Po przetworzeniu partycji danych Narzędzie próbuje wykonać zapytanie o następną dostępną partycję źródłową. Kontynuuje przetwarzanie następnej partycji źródłowej, dopóki nie zostaną zmigrowane wszystkie dane. Kod źródłowy narzędzia jest dostępny [tutaj](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion).  

 
:::image type="content" source="./media/migrate-cosmosdb-data/migrationsetup.png" alt-text="Konfiguracja narzędzia migracji" border="false":::
 

 

Kolekcja śledzenia zawiera dokumenty, jak pokazano w poniższym przykładzie. Zobaczysz takie dokumenty dla każdej partycji w danych źródłowych.  Każdy dokument zawiera metadane źródłowej partycji danych, takie jak lokalizacja, stan migracji i błędy (jeśli istnieją):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Wymagania wstępne dotyczące migracji danych 

Przed rozpoczęciem migracji danych należy wziąć pod uwagę kilka wymagań wstępnych:  

#### <a name="estimate-the-data-size"></a>Oszacuj rozmiar danych:  

Rozmiar danych źródłowych może nie być dokładnie mapowany na rozmiar danych w Azure Cosmos DB. Można wstawić kilka przykładowych dokumentów ze źródła, aby sprawdzić ich rozmiar w Azure Cosmos DB. W zależności od rozmiaru dokumentu przykładowego można oszacować łączny rozmiar danych w Azure Cosmos DB po migracji. 

Na przykład, jeśli każdy dokument po migracji w Azure Cosmos DB ma około 1 KB i jeśli w źródłowym zestawie danych znajduje się około 60 000 000 000 dokumentów, będzie to oznaczało, że szacowany rozmiar w Azure Cosmos DB będzie zbliżony do 60 TB. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Wstępne tworzenie kontenerów z wystarczającą jednostek ru: 

Mimo że Azure Cosmos DB automatycznie skaluje magazyn, nie zaleca się uruchamiania od najmniejszego rozmiaru kontenera. Mniejsze kontenery mają mniejszą dostępność, co oznacza, że ukończenie migracji zajmie dużo czasu. Zamiast tego warto utworzyć kontenery z końcowym rozmiarem danych (jak w poprzednim kroku) i upewnić się, że obciążenie migracji ma w pełni zużywaną przepływność.  

W poprzednim kroku. ponieważ rozmiar danych został osiągnięty wokół 60 TB, kontener o pojemności co najmniej 2,4 M jednostek ru jest wymagany do uwzględnienia całego zestawu danych.  

 

#### <a name="estimate-the-migration-speed"></a>Oszacuj szybkość migracji: 

Przy założeniu, że obciążenie migracji może zużywać całą zainicjowaną przepływność, przydziały w ramach tej operacji zapewnią oszacowanie szybkości migracji. Kontynuując poprzedni przykład, 5 jednostek ru są wymagane do zapisania dokumentu 1 KB w celu Azure Cosmos DB konta interfejsu API SQL.  2 400 000 jednostek ru zezwala na przesyłanie dokumentów 480 000 na sekundę (lub 480 MB/s). Oznacza to, że pełna migracja 60 TB zajmie 125 000 sekund lub około 34 godzin.  

W przypadku, gdy migracja ma zostać ukończona w ciągu dnia, należy zwiększyć przepustowość zainicjowaną do 5 000 000 jednostek ru. 

 

#### <a name="turn-off-the-indexing"></a>Wyłącz indeksowanie:  

Ponieważ migracja powinna zostać zakończona najszybciej, jak to możliwe, zaleca się, aby zminimalizować czas i jednostek ru poświęcany na tworzenie indeksów dla każdego z pozyskanych dokumentów.  Azure Cosmos DB automatycznie indeksuje wszystkie właściwości, wartościowa do zminimalizowania indeksowania do wybranych lub wyłączania w całości na potrzeby migracji. Zasady indeksowania kontenera można wyłączyć, zmieniając ustawienie indexmode na None, jak pokazano poniżej:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Po zakończeniu migracji można zaktualizować indeksowanie.  

## <a name="migration-process"></a>Proces migracji 

Po zakończeniu wymagań wstępnych można migrować dane przy użyciu następujących kroków:  

1. Najpierw zaimportuj dane ze źródła do usługi Azure Blob Storage. Aby zwiększyć szybkość migracji, warto zrównoleglanie na różnych partycjach źródłowych. Przed rozpoczęciem migracji zestaw danych źródłowych powinien być podzielony na pliki o rozmiarze około 200 MB.   

2. Biblioteka wykonawców zbiorczych może skalować w górę, aby korzystać z 500 000 jednostek ru na jednej maszynie wirtualnej klienta. Ponieważ dostępna przepływność to 5 000 000 jednostek ru, w tym samym regionie, w którym znajduje się baza danych Azure Cosmos, musi zostać zainicjowana obsługa 10 maszyn Standard_D32_v3 wirtualnych 16,04 Ubuntu. Należy przygotować te maszyny wirtualne za pomocą narzędzia migracji i jego pliku ustawień.  

3. Uruchom krok kolejki na jednej z maszyn wirtualnych klienta. W tym kroku zostanie utworzona kolekcja śledzenia, która skanuje kontener ADLS i tworzy dokument śledzenia postępu dla każdego z plików partycji zestawu danych źródłowych.  

4. Następnie uruchom etap importowania na wszystkich maszynach wirtualnych klienta. Każdy klient może przejąć własność na partycji źródłowej i pozyskać dane w Azure Cosmos DB. Po zakończeniu i jego stan zostanie zaktualizowany w kolekcji śledzenia, klienci mogą następnie wykonać zapytanie o następną dostępną partycję źródłową w kolekcji śledzenia.  

5. Ten proces jest kontynuowany do momentu pozyskania całego zestawu partycji źródłowych. Po przetworzeniu wszystkich partycji źródłowych należy ponownie uruchomić narzędzie w trybie korekcji błędów w tej samej kolekcji śledzenia. Ten krok jest wymagany do zidentyfikowania partycji źródłowych, które należy przetworzyć z powodu błędów.  

6. Niektóre z tych błędów mogą wynikać z nieprawidłowych dokumentów w danych źródłowych. Powinny być zidentyfikowane i naprawione. Następnie należy ponownie uruchomić etap importowania w przypadku partycji zakończonych niepowodzeniem w celu ich ponownego wyzyskania. 

Po zakończeniu migracji można sprawdzić, czy liczba dokumentów w Azure Cosmos DB jest taka sama jak liczba dokumentów w źródłowej bazie danych. W tym przykładzie łączny rozmiar w Azure Cosmos DB wyłączono do 65 terabajtów. Po migracji można wybiórczo włączyć indeksowanie, a jednostek ru można obniżyć do poziomu wymaganego przez operacje obciążenia.

## <a name="contact-the-azure-cosmos-db-team"></a>Skontaktuj się z zespołem Azure Cosmos DB
Chociaż w tym przewodniku można pomyślnie migrować duże zestawy danych do Azure Cosmos DB, w przypadku migracji z dużą skalą zaleca się skontaktowanie się z Azure Cosmos DB zespołem ds. produktów w celu zweryfikowania modelowania danych i ogólnego przeglądu architektury. Na podstawie zestawu danych i obciążenia zespół produktu może również zasugerować inne optymalizacje wydajności i kosztów, które mogą być dla Ciebie odpowiednie. Aby skontaktować się z zespołem Azure Cosmos DB, aby uzyskać pomoc dotyczącą migracji z dużą skalą, można otworzyć bilet pomocy technicznej w ramach typu problemu "ogólny poradnik" i "duże (TB +)".

:::image type="content" source="./media/migrate-cosmosdb-data/supporttopic.png" alt-text="Konfiguracja narzędzia migracji":::


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej, pobierając przykładowe aplikacje zużywające zbiorczą bibliotekę wykonawczą w programie [.NET i środowisku](bulk-executor-dot-net.md) [Java](bulk-executor-java.md). 
* Biblioteka wykonawców zbiorczych jest zintegrowana z łącznikiem Cosmos DB Spark, aby dowiedzieć się więcej, zobacz Azure Cosmos DB artykuł dotyczący [łącznika Spark](spark-connector.md) .  
* Skontaktuj się z zespołem produktu Azure Cosmos DB, otwierając bilet pomocy technicznej w ramach typu problemu "ogólny poradnik" i "duże (TB +) migracje", aby uzyskać dodatkową pomoc dotyczącą migracji z dużą skalą. 
