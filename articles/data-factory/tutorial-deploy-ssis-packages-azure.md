---
title: Aprowizuj środowisko uruchomieniowe integracji platformy Azure-SSIS
description: Dowiedz się, jak aprowizować środowisko Azure SSIS Integration Runtime w usłudze Azure Data Factory w celu wdrażania i uruchamiania pakietów SSIS na platformie Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 91962b578f9620384af86d25a33ba35830abb285
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418630"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Aprowizuj środowisko uruchomieniowe integracji platformy Azure-SSIS w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ten samouczek zawiera kroki dotyczące korzystania z witryny Azure portal w celu aprowizowania środowiska uruchomieniowego integracji usługi Azure-SQL Server Integration Services (IR) w usłudze Azure Data Factory. Azure-SSIS IR obsługuje:

- Uruchamianie pakietów wdrożonych w katalogu SSIS (SSISDB) obsługiwanych przez serwer bazy danych SQL platformy Azure lub wystąpienie zarządzane (model wdrażania projektu).
- Uruchamianie pakietów wdrożonych w systemach plików, udziałach plików lub usłudze Azure Files (Model wdrażania pakietów). 

Po zainicjowaniu obsługi administracyjnej usługi Azure-SSIS IR można użyć znanych narzędzi do wdrażania i uruchamiania pakietów na platformie Azure. Narzędzia te obejmują narzędzia SQL Server Data Tools (SSDT), SQL Server Management `dtinstall`Studio `dtutil`(SSMS) oraz narzędzia wiersza polecenia, takie jak , i `dtexec`.

Aby uzyskać informacje koncepcyjne dotyczące środowisk Azure SSIS IR, zobacz [Omówienie środowiska Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie fabryki danych.
> * Aprowizowanie środowiska Azure-SSIS Integration Runtime.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
- **Serwer bazy danych SQL azure (opcjonalnie)**. Jeśli nie masz jeszcze serwera bazy danych, utwórz go w witrynie Azure Portal, zanim zaczniesz. Fabryka danych z kolei utworzy wystąpienie SSISDB na tym serwerze bazy danych. 

  Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja umożliwia rejestrowania wykonywania zapisu środowiska wykonawczego integracji do usługi SSISDB bez przekraczania regionów platformy Azure.

  Należy pamiętać o następujących kwestiach:

  - Na podstawie wybranego serwera bazy danych wystąpienie SSISDB można utworzyć w twoim imieniu jako pojedynczą bazę danych, jako część puli elastycznej lub w wystąpieniu zarządzanym. Można go udostępnić w sieci publicznej lub dołączając do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do obsługi SSISDB, zobacz [Porównanie pojedynczej bazy danych usługi Azure SQL Database, puli elastycznej i wystąpienia zarządzanego.](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) 
  
    Jeśli używasz serwera bazy danych SQL azure z regułami zapory IP/punktów końcowych usługi sieci wirtualnej lub wystąpienia zarządzanego z prywatnym punktem końcowym do hostowania SSISDB lub jeśli potrzebujesz dostępu do danych lokalnych bez konfigurowania samodzielnego środowiska IR, musisz dołączyć do usługi Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie usługi Azure-SSIS IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. To ustawienie nie ma zastosowania, gdy używany jest serwer bazy danych Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpienie zarządzane z prywatnym punktem końcowym do hostowania usługi SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Dodaj adres IP komputera klienckiego lub zakres adresów IP, który zawiera adres IP komputera klienckiego, do listy adresów IP klienta w ustawieniach zapory serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md).

  - Można połączyć się z serwerem bazy danych przy użyciu uwierzytelniania SQL przy użyciu poświadczeń administratora serwera lub przy użyciu uwierzytelniania usługi Azure AD z tożsamością zarządzana dla fabryki danych. W przypadku tej ostatniej należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Tworzenie usługi Azure-SSIS IR przy użyciu uwierzytelniania usługi Azure AD.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

  - Upewnij się, że serwer bazy danych nie ma jeszcze wystąpienia SSISDB. Inicjowanie obsługi administracyjnej usługi Azure-SSIS IR nie obsługuje przy użyciu istniejącego wystąpienia SSISDB.


> [!NOTE]
> Aby uzyskać listę regionów platformy Azure, w których fabryka danych i usługa Azure-SSIS IR są obecnie dostępne, zobacz [Fabryka danych i dostępność SSIS IR według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Aby utworzyć fabrykę danych za pośrednictwem witryny Azure portal, postępuj zgodnie z instrukcjami krok po kroku w [obszarze Tworzenie fabryki danych za pośrednictwem interfejsu użytkownika.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) Wybierz **pozycję Przypnij do pulpitu nawigacyjnego,** aby umożliwić szybki dostęp po jego utworzeniu. 

Po utworzeniu fabryki danych otwórz jej stronę przeglądu w witrynie Azure portal. Wybierz kafelek **Autor & Monitor,** aby otworzyć stronę **Rozpocznij pracę** na osobnej karcie. Tam można kontynuować tworzenie usługi Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Tworzenie środowiska Azure SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Z poziomu omówienia usługi Data Factory

1. Na stronie **Wprowadzenie** wybierz kafelek **Konfigurowanie środowiska SSIS Integration Runtime**. 

   ![Kafelek „Konfigurowanie środowiska SSIS Integration Runtime”](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Aby poznać pozostałe kroki konfigurowania środowiska Azure-SSIS IR, zobacz sekcję [Aprowizowanie środowiska Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Z autorskiego interfejsu użytkownika

1. W interfejsie użytkownika usługi Azure Data Factory przełącz się na kartę **Edytowanie** i wybierz pozycję **Połączenia**. Następnie przełącz się do **integration runtimes** kartę, aby wyświetlić istniejące środowiska wykonawcze integracji w fabryce danych. 

   ![Opcje wyboru dotyczące wyświetlania istniejących środowisk IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Wybierz pozycję **Nowe**, aby utworzyć środowisko Azure-SSIS IR. 

   ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. W panelu **Ustawienia środowiska wykonawczego integracji** wybierz **istniejące pakiety SSIS do wykonania na** kafelku Platformy Azure, a następnie wybierz pozycję **Dalej**. 

   ![Określanie typu środowiska Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Aby poznać pozostałe kroki konfigurowania środowiska Azure-SSIS IR, zobacz sekcję [Aprowizowanie środowiska Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime

1. W sekcji **Ustawienia ogólne** panelu Ustawienia środowiska **wykonawczego integracji** wykonaj następujące czynności. 

   ![Ustawienia ogólne](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. W polu **Nazwa** wprowadź nazwę środowiska Integration Runtime. 

   1. W polu **Opis** wprowadź opis środowiska Integration Runtime. 

   1. W polu **Lokalizacja** wybierz lokalizację środowiska Integration Runtime. Wyświetlane są tylko obsługiwane lokalizacje. Zalecamy wybranie tej samej lokalizacji do hostowania bazy danych SSISDB co w przypadku serwera bazy danych. 

   1. W polu **Rozmiar węzła** wybierz rozmiar węzła w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane rozmiary węzłów. Wybierz duży rozmiar węzła (skalowanie w górę), jeśli chcesz uruchomić wiele pakietów intensywnie korzystających z mocy obliczeniowej lub intensywnie korzystających z pamięci. 

   1. W polu **Liczba węzłów** wybierz liczbę węzłów w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby węzłów. Wybierz duży klaster z wieloma węzłami (skalowanie w poziomie), jeśli chcesz uruchomić wiele pakietów równolegle. 

   1. W przypadku **wersji/licencji**wybierz wersję PROGRAMU SQL Server dla środowiska wykonawczego integracji: Standardowy lub Enterprise. Wybierz enterprise, jeśli chcesz używać zaawansowanych funkcji w czasie wykonywania integracji. 

   1. Aby **zaoszczędzić pieniądze,** wybierz opcję Korzyści hybrydowe platformy Azure dla środowiska wykonawczego integracji: **Tak** lub **Nie**. Wybierz **opcję Tak,** jeśli chcesz wprowadzić własną licencję programu SQL Server z pakietem Software Assurance, aby skorzystać z oszczędności kosztów podczas używania hybrydowego. 

   1. Wybierz opcję **Dalej**. 

1. W sekcji **Ustawienia SQL** wykonaj następujące kroki. 

   ![Ustawienia SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Wybierz pole wyboru **Utwórz katalog SSIS (SSISDB) hostowany przez serwer/wystąpienie zarządzanej bazy danych SQL platformy Azure do przechowywania projektów/pakietów/środowisk/dzienników wykonywania,** aby wybrać model wdrażania pakietów do uruchomienia na platformie Azure-SSIS IR. Wybierz model wdrażania projektu, w którym pakiety są wdrażane w SSISDB hostowane przez serwer bazy danych, albo model wdrażania pakietów, w którym pakiety są wdrażane w systemach plików, udziałach plików lub plikach Azure.
   
      Jeśli zaznaczysz to pole wyboru, musisz przynieść własny serwer bazy danych do hosta SSISDB, który utworzymy i będziemy zarządzać w Twoim imieniu.
   
      1. W polu **Subskrypcja** określ subskrypcję platformy Azure, z której korzysta Twój serwer bazy danych hostujący bazę danych SSISDB. 

      1. W polu **Lokalizacja** określ lokalizację serwera bazy danych hostującego bazę danych SSISDB. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime.

      1. W polu **Punkt końcowy serwera bazy danych wykazu** wybierz punkt końcowy serwera bazy danych hostującego bazę danych SSISDB. 
   
         Na podstawie wybranego serwera bazy danych wystąpienie SSISDB można utworzyć w twoim imieniu jako pojedynczą bazę danych, jako część puli elastycznej lub w wystąpieniu zarządzanym. Można go udostępnić w sieci publicznej lub dołączając do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do obsługi SSISDB, zobacz [Porównanie pojedynczej bazy danych usługi Azure SQL Database, puli elastycznej i wystąpienia zarządzanego.](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)   

         Jeśli wybierzesz serwer bazy danych SQL azure z regułami zapory IP/punktów końcowych usługi sieci wirtualnej lub wystąpienie zarządzane z prywatnym punktem końcowym do obsługi SSISDB lub jeśli potrzebujesz dostępu do danych lokalnych bez konfigurowania samodzielnie hostowanego środowiska IR, musisz dołączyć do usługi Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie usługi Azure-SSIS IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

      1. Zaznacz pole wyboru **Użyj uwierzytelniania AAD z tożsamością zarządzaną dla usługi ADF,** aby wybrać metodę uwierzytelniania serwera bazy danych do hostowania bazy danych SSISDB. Wybierz uwierzytelnianie SQL lub uwierzytelnianie usługi Azure AD z tożsamością zarządzana dla fabryki danych.

         Jeśli zaznaczysz to pole wyboru, musisz dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Tworzenie usługi Azure-SSIS IR przy użyciu uwierzytelniania usługi Azure AD.](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
   
      1. W przypadku **nazwy użytkownika administratora**wprowadź nazwę użytkownika uwierzytelniania SQL dla serwera bazy danych, aby hostować usługę SSISDB. 

      1. W przypadku **hasła administratora**wprowadź hasło uwierzytelniania SQL dla serwera bazy danych, aby hostować usługę SSISDB. 

      1. W przypadku **warstwy usługi bazy danych wykazu**wybierz warstwę usług dla serwera bazy danych do hostowania bazy danych SSISDB. Wybierz warstwę Podstawowa, Standardowa lub Premium lub wybierz nazwę puli elastycznej.

      1. Wybierz **opcję Testuj połączenie**. Jeśli test zakończy się pomyślnie, wybierz przycisk **Dalej**. 

1. W sekcji **Ustawienia zaawansowane** wykonaj następujące czynności. 

   ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. W przypadku **maksymalnych równoległych wykonań na węzeł**wybierz maksymalną liczbę pakietów do uruchomienia jednocześnie na węzeł w klastrze środowiska wykonawczego integracji. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz małą liczbę, jeśli chcesz użyć więcej niż jednego rdzenia do uruchomienia pojedynczego dużego pakietu, który jest wymagający mocy obliczeniowej lub pamięci. Wybierz dużą liczbę, jeśli chcesz uruchomić jeden lub więcej małych pakietów w jednym rdzeniu. 

   1. Zaznacz pole wyboru **Dostosuj środowisko uruchomieniowe integracji platformy Azure-SSIS za pomocą dodatkowych konfiguracji systemu/instalacji składników,** aby wybrać, czy chcesz dodać ustawienia niestandardowe standardowe/ekspresowe w usłudze Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Ustawienia niestandardowe dla usługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   1. Wybierz **pozycję Wybierz sieć wirtualną dla środowiska uruchomieniowego integracji platformy Azure-SSIS, aby dołączyć, zezwolić usłudze ADF na tworzenie pewnych zasobów sieciowych i opcjonalnie przywieźć własne statyczne publiczne adresy IP,** aby wybrać, czy chcesz dołączyć do sieci wirtualnej usługi Azure-SSIS IR.

      Wybierz go, jeśli używasz serwera bazy danych SQL usługi Azure z regułami zapory IP/punktów końcowych usługi sieci wirtualnej lub wystąpienia zarządzanego z prywatnym punktem końcowym do hostowania SSISDB lub jeśli potrzebujesz dostępu do danych lokalnych bez konfigurowania samodzielnego środowiska IR. Aby uzyskać więcej informacji, zobacz [Tworzenie usługi Azure-SSIS IR w sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   1. Wybierz pole wyboru **Konfigurowanie środowiska uruchomieniowego integracji hostowanego jako serwera proxy dla środowiska uruchomieniowego integracji platformy Azure-SSIS,** aby wybrać, czy chcesz skonfigurować samodzielnie hostowany podczerwony środowisko IR jako serwer proxy dla usługi Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Konfigurowanie samodzielnego podczerwonyego podczerwone jako serwera proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

   1. Wybierz przycisk **Kontynuuj**. 

1. W sekcji **Podsumowanie** przejrzyj wszystkie ustawienia inicjowania obsługi administracyjnej, dobieraj łącza do zalecanej dokumentacji i wybierz pozycję **Zakończ,** aby rozpocząć tworzenie środowiska wykonawczego integracji. 

   > [!NOTE]
   > Z wyłączeniem dowolnego czasu instalacji niestandardowej, ten proces powinien zakończyć się w ciągu 5 minut.
   >
   > Jeśli używasz SSISDB, usługa Data Factory połączy się z serwerem bazy danych w celu przygotowania bazy danych SSISDB. 
   > 
   > Podczas aprowizowania azure-SSIS IR, dostęp redystrybucyjny i pakiet funkcji Platformy Azure dla SSIS są również zainstalowane. Te składniki zapewniają łączność z plikami programu Excel, plikami programu Access i różnymi źródłami danych platformy Azure, oprócz źródeł danych, które są już obsługiwane przez wbudowane składniki. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Ustawienia niestandardowe dla usługi Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Na karcie **Połączenia** w razie potrzeby przełącz na opcję **Środowiska Integration Runtime**. Wybierz pozycję **Odśwież**, aby odświeżyć stan. 

   ![Stan tworzenia i przycisk „Odśwież”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Za pomocą linków w kolumnie **Akcje** możesz zatrzymać/uruchomić, edytować lub usunąć środowisko Integration Runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane. 

   ![Linki w kolumnie „Akcje”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz SSISDB, można wdrożyć pakiety do niego i uruchomić je na platformie Azure-SSIS IR przy użyciu narzędzi SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS). Narzędzia te łączą się z serwerem bazy danych za pośrednictwem punktu końcowego serwera: 

- W przypadku serwera usługi Azure SQL Database `<server name>.database.windows.net`format punktu końcowego serwera to .
- W przypadku wystąpienia zarządzanego z prywatnym punktem `<server name>.<dns prefix>.database.windows.net`końcowym format punktu końcowego serwera to .
- W przypadku wystąpienia zarządzanego z publicznym punktem `<server name>.public.<dns prefix>.database.windows.net,3342`końcowym format punktu końcowego serwera to . 

Jeśli nie używasz SSISDB, możesz wdrożyć pakiety w systemach plików, udziałach plików lub plikach Azure `dtinstall` `dtutil`i `dtexec` uruchomić je na platformie Azure-SSIS IR przy użyciu narzędzi wiersza polecenia , i wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Wdrażanie pakietów SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

W obu przypadkach można również uruchomić wdrożone pakiety na platformie Azure-SSIS IR przy użyciu działania Wykonywanie pakietu SSIS w potokach fabryki danych. Aby uzyskać więcej informacji, zobacz [Wywoływanie wykonywania pakietu SSIS jako działania pierwszej klasy usługi Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Zobacz także następującą dokumentację SSIS: 

- [Wdrażanie, uruchamianie i monitorowanie pakietów SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Łączenie się z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Planowanie wykonywania pakietów na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o dostosowywaniu środowiska Azure-SSIS Integration Runtime, przejdź do następującego artykułu: 

> [!div class="nextstepaction"]
> [Dostosowywanie identyfikatora IR usługi Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)