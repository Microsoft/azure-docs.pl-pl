---
title: 'Samouczek: Migrowanie SQL Server do wystąpienia zarządzanego SQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację z SQL Server lokalnie do wystąpienia zarządzanego Azure SQL Database przy użyciu Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 240e0a6469f16adee971492e70d88479c6a0bbd9
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020918"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Samouczek: Migrowanie SQL Server do wystąpienia zarządzanego Azure SQL Database w trybie offline za pomocą usługi DMS

Za pomocą Azure Database Migration Service można migrować bazy danych z wystąpienia lokalnego SQL Server do [wystąpienia zarządzanego Azure SQL Database](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Dodatkowe metody, które mogą wymagać ręcznej pracy, są dostępne w artykule [Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

W tym samouczku przeprowadzisz migrację bazy danych **Adventureworks2012** z wystąpienia lokalnego SQL Server do wystąpienia zarządzanego SQL Database przy użyciu Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> - Utwórz wystąpienie usługi Azure Database Migration Service.
> - Utwórz projekt migracji przy użyciu Azure Database Migration Service.
> - Uruchamianie migracji.
> - Monitorowanie migracji.
> - Pobieranie raportu migracji.

> [!IMPORTANT]
> W przypadku migracji w trybie offline z SQL Server do SQL Database wystąpienia zarządzanego Azure Database Migration Service można utworzyć pliki kopii zapasowej. Alternatywnie można podać najnowszą pełną kopię zapasową bazy danych w udziale sieciowym SMB, który będzie używany przez usługę do migrowania baz danych. Nie dołączaj wielu kopii zapasowych do jednego nośnika kopii zapasowej; Wykonaj każdą kopię zapasową w osobnym pliku kopii zapasowej. Należy pamiętać, że można również użyć skompresowanych kopii zapasowych, aby zmniejszyć prawdopodobieństwo wystąpienia potencjalnych problemów z migracją dużych kopii zapasowych.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migrację w trybie offline z SQL Server do wystąpienia zarządzanego SQL Database. Aby przeprowadzić migrację w trybie online, zobacz [migrowanie SQL Server do wystąpienia zarządzanego Azure SQL Database w trybie online za pomocą usługi DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Utwórz Microsoft Azure Virtual Network dla Azure Database Migration Service przy użyciu modelu wdrażania Azure Resource Manager, który zapewnia łączność między lokacjami z lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Informacje o topologii sieci dla Azure SQL Database migracji wystąpień zarządzanych przy użyciu Azure Database Migration Service](https://aka.ms/dmsnetworkformi). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zapoznaj się z [dokumentacją Virtual Network](https://docs.microsoft.com/azure/virtual-network/), a w szczególności artykuły szybkiego startu z szczegółowymi szczegółami.

    > [!NOTE]
    > Jeśli podczas konfigurowania sieci wirtualnej używasz usługi ExpressRoute z usługą Komunikacja równorzędna z firmą Microsoft, Dodaj następujące [punkty końcowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) do podsieci, w której zostanie zainicjowana usługa:
    > - Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, Cosmos DB punkt końcowy itd.)
    > - Punkt końcowy magazynu
    > - Punkt końcowy usługi Service Bus
    >
    > Ta konfiguracja jest konieczna, ponieważ Azure Database Migration Service brak łączności z Internetem.

- Upewnij się, że reguły grupy zabezpieczeń sieci wirtualnej nie blokują następujących portów komunikacji przychodzącej do Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na temat filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu źródłowej bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz Zaporę systemu Windows, aby umożliwić Azure Database Migration Service dostęp do SQL Server źródłowej, która domyślnie jest portem TCP 1433.
- Jeśli używasz wielu nazwanych wystąpień SQL Server przy użyciu portów dynamicznych, możesz włączyć usługę SQL Browser i zezwolić na dostęp do portu UDP 1434 za pośrednictwem zapór, aby Azure Database Migration Service mógł nawiązać połączenie z nazwanym wystąpieniem na serwerze źródłowym.
- Jeśli używasz urządzenia zapory przed źródłowymi bazami danych, może być konieczne dodanie reguł zapory, aby umożliwić Azure Database Migration Service dostęp do źródłowych baz danych na potrzeby migracji, a także plików za pośrednictwem portu SMB 445.
- Utworzenie wystąpienia zarządzanego usługi Azure SQL Database — szczegółowe instrukcje znajdują się w artykule [Tworzenie wystąpienia zarządzanego usługi Azure SQL Database w witrynie Azure Portal](https://aka.ms/sqldbmi).
- Upewnienie się, że dane logowania używane do połączenia źródłowego programu SQL Server i docelowego wystąpienia zarządzanego należą do roli administratora systemu serwera.

    >[!NOTE]
    >Domyślnie Azure Database Migration Service obsługuje tylko Migrowanie nazw logowania SQL. Można jednak włączyć możliwość migracji nazw logowania systemu Windows przez:
    >
    >- Upewnienie się, że obiekt docelowy SQL Database wystąpienia zarządzanego ma dostęp do odczytu usługi AAD, który można skonfigurować za pośrednictwem Azure Portal przez użytkownika z rolą **administratora firmy**lub **administratora globalnego**.
    >- Konfigurowanie wystąpienia Azure Database Migration Service, aby umożliwić migrację logowania użytkownika/grupy systemu Windows, która jest konfigurowana za pośrednictwem Azure Portal na stronie Konfiguracja. Po włączeniu tego ustawienia należy ponownie uruchomić usługę, aby zmiany zaczęły obowiązywać.
    >
    > Po ponownym uruchomieniu usługi, logowania użytkownika/grupy systemu Windows są wyświetlane na liście nazw logowania dostępnych do migracji. W przypadku wszystkich migrowanych kont użytkowników/grup systemu Windows zostanie wyświetlony monit o podanie skojarzonej nazwy domeny. Konta użytkowników usługi (konto z nazwą domeny NT AUTHORITY) i wirtualne konta użytkowników (nazwa konta z usługą nazw domen NT) nie są obsługiwane.

- Utwórz udział sieciowy, który Azure Database Migration Service może używać do tworzenia kopii zapasowej źródłowej bazy danych.
- Upewnienie się, że konto usługi z uruchomionym źródłowym wystąpieniem programu SQL Server ma uprawnienia w utworzonym udziale sieciowym oraz że konto komputera serwera źródłowego ma uprawnienia odczytu i zapisu do tego samego udziału.
- Zapisanie nazwy i hasła użytkownika systemu Windows, który ma uprawnienia do pełnej kontroli nad wcześniej utworzonym udziałem sieciowym. Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage w celu wykonania operacji przywracania.
- Utworzenie kontenera obiektów blob i pobranie jego identyfikatora URI sygnatury dostępu współdzielonego przez wykonanie czynności opisanych w artykule [Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Musisz wybrać wszystkie uprawnienia (odczyt, zapis, usuwanie, wyświetlanie listy) w oknie zasad podczas tworzenia identyfikatora URI sygnatury dostępu współdzielonego. Ten Szczegóły zawiera Azure Database Migration Service z dostępem do kontenera konta magazynu na potrzeby przekazywania plików kopii zapasowej używanych do migrowania baz danych do Azure SQL Database wystąpienia zarządzanego.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

    ![Wyświetlanie subskrypcji w portalu](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie Azure Database Migration Service, a następnie wybierz pozycję **dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Wyszukaj pozycję migracja, a następnie po prawej stronie **programu Microsoft. datamigration**wybierz pozycję **zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj pozycję **Azure Database Migration Service**, a następnie wybierz pozycję **Azure Database Migration Service** z listy rozwijanej.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi DMS.

5. Wybierz istniejącą sieć wirtualną lub utwórz ją.

    Sieć wirtualna zapewnia Azure Database Migration Service z dostępem do SQL Server źródłowych i docelowym Azure SQL Database wystąpieniem zarządzanym.

    Aby uzyskać więcej informacji na temat sposobu tworzenia sieci wirtualnej w Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu Azure Portal](https://aka.ms/DMSVnet).

    Aby uzyskać dodatkowe informacje, zobacz artykuł [topologie sieci dla migracji wystąpienia zarządzanego usługi Azure SQL dB przy użyciu Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Tworzenie usługi DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia usługi znajdź je w witrynie Azure Portal, otwórz je, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdź wszystkie wystąpienia Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia, a następnie wybierz to wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Wystąpienie zarządzane usługi Azure SQL Database**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie offline**.

   ![Tworzenie projektu usługi DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia TLS szyfrowane przy użyciu certyfikatu z podpisem własnym nie zapewniają silnych zabezpieczeń. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na protokole TLS przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Wybierz pozycję **Zapisz**.

4. Na ekranie **Wybierz źródłowe bazy danych** wybierz bazę danych **Adventureworks2012** na potrzeby migracji.

   ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Jeśli używasz usługi SQL Server Integration Services (SSIS), usługa DMS nie obsługuje obecnie migrowania bazy danych katalogu dla projektów/pakietów usługi SSIS (SSISDB) z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database. Możesz jednak aprowizować usługę SSIS w usłudze Azure Data Factory (ADF) i ponownie wdrożyć projekty/pakiety usługi SSIS w docelowej bazie danych SSISDB hostowanej przez wystąpienie zarządzane usługi Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla lokalizacji docelowej, czyli dla aprowizowanego wcześniej wystąpienia zarządzanego usługi Azure SQL Database, do którego jest migrowana baza danych **AdventureWorks2012**.

    Jeśli jeszcze nie zainicjowano obsługi administracyjnej SQL Database wystąpienia zarządzanego, wybierz [link](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) , aby ułatwić obsługę administracyjną wystąpienia. Nadal możesz kontynuować tworzenie projektu, a następnie, gdy Azure SQL Database wystąpienie zarządzane jest gotowe, Wróć do tego konkretnego projektu, aby przeprowadzić migrację.

    ![Wybieranie obiektu docelowego](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Wybierz pozycję **Zapisz**.

## <a name="select-source-databases"></a>Wybieranie źródłowych baz danych

1. Na ekranie **Wybierz źródłowe bazy danych** wybierz źródłową bazę danych, którą chcesz zmigrować.

    ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Wybierz pozycję **Zapisz**.

## <a name="select-logins"></a>Wybieranie identyfikatorów logowania

1. Na ekranie **Wybierz identyfikatory logowania** wybierz identyfikatory logowania, które chcesz zmigrować.

    >[!NOTE]
    >Domyślnie Azure Database Migration Service obsługuje tylko Migrowanie nazw logowania SQL. Aby włączyć obsługę migracji logowań systemu Windows, zobacz sekcję **wymagania wstępne** w tym samouczku.

    ![Wybieranie identyfikatorów logowania](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Wybierz pozycję **Zapisz**.

## <a name="configure-migration-settings"></a>Konfigurowanie ustawień migracji

1. Na ekranie **Konfiguruj ustawienia migracji** podaj następujące informacje:

    | | |
    |--------|---------|
    |**Wybierz opcję tworzenia kopii zapasowej źródła** | Wybierz opcję **Dostarczę najnowsze pliki kopii zapasowej**, jeśli pełne pliki kopii zapasowej są już dostępne do użycia przez usługę DMS na potrzeby migracji bazy danych. Wybierz opcję **Umożliwię usłudze Azure Database Migration Service utworzenie plików kopii zapasowej**, jeśli usługa DMS ma pobrać pełną kopię zapasową źródłowej bazy danych i użyć jej na potrzeby migracji. |
    |**Udział lokalizacji sieciowej** | Lokalny udział sieciowy SMB, który Azure Database Migration Service może pobrać kopie zapasowe źródłowej bazy danych do programu. Konto usługi uruchamiające źródłowe wystąpienie programu SQL Server musi mieć uprawnienia do zapisu w tym udziale sieciowym. Podaj nazwę FQDN lub adresy IP serwera w udziale sieciowym, na przykład „\\\nazwa_serwera.nazwa_domeny.com\folder_kopii_zapasowych” lub „\\\adres_IP\folder_kopii_zapasowych”.|
    |**Nazwa użytkownika** | Upewnij się, że użytkownik systemu Windows ma uprawnienia pełnej kontroli w udziale sieciowym, który podano powyżej. Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage w celu wykonania operacji przywracania. W przypadku wybrania baz danych obsługujących szyfrowanie TDE użytkownik systemu Windows musi korzystać z wbudowanego konta administratora, a [kontrola konta użytkownika](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) musi być wyłączona, aby usługa Azure Database Migration Service mogła przekazywać i usuwać pliki certyfikatów. |
    |**Hasło** | Hasło użytkownika. |
    |**Ustawienia konta magazynu** | Identyfikator URI sygnatury dostępu współdzielonego, który zapewnia Azure Database Migration Service z dostępem do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej i służy do migrowania baz danych do Azure SQL Database wystąpienia zarządzanego. [Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu współdzielonego dla kontenera obiektów blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Ustawienia szyfrowania TDE** | Jeśli migrujesz źródłowe bazy danych z włączonym Transparent Data Encryption (TDE), musisz mieć uprawnienia do zapisu w docelowym Azure SQL Database wystąpieniu zarządzanym.  Wybierz z menu rozwijanego subskrypcję, w której aprowizowano wystąpienie zarządzane usługi Azure SQL Database.  Wybierz docelowe **wystąpienie zarządzane usługi Azure SQL Database** z menu rozwijanego. |

    ![Konfigurowanie ustawień migracji](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Wybierz pozycję **Zapisz**.

## <a name="review-the-migration-summary"></a>Przeglądanie podsumowania migracji

1. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

2. Rozwiń sekcję **Opcja weryfikacji**, aby wyświetlić ekran **Wybierz opcję weryfikacji**, określ, czy przeprowadzić weryfikację bazy danych po migracji pod kątem poprawności zapytań, a następnie wybierz przycisk **Zapisz**.

3. Przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

    ![Podsumowanie projektu migracji](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Wybierz pozycję **Zapisz**.

## <a name="run-the-migration"></a>Uruchamianie migracji

- Wybierz polecenie **Uruchom migrację**.

  Zostanie wyświetlone okno działanie migracji, a stan działania to **oczekujące**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybierz pozycję **Odśwież**, aby zaktualizować wyświetlane dane.

   ![Działanie migracji w toku](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Możesz rozwinąć kategorie baz danych i danych logowania, aby monitorować stan migracji odpowiednich obiektów serwera.

   ![Działanie migracji w toku](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Po zakończeniu migracji wybierz polecenie **Pobierz raport**, aby pobrać raport zawierający szczegółowe informacje o procesie migracji.

3. Upewnij się, że docelowa baza danych znajduje się w docelowym środowisku wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="next-steps"></a>Następne kroki

- Samouczek pokazujący, jak przeprowadzić migrację bazy danych do wystąpienia zarządzanego przy użyciu polecenia przywracania T-SQL, zobacz [przywracanie kopii zapasowej do wystąpienia zarządzanego przy użyciu polecenia Restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Aby uzyskać informacje o wystąpieniu zarządzanym, zobacz [co to jest wystąpienie zarządzane](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
- Aby uzyskać informacje na temat łączenia aplikacji z wystąpieniem zarządzanym, zobacz [łączenie aplikacji](../azure-sql/managed-instance/connect-application-instance.md).
