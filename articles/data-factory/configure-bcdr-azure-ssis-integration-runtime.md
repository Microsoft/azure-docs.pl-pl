---
title: Konfigurowanie środowiska Azure-SSIS Integration Runtime w celu zapewnienia ciągłości działania i odzyskiwania po awarii (BCDR)
description: W tym artykule opisano sposób konfigurowania środowiska Azure-SSIS Integration Runtime w Azure Data Factory z grupą trybu failover wystąpienia Azure SQL Database/zarządzanego w celu zapewnienia ciągłości działania i odzyskiwania po awarii (BCDR).
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: 2744d51b6d68ed494050be10a9f0e4d1f59cdc49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204069"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Konfigurowanie środowiska Azure-SSIS Integration Runtime w celu zapewnienia ciągłości działania i odzyskiwania po awarii (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure SQL Database/wystąpienie zarządzane i SQL Server Integration Services (SSIS) w Azure Data Factory (ADF) można łączyć jako zalecane rozwiązanie PaaS (All-Platform as a Service) do migracji SQL Server. Możesz wdrożyć projekty SSIS w bazie danych wykazu usług SSIS (SSISDB) hostowanej przez wystąpienie Azure SQL Database/zarządzane i uruchamiać pakiety usług SSIS na platformie Azure SSIS Integration Runtime (IR) w podajniku ADF.

W celu zapewnienia ciągłości działania i odzyskiwania po awarii (BCDR) można skonfigurować wystąpienie Azure SQL Database/zarządzane z [grupą replikacji geograficznej/trybu failover](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview), gdzie SSISDB w podstawowym regionie platformy Azure z dostępem do odczytu i zapisu (rola podstawowa) będzie stale replikowana do regionu pomocniczego z dostępem tylko do odczytu (rola pomocnicza). Gdy wystąpi awaria w regionie podstawowym, zostanie wyzwolone przejście w tryb failover, gdzie podstawowa i pomocnicza SSISDBs zastąpią role.

W przypadku usługi BCDR można również skonfigurować podwójną, niegotową parę IR platformy Azure SSIS, która działa w synchronizacji z grupą trybu failover wystąpienia Azure SQL Database/zarządzanego. Dzięki temu można mieć parę uruchomionych usług Azure-SSIS IRs, które w danym momencie tylko jeden z nich może uzyskać dostęp do podstawowego SSISDB do pobierania i wykonywania pakietów, a także zapisywać dzienniki wykonywania pakietów (rolę podstawową), podczas gdy inne mogą wykonywać te same czynności dla pakietów wdrożonych w innym miejscu, na przykład w Azure Files (rola pomocnicza). W przypadku przełączenia SSISDB w tryb failover podstawowy i pomocniczy urząd platformy Azure-SSIS będą również zamieniać role

W tym artykule opisano sposób konfigurowania Azure-SSIS IR z grupą trybu failover wystąpienia Azure SQL Database/zarządzanego dla BCDR.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Skonfiguruj podwójną parę Azure-SSIS IR w stanie wstrzymania z grupą Azure SQL Database trybu failover

Aby skonfigurować podwójną parę Azure-SSIS IR w stanie wstrzymania, która działa w synchronizacji z Azure SQL Database grupą trybu failover, wykonaj następujące czynności.

1. Za pomocą interfejsu użytkownika usługi Azure Portal/ADF można utworzyć nowe Azure-SSIS IR z podstawowym serwerem Azure SQL Database, aby hostować SSISDB w regionie podstawowym. Jeśli masz istniejące Azure-SSIS IR, które są już dołączone do SSIDB hostowanego przez podstawowy serwer Azure SQL Database i nadal działa, należy najpierw go zatrzymać. Będzie to podstawowy Azure-SSIS IR.

   Gdy wybierzesz opcję [używania SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) na stronie **Ustawienia wdrożenia** w okienku **Konfiguracja środowiska Integration Runtime** , zaznacz również pole wyboru **Użyj podwójnej integracji Azure-SSIS Integration Runtime z trybem failover SSISDB** . Dla **podwójnej nazwy pary nazw**, wprowadź nazwę identyfikującą parę podstawowego i pomocniczego urzędu skarbowego Azure-SSIS. Po zakończeniu tworzenia Azure-SSIS IR podstawowej zostanie on uruchomiony i dołączony do podstawowego SSISDB, który zostanie utworzony w Twoim imieniu przy użyciu dostępu do odczytu i zapisu. Jeśli konfiguracja została właśnie skonfigurowana, należy ją uruchomić ponownie.

1. Za pomocą Azure Portal można sprawdzić, czy podstawowy SSISDB został utworzony na stronie **Przegląd** podstawowego serwera Azure SQL Database. Po jego utworzeniu można [utworzyć grupę trybu failover dla serwerów podstawowych i pomocniczych Azure SQL Database i dodać do niej SSISDB](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#2---create-the-failover-group) na stronie **grupy trybu failover** . Po utworzeniu grupy trybu failover można sprawdzić, czy główny SSISDB został zreplikowany do pomocniczej bazy danych z dostępem tylko do odczytu na stronie **Przegląd** na serwerze pomocniczym Azure SQL Database.

1. Za pomocą interfejsu użytkownika usługi Azure Portal/ADF można utworzyć kolejną Azure-SSIS IR z serwerem pomocniczym Azure SQL Database, aby hostować SSISDB w regionie pomocniczym. Będzie to Azure-SSIS IR pomocniczy. W celu uzyskania pełnej BCDR upewnij się, że wszystkie zasoby, od których zależy, zostały również utworzone w regionie pomocniczym, na przykład Azure Storage do przechowywania niestandardowych skryptów/plików instalacyjnych, ADF dla aranżacji/planowania wykonywania pakietów itp.

   Gdy wybierzesz opcję [używania SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) na stronie **Ustawienia wdrożenia** w okienku **Konfiguracja środowiska Integration Runtime** , zaznacz również pole wyboru **Użyj podwójnej integracji Azure-SSIS Integration Runtime z trybem failover SSISDB** . Dla **podwójnej nazwy pary**, wprowadź tę samą nazwę, aby zidentyfikować parę podstawowego i pomocniczego urzędu skarbowego Azure-SSIS. Po zakończeniu tworzenia Azure-SSIS IR dodatkowej zostanie on uruchomiony i dołączony do pomocniczej SSISDB.

1. Jeśli chcesz mieć bliski czas przestoju w przypadku SSISDB trybu failover, Zachowaj oba z nich. Tylko podstawowy Azure-SSIS IR może uzyskać dostęp do podstawowego SSISDB do pobierania i wykonywania pakietów, a także do zapisywania dzienników wykonywania pakietów, podczas gdy pomocniczy Azure-SSIS IR może wykonać te same dla pakietów wdrożonych w innym miejscu, na przykład w Azure Files.

   Jeśli chcesz zminimalizować koszt działania, możesz zatrzymać Azure-SSIS IR pomocniczy po jego utworzeniu. W przypadku przełączenia SSISDB w tryb failover podstawowy i pomocniczy urząd Azure-SSIS (IRs) zastąpią role. Jeśli podstawowa Azure-SSIS IR jest zatrzymana, należy uruchomić ją ponownie. W zależności od tego, czy jest on wprowadzany do sieci wirtualnej, a użyta metoda wstrzykiwania zostanie wykonana w ciągu 5 minut lub około 20-30 minut na uruchomienie.

1. W przypadku [korzystania z podajnika APD na potrzeby aranżacji/planowania pakietów](./how-to-invoke-ssis-package-ssis-activity.md)należy upewnić się, że wszystkie powiązane potoki ADF z działaniami pakietu SSIS i skojarzonymi wyzwalaczami są kopiowane do pomocniczego modułu ADF z wyłączonymi wyzwalaczami. Gdy SSISDB tryb failover, należy je włączyć.

1. Możesz [przetestować grupę Azure SQL Database trybu failover](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#3---test-failover) i sprawdzić [Azure-SSIS IR stronę monitorowania w portalu ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) , niezależnie od tego, czy podstawowy i pomocniczy urząd Azure-SSIS ma zamienione role. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Konfigurowanie podwójnej pary Azure-SSIS IR w stanie wstrzymania z grupą trybu failover wystąpienia zarządzanego Azure SQL

Aby skonfigurować podwójną parę Azure-SSIS IR w stanie wstrzymania, która działa w synchronizacji z grupą trybu failover wystąpienia zarządzanego Azure SQL, wykonaj następujące czynności.

1. Za pomocą Azure Portal można [utworzyć grupę trybu failover dla podstawowych i pomocniczych wystąpień usługi Azure SQL](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal) na stronie **grupy trybu failover** w podstawowym wystąpieniu zarządzanym Azure SQL.

1. Za pomocą interfejsu użytkownika usługi Azure Portal/ADF można utworzyć nowe Azure-SSIS IR z podstawowym wystąpieniem zarządzanym Azure SQL, aby hostować SSISDB w regionie podstawowym. Jeśli masz istniejące Azure-SSIS IR, które są już dołączone do usługi SSIDB hostowanej przez główne wystąpienie zarządzane Azure SQL, a nadal działa, musisz najpierw go zatrzymać. Będzie to podstawowy Azure-SSIS IR.

   Gdy wybierzesz opcję [używania SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) na stronie **Ustawienia wdrożenia** w okienku **Konfiguracja środowiska Integration Runtime** , zaznacz również pole wyboru **Użyj podwójnej integracji Azure-SSIS Integration Runtime z trybem failover SSISDB** . Dla **podwójnej nazwy pary nazw**, wprowadź nazwę identyfikującą parę podstawowego i pomocniczego urzędu skarbowego Azure-SSIS. Po zakończeniu tworzenia Azure-SSIS IR podstawowej zostanie on uruchomiony i dołączony do podstawowego SSISDB, który zostanie utworzony w Twoim imieniu przy użyciu dostępu do odczytu i zapisu. Jeśli konfiguracja została właśnie skonfigurowana, należy ją uruchomić ponownie. Możesz również sprawdzić, czy podstawowy SSISDB został zreplikowany do pomocniczej bazy danych z dostępem tylko do odczytu na stronie **Przegląd** w ramach pomocniczego wystąpienia zarządzanego Azure SQL.

1. Za pomocą interfejsu użytkownika usługi Azure Portal/ADF można utworzyć kolejną Azure-SSIS IR z pomocniczym wystąpieniem zarządzanym Azure SQL, aby hostować SSISDB w regionie pomocniczym. Będzie to Azure-SSIS IR pomocniczy. W celu uzyskania pełnej BCDR upewnij się, że wszystkie zasoby, od których zależy, zostały również utworzone w regionie pomocniczym, na przykład Azure Storage do przechowywania niestandardowych skryptów/plików instalacyjnych, ADF dla aranżacji/planowania wykonywania pakietów itp.

   Gdy wybierzesz opcję [używania SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) na stronie **Ustawienia wdrożenia** w okienku **Konfiguracja środowiska Integration Runtime** , zaznacz również pole wyboru **Użyj podwójnej integracji Azure-SSIS Integration Runtime z trybem failover SSISDB** . Dla **podwójnej nazwy pary**, wprowadź tę samą nazwę, aby zidentyfikować parę podstawowego i pomocniczego urzędu skarbowego Azure-SSIS. Po zakończeniu tworzenia Azure-SSIS IR dodatkowej zostanie on uruchomiony i dołączony do pomocniczej SSISDB.

1. Wystąpienie zarządzane usługi Azure SQL może zabezpieczać poufne dane w bazach danych, takich jak SSISDB, przez szyfrowanie ich przy użyciu klucza głównego bazy danych (DMK). DMK sam jest domyślnie szyfrowany przy użyciu klucza głównego usługi (klucza). W momencie pisania Grupa trybu failover wystąpienia zarządzanego Azure SQL nie replikuje klucza z podstawowego wystąpienia zarządzanego Azure SQL, dlatego nie można odszyfrować DMK i z usługi SSISDB w pomocniczym wystąpieniu programu Azure SQL po przejściu w tryb failover. Aby obejść ten sposób, można dodać szyfrowanie hasła dla DMK, które ma zostać odszyfrowane w pomocniczym wystąpieniu zarządzanym usługi Azure SQL. Za pomocą programu SSMS wykonaj następujące czynności.

   1. Uruchom następujące polecenie dla SSISDB w podstawowym wystąpieniu zarządzanym Azure SQL, aby dodać hasło do szyfrowania DMK.

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. Uruchom następujące polecenie dla SSISDB w podstawowym i pomocniczym wystąpieniu zarządzanym Azure SQL, aby dodać nowe hasło do odszyfrowywania DMK.

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. Jeśli chcesz mieć bliski czas przestoju w przypadku SSISDB trybu failover, Zachowaj oba z nich. Tylko podstawowy Azure-SSIS IR może uzyskać dostęp do podstawowego SSISDB do pobierania i wykonywania pakietów, a także do zapisywania dzienników wykonywania pakietów, podczas gdy pomocniczy Azure-SSIS IR może wykonać te same dla pakietów wdrożonych w innym miejscu, na przykład w Azure Files.

   Jeśli chcesz zminimalizować koszt działania, możesz zatrzymać Azure-SSIS IR pomocniczy po jego utworzeniu. W przypadku przełączenia SSISDB w tryb failover podstawowy i pomocniczy urząd Azure-SSIS (IRs) zastąpią role. Jeśli podstawowa Azure-SSIS IR jest zatrzymana, należy uruchomić ją ponownie. W zależności od tego, czy jest on wprowadzany do sieci wirtualnej, a użyta metoda wstrzykiwania zostanie wykonana w ciągu 5 minut lub około 20-30 minut na uruchomienie.

1. Jeśli [używasz agenta wystąpienia zarządzanego usługi Azure SQL na potrzeby aranżacji/planowania pakietów](./how-to-invoke-ssis-package-managed-instance-agent.md), upewnij się, że wszystkie powiązane zadania SSIS z ich etapami i skojarzonymi harmonogramami są kopiowane do pomocniczego wystąpienia zarządzanego usługi Azure SQL z początkowo wyłączonymi harmonogramami. Za pomocą programu SSMS wykonaj następujące czynności.

   1. Dla każdego zadania SSIS kliknij prawym przyciskiem myszy i wybierz elementy menu rozwijanego **zadanie skryptu jako**, **Utwórz do** i **nowe okno edytora zapytań** w celu wygenerowania jego skryptu.

      ![Generuj skrypt zadania SSIS](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. Dla każdego wygenerowanego skryptu zadania programu SSIS Znajdź polecenie wykonywania `sp_add_job` procedury składowanej, a następnie zmodyfikuj/usuń przypisanie wartości do `@owner_login_name` argumentu w razie potrzeby.

   1. Dla każdego zaktualizowanego skryptu zadania programu SSIS należy uruchomić go na pomocniczym wystąpieniu zarządzanym usługi Azure SQL, aby skopiować zadanie wraz z jego etapami zadania i skojarzonymi harmonogramami.

   1. Korzystając z następującego skryptu, można utworzyć nowe zadanie T-SQL w celu włączenia/wyłączenia harmonogramów zadań SSIS opartych na podstawowej lub pomocniczej roli SSISDB, zarówno w podstawowym, jak i pomocniczym wystąpieniu usługi Azure SQL. Gdy SSISDB tryb failover, harmonogramy zadań SSIS, które zostały wyłączone, zostaną włączone i na odwrót.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. W przypadku [korzystania z podajnika APD na potrzeby aranżacji/planowania pakietów](./how-to-invoke-ssis-package-ssis-activity.md)należy upewnić się, że wszystkie powiązane potoki ADF z działaniami pakietu SSIS i skojarzonymi wyzwalaczami są kopiowane do pomocniczego modułu ADF z wyłączonymi wyzwalaczami. Gdy SSISDB tryb failover, należy je włączyć.

1. Możesz [przetestować grupę trybu failover wystąpienia zarządzanego usługi Azure SQL](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal#test-failover) i zapoznaj się ze [stroną monitorowania Azure-SSIS IR w portalu ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) , czy podstawowy i pomocniczy urząd usługi Azure-SSIS ma zamienione role. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Dołącz nowy Azure-SSIS IR do istniejącego SSISDB hostowanego przez wystąpienie Azure SQL Database/Managed

Jeśli wystąpi awaria i ma wpływ na istniejące Azure-SSIS IR, ale nie Azure SQL Database/wystąpienia zarządzanego w tym samym regionie, można zastąpić go nowym w innym regionie. Aby dołączyć istniejące SSISDB hostowane przez wystąpienie Azure SQL Database/zarządzane do nowego Azure-SSIS IR, wykonaj następujące czynności.

1. Jeśli istniejący Azure-SSIS IR nadal działa, należy go zatrzymać najpierw za pomocą interfejsu użytkownika usługi Azure Portal/ADF lub Azure PowerShell. Jeśli awaria również ma wpływ na funkcję ADF w tym samym regionie, można pominąć ten krok.

1. Za pomocą programu SSMS Uruchom następujące polecenie dla SSISDB w wystąpieniu Azure SQL Database/zarządzanym, aby zaktualizować metadane zezwalające na połączenia z nowego ADF/Azure-SSIS IR.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. Za pomocą [interfejsu użytkownika usługi Azure Portal/ADF](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) lub [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)Utwórz nowy moduł ADF/Azure-SSIS IR o nazwie *YourNewADF* / *YourNewAzureSSISIR* odpowiednio w innym regionie. Jeśli używasz interfejsu użytkownika usługi Azure Portal/ADF, możesz zignorować błąd połączenia testowego na stronie **Ustawienia wdrożenia** w okienku **Konfiguracja środowiska Integration Runtime** .

## <a name="next-steps"></a>Następne kroki

Można wziąć pod uwagę następujące inne opcje konfiguracji Azure-SSIS IR:

- [Konfigurowanie magazynów pakietów dla Azure-SSIS IR](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Skonfiguruj niestandardowe ustawienia Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Skonfiguruj iniekcję sieci wirtualnej dla Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Skonfiguruj własne środowisko IR jako serwer proxy dla Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)
