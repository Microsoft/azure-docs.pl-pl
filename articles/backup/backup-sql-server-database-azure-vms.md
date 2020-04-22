---
title: Tworzenie kopii zapasowych baz danych programu SQL Server na maszynach wirtualnych platformy Azure
description: W tym artykule dowiesz się, jak wykonać kopię zapasową baz danych programu SQL Server na maszynach wirtualnych platformy Azure za pomocą usługi Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 887f15deed74330cf132e0574d166c074d2c7cad
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685710"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Tworzenie kopii zapasowych baz danych programu SQL Server na maszynach wirtualnych platformy Azure

Bazy danych programu SQL Server są krytyczne obciążenia, które wymagają celu niski punkt odzyskiwania (RPO) i długoterminowego przechowywania. Można wykonać kopię zapasową baz danych programu SQL Server uruchomionych na maszynach wirtualnych platformy Azure przy użyciu [usługi Azure Backup.](backup-overview.md)

W tym artykule pokazano, jak wykonać kopię zapasową bazy danych programu SQL Server, która jest uruchomiona na maszynie Wirtualnej Platformy Azure do magazynu usług Azure Backup Recovery Services.

W tym artykule dowiesz się, jak:

> [!div class="checklist"]
>
> * Tworzenie i konfigurowanie magazynu.
> * Odnajduj bazy danych i skonfiguruj kopie zapasowe.
> * Konfigurowanie ochrony automatycznej dla baz danych.

>[!NOTE]
>**Usuwanie trędowak dla serwera SQL w usłudze Azure VM i usuwanie nietrwałe dla sap HANA w obciążeniach maszyn wirtualnych platformy Azure** jest teraz dostępne w wersji zapoznawczej.<br>
>Aby zapisać się do podglądu, napisz do nas naAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Wymagania wstępne

Przed utworzeniem kopii zapasowej bazy danych programu SQL Server sprawdź następujące kryteria:

1. Identyfikowanie lub tworzenie [magazynu usług odzyskiwania](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) w tym samym regionie i subskrypcji co maszyna wirtualna obsługująca wystąpienie programu SQL Server.
2. Sprawdź, czy maszyna wirtualna ma [łączność sieciową](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Upewnij się, że bazy danych programu SQL Server są zgodne z [wytycznymi dotyczącymi nazewnictwa bazy danych dla usługi Azure Backup.](#database-naming-guidelines-for-azure-backup)
4. Sprawdź, czy nie masz żadnych innych rozwiązań do tworzenia kopii zapasowych włączonych dla bazy danych. Wyłącz wszystkie inne kopie zapasowe programu SQL Server przed utworzeniem kopii zapasowej bazy danych.

> [!NOTE]
> Można włączyć usługę Azure Backup dla maszyny Wirtualnej platformy Azure, a także dla bazy danych programu SQL Server uruchomionej na maszynie wirtualnej bez konfliktów.

### <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

W przypadku wszystkich operacji maszyna wirtualna programu SQL Server wymaga łączności z publicznymi adresami IP platformy Azure. Operacje maszyn wirtualnych (odnajdowanie bazy danych, konfigurowanie kopii zapasowych, planowanie tworzenia kopii zapasowych, przywracanie punktów odzyskiwania itd.) kończą się niepowodzeniem bez łączności z publicznymi adresami IP platformy Azure.

Nawiązuj łączność przy użyciu jednej z następujących opcji:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Zezwalaj na zakresy adresów IP centrum danych platformy Azure

Ta opcja umożliwia [zakresy adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) w pobranym pliku. Aby uzyskać dostęp do sieciowej grupy zabezpieczeń (NSG), należy użyć polecenia cmdlet Set-AzureNetworkSecurityRule. Jeśli lista bezpiecznych adresatów zawiera tylko adresy IP specyficzne dla regionu, należy również zaktualizować listę bezpiecznych adresatów tagu usługi Azure Active Directory (Azure AD), aby włączyć uwierzytelnianie.

#### <a name="allow-access-using-nsg-tags"></a>Zezwalaj na dostęp przy użyciu znaczników NSG

Jeśli używasz sieciowej sieciowej sieciowej do ograniczania łączności, należy użyć tagu usługi AzureBackup, aby zezwalać na dostęp wychodzący do usługi Azure Backup. Ponadto należy również zezwolić na łączność dla uwierzytelniania i transferu danych przy użyciu [reguł](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) dla usługi Azure AD i usługi Azure Storage. Można to zrobić za pośrednictwem witryny Azure portal lub za pośrednictwem programu PowerShell.

Aby utworzyć regułę za pomocą portalu:

  1. W **obszarze Wszystkie usługi**przejdź do grup zabezpieczeń **Sieć** i wybierz grupę zabezpieczeń sieci.
  2. Wybierz **pozycję Reguły zabezpieczeń wychodzących** w obszarze **Ustawienia**.
  3. Wybierz pozycję **Dodaj**. Wprowadź wszystkie szczegóły wymagane do utworzenia nowej reguły zgodnie z opisem w [ustawieniach reguły zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Upewnij się, że opcja **Miejsce docelowe** jest **ustawiona** na Tag usługi i **Tag usługi docelowej** jest ustawiony na **AzureBackup**.
  4. Kliknij **przycisk Dodaj**, aby zapisać nowo utworzoną regułę zabezpieczeń wychodzących.

Aby utworzyć regułę przy użyciu programu PowerShell:

 1. Dodawanie poświadczeń konta platformy Azure i aktualizowanie chmur krajowych<br/>
      `Add-AzureRmAccount`<br/>

 2. Wybierz subskrypcję nsg<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Wybierz nsg<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Dodaj regułę zezwalania na ruch wychodzący dla tagu usługi Azure Backup<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Dodaj regułę zezwalania na ruch wychodzący dla tagu usługi Magazyn<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Dodaj regułę zezwalania na ruch wychodzący dla tagu usługi AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Zapisz nsg<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Zezwól na dostęp przy użyciu tagów Zapory platformy Azure**. Jeśli używasz Zapory platformy Azure, utwórz regułę aplikacji przy użyciu [tagu FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags)usługi AzureBackup . Dzięki temu dostęp wychodzący do usługi Azure Backup.

**Wdrażanie serwera proxy HTTP w celu kierowania ruchu**. Podczas tworzenia kopii zapasowej bazy danych programu SQL Server na maszynie Wirtualnej platformy Azure rozszerzenie kopii zapasowej na maszynie Wirtualnej używa interfejsów API HTTPS do wysyłania poleceń zarządzania do usługi Azure Backup i danych do usługi Azure Storage. Rozszerzenie kopii zapasowej używa również usługi Azure AD do uwierzytelniania. Ruch rozszerzenia kopii zapasowej dla tych trzech usług należy kierować za pośrednictwem serwera proxy HTTP. Nie ma żadnych domen wieloznacznych w użyciu usługi Azure Backup, aby dodać do listy dozwolonych dla reguł proxy. Należy użyć publicznych zakresów adresów IP dla tych usług świadczonych przez platformę Azure. Rozszerzenia są jedynym składnikiem skonfigurowanym do uzyskiwania dostępu do publicznego internetu.

Opcje łączności obejmują następujące zalety i wady:

**Opcja** | **Zalety** | **Wady**
--- | --- | ---
Zezwolenie na zakresy adresów IP | Brak dodatkowych kosztów | Skomplikowane zarządzanie, ponieważ zakresy adresów IP zmieniają się w czasie <br/><br/> Zapewnia dostęp do całej platformy Azure, nie tylko usługi Azure Storage
Używanie tagów usług NSG | Łatwiejsze zarządzanie, ponieważ zmiany zakresu są automatycznie scalane <br/><br/> Brak dodatkowych kosztów <br/><br/> | Może być używany tylko z nsgs <br/><br/> Zapewnia dostęp do całej usługi
Używanie tagów FQDN zapory platformy Azure | Łatwiejsze zarządzanie, ponieważ wymagane nazwy FQDN są automatycznie zarządzane | Może być używany tylko z Zaporą platformy Azure
Używanie serwera proxy HTTP | Pojedynczy punkt dostępu do Internetu dla maszyn wirtualnych <br/> | Dodatkowe koszty uruchomienia maszyny wirtualnej z oprogramowaniem proxy <br/> Brak opublikowanych adresów FQDN, reguły zezwalają na zmiany adresu IP platformy Azure

#### <a name="private-endpoints"></a>Prywatne punkty końcowe

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Wskazówki dotyczące nazewnictwa bazy danych dla usługi Azure Backup

Unikaj używania następujących elementów w nazwach baz danych:

* Przestrzenie końcowe i wiodące
* Końcowe wykrzykniki (!)
* Zamykanie nawiasów kwadratowych (])
* Średnik ';'
* Ukośnik do przodu '/'

Aliasing jest dostępny dla nieobsługiconych znaków, ale zalecamy ich unikanie. Aby uzyskać więcej informacji, zobacz [Understanding the Table Service Data Model (Omówienie modelu danych usługi Table Service)](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

>[!NOTE]
>Operacja **Konfigurowanie ochrony** dla baz danych ze znakami specjalnymi, takimi jak "+" lub "&" w ich nazwie, nie jest obsługiwana. Można zmienić nazwę bazy danych lub włączyć **automatyczną ochronę,** która może skutecznie chronić te bazy danych.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Odnajdowanie baz danych programu SQL Server

Jak odnajdować bazy danych uruchomione na maszynie Wirtualnej:

1. W [witrynie Azure Portal](https://portal.azure.com) otwórz magazyn usługi Recovery Services używany do tworzenia kopii zapasowych bazy danych.

2. Na **pulpicie** nawigacyjnym magazynu usług odzyskiwania wybierz pozycję **Kopia zapasowa**.

   ![Wybieranie pozycji Utwórz kopię zapasową w celu otwarcia menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. W **celu tworzenia kopii zapasowej,** **Azure** **ustawić, gdzie jest uruchomione obciążenie?**

4. W obszarze **Co ma zawierać kopia zapasowa** wybierz pozycję **SQL Server na maszynie wirtualnej platformy Azure**.

    ![Wybieranie programu SQL Server na maszynie wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. W **obszarze Odnajdywanie bloków** > DB celów kopii zapasowej**na maszynach wirtualnych**wybierz pozycję **Rozpocznij odnajdywanie,** aby wyszukać niechronione maszyny wirtualne w ramach subskrypcji. To wyszukiwanie może zająć trochę czasu, w zależności od liczby niechronionych maszyn wirtualnych w subskrypcji.

   * Niechronione maszyny wirtualne powinny zostać wyświetlone na liście po odnalezieniu, uporządkowane według nazwy i grupy zasobów.
   * Jeśli maszyny Wirtualnej nie ma na liście zgodnie z oczekiwaniami, sprawdź, czy jest już utworzona kopia zapasowa w magazynie.
   * Wiele maszyn wirtualnych może mieć taką samą nazwę, ale będą należeć do różnych grup zasobów.

     ![Kopia zapasowa jest w stanie oczekiwania podczas wyszukiwania baz danych na maszynach wirtualnych](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na liście maszyn wirtualnych wybierz maszynę wirtualną z uruchomioną bazą danych programu SQL Server i wybierz pozycję **Odnajdź bazy danych**.

7. Śledzenie odnajdowania bazy danych w **obszarze Powiadomienia**. Czas wymagany dla tej akcji zależy od liczby baz danych maszyn wirtualnych. Po odnalezieniu wybranych baz danych zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Usługa Azure Backup odnajduje wszystkie bazy danych programu SQL Server na maszynie wirtualnej. Podczas odnajdywania w tle występują następujące elementy:

    * Usługa Azure Backup rejestruje maszynę wirtualną z magazynem do wykonywania kopii zapasowych obciążenia. Wszystkie bazy danych na zarejestrowanej maszynie Wirtualnej można wywrzeć tylko kopię zapasową w tym magazynie.
    * Usługa Azure Backup instaluje rozszerzenie AzureBackupWindowsWorkload na maszynie wirtualnej. Żaden agent nie jest zainstalowany w bazie danych SQL.
    * Usługa Azure Backup tworzy konto usługi NT Service\AzureWLBackupPluginSvc na maszynie wirtualnej.
      * Wszystkie operacje tworzenia kopii zapasowych i przywracania korzystają z konta usługi.
      * Usługa NT\AzureWLBackupPluginSvc wymaga uprawnień sysadmin SQL. Wszystkie maszyny wirtualne programu SQL Server utworzone w portalu Marketplace są wyposażone w zainstalowany program SqlIaaSExtension. Rozszerzenie AzureBackupWindowsWorkload używa rozszerzenia SQLIaaSExtension w celu automatycznego uzyskania wymaganych uprawnień.
    * Jeśli maszyna wirtualna nie została utworzona z portalu Marketplace lub jeśli korzystasz z programów SQL 2008 i 2008 R2, maszyna wirtualna może nie mieć zainstalowanej aplikacji SqlIaaSExtension, a operacja odnajdywania kończy się niepowodzeniem z komunikatem o błędzie UserErrorSQLNoSysAdminMembership. Aby rozwiązać ten problem, postępuj zgodnie z instrukcjami w obszarze [Ustawianie uprawnień maszyn wirtualnych](backup-azure-sql-database.md#set-vm-permissions).

        ![Wybieranie maszyny wirtualnej i bazy danych](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych  

1. W kroku 2 **celu** > tworzenia kopii**zapasowej: Konfigurowanie kopii zapasowej**wybierz pozycję **Konfiguruj kopię zapasową**.

   ![Wybieranie pozycji Konfiguruj kopię zapasową](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. W **obszarze Wybierz elementy do kopii zapasowej**są widoczne wszystkie zarejestrowane grupy dostępności i autonomiczne wystąpienia programu SQL Server. Zaznacz strzałkę po lewej stronie wiersza, aby rozwinąć listę wszystkich niechronionych baz danych w tym wystąpieniu lub w grupie Dostępności zawsze włączone.  

    ![Wyświetlanie wszystkich wystąpień programu SQL Server z autonomicznymi bazami danych](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Wybierz wszystkie bazy danych, które chcesz chronić, a następnie wybierz przycisk **OK**.

   ![Chronienie bazy danych](./media/backup-azure-sql-database/select-database-to-protect.png)

   Aby zoptymalizować obciążenia kopii zapasowych, usługa Azure Backup ustawia maksymalną liczbę baz danych w jednym zadaniu tworzenia kopii zapasowej na 50.

     * Aby chronić więcej niż 50 baz danych, skonfiguruj wiele kopii zapasowych.
     * Aby [włączyć](#enable-auto-protection) całe wystąpienie lub grupę Dostępność Zawsze włączone, na liście rozwijanej **AUTOPROTECT** wybierz pozycję **ON**, a następnie wybierz **przycisk OK**.

    > [!NOTE]
    > Funkcja [automatycznej ochrony](#enable-auto-protection) nie tylko umożliwia ochronę we wszystkich istniejących bazach danych jednocześnie, ale także automatycznie chroni wszystkie nowe bazy danych dodane do tego wystąpienia lub grupy dostępności.  

4. Wybierz **przycisk OK,** aby otworzyć **zasadę tworzenia kopii zapasowych**.

    ![Włączanie automatycznej ochrony dla grupy Dostępność zawsze włączone](./media/backup-azure-sql-database/enable-auto-protection.png)

5. W **obszarze Zasady tworzenia kopii zapasowych**wybierz zasadę, a następnie wybierz przycisk **OK**.

   * Wybierz domyślną zasadę jako HourlyLogBackup.
   * Wybrać istniejące zasady kopii zapasowych utworzone wcześniej dla środowiska SQL.
   * Zdefiniować nowe zasady oparte na własnym celu punktu odzyskiwania i zakresie przechowywania.

     ![Wybieranie pozycji Zasady kopii zapasowych](./media/backup-azure-sql-database/select-backup-policy.png)

6. W **obszarze Kopia zapasowa**wybierz pozycję **Włącz tworzenie kopii zapasowej**.

    ![Włączanie wybranych zasad kopii zapasowych](./media/backup-azure-sql-database/enable-backup-button.png)

7. Śledź postęp konfiguracji w obszarze **Powiadomienia** portalu.

    ![Obszar powiadomień](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady kopii zapasowych określają, kiedy są tworzone kopie zapasowe i jak długo są przechowywane.

* Zasady są tworzone na poziomie magazynu.
* Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.
* Po utworzeniu zasad kopii zapasowych ustawieniem domyślnym będzie pełna kopia zapasowa tworzona codziennie.
* Możesz dodać różnicową kopię zapasową, ale tylko jeśli skonfigurujesz tworzenie pełnych kopii zapasowych co tydzień.
* Dowiedz się więcej o [różnych typach zasad tworzenia kopii zapasowych](backup-architecture.md#sql-server-backup-types).

Aby utworzyć nowe zasady kopii zapasowych:

1. W przechowalni wybierz pozycję **Zasady** > tworzenia kopii zapasowych**Dodaj**.
2. W **programie Dodaj**wybierz pozycję SQL Server w **usłudze Azure VM,** aby zdefiniować typ zasad.

   ![Wybieranie typu nowych zasad kopii zapasowych](./media/backup-azure-sql-database/policy-type-details.png)

3. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.
4. W **obszarze Zasady pełnej kopii zapasowej**wybierz opcję Częstotliwość tworzenia kopii **zapasowych**. Wybierz opcję **Dzienna** lub **Tygodniowa**.

   * W przypadku opcji **Codziennie** wybierz godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej.
   * W przypadku opcji **Co tydzień** wybierz dzień tygodnia, godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej.
   * Uruchom pełną kopię zapasową, ponieważ nie można wyłączyć opcji **Pełna kopia zapasowa.**
   * Wybierz **pełną kopię zapasową,** aby wyświetlić zasady.
   * Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.

     ![Pola nowych zasad kopii zapasowych](./media/backup-azure-sql-database/full-backup-policy.png)  

5. W **polu ZAKRES PRZECHOWYWANIA**wszystkie opcje są wybierane domyślnie. Wyczyść wszystkie limity zakresu przechowywania, których nie chcesz, a następnie ustaw interwały do użycia.

    * Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełny, różnicowy i dziennik) wynosi siedem dni.
    * Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    * Kopia zapasowa dla określonego dnia jest oznaczana i zachowywana na podstawie tygodniowego zakresu przechowywania i ustawienia tygodniowego przechowywania.
    * Miesięczne i roczne zakresy retencji zachowują się w podobny sposób.

       ![Ustawienia interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)

6. W menu **zasad pełnej kopii zapasowej** wybierz pozycję **OK**, aby zaakceptować ustawienia.
7. Aby dodać zasady tworzenia różnicowej kopii zapasowej, wybierz pozycję **Różnicowa kopia zapasowa**.

   ![Ustawienia interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Otwieranie menu zasad różnicowej kopii zapasowej](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania.

    * Można wyzwolić tylko jedną różnicową kopię zapasową dziennie.
    * Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Aby uzyskać dłuższe przechowywanie, użyj pełnych kopii zapasowych.

9. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.

10. Aby dodać zasady kopii zapasowej dziennika transakcji, wybierz pozycję **Kopia zapasowa dziennika**.
11. W obszarze **Kopia zapasowa dziennika** wybierz pozycję **Włącz**, a następnie ustaw kontrolki częstotliwości i przechowywania. Kopie zapasowe dziennika mogą występować tak często, jak co 15 minut i mogą być przechowywane przez maksymalnie 35 dni.
12. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.

    ![Edytowanie zasad kopii zapasowej dziennika](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. W menu **zasady kopii zapasowej** wybierz, czy włączyć **kompresję kopii zapasowej SQL,** czy nie. Ta opcja jest domyślnie wyłączona. Jeśli ta opcja jest włączona, program SQL Server wyśle skompresowany strumień kopii zapasowej do interfejsu VDI.  Należy pamiętać, że usługa Azure Backup zastępuje domyślne ustawienia domyślne poziomu wystąpienia za pomocą klauzuli kompresji / NO_COMPRESSION w zależności od wartości tego formantu.

14. Po zakończeniu edycji zasad kopii zapasowych wybierz pozycję **OK**.

> [!NOTE]
> Każda kopia zapasowa dziennika jest przywiązywalem do poprzedniej pełnej kopii zapasowej w celu utworzenia łańcucha odzyskiwania. Ta pełna kopia zapasowa zostanie zachowana do czasu wygaśnięcia przechowywania ostatniej kopii zapasowej dziennika. Może to oznaczać, że pełna kopia zapasowa jest przechowywana przez dodatkowy okres, aby upewnić się, że wszystkie dzienniki można odzyskać. Załóżmy, że użytkownik ma cotygodniową pełną kopię zapasową, dzienną różnicę i 2-godzinne dzienniki. Wszystkie z nich są przechowywane przez 30 dni. Ale, tygodniowy pełny może być naprawdę oczyszczone / usunięte tylko po następnej pełnej kopii zapasowej jest dostępna, czyli po 30 + 7 dni. Powiedzmy, że cotygodniowa pełna kopia zapasowa ma miejsce 16 listopada. Zgodnie z zasadami przechowywania, należy zachować do 16 grudnia. Ostatnia kopia zapasowa dziennika dla tej pełnej dzieje się przed następnym zaplanowane pełne, na 22 listopada. Dopóki ten dziennik nie będzie dostępny do 22 grudnia, 16 listopada nie można usunąć pełnej. Tak, 16 listopada pełne jest zachowane do 22 grudnia.

## <a name="enable-auto-protection"></a>Włączanie ochrony automatycznej  

Można włączyć automatyczną ochronę, aby automatycznie tworzenie kopii zapasowych wszystkich istniejących i przyszłych baz danych w autonomicznym wystąpieniu programu SQL Server lub w grupie dostępności zawsze włączone.

* Nie ma limitu liczby baz danych, które można wybrać do automatycznej ochrony w tym samym czasie.
* Nie można selektywnie chronić ani wykluczać baz danych z ochrony w wystąpieniu w momencie włączania automatycznej ochrony.
* Jeśli wystąpienie zawiera już niektóre chronione bazy danych, pozostaną one chronione zgodnie z odpowiednimi zasadami, nawet po włączeniu automatycznej ochrony. Wszystkie niechronione bazy danych dodane później będą miały tylko jedną zasadę zdefiniowaną w czasie włączania automatycznej ochrony, wymienioną w obszarze **Konfiguruj kopię zapasową**. Można jednak później zmienić zasady skojarzone z automatycznie chroniona bazą danych.  

Aby włączyć automatyczną ochronę:

  1. W obszarze **Wybieranie elementów do wykonania kopii zapasowej** wybierz wystąpienie, dla którego chcesz włączyć ochronę automatyczną.
  2. Wybierz listę rozwijaną w obszarze **AUTOPROTECT**, wybierz **wł.,** a następnie wybierz przycisk **OK**.

      ![Włączanie automatycznej ochrony w grupie dostępności](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Tworzenie kopii zapasowych jest konfigurowane wspólnie dla wszystkich baz danych i można je śledzić w obszarze **Zadania kopii zapasowej**.

Jeśli chcesz wyłączyć automatyczną ochronę, wybierz nazwę wystąpienia w obszarze **Konfiguruj kopię zapasową,** a następnie wybierz pozycję **Wyłącz automatyczne zabezpieczenie** dla wystąpienia. Kopie zapasowe wszystkich baz danych będą nadal archiwizowane, ale przyszłe bazy danych nie będą automatycznie chronione.

![Wyłączanie automatycznej ochrony w tym wystąpieniu](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Następne kroki

Instrukcje:

* [Przywracanie kopii zapasowych baz danych programu SQL Server](restore-sql-database-azure-vm.md)
* [Zarządzanie bazami danych programu SQL Server z kopiami zapasowymi](manage-monitor-sql-database-backup.md)
