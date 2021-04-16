---
title: Tworzenie kopii zapasowej usługi Azure Database for PostgreSQL
description: Dowiedz się Azure Database for PostgreSQL kopii zapasowej z długoterminowym przechowywaniem (wersja zapoznawcza)
ms.topic: conceptual
ms.date: 04/12/2021
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 8fd69e016c7f0b175ef49b98add5692743858f62
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480073"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL kopii zapasowej z długoterminowym przechowywaniem (wersja zapoznawcza)

Azure Backup z usługami Azure Database Services zostały połączone w celu tworzenia rozwiązania do tworzenia kopii zapasowych klasy korporacyjnej dla serwerów Azure Database for PostgreSQL, które przechowuje kopie zapasowe przez maksymalnie 10 lat.

Oprócz przechowywania długoterminowego rozwiązanie ma również wiele innych możliwości wymienionych poniżej:

- Kontrola dostępu oparta na rolach (RBAC) platformy Azure do bazy danych przy użyciu Azure Active Directory i uwierzytelniania tożsamości usługi zarządzanej (MSI).
- Kontrolowane przez klienta zaplanowane i na żądanie kopie zapasowe na poziomie poszczególnych baz danych.
- Przywracanie na poziomie bazy danych na dowolnym serwerze Postgres lub bezpośrednio do magazynu obiektów blob.
- Długoterminowe przechowywanie.
- Centralne monitorowanie wszystkich operacji i zadań.
- Kopie zapasowe są przechowywane w oddzielnych domenach zabezpieczeń i błędów. Nawet jeśli bezpieczeństwo serwera źródłowego zostałoby naruszone, a nawet zostałoby złamane, kopie zapasowe pozostaną bezpieczne w magazynie [usługi Backup.](backup-vault-overview.md)
- Korzystanie z **pg_dump** zapewnia większą elastyczność przywracania, tak aby można było je przywrócić w różnych wersjach bazy danych, a nawet przywrócić tylko część kopii zapasowej.

Tego rozwiązania można używać niezależnie lub oprócz natywnego rozwiązania do tworzenia kopii zapasowych oferowanego przez usługę Azure PostgreSQL, które oferuje przechowywanie do 35 dni. Rozwiązanie natywne jest odpowiednie do odzyskiwania danych operacyjnych, takich jak odzyskiwanie z najnowszych kopii zapasowych. Rozwiązanie Azure Backup pomaga w spełnianiu wymagań dotyczących zgodności oraz bardziej szczegółowym i elastycznym tworzeniem kopii zapasowych oraz ich przywracaniem.

## <a name="support-matrix"></a>Tabela obsługi

|Pomoc techniczna  |Szczegóły  |
|---------|---------|
|Obsługiwane wdrożenia   |  [Azure Database for PostgreSQL — pojedynczy serwer](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Obsługiwane regiony platformy Azure |  Wschodnie stany USA, Wschodnie stany USA 2, Środkowe stany USA, Południowo-środkowe stany USA, Zachodnie stany USA, Zachodnie stany USA 2, Zachodnio-środkowe stany USA, Brazylia Południowa, Kanada Środkowa, Europa Północna, Europa Zachodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Niemcy Zachodnio-środkowe, Niemcy Północno-Środkowe, Kanada Zachodnia Azja Wschodnia Azja Wschodnia, Australia Środkowa, Australia Środkowa 2, Północne Zjednoczone Emiraty Zjednoczone  |
|Obsługiwane wersje usługi Azure PostgreSQL    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Zagadnienia i ograniczenia dotyczące funkcji

- Wszystkie operacje są obsługiwane tylko z Azure Portal.
- Zalecany limit maksymalnego rozmiaru bazy danych to 400 GB.
- Tworzenie kopii zapasowej między regionami nie jest obsługiwane. Oznacza to, że nie można utworzyć kopii zapasowej serwera usługi Azure PostgreSQL w magazynie w innym regionie. Podobnie można przywrócić kopię zapasową tylko na serwerze w tym samym regionie co magazyn.
- Tylko dane są odzyskiwane w czasie przywracania. "Role" nie są przywracane.
- W wersji zapoznawczej zalecamy uruchamianie rozwiązania tylko w środowisku testowym.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

1. To rozwiązanie używa **pg_dump** tworzenia kopii zapasowych baz danych Usługi Azure PostgreSQL.

2. Po określeniu baz danych Azure PostgreSQL, dla których chcesz utworzyć kopię zapasową, usługa sprawdza, czy ma odpowiedni zestaw uprawnień i dostępu do wykonywania operacji tworzenia kopii zapasowej na serwerze i w bazie danych.

3. Azure Backup rola procesu roboczego z zainstalowanym rozszerzeniem kopii zapasowej. To rozszerzenie komunikuje się z serwerem Postgres.

4. To rozszerzenie składa się z koordynatora i wtyczki usługi Azure Postgres. Koordynator jest odpowiedzialny za wyzwalanie przepływów pracy dla różnych operacji, takich jak konfigurowanie kopii zapasowej, tworzenia kopii zapasowej i przywracania, ale wtyczka jest odpowiedzialna za rzeczywisty przepływ danych.
  
5. Po wyzwoleniu konfigurowania ochrony dla wybranych baz danych usługa tworzenia kopii zapasowych konfiguruje koordynatora przy użyciu harmonogramów tworzenia kopii zapasowych i innych szczegółów zasad.

6. W zaplanowanym czasie koordynator komunikuje się z wtyczką i rozpoczyna przesyłanie strumieniowe danych kopii zapasowej z serwera Postgres przy **użyciu pg_dump**.

7. Wtyczka wysyła dane bezpośrednio do magazynu usługi Backup, eliminując konieczność przechowywania lokalizacji przejściowej. Dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft i przechowywane przez usługę Azure Backup na kontach magazynu.

8. Po zakończeniu transferu danych koordynator potwierdza zatwierdzenie za pomocą usługi tworzenia kopii zapasowych.

    ![Proces tworzenia kopii zapasowej](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Konfigurowanie kopii zapasowej w bazach danych Azure PostgreSQL

Kopię zapasową można skonfigurować na wielu bazach danych na wielu serwerach usługi Azure PostgreSQL. Upewnij [się, że wstępnie wymagane](#prerequisite-permissions-for-configure-backup-and-restore) przez usługę uprawnienia do wykonywania kopii zapasowej serwerów Postgres są już skonfigurowane.

Poniższe instrukcje zawierają instrukcje krok po kroku dotyczące konfigurowania kopii zapasowych baz danych Azure PostgreSQL przy użyciu Azure Backup:

1. Istnieją dwa sposoby rozpoczęcia procesu:

    1. Przejdź do [tematu Backup Center Overview](backup-center-overview.md)Backup backup  ->  (Omówienie centrum kopii  ->  **zapasowych).**

        ![Przejdź do Centrum kopii zapasowych](./media/backup-azure-database-postgresql/backup-center.png)

        W **obszarze Zainicjuj:** Skonfiguruj kopię zapasową wybierz typ źródła **danych** **Azure Database for PostgreSQL**.

        ![W chmurze Inicjowanie: Konfigurowanie kopii zapasowej wybierz pozycję Typ źródła danych](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Alternatywnie możesz bezpośrednio przejść do usługi [Backup Vaults Backup.](backup-vault-overview.md)  ->  

        ![Przejdź do magazynu kopii zapasowych](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Wybieranie opcji Kopia zapasowa w magazynie kopii zapasowych](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. W **obszarze Konfigurowanie kopii** zapasowej wybierz magazyn kopii **zapasowych,** w którym chcesz utworzyć kopię zapasową baz danych Postgres. Te informacje są wstępnie wypełnione, jeśli jesteś już w kontekście magazynu.

    ![Wybieranie magazynu kopii zapasowych w chmurze Konfigurowanie kopii zapasowej](./media/backup-azure-database-postgresql/configure-backup.png)

1. Wybierz lub utwórz zasady **kopii zapasowych.**

    ![Wybieranie zasad kopii zapasowych](./media/backup-azure-database-postgresql/backup-policy.png)

1. Wybierz zasoby lub bazy danych Postgres do kopii zapasowej.

    ![Wybieranie zasobów do kopii zapasowej](./media/backup-azure-database-postgresql/select-resources.png)

1. Możesz wybrać z listy wszystkich serwerów usługi Azure PostgreSQL w ramach subskrypcji, jeśli są one w tym samym regionie co magazyn. Rozwiń strzałkę, aby wyświetlić listę baz danych na serwerze.

    ![Wybieranie serwerów](./media/backup-azure-database-postgresql/choose-servers.png)

1. Usługa uruchamia te testy na wybranych bazach danych, aby sprawdzić, czy magazyn ma uprawnienia do wykonywania kopii zapasowych wybranych serwerów i baz danych Postgres.
    1. **Aby kontynuować, gotowość**  do tworzenia kopii zapasowych dla wszystkich baz danych powinna być wczytana jako Powodzenie.
    1. Jeśli wystąpi błąd, napraw  ten błąd  i ponownie przeszukaj bazę danych lub usuń ją z wybranych opcji.

    ![Błędy walidacji do naprawienia](./media/backup-azure-database-postgresql/validation-errors.png)

1. Potwierdź wszystkie szczegóły w obszarze **Przeglądanie i konfigurowanie,** a następnie wybierz **pozycję Konfiguruj kopię zapasową,** aby przesłać operację.

    ![Potwierdź szczegóły w te tematu Review and configure (Przeglądanie i konfigurowanie)](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Po wyzwoleniu operacja **Konfigurowania kopii zapasowej** utworzy wystąpienie kopii zapasowej. Stan operacji można śledzić w okienku Wystąpienia [kopii](backup-center-monitor-operate.md#backup-instances) zapasowych w widoku Centrum kopii zapasowych lub magazynu.

    ![Wystąpienia kopii zapasowych](./media/backup-azure-database-postgresql/backup-instances.png)

1. Kopie zapasowe są wyzwalane zgodnie z harmonogramem tworzenia kopii zapasowych zdefiniowanym w zasadach. Zadania można śledzić w obszarze Zadania [kopii zapasowej.](backup-center-monitor-operate.md#backup-jobs) Obecnie można wyświetlać zadania z ostatnich siedmiu dni.

    ![Zadania tworzenia kopii zapasowej](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Tworzenie zasad kopii zapasowych

1. Przejdź do zasad **kopii zapasowych Centrum** kopii zapasowych  ->    ->  **Dodaj**. Alternatywnie możesz przejść do zasad tworzenia kopii zapasowych **magazynu**  ->  **kopii zapasowych**  ->  **Dodaj**.

    ![Dodawanie zasad kopii zapasowych](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Wprowadź **nazwę** nowych zasad.

    ![Wprowadź nazwę zasad](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Zdefiniuj harmonogram tworzenia kopii zapasowych. Obecnie obsługujemy **cotygodniową kopię zapasową.** Kopie zapasowe można zaplanować na co najmniej jeden dzień tygodnia.

    ![Definiowanie harmonogramu tworzenia kopii zapasowych](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. **Zdefiniuj ustawienia** przechowywania. Możesz dodać co najmniej jedną regułę przechowywania. Każda reguła przechowywania zakłada dane wejściowe dla określonych kopii zapasowych oraz czas przechowywania i przechowywania danych dla tych kopii zapasowych.

1. Kopie zapasowe można przechowywać w jednym z dwóch magazynów danych (lub **warstw):** Magazyn danych kopii zapasowych (warstwa Standardowa) lub Magazyn danych **archiwum** (w wersji zapoznawczej).

   Możesz wybrać opcję W momencie **wygaśnięcia, aby** przenieść kopię zapasową do magazynu danych archiwum po jego wygaśnięciu w magazynie danych kopii zapasowej.

1. Domyślna **reguła przechowywania jest** stosowana w przypadku braku jakiejkolwiek innej reguły przechowywania i ma wartość domyślną trzy miesiące.

    - Czas przechowywania w magazynie danych kopii zapasowej wynosi od siedmiu dni do 10 **lat.**
    - Czas przechowywania w magazynie danych Archiwum wynosi od sześciu miesięcy do 10 **lat.**

    ![Edytowanie czasu przechowywania](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>Reguły przechowywania są oceniane we wstępnie ustalonej kolejności priorytetów. Priorytet jest najwyższy dla reguły **dotyczącej roku,** po której następuje **reguła** miesięczna, a następnie reguła **tygodniowa.** Domyślne ustawienia przechowywania są stosowane, gdy nie kwalifikują się żadne inne reguły. Na przykład ten sam punkt odzyskiwania może być pierwszą pomyślną kopią zapasową co tydzień, a także pierwszą pomyślną kopią zapasową co miesiąc. Jednak ponieważ miesięczny priorytet reguły jest wyższy niż cotygodniowej reguły, ma zastosowanie przechowywanie odpowiadające pierwszej pomyślnej kopii zapasowej wykonanej co miesiąc.

## <a name="restore"></a>Przywracanie

Bazę danych można przywrócić na dowolnym serwerze usługi Azure PostgreSQL w ramach tej samej subskrypcji, jeśli usługa ma odpowiedni zestaw uprawnień na serwerze docelowym. Upewnij [się, że](#prerequisite-permissions-for-configure-backup-and-restore) wstępnie wymagane uprawnienia wymagane przez usługę do wykonywania kopii zapasowej serwerów Postgres są już skonfigurowane.

Postępuj zgodnie z tym przewodnikiem krok po kroku, aby wyzwolić przywracanie:

1. Istnieją dwa sposoby uruchamiania procesu przywracania:
    1. Przejdź do [tematu Omówienie centrum kopii zapasowych](backup-center-overview.md)  ->  **Przywróć**  ->  .

    ![Wybieranie opcji Przywróć w Centrum kopii zapasowych](./media/backup-azure-database-postgresql/backup-center-restore.png)

    W **obszarze Zainicjuj:** Przywróć wybierz typ źródła **danych jako** **Azure Database for PostgreSQL**. Wybierz wystąpienie **kopii zapasowej**.

    ![Wybierz typ źródła danych w poleceniu Initiate:Restore](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Alternatywnie możesz przejść bezpośrednio do usługi **Backup Vault**  ->  **Backup Instances.** Wybierz **pozycję Wystąpienie kopii zapasowej** odpowiadające bazie danych, którą chcesz przywrócić.

    ![Wystąpienia kopii zapasowej do przywrócenia](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Lista wystąpień kopii zapasowych](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Wybieranie opcji Przywróć](./media/backup-azure-database-postgresql/select-restore.png)

1. **Wybierz punkt odzyskiwania** z listy wszystkich pełnych kopii zapasowych dostępnych dla wybranego wystąpienia kopii zapasowej. Domyślnie jest wybrany najnowszy punkt odzyskiwania.

    ![Wybieranie punktu odzyskiwania](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Lista punktów odzyskiwania](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Parametry **przywracania danych wejściowych**. W tym momencie możesz wybrać jeden z  dwóch rodzajów przywracania: Przywróć jako bazę danych i Przywróć **jako pliki.**

1. **Przywróć** jako bazę danych: przywróć dane kopii zapasowej, aby utworzyć nową bazę danych na docelowym serwerze PostgreSQL.

    - Serwer docelowy może być taki sam jak serwer źródłowy. Jednak nadpisanie oryginalnej bazy danych nie jest obsługiwane.
    - Możesz wybrać serwer we wszystkich subskrypcjach, ale w tym samym regionie co magazyn.
    - Wybierz **pozycję Przejrzyj i przywróć.** Spowoduje to wyzwolenie walidacji w celu sprawdzenia, czy usługa ma odpowiednie uprawnienia do przywracania na serwerze docelowym.

    ![Przywróć jako bazę danych](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Przywróć jako** pliki: zrzuć pliki kopii zapasowej na docelowe konto magazynu (obiekty blob).

    - Możesz wybrać konta magazynu dla wszystkich subskrypcji, ale w tym samym regionie co magazyn.
    - Wybierz kontener docelowy z listy kontenerów przefiltrowanych dla wybranego konta magazynu.
    - Wybierz **pozycję Przejrzyj i przywróć.** Spowoduje to wyzwolenie walidacji w celu sprawdzenia, czy usługa ma odpowiednie uprawnienia do przywracania na serwerze docelowym.

    ![Przywróć jako pliki](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Jeśli punkt odzyskiwania znajduje się w warstwie Archiwum, przed przywróceniem punktu odzyskiwania należy go ponownie zaimterować.
   
   ![Ustawienia ponownego nawodnienia](./media/backup-azure-database-postgresql/rehydration-settings.png)
   
   Podaj następujące dodatkowe parametry wymagane do ponownego nawodnienia:
   - **Priorytet ponownego nawodnienia:** Wartość domyślna to **Standardowa.**
   - **Czas trwania ponownego nawodnienia:** Maksymalny czas trwania ponownego nawodnienia wynosi 30 dni, a minimalny czas ponownego nawodnienia wynosi 10 dni. Wartość domyślna to **15**.
   
   Punkt odzyskiwania jest przechowywany w magazynie danych kopii **zapasowej** przez określony czas ponownego nawodnienia.


1. Przejrzyj informacje i wybierz pozycję **Przywróć.** Spowoduje to wyzwolenie odpowiedniego zadania przywracania, które można śledzić w obszarze **Zadania kopii zapasowej.**

>[!NOTE]
>Obsługa archiwum dla Azure Database for PostgreSQL jest w ograniczonej publicznej wersji zapoznawczej.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Uprawnienia wymagań wstępnych do konfigurowania kopii zapasowej i przywracania

Azure Backup są zgodnie z rygorystycznymi wytycznymi dotyczącymi zabezpieczeń. Mimo że itâ€™ jest natywną usługą platformy Azure, nie zakłada się uprawnień do zasobu i muszą być jawnie nadane przez użytkownika.  Podobnie poświadczenia służące do nawiązywania połączenia z bazą danych nie są przechowywane. Jest to ważne w celu ochrony danych. Zamiast tego używamy Azure Active Directory uwierzytelniania.

[Pobierz ten dokument,](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) aby uzyskać zautomatyzowany skrypt i powiązane instrukcje. Spowoduje to przyznanie odpowiedniego zestawu uprawnień do serwera usługi Azure PostgreSQL na potrzeby tworzenia kopii zapasowych i przywracania.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Zarządzanie kopiami zapasową baz danych Usługi Azure PostgreSQL

Są to operacje zarządzania, które można wykonywać na **wystąpieniach kopii zapasowych:**

### <a name="on-demand-backup"></a>Kopia zapasowa na żądanie

Aby wyzwolić kopię zapasową, która nie jest określona w harmonogramie określonym w zasadach, przejdź do tematu Tworzenie kopii zapasowej **wystąpień kopii** zapasowej  ->  **teraz.**
Wybierz z listy reguł przechowywania, które zostały zdefiniowane w skojarzonych zasadach kopii zapasowych.

![Wyzwalanie tworzenia kopii zapasowej teraz](./media/backup-azure-database-postgresql/backup-now.png)

![Wybieranie z listy reguł przechowywania](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Zatrzymywanie ochrony

Ochronę elementu kopii zapasowej można zatrzymać. Spowoduje to również usunięcie skojarzonych punktów odzyskiwania dla tego elementu kopii zapasowej. Jeśli punkty odzyskiwania nie znajdują się w warstwie Archiwum przez co najmniej sześć miesięcy, usunięcie tych punktów odzyskiwania spowoduje naliczenie kosztów wczesnego usunięcia. Nie zapewniamy jeszcze opcji zatrzymania ochrony przy zachowaniu istniejących punktów odzyskiwania.

![Zatrzymywanie ochrony](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Zmienianie zasad

Skojarzone zasady można zmienić z wystąpieniem kopii zapasowej.

1. Wybierz zasady **zmiany wystąpienia kopii**  ->  **zapasowej.**

    ![Zmienianie zasad](./media/backup-azure-database-postgresql/change-policy.png)

1. Wybierz nowe zasady, które chcesz zastosować do bazy danych.

    ![Ponowne przypisanie zasad](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ta sekcja zawiera informacje dotyczące rozwiązywania problemów z kopiami zapasową baz danych Azure PostgreSQL przy użyciu Azure Backup.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Nadaj magazynowi usługi Backup dostęp **MSI** do odczytu na serwerze PG, którego kopię zapasową chcesz utworzyć lub przywrócić.

Aby nawiązać bezpieczne połączenie z bazą danych PostgreSQL, Azure Backup model uwierzytelniania tożsamości usługi zarządzanej [(MSI).](../active-directory/managed-identities-azure-resources/overview.md) Oznacza to, że magazyn kopii zapasowych będzie miał dostęp tylko do tych zasobów, które zostały jawnie przyznane przez użytkownika.

Systemowa msi jest automatycznie przypisywana do magazynu w czasie tworzenia. Musisz nadać temu magazynowi msi dostęp do serwerów PostgreSQL, z których planujesz back up databases.

Kroki:

1. Na serwerze Postgres przejdź do **okienka Access Control (IAM).**

    ![Access Control okienku](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Wybierz **pozycję Dodaj przypisania ról.**

    ![Dodaj przypisanie roli](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. W prawym okienku kontekstu, które zostanie otwarte, wprowadź następujące informacje:<br>

   - **Rola:** Wybierz rolę **Czytelnik** z listy rozwijanej.<br>
   - **Przypisz dostęp do:** Z **listy rozwijanej** wybierz opcję Użytkownik, grupa lub nazwa główna usługi.<br>
   - **Wybierz pozycję:** Wprowadź nazwę magazynu kopii zapasowych, dla którego chcesz utworzyć kopię zapasową tego serwera i jego baz danych.<br>

    ![Wybierz rolę](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Utwórz użytkownika kopii zapasowej bazy danych, który może uwierzytelniać się za pomocą Azure Active Directory:

Ten błąd może wynikać z braku administratora Azure Active Directory serwera PostgreSQL lub braku użytkownika kopii zapasowej, który może się uwierzytelnić przy użyciu Azure Active Directory.

Kroki:

Dodaj administratora usługi Active Directory do serwera OSS:

Ten krok jest wymagany do nawiązania połączenia z bazą danych za pośrednictwem użytkownika, który może Azure Active Directory za pomocą Azure Active Directory hasła. Administrator usługi Azure AD w Azure Database for PostgreSQL będzie mieć rolę **azure_ad_admin**. Tylko rola **azure_ad_admin** może tworzyć nowych użytkowników bazy danych, którzy mogą uwierzytelniać się w usłudze Azure AD.

1. Przejdź do karty Administrator usługi Active Directory w lewym okienku nawigacji widoku serwera i dodaj siebie (lub inną osobę) jako administratora usługi Active Directory.

    ![Ustawianie administratora usługi Active Directory](./media/backup-azure-database-postgresql/set-admin.png)

1. Pamiętaj, aby **zapisać** ustawienie użytkownika administratora usługi AD.

    ![Zapisywanie ustawienia administratora usługi Active Directory](./media/backup-azure-database-postgresql/save-admin-setting.png)

Zapoznaj się [z tym dokumentem,](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) aby uzyskać listę kroków, które należy wykonać, aby wykonać kroki udzielania uprawnień.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Ustanów połączenie sieciowe, włączając flagę **Zezwalaj na dostęp** do usług platformy Azure w widoku serwera. W widoku serwera w okienku **Zabezpieczenia** połączeń ustaw flagę Zezwalaj na dostęp do usług **platformy Azure** na **wartość Tak.**

![Zezwalanie na dostęp usługom platformy Azure](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Uprawnienie do przywracania do kontenera konta magazynu podczas przywracania jako plików

1. Nadaj magazynowi usługi Backup uprawnienia dostępu do kontenerów konta magazynu przy użyciu Azure Portal.
    1. Przejdź do **konta magazynu**  ->  **Access Control**  ->  **Dodaj przypisanie roli.**
    1. Przypisz **rolę Współautor danych obiektu blob usługi** Storage do pliku MSI magazynu kopii zapasowych.

    ![Przypisywanie roli Współautor danych obiektu blob usługi Storage](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Możesz też udzielić szczegółowych uprawnień do określonego kontenera, do których chcesz przywrócić, za pomocą polecenia [az role assignment create](/cli/azure/role/assignment) interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Zastąp parametr assignee  identyfikatorem aplikacji tożsamości usługi msi magazynu i parametrem zakresu, aby odwołać się do określonego kontenera.
    1. Aby uzyskać identyfikator **aplikacji magazynu MSI,** wybierz pozycję **Wszystkie aplikacje w** obszarze Typ **aplikacji:**

        ![Wybierz pozycję Wszystkie aplikacje](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Wyszukaj nazwę magazynu i skopiuj identyfikator aplikacji:

        ![Wyszukiwanie nazwy magazynu](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Następne kroki

- [Omówienie magazynów kopii zapasowych](backup-vault-overview.md)
