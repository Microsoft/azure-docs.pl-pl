---
title: Tworzenie kopii zapasowych udziałów plików platformy Azure w Azure Portal
description: Dowiedz się, jak używać Azure Portal do tworzenia kopii zapasowych udziałów plików platformy Azure w magazynie Recovery Services
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: ca49f1ad48ab0534b27b91ad6a5a50b393cda782
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88890352"
---
# <a name="back-up-azure-file-shares"></a>Tworzenie kopii zapasowej udziałów plików platformy Azure

W tym artykule wyjaśniono, jak utworzyć kopię zapasową [udziałów plików platformy Azure](../storage/files/storage-files-introduction.md) z Azure Portal.

Ten artykuł obejmuje następujące zagadnienia:

* Utwórz magazyn usługi Recovery Services.
* Konfigurowanie kopii zapasowej z magazynu Recovery Services
* Konfiguruj kopię zapasową z okienka udostępniania plików
* Uruchomianie zadania tworzenia kopii zapasowej na żądanie w celu tworzenia punktu przywracania

## <a name="prerequisites"></a>Wymagania wstępne

* [Dowiedz się więcej](azure-file-share-backup-overview.md) na temat rozwiązania do tworzenia kopii zapasowych opartego na migawce udziałów plików platformy Azure.
* Upewnij się, że udział plików jest obecny w jednym z [obsługiwanych typów kont magazynu](azure-file-share-support-matrix.md).
* Zidentyfikuj lub Utwórz [magazyn Recovery Services](#create-a-recovery-services-vault) w tym samym regionie, w którym znajduje się konto magazynu obsługujące udział plików.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Konfigurowanie kopii zapasowej z magazynu Recovery Services

Poniższe kroki wyjaśniają, jak można skonfigurować tworzenie kopii zapasowych wielu udziałów plików w okienku magazynu Recovery Services:

1. W [Azure Portal](https://portal.azure.com/)otwórz magazyn Recovery Services, którego chcesz użyć do skonfigurowania kopii zapasowej udziału plików.

1. W okienku **magazyn Recovery Services** wybierz pozycję **+ kopia zapasowa** z menu u góry.

   ![Magazyn usługi Recovery Services](./media/backup-afs/recovery-services-vault.png)

    1. W okienku **cel kopii zapasowej** ustaw, **gdzie działa Twoje obciążenie?** na **platformę Azure** , wybierając opcję **Azure** z listy rozwijanej.

          ![Wybierz platformę Azure jako obciążenie](./media/backup-afs/backup-goal.png)

    2. W **czym chcesz utworzyć kopię zapasową?** wybierz pozycję **udział plików platformy Azure** z listy rozwijanej.

          ![Wybieranie udziału plików platformy Azure](./media/backup-afs/select-azure-file-share.png)

    3. Wybierz pozycję **kopia zapasowa** , aby zarejestrować rozszerzenie udziału plików platformy Azure w magazynie.

          ![Wybierz pozycję Kopia zapasowa, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-afs/register-extension.png)

1. Po wybraniu opcji **kopia zapasowa** zostanie otwarte okienko **kopia zapasowa** . Aby wybrać konto magazynu obsługujące udział plików, który ma być chroniony, zaznacz pole **wyboru wybierz** tekst linku poniżej pola tekstowego **konto magazynu** .

   ![Wybieranie linku wybierz](./media/backup-afs/choose-select-link.png)

1. Po prawej stronie zostanie otwarte **okienko Wybieranie konta magazynu** z listą odnalezionych obsługiwanych kont magazynu. Są one skojarzone z tym magazynem lub znajdują się w tym samym regionie co magazyn, ale nie są jeszcze skojarzone z żadnym magazynem Recovery Services.

1. Z listy odnalezionych kont magazynu wybierz konto, a następnie wybierz **przycisk OK**.

   ![Wybierz z odnalezionych kont magazynu](./media/backup-afs/select-discovered-storage-account.png)

1. Następnym krokiem jest wybranie udziałów plików, dla których ma zostać utworzona kopia zapasowa. Wybierz przycisk **Dodaj** w sekcji **udziałów plików do kopii zapasowej** .

   ![Wybierz udziały plików do utworzenia kopii zapasowej](./media/backup-afs/select-file-shares-to-back-up.png)

1. Po prawej stronie zostanie otwarte okienko kontekstowe **udziały plików** . Platforma Azure przeszukuje konto magazynu pod kątem udziałów plików, których kopię zapasową można utworzyć. Jeśli niedawno dodano udziały plików i nie są one widoczne na liście, poczekaj chwilę na wyświetlenie udziałów plików.

1. Z listy **Wybierz udziały plików** wybierz co najmniej jeden udział plików, dla którego chcesz utworzyć kopię zapasową. Wybierz przycisk **OK**.

   ![Wybierz udziały plików](./media/backup-afs/select-file-shares.png)

1. Aby wybrać zasady tworzenia kopii zapasowych dla udziału plików, dostępne są trzy opcje:

   * Wybierz zasady domyślne.<br>
   Ta opcja umożliwia włączenie codziennej kopii zapasowej, która będzie przechowywana przez 30 dni. Jeśli nie masz istniejących zasad tworzenia kopii zapasowych w magazynie, zostanie otwarte okienko kopia zapasowa z domyślnymi ustawieniami zasad. Jeśli chcesz wybrać ustawienia domyślne, możesz wybrać pozycję **Włącz kopię zapasową**.

   * Tworzenie nowych zasad <br>

      1. Aby utworzyć nowe zasady tworzenia kopii zapasowych dla udziału plików, wybierz link tekst poniżej listy rozwijanej w sekcji **zasady tworzenia kopii zapasowych** .<br>

         ![Utwórz nowe zasady](./media/backup-afs/create-new-policy.png)

      1. Po prawej stronie zostanie otwarte okienko kontekstowe **zasad tworzenia kopii zapasowej** . W polu tekstowym Określ nazwę zasad i wybierz okres przechowywania zgodnie z wymaganiami. Domyślnie włączona jest tylko opcja przechowywania dziennego. Jeśli chcesz mieć tygodniowe, miesięczne lub roczne przechowywanie, zaznacz odpowiednie pole wyboru i podaj żądaną wartość przechowywania.

      1. Po określeniu wartości przechowywania i prawidłowej nazwy zasad wybierz pozycję **OK**.<br>

         ![Podaj nazwę zasad i wartości przechowywania](./media/backup-afs/policy-name.png)

   * Wybierz jedną z istniejących zasad tworzenia kopii zapasowych <br>

      Aby wybrać jedną z istniejących zasad tworzenia kopii zapasowych w celu skonfigurowania ochrony, wybierz odpowiednie zasady z listy rozwijanej **zasady tworzenia kopii zapasowej** .<br>

      ![Wybieranie istniejących zasad](./media/backup-afs/choose-existing-policy.png)

1. Wybierz pozycję **Włącz kopię zapasową** , aby rozpocząć ochronę udziału plików.

   ![Wybierz pozycję Włącz kopię zapasową](./media/backup-afs/enable-backup.png)

Po ustawieniu zasad tworzenia kopii zapasowej migawka udziałów plików jest wykonywana w zaplanowanym czasie. Punkt odzyskiwania jest również zachowywany przez wybrany okres.

>[!NOTE]
>Azure Backup teraz obsługuje zasady z codziennym/tygodniowym/miesięcznym przechowywaniem kopii zapasowych udziałów plików platformy Azure.

## <a name="configure-backup-from-the-file-share-pane"></a>Konfiguruj kopię zapasową z okienka udostępniania plików

Poniższe kroki wyjaśniają, jak można skonfigurować kopię zapasową poszczególnych udziałów plików z odpowiedniego okienka udostępniania plików:

1. W [Azure Portal](https://portal.azure.com/)Otwórz konto magazynu hostujący udział plików, dla którego chcesz utworzyć kopię zapasową.

1. Na koncie magazynu wybierz kafelek z etykietą **udziały plików**. Możesz również przejść do **udziałów plików** za pośrednictwem spisu treści dla konta magazynu.

   ![Konto magazynu](./media/backup-afs/storage-account.png)

1. Na liście udział plików powinny być widoczne wszystkie udziały plików znajdujące się na koncie magazynu. Wybierz udział plików, dla którego chcesz utworzyć kopię zapasową.

   ![Lista udziałów plików](./media/backup-afs/file-shares-list.png)

1. Wybierz pozycję **kopia zapasowa** w sekcji **operacje** w okienku udział plików. Okienko **Konfiguruj kopię zapasową** zostanie załadowane po prawej stronie.

   ![Konfigurowanie okienka kopia zapasowa](./media/backup-afs/configure-backup.png)

1. Aby wybrać magazyn Recovery Services, wykonaj jedną z następujących czynności:

    * Jeśli masz już magazyn, wybierz przycisk radiowy **Wybierz istniejący** Recovery Services magazyn, a następnie wybierz jeden z istniejących magazynów z menu rozwijanego **Nazwa magazynu** .

       ![Wybierz istniejący magazyn](./media/backup-afs/select-existing-vault.png)

    * Jeśli nie masz magazynu, wybierz przycisk radiowy **Utwórz nowy** magazyn Recovery Services. Określ nazwę magazynu. Jest on tworzony w tym samym regionie co udział plików. Domyślnie magazyn jest tworzony w tej samej grupie zasobów co udział plików. Jeśli chcesz wybrać inną grupę zasobów, wybierz pozycję **Utwórz nowe** łącze poniżej listy rozwijanej **Typ zasobu** i określ nazwę grupy zasobów. Kliknij przycisk **OK**, aby kontynuować.

       ![Tworzenie nowego magazynu](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Jeśli konto magazynu jest zarejestrowane w magazynie lub istnieje kilka chronionych udziałów na koncie magazynu hostującym udział plików, który próbujesz chronić, nazwa magazynu Recovery Services zostanie wstępnie wypełniona i nie będzie można jej edytować [więcej informacji](backup-azure-files-faq.md#why-cant-i-change-the-vault-to-configure-backup-for-the-file-share).

1. Dla opcji wybór **zasad tworzenia kopii zapasowej** wykonaj jedną z następujących czynności:

    * Pozostaw zasady domyślne. Spowoduje to zaplanowanie codziennych kopii zapasowych z zachowaniem 30 dni.

    * Wybierz istniejące zasady tworzenia kopii zapasowych, jeśli je masz, z menu rozwijanego **zasady tworzenia kopii zapasowych** .

       ![Wybieranie zasad kopii zapasowych](./media/backup-afs/choose-backup-policy.png)

    * Utwórz nowe zasady za pomocą dziennego/cotygodniowego/miesięcznego okresu przechowywania zgodnie z wymaganiami.  

         1. Zaznacz tekst linku **Utwórz nowe zasady** .

         2. Po prawej stronie zostanie otwarte okienko kontekstowe **zasad tworzenia kopii zapasowej** . W polu tekstowym Określ nazwę zasad i wybierz okres przechowywania zgodnie z wymaganiami. Domyślnie włączona jest tylko opcja przechowywania dziennego. Jeśli chcesz mieć tygodniowe, miesięczne lub roczne przechowywanie, zaznacz odpowiednie pole wyboru i podaj żądaną wartość przechowywania.

         3. Po określeniu wartości przechowywania i prawidłowej nazwy zasad wybierz pozycję **OK**.

            ![Utwórz nowe zasady kopii zapasowej](./media/backup-afs/create-new-backup-policy.png)

1. Wybierz pozycję **Włącz kopię zapasową** , aby rozpocząć ochronę udziału plików.

   ![Wybierz pozycję Włącz kopię zapasową](./media/backup-afs/select-enable-backup.png)

1. Możesz śledzić postęp konfiguracji w powiadomieniach portalu lub monitorować zadania tworzenia kopii zapasowej w magazynie używanym do ochrony udziału plików.

   ![Powiadomienia portalu](./media/backup-afs/portal-notifications.png)

1. Po zakończeniu operacji Konfiguruj kopię zapasową wybierz pozycję **kopia zapasowa** w sekcji **operacje** w okienku udział plików. Okienko kontekstu z listą **podstawowych elementów magazynu** zostanie załadowane po prawej stronie. Z tego miejsca można wyzwolić operacje tworzenia kopii zapasowych na żądanie i przywracania.

   ![Podstawowe informacje o magazynie](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej na żądanie

Czasami może być konieczne wygenerowanie migawki kopii zapasowej lub punktu odzyskiwania poza godzinami zaplanowanymi w ramach zasad tworzenia kopii zapasowych. Typowym powodem generowania kopii zapasowej na żądanie jest prawidłowe po skonfigurowaniu zasad tworzenia kopii zapasowych. Na podstawie harmonogramu w ramach zasad tworzenia kopii zapasowych może to potrwać kilka godzin lub dni do momentu utworzenia migawki. Aby chronić dane przed rozpoczęciem obowiązywania zasad tworzenia kopii zapasowych, zainicjuj tworzenie kopii zapasowej na żądanie. Tworzenie kopii zapasowej na żądanie jest często wymagane przed wprowadzeniem planowanych zmian w udziałach plików.

### <a name="from-the-recovery-services-vault"></a>Z magazynu Recovery Services

1. Otwórz magazyn Recovery Services użyty do utworzenia kopii zapasowej udziału plików. W okienku **Przegląd** wybierz pozycję **elementy kopii zapasowej** w sekcji **chronione elementy** .

   ![Wybierz elementy kopii zapasowej](./media/backup-afs/backup-items.png)

1. Po wybraniu **pozycji elementy kopii zapasowej** w okienku **Przegląd** pojawia się nowe okienko zawierające listę wszystkich **typów zarządzania kopiami zapasowymi** .

   ![Lista typów zarządzania kopiami zapasowymi](./media/backup-afs/backup-management-types.png)

1. Z listy **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Azure Storage (Azure Files)**. Zostanie wyświetlona lista wszystkich udziałów plików i odpowiednich kont magazynu, których kopię zapasową utworzono przy użyciu tego magazynu.

   ![Elementy kopii zapasowej usługi Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. Z listy udziałów plików platformy Azure wybierz żądany udział plików. Pojawią się szczegóły **elementu kopia zapasowa** . W menu **element kopii zapasowej** wybierz pozycję **Utwórz kopię zapasową teraz**. Ponieważ to zadanie tworzenia kopii zapasowej jest na żądanie, nie ma żadnych zasad przechowywania skojarzonych z punktem odzyskiwania.

   ![Wybierz pozycję Utwórz kopię zapasową teraz](./media/backup-afs/backup-now.png)

1. Zostanie otwarte okienko **Tworzenie kopii zapasowej** . Określ ostatni dzień zachowywania punktu odzyskiwania. Możesz mieć maksymalnie 10 lat na tworzenie kopii zapasowej na żądanie.

   ![Wybierz datę przechowywania](./media/backup-afs/retention-date.png)

1. Wybierz **przycisk OK** , aby potwierdzić uruchomione zadanie tworzenia kopii zapasowej na żądanie.

1. Monitoruj powiadomienia portalu, aby śledzić Kończenie wykonywania zadania tworzenia kopii zapasowej. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu. Wybierz pozycję **zadania tworzenia kopii zapasowej**  >  **w toku**.

### <a name="from-the-file-share-pane"></a>W okienku udział plików

1. Otwórz okienko **Przegląd** udziału plików, dla którego chcesz wykonać kopię zapasową na żądanie.

1. Wybierz pozycję **kopia zapasowa** w sekcji **operacja** . Okienko kontekstu z listą **podstawowych elementów magazynu** zostanie załadowane po prawej stronie. Wybierz pozycję **Utwórz kopię zapasową teraz** , aby wykonać kopię zapasową na żądanie.

   ![Wybierz pozycję Utwórz kopię zapasową teraz](./media/backup-afs/select-backup-now.png)

1. Zostanie otwarte okienko **Tworzenie kopii zapasowej** . Określ przechowywanie punktu odzyskiwania. Możesz mieć maksymalnie 10 lat na tworzenie kopii zapasowej na żądanie.

   ![Zachowaj datę kopii zapasowej](./media/backup-afs/retain-backup-date.png)

1. Wybierz przycisk **OK**, aby potwierdzić.

>[!NOTE]
>Azure Backup blokuje konto magazynu podczas konfigurowania ochrony dowolnego udziału plików na odpowiednim koncie. Zapewnia to ochronę przed przypadkowym usunięciem konta magazynu przy użyciu kopii zapasowych udziałów plików.

## <a name="best-practices"></a>Najlepsze rozwiązania

* Nie usuwaj migawek utworzonych przez Azure Backup. Usunięcie migawek może spowodować utratę punktów odzyskiwania i/lub błędy przywracania.

* Nie usuwaj blokady wykonanej na koncie magazynu przez Azure Backup. Jeśli usuniesz blokadę, Twoje konto magazynu będzie podatne na przypadkowe usunięcie i jeśli zostanie usunięte, utracisz migawki lub kopie zapasowe.

## <a name="next-steps"></a>Następne kroki

Instrukcje:

* [Przywracanie udziałów plików platformy Azure](restore-afs.md)
* [Zarządzanie kopiami zapasowymi udziałów plików platformy Azure](manage-afs-backup.md)
