---
title: 'Samouczek: Migruj MongoDB offline do Azure Cosmos DB API for MongoDB'
titleSuffix: Azure Database Migration Service
description: Migrowanie z MongoDB lokalnego Azure Cosmos DB do interfejsu API usługi MongoDB w trybie offline przy użyciu Azure Database Migration Service.
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
ms.openlocfilehash: 8977bb90087d9d3d4962d7eda50903d97da93539
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443871"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Samouczek: Migrowanie MongoDB Azure Cosmos DB do interfejsu API usługi MongoDB w trybie offline

Użyj Azure Database Migration Service do przeprowadzenia w trybie offline, jednorazowej migracji baz danych z lokalnego lub w chmurze wystąpienia MongoDB do interfejsu API Azure Cosmos DB dla MongoDB.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> * Uruchamianie migracji.
> * Monitoruj migrację.

W tym samouczku przeprowadzisz migrację zestawu danych w MongoDB, który jest hostowany na maszynie wirtualnej platformy Azure. Za pomocą Azure Database Migration Service można migrować zestaw danych do interfejsu API Azure Cosmos DB dla MongoDB. Jeśli nie masz już skonfigurowanego źródła MongoDB, zobacz [Instalowanie i Konfigurowanie MongoDB na maszynie wirtualnej z systemem Windows na platformie Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* [Wykonaj kroki sprzed migracji](../cosmos-db/mongodb-pre-migration.md) , takie jak oszacowanie przepływności i wybór klucza partycji.
* [Utwórz konto dla interfejsu API Azure Cosmos DB dla MongoDB](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu Azure Resource Manager. Ten model wdrażania zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu [usługi Azure ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentację usługi Azure Virtual Network](../virtual-network/index.yml), w szczególności artykuły szybkiego startu, zawierające szczegółowe informacje.

    > [!NOTE]
    > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z usługą Komunikacja równorzędna z firmą Microsoft, Dodaj następujące [punkty końcowe](../virtual-network/virtual-network-service-endpoints-overview.md) do podsieci, w której zostanie zainicjowana usługa:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL lub Azure Cosmos DB punkt końcowy)
    > * Punkt końcowy magazynu
    > * Punkt końcowy usługi Service Bus
    >
    > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service brak łączności z Internetem.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) dla sieci wirtualnej nie blokują następujących portów komunikacyjnych: 53, 443, 445, 9354 i 10000-20000. Aby uzyskać więcej informacji, zobacz [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Otwórz Zaporę systemu Windows, aby umożliwić Azure Database Migration Service dostęp do źródłowego serwera MongoDB, który domyślnie jest portem TCP 27017.
* Jeśli używasz urządzenia zapory przed źródłową bazą danych, może być konieczne dodanie reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowej bazy danych na potrzeby migracji.

## <a name="configure-the-server-side-retry-feature"></a>Skonfiguruj funkcję ponawiania po stronie serwera

Możesz skorzystać z możliwości zarządzania zasobami w przypadku migrowania z programu MongoDB do Azure Cosmos DB. Korzystając z tych możliwości, można w pełni wykorzystać jednostki żądań (RU/s) w zakresie przepływności. Azure Cosmos DB może ograniczać konkretne Database Migration Service żądania w trakcie migracji, jeśli to żądanie przekroczy zarezerwowane przez kontenery RU/s. Następnie należy ponowić próbę żądania.

Database Migration Service jest w stanie wykonać ponownych prób. Ważne jest, aby zrozumieć, że czas błądzenia związany z przeskokiem sieci między Database Migration Service i Azure Cosmos DB ma wpływ na ogólny czas odpowiedzi tego żądania. Skrócenie czasu odpowiedzi dla żądań ograniczających może skrócić łączny czas wymagany do migracji.

Funkcja ponawiania po stronie serwera Azure Cosmos DB umożliwia usłudze przechwycenie kodów błędów ograniczania przepustowości i ponowienie próby za pomocą znacznie mniejszego czasu rundy, znacznie poprawiając czas odpowiedzi na żądanie.

Aby użyć ponowienia strony serwera, w portalu Azure Cosmos DB wybierz pozycję **funkcje**  >  **serwer ponów próbę**.

![Zrzut ekranu pokazujący, gdzie znaleźć funkcję ponawiania po stronie serwera.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Jeśli funkcja jest wyłączona, wybierz pozycję **Włącz**.

![Zrzut ekranu pokazujący sposób włączania ponowień po stronie serwera.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>Rejestrowanie dostawcy zasobów

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

   ![Zrzut ekranu przedstawiający subskrypcje portalu.](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Zrzut ekranu przedstawiający dostawców zasobów.](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration** wybierz pozycję **zarejestruj**.

    ![Zrzut ekranu pokazujący sposób rejestrowania dostawcy zasobów.](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Tworzenie wystąpienia

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz usługę **Azure Database Migration Service** na liście rozwijanej.

    ![Zrzut ekranu przedstawiający witrynę Azure Marketplace.](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Zrzut ekranu pokazujący sposób tworzenia wystąpienia Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. W obszarze **Tworzenie usługi migracji** Określ nazwę usługi, subskrypcję i nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie Azure Database Migration Service. 

5. Wybierz istniejącą sieć wirtualną lub Utwórz nową.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do źródłowego wystąpienia MongoDB i docelowego konta Azure Cosmos DB.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Zrzut ekranu pokazujący ustawienia konfiguracji wystąpienia Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi Znajdź ją w Azure Portal i otwórz ją. Następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

      ![Zrzut ekranu pokazujący sposób lokalizowania wszystkich wystąpień Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Na ekranie **usługi Azure Database Migration Services** Wyszukaj nazwę utworzonego wystąpienia Azure Database Migration Service, a następnie wybierz wystąpienie.

3. Wybierz pozycję **+ Nowy projekt migracji**.

4. W obszarze **Nowy projekt migracji** Określ nazwę projektu, a następnie w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **MongoDB**. W polu tekstowym **Typ serwera docelowego** wybierz pozycję **COSMOSDB (MongoDB API)**, a następnie wybierz **Typ działania**, a następnie wybierz pozycję **migracja danych w trybie offline**. 

    ![Zrzut ekranu przedstawiający opcje projektu.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Wybierz polecenie **Utwórz i uruchom działanie**, aby utworzyć projekt i uruchomić działanie migracji.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła** określ szczegóły połączenia dla źródłowego serwera MongoDB.

   > [!IMPORTANT]
   > Azure Database Migration Service nie obsługuje Azure Cosmos DB jako źródła.

    Istnieją trzy tryby nawiązywania połączenia ze źródłem:
   * **Tryb standardowy**, który akceptuje w pełni kwalifikowaną nazwę domeny lub adres IP, numer portu i poświadczenia połączenia.
   * **Tryb parametrów połączenia**, który akceptuje parametry połączenia MongoDB zgodnie z opisem w [formacie identyfikatora URI parametrów połączenia](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Tryb danych z usługi Azure Storage**, który akceptuje adres URL sygnatury dostępu współdzielonego kontenera obiektów blob. Wybierz pozycję **BLOB zawiera zrzuty BSON** , jeśli kontener obiektów BLOB zawiera zrzuty BSON utworzone przez [Narzędzie MongoDB bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/). Nie wybieraj tej opcji, jeśli kontener zawiera pliki JSON.

     W przypadku wybrania tej opcji upewnij się, że parametry połączenia konta magazynu są wyświetlane w następującym formacie:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Te parametry połączenia sygnatury dostępu współdzielonego kontenera obiektów BLOB można znaleźć w Eksploratorze usługi Azure Storage. Utworzenie sygnatury dostępu współdzielonego dla danego kontenera zapewnia adres URL w żądanym formacie.
     
     Na podstawie informacji zrzutu typu w usłudze Azure Storage należy również pamiętać o następujących kwestiach:

     * W przypadku zrzutów BSON dane w kontenerze obiektów BLOB muszą być w formacie bsondump. Umieść pliki danych w folderach o nazwach znajdujących się po zawierających je w formacie *Collection. BSON*. Nazwij wszystkie pliki metadanych przy użyciu formatu *collection.metadata.json*.

     * W przypadku zrzutów JSON pliki w kontenerze obiektów blob muszą być umieszczone w folderach o nazwach zgodnych z odpowiednimi bazami danych. W każdym folderze bazy danych pliki danych muszą być umieszczone w podfolderze o nazwie *dane* i nazwane przy użyciu formatu *collection.json*. Umieść wszystkie pliki metadanych w podfolderze o nazwie *Metadata* i nazwanych przy użyciu tego samego formatu, *collection.json*. Pliki metadanych muszą mieć taki sam format jak pliki tworzone przez narzędzie bsondump bazy danych MongoDB.

    > [!IMPORTANT]
    > Nie zalecamy używania certyfikatu z podpisem własnym na serwerze MongoDB. Jeśli musisz użyć jednej z nich, Połącz się z serwerem przy użyciu trybu parametrów połączenia i upewnij się, że parametry połączenia mają znaki cudzysłowu ("").
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Adresu IP można również użyć w sytuacjach, w których rozpoznawanie nazw DNS nie jest możliwe.

   ![Zrzut ekranu pokazujący Określanie szczegółów źródła.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **szczegóły lokalizacji docelowej migracji** Określ szczegóły połączenia dla docelowego konta Azure Cosmos DB. To konto jest wstępnie zainicjowanym interfejsem API Azure Cosmos DB dla konta MongoDB, do którego migrujesz dane MongoDB.

    ![Zrzut ekranu pokazujący Określanie szczegółowych informacji o miejscu docelowym.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Wybierz pozycję **Zapisz**.

## <a name="map-to-target-databases"></a>Mapowanie do docelowych baz danych

1. Na ekranie **Mapowanie do docelowych baz danych** określ mapowanie źródłowej i docelowej bazy danych na potrzeby migracji.

    Jeśli docelowa baza danych zawiera tę samą nazwę bazy danych co źródłowa baza danych, Azure Database Migration Service domyślnie wybiera docelową bazę danych.

    Jeśli opcja **Utwórz** pojawia się obok nazwy bazy danych, oznacza to, że Azure Database Migration Service nie znalazła docelowej bazy danych i usługa utworzy bazę danych.

    Na tym etapie migracji możesz [zaaprowizować przepływność](../cosmos-db/set-throughput.md). W Azure Cosmos DB można zainicjować przepływność na poziomie bazy danych lub indywidualnie dla każdej kolekcji. Przepływność jest mierzona w [jednostkach żądania](../cosmos-db/request-units.md). Dowiedz się więcej o [cenniku usługi Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Zrzut ekranu pokazujący mapowanie do docelowych baz danych.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Wybierz pozycję **Zapisz**.
3. Na ekranie **Ustawienia kolekcji** rozwiń listę kolekcji, a następnie przejrzyj listę kolekcji, które będą migrowane.

    Azure Database Migration Service automatycznie wybiera wszystkie kolekcje istniejące w źródłowym wystąpieniu MongoDB, które nie istnieją na docelowym koncie Azure Cosmos DB. Jeśli chcesz ponownie zmigrować kolekcje, które zawierają już dane, musisz jawnie zaznaczyć kolekcje w tym okienku.

    Możesz określić liczbę jednostek RU używanych przez kolekcje. Azure Database Migration Service sugeruje inteligentne wartości domyślne na podstawie rozmiaru kolekcji.

    > [!NOTE]
    > Równoległe migrowanie i zbieranie baz danych. W razie potrzeby można użyć wielu wystąpień Azure Database Migration Service w celu przyspieszenia uruchomienia.

    Możesz również wskazać klucz fragmentu, aby skorzystać z [partycjonowania w usłudze Azure Cosmos DB](../cosmos-db/partitioning-overview.md), które zapewnia optymalną skalowalność. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi wybierania klucza fragmentu/Partition](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Zrzut ekranu pokazujący Wybieranie tabel kolekcji.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Wybierz pozycję **Zapisz**.

5. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

    ![Zrzut ekranu przedstawiający podsumowanie nigration.](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Uruchamianie migracji

Wybierz polecenie **Uruchom migrację**. Zostanie wyświetlone okno działania migracji, a stan działania **nie jest uruchomiony**.

![Zrzut ekranu pokazujący stan działania.](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Monitorowanie migracji

Na ekranie działanie migracji wybierz pozycję **Odśwież** , aby zaktualizować ekran do momentu, gdy stan migracji zostanie wyświetlony jako **zakończony**.

> [!NOTE]
> Można wybrać działanie, aby uzyskać szczegółowe informacje na temat metryk migracji na poziomie bazy danych i kolekcji.

![Screnshot pokazujący stan działania zakończony.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Weryfikowanie danych w Azure Cosmos DB

Po zakończeniu migracji możesz sprawdzić konto Azure Cosmos DB, aby sprawdzić, czy wszystkie kolekcje zostały pomyślnie zmigrowane.

![Zrzut ekranu pokazujący, gdzie sprawdzić konto Azure Cosmos DB, aby sprawdzić, czy wszystkie kolekcje zostały pomyślnie zmigrowane.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optymalizacja po migracji

Po przeprowadzeniu migracji danych przechowywanych w bazie danych MongoDB do interfejsu API Azure Cosmos DB dla MongoDB, można nawiązać połączenie z Azure Cosmos DB i zarządzać danymi. Można również wykonać inne kroki optymalizacji po migracji. Mogą one obejmować optymalizację zasad indeksowania, zaktualizowanie domyślnego poziomu spójności lub skonfigurowanie dystrybucji globalnej dla konta Azure Cosmos DB. Aby uzyskać więcej informacji, zobacz [Optymalizacja po migracji](../cosmos-db/mongodb-post-migration.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się ze wskazówkami dotyczącymi migracji w przypadku dodatkowych scenariuszy w [przewodniku migracji usługi Azure Database](https://datamigration.microsoft.com/).



