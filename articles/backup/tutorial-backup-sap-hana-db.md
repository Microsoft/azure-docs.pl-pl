---
title: Samouczek — Tworzenie kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure
description: W tym samouczku dowiesz się, jak utworzyć kopię zapasową SAP HANA baz danych działających na maszynie wirtualnej platformy Azure do magazynu Azure Backup Recovery Services.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: cb1fc4c1b9bfa2025850f16d175ba83bd5ee1470
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747231"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Samouczek: Tworzenie kopii zapasowych baz danych SAP HANA na maszynie wirtualnej platformy Azure

W tym samouczku pokazano, jak utworzyć kopię zapasową SAP HANA baz danych działających na maszynach wirtualnych platformy Azure do magazynu Azure Backup Recovery Services. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
>
> * Tworzenie i Konfigurowanie magazynu
> * Odnajdywanie baz danych
> * Skonfiguruj kopie zapasowe

[Oto](sap-hana-backup-support-matrix.md#scenario-support) wszystkie scenariusze, które obecnie są obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem kopii zapasowych upewnij się, że wykonano następujące czynności:

* Zezwól na połączenie z maszyną wirtualną z Internetem, aby można było uzyskać dostęp do platformy Azure, zgodnie z opisem w poniższej procedurze [Konfigurowanie łączności sieciowej](#set-up-network-connectivity) .
* Klucz powinien istnieć w **hdbuserstore** , który spełnia następujące kryteria:
  * Powinien być obecny w domyślnym **hdbuserstore**
  * W przypadku MDC klucz powinien wskazywać port SQL **serwer nazw**. W przypadku SDC powinna wskazywać port SQL of **INDEXSERVER**
  * Należy mieć poświadczenia, aby dodawać i usuwać użytkowników
* Uruchom skrypt konfiguracji kopii zapasowej SAP HANA (skrypt przed rejestracją) na maszynie wirtualnej, na której jest zainstalowany program HANA, jako użytkownik główny. [Ten skrypt](https://aka.ms/scriptforpermsonhana) Pobiera System Hana do utworzenia kopii zapasowej. Zapoznaj się z sekcją co to jest [skrypt przed rejestracją](#what-the-pre-registration-script-does) , aby dowiedzieć się więcej na temat skryptu przed rejestracją.

>[!NOTE]
>Azure Backup nie dostosowuje się automatycznie podczas tworzenia kopii zapasowej bazy danych SAP HANA uruchomionej na maszynie wirtualnej platformy Azure.
>
>Zmodyfikuj zasady ręcznie w razie konieczności.

## <a name="set-up-network-connectivity"></a>Konfigurowanie łączności sieciowej

Dla wszystkich operacji maszyna wirtualna SAP HANA wymaga łączności z publicznymi adresami IP platformy Azure. Operacje maszyny wirtualnej (Odnajdywanie bazy danych, konfigurowanie kopii zapasowych, Planowanie kopii zapasowych, przywracanie punktów odzyskiwania itd.) kończą się niepowodzeniem bez łączności z publicznymi adresami IP platformy Azure.

Ustanów łączność przy użyciu jednej z następujących opcji:

### <a name="allow-the-azure-datacenter-ip-ranges"></a>Zezwalaj na zakresy adresów IP centrum danych platformy Azure

Ta opcja zezwala na [zakresy adresów IP](https://www.microsoft.com/download/details.aspx?id=41653) w pobranym pliku. Aby uzyskać dostęp do sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), użyj polecenia cmdlet Set-AzureNetworkSecurityRule. Jeśli lista bezpiecznych adresatów zawiera tylko adresy IP specyficzne dla regionu, należy również zaktualizować Azure Active Directory listę bezpiecznych adresatów, aby włączyć uwierzytelnianie.

### <a name="allow-access-using-nsg-tags"></a>Zezwalaj na dostęp za pomocą tagów sieciowej grupy zabezpieczeń

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

## <a name="what-the-pre-registration-script-does"></a>Co robi skrypt przed rejestracją

Uruchamianie skryptu przed rejestracją wykonuje następujące funkcje:

* Instaluje lub aktualizuje wszelkie niezbędne pakiety wymagane przez agenta Azure Backup w dystrybucji.
* Przeprowadza ona kontrolę łączności sieciowej wychodzącej za pomocą serwerów Azure Backup i usług zależnych, takich jak Azure Active Directory i Azure Storage.
* Loguje się do systemu HANA przy użyciu klucza użytkownika podanego w ramach [wymagań wstępnych](#prerequisites). Klucz użytkownika służy do tworzenia kopii zapasowej użytkownika (AZUREWLBACKUPHANAUSER) w systemie HANA, a klucz użytkownika można usunąć po pomyślnym uruchomieniu skryptu przed rejestracją.
* Do AZUREWLBACKUPHANAUSER są przypisane następujące wymagane role i uprawnienia:
  * Administrator bazy danych (w przypadku MDC) i administrator kopii ZAPASowych (w przypadku SDC): do tworzenia nowych baz danych podczas przywracania.
  * Odczyt wykazu: odczytywanie wykazu kopii zapasowych.
  * SAP_INTERNAL_HANA_SUPPORT: Aby uzyskać dostęp do kilku tabel prywatnych.
* Skrypt dodaje klucz do **hdbuserstore** for AZUREWLBACKUPHANAUSER dla wtyczki kopii zapasowej Hana do obsługi wszystkich operacji (zapytania bazy danych, operacji przywracania, konfigurowania i uruchamiania kopii zapasowej).

>[!NOTE]
> Możesz jawnie przekazać klucz użytkownika wymieniony jako część [wymagań wstępnych](#prerequisites) jako parametr do skryptu przed rejestracją:`-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Aby dowiedzieć się, jakie inne parametry są akceptowane przez skrypt, użyj polecenia`bash msawb-plugin-config-com-sap-hana.sh --help`

Aby potwierdzić utworzenie klucza, uruchom polecenie HDBSQL na maszynie HANA z poświadczeniami SIDADM:

```hdbsql
hdbuserstore list
```

W danych wyjściowych polecenia powinien być wyświetlany klucz {SID} {dbname}, który jest wyświetlany jako AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Upewnij się, że masz unikatowy zestaw plików SSFS `/usr/sap/{SID}/home/.hdb/` . W tej ścieżce powinien znajdować się tylko jeden folder.

## <a name="create-a-recovery-service-vault"></a>Utwórz magazyn usługi Recovery Service

Magazyn Recovery Services jest jednostką, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn Recovery Services zawiera również zasady tworzenia kopii zapasowych, które są skojarzone z chronionymi maszynami wirtualnymi.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

2. Z menu po lewej stronie wybierz pozycję **wszystkie usługi** .

   ![Wybieranie pozycji Wszystkie usługi](./media/tutorial-backup-sap-hana-db/all-services.png)

3. W oknie dialogowym **Wszystkie usługi** wprowadź frazę **Recovery Services**. Lista filtrów zasobów zgodnie z danymi wejściowymi. Na liście zasobów wybierz pozycję **magazyny Recovery Services**.

   ![Wybierz Recovery Services magazyny](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Na pulpicie nawigacyjnym **Recovery Services** magazynów wybierz pozycję **Dodaj**.

   ![Dodaj magazyn Recovery Services](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Zostanie otwarte okno dialogowe **magazyn Recovery Services** . Podaj wartości dla **nazwy, subskrypcji, grupy zasobów** i **lokalizacji**

   ![Tworzenie magazynu usługi Recovery Services](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Nazwa**: nazwa służy do identyfikowania magazynu usług Recovery Services i musi być unikatowa dla subskrypcji platformy Azure. Określ nazwę, która ma co najmniej dwa znaki, ale nie więcej niż 50 znaków. Nazwa musi rozpoczynać się od litery i zawierać tylko litery, cyfry i łączniki. W tym samouczku użyto nazwy **SAPHanaVault**.
   * **Subskrypcja**: wybierz subskrypcję do użycia. Jeśli jesteś członkiem tylko jednej subskrypcji, zobaczysz tę nazwę. Jeśli nie masz pewności, której subskrypcji użyć, Użyj domyślnej (sugerowanej) subskrypcji. Istnieje wiele opcji, które są dostępne tylko wtedy, gdy konto służbowe jest skojarzone z więcej niż jedną subskrypcją platformy Azure. W tym miejscu użyto subskrypcji **subskrypcji SAP HANA Solution Lab** .
   * **Grupa zasobów**: Użyj istniejącej grupy zasobów lub Utwórz nową. W tym miejscu użyto **SAPHANADemo**.<br>
   Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz pozycję **Użyj istniejącej**, a następnie wybierz zasób z listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nową** i wprowadź nazwę. Aby uzyskać pełne informacje na temat grup zasobów, zobacz [Azure Resource Manager przegląd](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   * **Lokalizacja**: Wybierz region geograficzny magazynu. Magazyn musi znajdować się w tym samym regionie co maszyna wirtualna, na której działa SAP HANA. Użyto **Wschodnie stany USA 2**.

5. Wybierz pozycję **Recenzja + Utwórz**.

   ![Wybierz pozycję Przeglądaj & Utwórz](./media/tutorial-backup-sap-hana-db/review-create.png)

Magazyn usługi Recovery Services został utworzony.

## <a name="discover-the-databases"></a>Odnajdywanie baz danych

1. W magazynie w **wprowadzenie**kliknij pozycję **kopia zapasowa**. W **miejscu, w którym jest uruchomione Twoje obciążenie?** wybierz pozycję **SAP HANA na maszynie wirtualnej platformy Azure**.
2. Kliknij przycisk **Rozpocznij odnajdywanie**. Spowoduje to zainicjowanie odnajdywania niechronionych maszyn wirtualnych z systemem Linux w regionie magazynu. Zobaczysz maszynę wirtualną platformy Azure, która ma być chroniona.
3. W obszarze **wybierz Virtual Machines**kliknij link, aby pobrać skrypt, który zapewnia uprawnienia do usługi Azure Backup do uzyskiwania dostępu do maszyn wirtualnych SAP HANA na potrzeby odnajdywania bazy danych.
4. Uruchom skrypt na maszynie wirtualnej, w której znajdują się SAP HANA bazy danych, których kopię zapasową chcesz utworzyć.
5. Po uruchomieniu skryptu na maszynie wirtualnej w obszarze **wybierz Virtual Machines**wybierz maszynę wirtualną. Następnie kliknij pozycję **odkryj baz danych**.
6. Azure Backup odnajduje wszystkie SAP HANA bazy danych na maszynie wirtualnej. Podczas odnajdywania Azure Backup rejestruje maszynę wirtualną w magazynie i instaluje rozszerzenie na maszynie wirtualnej. Żaden Agent nie jest zainstalowany w bazie danych.

   ![Odnajdywanie baz danych](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Po odnalezieniu baz danych, których kopia zapasowa ma zostać utworzona, należy włączyć funkcję tworzenia kopii zapasowych.

1. Kliknij pozycję **Konfiguruj kopię zapasową**.

   ![Konfigurowanie kopii zapasowych](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. W obszarze **Wybierz elementy do utworzenia kopii zapasowej**wybierz co najmniej jedną bazę danych, która ma być chroniona, a następnie kliknij przycisk **OK**.

   ![Wybierz elementy do utworzenia kopii zapasowej](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. W obszarze **zasady tworzenia kopii zapasowych > wybierz pozycję Zasady tworzenia kopii**zapasowych, Utwórz nowe zasady tworzenia kopii zapasowej dla baz danych, zgodnie z instrukcjami w następnej sekcji.

   ![Wybieranie zasad kopii zapasowych](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Po utworzeniu zasad w **menu kopia zapasowa**kliknij pozycję **Włącz kopię zapasową**.

   ![Kliknij pozycję Włącz kopię zapasową](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Śledź postęp konfiguracji kopii zapasowej w obszarze **powiadomienia** portalu.

## <a name="creating-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych definiują, kiedy są tworzone kopie zapasowe, oraz czas ich przechowywania.

* Zasady są tworzone na poziomie magazynu.
* Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.

Określ ustawienia zasad w następujący sposób:

1. W polu **Nazwa zasad** wprowadź nazwę nowych zasad. W takim przypadku wprowadź **SAPHANA**.

   ![Wprowadź nazwę nowych zasad](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. W obszarze **zasady pełnej kopii zapasowej**wybierz **częstotliwość tworzenia kopii zapasowych**. Możesz wybrać opcję **codziennie** lub **co tydzień**. Na potrzeby tego samouczka wybieramy **codzienne** kopie zapasowe.

   ![Wybierz częstotliwość tworzenia kopii zapasowych](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. W obszarze **Zakres przechowywania**Skonfiguruj ustawienia przechowywania dla pełnej kopii zapasowej.
   * Domyślnie są wybierane wszystkie opcje. Wyczyść limity zakresu przechowywania, których nie chcesz używać, i ustaw te, które chcesz wykonać.
   * Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełny/różnicowa/log) wynosi siedem dni.
   * Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
   * Kopia zapasowa dla określonego dnia jest otagowana i zachowywana na podstawie tygodniowego zakresu przechowywania i ustawienia.
   * Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.
4. W menu **pełne zasady tworzenia kopii zapasowej** kliknij przycisk **OK** , aby zaakceptować ustawienia.
5. Następnie wybierz pozycję **różnicowa kopia zapasowa** , aby dodać zasady różnicowe.
6. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania. Włączono różnicową kopię zapasową w każdej **niedzielę** o godzinie **2:00**, która jest zachowywana przez **30 dni**.

   ![Zasady różnicowych kopii zapasowych](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Przyrostowe kopie zapasowe nie są obecnie obsługiwane.
   >

7. Kliknij przycisk **OK** , aby zapisać zasady i wrócić do głównego menu **zasad kopii zapasowych** .
8. Wybierz pozycję **kopia zapasowa dziennika** , aby dodać zasady kopii zapasowej dziennika transakcyjnego,
   * **Kopia zapasowa dziennika** jest domyślnie ustawiona na wartość **enable**. Nie można go wyłączyć, ponieważ SAP HANA zarządza wszystkimi kopiami zapasowymi dzienników.
   * Ustawiono **2 godziny** jako harmonogram tworzenia kopii zapasowych i **15 dni** okresu przechowywania.

    ![Zasady tworzenia kopii zapasowych dziennika](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Kopie zapasowe dzienników zaczynają przepływać dopiero po ukończeniu jednej pełnej kopii zapasowej.
   >

9. Kliknij przycisk **OK** , aby zapisać zasady i wrócić do głównego menu **zasad kopii zapasowych** .
10. Po zdefiniowaniu zasad tworzenia kopii zapasowej kliknij przycisk **OK**.

Pomyślnie skonfigurowano kopie zapasowe baz danych SAP HANA.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [uruchamiać kopie zapasowe na żądanie na SAP HANA bazach danych uruchomionych na maszynach wirtualnych platformy Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](sap-hana-db-restore.md)
* Dowiedz się [, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu Azure Backup](sap-hana-db-manage.md)
* Dowiedz się, jak [rozwiązywać typowe problemy podczas tworzenia kopii zapasowych baz danych SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)
