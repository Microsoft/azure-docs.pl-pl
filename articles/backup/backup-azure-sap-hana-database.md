---
title: Tworzenie kopii zapasowej bazy danych SAP HANA na platformie Azure przy użyciu Azure Backup
description: W tym artykule dowiesz się, jak utworzyć kopię zapasową bazy danych SAP HANA na maszynach wirtualnych platformy Azure przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e7735c4240529cc6fc9bb6470934dd335d22aa77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719614"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Tworzenie kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure

Bazy danych SAP HANA są obciążeniami krytycznymi, które wymagają niewielkiego celu punktu odzyskiwania (RPO) i długoterminowego przechowywania. Można utworzyć kopię zapasową SAP HANA baz danych uruchomionych na maszynach wirtualnych platformy Azure, korzystając z [Azure Backup](backup-overview.md).

W tym artykule pokazano, jak utworzyć kopię zapasową SAP HANA baz danych, które są uruchomione na maszynach wirtualnych platformy Azure, do magazynu Recovery Services Azure Backup.

Ten artykuł obejmuje następujące zagadnienia:
> [!div class="checklist"]
>
> * Tworzenie i Konfigurowanie magazynu
> * Odnajdywanie baz danych
> * Skonfiguruj kopie zapasowe
> * Uruchamianie zadania tworzenia kopii zapasowej na żądanie

>[!NOTE]
>Od 1 sierpnia 2020 SAP HANA kopia zapasowa RHEL (7,4, 7,6, 7,7 & 8,1) jest ogólnie dostępna.

>[!NOTE]
>**Nietrwałe usuwanie programu SQL Server na maszynie wirtualnej platformy Azure oraz usuwanie nietrwałe dla SAP HANA w obciążeniach maszyn wirtualnych platformy Azure** jest teraz dostępne w wersji zapoznawczej.<br>
>Aby utworzyć konto w wersji zapoznawczej, Zapisz się do nas pod adresem [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się z [wymaganiami wstępnymi](tutorial-backup-sap-hana-db.md#prerequisites) i informacjami o [skrypcie przed rejestracją](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) , aby skonfigurować bazę danych do tworzenia kopii zapasowych.

### <a name="establish-network-connectivity"></a>Ustawianie łączności sieciowej

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

#### <a name="private-endpoints"></a>Prywatne punkty końcowe

Prywatne punkty końcowe umożliwiają bezpieczne nawiązywanie połączenia z serwerów znajdujących się w sieci wirtualnej z magazynem Recovery Services. Prywatny punkt końcowy używa adresu IP z przestrzeni adresowej sieci wirtualnej dla Twojego magazynu. Ruch sieciowy między zasobami w sieci wirtualnej a magazynem jest przesyłany przez sieć wirtualną i prywatny link w sieci szkieletowej firmy Microsoft. Eliminuje to narażenie z publicznego Internetu. Przeczytaj więcej na temat prywatnych punktów końcowych Azure Backup [tym miejscu](./private-endpoints.md).

#### <a name="nsg-tags"></a>Tagi sieciowej grupy zabezpieczeń

Jeśli używasz sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń), Użyj znacznika usługi *AzureBackup* , aby zezwolić na dostęp wychodzący do Azure Backup. Oprócz znacznika Azure Backup należy również zezwolić na połączenie z uwierzytelnianiem i transferem danych, tworząc podobne [reguły sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md#service-tags) dla usługi Azure AD (*usługi azureactivedirectory*) i usługi Azure Storage (*Magazyn*).  Poniższe kroki opisują proces tworzenia reguły dla tagu Azure Backup:

1. W obszarze **wszystkie usługi** przejdź do pozycji **sieciowe grupy zabezpieczeń** i wybierz grupę zabezpieczeń sieci.

1. W obszarze **Ustawienia** wybierz pozycję **reguły zabezpieczeń dla ruchu wychodzącego** .

1. Wybierz pozycję **Dodaj**. Wprowadź wszystkie wymagane szczegóły dotyczące tworzenia nowej reguły zgodnie z opisem w [ustawieniach reguły zabezpieczeń](../virtual-network/manage-network-security-group.md#security-rule-settings). Upewnij się, że opcja **miejsce docelowe** jest ustawiona na *tag usługi* i **znacznik usługi docelowej** jest ustawiony na *AzureBackup*.

1. Wybierz pozycję **Dodaj**  , aby zapisać nowo utworzoną regułę zabezpieczeń dla ruchu wychodzącego.

W podobny sposób można tworzyć reguły zabezpieczeń wychodzące sieciowej grupy zabezpieczeń dla usługi Azure Storage i usługi Azure AD. Aby uzyskać więcej informacji na temat tagów usługi, zobacz [ten artykuł](../virtual-network/service-tags-overview.md).

#### <a name="azure-firewall-tags"></a>Tagi zapory platformy Azure

Jeśli używasz zapory platformy Azure, Utwórz regułę aplikacji przy użyciu  [znacznika FQDN zapory AzureBackup platformy Azure](../firewall/fqdn-tags.md). Umożliwia to wychodzący dostęp do Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Zezwalaj na dostęp do zakresów adresów IP usługi

Jeśli zdecydujesz się zezwolić na dostęp do adresów IP usługi, zapoznaj się z zakresem w pliku JSON dostępnym w [tym miejscu](https://www.microsoft.com/download/confirmation.aspx?id=56519). Musisz zezwolić na dostęp do adresów IP odpowiadających Azure Backup, usłudze Azure Storage i Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Zezwalaj na dostęp do nazw FQDN usługi

Można również użyć następujących nazw FQDN, aby zezwolić na dostęp do wymaganych usług z serwerów:

| Usługa    | Nazwy domen do uzyskania dostępu                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Usługa Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Usługa Azure AD      | Zezwalaj na dostęp do nazw FQDN w sekcjach 56 i 59 zgodnie z [tym artykułem](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Kierowanie ruchu przy użyciu serwera proxy HTTP

Podczas tworzenia kopii zapasowej bazy danych SAP HANA działającej na maszynie wirtualnej platformy Azure, rozszerzenie kopii zapasowej na maszynie wirtualnej używa interfejsów API HTTPS do wysyłania poleceń zarządzania do Azure Backup i danych do usługi Azure Storage. Rozszerzenie kopii zapasowej używa także usługi Azure AD do uwierzytelniania. Ruch rozszerzenia kopii zapasowej dla tych trzech usług należy kierować za pośrednictwem serwera proxy HTTP. Użyj listy adresów IP i nazw FQDN wymienionych powyżej, aby umożliwić dostęp do wymaganych usług. Uwierzytelnione serwery proxy nie są obsługiwane.

> [!NOTE]
> Brak obsługi serwera proxy poziomu usług. Oznacza to, że ruch przez serwer proxy z tylko kilku lub wybranych usług (usługi Azure Backup) nie jest obsługiwany. Wszystkie dane lub ruch mogą być kierowane przez serwer proxy.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Odnajdywanie baz danych

1. W magazynie w obszarze **wprowadzenie** wybierz pozycję **kopia zapasowa**. W **miejscu, w którym jest uruchomione Twoje obciążenie?** wybierz pozycję **SAP HANA na maszynie wirtualnej platformy Azure**.
2. Wybierz pozycję **Rozpocznij odnajdywanie**. Spowoduje to zainicjowanie odnajdywania niechronionych maszyn wirtualnych z systemem Linux w regionie magazynu.

   * Po odnajdywaniu w portalu są wyświetlane niechronione maszyny wirtualne wymienione według nazwy i grupy zasobów.
   * Jeśli maszyna wirtualna nie znajduje się w oczekiwany sposób, sprawdź, czy kopia zapasowa została już utworzona w magazynie.
   * Wiele maszyn wirtualnych może mieć taką samą nazwę, ale należą do różnych grup zasobów.

3. W obszarze **wybierz Virtual Machines** wybierz link, aby pobrać skrypt, który zapewnia uprawnienia do usługi Azure Backup do uzyskiwania dostępu do maszyn wirtualnych SAP HANA na potrzeby odnajdywania bazy danych.
4. Uruchom skrypt na każdej maszynie wirtualnej, w której znajdują się SAP HANA bazy danych, których kopię zapasową chcesz utworzyć.
5. Po uruchomieniu skryptu na maszynach wirtualnych w obszarze **wybierz Virtual Machines** Wybierz Maszyny wirtualne. Następnie wybierz pozycję **odkryj baz danych**.
6. Azure Backup odnajduje wszystkie SAP HANA bazy danych na maszynie wirtualnej. Podczas odnajdywania Azure Backup rejestruje maszynę wirtualną w magazynie i instaluje rozszerzenie na maszynie wirtualnej. Żaden Agent nie jest zainstalowany w bazie danych.

    ![Odnajdź SAP HANA bazy danych](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych  

Teraz Włącz tworzenie kopii zapasowej.

1. W kroku 2 Wybierz pozycję **Konfiguruj kopię zapasową**.

    ![Skonfiguruj kopię zapasową](./media/backup-azure-sap-hana-database/configure-backup.png)
2. W obszarze **Wybierz elementy do utworzenia kopii zapasowej** wybierz wszystkie bazy danych, które mają być chronione > **OK**.

    ![Wybierz elementy do utworzenia kopii zapasowej](./media/backup-azure-sap-hana-database/select-items.png)
3. W obszarze **zasady tworzenia kopii** zapasowych  >  **Wybierz pozycję Zasady tworzenia kopii** zapasowych, Utwórz nowe zasady tworzenia kopii zapasowych baz danych, zgodnie z poniższymi instrukcjami.

    ![Wybieranie zasad kopii zapasowych](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Po utworzeniu zasad w menu **kopia zapasowa** wybierz pozycję **Włącz kopię zapasową**.

    ![Włącz tworzenie kopii zapasowej](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Śledź postęp konfiguracji kopii zapasowej w obszarze **powiadomienia** portalu.

### <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych definiują, kiedy są tworzone kopie zapasowe, oraz czas ich przechowywania.

* Zasady są tworzone na poziomie magazynu.
* Wiele magazynów może korzystać z tych samych zasad kopii zapasowych, ale do każdego magazynu należy zastosować zasady kopii zapasowych.

>[!NOTE]
>Azure Backup nie dostosowuje się automatycznie podczas tworzenia kopii zapasowej bazy danych SAP HANA uruchomionej na maszynie wirtualnej platformy Azure.
>
>Zmodyfikuj zasady ręcznie w razie konieczności.

Określ ustawienia zasad w następujący sposób:

1. W polu **Nazwa zasad** wprowadź nazwę nowych zasad.

   ![Wprowadź nazwę zasad](./media/backup-azure-sap-hana-database/policy-name.png)
1. W obszarze **Zasady pełnej kopii zapasowej** wybierz opcję **Częstotliwość tworzenia kopii zapasowej** (**Codziennie** lub **Co tydzień**).
   * **Codziennie**: Wybierz godzinę i strefę czasową, w której rozpoczyna się zadanie tworzenia kopii zapasowej.
       * Należy uruchomić pełną kopię zapasową. Nie można wyłączyć tej opcji.
       * Wybierz pozycję **Pełna kopia zapasowa**, aby wyświetlić zasady.
       * Nie można tworzyć różnicowych kopii zapasowych w przypadku codziennego tworzenia pełnych kopii zapasowych.
   * **Co tydzień**: Wybierz dzień tygodnia, godzinę i strefę czasową, w której jest uruchamiane zadanie tworzenia kopii zapasowej.

   ![Wybierz częstotliwość tworzenia kopii zapasowych](./media/backup-azure-sap-hana-database/backup-frequency.png)

1. W obszarze **Zakres przechowywania** Skonfiguruj ustawienia przechowywania dla pełnej kopii zapasowej.
    * Domyślnie wszystkie opcje są zaznaczone. Wyczyść limity zakresu przechowywania, których nie chcesz używać, i ustaw te, które chcesz wykonać.
    * Minimalny okres przechowywania dla dowolnego typu kopii zapasowej (pełny/różnicowa/log) wynosi siedem dni.
    * Punkty odzyskiwania są oznaczone do przechowywania na podstawie ich zakresu przechowywania. Jeśli na przykład wybierzesz codzienne tworzenie pełnej kopii zapasowej, każdego dnia będzie wyzwalana tylko jedna pełna kopia zapasowa.
    * Kopia zapasowa dla określonego dnia jest otagowana i zachowywana na podstawie tygodniowego zakresu przechowywania i ustawienia.
    * Miesięczne i roczne zakresy przechowywania zachowują się w podobny sposób.

1. W menu **zasad pełnej kopii zapasowej** wybierz pozycję **OK**, aby zaakceptować ustawienia.
1. Wybierz pozycję **różnicowa kopia zapasowa** , aby dodać zasady różnicowe.
1. W obszarze **Zasady różnicowej kopii zapasowej** wybierz pozycję **Włącz**, aby otworzyć kontrolki częstotliwości i przechowywania.
    * Można wyzwalać maksymalnie jedną różnicową kopię zapasową dziennie.
    * Różnicowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższego okresu przechowywania, musisz użyć pełnych kopii zapasowych.

    ![Zasady różnicowych kopii zapasowych](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Można wybrać różnicową lub przyrostową kopię zapasową, ale nie oba jednocześnie.
1. W obszarze **zasady przyrostowej kopii zapasowej** wybierz pozycję **Włącz** , aby otworzyć kontrolki częstotliwość i przechowywanie.
    * Co więcej, można wyzwolić jedną przyrostową kopię zapasową dziennie.
    * Przyrostowe kopie zapasowe mogą być przechowywane przez maksymalnie 180 dni. Jeśli potrzebujesz dłuższego okresu przechowywania, musisz użyć pełnych kopii zapasowych.

    ![Zasady przyrostowych kopii zapasowych](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

1. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.
1. Wybierz pozycję **kopia zapasowa dziennika** , aby dodać zasady kopii zapasowej dziennika transakcyjnego,
    * W obszarze **kopia zapasowa dziennika** wybierz pozycję **Włącz**.  Nie można go wyłączyć, ponieważ SAP HANA zarządza wszystkimi kopiami zapasowymi dzienników.
    * Ustaw kontrolkę częstotliwość i przechowywanie.

    > [!NOTE]
    > Kopie zapasowe dzienników zaczynają przepływać dopiero po pomyślnym wykonaniu pełnej kopii zapasowej.

1. Wybierz pozycję **OK**, aby zapisać zasady i wróć do głównego menu **zasad kopii zapasowych**.
1. Po zakończeniu definiowania zasad tworzenia kopii zapasowych wybierz pozycję **OK**.

> [!NOTE]
> Każda kopia zapasowa dziennika jest łańcuchem do poprzedniej pełnej kopii zapasowej w celu utworzenia łańcucha odzyskiwania. Ta pełna kopia zapasowa zostanie zachowana do momentu wygaśnięcia ostatniej kopii zapasowej dziennika. Może to oznaczać, że pełna kopia zapasowa jest przechowywana przez dodatkowy okres, aby upewnić się, że wszystkie dzienniki mogą zostać odzyskane. Załóżmy, że użytkownik dysponuje tygodniową pełną kopią zapasową, różnicą dzienną i 2-godzinnymi dziennikami. Wszystkie z nich są przechowywane przez 30 dni. Jednak tydzień pełny może być naprawdę oczyszczony/usunięty tylko po udostępnieniu kolejnej pełnej kopii zapasowej, czyli po upływie 30 dni. Na przykład cotygodniowe pełne kopie zapasowe odbywają się na LIS 16 listopada. Zgodnie z zasadami przechowywania należy je zachować do 16 grudnia. Ostatnia kopia zapasowa dziennika dla tego stanu jest wykonywana przed następnym zaplanowanym zaplanowaną godziną. Do momentu udostępnienia tego dziennika do grudnia 22 nie można usunąć. W związku z tym do gru.

## <a name="run-an-on-demand-backup"></a>Uruchamianie tworzenia kopii zapasowej na żądanie

Kopie zapasowe są uruchamiane zgodnie z harmonogramem zasad. Kopię zapasową można uruchomić na żądanie w następujący sposób:

1. W menu magazyn wybierz pozycję **elementy kopii zapasowej**.
2. W obszarze **elementy kopii zapasowej** wybierz maszynę wirtualną z uruchomioną SAP HANA bazą danych, a następnie wybierz pozycję **Utwórz kopię zapasową teraz**.
3. W obszarze **kopia zapasowa** wybierz typ kopii zapasowej, którą chcesz wykonać. Następnie wybierz przycisk **OK**. Ta kopia zapasowa zostanie zachowana zgodnie z zasadami skojarzonymi z tym elementem kopii zapasowej.
4. Monitoruj powiadomienia portalu. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej**  >  **w toku**. W zależności od rozmiaru bazy danych Tworzenie początkowej kopii zapasowej może chwilę potrwać.

Domyślnie przechowywanie kopii zapasowych na żądanie to 45 dni.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Uruchamianie kopii zapasowej SAP HANA Studio w bazie danych z włączoną funkcją Azure Backup

Jeśli chcesz utworzyć lokalną kopię zapasową bazy danych, której kopia zapasowa jest tworzona przy użyciu Azure Backup, wykonaj następujące czynności:

1. Poczekaj na zakończenie wszystkich pełnych lub dzienników kopii zapasowych bazy danych. Sprawdź stan w SAP HANA Studio/Panel sterowania.
1. Wyłącz kopie zapasowe dzienników i ustaw wykaz kopii zapasowych w systemie plików dla odpowiedniej bazy danych.
1. Aby to zrobić, kliknij dwukrotnie pozycję   >  **Konfiguracja** systemdb  >  **Wybierz pozycję Filtr bazy danych**  >  **(log)**.
1. Ustaw **enable_auto_log_backup** na wartość **nie**.
1. Ustaw **log_backup_using_backint** na **wartość false**.
1. Ustaw **catalog_backup_using_backint** na **wartość false**.
1. Wykonaj pełną kopię zapasową bazy danych.
1. Poczekaj na zakończenie pełnej kopii zapasowej i kopii zapasowej wykazu.
1. Przywróć poprzednie ustawienia z powrotem do tych dla platformy Azure:
    * Ustaw wartość **enable_auto_log_backup** na **tak**.
    * Ustaw **log_backup_using_backint** na **wartość true**.
    * Ustaw **catalog_backup_using_backint** na **wartość true**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić bazy danych SAP HANA uruchomione na maszynach wirtualnych platformy Azure](./sap-hana-db-restore.md)
* Dowiedz się [, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu Azure Backup](./sap-hana-db-manage.md)
