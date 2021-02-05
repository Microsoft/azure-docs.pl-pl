---
title: 'Samouczek: Migruj MongoDB offline do Azure Cosmos DB API for MongoDB'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację z MongoDB lokalnego Azure Cosmos DB do interfejsu API usługi MongoDB w trybie offline przy użyciu Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 1ba6a45062f4018c59f5b41ab616f7a04f87140a
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575575"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Samouczek: Migrowanie MongoDB Azure Cosmos DB do interfejsu API usługi MongoDB w trybie offline za pomocą usługi DMS

Za pomocą Azure Database Migration Service można przeprowadzić migrację baz danych w trybie offline (jednorazowym) z wystąpienia lokalnego lub w chmurze MongoDB do Azure Cosmos DB interfejsu API MongoDB.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
>
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.

W tym samouczku przeprowadzisz migrację zestawu danych w MongoDB hostowanym na maszynie wirtualnej platformy Azure do interfejsu API Azure Cosmos DB dla MongoDB przy użyciu Azure Database Migration Service. Jeśli nie masz jeszcze skonfigurowanego źródła bazy danych MongoDB, zobacz artykuł [Install and configure MongoDB on a Windows VM in Azure (Instalowanie i konfigurowanie bazy danych MongoDB na maszynie wirtualnej z systemem Windows na platformie Azure)](../virtual-machines/windows/install-mongodb.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Wykonaj kroki sprzed migracji](../cosmos-db/mongodb-pre-migration.md) , takie jak szacowanie przepływności, wybór klucza partycji i zasad indeksowania.
* [Utworzenie konta interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu Azure Resource Manager model wdrażania, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](../virtual-network/index.yml), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.

    > [!NOTE]
    > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z usługą Komunikacja równorzędna z firmą Microsoft, Dodaj następujące [punkty końcowe](../virtual-network/virtual-network-service-endpoints-overview.md) do podsieci, w której zostanie zainicjowana usługa:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy itd.)
    > * Punkt końcowy magazynu
    > * Punkt końcowy usługi Service Bus
    >
    > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service brak łączności z Internetem.

* Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej (sieciowej grupy zabezpieczeń) nie blokują następujących portów komunikacji: 53, 443, 445, 9354 i 10000-20000. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Otwórz Zaporę systemu Windows, aby umożliwić Azure Database Migration Service dostęp do źródłowego serwera MongoDB, który domyślnie jest portem TCP 27017.
* W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych może zajść potrzeba dodania reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji.

## <a name="configure-azure-cosmos-db-server-side-retries-for-efficient-migration"></a>Skonfiguruj ponowną próbę po stronie serwera Azure Cosmos DB na potrzeby wydajnej migracji

Klienci migrowani z MongoDB Azure Cosmos DB mogą korzystać z funkcji zarządzania zasobami, co gwarantuje, że możliwość pełnego wykorzystania zainicjowanej usługi RU/s przepływności. Azure Cosmos DB może ograniczać dane żądania usługi migracji danych w trakcie migracji, jeśli to żądanie przekroczy zarezerwowany kontener RU/s; Następnie należy ponowić próbę żądania. Usługa migracji danych jest w stanie wykonać ponowne próby, ale czas rundy dotyczący przeskoku sieci między usługą migracji danych i Azure Cosmos DB ma wpływ na ogólny czas odpowiedzi tego żądania. Skrócenie czasu odpowiedzi dla żądań ograniczających może skrócić łączny czas wymagany do migracji. Funkcja *ponawiania po stronie serwera* Azure Cosmos DB umożliwia usłudze przechwycenie kodów błędów ograniczania przepustowości i ponawianie próby w przypadku znacznie mniejszego czasu rundy, znacznie poprawiając czas odpowiedzi na żądania.

Funkcję ponawiania po stronie serwera można znaleźć w bloku *funkcje* portalu Azure Cosmos DB

![Funkcja SSR MongoDB](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

A jeśli jest *wyłączona*, zalecamy włączenie tego, jak pokazano poniżej

![MongoDB — Włącz](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Wyświetlanie subskrypcji w portalu](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration** wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie Azure Database Migration Service. 

5. Wybierz istniejącą sieć wirtualną lub Utwórz nową.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do źródłowego wystąpienia MongoDB i docelowego konta Azure Cosmos DB.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Konfigurowanie ustawień wystąpienia usługi Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znajdź ją w witrynie Azure Portal, otwórz ją, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Znajdź wszystkie wystąpienia Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na ekranie **usługi Azure Database Migration Services** Wyszukaj nazwę utworzonego wystąpienia Azure Database Migration Service, a następnie wybierz wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **MongoDB**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **CosmosDB (interfejs API MongoDB)**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie offline**. 

    ![Tworzenie projektu usługi Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła** określ szczegóły połączenia dla źródłowego serwera MongoDB.

   > [!IMPORTANT]
   > Azure Database Migration Service nie obsługuje Azure Cosmos DB jako źródła.

    Istnieją trzy tryby nawiązywania połączenia ze źródłem:
   * **Tryb standardowy**, który akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP, numer portu i poświadczenia połączenia.
   * **Tryb parametrów połączenia**, który akceptuje parametry połączenia bazy danych MongoDB zgodnie z opisem znajdującym się w artykule [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/) (Format identyfikatora URI parametrów połączenia).
   * **Tryb danych z usługi Azure Storage**, który akceptuje adres URL sygnatury dostępu współdzielonego kontenera obiektów blob. Zaznacz pole wyboru **Obiekt blob zawiera zrzuty BSON**, jeśli kontener obiektów blob zawiera zrzuty BSON utworzone przez [narzędzie bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/) bazy danych MongoDB, lub usuń zaznaczenie tego pola wyboru, jeśli kontener zawiera pliki JSON.

     Jeśli wybierzesz tę opcję, upewnij się, że parametry połączenia konta magazynu mają następujący format:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Te parametry połączenia SAS kontenera obiektów BLOB można znaleźć w Eksploratorze usługi Azure Storage. Utworzenie sygnatury dostępu współdzielonego dla danego kontenera spowoduje udostępnienie adresu URL w powyższym żądanym formacie.
     
     Na podstawie informacji zrzutu typu w usłudze Azure Storage należy również pamiętać o następujących kwestiach.

     * W przypadku zrzutów BSON dane w kontenerze obiektów blob muszą mieć format bsondump, tak aby pliki danych były umieszczone w folderach o nazwach zgodnych z odpowiednimi bazami danych w formacie kolekcja.bson. Pliki metadanych (jeśli istnieją) powinny mieć nazwy w formacie *kolekcja*.metadata.json.

     * W przypadku zrzutów JSON pliki w kontenerze obiektów blob muszą być umieszczone w folderach o nazwach zgodnych z odpowiednimi bazami danych. W każdym folderze bazy danych pliki danych muszą być umieszczone w podfolderze o nazwie „data” i mieć nazwy w formacie *kolekcja*.json. Pliki metadanych (jeśli istnieją) muszą być umieszczone w podfolderze o nazwie „metadata” i mieć nazwy w takim samym formacie *kolekcja*.json. Pliki metadanych muszą mieć taki sam format jak pliki tworzone przez narzędzie bsondump bazy danych MongoDB.

    > [!IMPORTANT]
    > Nie zaleca się używania certyfikatu z podpisem własnym na serwerze Mongo. Jeśli jednak jest używany, Połącz się z serwerem przy użyciu **trybu parametrów połączenia** i upewnij się, że parametry połączenia mają ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Możesz również użyć adresu IP, jeśli rozpoznawanie nazw DNS nie jest możliwe.

   ![Określanie szczegółów źródła](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla docelowego konta usługi Azure Cosmos DB, czyli dla aprowizowanego wcześniej konta interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB, na które są migrowane dane MongoDB.

    ![Określanie szczegółów elementu docelowego](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Wybierz pozycję **Zapisz**.

## <a name="map-to-target-databases"></a>Mapowanie do docelowych baz danych

1. Na ekranie **Mapowanie do docelowych baz danych** określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelową bazę danych.

    Jeśli ciąg **tworzenia** pojawia się obok nazwy bazy danych, oznacza to, że Azure Database Migration Service nie znalazł docelowej bazy danych i usługa utworzy bazę danych.

    Na tym etapie migracji możesz [zaaprowizować przepływność](../cosmos-db/set-throughput.md). W usłudze Cosmos DB można aprowizować przepływność na poziomie bazy danych lub na poziomie poszczególnych kolekcji. Przepływność jest mierzona w [jednostkach żądań](../cosmos-db/request-units.md) (RU). Dowiedz się więcej o [cenniku usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mapowanie do docelowych baz danych](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Wybierz pozycję **Zapisz**.
3. Na ekranie **Ustawienia kolekcji** rozwiń listę kolekcji, a następnie przejrzyj listę kolekcji, które będą migrowane.

    Azure Database Migration Service wybiera wszystkie kolekcje istniejące w źródłowym wystąpieniu MongoDB, które nie istnieją na docelowym koncie Azure Cosmos DB. Jeśli chcesz ponownie przeprowadzić migrację kolekcji, które już zawierają dane, musisz jawnie wybrać te kolekcje w tym bloku.

    Możesz określić liczbę jednostek RU używanych przez kolekcje. Azure Database Migration Service sugeruje inteligentne wartości domyślne na podstawie rozmiaru kolekcji.

    > [!NOTE]
    > Przeprowadź migrację i kolekcję baz danych równolegle przy użyciu wielu wystąpień Azure Database Migration Service, jeśli to konieczne, aby przyspieszyć działanie.

    Możesz również wskazać klucz fragmentu, aby skorzystać z [partycjonowania w usłudze Azure Cosmos DB](../cosmos-db/partitioning-overview.md), które zapewnia optymalną skalowalność. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wybierania klucza fragmentu/klucza partycji](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Wybieranie tabel kolekcji](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Wybierz pozycję **Zapisz**.

5. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

    ![Podsumowanie migracji](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

    Zostanie wyświetlone okno działania migracji, a **stan** działania będzie określony jako **Nierozpoczęte**.

    ![Stan działania](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji

* Na ekranie działania migracji wybieraj polecenie **Odśwież**, aby zaktualizować ekran, aż do momentu, gdy **Stan** migracji zmieni się na **Ukończono**.

   > [!NOTE]
   > Po wybraniu działania można uzyskać szczegółowe informacje o metrykach migracji na poziomie bazy danych i kolekcji.

    ![Stan działania — ukończono](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Weryfikowanie danych w usłudze Cosmos DB

* Po zakończeniu migracji możesz sprawdzić konto usługi Azure Cosmos DB, aby upewnić się, że wszystkie kolekcje zostały zmigrowane pomyślnie.

    ![Zrzut ekranu pokazujący, gdzie sprawdzić konto Azure Cosmos DB, aby sprawdzić, czy wszystkie kolekcje zostały pomyślnie zmigrowane.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optymalizacja po migracji

Po przeprowadzeniu migracji danych przechowywanych w bazie danych MongoDB Azure Cosmos DB do interfejsu API programu MongoDB, można nawiązać połączenie z Azure Cosmos DB i zarządzać danymi. Można również wykonać inne czynności optymalizacji po migracji, takie jak optymalizacja zasad indeksowania, aktualizowanie domyślnego poziomu spójności lub Konfigurowanie dystrybucji globalnej dla konta Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz artykuł [Optymalizacja po migracji](../cosmos-db/mongodb-post-migration.md) .

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Informacje o usłudze Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Następne kroki

* Wskazówki dotyczące innych scenariuszy migracji można znaleźć w dokumencie [Przewodnik po migracji baz danych](https://datamigration.microsoft.com/) opracowanym przez firmę Microsoft.