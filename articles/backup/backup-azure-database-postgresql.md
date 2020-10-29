---
title: Tworzenie kopii zapasowej usługi Azure Database for PostgreSQL
description: Dowiedz się więcej o Azure Database for PostgreSQL kopii zapasowej z długoterminowym przechowywaniem (wersja zapoznawcza)
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: edbfdb6ea741cdb344a121acdbee3b8bd4bc743c
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927893"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL kopii zapasowej z długoterminowym przechowywaniem (wersja zapoznawcza)

Azure Backup i usługi Azure Database zostały połączone w celu utworzenia rozwiązania do tworzenia kopii zapasowych klasy korporacyjnej dla serwerów Azure Database for PostgreSQL, które przechowują kopie zapasowe przez maksymalnie 10 lat.

Oprócz długoterminowego przechowywania rozwiązanie ma także wiele innych funkcji, jak pokazano poniżej:

- Kontrola dostępu oparta na rolach (Azure RBAC) do bazy danych przy użyciu uwierzytelniania Azure Active Directory i tożsamość usługi zarządzanej (MSI).
- Planowanie kopii zapasowych według harmonogramu przez klienta i na żądanie na poziomie poszczególnych baz danych.
- Przywracanie na poziomie bazy danych do dowolnego serwera Postgres lub bezpośrednio do magazynu obiektów BLOB.
- Długoterminowe przechowywanie.
- Centralne monitorowanie wszystkich operacji i zadań.
- Kopie zapasowe są przechowywane w oddzielnych domenach zabezpieczeń i błędów. Nawet w przypadku naruszenia zabezpieczeń lub nawet zabicia serwera źródłowego kopie zapasowe byłyby bezpieczne w [magazynie kopii zapasowych](backup-vault-overview.md).
- Korzystanie z **pg_dump** zapewnia większą elastyczność w zakresie przywracania, w taki sposób, że można przywrócić wiele wersji bazy danych, a nawet przywrócić tylko część kopii zapasowej.

To rozwiązanie może być używane niezależnie lub oprócz natywnego rozwiązania do tworzenia kopii zapasowych oferowanego przez usługę Azure PostgreSQL, które oferuje przechowywanie do 35 dni. Natywne rozwiązanie jest odpowiednie dla operacji odzyskiwania operacyjnego, takich jak odzyskiwanie z najnowszych kopii zapasowych. Azure Backup rozwiązanie pomaga w spełnianiu wymagań dotyczących zgodności oraz bardziej szczegółowych i elastycznych kopii zapasowych i przywracania.

## <a name="support-matrix"></a>Tabela obsługi

|Pomoc techniczna  |Szczegóły  |
|---------|---------|
|Obsługiwane wdrożenia   |  Azure Database for PostgreSQL autonomiczny pojedynczy serwer     |
|Obsługiwane regiony platformy Azure |  Wschodnie stany USA, Wschodnie stany USA 2, środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA, zachodnie stany USA 2, zachodnie stany USA, Brazylia Południowa, Kanada środkowa, Europa Północna, Europa Zachodnia, Południowe Zjednoczone Królestwo, Zachodnie Zjednoczone Królestwo, Niemcy Środkowo-Zachodnie, Szwajcaria Północna, Szwajcaria Zachodnia, Azja Wschodnia, Południowe Azja Wschodnia, Japonia Wschodnia, Japonia Zachodnia, Korea środkowa, Korea Południowa  |
|Obsługiwane wersje usługi Azure PostgreSQL    |   9,5, 9,6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Zagadnienia i ograniczenia dotyczące funkcji

- Wszystkie operacje są obsługiwane tylko z Azure Portal.
- Zalecany limit maksymalny rozmiar bazy danych to 400 GB.
- Tworzenie kopii zapasowych między regionami nie jest obsługiwane. Oznacza to, że nie można utworzyć kopii zapasowej serwera usługi Azure PostgreSQL w magazynie w innym regionie. Podobnie można przywrócić kopię zapasową tylko do serwera w tym samym regionie, w którym znajduje się magazyn.
- Tylko dane są odzyskiwane w momencie przywracania. "Role" nie są przywracane.
- W wersji zapoznawczej zalecamy korzystanie z rozwiązania tylko w środowisku testowym.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

1. To rozwiązanie używa **pg_dump** do wykonywania kopii zapasowych baz danych usługi Azure PostgreSQL.

2. Po określeniu baz danych usługi Azure PostgreSQL, których kopia zapasowa ma zostać utworzona, usługa sprawdza poprawność, jeśli ma odpowiedni zestaw uprawnień i dostęp do wykonywania operacji tworzenia kopii zapasowej na serwerze i w bazie danych programu.

3. Azure Backup powoduje utworzenie roli procesu roboczego z zainstalowanym rozszerzeniem kopii zapasowej. To rozszerzenie komunikuje się z serwerem Postgres.

4. To rozszerzenie składa się z koordynatora i wtyczki usługi Azure Postgres. Chociaż koordynator jest odpowiedzialny za wyzwalanie przepływów pracy dla różnych operacji, takich jak konfigurowanie kopii zapasowej, kopii zapasowej i przywracania, wtyczka jest odpowiedzialna za rzeczywisty przepływ danych.
  
5. Po zainicjowaniu konfigurowania ochrony dla wybranych baz danych usługa tworzenia kopii zapasowych konfiguruje koordynatora przy użyciu harmonogramów tworzenia kopii zapasowych i innych szczegółów zasad.

6. W zaplanowanym czasie koordynator komunikuje się z wtyczką i zaczyna przesyłać strumieniowo dane kopii zapasowej z serwera Postgres przy użyciu **pg_dump** .

7. Wtyczka wysyła dane bezpośrednio do magazynu kopii zapasowych, eliminując konieczność przechowywania lokalizacji tymczasowej. Dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft i przechowywane przez usługę Azure Backup na kontach magazynu.

8. Po zakończeniu transferu danych koordynator potwierdza zatwierdzenie w usłudze kopii zapasowej.

    ![Proces tworzenia kopii zapasowej](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Konfigurowanie kopii zapasowych w bazach danych usługi Azure PostgreSQL

Można skonfigurować tworzenie kopii zapasowych na wielu bazach danych na wielu serwerach PostgreSQL platformy Azure. Upewnij się, że [uprawnienia](#prerequisite-permissions-for-configure-backup-and-restore) wymagane przez usługę do tworzenia kopii zapasowych serwerów Postgres są już skonfigurowane.

Poniższe instrukcje to przewodnik krok po kroku dotyczący konfigurowania kopii zapasowych w bazach danych Azure PostgreSQL przy użyciu Azure Backup:

1. Istnieją dwa sposoby rozpoczęcia procesu:

    1. Przejdź do [centrum kopii zapasowych](backup-center-overview.md)  ->  **— Omówienie**  ->  **kopii zapasowej** .

        ![Przejdź do centrum kopii zapasowych](./media/backup-azure-database-postgresql/backup-center.png)

        W obszarze **Inicjowanie: Skonfiguruj kopię zapasową** wybierz **Typ źródła danych** jako **Azure Database for PostgreSQL** .

        ![W obszarze inicjowanie: Konfigurowanie kopii zapasowej wybierz pozycję typ źródła danych](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Alternatywnie możesz bezpośrednio przejść do kopii zapasowych [magazynów kopii zapasowych](backup-vault-overview.md)  ->  **Backup** .

        ![Przejdź do magazynów kopii zapasowych](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Wybieranie kopii zapasowej w magazynie kopii zapasowych](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. W obszarze **Konfigurowanie kopii zapasowej** wybierz **Magazyn kopii zapasowych** , do którego chcesz utworzyć kopię zapasową baz danych Postgres. Te informacje są wstępnie wypełniane, jeśli jesteś już w kontekście magazynu.

    ![Wybieranie magazynu kopii zapasowych w obszarze Konfigurowanie kopii zapasowej](./media/backup-azure-database-postgresql/configure-backup.png)

1. Wybierz lub Utwórz **zasady tworzenia kopii zapasowych** .

    ![Wybieranie zasad kopii zapasowych](./media/backup-azure-database-postgresql/backup-policy.png)

1. Wybierz pozycję zasoby lub bazy danych Postgres, aby utworzyć kopię zapasową.

    ![Wybierz zasoby do utworzenia kopii zapasowej](./media/backup-azure-database-postgresql/select-resources.png)

1. Możesz wybrać z listy wszystkie serwery usługi Azure PostgreSQL w różnych subskrypcjach, jeśli znajdują się w tym samym regionie co magazyn. Rozwiń strzałkę, aby wyświetlić listę baz danych znajdujących się na serwerze.

    ![Wybierz serwery](./media/backup-azure-database-postgresql/choose-servers.png)

1. Usługa uruchamia te testy w wybranych bazach danych w celu sprawdzenia, czy magazyn ma uprawnienia do tworzenia kopii zapasowych wybranych serwerów Postgres i baz danych.
    1. **Gotowość do tworzenia kopii zapasowych** dla wszystkich baz danych powinna zostać **przeczytana** w celu kontynuowania.
    1. Jeśli wystąpi błąd, **Usuń** błąd i ponownie **Zweryfikuj** lub Usuń bazę danych z wybranych opcji.

    ![Błędy walidacji do naprawienia](./media/backup-azure-database-postgresql/validation-errors.png)

1. Potwierdź wszystkie szczegóły w obszarze **Przegląd i skonfiguruj** i wybierz pozycję **Konfiguruj kopię zapasową** , aby przesłać operację.

    ![Potwierdź szczegóły w przeglądzie i skonfiguruj](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Po wyzwoleniu operacja **Konfiguruj kopię zapasową** spowoduje utworzenie wystąpienia kopii zapasowej. Stan operacji można śledzić w okienku [wystąpienia kopii zapasowej](backup-center-monitor-operate.md#backup-instances) w widoku centrum kopii zapasowych lub magazynu.

    ![Wystąpienia kopii zapasowej](./media/backup-azure-database-postgresql/backup-instances.png)

1. Kopie zapasowe są wyzwalane zgodnie z harmonogramem tworzenia kopii zapasowych zdefiniowanym w zasadach. Zadania można śledzić w obszarze [zadania tworzenia kopii zapasowej](backup-center-monitor-operate.md#backup-jobs). Obecnie można wyświetlać zadania z ostatnich siedmiu dni.

    ![Zadania tworzenia kopii zapasowej](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Tworzenie zasad kopii zapasowych

1. Przejdź do **Backup Center** pozycji  ->  **zasady tworzenia kopii zapasowych** centrum kopii zapasowych  ->  **Dodaj** . Alternatywnie możesz przejść do pozycji **Backup vault**  ->  **zasady kopii zapasowej** magazynu kopii zapasowych  ->  **Dodaj** .

    ![Dodawanie zasad kopii zapasowych](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Wprowadź **nazwę** nowych zasad.

    ![Wprowadź nazwę zasad](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Zdefiniuj harmonogram tworzenia kopii zapasowych. Obecnie obsługujemy **cotygodniowe** wykonywanie kopii zapasowych. Kopie zapasowe można planować na co najmniej jeden dzień tygodnia.

    ![Definiowanie harmonogramu tworzenia kopii zapasowych](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. Zdefiniuj ustawienia **przechowywania** . Można dodać co najmniej jedną regułę przechowywania. Każda reguła przechowywania przyjmuje dane wejściowe dla określonych kopii zapasowych oraz czas przechowywania i przechowywania danych dla tych kopii zapasowych.

1. Możesz zapisywać kopie zapasowe w jednym z dwóch magazynów danych (lub warstw): **Backup Data Store** (warstwa gorąca) lub **archiwum danych archiwalnych** (w wersji zapoznawczej). Można wybrać jedną z **dwóch opcji warstwowych** , aby zdefiniować, kiedy kopie zapasowe mają być warstwowe między dwoma magazynami danych:

    - Wybierz opcję kopiowania **natychmiast** , jeśli wolisz utworzyć kopię zapasową zarówno dla magazynu danych kopii zapasowej, jak i archiwum.
    - Wybierz przeniesienie **na wygaśnięcie** , jeśli wolisz przenieść kopię zapasową w celu zarchiwizowania magazynu danych w magazynie danych kopii zapasowej.

1. **Domyślna reguła przechowywania** jest stosowana w przypadku braku innej reguły przechowywania i ma wartość domyślną trzy miesiące.

    - Czas trwania przechowywania wynosi od siedmiu dni do 10 lat w **magazynie danych kopii zapasowej** .
    - Czas trwania przechowywania wynosi od sześciu miesięcy do 10 lat w **magazynie danych archiwum** .

    ![Edytowanie czasu trwania przechowywania](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>Reguły przechowywania są oceniane w ustalonej kolejności priorytetu. Priorytet jest najwyższy dla reguły **rocznej** , a następnie **co miesiąc** , a następnie regułę **tygodniową** . Domyślne ustawienia przechowywania są stosowane, gdy nie ma innych reguł. Na przykład ten sam punkt odzyskiwania może stanowić pierwszą pomyślną kopię zapasową wykonywaną co tydzień, a także pierwszą pomyślną kopię zapasową wykonywaną co miesiąc. Ponieważ priorytet reguły miesięcznej jest wyższy niż wartość reguły cotygodniowej, obowiązuje przechowywanie odpowiadające pierwszej pomyślnej kopii zapasowej wykonanej co miesiąc.

## <a name="restore"></a>Przywracanie

Bazę danych można przywrócić na dowolnym serwerze usługi Azure PostgreSQL w ramach tej samej subskrypcji, jeśli usługa ma odpowiedni zestaw uprawnień na serwerze docelowym. Upewnij się, że [uprawnienia](#prerequisite-permissions-for-configure-backup-and-restore) wymagane przez usługę do tworzenia kopii zapasowych serwerów Postgres są już skonfigurowane.

Postępuj zgodnie z tym przewodnikiem krok po kroku, aby wyzwolić przywracanie:

1. Istnieją dwa sposoby uruchomienia procesu przywracania:
    1. Przejdź do omówienia [centrum kopii zapasowych](backup-center-overview.md)  ->  **—**  ->  **przywracanie** .

    ![Wybieranie opcji Przywróć w centrum kopii zapasowych](./media/backup-azure-database-postgresql/backup-center-restore.png)

    W obszarze **Inicjowanie: Przywracanie** wybierz **Typ źródła danych** jako **Azure Database for PostgreSQL** . Wybierz **wystąpienie kopii zapasowej** .

    ![Wybierz typ źródła danych w elemencie initiate: Restore](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Alternatywnie możesz bezpośrednio przejść do wystąpień kopii zapasowej **magazynu kopii zapasowych**  ->  **Backup Instances** . Wybierz **wystąpienie kopii zapasowej** odpowiadające bazie danych, którą chcesz przywrócić.

    ![Wystąpienia kopii zapasowej do przywrócenia](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Lista wystąpień kopii zapasowych](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Wybierz pozycję Przywróć](./media/backup-azure-database-postgresql/select-restore.png)

1. **Wybierz pozycję punkt odzyskiwania** z listy wszystkich pełnych kopii zapasowych dostępnych dla wybranego wystąpienia kopii zapasowej. Domyślnie jest wybierany najnowszy punkt odzyskiwania.

    ![Wybierz punkt odzyskiwania](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Lista punktów odzyskiwania](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. **Parametry przywracania** danych wejściowych. W tym momencie można wybrać jedną z dwóch rodzajów przywracania: **przywracanie jako baza danych** i **przywracanie jako pliki** .

1. **Przywróć jako bazę danych** : Przywróć dane kopii zapasowej, aby utworzyć nową bazę danych na docelowym serwerze PostgreSQL.

    - Serwer docelowy może być taki sam jak serwer źródłowy. Jednak zastąpienie oryginalnej bazy danych nie jest obsługiwane.
    - Możesz wybrać z serwera wszystkie subskrypcje, ale w tym samym regionie, w którym znajduje się magazyn.
    - Wybierz pozycję **Przegląd + przywracanie** . Spowoduje to wyzwolenie walidacji w celu sprawdzenia, czy usługa ma odpowiednie uprawnienia do przywracania na serwerze docelowym.

    ![Przywróć jako bazę danych](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Przywróć jako pliki** : Zrzuć pliki kopii zapasowej na docelowym koncie magazynu (BLOB).

    - Możesz wybrać spośród kont magazynu dla wszystkich subskrypcji, ale w tym samym regionie, w którym znajduje się magazyn.
    - Wybierz kontener docelowy z listy kontenerów przefiltrowany dla wybranego konta magazynu.
    - Wybierz pozycję **Przegląd + przywracanie** . Spowoduje to wyzwolenie walidacji w celu sprawdzenia, czy usługa ma odpowiednie uprawnienia do przywracania na serwerze docelowym.

    ![Przywróć jako pliki](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Przejrzyj informacje i wybierz pozycję **Przywróć** . Spowoduje to wyzwolenie odpowiedniego zadania przywracania, które może być śledzone w obszarze **zadania tworzenia kopii zapasowej** .

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Wstępnie wymagane uprawnienia do konfigurowania kopii zapasowych i przywracania

Azure Backup obowiązują rygorystyczne wytyczne dotyczące zabezpieczeń. Mimo że jest to natywna usługa platformy Azure, uprawnienia do zasobu nie są zakładane i muszą być jawnie określone przez użytkownika.  Podobnie poświadczenia do łączenia się z bazą danych nie są przechowywane. Jest to ważne, aby chronić dane. Zamiast tego używamy uwierzytelniania Azure Active Directory.

[Pobierz ten dokument](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) , aby uzyskać zautomatyzowany skrypt i powiązane instrukcje. Zostanie udzielony odpowiedni zestaw uprawnień dla serwera usługi Azure PostgreSQL, na potrzeby tworzenia kopii zapasowych i przywracania.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Zarządzanie kopiami zapasowymi baz danych usługi Azure PostgreSQL

Są to operacje zarządzania, które można wykonywać w **wystąpieniach kopii zapasowych** :

### <a name="on-demand-backup"></a>Kopia zapasowa na żądanie

Aby wyzwolić kopię zapasową poza harmonogramem określonym w zasadach, przejdź do pozycji tworzenie kopii zapasowych **wystąpień** kopii  ->  **zapasowej teraz** .
Wybierz z listy reguły przechowywania zdefiniowane w ramach skojarzonych zasad tworzenia kopii zapasowych.

![Wykonaj kopię zapasową teraz](./media/backup-azure-database-postgresql/backup-now.png)

![Wybierz z listy reguł przechowywania](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Zatrzymywanie ochrony

Można zatrzymać ochronę dla elementu kopii zapasowej. Spowoduje to również usunięcie skojarzonych punktów odzyskiwania dla tego elementu kopii zapasowej. Nie zapewniamy jeszcze opcji zatrzymania ochrony przy zachowaniu istniejących punktów odzyskiwania.

![Zatrzymywanie ochrony](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Zmień zasady

Można zmienić skojarzone zasady z wystąpieniem kopii zapasowej.

1. Wybierz zasady **zmiany wystąpienia kopii zapasowej**  ->  **Change Policy** .

    ![Zmień zasady](./media/backup-azure-database-postgresql/change-policy.png)

1. Wybierz nowe zasady, które chcesz zastosować do bazy danych.

    ![Ponowne przypisywanie zasad](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ta sekcja zawiera informacje dotyczące rozwiązywania problemów z tworzeniem kopii zapasowych baz danych usługi Azure PostgreSQL za pomocą Azure Backup.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Nadaj kopii zapasowej MSI dostęp do **odczytu** na serwerze PG, dla którego chcesz utworzyć kopię zapasową lub przywrócić:

Aby nawiązać bezpieczne połączenie z bazą danych PostgreSQL, Azure Backup używa modelu uwierzytelniania [tożsamość usługi zarządzanej (msi)](../active-directory/managed-identities-azure-resources/overview.md) . Oznacza to, że magazyn kopii zapasowych będzie miał dostęp tylko do tych zasobów, które zostały jawnie przyznane użytkownikowi uprawnienia.

System MSI jest automatycznie przypisywany do magazynu w momencie jego tworzenia. Musisz nadać temu magazynowi MSI dostęp do serwerów PostgreSQL, z których zamierzasz tworzyć kopie zapasowe baz danych.

Kroki:

1. Na serwerze Postgres przejdź do okienka **Access Control (IAM)** .

    ![Okienko Access Control](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Wybierz pozycję **Dodaj przypisanie roli** .

    ![Dodaj przypisanie roli](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. W prawym okienku kontekstu, które zostanie otwarte, wprowadź następujące polecenie:<br>

    **Rola:** Czytnika<br>
    **Przypisz dostęp do:** Wybieranie **magazynu kopii zapasowych**<br>
    Jeśli nie możesz znaleźć opcji **Magazyn kopii zapasowych** na liście rozwijanej, wybierz **opcję użytkownik, Grupa lub nazwa główna usługi Azure AD**<br>

    ![Wybierz rolę](./media/backup-azure-database-postgresql/select-role.png)

    **Wybierz:** Wprowadź nazwę magazynu kopii zapasowych, do którego chcesz utworzyć kopię zapasową tego serwera i jego baz danych.<br>

    ![Wprowadź nazwę magazynu kopii zapasowych](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Utwórz użytkownika kopii zapasowej bazy danych, który może być uwierzytelniany przy użyciu Azure Active Directory:

Ten błąd może występować w przypadku braku Azure Active Directory administratora dla serwera PostgreSQL lub nieobecności użytkownika kopii zapasowej, który może być uwierzytelniany przy użyciu Azure Active Directory.

Kroki:

Dodaj administratora Active Directory do serwera OSS:

Ten krok jest wymagany do nawiązania połączenia z bazą danych za pomocą użytkownika, który może uwierzytelniać się przy użyciu Azure Active Directory zamiast hasła. Użytkownik administracyjny usługi Azure AD w Azure Database for PostgreSQL będzie miał rolę **azure_ad_admin** . Tylko rola **azure_ad_admin** może tworzyć użytkowników nowej bazy danych, którzy mogą uwierzytelniać się w usłudze Azure AD.

1. Przejdź do karty administrator Active Directory w lewym okienku nawigacji w widoku serwera i Dodaj siebie (lub kogoś innego) jako administrator Active Directory.

    ![Ustaw Active Directory administratora](./media/backup-azure-database-postgresql/set-admin.png)

1. Pamiętaj, aby **zapisać** ustawienia użytkownika administrator usługi AD.

    ![Zapisz ustawienia użytkownika Active Directory administrator](./media/backup-azure-database-postgresql/save-admin-setting.png)

Zapoznaj się z [tym dokumentem](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) , aby zapoznać się z listą czynności, które należy wykonać w celu wykonania czynności związanych z udzieleniem uprawnień.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Aby uzyskać wgląd w sieć, Włącz flagę " **Zezwalaj na dostęp do usług platformy Azure** " w widoku serwera. W widoku serwer w okienku **zabezpieczenia połączeń** Ustaw flagę **Zezwalaj na dostęp do usług platformy Azure** na **wartość tak** .

![Zezwalanie na dostęp usługom platformy Azure](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Uprawnienie do przywracania do kontenera konta magazynu podczas przywracania jako pliki

1. Nadaj plikowi MSI magazynu kopii zapasowych uprawnienia dostępu do kontenerów kont magazynu przy użyciu Azure Portal.
    1. Przejdź do **konta magazynu**  ->  **Access Control**  ->  **Dodaj przypisanie roli** .
    1. Przypisz rolę **współautor danych obiektów blob magazynu** do pliku MSI magazynu kopii zapasowych.

    ![Przypisywanie roli współautor danych obiektów blob magazynu](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Alternatywnie Udziel szczegółowych uprawnień do określonego kontenera, do którego przywracasz, przy użyciu interfejsu wiersza polecenia platformy Azure [AZ role przypisania Create](/cli/azure/role/assignment) .

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Zastąp parametr osoby przydzielonej **identyfikatorem aplikacji** MSI magazynu i parametr zakresu, aby odwołać się do określonego kontenera.
    1. Aby uzyskać **Identyfikator aplikacji** dla pliku MSI magazynu, wybierz pozycję **wszystkie aplikacje** w obszarze **Typ aplikacji** :

        ![Wybierz wszystkie aplikacje](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Wyszukaj nazwę magazynu i skopiuj identyfikator aplikacji:

        ![Wyszukaj nazwę magazynu](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Następne kroki

- [Magazyny kopii zapasowych — Omówienie](backup-vault-overview.md)
