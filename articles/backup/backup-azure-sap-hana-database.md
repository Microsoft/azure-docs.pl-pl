---
title: Tworzenie kopii zapasowej bazy danych SAP HANA na platformie Azure przy użyciu Azure Backup
description: W tym artykule dowiesz się, jak utworzyć kopię zapasową bazy danych SAP HANA na maszynach wirtualnych platformy Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79248062"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Tworzenie kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure

Bazy danych SAP HANA są obciążeniami krytycznymi, które wymagają niewielkiego celu punktu odzyskiwania (RPO) i długoterminowego przechowywania. Można utworzyć kopię zapasową SAP HANA baz danych uruchomionych na maszynach wirtualnych platformy Azure, korzystając z [Azure Backup](backup-overview.md).

W tym artykule pokazano, jak utworzyć kopię zapasową SAP HANA baz danych, które są uruchomione na maszynach wirtualnych platformy Azure, do magazynu Recovery Services Azure Backup.

W tym artykule dowiesz się, jak:
> [!div class="checklist"]
>
> * Tworzenie i Konfigurowanie magazynu
> * Odnajdywanie baz danych
> * Skonfiguruj kopie zapasowe
> * Uruchamianie zadania tworzenia kopii zapasowej na żądanie

>[!NOTE]
>**Nietrwałe usuwanie programu SQL Server na maszynie wirtualnej platformy Azure oraz usuwanie nietrwałe dla SAP HANA w obciążeniach maszyn wirtualnych platformy Azure** jest teraz dostępne w wersji zapoznawczej.<br>
>Aby utworzyć konto w wersji zapoznawczej, Zapisz się do nas naAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się z [wymaganiami wstępnymi](tutorial-backup-sap-hana-db.md#prerequisites) i informacjami o [skrypcie przed rejestracją](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) , aby skonfigurować bazę danych do tworzenia kopii zapasowych.

### <a name="set-up-network-connectivity"></a>Konfigurowanie łączności sieciowej

Dla wszystkich operacji maszyna wirtualna SAP HANA wymaga łączności z publicznymi adresami IP platformy Azure. Operacje maszyny wirtualnej (Odnajdywanie bazy danych, konfigurowanie kopii zapasowych, Planowanie kopii zapasowych, przywracanie punktów odzyskiwania itd.) kończą się niepowodzeniem bez łączności z publicznymi adresami IP platformy Azure.

Ustanów łączność przy użyciu jednej z następujących opcji:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Zezwalaj na zakresy adresów IP centrum danych platformy Azure

Ta opcja zezwala na [zakresy adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) w pobranym pliku. Aby uzyskać dostęp do sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), użyj polecenia cmdlet Set-AzureNetworkSecurityRule. Jeśli lista bezpiecznych adresatów zawiera tylko adresy IP specyficzne dla regionu, należy również zaktualizować Azure Active Directory listę bezpiecznych adresatów, aby włączyć uwierzytelnianie.

#### <a name="allow-access-using-nsg-tags"></a>Zezwalaj na dostęp za pomocą tagów sieciowej grupy zabezpieczeń

Jeśli używasz sieciowej grupy zabezpieczeń do ograniczenia łączności, należy użyć znacznika usługi AzureBackup w celu zezwalania na dostęp wychodzący do Azure Backup. Ponadto należy również zezwolić na łączność z uwierzytelnianiem i transferem danych przy użyciu [reguł](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) dla usług Azure AD i Azure Storage. Można to zrobić w Azure Portal lub za pomocą programu PowerShell.

Aby utworzyć regułę przy użyciu portalu:

  1. W obszarze **wszystkie usługi**przejdź do pozycji **sieciowe grupy zabezpieczeń** i wybierz grupę zabezpieczeń sieci.
  2. W obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń dla ruchu wychodzącego** .
  3. Wybierz pozycję **Dodaj**. Wprowadź wszystkie wymagane szczegóły dotyczące tworzenia nowej reguły zgodnie z opisem w [ustawieniach reguły zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Upewnij się, że opcja **miejsce docelowe** jest ustawiona na **tag usługi** i **znacznik usługi docelowej** jest ustawiony na **AzureBackup**.
  4. Kliknij przycisk **Dodaj**, aby zapisać nowo utworzoną regułę zabezpieczeń dla ruchu wychodzącego.

Aby utworzyć regułę przy użyciu programu PowerShell:

 1. Dodawanie poświadczeń konta platformy Azure i aktualizowanie chmur narodowych<br/>
      `Add-AzureRmAccount`<br/>

 2. Wybierz subskrypcję usługi sieciowej grupy zabezpieczeń<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Wybierz sieciowej grupy zabezpieczeń<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Dodaj regułę zezwalającą na ruch wychodzący dla tagu usługi Azure Backup<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Dodaj regułę zezwalającą na ruch wychodzący dla tagu usługi magazynu<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Dodaj regułę zezwalającą na ruch wychodzący dla tagu usługi usługi azureactivedirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Zapisz sieciowej grupy zabezpieczeń<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Zezwalaj na dostęp za pomocą tagów zapory platformy Azure**. Jeśli używasz zapory platformy Azure, Utwórz regułę aplikacji przy użyciu [znacznika FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags)AzureBackup. Umożliwia to wychodzący dostęp do Azure Backup.

**Wdróż serwer proxy HTTP, aby kierować ruchem**. Podczas tworzenia kopii zapasowej bazy danych SAP HANA na maszynie wirtualnej platformy Azure rozszerzenie kopii zapasowej na maszynie wirtualnej używa interfejsów API HTTPS do wysyłania poleceń zarządzania do Azure Backup i danych do usługi Azure Storage. Rozszerzenie kopii zapasowej używa także usługi Azure AD do uwierzytelniania. Ruch rozszerzenia kopii zapasowej dla tych trzech usług należy kierować za pośrednictwem serwera proxy HTTP. Rozszerzenia są jedynym składnikiem skonfigurowanym do uzyskiwania dostępu do publicznego Internetu.

Opcje łączności obejmują następujące zalety i wady:

**Zaznaczyć** | **Zalety** | **Wady**
--- | --- | ---
Zezwolenie na zakresy adresów IP | Brak dodatkowych kosztów | Skomplikowane do zarządzania, ponieważ zakresy adresów IP zmieniają się w czasie <br/><br/> Zapewnia dostęp do całości platformy Azure, a nie tylko usługi Azure Storage
Użyj tagów usługi sieciowej grupy zabezpieczeń | Łatwiejsze zarządzanie, ponieważ zmiany zakresu są automatycznie scalane <br/><br/> Brak dodatkowych kosztów <br/><br/> | Może być używany tylko z sieciowych grup zabezpieczeń <br/><br/> Zapewnia dostęp do całej usługi
Używanie tagów nazwy FQDN zapory platformy Azure | Łatwiejsze zarządzanie, ponieważ wymagane są automatycznie zarządzane nazwy FQDN | Może być używany tylko z zaporą platformy Azure
Używanie serwera proxy HTTP | Szczegółowa kontrola w serwerze proxy za pośrednictwem adresów URL magazynu jest dozwolona <br/><br/> Dostęp do maszyn wirtualnych w jednym punkcie dostępu do Internetu <br/><br/> Nie podlegają zmianom adresów IP platformy Azure | Dodatkowe koszty związane z uruchamianiem maszyny wirtualnej za pomocą oprogramowania serwera proxy

#### <a name="private-endpoints"></a>Prywatne punkty końcowe

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Odnajdywanie baz danych

1. W magazynie w **wprowadzenie**kliknij pozycję **kopia zapasowa**. W **miejscu, w którym jest uruchomione Twoje obciążenie?** wybierz pozycję **SAP HANA na maszynie wirtualnej platformy Azure**.
2. Kliknij przycisk **Rozpocznij odnajdywanie**. Spowoduje to zainicjowanie odnajdywania niechronionych maszyn wirtualnych z systemem Linux w regionie magazynu.

   * Po odnajdywaniu w portalu są wyświetlane niechronione maszyny wirtualne wymienione według nazwy i grupy zasobów.
   * Jeśli maszyna wirtualna nie znajduje się w oczekiwany sposób, sprawdź, czy kopia zapasowa została już utworzona w magazynie.
   * Wiele maszyn wirtualnych może mieć taką samą nazwę, ale należą do różnych grup zasobów.

3. W obszarze **wybierz Virtual Machines**kliknij link, aby pobrać skrypt, który zapewnia uprawnienia do usługi Azure Backup do uzyskiwania dostępu do maszyn wirtualnych SAP HANA na potrzeby odnajdywania bazy danych.
4. Uruchom skrypt na każdej maszynie wirtualnej, w której znajdują się SAP HANA bazy danych, których kopię zapasową chcesz utworzyć.
5. Po uruchomieniu skryptu na maszynach wirtualnych w obszarze **wybierz Virtual Machines**Wybierz Maszyny wirtualne. Następnie kliknij pozycję **odkryj baz danych**.
6. Azure Backup odnajduje wszystkie SAP HANA bazy danych na maszynie wirtualnej. Podczas odnajdywania Azure Backup rejestruje maszynę wirtualną w magazynie i instaluje rozszerzenie na maszynie wirtualnej. Żaden Agent nie jest zainstalowany w bazie danych.

    ![Odnajdź SAP HANA bazy danych](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych  

Teraz Włącz tworzenie kopii zapasowej.

1. W kroku 2 kliknij pozycję **Konfiguruj kopię zapasową**.

    ![Skonfiguruj kopię zapasową](./media/backup-azure-sap-hana-database/configure-backup.png)
2. W obszarze **Wybierz elementy do utworzenia kopii zapasowej**wybierz wszystkie bazy danych, które mają być chronione > **OK**.

    ![Wybierz elementy do utworzenia kopii zapasowej](./media/backup-azure-sap-hana-database/select-items.png)
3. W obszarze >  **zasady tworzenia kopii**zapasowych**Wybierz pozycję Zasady tworzenia kopii**zapasowych, Utwórz nowe zasady tworzenia kopii zapasowych baz danych, zgodnie z poniższymi instrukcjami.

    ![Wybieranie zasad kopii zapasowych](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Po utworzeniu zasad w menu **kopia zapasowa** kliknij pozycję **Włącz kopię zapasową**.

    ![Włącz tworzenie kopii zapasowej](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Śledź postęp konfiguracji kopii zapasowej w obszarze **powiadomienia** portalu.

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych definiują, kiedy są tworzone kopie zapasowe, oraz czas ich przechowywania.

* Zasady są tworzone na poziomie magazynu.
* Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.

Określ ustawienia zasad w następujący sposób:

1. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.

   ![Wprowadź nazwę zasad](./media/backup-azure-sap-hana-database/policy-name.png)
2. W obszarze **Zasady pełnej kopii zapasowej** wybierz opcję **Częstotliwość tworzenia kopii zapasowej** (**Codziennie** lub **Co tydzień**).
   * **Codziennie**: Wybierz godzinę i strefę czasową, w której rozpoczyna się zadanie tworzenia kopii zapasowej.
       * Należy uruchomić pełną kopię zapasową. Nie można wyłączyć tej opcji.
       * Kliknij pozycję **Pełna kopia zapasowa**, aby wyświetlić zasady.
       * Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.
   * **Co tydzień**: Wybierz dzień tygodnia, godzinę i strefę czasową, w której jest uruchamiane zadanie tworzenia kopii zapasowej.

   ![Wybierz częstotliwość tworzenia kopii zapasowych](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. W obszarze **Zakres przechowywania**Skonfiguruj ustawienia przechowywania dla pełnej kopii zapasowej.
    * Domyślnie wszystkie opcje są zaznaczone. Wyczyść limity zakresu przechowywania, których nie chcesz używać, i ustaw te, które chcesz wykonać.
    * Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełny/różnicowa/log) wynosi siedem dni.
    * Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    * Kopia zapasowa dla określonego dnia jest otagowana i zachowywana na podstawie tygodniowego zakresu przechowywania i ustawienia.
    * Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.

4. W menu **pełne zasady tworzenia kopii zapasowej** kliknij przycisk **OK** , aby zaakceptować ustawienia.
5. Wybierz pozycję **różnicowa kopia zapasowa** , aby dodać zasady różnicowe.
6. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania.
    * Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.
    * Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższego okresu przechowywania, musisz użyć pełnych kopii zapasowych.

    ![Zasady różnicowych kopii zapasowych](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Przyrostowe kopie zapasowe nie są obecnie obsługiwane.

7. Kliknij przycisk **OK** , aby zapisać zasady i wrócić do głównego menu **zasad kopii zapasowych** .
8. Wybierz pozycję **kopia zapasowa dziennika** , aby dodać zasady kopii zapasowej dziennika transakcyjnego,
    * W obszarze **kopia zapasowa dziennika**wybierz pozycję **Włącz**.  Nie można go wyłączyć, ponieważ SAP HANA zarządza wszystkimi kopiami zapasowymi dzienników.
    * Ustaw kontrolkę częstotliwość i przechowywanie.

    > [!NOTE]
    > Kopie zapasowe dzienników zaczynają przepływać dopiero po pomyślnym wykonaniu pełnej kopii zapasowej.

9. Kliknij przycisk **OK** , aby zapisać zasady i wrócić do głównego menu **zasad kopii zapasowych** .
10. Po zdefiniowaniu zasad tworzenia kopii zapasowej kliknij przycisk **OK**.

> [!NOTE]
> Każda kopia zapasowa dziennika jest łańcuchem do poprzedniej pełnej kopii zapasowej w celu utworzenia łańcucha odzyskiwania. Ta pełna kopia zapasowa zostanie zachowana do momentu wygaśnięcia ostatniej kopii zapasowej dziennika. Może to oznaczać, że pełna kopia zapasowa jest przechowywana przez dodatkowy okres, aby upewnić się, że wszystkie dzienniki mogą zostać odzyskane. Załóżmy, że użytkownik dysponuje tygodniową pełną kopią zapasową, różnicą dzienną i 2-godzinnymi dziennikami. Wszystkie z nich są przechowywane przez 30 dni. Jednak tydzień pełny może być naprawdę oczyszczony/usunięty tylko po udostępnieniu kolejnej pełnej kopii zapasowej, tj. po upływie 30 dni. Załóżmy, że cotygodniowe pełne kopie zapasowe odbywają się na LIS 16. Zgodnie z zasadami przechowywania należy je zachować do 16 grudnia. Ostatnia kopia zapasowa dziennika dla tego stanu jest wykonywana przed następnym zaplanowanym zaplanowaną godziną. Do momentu udostępnienia tego dziennika do grudnia 22 nie można usunąć. W związku z tym do gru.

## <a name="run-an-on-demand-backup"></a>Uruchamianie kopii zapasowej na żądanie

Kopie zapasowe są uruchamiane zgodnie z harmonogramem zasad. Kopię zapasową można uruchomić na żądanie w następujący sposób:

1. W menu magazyn kliknij pozycję **elementy kopii zapasowej**.
2. W obszarze **elementy kopii zapasowej**wybierz maszynę wirtualną z uruchomioną SAP HANA bazą danych, a następnie kliknij pozycję **Utwórz kopię zapasową teraz**.
3. W obszarze **kopia zapasowa**Użyj formantu kalendarza, aby wybrać ostatni dzień przechowywania punktu odzyskiwania. Następnie kliknij przycisk **OK**.
4. Monitoruj powiadomienia portalu. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu > >  **zadania tworzenia kopii zapasowej****w toku**. W zależności od rozmiaru bazy danych Tworzenie początkowej kopii zapasowej może chwilę potrwać.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Uruchamianie kopii zapasowej SAP HANA Studio w bazie danych z włączoną funkcją Azure Backup

Jeśli chcesz utworzyć lokalną kopię zapasową bazy danych, której kopia zapasowa jest tworzona przy użyciu Azure Backup, wykonaj następujące czynności:

1. Poczekaj na zakończenie wszystkich pełnych lub dzienników kopii zapasowych bazy danych. Sprawdź stan w SAP HANA Studio/Panel sterowania.
2. Wyłącz kopie zapasowe dzienników i ustaw wykaz kopii zapasowych w systemie plików dla odpowiedniej bazy danych.
3. Aby to zrobić, kliknij dwukrotnie pozycję **systemdb** > **Konfiguracja** > systemdb**Wybierz pozycję Filtr bazy danych** > **(log)**.
4. Ustaw **enable_auto_log_backup** na wartość **nie**.
5. Ustaw **log_backup_using_backint** na **wartość false**.
6. Wykonaj pełną kopię zapasową bazy danych.
7. Poczekaj na zakończenie pełnej kopii zapasowej i kopii zapasowej wykazu.
8. Przywróć poprzednie ustawienia z powrotem do tych dla platformy Azure:
    * Ustaw wartość **enable_auto_log_backup** na **tak**.
    * Ustaw **log_backup_using_backint** na **wartość true**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Dowiedz się [, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
