---
title: Migruj MongoDB offline do Azure Cosmos DB API for MongoDB przy użyciu narzędzi MongoDB Native
description: Dowiedz się, jak narzędzia natywne MongoDB mogą służyć do migrowania małych zestawów danych z wystąpień MongoDB do Azure Cosmos DB
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2b52a9227e8bd487a8929df11047eef4672f7f4a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100421917"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Samouczek: Migrowanie MongoDB Azure Cosmos DB do interfejsu API usługi MongoDB w trybie offline przy użyciu narzędzi MongoDB Native

Za pomocą natywnych narzędzi MongoDB można przeprowadzić migrację baz danych w trybie offline (jednorazowym) z wystąpienia lokalnego lub w chmurze MongoDB do Azure Cosmos DB interfejsu API dla MongoDB.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
>
> * Wybierz odpowiednie narzędzie natywne MongoDB dla przypadków użycia
> * Uruchamianie migracji.
> * Monitoruj migrację.
> * Sprawdź, czy migracja zakończyła się pomyślnie.

W tym samouczku przeprowadzisz migrację zestawu danych w MongoDB hostowanym na maszynie wirtualnej platformy Azure Azure Cosmos DB do interfejsu API usługi MongoDB przy użyciu MongoDB natywnych narzędzi. Narzędzia MongoDB Native są zestawem plików binarnych, które ułatwiają manipulowanie danymi na istniejącym wystąpieniu MongoDB. Ponieważ Azure Cosmos DB uwidacznia interfejs API Mongo, narzędzia MongoDB Native mogą wstawiać dane do Azure Cosmos DB. Fokus tego dokumentu polega na migrowaniu danych z wystąpienia MongoDB przy użyciu *mongoexport/mongoimport* lub *mongodump/mongorestore*. Ponieważ narzędzia natywne nawiązują połączenie z usługą MongoDB przy użyciu parametrów połączenia, można uruchomić narzędzia w dowolnym miejscu, jednak zaleca się uruchomienie tych narzędzi w tej samej sieci co wystąpienie MongoDB, aby uniknąć problemów z zaporą. 

Natywne narzędzia MongoDB umożliwiają przenoszenie danych tylko tak szybko, jak to zapewnia sprzęt hosta; narzędzia natywne mogą być najprostszym rozwiązaniem dla małych zestawów danych, w których łączny czas migracji nie jest problemem. [Łącznik MongoDB Spark](https://docs.mongodb.com/spark-connector/current/), [usługa Azure Data Migration Service (DMS)](../dms/tutorial-mongodb-cosmos-db.md)lub [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) może być lepszym rozwiązaniem alternatywnym w przypadku potrzeby skalowalnego potoku migracji.

Jeśli nie masz jeszcze skonfigurowanego źródła bazy danych MongoDB, zobacz artykuł [Install and configure MongoDB on a Windows VM in Azure (Instalowanie i konfigurowanie bazy danych MongoDB na maszynie wirtualnej z systemem Windows na platformie Azure)](../virtual-machines/windows/install-mongodb.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Wykonaj kroki sprzed migracji](../cosmos-db/mongodb-pre-migration.md) , takie jak szacowanie przepływności, wybór klucza partycji i zasad indeksowania.
* [Utwórz Azure Cosmos DB interfejs API dla konta MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Zaloguj się do wystąpienia MongoDB
    * [Pobierz i zainstaluj narzędzia MongoDB Native z tego linku](https://www.mongodb.com/try/download/database-tools).
        * **Upewnij się, że wersja natywnych narzędzi MongoDB jest zgodna z istniejącym wystąpieniem MongoDB.**
        * Jeśli wystąpienie MongoDB ma inną wersję niż Azure Cosmos DB interfejs API Mongo, **Zainstaluj obie wersje narzędzia MongoDB Native i użyj odpowiednio odpowiedniej wersji narzędzia dla MongoDB i Azure Cosmos DB Mongo.**
    * Dodaj użytkownika z `readWrite` uprawnieniami, chyba że taki już istnieje. W dalszej części tego samouczka podaj tę nazwę użytkownika/hasło do narzędzi *mongoexport* i *mongodump* .

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Konfigurowanie ponownych prób po stronie serwera Azure Cosmos DB

Klienci migrowani z MongoDB Azure Cosmos DB mogą korzystać z funkcji zarządzania zasobami, co gwarantuje, że możliwość pełnego wykorzystania zainicjowanej usługi RU/s przepływności. Azure Cosmos DB może ograniczać daną prośbę w trakcie migracji, jeśli to żądanie przekroczy zainicjowany kontener RU/s; Następnie należy ponowić próbę żądania. Czas błądzenia związany z przeskokiem sieci między narzędziem migracji a Azure Cosmos DB ma wpływ na ogólny czas odpowiedzi tego żądania; Ponadto narzędzia natywne MongoDB mogą nie obsługiwać ponownych prób. Funkcja *ponawiania po stronie serwera* Azure Cosmos DB umożliwia usłudze przechwycenie kodów błędów ograniczania przepustowości i ponawianie próby w przypadku znacznie mniejszego czasu rundy, znacznie poprawiając czas odpowiedzi na żądania. Z punktu widzenia narzędzi natywnych MongoDB musi dojść do obsłużenia ponownych prób, co zmniejsza się podczas migracji.

Funkcję ponawiania po stronie serwera można znaleźć w bloku *funkcje* portalu Azure Cosmos DB

![Zrzut ekranu funkcji SSR MongoDB.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

A jeśli jest *wyłączona*, zalecamy włączenie tego, jak pokazano poniżej

![Zrzut ekranu przedstawiający MongoDB SSR.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>Wybieranie odpowiedniego narzędzia macierzystego MongoDB

![Diagram wybierania najlepszego MongoDB macierzystego narzędzia.](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoimport* to Najlepsza para narzędzi do migracji służąca do migrowania podzestawu bazy danych MongoDB.
    * *mongoexport* eksportuje istniejące dane do pliku JSON lub CSV z możliwością odczytu. *mongoexport* przyjmuje argument określający podzestaw istniejących danych do wyeksportowania. 
    * *mongoimport* otwiera plik JSON lub CSV i wstawia zawartość do docelowego wystąpienia bazy danych (Azure Cosmos DB w tym przypadku). 
    * Należy pamiętać, że pliki JSON i CSV nie są formatami kompaktowymi; opłaty za sieć mogą być naliczane, ponieważ *mongoimport* wysyła dane do Azure Cosmos DB.
* *mongodump/mongorestore* to Najlepsza para narzędzi do migracji w celu migrowania całej bazy danych MongoDB. Format Compact BSON umożliwia efektywniejsze korzystanie z zasobów sieciowych, ponieważ dane są wstawiane do Azure Cosmos DB.
    * *mongodump* eksportuje istniejące dane jako plik BSON.
    * *mongorestore* importuje zrzut pliku BSON do Azure Cosmos DB.
* Jako dodatkowy — Jeśli po prostu masz mały plik JSON, który chcesz zaimportować do Azure Cosmos DB API Mongo, narzędzie *mongoimport* to szybkie rozwiązanie do pozyskiwania danych.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>Zbierz Azure Cosmos DB poświadczenia interfejsu API Mongo

Interfejs API usługi Azure Cosmos DB Mongo zapewnia zgodne poświadczenia dostępu, które mogą być używane przez narzędzia natywne MongoDB. Te poświadczenia dostępu muszą być dostępne w celu przeprowadzenia migracji danych do Azure Cosmos DB interfejsu API Mongo. Aby znaleźć te poświadczenia:

1. Otwórz witrynę Azure Portal
1. Przejdź do konta interfejsu API usługi Azure Cosmos DB Mongo
1. W lewym okienku nawigacji wybierz blok *Parametry połączenia* , a powinien zostać wyświetlony ekran podobny do poniższego:

    ![Zrzut ekranu przedstawiający poświadczenia Azure Cosmos DB.](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *Host* — punkt końcowy Azure Cosmos DB pełni funkcję nazwy hosta MongoDB
    * *Port* — gdy MongoDB narzędzia natywne nawiązują połączenie z Azure Cosmos DB, należy określić ten port jawnie
    * Nazwa *użytkownika* — prefiks nazwy domeny punktu końcowego Azure Cosmos DB funkcjonuje jako nazwa użytkownika MongoDB
    * *Hasło* — klucz główny Azure Cosmos DB działa jako hasło MongoDB
    * Ponadto Zwróć uwagę na to, że pole *SSL* , które jest `true` — Narzędzie MongoDB Native **musi** włączyć protokół ssl podczas zapisywania danych w Azure Cosmos DB

## <a name="perform-the-migration"></a>Przeprowadź migrację

1. Wybierz bazy danych i kolekcje, które chcesz zmigrować. W tym przykładzie Migrujemy kolekcję *zapytań* w bazie danych *EDX* z MongoDB do Azure Cosmos DB.

Pozostała część tej sekcji przeprowadzi Cię przez użytkownika przez zastosowanie pary narzędzi wybranych w poprzedniej sekcji.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. Aby wyeksportować dane z wystąpienia MongoDB źródłowego, Otwórz Terminal na maszynie wystąpienia MongoDB. Jeśli jest to komputer z systemem Linux, wpisz

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    W systemie Windows plik wykonywalny będzie miał wartość `mongoexport.exe` . *Host*, *port*, *Nazwa użytkownika* i *hasło* powinny być wypełnione na podstawie właściwości istniejącego wystąpienia bazy danych MongoDB. 
    
    Możesz również wyeksportować tylko podzestaw zestawu danych MongoDB. Jednym ze sposobów jest dodanie dodatkowego argumentu filtru:
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Tylko dokumenty, które pasują do filtru, `{"field1":"value1"}` zostaną wyeksportowane.

    Po wykonaniu wywołania należy zobaczyć, że `edx.json` plik jest produkowany:

    ![Zrzut ekranu przedstawiający wywołanie mongoexport.](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. Możesz użyć tego samego terminalu do zaimportowania `edx.json` do Azure Cosmos DB. Jeśli używasz `mongoimport` programu na komputerze z systemem Linux, wpisz polecenie

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    W systemie Windows plik wykonywalny będzie miał wartość `mongoimport.exe` . Należy podać *hosta*, *port*, *nazwę użytkownika* i *hasło* na podstawie zebranych wcześniej poświadczeń Azure Cosmos DB. 
1. **Monitoruj** dane wyjściowe terminalu z *mongoimport*. Powinieneś zobaczyć, że program Drukuje wiersze tekstu do terminalu zawierającego aktualizacje stanu migracji:

    ![Zrzut ekranu przedstawiający wywołanie mongoimport.](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Na koniec Zbadaj Azure Cosmos DB, aby **sprawdzić** , czy migracja zakończyła się pomyślnie. Otwórz Portal Azure Cosmos DB i przejdź do Eksplorator danych. Powinna zostać wyświetlona (1), że utworzono bazę danych *EDX* z kolekcją *importedQuery* i (2) wyeksportowano tylko podzestaw danych, *importedQuery* powinna zawierać *tylko* dokumenty zgodne z żądanym podzbiorem danych. W poniższym przykładzie tylko jeden dokument pasuje do filtru `{"field1":"value1"}` :

    ![Zrzut ekranu przedstawiający weryfikację danych Cosmos DB.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. Aby utworzyć zrzut danych BSON wystąpienia MongoDB, Otwórz Terminal na maszynie wystąpienia MongoDB. Jeśli jest to komputer z systemem Linux, wpisz

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    *Host*, *port*, *Nazwa użytkownika* i *hasło* powinny być wypełnione na podstawie właściwości istniejącego wystąpienia bazy danych MongoDB. Powinieneś zobaczyć, że `edx-dump` katalog jest produkowany i że struktura katalogów `edx-dump` reprodukuje hierarchię zasobów (strukturę bazy danych i kolekcji) źródłowego wystąpienia MongoDB. Każda kolekcja jest reprezentowana przez plik BSON:

    ![Zrzut ekranu przedstawiający wywołanie mongodump.](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. Możesz użyć tego samego terminalu, aby przywrócić zawartość `edx-dump` do Azure Cosmos DB. Jeśli używasz `mongorestore` programu na komputerze z systemem Linux, wpisz polecenie

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    W systemie Windows plik wykonywalny będzie miał wartość `mongorestore.exe` . Należy podać *hosta*, *port*, *nazwę użytkownika* i *hasło* na podstawie zebranych wcześniej poświadczeń Azure Cosmos DB. 
1. **Monitoruj** dane wyjściowe terminalu z *mongorestore*. Powinna zostać wyświetlona pozycja drukowania wierszy do aktualizacji terminalu w przypadku stanu migracji:

    ![Zrzut ekranu przedstawiający wywołanie mongorestore.](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Na koniec Zbadaj Azure Cosmos DB, aby **sprawdzić** , czy migracja zakończyła się pomyślnie. Otwórz Portal Azure Cosmos DB i przejdź do Eksplorator danych. Powinna zostać wyświetlona (1), że utworzono bazę danych *EDX* z kolekcją *importedQuery* , a (2) *importedQuery* powinna zawierać *cały* zestaw danych z kolekcji źródłowej:

    ![Zrzut ekranu przedstawiający sprawdzanie Cosmos DB danych mongorestore.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Optymalizacja po migracji

Po przeprowadzeniu migracji danych przechowywanych w bazie danych MongoDB Azure Cosmos DB do interfejsu API programu MongoDB, można nawiązać połączenie z Azure Cosmos DB i zarządzać danymi. Można również wykonać inne czynności optymalizacji po migracji, takie jak optymalizacja zasad indeksowania, aktualizowanie domyślnego poziomu spójności lub Konfigurowanie dystrybucji globalnej dla konta Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz artykuł [Optymalizacja po migracji](../cosmos-db/mongodb-post-migration.md) .

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Informacje o usłudze Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)
* [Dokumentacja narzędzi bazy danych MongoDB](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Następne kroki

* Wskazówki dotyczące innych scenariuszy migracji można znaleźć w dokumencie [Przewodnik po migracji baz danych](https://datamigration.microsoft.com/) opracowanym przez firmę Microsoft.