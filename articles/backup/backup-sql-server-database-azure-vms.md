---
title: Tworzenie kopii zapasowej wielu maszyn wirtualnych programu SQL Server z magazynu
description: W tym artykule dowiesz się, jak utworzyć kopię zapasową SQL Server baz danych w usłudze Azure Virtual Machines z Azure Backup z magazynu Recovery Services
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 798dc81012ad968c3ecc287717240513a08a1349
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "105567218"
---
# <a name="back-up-multiple-sql-server-vms-from-the-recovery-services-vault"></a>Tworzenie kopii zapasowych wielu maszyn wirtualnych SQL Server z magazynu Recovery Services

Bazy danych SQL Server są obciążeniami krytycznymi, które wymagają niewielkiego celu punktu odzyskiwania (RPO) i długoterminowego przechowywania. Można utworzyć kopię zapasową SQL Server baz danych uruchomionych na maszynach wirtualnych platformy Azure, korzystając z [Azure Backup](backup-overview.md).

W tym artykule pokazano, jak utworzyć kopię zapasową bazy danych SQL Server działającej na maszynie wirtualnej platformy Azure z magazynem Azure Backup Recovery Services.

Ten artykuł obejmuje następujące zagadnienia:

> [!div class="checklist"]
>
> * Tworzenie i konfigurowanie magazynu.
> * Odkryj bazy danych i skonfiguruj kopie zapasowe.
> * Konfigurowanie ochrony automatycznej dla baz danych.

>[!NOTE]
>**Nietrwałe usuwanie programu SQL Server na maszynie wirtualnej platformy Azure oraz usuwanie nietrwałe dla SAP HANA w obciążeniach maszyn wirtualnych platformy Azure** jest teraz dostępne w wersji zapoznawczej.<br>
>Aby utworzyć konto w wersji zapoznawczej, Zapisz się do nas na AskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kopii zapasowej bazy danych SQL Server należy sprawdzić następujące kryteria:

1. Zidentyfikuj lub Utwórz [magazyn Recovery Services](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) w tym samym regionie i w ramach subskrypcji co maszyna wirtualna hostującym wystąpienie SQL Server.
1. Sprawdź, czy maszyna wirtualna ma [łączność sieciową](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
1. Upewnij się, że bazy danych SQL Server są zgodne z [wytycznymi nazewnictwa bazy danych dla Azure Backup](#database-naming-guidelines-for-azure-backup).
1. Upewnij się, że łączna długość nazwy maszyny wirtualnej SQL Server i nazwy grupy zasobów nie przekracza 84 znaków dla Azure Resource Manager maszyn wirtualnych (lub 77 znaków dla klasycznych maszyn wirtualnych). To ograniczenie wynika z faktu, że niektóre znaki są zarezerwowane przez usługę.
1. Sprawdź, czy nie masz włączonych rozwiązań do tworzenia kopii zapasowych dla bazy danych. Przed utworzeniem kopii zapasowej bazy danych Wyłącz wszystkie inne SQL Server kopie zapasowe.
1. W przypadku korzystania z SQL Server 2008 R2 lub SQL Server 2012 może wystąpić problem z strefą czasową dla kopii zapasowej, zgodnie z opisem w [tym miejscu](https://support.microsoft.com/help/2697983/kb2697983-fix-an-incorrect-value-is-stored-in-the-time-zone-column-of). Upewnij się, że korzystasz z najnowszych aktualizacji zbiorczych, aby uniknąć opisanego powyżej problemu dotyczącego strefy czasowej. Jeśli zastosowanie aktualizacji do wystąpienia SQL Server na maszynie wirtualnej platformy Azure nie jest możliwe, wyłącz czas letni dla strefy czasowej na maszynie wirtualnej.

> [!NOTE]
> Możesz włączyć Azure Backup dla maszyny wirtualnej platformy Azure, a także dla SQL Serverj bazy danych działającej na maszynie wirtualnej bez konfliktu.

### <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

Dla wszystkich operacji maszyna wirtualna SQL Server wymaga łączności z usługą Azure Backup, magazynem platformy Azure i Azure Active Directory. Można to osiągnąć za pomocą prywatnych punktów końcowych lub zezwalając na dostęp do wymaganych publicznych adresów IP lub nazw FQDN. Nieumożliwienie właściwej łączności z wymaganymi usługami platformy Azure może prowadzić do niepowodzenia operacji takich jak odnajdywanie bazy danych, konfigurowanie kopii zapasowej, wykonywanie kopii zapasowych i przywracanie danych.

W poniższej tabeli wymieniono różne alternatywy, których można użyć do ustanowienia łączności:

| **Opcja**                        | **Zalety**                                               | **Wady**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Prywatne punkty końcowe                 | Zezwalaj na wykonywanie kopii zapasowych za pośrednictwem prywatnych adresów IP w sieci wirtualnej  <br><br>   Zapewnianie szczegółowej kontroli po stronie sieci i magazynu | Odnosi się do standardowych [kosztów](https://azure.microsoft.com/pricing/details/private-link/) prywatnych punktów końcowych |
| Tagi usługi sieciowej grupy zabezpieczeń                  | Łatwiejsze zarządzanie, ponieważ zmiany zakresu są automatycznie scalane   <br><br>   Brak dodatkowych kosztów | Może być używany tylko z sieciowych grup zabezpieczeń  <br><br>    Zapewnia dostęp do całej usługi |
| Tagi FQDN zapory platformy Azure          | Łatwiejsze zarządzanie, ponieważ wymagane są automatycznie zarządzane nazwy FQDN | Może być używany tylko z zaporą platformy Azure                         |
| Zezwalaj na dostęp do nazw FQDN usługi/adresów IP | Brak dodatkowych kosztów   <br><br>  Współpracuje ze wszystkimi urządzeniami i zaporami zabezpieczeń sieci | Dostęp do szerokiego zestawu adresów IP lub nazw FQDN może być wymagany   |
| Używanie serwera proxy HTTP                 | Dostęp do maszyn wirtualnych w jednym punkcie dostępu do Internetu                       | Dodatkowe koszty związane z uruchamianiem maszyny wirtualnej za pomocą oprogramowania serwera proxy         |

Więcej informacji na temat korzystania z tych opcji są następujące:

#### <a name="private-endpoints"></a>Prywatne punkty końcowe

Prywatne punkty końcowe umożliwiają bezpieczne nawiązywanie połączenia z serwerów znajdujących się w sieci wirtualnej z magazynem Recovery Services. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla Twojego magazynu. Ruch sieciowy między zasobami w sieci wirtualnej a magazynem jest przesyłany przez sieć wirtualną i prywatny link w sieci szkieletowej firmy Microsoft. Eliminuje to narażenie z publicznego Internetu. Przeczytaj więcej na temat prywatnych punktów końcowych Azure Backup [tym miejscu](./private-endpoints.md).

#### <a name="nsg-tags"></a>Tagi sieciowej grupy zabezpieczeń

Jeśli używasz sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń), Użyj znacznika usługi *AzureBackup* , aby zezwolić na dostęp wychodzący do Azure Backup. Oprócz znacznika Azure Backup należy również zezwolić na połączenie z uwierzytelnianiem i transferem danych, tworząc podobne [reguły sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md#service-tags) dla usługi Azure AD (*usługi azureactivedirectory*) i usługi Azure Storage (*Magazyn*).  Poniższe kroki opisują proces tworzenia reguły dla tagu Azure Backup:

1. W obszarze **wszystkie usługi** przejdź do pozycji **sieciowe grupy zabezpieczeń** i wybierz grupę zabezpieczeń sieci.

1. W obszarze **Ustawienia** wybierz pozycję **reguły zabezpieczeń dla ruchu wychodzącego** .

1. Wybierz pozycję **Dodaj**. Wprowadź wszystkie wymagane szczegóły dotyczące tworzenia nowej reguły zgodnie z opisem w [ustawieniach reguły zabezpieczeń](../virtual-network/manage-network-security-group.md#security-rule-settings). Upewnij się, że opcja **miejsce docelowe** jest ustawiona na *tag usługi* i **znacznik usługi docelowej** jest ustawiony na *AzureBackup*.

1. Wybierz pozycję **Dodaj**  , aby zapisać nowo utworzoną regułę zabezpieczeń dla ruchu wychodzącego.

W podobny sposób można tworzyć reguły zabezpieczeń wychodzące sieciowej grupy zabezpieczeń dla usługi Azure Storage i usługi Azure AD.

#### <a name="azure-firewall-tags"></a>Tagi zapory platformy Azure

Jeśli używasz zapory platformy Azure, Utwórz regułę aplikacji przy użyciu  [znacznika FQDN zapory AzureBackup platformy Azure](../firewall/fqdn-tags.md). Umożliwia to wychodzący dostęp do Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Zezwalaj na dostęp do zakresów adresów IP usługi

Jeśli zdecydujesz się zezwolić na dostęp do adresów IP usługi, zapoznaj się z zakresem w pliku JSON dostępnym w [tym miejscu](https://www.microsoft.com/download/confirmation.aspx?id=56519). Musisz zezwolić na dostęp do adresów IP odpowiadających Azure Backup, usłudze Azure Storage i Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Zezwalaj na dostęp do nazw FQDN usługi

Można również użyć następujących nazw FQDN, aby zezwolić na dostęp do wymaganych usług z serwerów:

| Usługa    | Nazwy domen do uzyskania dostępu                             | Porty
| -------------- | ------------------------------------------------------------ | ---
| Azure Backup  | `*.backup.windowsazure.com`                             | 443
| Usługa Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` | 443
| Usługa Azure AD      | Zezwalaj na dostęp do nazw FQDN w sekcjach 56 i 59 zgodnie z [tym artykułem](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) | Zgodnie z wymaganiami

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Kierowanie ruchu przy użyciu serwera proxy HTTP

Podczas tworzenia kopii zapasowej bazy danych SQL Server na maszynie wirtualnej platformy Azure rozszerzenie kopii zapasowej na maszynie wirtualnej używa interfejsów API HTTPS do wysyłania poleceń zarządzania do Azure Backup i danych do usługi Azure Storage. Rozszerzenie kopii zapasowej używa także usługi Azure AD do uwierzytelniania. Ruch rozszerzenia kopii zapasowej dla tych trzech usług należy kierować za pośrednictwem serwera proxy HTTP. Użyj listy adresów IP i nazw FQDN wymienionych powyżej, aby umożliwić dostęp do wymaganych usług. Uwierzytelnione serwery proxy nie są obsługiwane.

### <a name="database-naming-guidelines-for-azure-backup"></a>Wskazówki dotyczące nazewnictwa baz danych dla Azure Backup

Należy unikać używania następujących elementów w nazwach baz danych:

* Spacje końcowe i początkowe
* Końcowe znaki wykrzyknika (!)
* Zamykające nawiasy kwadratowe (])
* Średnik ";"
* Ukośnik "/"

Aliasowanie jest dostępne dla nieobsługiwanych znaków, ale zalecamy ich uniknięcie. Aby uzyskać więcej informacji, zobacz [Understanding the Table Service Data Model (Omówienie modelu danych usługi Table Service)](/rest/api/storageservices/understanding-the-table-service-data-model).

>[!NOTE]
>Operacja **konfigurowania ochrony** baz danych zawierających znaki specjalne, takie jak "+" lub "&", nie jest obsługiwana. Można zmienić nazwę bazy danych lub włączyć funkcję **autoochrony**, która może pomyślnie chronić te bazy danych.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Odnajdowanie baz danych programu SQL Server

Jak odnajdywać bazy danych uruchomione na maszynie wirtualnej:

1. W [witrynie Azure Portal](https://portal.azure.com) otwórz magazyn usługi Recovery Services używany do tworzenia kopii zapasowych bazy danych.

2. Na pulpicie nawigacyjnym **magazynu Recovery Services** wybierz pozycję **kopia zapasowa**.

   ![Wybieranie pozycji Utwórz kopię zapasową w celu otwarcia menu Cel kopii zapasowej](./media/backup-azure-sql-database/open-backup-menu.png)

3. W obszarze **cel kopii zapasowej** ustaw, **gdzie działa Twoje obciążenie?** na **platformę Azure**.

4. W obszarze **Co ma zawierać kopia zapasowa** wybierz pozycję **SQL Server na maszynie wirtualnej platformy Azure**.

    ![Wybieranie programu SQL Server na maszynie wirtualnej platformy Azure na potrzeby tworzenia kopii zapasowej](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. W obszarze **Cel kopii zapasowej** > **Odnajdywanie baz danych na maszynach wirtualnych** wybierz pozycję **Uruchom odnajdywanie**, aby wyszukać niechronione maszyny wirtualne w subskrypcji. To wyszukiwanie może chwilę potrwać, w zależności od liczby niechronionych maszyn wirtualnych w subskrypcji.

   * Niechronione maszyny wirtualne powinny zostać wyświetlone na liście po odnalezieniu, uporządkowane według nazwy i grupy zasobów.
   * Jeśli maszyna wirtualna nie jest wyświetlana w oczekiwany sposób, sprawdź, czy kopia zapasowa została już utworzona w magazynie.
   * Wiele maszyn wirtualnych może mieć taką samą nazwę, ale będą należeć do różnych grup zasobów.

     ![Kopia zapasowa jest w stanie oczekiwania podczas wyszukiwania baz danych na maszynach wirtualnych](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na liście maszyn wirtualnych wybierz maszynę wirtualną z uruchomioną bazą danych programu SQL Server i wybierz pozycję **Odnajdź bazy danych**.

7. Śledź odnajdywanie bazy danych w **powiadomieniach**. Czas wymagany dla tej akcji zależy od liczby baz danych maszyn wirtualnych. Po odnalezieniu wybranych baz danych zostanie wyświetlony komunikat o powodzeniu.

    ![Komunikat o powodzeniu wdrożenia](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Usługa Azure Backup odnajduje wszystkie bazy danych programu SQL Server na maszynie wirtualnej. Podczas odnajdywania następujące elementy są wykonywane w tle:

    * Azure Backup rejestruje maszynę wirtualną z magazynem na potrzeby tworzenia kopii zapasowych obciążeń. Kopie zapasowe wszystkich baz danych na zarejestrowanej maszynie wirtualnej można tworzyć tylko w tym magazynie.
    * Usługa Azure Backup instaluje rozszerzenie AzureBackupWindowsWorkload na maszynie wirtualnej. Żaden Agent nie jest zainstalowany w bazie danych SQL.
    * Usługa Azure Backup tworzy konto usługi NT Service\AzureWLBackupPluginSvc na maszynie wirtualnej.
      * Wszystkie operacje tworzenia kopii zapasowych i przywracania korzystają z konta usługi.
      * NT Service\AzureWLBackupPluginSvc wymaga uprawnień administratora systemu SQL. Wszystkie SQL Server maszyny wirtualne utworzone w portalu Marketplace pochodzą z zainstalowanym SqlIaaSExtension. Rozszerzenie AzureBackupWindowsWorkload używa rozszerzenia SQLIaaSExtension w celu automatycznego uzyskania wymaganych uprawnień.
    * Jeśli maszyna wirtualna nie została utworzona z poziomu portalu Marketplace lub jeśli korzystasz z programu SQL 2008 i 2008 R2, maszyna wirtualna może nie mieć zainstalowanych SqlIaaSExtension i operacja odnajdywania zakończy się niepowodzeniem z komunikatem o błędzie UserErrorSQLNoSysAdminMembership. Aby rozwiązać ten problem, postępuj zgodnie z instrukcjami w obszarze [Ustaw uprawnienia maszyny wirtualnej](backup-azure-sql-database.md#set-vm-permissions).

        ![Wybieranie maszyny wirtualnej i bazy danych](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych  

1. W obszarze **cel kopii zapasowej**  >  **krok 2: Konfigurowanie kopii zapasowej**, wybierz pozycję **Konfiguruj kopię zapasową**.

   ![Wybieranie pozycji Konfiguruj kopię zapasową](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

1. Wybierz pozycję **Dodaj zasoby** , aby wyświetlić wszystkie zarejestrowane grupy dostępności i autonomiczne wystąpienia SQL Server.

    ![Wybierz pozycję Dodaj zasoby](./media/backup-azure-sql-database/add-resources.png)

1. Na ekranie **Wybierz elementy do utworzenia kopii zapasowej** wybierz strzałkę z lewej strony wiersza, aby rozwinąć listę wszystkich niechronionych baz danych w tym wystąpieniu lub zawsze włączona Grupa dostępności.

    ![Wybierz elementy do wykonania kopii zapasowej](./media/backup-azure-sql-database/select-items-to-backup.png)

1. Wybierz wszystkie bazy danych, które chcesz chronić, a następnie wybierz przycisk **OK**.

   ![Chronienie bazy danych](./media/backup-azure-sql-database/select-database-to-protect.png)

   Aby zoptymalizować obciążenia kopii zapasowych, usługa Azure Backup ustawia maksymalną liczbę baz danych w jednym zadaniu tworzenia kopii zapasowej na 50.

     * Aby chronić więcej niż 50 baz danych, skonfiguruj wiele kopii zapasowych.
     * Aby [włączyć](#enable-auto-protection) całe wystąpienie lub zawsze włączona Grupa dostępności, na liście rozwijanej **Autoochrona** wybierz pozycję  **włączone**, a następnie wybierz przycisk **OK**.

         > [!NOTE]
         > Funkcja [automatycznej ochrony](#enable-auto-protection) nie tylko włącza ochronę wszystkich istniejących baz danych, ale również automatycznie chroni wszystkie nowe bazy danych dodane do tego wystąpienia lub grupy dostępności.  

1. Zdefiniuj **zasady tworzenia kopii zapasowych**. Możesz wykonać jedną z następujących czynności:

   * Wybierz zasady domyślne jako *HourlyLogBackup*.
   * Wybrać istniejące zasady kopii zapasowych utworzone wcześniej dla środowiska SQL.
   * Zdefiniować nowe zasady oparte na własnym celu punktu odzyskiwania i zakresie przechowywania.

     ![Wybieranie pozycji Zasady kopii zapasowych](./media/backup-azure-sql-database/select-backup-policy.png)

1. Wybierz pozycję **Włącz kopię zapasową** , aby przesłać operację **konfigurowania ochrony** i śledzić postęp konfiguracji w obszarze **powiadomienia** portalu.

   ![Śledź postęp konfiguracji](./media/backup-azure-sql-database/track-configuration-progress.png)

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady kopii zapasowych określają, kiedy są tworzone kopie zapasowe i jak długo są przechowywane.

* Zasady są tworzone na poziomie magazynu.
* Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.
* Po utworzeniu zasad kopii zapasowych ustawieniem domyślnym będzie pełna kopia zapasowa tworzona codziennie.
* Możesz dodać różnicową kopię zapasową, ale tylko jeśli skonfigurujesz tworzenie pełnych kopii zapasowych co tydzień.
* Dowiedz się więcej o [różnych typach zasad tworzenia kopii zapasowych](backup-architecture.md#sql-server-backup-types).

Aby utworzyć nowe zasady kopii zapasowych:

1. W magazynie wybierz pozycję **Zasady kopii zapasowych** > **Dodaj**.
1. W obszarze **Dodaj** wybierz pozycję **SQL Server na maszynie wirtualnej platformy Azure** , aby zdefiniować typ zasad.

   ![Wybieranie typu nowych zasad kopii zapasowych](./media/backup-azure-sql-database/policy-type-details.png)

1. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.

    ![Wprowadź nazwę zasad](./media/backup-azure-sql-database/policy-name.png)

1. Wybierz odpowiednie **łącze, aby utworzyć** **pełną kopię zapasową**, aby zmodyfikować ustawienia domyślne.

   * Wybierz **częstotliwość tworzenia kopii zapasowych**. Wybierz opcję **codziennie** lub **co tydzień**.
   * W przypadku opcji **Codziennie** wybierz godzinę i strefę czasową rozpoczęcia zadania tworzenia kopii zapasowej. Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.

     ![Pola nowych zasad kopii zapasowych](./media/backup-azure-sql-database/full-backup-policy.png)  

1. W obszarze **Zakres przechowywania** wszystkie opcje są domyślnie zaznaczone. Wyczyść limity zakresu przechowywania, które nie są potrzebne, a następnie ustaw interwały, które mają być używane.

    * Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełny, różnicowa i log) wynosi siedem dni.
    * Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    * Kopia zapasowa dla określonego dnia jest otagowana i zachowywana na podstawie tygodniowego zakresu przechowywania oraz ustawienia przechowywania tygodniowego.
    * Zakresy przechowywania miesięczne i roczne działają w podobny sposób.

       ![Ustawienia interwału zakresu przechowywania](./media/backup-azure-sql-database/retention-range-interval.png)

1. Wybierz **przycisk OK** , aby zaakceptować ustawienie dla pełnych kopii zapasowych.
1. Wybierz łącze **Edytuj** odpowiadające **różnicowej kopii zapasowej**, aby zmodyfikować ustawienia domyślne.

    * W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania.
    * Można wyzwolić tylko jedną różnicową kopię zapasową dziennie. Nie można wyzwolić różnicowej kopii zapasowej w tym samym dniu co pełna kopia zapasowa.
    * Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni.
    * Różnicowa kopia zapasowa nie jest obsługiwana dla bazy danych Master.

      ![Zasady różnicowych kopii zapasowych](./media/backup-azure-sql-database/differential-backup-policy.png)

1. Wybierz łącze **Edytuj** odpowiadające **kopii zapasowej dziennika**, aby zmodyfikować ustawienia domyślne

    * W obszarze **Kopia zapasowa dziennika** wybierz pozycję **Włącz**, a następnie ustaw kontrolki częstotliwości i przechowywania.
    * Kopie zapasowe dzienników mogą odbywać się przez co 15 minut i mogą być przechowywane przez maksymalnie 35 dni.
    * Jeśli baza danych znajduje się w [modelu odzyskiwania prostego](/sql/relational-databases/backup-restore/recovery-models-sql-server), harmonogram tworzenia kopii zapasowych dziennika dla tej bazy danych zostanie wstrzymany i w związku z tym żadne kopie zapasowe dziennika nie będą wyzwalane.
    * Jeśli model odzyskiwania bazy danych zmieni się z **pełny** na **prosty**, kopie zapasowe dzienników zostaną wstrzymane w ciągu 24 godzin od zmiany w modelu odzyskiwania. Podobnie, jeśli model odzyskiwania zmienia się z **prostego**, oznacza to, że w bazie danych można teraz obsługiwać kopie zapasowe dzienników, harmonogramy kopii zapasowych dzienników zostaną włączone w ciągu 24 godzin od zmiany modelu odzyskiwania.

      ![Zasady tworzenia kopii zapasowych dziennika](./media/backup-azure-sql-database/log-backup-policy.png)

1. W menu **zasady tworzenia kopii zapasowych** wybierz, czy włączyć **kompresję kopii zapasowej SQL** , czy nie. Ta opcja jest domyślnie wyłączona. Jeśli ta funkcja jest włączona, SQL Server wyśle skompresowany strumień kopii zapasowych do infrastruktury VDI. Azure Backup przesłania wartości domyślne poziomu wystąpienia z klauzulą COMPRESSION/NO_COMPRESSION w zależności od wartości tego formantu.

1. Po zakończeniu edycji zasad kopii zapasowych wybierz pozycję **OK**.

> [!NOTE]
> Każda kopia zapasowa dziennika jest łańcuchem do poprzedniej pełnej kopii zapasowej w celu utworzenia łańcucha odzyskiwania. Ta pełna kopia zapasowa zostanie zachowana do momentu wygaśnięcia ostatniej kopii zapasowej dziennika. Może to oznaczać, że pełna kopia zapasowa jest przechowywana przez dodatkowy okres, aby upewnić się, że wszystkie dzienniki mogą zostać odzyskane. Załóżmy, że masz tygodniową pełną kopię zapasową, różnice dzienne i 2-godzinowe dzienniki. Wszystkie z nich są przechowywane przez 30 dni. Jednak tydzień pełny może być naprawdę oczyszczony/usunięty tylko po udostępnieniu kolejnej pełnej kopii zapasowej, czyli po upływie 30 dni. Na przykład cotygodniowe pełne kopie zapasowe odbywają się na LIS 16 listopada. Zgodnie z zasadami przechowywania należy je zachować do 16 grudnia. Ostatnia kopia zapasowa dziennika dla tego stanu jest wykonywana przed następnym zaplanowanym zaplanowaną godziną. Do momentu udostępnienia tego dziennika do grudnia 22 nie można usunąć. W związku z tym do gru.

## <a name="enable-auto-protection"></a>Włączanie ochrony automatycznej  

Można włączyć automatyczną ochronę, aby automatycznie tworzyć kopie zapasowe wszystkich istniejących i przyszłych baz danych w autonomicznym wystąpieniu SQL Server lub do zawsze włączonej grupy dostępności.

* Nie ma żadnego limitu liczby baz danych, które można wybrać do ochrony w danym momencie. Odnajdywanie jest zazwyczaj uruchamiane co osiem godzin. Można jednak odnajdywać i chronić nowe bazy danych natychmiast po ręcznym uruchomieniu odnajdywania, wybierając opcję ponownie **odkryj baz danych** .
* Nie można wybiórczo chronić ani wykluczać baz danych z ochrony w wystąpieniu w momencie włączenia ochrony autoprotection.
* Jeśli wystąpienie zawiera już pewne chronione bazy danych, pozostaną one chronione w ramach odpowiednich zasad nawet po włączeniu ochrony autoprotection. Wszystkie niechronione bazy danych dodane później będą mieć tylko pojedyncze zasady zdefiniowane w momencie włączania ochrony automatycznie, wymienione w obszarze **Konfiguruj kopię zapasową**. Można jednak później zmienić zasady skojarzone z chronioną bazą danych.  

Aby włączyć ochronę autoprotection:

  1. W obszarze **Wybieranie elementów do wykonania kopii zapasowej** wybierz wystąpienie, dla którego chcesz włączyć ochronę automatyczną.
  2. Wybierz listę rozwijaną w obszarze **Autoochrona**, wybierz pozycję **włączone**, a następnie wybierz przycisk **OK**.

      ![Włącz funkcję autoochrony w grupie dostępności](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Tworzenie kopii zapasowych jest konfigurowane wspólnie dla wszystkich baz danych i można je śledzić w obszarze **Zadania kopii zapasowej**.

Jeśli musisz wyłączyć funkcję autoochrony, wybierz nazwę wystąpienia w obszarze **Konfiguruj kopię zapasową**, a następnie wybierz opcję **Wyłącz autoochronę** dla tego wystąpienia. Kopie zapasowe wszystkich baz danych zostaną zachowane, ale przyszłe bazy danych nie będą chronione automatycznie.

![Wyłącz autoochronę dla tego wystąpienia](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Następne kroki

Instrukcje:

* [Przywracanie kopii zapasowych SQL Server baz danych](restore-sql-database-azure-vm.md)
* [Zarządzanie kopiami zapasowymi baz danych SQL Server](manage-monitor-sql-database-backup.md)
