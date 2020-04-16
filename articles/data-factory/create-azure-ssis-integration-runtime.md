---
title: Tworzenie środowiska wykonawczego integracji platformy Azure-SSIS w usłudze Azure Data Factory
description: Dowiedz się, jak utworzyć środowisko uruchomieniowe integracji platformy Azure-SSIS w usłudze Azure Data Factory, aby można było wdrażać i uruchamiać pakiety SSIS na platformie Azure.
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
ms.openlocfilehash: 1c3f58d42b6f311e4e238dcffe7da42afd8a5306
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416727"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Tworzenie środowiska wykonawczego integracji platformy Azure-SSIS w usłudze Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ten artykuł zawiera kroki inicjowania obsługi administracyjnej środowiska wykonawczego integracji usługi Azure-SQL Server (SSIS) w usłudze Azure Data Factory. Azure-SSIS IR obsługuje:

- Uruchamianie pakietów wdrożonych w katalogu SSIS (SSISDB) obsługiwanych przez serwer bazy danych SQL platformy Azure lub wystąpienie zarządzane (model wdrażania projektu).
- Uruchamianie pakietów wdrożonych w systemach plików, udziałach plików lub usłudze Azure Files (Model wdrażania pakietów). 

Po zainicjowaniu obsługi administracyjnej usługi Azure-SSIS IR można użyć znanych narzędzi do wdrażania i uruchamiania pakietów na platformie Azure. Narzędzia te obejmują narzędzia SQL Server Data Tools (SSDT), SQL Server Management `dtinstall`Studio `dtutil`(SSMS) oraz narzędzia wiersza polecenia, takie jak , i `dtexec`.

Samouczek [inicjowania obsługi administracyjnej platformy Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) pokazuje, jak utworzyć usługę Azure-SSIS IR za pośrednictwem portalu Azure lub aplikacji Data Factory. W samouczku pokazano również, jak opcjonalnie używać serwera bazy danych SQL platformy Azure lub wystąpienia zarządzanego do hostowania usługi SSISDB. W tym artykule opisano, jak wykonać te zadania opcjonalne:

- Użyj serwera bazy danych SQL platformy Azure z regułami zapory IP/punktów końcowych usługi sieci wirtualnej lub wystąpienia zarządzanego z prywatnym punktem końcowym do hostowania usługi SSISDB. Jako warunek wstępny należy skonfigurować uprawnienia i ustawienia sieci wirtualnej dla usługi Azure-SSIS IR do przyłączenia się do sieci wirtualnej.

- Użyj uwierzytelniania usługi Azure Active Directory (Azure AD) z tożsamością zarządzaną dla fabryki danych, aby połączyć się z serwerem bazy danych SQL platformy Azure lub wystąpieniem zarządzanym. Jako warunek wstępny należy dodać tożsamość zarządzaną dla fabryki danych jako użytkownika bazy danych, który może utworzyć wystąpienie SSISDB.

- Dołącz do usługi Azure-SSIS IR w sieci wirtualnej lub skonfiguruj samodzielnie hostowany podczerwony środowiska jako serwer proxy dla usługi Azure-SSIS IR, aby uzyskać dostęp do danych w środowisku lokalnym.

W tym artykule pokazano, jak aprowizować usługę Azure-SSIS IR przy użyciu witryny Azure portal, usługi Azure PowerShell i szablonu usługi Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**. Jeśli nie masz jeszcze subskrypcji, możesz utworzyć bezpłatne konto [próbne.](https://azure.microsoft.com/pricing/free-trial/)

- **Serwer bazy danych SQL platformy Azure lub wystąpienie zarządzane (opcjonalnie).** Jeśli nie masz jeszcze serwera bazy danych, utwórz go w witrynie Azure Portal, zanim zaczniesz. Fabryka danych z kolei utworzy wystąpienie SSISDB na tym serwerze bazy danych. 

  Zaleca się utworzenie serwera bazy danych w tym samym regionie platformy Azure, co środowisko Integration Runtime. Ta konfiguracja umożliwia rejestrowania wykonywania zapisu środowiska wykonawczego integracji do usługi SSISDB bez przekraczania regionów platformy Azure.

  Należy pamiętać o następujących kwestiach:

  - Na podstawie wybranego serwera bazy danych wystąpienie SSISDB można utworzyć w twoim imieniu jako pojedynczą bazę danych, jako część puli elastycznej lub w wystąpieniu zarządzanym. Można go udostępnić w sieci publicznej lub dołączając do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie pojedynczej bazy danych usługi Azure SQL Database, puli elastycznej i wystąpienia zarządzanego](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) w tym artykule. 
  
    Jeśli używasz serwera bazy danych SQL azure z regułami zapory IP/punktów końcowych usługi sieci wirtualnej lub wystąpienia zarządzanego z prywatnym punktem końcowym do hostowania SSISDB lub jeśli potrzebujesz dostępu do danych lokalnych bez konfigurowania samodzielnego środowiska IR, musisz dołączyć do usługi Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie usługi Azure-SSIS IR do sieci wirtualnej.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

  - Upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest włączone dla serwera bazy danych. To ustawienie nie ma zastosowania, gdy używany jest serwer bazy danych Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpienie zarządzane z prywatnym punktem końcowym do hostowania usługi SSISDB. Aby uzyskać więcej informacji, zobacz artykuł [Secure your Azure SQL database (Zabezpieczenia bazy danych Azure SQL Database)](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Aby włączyć to ustawienie przy użyciu programu PowerShell, zobacz [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Dodaj adres IP komputera klienckiego lub zakres adresów IP, który zawiera adres IP komputera klienckiego, do listy adresów IP klienta w ustawieniach zapory serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Reguły zapory na poziomie serwera i na poziomie bazy danych usługi Azure SQL Database](../sql-database/sql-database-firewall-configure.md).

  - Można połączyć się z serwerem bazy danych przy użyciu uwierzytelniania SQL przy użyciu poświadczeń administratora serwera lub przy użyciu uwierzytelniania usługi Azure AD z tożsamością zarządzana dla fabryki danych. W przypadku tej ostatniej należy dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla usługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Upewnij się, że serwer bazy danych nie ma jeszcze wystąpienia SSISDB. Inicjowanie obsługi administracyjnej usługi Azure-SSIS IR nie obsługuje przy użyciu istniejącego wystąpienia SSISDB.

- **Sieć wirtualna usługi Azure Resource Manager (opcjonalnie)**. Musisz mieć sieć wirtualną usługi Azure Resource Manager, jeśli spełnia się co najmniej jeden z następujących warunków:
  - Hostujesz usługę SSISDB na serwerze bazy danych SQL azure z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym.
  - Chcesz połączyć się z lokalnymi magazynami danych z pakietów SSIS uruchomionych na platformie Azure-SSIS IR bez konfigurowania samodzielnie hostowanego środowiska IR.

- **Program Azure PowerShell (opcjonalnie)**. Postępuj zgodnie z instrukcjami w [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/install-az-ps), jeśli chcesz uruchomić skrypt programu PowerShell do aprowizowania usługi Azure-SSIS IR.

### <a name="regional-support"></a>Wsparcie regionalne

Aby uzyskać listę regionów platformy Azure, w których usługa Fabryka danych i usługa Azure-SSIS IR są dostępne, zobacz [Fabryka danych i dostępność SSIS IR według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Porównanie pojedynczej bazy danych SQL, puli elastycznej i wystąpienia zarządzanego

W poniższej tabeli porównano niektóre funkcje serwera usługi Azure SQL Database i wystąpienia zarządzanego w odniesieniu do usługi Azure-SSIR IR:

| Funkcja | Pojedyncza baza danych/pula elastyczna| Wystąpienie zarządzane |
|---------|--------------|------------------|
| **Planowanie** | Agent programu SQL Server jest niedostępny.<br/><br/>Zobacz [Planowanie wykonania pakietu w potoku fabryki danych](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Dostępny jest agent wystąpienia zarządzanego. |
| **Authentication** | Można utworzyć wystąpienie SSISDB z użytkownikiem zawartej bazy danych, który reprezentuje dowolną grupę usługi Azure AD z zarządzaną tożsamością fabryki danych jako element członkowski w **roli db_owner.**<br/><br/>Zobacz [Włącz uwierzytelnianie usługi Azure AD, aby utworzyć wystąpienie bazy danych SSISDB na serwerze bazy danych SQL platformy Azure](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Można utworzyć wystąpienie SSISDB z użytkownikiem zawartej bazy danych, który reprezentuje zarządzaną tożsamość fabryki danych. <br/><br/>Zobacz [Włącz uwierzytelnianie usługi Azure AD, aby utworzyć wystąpienie SSISDB w wystąpieniu zarządzanym usługi Azure SQL Database.](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance) |
| **Warstwa usług** | Podczas tworzenia usługi Azure-SSIS IR z serwerem bazy danych SQL azure, można wybrać warstwę usług dla SSISDB. Istnieje wiele warstw usług. | Podczas tworzenia usługi Azure-SSIS IR z wystąpieniem zarządzanym nie można wybrać warstwy usługi dla usługi SSISDB. Wszystkie bazy danych w wystąpieniu zarządzanym współużytkują ten sam zasób przydzielony do tego wystąpienia. |
| **Sieć wirtualna** | Urządzenie Azure-SSIS IR może dołączyć do sieci wirtualnej usługi Azure Resource Manager, jeśli używasz serwera bazy danych SQL azure z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej. | Urządzenie Azure-SSIS IR może dołączyć do sieci wirtualnej usługi Azure Resource Manager, jeśli używasz wystąpienia zarządzanego z prywatnym punktem końcowym. Sieć wirtualna jest wymagana, gdy nie włączysz publicznego punktu końcowego dla wystąpienia zarządzanego.<br/><br/>Jeśli dołączysz do usługi Azure-SSIS IR do tej samej sieci wirtualnej co wystąpienie zarządzane, upewnij się, że usługa Azure-SSIS IR znajduje się w innej podsieci niż wystąpienie zarządzane. Jeśli dołączysz do usługi Azure-SSIS IR do innej sieci wirtualnej niż wystąpienie zarządzane, zalecamy komunikację równorzędną sieci wirtualnej lub połączenie między sieciami. Zobacz [Łączenie aplikacji z wystąpieniem zarządzanym usługi Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transakcje rozproszone** | Ta funkcja jest obsługiwana za pomocą transakcji elastycznych. Transakcje koordynatora transakcji rozproszonych firmy Microsoft (MSDTC) nie są obsługiwane. Jeśli pakiety SSIS używają usługi MSDTC do koordynowania transakcji rozproszonych, należy rozważyć migrację do transakcji elastycznych dla usługi Azure SQL Database. Aby uzyskać więcej informacji, zobacz [Transakcje rozproszone w bazach danych w chmurze](../sql-database/sql-database-elastic-transactions-overview.md). | Bez pomocy technicznej. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Tworzenie środowiska wykonawczego integracji za pomocą portalu Azure

W tej sekcji można użyć witryny Azure portal, w szczególności interfejsu użytkownika usługi Data Factory (UI) lub aplikacji, aby utworzyć identyfikator Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

Aby utworzyć fabrykę danych za pośrednictwem witryny Azure portal, postępuj zgodnie z instrukcjami krok po kroku w [obszarze Tworzenie fabryki danych za pośrednictwem interfejsu użytkownika.](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) Wybierz **pozycję Przypnij do pulpitu nawigacyjnego,** aby umożliwić szybki dostęp po jego utworzeniu. 

Po utworzeniu fabryki danych otwórz jej stronę przeglądu w witrynie Azure portal. Wybierz kafelek **Monitor & Autor,** aby otworzyć jego stronę **Rozpocznij pracę** na osobnej karcie. Tam można kontynuować tworzenie usługi Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime

1. Na stronie **Wprowadzenie** wybierz kafelek **Konfigurowanie środowiska SSIS Integration Runtime**.

   ![Kafelek Konfigurowanie środowiska SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. W sekcji **Ustawienia ogólne** panelu Ustawienia środowiska **wykonawczego integracji** wykonaj następujące czynności.

   ![Ustawienia ogólne](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. W polu **Nazwa** wprowadź nazwę środowiska Integration Runtime.

   1. W polu **Opis** wprowadź opis środowiska Integration Runtime.

   1. W polu **Lokalizacja** wybierz lokalizację środowiska Integration Runtime. Wyświetlane są tylko obsługiwane lokalizacje. Zalecamy wybranie tej samej lokalizacji do hostowania bazy danych SSISDB co w przypadku serwera bazy danych.

   1. W przypadku **opcji Rozmiar węzła**wybierz rozmiar węzła w klastrze środowiska wykonawczego integracji. Wyświetlane są tylko obsługiwane rozmiary węzłów. Wybierz duży rozmiar węzła (skalowanie w górę), jeśli chcesz uruchomić wiele pakietów intensywnie korzystających z mocy obliczeniowej lub intensywnie korzystających z pamięci.

   1. W polu **Liczba węzłów** wybierz liczbę węzłów w klastrze środowiska Integration Runtime. Wyświetlane są tylko obsługiwane liczby węzłów. Wybierz duży klaster z wieloma węzłami (skalowanie w poziomie), jeśli chcesz uruchomić wiele pakietów równolegle.

   1. W przypadku **wersji/licencji**wybierz wersję PROGRAMU SQL Server dla środowiska wykonawczego integracji: Standardowy lub Enterprise. Wybierz enterprise, jeśli chcesz używać zaawansowanych funkcji w czasie wykonywania integracji.

   1. Aby **zaoszczędzić pieniądze,** wybierz opcję Korzyści hybrydowe platformy Azure dla środowiska wykonawczego integracji: **Tak** lub **Nie**. Wybierz **opcję Tak,** jeśli chcesz wprowadzić własną licencję programu SQL Server z pakietem Software Assurance, aby skorzystać z oszczędności kosztów podczas używania hybrydowego.

   1. Wybierz opcję **Dalej**.

1. W sekcji **Ustawienia SQL** wykonaj następujące kroki.

   ![Ustawienia SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Wybierz pole wyboru **Utwórz katalog SSIS (SSISDB) hostowany przez serwer/wystąpienie zarządzanej bazy danych SQL platformy Azure do przechowywania projektów/pakietów/środowisk/dzienników wykonywania,** aby wybrać model wdrażania pakietów do uruchomienia na platformie Azure-SSIS IR. Wybierz model wdrażania projektu, w którym pakiety są wdrażane w SSISDB hostowane przez serwer bazy danych, albo model wdrażania pakietów, w którym pakiety są wdrażane w systemach plików, udziałach plików lub plikach Azure. 
    
      Jeśli zaznaczysz to pole wyboru, musisz przynieść własny serwer bazy danych, aby hostować wystąpienie SSISDB, które utworzymy i będziemy zarządzać w Twoim imieniu.
   
      1. W polu **Subskrypcja** określ subskrypcję platformy Azure, z której korzysta Twój serwer bazy danych hostujący bazę danych SSISDB. 

      1. W polu **Lokalizacja** określ lokalizację serwera bazy danych hostującego bazę danych SSISDB. Zalecamy wybranie tej samej lokalizacji co w przypadku środowiska Integration Runtime. 

      1. W polu **Punkt końcowy serwera bazy danych wykazu** wybierz punkt końcowy serwera bazy danych hostującego bazę danych SSISDB. 
    
         Na podstawie wybranego serwera bazy danych wystąpienie SSISDB można utworzyć w twoim imieniu jako pojedynczą bazę danych, jako część puli elastycznej lub w wystąpieniu zarządzanym. Można go udostępnić w sieci publicznej lub dołączając do sieci wirtualnej. Aby uzyskać wskazówki dotyczące wybierania typu serwera bazy danych do hostowania SSISDB, zobacz [porównanie pojedynczej bazy danych usługi Azure SQL Database, puli elastycznej i wystąpienia zarządzanego](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) w tym artykule. 
    
         Jeśli wybierzesz serwer bazy danych SQL azure z regułami zapory IP/punktów końcowych usługi sieci wirtualnej lub wystąpienie zarządzane z prywatnym punktem końcowym do obsługi SSISDB lub jeśli potrzebujesz dostępu do danych lokalnych bez konfigurowania samodzielnie hostowanego środowiska IR, musisz dołączyć do usługi Azure-SSIS IR do sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie usługi Azure-SSIS IR do sieci wirtualnej.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) 

      1. Zaznacz pole wyboru **Użyj uwierzytelniania AAD z tożsamością zarządzaną dla usługi ADF,** aby wybrać metodę uwierzytelniania serwera bazy danych do hostowania bazy danych SSISDB. Wybierz uwierzytelnianie SQL lub uwierzytelnianie usługi Azure AD z tożsamością zarządzana dla fabryki danych. 
    
         Jeśli zaznaczysz to pole wyboru, musisz dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla usługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

      1. W przypadku **nazwy użytkownika administratora**wprowadź nazwę użytkownika uwierzytelniania SQL dla serwera bazy danych, aby hostować usługę SSISDB. 

      1. W przypadku **hasła administratora**wprowadź hasło uwierzytelniania SQL dla serwera bazy danych, aby hostować usługę SSISDB. 

      1. W przypadku **warstwy usługi bazy danych wykazu**wybierz warstwę usług dla serwera bazy danych do hostowania bazy danych SSISDB. Wybierz warstwę Podstawowa, Standardowa lub Premium lub wybierz nazwę puli elastycznej. 

      1. Wybierz **opcję Testuj połączenie**. Jeśli test zakończy się pomyślnie, wybierz przycisk **Dalej**. 

1. W sekcji **Ustawienia zaawansowane** wykonaj następujące czynności.

   ![Ustawienia zaawansowane](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. W przypadku **maksymalnych równoległych wykonań na węzeł**wybierz maksymalną liczbę pakietów do uruchomienia jednocześnie na węzeł w klastrze środowiska wykonawczego integracji. Wyświetlane są tylko obsługiwane liczby pakietów. Wybierz małą liczbę, jeśli chcesz użyć więcej niż jednego rdzenia do uruchomienia pojedynczego dużego pakietu, który jest wymagający mocy obliczeniowej lub pamięci. Wybierz dużą liczbę, jeśli chcesz uruchomić jeden lub więcej małych pakietów w jednym rdzeniu.

   1. Zaznacz pole wyboru **Dostosuj środowisko uruchomieniowe integracji platformy Azure-SSIS za pomocą dodatkowych konfiguracji systemu/instalacji składników,** aby wybrać, czy chcesz dodać ustawienia niestandardowe standardowe/ekspresowe w usłudze Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Ustawienia niestandardowe dla usługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Jeśli zaznaczysz to pole wyboru, wykonaj następujące kroki.

      ![Ustawienia zaawansowane z niestandardowymi ustawieniami](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. W przypadku **identyfikatora URI URI kontenera instalacji niestandardowej**wprowadź identyfikator URI sygnatury dostępu Współdzielonego kontenera, w którym przechowywane są skrypty i skojarzone pliki dla standardowych ustawień niestandardowych.

      1. W przypadku konfiguracji **niestandardowej express**wybierz pozycję **Nowy,** aby otworzyć panel Dodaj **konfigurację niestandardową,** a następnie wybierz dowolny **Add environment variable**typ w **Install licensed component**menu rozwijanym **Typ konfiguracji niestandardowej Express,** **np.**

         Jeśli wybierzesz typ **składnika Install licensed,** możesz wybrać wszystkie zintegrowane składniki od naszych partnerów z oprogramowania isv w menu rozwijanym **Nazwa składnika** i w razie potrzeby wprowadź klucz licencyjny produktu zakupiony od nich w polu **Klucz licencyjny.**
  
         Dodane ustawienia niestandardowe zostaną wyświetlone w sekcji **Ustawienia zaawansowane.** Aby je usunąć, możesz zaznaczyć ich pola wyboru, a następnie wybrać pozycję **Usuń**.

   1. Wybierz **pozycję Wybierz sieć wirtualną dla środowiska uruchomieniowego integracji platformy Azure-SSIS do dołączenia, zezwalaj podaj podajnika ADF na tworzenie pewnych zasobów sieciowych i opcjonalnie przynieś własne statyczne publiczne adresy IP,** aby wybrać, czy chcesz dołączyć środowisko wykonawcze integracji do sieci wirtualnej. 

      Wybierz go, jeśli używasz serwera bazy danych SQL Azure z regułami zapory IP/punktów końcowych usługi sieci wirtualnej lub wystąpienia zarządzanego z prywatnym punktem końcowym do hostowania SSISDB lub jeśli potrzebujesz dostępu do danych lokalnych (oznacza to, że masz lokalne źródła danych lub miejsca docelowe w pakietach SSIS) bez konfigurowania samodzielnie hostowanego podczerwania. Aby uzyskać więcej informacji, zobacz [Dołączanie usługi Azure-SSIS IR do sieci wirtualnej](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      Jeśli zaznaczysz to pole wyboru, wykonaj następujące kroki.

      ![Ustawienia zaawansowane sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. W przypadku **subskrypcji**wybierz subskrypcję platformy Azure, która ma sieć wirtualną.

      1. W przypadku **lokalizacji**wybrano tę samą lokalizację środowiska wykonawczego integracji.

      1. W przypadku **opcji Typ**wybierz typ sieci wirtualnej: klasyczny lub Usługę Azure Resource Manager. Zaleca się wybranie sieci wirtualnej usługi Azure Resource Manager, ponieważ klasyczne sieci wirtualne zostaną wkrótce przestarzałe.

      1. W przypadku **nazwy sieci wirtualnej**wybierz nazwę sieci wirtualnej. Powinien być taki sam, który jest używany dla serwera usługi Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania usługi SSISDB. Lub powinien być ten sam, który jest połączony z siecią lokalną. W przeciwnym razie może to być dowolna sieć wirtualna, aby przynieść własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR.

      1. W obszarze **Nazwa podsieci**wybierz nazwę podsieci dla sieci wirtualnej. Powinien być taki sam, który jest używany dla serwera usługi Azure SQL Database z punktami końcowymi usługi sieci wirtualnej do obsługi usługi SSISDB. Lub powinna być inna podsieć niż używana dla wystąpienia zarządzanego z prywatnym punktem końcowym do hostowania SSISDB. W przeciwnym razie może być dowolna podsieć, aby przynieść własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR.

      1. Zaznacz pole wyboru **Przynieś statyczne publiczne adresy IP dla środowiska uruchomieniowego integracji platformy Azure-SSIS,** aby wybrać, czy chcesz przywieźć własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR, aby umożliwić im korzystanie z zapory dla źródeł danych.

         Jeśli zaznaczysz to pole wyboru, wykonaj następujące kroki.

         1. W przypadku **pierwszego statycznego publicznego adresu IP**wybierz pierwszy statyczny publiczny adres IP, który spełnia wymagania dotyczące usługi Azure-SSIS IR. Jeśli nie masz żadnych, kliknij przycisk **Utwórz nowe** łącze, aby utworzyć statyczne publiczne adresy IP w witrynie Azure portal, a następnie kliknij przycisk odświeżania tutaj, aby można je było wybrać.
      
         1. W przypadku **drugiego statycznego publicznego adresu IP**wybierz drugi statyczny publiczny adres IP, który spełnia wymagania dotyczące usługi Azure-SSIS IR. Jeśli nie masz żadnych, kliknij przycisk **Utwórz nowe** łącze, aby utworzyć statyczne publiczne adresy IP w witrynie Azure portal, a następnie kliknij przycisk odświeżania tutaj, aby można je było wybrać.

   1. Wybierz pole wyboru **Konfigurowanie środowiska uruchomieniowego integracji hostowanego jako serwera proxy dla środowiska uruchomieniowego integracji platformy Azure-SSIS,** aby wybrać, czy chcesz skonfigurować samodzielnie hostowany podczerwony środowisko IR jako serwer proxy dla usługi Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Konfigurowanie samodzielnego podczerwonyego podczerwone jako serwera proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Jeśli zaznaczysz to pole wyboru, wykonaj następujące kroki.

      ![Ustawienia zaawansowane z samodzielnym podczerwonym](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. W przypadku **środowiska uruchomieniowego integracji hostowanego samodzielnie**wybierz istniejącą, samodzielnie hostowane środowisko IR jako serwer proxy dla usługi Azure-SSIS IR.

      1. W przypadku **usługi połączonej magazynu przemieszczania**wybierz istniejącą usługę połączonego magazynu obiektów Blob platformy Azure lub utwórz nową usługę przemieszczania.

      1. W przypadku **ścieżki przemieszczania**określ kontener obiektów blob na wybranym koncie magazynu obiektów Blob platformy Azure lub pozostaw go pustym, aby użyć domyślnego kontenera dla przemieszczania.

   1. Wybierz **pozycję Walidacja sieci wirtualnej** > **Kontynuuj**. 

1. W sekcji **Podsumowanie** przejrzyj wszystkie ustawienia inicjowania obsługi administracyjnej, dobieraj łącza do zalecanej dokumentacji i wybierz pozycję **Zakończ,** aby rozpocząć tworzenie środowiska wykonawczego integracji.

   > [!NOTE]
   > Z wyłączeniem dowolnego czasu instalacji niestandardowej, ten proces powinien zakończyć się w ciągu 5 minut. Może jednak upłynąć 20–30 minut, zanim usługa Azure-SSIS IR dołączy do sieci wirtualnej.
   >
   > Jeśli używasz SSISDB, usługa Data Factory połączy się z serwerem bazy danych w celu przygotowania bazy danych SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i dołącza do usługi Azure-SSIS IR do sieci wirtualnej.
   > 
   > Podczas aprowizowania azure-SSIS IR, dostęp redystrybucyjny i pakiet funkcji Platformy Azure dla SSIS są również zainstalowane. Te składniki zapewniają łączność z plikami programu Excel, plikami programu Access i różnymi źródłami danych platformy Azure, oprócz źródeł danych, które są już obsługiwane przez wbudowane składniki. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Ustawienia niestandardowe dla usługi Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Na karcie **Połączenia** w razie potrzeby przełącz na opcję **Środowiska Integration Runtime**. Wybierz pozycję **Odśwież**, aby odświeżyć stan.

   ![Stan tworzenia](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Za pomocą linków w kolumnie **Akcje** możesz zatrzymać/uruchomić, edytować lub usunąć środowisko Integration Runtime. Użyj ostatniego linku, aby wyświetlić kod JSON dla środowiska Integration Runtime. Przyciski edytowania i usuwania są włączone tylko wtedy, gdy środowisko IR jest zatrzymane.

   ![Akcje usługi Azure SSIS IR](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Środowiska Azure SSIS Integration Runtime w portalu

1. W interfejsie użytkownika usługi Azure Data Factory przełącz się na kartę **Edytowanie** i wybierz pozycję **Połączenia**. Następnie przełącz się do **integration runtimes** kartę, aby wyświetlić istniejące środowiska wykonawcze integracji w fabryce danych.

   ![Wyświetlanie istniejących środowisk IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Wybierz **pozycję Nowy,** aby utworzyć nową usługę Azure-SSIS IR.

   ![Środowisko Integration Runtime za pomocą menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. W panelu **Ustawienia środowiska wykonawczego integracji** wybierz **istniejące pakiety SSIS do wykonania na** kafelku Platformy Azure, a następnie wybierz pozycję **Dalej**.

   ![Określanie typu środowiska Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Aby uzyskać pozostałe kroki, aby skonfigurować azure-SSIS IR, zobacz [aprowizowanie sekcji środowiska wykonawczego integracji usługi Azure SSIS.](#provision-an-azure-ssis-integration-runtime)

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Tworzenie środowiska wykonawczego integracji za pomocą programu Azure PowerShell

W tej sekcji używasz programu Azure PowerShell do utworzenia usługi Azure-SSIS IR.

### <a name="create-variables"></a>Tworzenie zmiennych

Skopiuj i wklej następujący skrypt. Określ wartości zmiennych. 

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

Dodaj następujący skrypt, aby się zalogować i wybierz subskrypcję platformy Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Sprawdzanie poprawności połączenia z serwerem bazy danych

Dodaj następujący skrypt, aby sprawdzić poprawność serwera bazy danych SQL platformy Azure lub wystąpienia zarządzanego.

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

Dodaj następujący skrypt, aby automatycznie skonfigurować uprawnienia i ustawienia sieci wirtualnej dla środowiska wykonawczego integracji platformy Azure-SSIS do dołączenia.

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

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) przy użyciu polecenia [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

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

Uruchom następujące polecenia, aby utworzyć środowisko uruchomieniowe integracji platformy Azure-SSIS, które uruchamia pakiety SSIS na platformie Azure.

Jeśli nie używasz SSISDB, możesz `CatalogServerEndpoint`pominąć `CatalogPricingTier`, `CatalogAdminCredential` i parametry.

Jeśli serwer usługi Azure SQL Database nie jest używany z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z `VNetId` prywatnym `Subnet` punktem końcowym do hostowania bazy SSISDB lub wymagasz dostępu do danych lokalnych, możesz pominąć parametry i lub przekazać dla nich puste wartości. Można je również pominąć, jeśli skonfigurujesz samodzielnie hostowane IR jako serwer proxy dla usługi Azure-SSIS IR, aby uzyskać dostęp do danych w środowisku lokalnym. W przeciwnym razie nie można ich pominąć i należy przekazać prawidłowe wartości z konfiguracji sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Dołączanie usługi Azure-SSIS IR do sieci wirtualnej.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

Jeśli używasz wystąpienia zarządzanego do hostowania SSISDB, można pominąć `CatalogPricingTier` parametr lub przekazać pustą wartość dla niego. W przeciwnym razie nie można go pominąć i należy przekazać prawidłową wartość z listy obsługiwanych warstw cenowych dla usługi Azure SQL Database. Aby uzyskać więcej informacji, zobacz [Limity zasobów bazy danych SQL](../sql-database/sql-database-resource-limits.md).

Jeśli używasz uwierzytelniania usługi Azure AD z tożsamością zarządzana dla fabryki `CatalogAdminCredential` danych, aby połączyć się z serwerem bazy danych, możesz pominąć ten parametr. Należy jednak dodać tożsamość zarządzaną dla fabryki danych do grupy usługi Azure AD z uprawnieniami dostępu do serwera bazy danych. Aby uzyskać więcej informacji, zobacz [Włączanie uwierzytelniania usługi Azure AD dla usługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). W przeciwnym razie nie można go pominąć i musi przekazać prawidłowy obiekt utworzony z nazwy użytkownika administratora serwera i hasła do uwierzytelniania SQL.

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

### <a name="start-the-integration-runtime"></a>Uruchamianie środowiska wykonawczego integracji

Uruchom następujące polecenia, aby uruchomić środowisko uruchomieniowe integracji azure-SSIS.

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
> Z wyłączeniem dowolnego czasu instalacji niestandardowej, ten proces powinien zakończyć się w ciągu 5 minut. Może jednak upłynąć 20–30 minut, zanim usługa Azure-SSIS IR dołączy do sieci wirtualnej.
>
> Jeśli używasz SSISDB, usługa Data Factory połączy się z serwerem bazy danych w celu przygotowania bazy danych SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i dołącza do usługi Azure-SSIS IR do sieci wirtualnej.
> 
> Podczas aprowizowania azure-SSIS IR, dostęp redystrybucyjny i pakiet funkcji Platformy Azure dla SSIS są również zainstalowane. Te składniki zapewniają łączność z plikami programu Excel, plikami programu Access i różnymi źródłami danych platformy Azure, oprócz źródeł danych, które są już obsługiwane przez wbudowane składniki. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Ustawienia niestandardowe dla usługi Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Pełny skrypt

Oto pełny skrypt, który tworzy środowisko uruchomieniowe integracji azure-SSIS.

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

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Tworzenie środowiska wykonawczego integracji za pomocą szablonu usługi Azure Resource Manager

W tej sekcji można użyć szablonu usługi Azure Resource Manager do utworzenia środowiska wykonawczego integracji azure-SSIS. Oto przykładowy instruktaż:

1. Utwórz plik JSON z następującym szablonem usługi Azure Resource Manager. Zastąp wartości w nawiasach kątowych (symbolach zastępczych) własnymi wartościami.

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

2. Aby wdrożyć szablon usługi Azure `New-AzResourceGroupDeployment` Resource Manager, uruchom polecenie, jak pokazano w poniższym przykładzie. W przykładzie `ADFTutorialResourceGroup` jest nazwa grupy zasobów. `ADFTutorialARM.json`jest plik, który zawiera definicję JSON dla fabryki danych i Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    To polecenie tworzy fabrykę danych i azure-SSIS IR w nim, ale nie uruchamia IR.

3. Aby uruchomić usługę Azure-SSIS IR, uruchom `Start-AzDataFactoryV2IntegrationRuntime` polecenie:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Z wyłączeniem dowolnego czasu instalacji niestandardowej, ten proces powinien zakończyć się w ciągu 5 minut. Może jednak upłynąć 20–30 minut, zanim usługa Azure-SSIS IR dołączy do sieci wirtualnej.
>
> Jeśli używasz SSISDB, usługa Data Factory połączy się z serwerem bazy danych w celu przygotowania bazy danych SSISDB. Konfiguruje również uprawnienia i ustawienia dla sieci wirtualnej, jeśli określono, i dołącza do usługi Azure-SSIS IR do sieci wirtualnej.
> 
> Podczas aprowizowania azure-SSIS IR, dostęp redystrybucyjny i pakiet funkcji Platformy Azure dla SSIS są również zainstalowane. Te składniki zapewniają łączność z plikami programu Excel, plikami programu Access i różnymi źródłami danych platformy Azure, oprócz źródeł danych, które są już obsługiwane przez wbudowane składniki. Aby uzyskać informacje o innych składnikach, które można zainstalować, zobacz [Ustawienia niestandardowe dla usługi Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Wdrażanie pakietów usług SSIS

Jeśli używasz SSISDB, można wdrożyć pakiety do niego i uruchomić je na platformie Azure-SSIS IR przy użyciu narzędzi SQL Server Data Tools (SSDT) lub SQL Server Management Studio (SSMS). Narzędzia te łączą się z serwerem bazy danych za pośrednictwem punktu końcowego serwera: 

- W przypadku serwera usługi Azure SQL Database `<server name>.database.windows.net`format punktu końcowego serwera to .
- W przypadku wystąpienia zarządzanego z prywatnym punktem `<server name>.<dns prefix>.database.windows.net`końcowym format punktu końcowego serwera to .
- W przypadku wystąpienia zarządzanego z publicznym punktem `<server name>.public.<dns prefix>.database.windows.net,3342`końcowym format punktu końcowego serwera to . 

Jeśli nie używasz SSISDB, możesz wdrożyć pakiety w systemach plików, udziałach plików lub plikach Azure `dtinstall` `dtutil`i `dtexec` uruchomić je na urządzeniu Azure-SSIS IR przy użyciu narzędzi wiersza polecenia , i wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Wdrażanie pakietów SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

W obu przypadkach można również uruchomić wdrożone pakiety na platformie Azure-SSIS IR przy użyciu działania Wykonywanie pakietu SSIS w potokach fabryki danych. Aby uzyskać więcej informacji, zobacz [Wywoływanie wykonywania pakietu SSIS jako działania pierwszej klasy usługi Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Następne kroki

Zobacz inne tematy usługi Azure-SSIS IR w tej dokumentacji:

- [Środowisko uruchomieniowe integracji platformy Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje dotyczące środowiska wykonawczego integracji w ogóle, w tym Azure-SSIS IR.
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule pokazano, jak pobrać i zrozumieć informacje o usłudze Azure-SSIS IR.
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule pokazano, jak zatrzymać, uruchomić lub usunąć usługę Azure-SSIS IR. Pokazuje również, jak skalować w poziomie usługi Azure-SSIS IR, dodając więcej węzłów.
- [Wdrażanie, uruchamianie i monitorowanie pakietów SSIS na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Łączenie się z usługą SSISDB na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Planowanie wykonywania pakietów na platformie Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)