---
title: Migrowanie pakietów usług SSIS do wystąpienia zarządzanego SQL
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak migrować pakiety SQL Server Integration Services (SSIS) i projekty do wystąpienia zarządzanego usługi Azure SQL przy użyciu Azure Database Migration Service lub Data Migration Assistant.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: e3e2aa055baf3dfb4bee0629040fc7c140844637
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094019"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-managed-instance"></a>Migrowanie pakietów SQL Server Integration Services do wystąpienia zarządzanego usługi Azure SQL
Jeśli używasz SQL Server Integration Services (SSIS) i chcesz migrować projekty SSIS/pakiety z SSISDB źródłowego hostowanego przez SQL Server do SSISDB docelowego hostowanego przez wystąpienie zarządzane Azure SQL, możesz użyć Azure Database Migration Service.

Jeśli używana wersja usług SSIS jest wcześniejsza niż 2012 lub używasz typów magazynu pakietów innych niż SSISDB, przed migracją projektów i pakietów SSIS należy je przekonwertować przy użyciu Kreatora konwersji projektów usług Integration Services, który można także uruchomić z programu SSMS. Aby uzyskać więcej informacji, zobacz artykuł [konwertowanie projektów na model wdrażania projektu](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) obecnie nie obsługuje Azure SQL Database jako docelowej docelowej migracji. Aby ponownie wdrożyć projekty usług SSIS/pakiety do Azure SQL Database, zapoznaj się z artykułem [ponowne wdrażanie pakietów SQL Server Integration Services do Azure SQL Database](./how-to-migrate-ssis-packages.md).

W tym artykule omówiono sposób wykonywania następujących zadań:
> [!div class="checklist"]
>
> * Oceń źródłowe projekty SSIS/pakiety.
> * Migruj projekty SSIS/pakiety na platformę Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te kroki, potrzebne są:

* Aby utworzyć Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](../expressroute/expressroute-introduction.md) lub [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Aby uzyskać więcej informacji, zobacz artykuł [topologie sieci dla migracji wystąpienia zarządzanego SQL przy użyciu Azure Database Migration Service]( https://aka.ms/dmsnetworkformi). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](../virtual-network/index.yml), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.
* Aby upewnić się, że reguły grupy zabezpieczeń sieci wirtualnej nie blokują portu wychodzącego 443 z ServiceTag dla ServiceBus, Storage i AzureMonitor. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Aby skonfigurować [zaporę systemu Windows na potrzeby dostępu do aparatu dla źródłowej bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017).
* Aby otworzyć Zaporę systemu Windows w celu umożliwienia Azure Database Migration Service dostępu do SQL Server źródłowej, domyślnie jest to port TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowych baz danych podczas migracji oraz do plików za pośrednictwem portu SMB 445.
* Wystąpienie zarządzane SQL do hostowania SSISDB. Jeśli musisz ją utworzyć, postępuj zgodnie ze szczegółowymi informacjami w artykule [Tworzenie wystąpienia zarządzanego Azure SQL](../azure-sql/managed-instance/instance-create-quickstart.md).
* Aby upewnić się, że nazwy logowania używane do łączenia źródłowego SQL Server i docelowego wystąpienia zarządzanego są członkami roli serwera sysadmin.
* Aby sprawdzić, czy program SSIS został zainicjowany w Azure Data Factory (ADF) zawierający Azure-SSIS Integration Runtime (IR) z miejscem docelowym SSISDB hostowanym przez wystąpienie zarządzane SQL (zgodnie z opisem w artykule [Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory](../data-factory/create-azure-ssis-integration-runtime.md)).

## <a name="assess-source-ssis-projectspackages"></a>Oceń źródłowe projekty SSIS/pakiety

Ocena źródłowej SSISDB nie jest jeszcze zintegrowana z bazą Asystent migracji danych (DMA), dlatego projekty i pakiety usług SSIS zostaną ocenione/zweryfikowane w miarę ich ponownego wdrożenia w miejscu docelowym SSISDB hostowanym w wystąpieniu zarządzanym usługi Azure SQL.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

    ![Wyświetlanie subskrypcji w portalu](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration** wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj pozycję **Azure Database Migration Service**, a następnie wybierz pozycję **Azure Database Migration Service** z listy rozwijanej.

     ![Azure Marketplace](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi DMS.

5. Wybierz istniejącą sieć wirtualną lub utwórz ją.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do SQL Server źródłowej i docelowym wystąpieniem zarządzanym usługi Azure SQL.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).

    Aby uzyskać dodatkowe informacje, zobacz artykuł [topologie sieci dla migracji wystąpienia zarządzanego usługi Azure SQL przy użyciu Azure Database Migration Service](./resource-network-topologies.md).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Tworzenie usługi DMS](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia usługi znajdź je w witrynie Azure Portal, otwórz je, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia, a następnie wybierz to wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** Określ nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz opcję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **wystąpienie zarządzane Azure SQL**, a następnie wybierz **Typ działania** wybierz pozycję **migracja pakietu SSIS**.

   ![Tworzenie projektu usługi DMS](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia TLS szyfrowane przy użyciu certyfikatu z podpisem własnym nie zapewniają silnych zabezpieczeń. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na protokole TLS przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **szczegóły lokalizacji docelowej migracji** Określ szczegóły połączenia dla elementu docelowego.

     ![Szczegóły elementu docelowego](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. Wybierz pozycję **Zapisz**.

## <a name="review-the-migration-summary"></a>Przeglądanie podsumowania migracji

1. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

2. W przypadku **projektów SSIS i opcji zastępowania środowisk** należy określić, czy mają być zastępowane lub ignorowane istniejące projekty i środowiska usług SSIS.

    ![Podsumowanie projektu migracji](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. Przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

## <a name="run-the-migration"></a>Uruchamianie migracji

* Wybierz polecenie **Uruchom migrację**.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się ze wskazówkami dotyczącymi migracji w [przewodniku migracji bazy danych](https://datamigration.microsoft.com/)firmy Microsoft.