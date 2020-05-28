---
title: Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory
description: Dowiedz się, jak utworzyć środowisko Azure-SSIS Integration Runtime w Azure Data Factory, aby móc wdrażać i uruchamiać pakiety usług SSIS na platformie Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: af6e6346616255cfb153d59df7031c6d0d4710da
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117890"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule przedstawiono procedurę aprowizacji środowiska Integration Runtime (IR) platformy Azure — SQL Server Integration Services (SSIS) w programie Azure Data Factory. Azure-SSIS IR obsługuje:

- Uruchamianie pakietów wdrożonych do wykazu usług SSIS (SSISDB) na Azure SQL Database lub wystąpienia zarządzanego SQL (model wdrażania projektu).
- Uruchomione pakiety wdrożone w systemach plików, udziałach plików lub Azure Files (model wdrażania pakietu). 

Po zainicjowaniu Azure-SSIS IR można używać dobrze znanych narzędzi do wdrażania i uruchamiania pakietów na platformie Azure. Te narzędzia obejmują SQL Server narzędzia danych (SSDT), SQL Server Management Studio (SSMS) i narzędzia wiersza polecenia, takie jak `dtinstall` , `dtutil` i `dtexec` .

W samouczku [Azure-SSIS IR aprowizacji](tutorial-create-azure-ssis-runtime-portal.md) przedstawiono sposób tworzenia Azure-SSIS IR za pomocą aplikacji Azure Portal lub Data Factory. W tym samouczku pokazano również, jak opcjonalnie używać wystąpienia zarządzanego SQL Database lub SQL do hostowania SSISDB. Ten artykuł rozszerza się w samouczku i opisuje, jak wykonać te opcjonalne zadania:

- Użyj SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym do hostowania SSISDB. Jako wymaganie wstępne należy skonfigurować uprawnienia i ustawienia sieci wirtualnej dla Azure-SSIS IR, aby dołączyć do sieci wirtualnej.

- Użyj uwierzytelniania Azure Active Directory (Azure AD) z zarządzaną tożsamością dla fabryki danych, aby połączyć się z SQL Database lub wystąpieniem zarządzanym SQL. Jako warunek wstępny należy dodać tożsamość zarządzaną dla fabryki danych jako użytkownika bazy danych, który może utworzyć wystąpienie SSISDB.

- Dołącz do Azure-SSIS IR do sieci wirtualnej lub skonfiguruj własne środowisko IR jako serwer proxy dla Azure-SSIS IR, aby uzyskać dostęp do danych lokalnie.

W tym artykule pokazano, jak zainicjować obsługę Azure-SSIS IR przy użyciu Azure Portal, Azure PowerShell i szablonu Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz jeszcze subskrypcji, możesz utworzyć [bezpłatne konto wersji próbnej](https://azure.microsoft.com/pricing/free-trial/) .

- **Wystąpienie zarządzane SQL Database lub SQL (opcjonalnie)**. Jeśli nie masz jeszcze serwera w SQL Database lub wystąpienia wystąpienia zarządzanego SQL, utwórz je w Azure Portal przed rozpoczęciem pracy. Data Factory spowoduje utworzenie wystąpienia SSISDB dla tego serwera lub wystąpienia. 

  Zalecamy utworzenie serwera lub wystąpienia zarządzanego w tym samym regionie świadczenia usługi Azure, w którym znajduje się środowisko Integration Runtime. Ta konfiguracja umożliwia wykonywanie dzienników wykonywania zapisu środowiska Integration Runtime w usłudze SSISDB bez przekroczenia regionów świadczenia usługi Azure.

  Należy pamiętać o następujących kwestiach:

  - Na podstawie wybranego produktu wystąpienie SSISDB można utworzyć w Twoim imieniu w SQL Database jako pojedynczą bazę danych lub jako część puli elastycznej lub w wystąpieniu zarządzanym SQL. Może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania odpowiedniego produktu bazy danych do hostowania SSISDB, zobacz [porównanie SQL Database i wystąpienia zarządzanego SQL](#comparison-of-sql-database-and-sql-managed-instance). 
  
    Jeśli używasz SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym do hostowania SSISDB, lub jeśli potrzebujesz dostępu do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, musisz dołączyć Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

  - Aby uzyskać SQL Database, skonfiguruj reguły zapory IP/punkty końcowe usługi sieci wirtualnej lub wystąpienie zarządzane SQL z prywatnym punktem końcowym do hostowania SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz polecenie [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Dodaj adres IP komputera klienckiego lub zakres adresów IP, który zawiera adres IP komputera klienckiego, do listy adres IP klienta w ustawieniach zapory dla serwera bazy danych. Aby uzyskać więcej informacji, zobacz [reguły zapory na poziomie serwera i na poziomie bazy danych](../azure-sql/database/firewall-configure.md).

  - Można nawiązać połączenie z serwerem bazy danych przy użyciu uwierzytelniania SQL z poświadczeniami administratora serwera lub przy użyciu uwierzytelniania usługi Azure AD z zarządzaną tożsamością dla fabryki danych. Dla tych ostatnich należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Upewnij się, że serwer bazy danych nie ma już wystąpienia SSISDB. Inicjowanie obsługi Azure-SSIS IR nie obsługuje korzystania z istniejącego wystąpienia SSISDB.

- **Azure Resource Manager sieci wirtualnej (opcjonalnie)**. Należy mieć Azure Resource Manager sieci wirtualnej, jeśli co najmniej jeden z następujących warunków jest spełniony:
  - Udostępniasz SSISDB na SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym.
  - Chcesz połączyć się z lokalnymi magazynami danych z pakietów SSIS uruchomionych na Azure-SSIS IR bez konfigurowania własnego środowiska IR.

- **Azure PowerShell (opcjonalnie)**. Postępuj zgodnie z instrukcjami w temacie [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps), jeśli chcesz uruchomić skrypt programu PowerShell w celu aprowizacji Azure-SSIS IR.

### <a name="regional-support"></a>Wsparcie regionalne

Aby uzyskać listę regionów świadczenia usługi Azure, w których Data Factory i Azure-SSIS IR są dostępne, zobacz [Data Factory i SSIS IR dostępność według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Porównanie SQL Database i wystąpienia zarządzanego SQL

W poniższej tabeli porównano niektóre funkcje SQL Database i wystąpienia zarządzanego SQL, które są powiązane z usługą Azure-SSIR IR:

| Cecha | SQL Database | Wystąpienie zarządzane SQL |
|---------|--------------|------------------|
| **Planowanie** | Agent SQL Server jest niedostępny.<br/><br/>Zobacz [Planowanie wykonywania pakietu w potoku Data Factory](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agent wystąpienia zarządzanego SQL jest dostępny. |
| **Authentication** | Można utworzyć wystąpienie SSISDB z użytkownikiem zawartej bazy danych, który reprezentuje dowolną grupę usługi Azure AD z zarządzaną tożsamością fabryki danych jako członkiem roli **db_owner** .<br/><br/>Zobacz [Włączanie uwierzytelniania usługi Azure AD, aby utworzyć wystąpienie SSISDB na SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Można utworzyć wystąpienie SSISDB z użytkownikiem zawartej bazy danych, który reprezentuje zarządzaną tożsamość fabryki danych. <br/><br/>Zobacz [Włączanie uwierzytelniania usługi Azure AD, aby utworzyć wystąpienie SSISDB w wystąpieniu zarządzanym SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Warstwa usług** | Podczas tworzenia Azure-SSIS IR za pomocą SQL Database można wybrać warstwę usług dla SSISDB. Istnieje wiele warstw usług. | Podczas tworzenia Azure-SSIS IR przy użyciu wystąpienia zarządzanego SQL nie można wybrać warstwy usług dla SSISDB. Wszystkie bazy danych w wystąpieniu zarządzanym SQL współużytkują ten sam zasób przydzielony do tego wystąpienia. |
| **Sieć wirtualna** | Azure-SSIS IR można przyłączyć do Azure Resource Manager sieci wirtualnej, jeśli używasz SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej. | Azure-SSIS IR może przyłączyć się do Azure Resource Manager sieci wirtualnej, jeśli używasz wystąpienia zarządzanego SQL z prywatnym punktem końcowym. Sieć wirtualna jest wymagana, gdy nie zostanie włączony publiczny punkt końcowy dla wystąpienia zarządzanego.<br/><br/>Jeśli dołączysz Azure-SSIS IR do tej samej sieci wirtualnej co wystąpienie zarządzane, upewnij się, że Azure-SSIS IR znajduje się w innej podsieci od wystąpienia zarządzanego. Jeśli dołączysz Azure-SSIS IR do innej sieci wirtualnej z wystąpienia zarządzanego, zalecamy komunikację równorzędną sieci wirtualnej lub połączenie sieciowe-sieciowe. Zobacz [łączenie aplikacji z wystąpieniem zarządzanym bazy danych SQL](../azure-sql/managed-instance/connect-application-instance.md). |
| **Transakcje rozproszone** | Ta funkcja jest obsługiwana przez transakcje elastyczne. Transakcje Microsoft Distributed Transaction Coordinator (MSDTC) nie są obsługiwane. Jeśli pakiety SSIS używają usługi MSDTC do koordynowania transakcji rozproszonych, rozważ migrację do transakcji elastycznych dla Azure SQL Database. Aby uzyskać więcej informacji, zobacz [transakcje rozproszone w bazach danych w chmurze](../azure-sql/database/elastic-transactions-overview.md). | Nieobsługiwane. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Tworzenie środowiska Integration Runtime za pomocą Azure Portal

W tej sekcji użyjesz Azure Portal, w tym interfejsu użytkownika Data Factory (UI) lub aplikacji, aby utworzyć Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Aby utworzyć fabrykę danych za pomocą Azure Portal, wykonaj instrukcje krok po kroku w temacie [Tworzenie fabryki danych za pomocą interfejsu użytkownika](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby umożliwić szybki dostęp po utworzeniu. 

Po utworzeniu fabryki danych Otwórz jej stronę przeglądu w Azure Portal. Wybierz kafelek **tworzenie & monitor** , aby **otworzyć stronę wprowadzenie** na osobnej karcie. W tym miejscu możesz kontynuować tworzenie Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime

1. Na stronie **Wprowadzenie** wybierz kafelek **Konfigurowanie środowiska SSIS Integration Runtime**.

   ![Kafelek Konfigurowanie środowiska SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. W sekcji **Ustawienia ogólne** w panelu **ustawień Integration Runtime** wykonaj następujące czynności.

   ![Ustawienia ogólne](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. W polu **Nazwa** wprowadź nazwę środowiska Integration Runtime.
   2. W polu **Opis** wprowadź opis środowiska Integration Runtime.
   3. W polu **Lokalizacja** wybierz lokalizację środowiska Integration Runtime. Wyświetlane są tylko obsługiwane lokalizacje. Zalecamy wybranie tej samej lokalizacji do hostowania bazy danych SSISDB co w przypadku serwera bazy danych.
   4. W polu **rozmiar węzła**wybierz rozmiar węzła w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane rozmiary węzłów. Wybierz duży rozmiar węzła (skalowanie w górę), jeśli chcesz uruchomić wiele pakietów intensywnie korzystających z mocy obliczeniowej lub intensywnie korzystających z pamięci.
   5. W polu **Liczba węzłów** wybierz liczbę węzłów w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby węzłów. Wybierz duży klaster z wieloma węzłami (skalowanie w poziomie), jeśli chcesz uruchomić wiele pakietów równolegle.
   6. W polu **wydanie/licencja**wybierz wersję SQL Server dla środowiska Integration Runtime: Standard lub Enterprise. Wybierz pozycję Enterprise (przedsiębiorstwo), jeśli chcesz korzystać z funkcji zaawansowanych w środowisku Integration Runtime.
   7. W obszarze **oszczędność pieniędzy**wybierz opcję korzyść użycia hybrydowego platformy Azure dla środowiska Integration Runtime: **tak** lub **nie**. Wybierz pozycję **tak** , jeśli chcesz uzyskać własną licencję SQL Server z programem Software Assurance, aby korzystać z oszczędności związanych z użyciem hybrydowej.
   8. Wybierz opcję **Dalej**.

3. W sekcji **Ustawienia SQL** wykonaj następujące czynności.

   ![Ustawienia SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Zaznacz pole wyboru **Utwórz katalog usług SSIS (SSISDB) z wystąpieniem zarządzanym SQL Database/SQL, aby przechowywać projekty/pakiety/środowiska/dzienniki wykonywania** , aby wybrać produkt Azure SQL dla pakietów do uruchomienia na Azure-SSIS IR. Wybierz model wdrażania projektu, w którym pakiety są wdrażane do SSISDB hostowanych przez wystąpienia zarządzane przez SQL Database lub SQL lub model wdrażania pakietów, w którym pakiety są wdrażane w systemach plików, udziałach plików lub Azure Files. 
    
    Jeśli zaznaczysz to pole wyboru, musisz wprowadzić własne SQL Database lub wystąpienie zarządzane SQL, aby hostować wystąpienie SSISDB, które utworzymy i zarządzamy w Twoim imieniu.
   
    1. W obszarze **subskrypcja**wybierz subskrypcję platformy Azure, która ma SQL Database lub wystąpienie zarządzane SQL, aby hostować SSISDB. 

    1. W polu **Lokalizacja**wybierz lokalizację SQL Database lub wystąpienia zarządzanego SQL. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime. 

    1. W przypadku **punktu końcowego serwera bazy danych wykazu**wybierz punkt końcowy SQL Database lub wystąpienia zarządzanego SQL. 
    
       Na podstawie wybranego SQL Database lub wystąpienia zarządzanego SQL wystąpienie SSISDB można utworzyć w Twoim imieniu jako pojedynczą bazę danych lub jako część puli elastycznej w SQL Database lub jako baza danych wystąpienia w wystąpieniu zarządzanym SQL. Może być dostępna w sieci publicznej lub przez dołączenie do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania produktu do hostowania SSISDB, zobacz [porównanie Azure SQL Database i wystąpienia zarządzanego SQL](#comparison-of-sql-database-and-sql-managed-instance).
       W przypadku wybrania SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym do hostowania SSISDB lub gdy wymagany jest dostęp do danych lokalnych bez konfigurowania samoobsługowego środowiska IR, należy dołączyć Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    1. Zaznacz pole wyboru **Użyj uwierzytelniania usługi AAD z zarządzaną tożsamością dla usługi ADF,** aby wybrać metodę uwierzytelniania dla serwera bazy danych na potrzeby hostowania SSISDB. Wybierz opcję uwierzytelnianie SQL lub uwierzytelnianie usługi Azure AD z zarządzaną tożsamością dla fabryki danych. 
    
        W przypadku zaznaczenia tego pola wyboru należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    1. W polu **Nazwa użytkownika administratora**wprowadź nazwę użytkownika uwierzytelniania SQL dla serwera bazy danych, który będzie hostować SSISDB. 

    1. W polu **hasło administratora**wprowadź hasło uwierzytelniania SQL dla serwera bazy danych, aby hostować SSISDB. 

    1. W polu **warstwa usługi bazy danych wykazu**wybierz warstwę usług dla serwera bazy danych, która będzie hostować SSISDB. Wybierz warstwę podstawowa, standardowa lub Premium lub wybierz nazwę puli elastycznej. 

      1. Wybierz pozycję **Testuj połączenie**. Jeśli test zakończy się pomyślnie, wybierz pozycję **dalej**. 

4. W sekcji **Ustawienia zaawansowane** wykonaj następujące czynności.

   ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Dla opcji **Maksymalna liczba równoległych wykonań na węzeł**wybierz maksymalną liczbę pakietów do uruchomienia współbieżnie na węzeł w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz niską liczbę, jeśli chcesz użyć więcej niż jednego rdzenia do uruchamiania pojedynczego dużego pakietu, który jest obliczeniowy lub intensywnie korzystający z pamięci. Wybierz dużą liczbę, jeśli chcesz uruchomić co najmniej jeden niewielki pakiet w jednym rdzeniu.

   1. Zaznacz pole wyboru **dostosuj Azure-SSIS Integration Runtime z dodatkowymi konfiguracjami systemu/instalacjami składników** , aby określić, czy chcesz dodać do Azure-SSIS IR niestandardowe ustawienia standardowe/ekspresowe. Aby uzyskać więcej informacji, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

      ![Ustawienia zaawansowane z konfiguracjami niestandardowymi](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. W polu **Identyfikator URI sygnatury dostępu współdzielonego kontenera ustawień niestandardowych**wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera, w którym są przechowywane skrypty i skojarzone pliki dla standardowych konfiguracji niestandardowych.

      1. W **przypadku instalacji Niestandardowa Express**wybierz pozycję **Nowy** , aby otworzyć panel **Dodaj ekspresową instalację niestandardową** , a następnie wybierz dowolny typ z menu rozwijanego **Typ instalacji niestandardowej** , np. **Uruchom polecenie cmdkey**, **Dodaj zmienną środowiskową**, **Zainstaluj licencjonowany składnik**itd.

        Jeśli wybierzesz opcję **Zainstaluj licencjonowany typ składnika** , możesz wybrać wszystkie składniki zintegrowane od partnerów niezależnych dostawców oprogramowania w menu rozwijanym **Nazwa składnika** i w razie potrzeby wprowadzić klucz licencji produktu nabyty w ramach pola **klucz licencji** .
  
        Dodane ustawienia Express Custom Setup zostaną wyświetlone w sekcji **Ustawienia zaawansowane** . Aby je usunąć, zaznacz ich pola wyboru, a następnie wybierz pozycję **Usuń**.

   1. Wybierz **sieć wirtualną do przyłączenia do Azure-SSIS Integration Runtime, Zezwól na automatyczny dostęp do zasobów sieciowych, a opcjonalnie Przenieś własne statyczne publiczne adresy IP,** aby wybrać, czy chcesz dołączyć do środowiska Integration Runtime do sieci wirtualnej. 

    Wybierz go, jeśli używasz SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym w celu hostowania SSISDB lub jeśli potrzebujesz dostępu do danych lokalnych (czyli lokalnych źródeł danych lub miejsc docelowych w pakietach SSIS) bez konieczności konfigurowania samodzielnego środowiska IR. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

    ![Ustawienia zaawansowane sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    1. W obszarze **subskrypcja**wybierz subskrypcję platformy Azure, która ma swoją sieć wirtualną.

    1. W polu **Lokalizacja**jest wybierana ta sama lokalizacja środowiska Integration Runtime.

    1. W polu **Typ**wybierz typ sieci wirtualnej: klasyczny lub Azure Resource Manager. Zalecamy wybranie Azure Resource Manager sieci wirtualnej, ponieważ klasyczne sieci wirtualne będą wkrótce przestarzałe.

    1. W polu **Nazwa**sieci wirtualnej wybierz nazwę swojej usługi. Powinna być taka sama, jak ta, która jest używana dla SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym do hostowania SSISDB. Lub powinien być taki sam, jak jeden połączony z siecią lokalną. W przeciwnym razie może to być dowolna Sieć wirtualna do przenoszenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR.

    1. W polu **Nazwa podsieci**wybierz nazwę podsieci dla sieci wirtualnej. Powinna być taka sama, jak ta, która jest używana dla SQL Database z punktami końcowymi usługi sieci wirtualnej do hostowania SSISDB. Lub powinien być inną podsiecią niż używana dla wystąpienia zarządzanego SQL z prywatnym punktem końcowym do hostowania SSISDB. W przeciwnym razie może być dowolna podsieć do przenoszenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR.

    1. Zaznacz pole wyboru **Przenieś statyczne publiczne adresy IP dla Azure-SSIS Integration Runtime** , aby wybrać, czy chcesz przenieść własne statyczne publiczne adresy ip dla Azure-SSIS IR, dzięki czemu możesz zezwolić na dostęp do nich w zaporze dla źródeł danych.

        Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

        1. W polu **pierwszy statyczny publiczny adres IP**wybierz pierwszy statyczny publiczny adres IP, który spełnia wymagania Azure-SSIS IR. Jeśli nie masz żadnego z tych opcji, kliknij przycisk **Utwórz nowy** link, aby utworzyć statyczne publiczne adresy IP na Azure Portal a następnie kliknij przycisk Odśwież tutaj, aby je wybrać.
        2. W polu **drugi statyczny publiczny adres IP**wybierz drugi statyczny publiczny adres IP, który spełnia wymagania Azure-SSIS IR. Jeśli nie masz żadnego z tych opcji, kliknij przycisk **Utwórz nowy** link, aby utworzyć statyczne publiczne adresy IP na Azure Portal a następnie kliknij przycisk Odśwież tutaj, aby je wybrać.

   1. Zaznacz pole wyboru **Skonfiguruj Samoobsługowe Integration Runtime jako serwer proxy dla Azure-SSIS Integration Runtime** , aby określić, czy chcesz skonfigurować własne środowisko IR jako serwer proxy dla Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Konfigurowanie własnego środowiska IR jako serwera proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

      ![Ustawienia zaawansowane przy użyciu samodzielnego środowiska IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. W przypadku **Samodzielnej Integration Runtime**Wybierz istniejące własne środowisko IR jako serwer proxy dla Azure-SSIS IR.

      1. W przypadku **usługi połączonej z magazynem przemieszczania**wybierz istniejącą połączoną usługę Azure Blob Storage lub Utwórz nową na potrzeby przemieszczania.

      1. W polu **ścieżka tymczasowa**Określ kontener obiektów BLOB na wybranym koncie usługi Azure Blob Storage lub pozostaw to pole puste, aby użyć domyślnego ustawienia do przemieszczania.

   1. Wybierz pozycję **weryfikacja sieci wirtualnej**  >  **Kontynuuj**. 

1. W sekcji **Podsumowanie** Przejrzyj wszystkie ustawienia aprowizacji, zakładkę zalecane linki do dokumentacji i wybierz pozycję **Zakończ** , aby rozpocząć tworzenie środowiska Integration Runtime.

   > [!NOTE]
   > Bez czasu instalacji niestandardowej ten proces powinien zakończyć się w ciągu 5 minut. Azure-SSIS IR można przyłączyć do sieci wirtualnej, ale może upłynąć do 20-30 minut.
   >
   > Jeśli używasz SSISDB, Usługa Data Factory będzie łączyć się z serwerem bazy danych w celu przygotowania SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i przyłączy Azure-SSIS IR do sieci wirtualnej.
   > 
   > Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Na karcie **Połączenia** w razie potrzeby przełącz na opcję **Środowiska Integration Runtime**. Wybierz pozycję **Odśwież**, aby odświeżyć stan.

   ![Stan tworzenia](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Za pomocą linków w kolumnie **Akcje** możesz zatrzymać/uruchomić, edytować lub usunąć środowisko Integration Runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane.

   ![Akcje środowiska IR platformy Azure SSIS](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Środowiska Azure SSIS Integration Runtime w portalu

1. W interfejsie użytkownika Azure Data Factory przejdź do karty **Edycja** i wybierz pozycję **połączenia**. Następnie przejdź na kartę **Integration Runtime** , aby wyświetlić istniejące środowiska Integration Runtime w fabryce danych.

   ![Wyświetlanie istniejących środowisk IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Wybierz pozycję **Nowy** , aby utworzyć nowy Azure-SSIS IR.

   ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. W panelu **konfiguracja Integration Runtime** wybierz **istniejące pakiety usług SSIS i Shift do wykonania na kafelku platformy Azure** , a następnie wybierz przycisk **dalej**.

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your logical server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

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

### <a name="validate-the-connection"></a>Sprawdzanie poprawności połączenia

Dodaj następujący skrypt, aby sprawdzić poprawność połączenia z usługą SQL Database lub wystąpieniem zarządzanym SQL.

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
                Write-Warning "Cannot connect, exception: $_";
                Write-Warning "Please make sure the server or instance you specified has already been created. Do you want to proceed? [Y/N]"
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

Jeśli nie używasz SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym do hostowania SSISDB lub Wymagaj dostępu do danych lokalnych, możesz pominąć `VNetId` `Subnet` Parametry i lub przekazać wartości dla nich puste. Można je również pominąć, jeśli skonfigurujesz własne środowisko IR jako serwer proxy dla Azure-SSIS IR dostępu do danych lokalnych. W przeciwnym razie nie można ich pominąć i musi upłynąć poprawne wartości z konfiguracji sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Jeśli używasz wystąpienia zarządzanego SQL do hostowania SSISDB, możesz pominąć `CatalogPricingTier` parametr lub przekazać pustą wartość dla tego parametru. W przeciwnym razie nie można go pominąć i należy przekazać prawidłową wartość z listy obsługiwanych warstw cenowych dla Azure SQL Database. Aby uzyskać więcej informacji, zobacz [SQL Database limitów zasobów](../sql-database/sql-database-resource-limits.md).

Jeśli używasz uwierzytelniania usługi Azure AD z zarządzaną tożsamością dla fabryki danych do łączenia się z serwerem bazy danych, możesz pominąć ten `CatalogAdminCredential` parametr. Jednak należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). W przeciwnym razie nie można go pominąć i musi upłynąć prawidłowy obiekt utworzony z nazwy użytkownika i hasła administratora serwera na potrzeby uwierzytelniania SQL.

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
> Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your logical server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

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
                Write-Warning "Cannot connect, exception: $_";
                Write-Warning "Please make sure the server or instance you specified has already been created. Do you want to proceed? [Y/N]"
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
                                "catalogServerEndpoint": "<server name>.database.windows.net",
                                "catalogAdminUserName": "<server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<server admin password>"
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

2. Aby wdrożyć szablon Azure Resource Manager, uruchom polecenie, `New-AzResourceGroupDeployment` jak pokazano w poniższym przykładzie. W tym przykładzie `ADFTutorialResourceGroup` jest nazwą grupy zasobów. `ADFTutorialARM.json`to plik, który zawiera definicję JSON dla fabryki danych i Azure-SSIS IR.

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
> Podczas aprowizacji Azure-SSIS IR są również instalowane narzędzia do redystrybucyjny i pakiet Azure Feature Pack dla usług SSIS. Te składniki zapewniają łączność z plikami programu Excel, plikami dostępu i różnymi źródłami danych platformy Azure, a także ze źródłami danych, które już obsługują. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Konfiguracja niestandardowa dla Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz SSISDB, możesz wdrożyć w nim pakiety i uruchamiać je na Azure-SSIS IR za pomocą narzędzi SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS). Narzędzia te łączą się z serwerem bazy danych za pośrednictwem swojego punktu końcowego serwera: 

- W przypadku SQL Database format punktu końcowego serwera to `<server name>.database.windows.net` .
- W przypadku wystąpienia zarządzanego SQL z prywatnym punktem końcowym format punktu końcowego serwera to `<server name>.<dns prefix>.database.windows.net` .
- W przypadku wystąpienia zarządzanego SQL z publicznym punktem końcowym format punktu końcowego serwera to `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Jeśli nie korzystasz z programu SSISDB, możesz wdrożyć pakiety w systemach plików, udziałach plików lub Azure Files i uruchamiać je na Azure-SSIS IR przy użyciu `dtinstall` `dtutil` `dtexec` narzędzi wiersza polecenia, i. Aby uzyskać więcej informacji, zobacz [wdrażanie pakietów SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

W obu przypadkach wdrożone pakiety można również uruchomić na Azure-SSIS IR przy użyciu działania wykonywania pakietu SSIS w potokach Data Factory. Aby uzyskać więcej informacji, zobacz [wywołania wykonywania pakietu SSIS jako działania pierwszej klasy Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Następne kroki

Zobacz inne tematy Azure-SSIS IR w tej dokumentacji:

- [Środowisko Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje na temat środowiska Integration Runtime, w tym Azure-SSIS IR.
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule pokazano, jak pobrać i zrozumieć informacje o Azure-SSIS IR.
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania Azure-SSIS IR. Przedstawiono w nim również sposób skalowania Azure-SSIS IR przez dodanie kolejnych węzłów.
- [Wdrażanie, uruchamianie i monitorowanie pakietów SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Łączenie się z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Zaplanuj wykonywanie pakietów na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)