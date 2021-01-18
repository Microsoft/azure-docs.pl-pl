---
title: Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory
description: Dowiedz się, jak utworzyć środowisko Azure-SSIS Integration Runtime w Azure Data Factory, aby móc wdrażać i uruchamiać pakiety usług SSIS na platformie Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 62b1575e2ab379e6b4e61926e00dfad85ffeb6c0
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556362"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule przedstawiono procedurę aprowizacji środowiska Integration Runtime (IR) platformy Azure — SQL Server Integration Services (SSIS) w Azure Data Factory (ADF). Azure-SSIS IR obsługuje:

- Uruchomione pakiety wdrożone do wykazu usług SSIS (SSISDB) hostowane przez Azure SQL Database Server/wystąpienie zarządzane (model wdrażania projektu)
- Uruchomione pakiety wdrożone w systemie plików, Azure Files lub SQL Server Database (MSDB) hostowane przez wystąpienie zarządzane Azure SQL (model wdrażania pakietów)

Po zainicjowaniu Azure-SSIS IR można używać dobrze znanych narzędzi do wdrażania i uruchamiania pakietów na platformie Azure. Te narzędzia już obsługują platformę Azure i obejmują SQL Server narzędzia do obsługi danych (SSDT), SQL Server Management Studio (SSMS) i narzędzia wiersza polecenia, takie jak [dtutil](/sql/integration-services/dtutil-utility) i [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

W samouczku [Azure-SSIS IR aprowizacji](./tutorial-deploy-ssis-packages-azure.md) przedstawiono sposób tworzenia Azure-SSIS IR za pomocą aplikacji Azure Portal lub Data Factory. W tym samouczku pokazano również, jak opcjonalnie użyć serwera Azure SQL Database lub wystąpienia zarządzanego do hostowania SSISDB. Ten artykuł rozszerza się w samouczku i opisuje, jak wykonać te opcjonalne zadania:

- Użyj serwera Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB. Jako wymaganie wstępne należy skonfigurować uprawnienia i ustawienia sieci wirtualnej dla Azure-SSIS IR, aby dołączyć do sieci wirtualnej.

- Użyj uwierzytelniania Azure Active Directory (Azure AD) z zarządzaną tożsamością dla fabryki danych, aby nawiązać połączenie z serwerem Azure SQL Database lub wystąpieniem zarządzanym. Jako warunek wstępny należy dodać tożsamość zarządzaną dla fabryki danych jako użytkownika bazy danych, który może utworzyć wystąpienie SSISDB.

- Dołącz do Azure-SSIS IR do sieci wirtualnej lub skonfiguruj własne środowisko IR jako serwer proxy dla Azure-SSIS IR, aby uzyskać dostęp do danych lokalnie.

W tym artykule pokazano, jak zainicjować obsługę Azure-SSIS IR przy użyciu Azure Portal, Azure PowerShell i szablonu Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz jeszcze subskrypcji, możesz utworzyć [bezpłatne konto wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) .

- **Serwer Azure SQL Database lub wystąpienie zarządzane SQL (opcjonalnie)**. Jeśli nie masz jeszcze serwera bazy danych lub wystąpienia zarządzanego, utwórz je w Azure Portal przed rozpoczęciem pracy. Data Factory spowoduje utworzenie wystąpienia SSISDB na tym serwerze bazy danych. 

  Zalecamy utworzenie serwera bazy danych lub wystąpienia zarządzanego w tym samym regionie świadczenia usługi Azure, w którym znajduje się środowisko Integration Runtime. Ta konfiguracja umożliwia wykonywanie dzienników wykonywania zapisu środowiska Integration Runtime w usłudze SSISDB bez przekroczenia regionów świadczenia usługi Azure.

  Należy pamiętać o następujących kwestiach:

  - Wystąpienie SSISDB można utworzyć w Twoim imieniu jako pojedynczą bazę danych w ramach puli elastycznej lub w wystąpieniu zarządzanym. Może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania między SQL Database i wystąpieniem zarządzanym SQL do hostowania SSISDB, zobacz sekcję [porównanie SQL Database i wystąpienia zarządzanego SQL](#comparison-of-sql-database-and-sql-managed-instance) w tym artykule. 
  
    Jeśli używasz serwera Azure SQL Database z regułami zapory protokołu IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym do hostowania SSISDB lub potrzebujesz dostępu do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, musisz dołączyć Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](./join-azure-ssis-integration-runtime-virtual-network.md).

  - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. To ustawienie nie ma zastosowania w przypadku używania serwera Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym do hostowania SSISDB. Aby uzyskać więcej informacji, zobacz [bezpieczny Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz polecenie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Dodaj adres IP komputera klienckiego lub zakres adresów IP, który zawiera adres IP komputera klienckiego, do listy adres IP klienta w ustawieniach zapory dla serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../azure-sql/database/firewall-configure.md).

  - Można nawiązać połączenie z serwerem bazy danych przy użyciu uwierzytelniania SQL z poświadczeniami administratora serwera lub przy użyciu uwierzytelniania usługi Azure AD z zarządzaną tożsamością dla fabryki danych. Dla tych ostatnich należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md).

  - Upewnij się, że serwer bazy danych nie ma już wystąpienia SSISDB. Inicjowanie obsługi Azure-SSIS IR nie obsługuje korzystania z istniejącego wystąpienia SSISDB.

- **Azure Resource Manager sieci wirtualnej (opcjonalnie)**. Należy mieć Azure Resource Manager sieci wirtualnej, jeśli co najmniej jeden z następujących warunków jest spełniony:

  - Hosting SSISDB na serwerze Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym.

  - Chcesz połączyć się z lokalnymi magazynami danych z pakietów SSIS uruchomionych na Azure-SSIS IR bez konfigurowania własnego środowiska IR.

- **Azure PowerShell (opcjonalnie)**. Postępuj zgodnie z instrukcjami w temacie [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps), jeśli chcesz uruchomić skrypt programu PowerShell w celu aprowizacji Azure-SSIS IR.

### <a name="regional-support"></a>Wsparcie regionalne

Aby uzyskać listę regionów świadczenia usługi Azure, w których Data Factory i Azure-SSIS IR są dostępne, zobacz [Data Factory i SSIS IR dostępność według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Porównanie SQL Database i wystąpienia zarządzanego SQL

Poniższa tabela zawiera porównanie niektórych funkcji serwera Azure SQL Database i wystąpienia zarządzanego SQL, które są powiązane z usługą Azure-SSIR IR:

| Cechy | Baza danych SQL| Wystąpienie zarządzane SQL |
|---------|--------------|------------------|
| **Planowanie** | Agent SQL Server jest niedostępny.<br/><br/>Zobacz [Planowanie wykonywania pakietu w potoku Data Factory](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| Agent wystąpienia zarządzanego jest dostępny. |
| **Authentication** | Można utworzyć wystąpienie SSISDB z użytkownikiem zawartej bazy danych, który reprezentuje dowolną grupę usługi Azure AD z zarządzaną tożsamością fabryki danych jako członkiem roli **db_owner** .<br/><br/>Zobacz [Włączanie uwierzytelniania usługi Azure AD, aby utworzyć SSISDB na serwerze Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Można utworzyć wystąpienie SSISDB z użytkownikiem zawartej bazy danych, który reprezentuje zarządzaną tożsamość fabryki danych. <br/><br/>Zobacz [Włączanie uwierzytelniania usługi Azure AD, aby utworzyć SSISDB w wystąpieniu zarządzanym usługi Azure SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Warstwa usług** | Po utworzeniu Azure-SSIS IR z serwerem Azure SQL Database można wybrać warstwę usług dla SSISDB. Istnieje wiele warstw usług. | Po utworzeniu Azure-SSIS IR z wystąpieniem zarządzanym nie można wybrać warstwy usług dla SSISDB. Wszystkie bazy danych w wystąpieniu zarządzanym współdzielą ten sam zasób przydzielony do tego wystąpienia. |
| **Sieć wirtualna** | Azure-SSIS IR można przyłączyć do Azure Resource Manager sieci wirtualnej, jeśli używasz serwera Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej. | Azure-SSIS IR może przyłączyć się do Azure Resource Manager sieci wirtualnej, jeśli używasz wystąpienia zarządzanego z prywatnym punktem końcowym. Sieć wirtualna jest wymagana, gdy nie zostanie włączony publiczny punkt końcowy dla wystąpienia zarządzanego.<br/><br/>Jeśli dołączysz Azure-SSIS IR do tej samej sieci wirtualnej co wystąpienie zarządzane, upewnij się, że Azure-SSIS IR znajduje się w innej podsieci od wystąpienia zarządzanego. Jeśli dołączysz Azure-SSIS IR do innej sieci wirtualnej z wystąpienia zarządzanego, zalecamy komunikację równorzędną sieci wirtualnej lub połączenie sieciowe-sieciowe. Zobacz [łączenie aplikacji z wystąpieniem zarządzanym Azure SQL Database](../azure-sql/managed-instance/connect-application-instance.md). |
| **Transakcje rozproszone** | Ta funkcja jest obsługiwana przez transakcje elastyczne. Transakcje Microsoft Distributed Transaction Coordinator (MSDTC) nie są obsługiwane. Jeśli pakiety SSIS używają usługi MSDTC do koordynowania transakcji rozproszonych, rozważ migrację do transakcji elastycznych dla Azure SQL Database. Aby uzyskać więcej informacji, zobacz [transakcje rozproszone w bazach danych w chmurze](../azure-sql/database/elastic-transactions-overview.md). | Nieobsługiwane. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime za pomocą Azure Portal

W tej sekcji użyjesz Azure Portal, w tym interfejsu użytkownika Data Factory (UI) lub aplikacji, aby utworzyć Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Aby utworzyć fabrykę danych za pomocą Azure Portal, wykonaj instrukcje krok po kroku w temacie [Tworzenie fabryki danych za pomocą interfejsu użytkownika](./quickstart-create-data-factory-portal.md#create-a-data-factory). Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby umożliwić szybki dostęp po utworzeniu. 

Po utworzeniu fabryki danych Otwórz jej stronę przeglądu w Azure Portal. Wybierz kafelek **tworzenie & monitor** , aby **otworzyć stronę wprowadzenie** na osobnej karcie. W tym miejscu możesz kontynuować tworzenie Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime

**Na stronie Wprowadzenie** wybierz kafelek Konfigurowanie usług **SSIS Integration Runtime** , aby otworzyć okienko **Konfiguracja środowiska Integration Runtime** .

   ![Kafelek Konfigurowanie środowiska SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   Okienko **Konfiguracja środowiska Integration Runtime** zawiera trzy strony, na których można kolejno skonfigurować ustawienia ogólne, wdrożenia i zaawansowane.

#### <a name="general-settings-page"></a>Strona ustawień ogólnych

Na stronie **Ustawienia ogólne** okienka **konfiguracji środowiska Integration Runtime** wykonaj następujące czynności.

   ![Ustawienia ogólne](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. W polu **Nazwa** wprowadź nazwę środowiska Integration Runtime.

   2. W polu **Opis** wprowadź opis środowiska Integration Runtime.

   3. W polu **Lokalizacja** wybierz lokalizację środowiska Integration Runtime. Wyświetlane są tylko obsługiwane lokalizacje. Zalecamy wybranie tej samej lokalizacji do hostowania bazy danych SSISDB co w przypadku serwera bazy danych.

   4. W polu **rozmiar węzła** wybierz rozmiar węzła w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane rozmiary węzłów. Wybierz duży rozmiar węzła (skalowanie w górę), jeśli chcesz uruchomić wiele pakietów intensywnie korzystających z mocy obliczeniowej lub intensywnie korzystających z pamięci.
   > [!NOTE]
   > Jeśli wymagana jest [izolacja obliczeniowa](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), wybierz rozmiar węzła **Standard_E64i_v3** . Ten rozmiar węzła reprezentuje izolowane maszyny wirtualne korzystające z całego hosta fizycznego i zapewniające wymagany poziom izolacji wymagane przez pewne obciążenia, takie jak Departament STANów oddziaływania na poziomie 5 (IL5).
   
   5. W polu **Liczba węzłów** wybierz liczbę węzłów w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby węzłów. Wybierz duży klaster z wieloma węzłami (skalowanie w poziomie), jeśli chcesz uruchomić wiele pakietów równolegle.

   6. W polu **wydanie/licencja** wybierz wersję SQL Server dla środowiska Integration Runtime: Standard lub Enterprise. Wybierz pozycję Enterprise (przedsiębiorstwo), jeśli chcesz korzystać z funkcji zaawansowanych w środowisku Integration Runtime.

   7. W obszarze **oszczędność pieniędzy** wybierz opcję korzyść użycia hybrydowego platformy Azure dla środowiska Integration Runtime: **tak** lub **nie**. Wybierz pozycję **tak** , jeśli chcesz uzyskać własną licencję SQL Server z programem Software Assurance, aby korzystać z oszczędności związanych z użyciem hybrydowej.

   8. Wybierz pozycję **Dalej**.

#### <a name="deployment-settings-page"></a>Strona Ustawienia wdrożenia

Na stronie **Ustawienia wdrożenia** w okienku **Konfiguracja środowiska Integration Runtime** dostępne są opcje tworzenia SSISDB i lub Azure-SSIS IR magazynów pakietów.

##### <a name="creating-ssisdb"></a>Tworzenie SSISDB

Jeśli chcesz wdrożyć pakiety w programie SSISDB (model wdrażania projektu) na stronie **Ustawienia wdrożenia** w okienku **Konfiguracja środowiska Integration Runtime** , zaznacz pole wyboru **Utwórz wykaz usług SSIS (SSISDB) hostowane przez wystąpienie serwera Azure SQL Database Server/zarządzanego w celu przechowywania projektów/pakietów/środowisk/dzienników wykonywania** . Alternatywnie, jeśli chcesz wdrożyć pakiety w systemie plików, Azure Files lub SQL Server bazie danych (MSDB) hostowanej przez wystąpienie zarządzane usługi Azure SQL (model wdrażania pakietów), nie musisz tworzyć SSISDB ani zaznaczać pola wyboru.

Bez względu na model wdrażania, jeśli chcesz użyć agenta SQL Server hostowanego przez wystąpienie zarządzane Azure SQL do organizowania/planowania wykonywania pakietów, jest on włączony przez SSISDB, więc zaznacz pole wyboru mimo wszystko. Aby uzyskać więcej informacji, zobacz [Planowanie wykonywania pakietów usług SSIS za pośrednictwem agenta wystąpienia zarządzanego usługi Azure SQL](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
W przypadku zaznaczenia tego pola wyboru wykonaj następujące kroki, aby utworzyć własny serwer baz danych, który będzie hostować SSISDB i zarządzać nim w Twoim imieniu.

   ![Ustawienia wdrażania dla SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. W polu **Subskrypcja** określ subskrypcję platformy Azure, z której korzysta Twój serwer bazy danych hostujący bazę danych SSISDB. 

   1. W polu **Lokalizacja** określ lokalizację serwera bazy danych hostującego bazę danych SSISDB. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime.

   1. W polu **Punkt końcowy serwera bazy danych wykazu** wybierz punkt końcowy serwera bazy danych hostującego bazę danych SSISDB. 
   
      Na podstawie wybranego serwera bazy danych wystąpienie SSISDB można utworzyć w Twoim imieniu jako pojedynczą bazę danych w ramach puli elastycznej lub w wystąpieniu zarządzanym. Może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie SQL Database i wystąpienia zarządzanego SQL](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      W przypadku wybrania serwera Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB lub gdy wymagany jest dostęp do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, należy dołączyć Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [tworzenie Azure-SSIS IR w sieci wirtualnej]().

   1. Zaznacz pole wyboru **Użyj uwierzytelniania usługi Azure AD z zarządzaną tożsamością dla usługi ADF,** aby wybrać metodę uwierzytelniania dla serwera bazy danych do hostowania SSISDB. Wybierz opcję uwierzytelnianie SQL lub uwierzytelnianie usługi Azure AD z zarządzaną tożsamością dla fabryki danych.

      W przypadku zaznaczenia tego pola wyboru należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [tworzenie Azure-SSIS IR przy użyciu uwierzytelniania usługi Azure AD]().
   
   1. W polu **Nazwa użytkownika administratora** wprowadź nazwę użytkownika uwierzytelniania SQL dla serwera bazy danych, który będzie hostować SSISDB. 

   1. W polu **hasło administratora** wprowadź hasło uwierzytelniania SQL dla serwera bazy danych, aby hostować SSISDB. 

   1. W polu **warstwa usługi bazy danych wykazu** wybierz warstwę usług dla serwera bazy danych, która będzie hostować SSISDB. Wybierz warstwę podstawowa, standardowa lub Premium lub wybierz nazwę puli elastycznej.

Wybierz **Test connection** , jeśli ma to zastosowanie, a jeśli to się powiedzie, wybierz pozycję **dalej**.

> [!NOTE]
   > Jeśli do hostowania SSISDB jest używany serwer Azure SQL Database, dane będą przechowywane w magazynie geograficznie nadmiarowym dla kopii zapasowych. Jeśli nie chcesz, aby dane były replikowane w innych regionach, postępuj zgodnie z instrukcjami, aby [skonfigurować nadmiarowość magazynu kopii zapasowych za pomocą programu PowerShell](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
##### <a name="creating-azure-ssis-ir-package-stores"></a>Tworzenie Azure-SSIS IR magazynów pakietów

Jeśli chcesz zarządzać pakietami, które są wdrożone w bazie danych MSDB, systemie plików lub Azure Files (model wdrażania pakietów) przy użyciu Azure-SSIS IR magazynów pakietów, na stronie **Ustawienia wdrożenia** w okienku **Konfiguracja środowiska Integration Runtime** wybierz pozycję **Utwórz magazyny pakietów, aby zarządzać pakietami wdrożonymi w programie system plików/Azure Files/SQL Server Database (msdb) hostowanych przez wystąpienie zarządzane Azure SQL** .
   
Magazyn pakietów Azure-SSIS IR umożliwia importowanie/eksportowanie/usuwanie/uruchamianie pakietów oraz monitorowanie/zatrzymywanie uruchomionych pakietów za pośrednictwem programu SSMS podobnie jak w przypadku [starszego magazynu pakietów SSIS](/sql/integration-services/service/package-management-ssis-service). Aby uzyskać więcej informacji, zobacz [Zarządzanie pakietami SSIS za pomocą magazynów pakietów Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md).
   
Jeśli zaznaczysz to pole wyboru, możesz dodać wiele magazynów pakietów do Azure-SSIS IR, wybierając pozycję **Nowy**. Z kolei jeden magazyn pakietów może być współużytkowany przez wiele urzędów danych Azure-SSIS.

![Ustawienia wdrażania dla systemu MSDB/system plików/Azure Files](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

W okienku **Dodaj magazyn pakietów** wykonaj następujące czynności.
   
   1. W polu **Nazwa magazynu pakietów** wprowadź nazwę swojego magazynu pakietów. 

   1. W przypadku **połączonej usługi magazynu pakietów** wybierz istniejącą połączoną usługę, która przechowuje informacje o dostępie do systemu plików/Azure Files/wystąpienia zarządzanego Azure SQL, w którym są wdrażane pakiety, lub Utwórz nowe, wybierając pozycję **nowe**. W okienku **Nowa połączona usługa** wykonaj następujące czynności.
   
      > [!NOTE]
      > Aby uzyskać dostęp do Azure Files, można użyć usług **Azure File Storage** lub połączonych z **systemem plików** . Jeśli używasz połączonej usługi **Azure File Storage** , magazyn pakietów Azure-SSIS IR obsługuje teraz tylko **podstawowy** (nie **klucz konta** ani **Identyfikator URI sygnatury dostępu współdzielonego**). Aby korzystać z uwierzytelniania **podstawowego** na **platformie Azure File Storage** połączonej usłudze, możesz dołączyć `?feature.upgradeAzureFileStorage=false` do adresu URL portalu ADF w przeglądarce. Alternatywnie możesz użyć połączonej usługi **plików** , aby uzyskać dostęp do Azure Files zamiast tego. 

      ![Ustawienia wdrażania dla połączonych usług](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. W polu **Nazwa** wprowadź nazwę połączonej usługi. 
         
      1. W polu **Opis** wprowadź opis połączonej usługi. 
         
      1. W obszarze **Typ** wybierz pozycję **Azure File Storage**, **wystąpienie zarządzane Azure SQL** lub **system plików**.

      1. Możesz zignorować **połączenie za pośrednictwem środowiska Integration Runtime**, ponieważ zawsze korzystamy z Azure-SSIS IR, aby pobrać informacje o dostępie do magazynów pakietów.

      1. W przypadku wybrania **usługi Azure File Storage** wykonaj następujące czynności. 

         1. W **obszarze Metoda wyboru konta** wybierz pozycję **z subskrypcji platformy Azure** lub **wprowadź ręcznie**.
         
         1. Jeśli wybierzesz opcję **z subskrypcji platformy Azure**, wybierz odpowiednią **subskrypcję platformy Azure**, **nazwę konta magazynu** i **udział plików**.
            
         1. W przypadku wybrania opcji **wprowadź ręcznie** wprowadź wartość `\\<storage account name>.file.core.windows.net\<file share name>` w polach **host**, `Azure\<storage account name>` **nazwa użytkownika** i `<storage account key>` **hasło** lub wybierz swoje **Azure Key Vault** w miejscu, w którym są przechowywane jako wpis tajny.

      1. W przypadku wybrania **wystąpienia zarządzanego Azure SQL** należy wykonać następujące czynności. 

         1. Wybierz opcję **Parametry połączenia** , aby wprowadzić ją ręcznie lub **Azure Key Vault** , gdzie jest przechowywana jako wpis tajny.
         
         1. W przypadku wybrania opcji **Parametry połączenia** wykonaj następujące czynności. 

            1. W przypadku w **pełni kwalifikowanej nazwy domeny** wprowadź `<server name>.<dns prefix>.database.windows.net` `<server name>.public.<dns prefix>.database.windows.net,3342` odpowiednio lub jako prywatny lub publiczny punkt końcowy wystąpienia zarządzanego Azure SQL. W przypadku wprowadzenia prywatnego punktu końcowego **Test connection** nie ma zastosowania, ponieważ interfejs użytkownika funkcji ADF nie może uzyskać do niego dostępu.

            1. W obszarze **Nazwa bazy danych** wprowadź `msdb` .
               
            1. W obszarze **Typ uwierzytelniania** wybierz pozycję **uwierzytelnianie SQL**, **tożsamość zarządzana** lub nazwa **główna usługi**.

            1. W przypadku wybrania opcji **uwierzytelnianie SQL** wprowadź odpowiednią **nazwę użytkownika** i **hasło** lub wybierz **Azure Key Vault** , w których jest przechowywany jako wpis tajny.

            1. W przypadku wybrania opcji **tożsamość zarządzana** Przydziel usłudze ADF zarządzaną tożsamość do wystąpienia zarządzanego usługi Azure SQL.

            1. W przypadku wybrania **nazwy głównej usługi** wprowadź odpowiedni **Identyfikator** jednostki usługi i **klucz jednostki usługi** albo wybierz **Azure Key Vault** , w którym jest przechowywany jako wpis tajny.

      1. W przypadku wybrania opcji **system plików** wprowadź ścieżkę UNC do folderu, w którym pakiety są wdrożone dla **hosta**, a także odpowiednią **nazwę użytkownika** i **hasło** lub wybierz **Azure Key Vault** , w którym są przechowywane jako wpis tajny.

      1. Wybierz **Test connection** , jeśli ma to zastosowanie, a jeśli to się powiedzie, wybierz pozycję **Utwórz**.

   1. Dodane magazyny pakietów zostaną wyświetlone na stronie **Ustawienia wdrożenia** . Aby je usunąć, zaznacz ich pola wyboru, a następnie wybierz pozycję **Usuń**.

Wybierz **Test connection** , jeśli ma to zastosowanie, a jeśli to się powiedzie, wybierz pozycję **dalej**.

#### <a name="advanced-settings-page"></a>Strona Ustawienia zaawansowane

Na stronie **Ustawienia zaawansowane** okienka **Konfiguracja środowiska Integration Runtime** wykonaj następujące czynności.

   ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Dla opcji **Maksymalna liczba równoległych wykonań na węzeł** wybierz maksymalną liczbę pakietów do uruchomienia współbieżnie na węzeł w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz niską liczbę, jeśli chcesz użyć więcej niż jednego rdzenia do uruchamiania pojedynczego dużego pakietu, który jest obliczeniowy lub intensywnie korzystający z pamięci. Wybierz dużą liczbę, jeśli chcesz uruchomić co najmniej jeden niewielki pakiet w jednym rdzeniu.

   1. Zaznacz pole wyboru **dostosuj Azure-SSIS Integration Runtime z dodatkowymi konfiguracjami systemu/instalacjami składników** , aby określić, czy chcesz dodać do Azure-SSIS IR niestandardowe ustawienia standardowe/ekspresowe. Aby uzyskać więcej informacji, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

      ![Ustawienia zaawansowane z konfiguracjami niestandardowymi](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. W polu **Identyfikator URI sygnatury dostępu współdzielonego kontenera ustawień niestandardowych** wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera, w którym są przechowywane skrypty i skojarzone pliki dla standardowych konfiguracji niestandardowych.

      1. W **przypadku instalacji Niestandardowa Express** wybierz pozycję **Nowy** , aby otworzyć panel **Dodaj ekspresową instalację niestandardową** , a następnie wybierz dowolny typ z menu rozwijanego **Typ instalacji niestandardowej** , np. **Uruchom polecenie cmdkey**, **Dodaj zmienną środowiskową**, **Zainstaluj licencjonowany składnik** itd.

         Jeśli wybierzesz opcję **Zainstaluj licencjonowany typ składnika** , możesz wybrać wszystkie składniki zintegrowane od partnerów niezależnych dostawców oprogramowania w menu rozwijanym **Nazwa składnika** , a jeśli to konieczne, wprowadź klucz licencji produktu/Przekaż plik licencji produktu zakupionego z nich w polu  / **plik licencji** klucza licencji.
  
         Dodane ustawienia Express Custom Setup zostaną wyświetlone na stronie **Ustawienia zaawansowane** . Aby je usunąć, zaznacz ich pola wyboru, a następnie wybierz pozycję **Usuń**.

   1. Wybierz **sieć wirtualną do przyłączenia do Azure-SSIS Integration Runtime, Zezwól na automatyczny dostęp do zasobów sieciowych, a opcjonalnie Przenieś własne statyczne publiczne adresy IP,** aby wybrać, czy chcesz dołączyć do środowiska Integration Runtime do sieci wirtualnej. 

      Wybierz go, jeśli używasz serwera Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym w celu hostowania SSISDB lub jeśli potrzebujesz dostępu do danych lokalnych (czyli lokalnych źródeł danych lub miejsc docelowych w pakietach SSIS) bez konieczności konfigurowania samodzielnego środowiska IR. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](./join-azure-ssis-integration-runtime-virtual-network.md). 

      Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

      ![Ustawienia zaawansowane sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. W obszarze **subskrypcja** wybierz subskrypcję platformy Azure, która ma swoją sieć wirtualną.

      1. W polu **Lokalizacja** jest wybierana ta sama lokalizacja środowiska Integration Runtime.

      1. W polu **Typ** wybierz typ sieci wirtualnej: klasyczny lub Azure Resource Manager. Zalecamy wybranie Azure Resource Manager sieci wirtualnej, ponieważ klasyczne sieci wirtualne będą wkrótce przestarzałe.

      1. W polu **Nazwa** sieci wirtualnej wybierz nazwę swojej usługi. Powinna być taka sama, jak ta, która jest używana przez serwer Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB. Lub powinien być taki sam, jak jeden połączony z siecią lokalną. W przeciwnym razie może to być dowolna Sieć wirtualna do przenoszenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR.

      1. W polu **Nazwa podsieci** wybierz nazwę podsieci dla sieci wirtualnej. Powinna być taka sama, jak ta, która jest używana przez serwer Azure SQL Database z punktami końcowymi usługi sieci wirtualnej do hostowania SSISDB. Lub powinien być inną podsiecią niż używana dla wystąpienia zarządzanego z prywatnym punktem końcowym do hostowania SSISDB. W przeciwnym razie może być dowolna podsieć do przenoszenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR.

      1. Zaznacz pole wyboru **Przenieś statyczne publiczne adresy IP dla Azure-SSIS Integration Runtime** , aby wybrać, czy chcesz przenieść własne statyczne publiczne adresy ip dla Azure-SSIS IR, dzięki czemu możesz zezwolić na dostęp do nich w zaporze dla źródeł danych.

         Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

         1. W polu **pierwszy statyczny publiczny adres IP** wybierz pierwszy statyczny publiczny adres IP, który spełnia wymagania Azure-SSIS IR. Jeśli nie masz żadnego z tych opcji, kliknij przycisk **Utwórz nowy** link, aby utworzyć statyczne publiczne adresy IP na Azure Portal a następnie kliknij przycisk Odśwież tutaj, aby je wybrać.
      
         1. W polu **drugi statyczny publiczny adres IP** wybierz drugi statyczny publiczny adres IP, który spełnia wymagania Azure-SSIS IR. Jeśli nie masz żadnego z tych opcji, kliknij przycisk **Utwórz nowy** link, aby utworzyć statyczne publiczne adresy IP na Azure Portal a następnie kliknij przycisk Odśwież tutaj, aby je wybrać.

   1. Zaznacz pole wyboru **skonfiguruj Self-Hosted Integration Runtime jako serwer proxy dla Azure-SSIS Integration Runtime** , aby określić, czy chcesz skonfigurować własne środowisko IR jako serwer proxy dla Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Konfigurowanie własnego środowiska IR jako serwera proxy](./self-hosted-integration-runtime-proxy-ssis.md). 

      Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

      ![Ustawienia zaawansowane przy użyciu samodzielnego środowiska IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. W przypadku **Samodzielnej Integration Runtime** Wybierz istniejące własne środowisko IR jako serwer proxy dla Azure-SSIS IR.

      1. W przypadku **usługi połączonej z magazynem przemieszczania** wybierz istniejącą połączoną usługę Azure Blob Storage lub Utwórz nową na potrzeby przemieszczania.

      1. W polu **ścieżka tymczasowa** Określ kontener obiektów BLOB na wybranym koncie usługi Azure Blob Storage lub pozostaw to pole puste, aby użyć domyślnego ustawienia do przemieszczania.

   1. Wybierz pozycję **weryfikacja sieci wirtualnej**  >  **Kontynuuj**. 

W sekcji **Podsumowanie** Przejrzyj wszystkie ustawienia aprowizacji, zakładkę zalecane linki do dokumentacji i wybierz pozycję **Zakończ** , aby rozpocząć tworzenie środowiska Integration Runtime.

   > [!NOTE]
   > Bez czasu instalacji niestandardowej ten proces powinien zakończyć się w ciągu 5 minut. Azure-SSIS IR można przyłączyć do sieci wirtualnej, ale może upłynąć do 20-30 minut.
   >
   > Jeśli używasz SSISDB, Usługa Data Factory będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i przyłączy Azure-SSIS IR do sieci wirtualnej.
   > 
   > Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać więcej informacji na temat wbudowanych/preinstalowanych składników, zobacz [składniki wbudowane/preinstalowane na Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Aby uzyskać więcej informacji o dodatkowych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

#### <a name="connections-pane"></a>Okienko połączenia

W okienku **połączenia** okienka **Zarządzanie** centrum przejdź do strony **Integration Runtimes** i wybierz pozycję **Odśwież**. 

   ![Okienko połączenia](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Możesz edytować/zmienić konfigurację Azure-SSIS IR, wybierając jego nazwę. Możesz również wybrać odpowiednie przyciski do monitorowania/uruchamiania/zatrzymywania/usuwania Azure-SSIS IR, automatycznie wygenerować potok ADF z działaniem pakietu SSIS do uruchomienia na Azure-SSIS IR, a następnie wyświetlić kod JSON/ładunek Azure-SSIS IR.  Edytowanie/usuwanie Azure-SSIS IR można wykonać tylko wtedy, gdy jest ono zatrzymane.

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Środowiska Azure SSIS Integration Runtime w portalu

1. W interfejsie użytkownika Azure Data Factory przejdź do karty **Edycja** i wybierz pozycję **połączenia**. Następnie przejdź na kartę **Integration Runtime** , aby wyświetlić istniejące środowiska Integration Runtime w fabryce danych.

   ![Wyświetlanie istniejących środowisk IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Wybierz pozycję **Nowy** , aby utworzyć nowy Azure-SSIS IR i Otwórz okienko **konfiguracji środowiska Integration Runtime** .

   ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. W okienku **Konfiguracja środowiska Integration Runtime** wybierz **istniejące pakiety usług SSIS i przenieś je do wykonania na kafelku platformy Azure** , a następnie wybierz przycisk **dalej**.

   ![Określanie typu środowiska Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Pozostałe kroki konfigurowania Azure-SSIS IR można znaleźć w sekcji [Inicjowanie obsługi platformy Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime) .

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime za pomocą Azure PowerShell

W tej sekcji utworzysz Azure-SSIS IR za pomocą Azure PowerShell.

### <a name="create-variables"></a>Tworzenie zmiennych

Skopiuj i wklej poniższy skrypt. Określ wartości zmiennych. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Zaloguj się i wybierz subskrypcję

Dodaj następujący skrypt, aby zalogować się i wybrać subskrypcję platformy Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Weryfikowanie połączenia z serwerem bazy danych

Dodaj następujący skrypt, aby sprawdzić poprawność serwera Azure SQL Database lub wystąpienia zarządzanego.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Konfigurowanie sieci wirtualnej

Dodaj następujący skrypt, aby automatycznie skonfigurować uprawnienia i ustawienia sieci wirtualnej dla środowiska Azure-SSIS Integration Runtime do przyłączenia.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Jeśli grupa zasobów już istnieje, nie kopiuj tego kodu do skryptu. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Uruchom poniższe polecenie, aby utworzyć fabrykę danych.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime

Uruchom następujące polecenia, aby utworzyć środowisko Azure-SSIS Integration Runtime, które uruchamia pakiety usług SSIS na platformie Azure.

Jeśli nie używasz SSISDB, możesz pominąć `CatalogServerEndpoint` `CatalogPricingTier` Parametry, i `CatalogAdminCredential` .

Jeśli nie używasz serwera Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB lub Wymagaj dostępu do danych lokalnych, możesz `VNetId` pominąć `Subnet` Parametry i lub przekazać wartości dla nich puste. Można je również pominąć, jeśli skonfigurujesz własne środowisko IR jako serwer proxy dla Azure-SSIS IR dostępu do danych lokalnych. W przeciwnym razie nie można ich pominąć i musi upłynąć poprawne wartości z konfiguracji sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](./join-azure-ssis-integration-runtime-virtual-network.md).

Jeśli używasz wystąpienia zarządzanego do hostowania SSISDB, możesz pominąć `CatalogPricingTier` parametr lub przekazać pustą wartość dla tego parametru. W przeciwnym razie nie można go pominąć i należy przekazać prawidłową wartość z listy obsługiwanych warstw cenowych dla Azure SQL Database. Aby uzyskać więcej informacji, zobacz [SQL Database limitów zasobów](../azure-sql/database/resource-limits-logical-server.md).

Jeśli używasz uwierzytelniania usługi Azure AD z zarządzaną tożsamością dla fabryki danych do łączenia się z serwerem bazy danych, możesz pominąć ten `CatalogAdminCredential` parametr. Jednak należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md). W przeciwnym razie nie można go pominąć i musi upłynąć prawidłowy obiekt utworzony z nazwy użytkownika i hasła administratora serwera na potrzeby uwierzytelniania SQL.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Uruchom środowisko Integration Runtime

Uruchom następujące polecenia, aby uruchomić środowisko Azure-SSIS Integration Runtime.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Bez czasu instalacji niestandardowej ten proces powinien zakończyć się w ciągu 5 minut. Azure-SSIS IR można przyłączyć do sieci wirtualnej, ale może upłynąć do 20-30 minut.
>
> Jeśli używasz SSISDB, Usługa Data Factory będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i przyłączy Azure-SSIS IR do sieci wirtualnej.
> 
> Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać więcej informacji na temat wbudowanych/preinstalowanych składników, zobacz [składniki wbudowane/preinstalowane na Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Aby uzyskać więcej informacji o dodatkowych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Pełny skrypt

Oto pełny skrypt służący do tworzenia środowiska Azure-SSIS Integration Runtime.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime przy użyciu szablonu Azure Resource Manager

W tej sekcji użyjesz szablonu Azure Resource Manager, aby utworzyć środowisko Azure-SSIS Integration Runtime. Oto przykład przewodnika:

1. Utwórz plik JSON z następującym szablonem Azure Resource Manager. Zastąp wartości w nawiasach kątowych (symbolami zastępczymi) własnymi wartościami.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Aby wdrożyć szablon Azure Resource Manager, uruchom polecenie, `New-AzResourceGroupDeployment` jak pokazano w poniższym przykładzie. W tym przykładzie `ADFTutorialResourceGroup` jest nazwą grupy zasobów. `ADFTutorialARM.json` to plik, który zawiera definicję JSON dla fabryki danych i Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    To polecenie tworzy fabrykę danych i Azure-SSIS IR w niej, ale nie uruchamia środowiska IR.

3. Aby rozpocząć Azure-SSIS IR, uruchom `Start-AzDataFactoryV2IntegrationRuntime` polecenie:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Bez czasu instalacji niestandardowej ten proces powinien zakończyć się w ciągu 5 minut. Azure-SSIS IR można przyłączyć do sieci wirtualnej, ale może upłynąć do 20-30 minut.
>
> Jeśli używasz SSISDB, Usługa Data Factory będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i przyłączy Azure-SSIS IR do sieci wirtualnej.
> 
> Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać więcej informacji na temat wbudowanych/preinstalowanych składników, zobacz [składniki wbudowane/preinstalowane na Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Aby uzyskać więcej informacji o dodatkowych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz SSISDB, możesz wdrożyć w nim pakiety i uruchamiać je na Azure-SSIS IR przy użyciu narzędzi SSDT lub SSMS z obsługą platformy Azure. Narzędzia te łączą się z serwerem bazy danych za pośrednictwem swojego punktu końcowego serwera: 

- W przypadku serwera Azure SQL Database format punktu końcowego serwera to `<server name>.database.windows.net` .
- W przypadku wystąpienia zarządzanego z prywatnym punktem końcowym format punktu końcowego serwera to `<server name>.<dns prefix>.database.windows.net` .
- W przypadku wystąpienia zarządzanego z publicznym punktem końcowym format punktu końcowego serwera to `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Jeśli nie korzystasz z SSISDB, możesz wdrożyć pakiety w systemie plików, Azure Files lub MSDB, hostowanym przez wystąpienie zarządzane usługi Azure SQL i uruchamiać je na Azure-SSIS IR przy użyciu narzędzi wiersza polecenia [dtutil](/sql/integration-services/dtutil-utility) i [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) . 

Aby uzyskać więcej informacji, zobacz [wdrażanie projektów SSIS/pakietów](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

W obu przypadkach wdrożone pakiety można również uruchomić na Azure-SSIS IR przy użyciu działania wykonywania pakietu SSIS w potokach Data Factory. Aby uzyskać więcej informacji, zobacz [wywołania wykonywania pakietu SSIS jako działania pierwszej klasy Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Następne kroki

Zobacz inne tematy Azure-SSIS IR w tej dokumentacji:

- [Środowisko Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje na temat środowiska Integration Runtime, w tym Azure-SSIS IR.
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule pokazano, jak pobrać i zrozumieć informacje o Azure-SSIS IR.
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania Azure-SSIS IR. Przedstawiono w nim również sposób skalowania Azure-SSIS IR przez dodanie kolejnych węzłów.
- [Wdrażanie, uruchamianie i monitorowanie pakietów SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Łączenie się z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Zaplanuj wykonywanie pakietów na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)