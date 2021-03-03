---
title: Samouczek — Tworzenie kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure
description: W tym samouczku dowiesz się, jak utworzyć kopię zapasową SAP HANA baz danych działających na maszynie wirtualnej platformy Azure do magazynu Azure Backup Recovery Services.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 5548717b25ea3ec027ba5f588e5e28faafbb5d6f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703685"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Samouczek: Tworzenie kopii zapasowych baz danych SAP HANA na maszynie wirtualnej platformy Azure

W tym samouczku pokazano, jak utworzyć kopię zapasową SAP HANA baz danych działających na maszynach wirtualnych platformy Azure do magazynu Azure Backup Recovery Services. W tym artykule dowiesz się, jak:

> [!div class="checklist"]
>
> * Tworzenie i Konfigurowanie magazynu
> * Odnajdywanie baz danych
> * Skonfiguruj kopie zapasowe

[Oto](sap-hana-backup-support-matrix.md#scenario-support) wszystkie scenariusze, które obecnie są obsługiwane.

>[!NOTE]
>Od 1 sierpnia 2020 SAP HANA kopia zapasowa RHEL (7,4, 7,6, 7,7 & 8,1) jest ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem kopii zapasowych upewnij się, że wykonano następujące czynności:

* Zidentyfikuj lub Utwórz [magazyn Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) w tym samym regionie i w ramach subskrypcji co maszyna wirtualna, na której działa SAP HANA.
* Zezwól na połączenie z maszyną wirtualną z Internetem, aby można było uzyskać dostęp do platformy Azure, zgodnie z opisem w poniższej procedurze [Konfigurowanie łączności sieciowej](#set-up-network-connectivity) .
* Upewnij się, że łączna długość nazwy maszyny wirtualnej serwera SAP HANA i nazwy grupy zasobów nie przekracza 84 znaków dla Azure Resource Manager (maszyny wirtualne ARM_ (i 77 znaków dla klasycznych maszyn wirtualnych). To ograniczenie wynika z faktu, że niektóre znaki są zarezerwowane przez usługę.
* Klucz powinien istnieć w **hdbuserstore** , który spełnia następujące kryteria:
  * Powinien być obecny w domyślnym **hdbuserstore**. Wartość domyślna to `<sid>adm` konto, pod którym zainstalowano SAP HANA.
  * W przypadku MDC klucz powinien wskazywać port SQL **serwer nazw**. W przypadku SDC powinna wskazywać port SQL of **INDEXSERVER**
  * Należy mieć poświadczenia, aby dodawać i usuwać użytkowników
  * Należy pamiętać, że ten klucz można usunąć po pomyślnym uruchomieniu skryptu przed rejestracją
* Uruchom skrypt konfiguracji kopii zapasowej SAP HANA (skrypt przed rejestracją) na maszynie wirtualnej, na której jest zainstalowany program HANA, jako użytkownik główny. [Ten skrypt](https://aka.ms/scriptforpermsonhana) Pobiera System Hana do utworzenia kopii zapasowej. Zapoznaj się z sekcją co to jest [skrypt przed rejestracją](#what-the-pre-registration-script-does) , aby dowiedzieć się więcej na temat skryptu przed rejestracją.
* Jeśli Instalator platformy HANA używa prywatnych punktów końcowych, uruchom [skrypt poprzedzający rejestrację](https://aka.ms/scriptforpermsonhana) za pomocą parametru *-SN* lub *--Skip-Network-checks* .

>[!NOTE]
>Skrypt przed rejestracją instaluje **unixODBC234** SAP HANA dla obciążeń, które działają w RHEL (7,4, 7,6 i 7,7) i **unixODBC** dla RHEL 8,1. [Ten pakiet znajduje się w repozytorium RHEL for SAP HANA (w przypadku serwera RHEL 7) usługi Update Services for SAP Solutions (pakiety RPM)](https://access.redhat.com/solutions/5094721).  W przypadku obrazu RHEL w portalu Azure Marketplace repozytorium będzie **rhui-RHEL-SAP-HANA-for-RHEL-7-Server-rhui-e4s-pakiety RPM**.

## <a name="set-up-network-connectivity"></a>Konfigurowanie łączności sieciowej

W przypadku wszystkich operacji baza danych SAP HANA uruchomiona na maszynie wirtualnej platformy Azure wymaga łączności z usługą Azure Backup, magazynem platformy Azure i Azure Active Directory. Można to osiągnąć za pomocą prywatnych punktów końcowych lub zezwalając na dostęp do wymaganych publicznych adresów IP lub nazw FQDN. Nieumożliwienie właściwej łączności z wymaganymi usługami platformy Azure może prowadzić do niepowodzenia operacji takich jak odnajdywanie bazy danych, konfigurowanie kopii zapasowej, wykonywanie kopii zapasowych i przywracanie danych.

W poniższej tabeli wymieniono różne alternatywy, których można użyć do ustanowienia łączności:

| **Opcja**                        | **Zalety**                                               | **Wady**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Prywatne punkty końcowe                 | Zezwalaj na wykonywanie kopii zapasowych za pośrednictwem prywatnych adresów IP w sieci wirtualnej  <br><br>   Zapewnianie szczegółowej kontroli po stronie sieci i magazynu | Odnosi się do standardowych [kosztów](https://azure.microsoft.com/pricing/details/private-link/) prywatnych punktów końcowych |
| Tagi usługi sieciowej grupy zabezpieczeń                  | Łatwiejsze zarządzanie, ponieważ zmiany zakresu są automatycznie scalane   <br><br>   Brak dodatkowych kosztów | Może być używany tylko z sieciowych grup zabezpieczeń  <br><br>    Zapewnia dostęp do całej usługi |
| Tagi FQDN zapory platformy Azure          | Łatwiejsze zarządzanie, ponieważ wymagane są automatycznie zarządzane nazwy FQDN | Może być używany tylko z zaporą platformy Azure                         |
| Zezwalaj na dostęp do nazw FQDN usługi/adresów IP | Brak dodatkowych kosztów   <br><br>  Współpracuje ze wszystkimi urządzeniami i zaporami zabezpieczeń sieci | Dostęp do szerokiego zestawu adresów IP lub nazw FQDN może być wymagany   |
| Używanie serwera proxy HTTP                 | Dostęp do maszyn wirtualnych w jednym punkcie dostępu do Internetu                       | Dodatkowe koszty związane z uruchamianiem maszyny wirtualnej za pomocą oprogramowania serwera proxy         |

Więcej informacji na temat korzystania z tych opcji są następujące:

### <a name="private-endpoints"></a>Prywatne punkty końcowe

Prywatne punkty końcowe umożliwiają bezpieczne nawiązywanie połączenia z serwerów znajdujących się w sieci wirtualnej z magazynem Recovery Services. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla Twojego magazynu. Ruch sieciowy między zasobami w sieci wirtualnej a magazynem jest przesyłany przez sieć wirtualną i prywatny link w sieci szkieletowej firmy Microsoft. Eliminuje to narażenie z publicznego Internetu. Przeczytaj więcej na temat prywatnych punktów końcowych Azure Backup [tym miejscu](./private-endpoints.md).

### <a name="nsg-tags"></a>Tagi sieciowej grupy zabezpieczeń

Jeśli używasz sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń), Użyj znacznika usługi *AzureBackup* , aby zezwolić na dostęp wychodzący do Azure Backup. Oprócz znacznika Azure Backup należy również zezwolić na połączenie z uwierzytelnianiem i transferem danych, tworząc podobne [reguły sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md#service-tags) dla usługi Azure AD (*usługi azureactivedirectory*) i usługi Azure Storage (*Magazyn*). Poniższe kroki opisują proces tworzenia reguły dla tagu Azure Backup:

1. W obszarze **wszystkie usługi** przejdź do pozycji **sieciowe grupy zabezpieczeń** i wybierz grupę zabezpieczeń sieci.

1. W obszarze **Ustawienia** wybierz pozycję **reguły zabezpieczeń dla ruchu wychodzącego** .

1. Wybierz pozycję **Dodaj**. Wprowadź wszystkie wymagane szczegóły dotyczące tworzenia nowej reguły zgodnie z opisem w [ustawieniach reguły zabezpieczeń](../virtual-network/manage-network-security-group.md#security-rule-settings). Upewnij się, że opcja **miejsce docelowe** jest ustawiona na *tag usługi* i **znacznik usługi docelowej** jest ustawiony na *AzureBackup*.

1. Wybierz pozycję **Dodaj**  , aby zapisać nowo utworzoną regułę zabezpieczeń dla ruchu wychodzącego.

W podobny sposób można tworzyć [reguły zabezpieczeń wychodzące sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md#service-tags) dla usługi Azure Storage i usługi Azure AD. Aby uzyskać więcej informacji na temat tagów usługi, zobacz [ten artykuł](../virtual-network/service-tags-overview.md).

### <a name="azure-firewall-tags"></a>Tagi zapory platformy Azure

Jeśli używasz zapory platformy Azure, Utwórz regułę aplikacji przy użyciu  [znacznika FQDN zapory AzureBackup platformy Azure](../firewall/fqdn-tags.md). Umożliwia to wychodzący dostęp do Azure Backup.

### <a name="allow-access-to-service-ip-ranges"></a>Zezwalaj na dostęp do zakresów adresów IP usługi

Jeśli zdecydujesz się zezwolić na dostęp do adresów IP usługi, zapoznaj się z zakresem w pliku JSON dostępnym w [tym miejscu](https://www.microsoft.com/download/confirmation.aspx?id=56519). Musisz zezwolić na dostęp do adresów IP odpowiadających Azure Backup, usłudze Azure Storage i Azure Active Directory.

### <a name="allow-access-to-service-fqdns"></a>Zezwalaj na dostęp do nazw FQDN usługi

Można również użyć następujących nazw FQDN, aby zezwolić na dostęp do wymaganych usług z serwerów:

| Usługa    | Nazwy domen do uzyskania dostępu                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Usługa Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Usługa Azure AD      | Zezwalaj na dostęp do nazw FQDN w sekcjach 56 i 59 zgodnie z [tym artykułem](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>Kierowanie ruchu przy użyciu serwera proxy HTTP

Podczas tworzenia kopii zapasowej bazy danych SAP HANA działającej na maszynie wirtualnej platformy Azure, rozszerzenie kopii zapasowej na maszynie wirtualnej używa interfejsów API HTTPS do wysyłania poleceń zarządzania do Azure Backup i danych do usługi Azure Storage. Rozszerzenie kopii zapasowej używa także usługi Azure AD do uwierzytelniania. Ruch rozszerzenia kopii zapasowej dla tych trzech usług należy kierować za pośrednictwem serwera proxy HTTP. Użyj listy adresów IP i nazw FQDN wymienionych powyżej, aby umożliwić dostęp do wymaganych usług. Uwierzytelnione serwery proxy nie są obsługiwane.

## <a name="understanding-backup-and-restore-throughput-performance"></a>Informacje o wydajności operacji tworzenia kopii zapasowej i przywracania

Kopie zapasowe (Dziennik i niedziennik) w SAP HANA maszynach wirtualnych platformy Azure dostarczanych za pośrednictwem BACKINT są strumieniami do magazynów usługi Azure Recovery Services i dlatego ważne jest zrozumienie tej metodologii przesyłania strumieniowego.

Składnik BACKINT platformy HANA udostępnia "potoki" (potok do odczytu i potok do zapisu), połączony z dyskami źródłowymi, w których znajdują się pliki bazy danych, które są następnie odczytywane przez usługę Azure Backup i transportowane do magazynu usługi Azure Recovery Services. Usługa Azure Backup wykonuje również sumę kontrolną, aby sprawdzić poprawność strumieni, oprócz natywnych testów weryfikacyjnych BACKINT. Te walidacji będą mieć pewność, że dane znajdujące się w magazynie usługi Azure Recovery Services są rzeczywiście niezawodne i odzyskiwalne.

Ponieważ strumienie polegają głównie na dyskach, należy zrozumieć wydajność dysku, aby zmierzyć wydajność tworzenia kopii zapasowych i przywracania. Zapoznaj się z [tym artykułem](../virtual-machines/disks-performance.md) , aby uzyskać szczegółowe informacje na temat przepływności i wydajności dysków na maszynach wirtualnych platformy Azure. Dotyczą one również wydajności tworzenia kopii zapasowych i przywracania.

**Usługa Azure Backup próbuje uzyskać maksymalnie 420 MB/s dla kopii zapasowych bez dziennika (takich jak pełna, różnicowa i przyrostowa) i do 100 MB/s dla kopii zapasowych dzienników dla platformy Hana**. Jak wspomniano powyżej, nie są one gwarantowane szybkości i są zależne od następujących czynników:

* Maksymalna przepływność dysku w pamięci podręcznej dla maszyny wirtualnej
* Typ dysku podstawowego i jego przepływność
* Liczba procesów, które próbują odczytać i zapisać na tym samym dysku w tym samym czasie.

> [!IMPORTANT]
> W mniejszych maszynach wirtualnych, w których przepływność dysku w pamięci podręcznej jest bardzo bliska lub mniejsza niż 400 MB/s, może być konieczne, aby całe operacje we/wy dysku były zużywane przez usługę kopii zapasowych, co może mieć wpływ na działania SAP HANA związane z odczytem/zapisem dysków. W takim przypadku, jeśli chcemy ograniczyć użycie usługi kopii zapasowej do maksymalnego limitu, można odwołać się do następnej sekcji.

### <a name="limiting-backup-throughput-performance"></a>Ograniczanie wydajności tworzenia kopii zapasowych

Jeśli chcesz ograniczyć użycie liczby operacji we/wy dysku usługi kopii zapasowej do wartości maksymalnej, wykonaj następujące czynności.

1. Przejdź do folderu "opt/msawb/bin"
2. Utwórz nowy plik JSON o nazwie "ExtensionSettingOverrides.JSON"
3. Dodaj parę klucz-wartość do pliku JSON w następujący sposób:

    ```json
    {
    "MaxUsableVMThroughputInMBPS": 200
    }
    ```

4. Zmień uprawnienia i własność pliku w następujący sposób:
    
    ```bash
    chmod 750 ExtensionSettingsOverrides.json
    chown root:msawb ExtensionSettingsOverrides.json
    ```

5. Nie jest wymagane ponowne uruchomienie żadnej usługi. Usługa Azure Backup podejmie próbę ograniczenia wydajności przepływności opisanej w tym pliku.

## <a name="what-the-pre-registration-script-does"></a>Co robi skrypt przed rejestracją

Uruchamianie skryptu przed rejestracją wykonuje następujące funkcje:

* W oparciu o dystrybucję systemu Linux skrypt instaluje lub aktualizuje wszelkie niezbędne pakiety wymagane przez agenta Azure Backup.
* Przeprowadza ona kontrolę łączności sieciowej wychodzącej za pomocą serwerów Azure Backup i usług zależnych, takich jak Azure Active Directory i Azure Storage.
* Loguje się do systemu HANA przy użyciu klucza użytkownika podanego w ramach [wymagań wstępnych](#prerequisites). Klucz użytkownika służy do tworzenia kopii zapasowej użytkownika (AZUREWLBACKUPHANAUSER) w systemie HANA, a **klucz użytkownika można usunąć po pomyślnym uruchomieniu skryptu przed rejestracją**.
* Do AZUREWLBACKUPHANAUSER są przypisane następujące wymagane role i uprawnienia:
  * Dla MDC: administrator bazy danych i administrator kopii ZAPASowych (z platformy HANA 2,0 SPS05 i nowsze): Aby utworzyć nowe bazy danych podczas przywracania.
  * Dla SDC: administrator kopii ZAPASowej: Aby utworzyć nowe bazy danych podczas przywracania.
  * Odczyt wykazu: odczytywanie wykazu kopii zapasowych.
  * SAP_INTERNAL_HANA_SUPPORT: Aby uzyskać dostęp do kilku tabel prywatnych. Wymagany tylko w przypadku wersji SDC i MDC, które są opisane poniżej HANA 2,0 SPS04 Rev 46. Nie jest to wymagane w przypadku platformy HANA 2,0 SPS04 Rev 46 lub nowszej, ponieważ firma Microsoft Pobiera wymagane informacje z tabel publicznych teraz dzięki rozwiązaniu od zespołu HANA.
* Skrypt dodaje klucz do **hdbuserstore** for AZUREWLBACKUPHANAUSER dla wtyczki kopii zapasowej Hana do obsługi wszystkich operacji (zapytania bazy danych, operacji przywracania, konfigurowania i uruchamiania kopii zapasowej).

>[!NOTE]
> Możesz jawnie przekazać klucz użytkownika wymieniony jako część [wymagań wstępnych](#prerequisites) jako parametr do skryptu przed rejestracją: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Aby dowiedzieć się, jakie inne parametry są akceptowane przez skrypt, użyj polecenia `bash msawb-plugin-config-com-sap-hana.sh --help`

Aby potwierdzić utworzenie klucza, uruchom polecenie HDBSQL na maszynie HANA z poświadczeniami SIDADM:

```hdbsql
hdbuserstore list
```

W danych wyjściowych polecenia powinien być wyświetlany klucz {SID} {dbname}, który jest wyświetlany jako AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Upewnij się, że masz unikatowy zestaw plików SSFS `/usr/sap/{SID}/home/.hdb/` . W tej ścieżce powinien znajdować się tylko jeden folder.

## <a name="create-a-recovery-services-vault"></a>Tworzenie magazynu usługi Recovery Services

Magazyn Recovery Services jest jednostką, która przechowuje kopie zapasowe i punkty odzyskiwania utworzone w czasie. Magazyn Recovery Services zawiera również zasady tworzenia kopii zapasowych, które są skojarzone z chronionymi maszynami wirtualnymi.

Aby utworzyć magazyn Usług odzyskiwania:

1. Zaloguj się do subskrypcji w witrynie [Azure Portal](https://portal.azure.com/).

2. Z menu po lewej stronie wybierz pozycję **wszystkie usługi** .

   ![Wybieranie pozycji Wszystkie usługi](./media/tutorial-backup-sap-hana-db/all-services.png)

3. W oknie dialogowym **Wszystkie usługi** wprowadź frazę **Recovery Services**. Lista zasobów jest filtrowana zgodnie z danymi wejściowymi. Na liście zasobów wybierz pozycję **Magazyny usługi Recovery Services**.

   ![Wybierz Recovery Services magazyny](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Na pulpicie nawigacyjnym **Recovery Services** magazynów wybierz pozycję **Dodaj**.

   ![Dodaj magazyn Recovery Services](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Zostanie otwarte okno dialogowe **Magazyn usługi Recovery Services**. Podaj wartości dla **nazwy, subskrypcji, grupy zasobów** i **lokalizacji**

   ![Tworzenie magazynu usługi Recovery Services](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Nazwa**: nazwa jest używana do identyfikowania magazynu Recovery Services i musi być unikatowa dla subskrypcji platformy Azure. Określ nazwę, która ma co najmniej dwa znaki, ale nie więcej niż 50 znaków. Nazwa musi rozpoczynać się od litery i składać się tylko z liter, cyfr i łączników. W tym samouczku użyto nazwy **SAPHanaVault**.
   * **Subskrypcja**: Wybierz subskrypcję, która ma zostać użyta. Jeśli jesteś członkiem tylko jednej subskrypcji, zostanie wyświetlona jej nazwa. Jeśli nie masz pewności, której subskrypcji użyć, wybierz domyślną (sugerowaną). Większa liczba opcji do wyboru jest dostępna tylko w przypadku, gdy konto służbowe jest skojarzone z więcej niż jedną subskrypcją platformy Azure. W tym miejscu użyto subskrypcji **subskrypcji SAP HANA Solution Lab** .
   * **Grupa zasobów**: Użyj istniejącej grupy zasobów lub utwórz nową. W tym miejscu użyto **SAPHANADemo**.<br>
   Aby wyświetlić listę dostępnych grup zasobów w ramach subskrypcji, wybierz pozycję **Użyj istniejącej**, a następnie wybierz zasób z listy rozwijanej. Aby utworzyć nową grupę zasobów, wybierz pozycję **Utwórz nową** i wprowadź nazwę. Aby uzyskać pełne informacje na temat grup zasobów, zobacz [Azure Resource Manager przegląd](../azure-resource-manager/management/overview.md).
   * **Lokalizacja**: Wybierz region geograficzny dla magazynu. Magazyn musi znajdować się w tym samym regionie co maszyna wirtualna, na której działa SAP HANA. Użyto **Wschodnie stany USA 2**.

5. Wybierz pozycję **Przejrzyj i utwórz**.

   ![Wybierz pozycję Przeglądaj & Utwórz](./media/tutorial-backup-sap-hana-db/review-create.png)

Magazyn Recovery Services jest teraz utworzony.

## <a name="discover-the-databases"></a>Odnajdywanie baz danych

1. W magazynie w obszarze **wprowadzenie** wybierz pozycję **kopia zapasowa**. W **miejscu, w którym jest uruchomione Twoje obciążenie?** wybierz pozycję **SAP HANA na maszynie wirtualnej platformy Azure**.
2. Wybierz pozycję **Rozpocznij odnajdywanie**. Spowoduje to zainicjowanie odnajdywania niechronionych maszyn wirtualnych z systemem Linux w regionie magazynu. Zobaczysz maszynę wirtualną platformy Azure, która ma być chroniona.
3. W obszarze **wybierz Virtual Machines** wybierz link, aby pobrać skrypt, który zapewnia uprawnienia do usługi Azure Backup do uzyskiwania dostępu do maszyn wirtualnych SAP HANA na potrzeby odnajdywania bazy danych.
4. Uruchom skrypt na maszynie wirtualnej, w której znajdują się SAP HANA bazy danych, których kopię zapasową chcesz utworzyć.
5. Po uruchomieniu skryptu na maszynie wirtualnej w obszarze **wybierz Virtual Machines** wybierz maszynę wirtualną. Następnie wybierz pozycję **odkryj baz danych**.
6. Azure Backup odnajduje wszystkie SAP HANA bazy danych na maszynie wirtualnej. Podczas odnajdywania Azure Backup rejestruje maszynę wirtualną w magazynie i instaluje rozszerzenie na maszynie wirtualnej. Żaden Agent nie jest zainstalowany w bazie danych.

   ![Odnajdywanie baz danych](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Po odnalezieniu baz danych, których kopia zapasowa ma zostać utworzona, należy włączyć funkcję tworzenia kopii zapasowych.

1. Wybierz pozycję **Konfiguruj kopię zapasową**.

   ![Konfigurowanie kopii zapasowych](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. W obszarze **Wybierz elementy do utworzenia kopii zapasowej** wybierz co najmniej jedną bazę danych, która ma być chroniona, a następnie wybierz przycisk **OK**.

   ![Wybierz elementy do utworzenia kopii zapasowej](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. W obszarze **zasady tworzenia kopii zapasowych > wybierz pozycję Zasady tworzenia kopii** zapasowych, Utwórz nowe zasady tworzenia kopii zapasowej dla baz danych, zgodnie z instrukcjami w następnej sekcji.

   ![Wybieranie zasad kopii zapasowych](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Po utworzeniu zasad w **menu kopia zapasowa** wybierz pozycję **Włącz kopię zapasową**.

   ![Wybierz pozycję Włącz kopię zapasową](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Śledź postęp konfiguracji kopii zapasowej w obszarze **powiadomienia** portalu.

## <a name="creating-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych definiują, kiedy są tworzone kopie zapasowe, oraz czas ich przechowywania.

* Zasady są tworzone na poziomie magazynu.
* Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.

Określ ustawienia zasad w następujący sposób:

1. W polu **Nazwa zasad** wprowadź nazwę nowych zasad. W takim przypadku wprowadź **SAPHANA**.

   ![Wprowadź nazwę nowych zasad](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. W obszarze **zasady pełnej kopii zapasowej** wybierz **częstotliwość tworzenia kopii zapasowych**. Możesz wybrać opcję **codziennie** lub **co tydzień**. Na potrzeby tego samouczka wybieramy **codzienne** kopie zapasowe.

   ![Wybierz częstotliwość tworzenia kopii zapasowych](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. W obszarze **Zakres przechowywania** Skonfiguruj ustawienia przechowywania dla pełnej kopii zapasowej.
   * Domyślnie są wybierane wszystkie opcje. Wyczyść limity zakresu przechowywania, których nie chcesz używać, i ustaw te, które chcesz wykonać.
   * Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełny/różnicowa/log) wynosi siedem dni.
   * Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
   * Kopia zapasowa dla określonego dnia jest otagowana i zachowywana na podstawie tygodniowego zakresu przechowywania i ustawienia.
   * Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.
4. W menu **zasad pełnej kopii zapasowej** wybierz pozycję **OK**, aby zaakceptować ustawienia.
5. Następnie wybierz pozycję **różnicowa kopia zapasowa** , aby dodać zasady różnicowe.
6. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania. Włączono różnicową kopię zapasową w każdej **niedzielę** o godzinie **2:00**, która jest zachowywana przez **30 dni**.

   ![Zasady różnicowych kopii zapasowych](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Można wybrać różnicową lub przyrostową kopię zapasową, ale nie oba jednocześnie.

7. W obszarze **zasady przyrostowej kopii zapasowej** wybierz pozycję **Włącz** , aby otworzyć kontrolki częstotliwość i przechowywanie.
    * Co więcej, można wyzwolić jedną przyrostową kopię zapasową dziennie.
    * Przyrostowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższego okresu przechowywania, musisz użyć pełnych kopii zapasowych.

    ![Zasady przyrostowych kopii zapasowych](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

8. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.
9. Wybierz pozycję **kopia zapasowa dziennika** , aby dodać zasady kopii zapasowej dziennika transakcyjnego,
   * **Kopia zapasowa dziennika** jest domyślnie ustawiona na wartość **enable**. Nie można go wyłączyć, ponieważ SAP HANA zarządza wszystkimi kopiami zapasowymi dzienników.
   * Ustawimy **2 godziny** jako Harmonogram kopii zapasowych i **15 dni** okresu przechowywania.

    ![Zasady tworzenia kopii zapasowych dziennika](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Kopie zapasowe dzienników zaczynają przepływać dopiero po ukończeniu jednej pełnej kopii zapasowej.
   >

10. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.
11. Po zakończeniu definiowania zasad tworzenia kopii zapasowych wybierz pozycję **OK**.

Pomyślnie skonfigurowano kopie zapasowe baz danych SAP HANA.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [uruchamiać kopie zapasowe na żądanie na SAP HANA bazach danych uruchomionych na maszynach wirtualnych platformy Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](sap-hana-db-restore.md)
* Dowiedz się [, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu Azure Backup](sap-hana-db-manage.md)
* Dowiedz się, jak [rozwiązywać typowe problemy podczas tworzenia kopii zapasowych baz danych SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)