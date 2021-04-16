---
title: Kopii zapasowej udziałów plików platformy Azure w Azure Portal
description: Dowiedz się, jak za pomocą Azure Portal kopii zapasowej udziałów plików platformy Azure w magazynie usługi Recovery Services
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: e7f44a71388468be432bdfcb0eb2bf67c0fcc8ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519939"
---
# <a name="back-up-azure-file-shares"></a>Tworzenie kopii zapasowej udziałów plików platformy Azure

W tym artykule opisano sposób kopii zapasowej [udziałów plików platformy Azure](../storage/files/storage-files-introduction.md) z Azure Portal.

Ten artykuł obejmuje następujące zagadnienia:

* Utwórz magazyn usługi Recovery Services.
* Konfigurowanie kopii zapasowej z magazynu usługi Recovery Services
* Konfigurowanie kopii zapasowej z okienka udziału plików
* Uruchomianie zadania tworzenia kopii zapasowej na żądanie w celu tworzenia punktu przywracania

## <a name="prerequisites"></a>Wymagania wstępne

* [Dowiedz się](azure-file-share-backup-overview.md) więcej o rozwiązaniu do tworzenia kopii zapasowych opartym na migawkach udziału plików platformy Azure.
* Upewnij się, że udział plików znajduje się w jednym z [obsługiwanych typów kont magazynu.](azure-file-share-support-matrix.md)
* Zidentyfikuj lub utwórz magazyn [usługi Recovery Services](#create-a-recovery-services-vault) w tym samym regionie co konto magazynu, które hostuje udział plików.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Konfigurowanie kopii zapasowej z magazynu usługi Recovery Services

W poniższych krokach wyjaśniono, jak skonfigurować kopię zapasową dla wielu udziałów plików w okienku magazynu usługi Recovery Services:

1. W [Azure Portal](https://portal.azure.com/)otwórz magazyn usługi Recovery Services, którego chcesz użyć do konfigurowania kopii zapasowej udziału plików.

1. W **okienku Magazyn usługi Recovery Services** wybierz pozycję **+Kopia** zapasowa z menu u góry.

   ![Magazyn usługi Recovery Services](./media/backup-afs/recovery-services-vault.png)

    1. W **okienku Cel kopii** zapasowej ustaw pozycję Gdzie działa Twoje **obciążenie?** na **platformę Azure,** wybierając opcję **Azure** z listy rozwijanej.

          ![Wybieranie platformy Azure jako obciążenia](./media/backup-afs/backup-goal.png)

    2. W **witrynie What do you want to back up?**(Co chcesz wykonać? ) wybierz pozycję **Azure File Share (Udział plików** platformy Azure) z listy rozwijanej.

          ![Wybieranie usługi Azure FileShare](./media/backup-afs/select-azure-file-share.png)

    3. Wybierz **pozycję Kopia** zapasowa, aby zarejestrować rozszerzenie udziału plików platformy Azure w magazynie.

          ![Wybierz pozycję Kopia zapasowa, aby skojarzyć udział plików platformy Azure z magazynem](./media/backup-afs/register-extension.png)

1. Po wybraniu opcji **Kopia zapasowa** zostanie **otwarte** okienko Kopia zapasowa. Aby wybrać konto magazynu hostowania udziału plików, który chcesz chronić, wybierz **tekst** linku Wybierz poniżej **pola tekstowego Konto** magazynu.

   ![Wybierz link Wybierz](./media/backup-afs/choose-select-link.png)

1. Po **prawej stronie zostanie** otwarte okienko Wybieranie konta magazynu z listą odnalezionych obsługiwanych kont magazynu. Są one skojarzone z tym magazynem lub obecne w tym samym regionie co magazyn, ale jeszcze nie są skojarzone z żadnym magazynem usługi Recovery Services.

1. Z listy odnalezionych kont magazynu wybierz konto, a następnie wybierz przycisk **OK.**

   ![Wybieranie z odnalezionych kont magazynu](./media/backup-afs/select-discovered-storage-account.png)

1. Następnym krokiem jest wybranie udziałów plików, których kopię zapasową chcesz wrócić. Wybierz przycisk **Dodaj** w sekcji **Udziały plików do kopii zapasowej.**

   ![Wybieranie udziałów plików do kopii zapasowej](./media/backup-afs/select-file-shares-to-back-up.png)

1. Po **prawej stronie** zostanie otwarte okienko kontekstowe Wybieranie udziałów plików. Platforma Azure wyszukuje udziały plików, których kopię zapasową można utworzyć na koncie magazynu. Jeśli niedawno dodano udziały plików i nie są one widoczne na liście, pośwęć trochę czasu na ich wyświetlanie.

1. Z listy **Wybierz udziały** plików wybierz co najmniej jeden udział plików, którego kopię zapasową chcesz wrócić. Wybierz przycisk **OK**.

   ![Wybieranie udziałów plików](./media/backup-afs/select-file-shares.png)

1. Aby wybrać zasady tworzenia kopii zapasowej dla udziału plików, dostępne są trzy opcje:

   * Wybierz zasady domyślne.<br>
   Ta opcja umożliwia włączenie codziennej kopii zapasowej, która będzie zachowywana przez 30 dni. Jeśli nie masz istniejących zasad tworzenia kopii zapasowych w magazynie, zostanie otwarte okienko kopii zapasowej z domyślnymi ustawieniami zasad. Jeśli chcesz wybrać ustawienia domyślne, możesz bezpośrednio wybrać pozycję **Włącz kopię zapasową.**

   * Tworzenie nowych zasad <br>

      1. Aby utworzyć nowe zasady tworzenia kopii zapasowych dla udziału plików, wybierz tekst linku poniżej listy rozwijanej w sekcji **Zasady kopii zapasowych.**<br>

         ![Tworzenie nowych zasad](./media/backup-afs/create-new-policy.png)

      1. Po **prawej stronie** zostanie otwarte okienko kontekstowe Zasady tworzenia kopii zapasowych. Określ nazwę zasad w polu tekstowym i wybierz okres przechowywania zgodnie z wymaganiami. Domyślnie włączona jest tylko opcja dziennego przechowywania. Jeśli chcesz mieć przechowywanie co tydzień, co miesiąc lub co rok, zaznacz odpowiednie pole wyboru i podaj żądaną wartość przechowywania.

      1. Po określeniu wartości przechowywania i prawidłowej nazwy zasad wybierz przycisk **OK.**<br>

         ![Nadaj nazwę zasad i wartości przechowywania](./media/backup-afs/policy-name.png)

   * Wybierz jedną z istniejących zasad tworzenia kopii zapasowych <br>

      Aby wybrać jedną z istniejących zasad tworzenia kopii zapasowych na  potrzeby konfigurowania ochrony, wybierz odpowiednie zasady z listy rozwijanej Zasady kopii zapasowych.<br>

      ![Wybieranie istniejących zasad](./media/backup-afs/choose-existing-policy.png)

1. Wybierz **pozycję Włącz kopię** zapasową, aby rozpocząć ochronę udziału plików.

   ![Wybierz pozycję Włącz tworzenie kopii zapasowej](./media/backup-afs/enable-backup.png)

Po skonfigurowaniu zasad tworzenia kopii zapasowej migawka udziałów plików jest tworzyć w zaplanowanym czasie. Punkt odzyskiwania jest również zachowywany dla wybranego okresu.

>[!NOTE]
>Azure Backup obsługuje teraz zasady z przechowywaniem kopii zapasowej udziału plików platformy Azure codziennie/co tydzień/co miesiąc/co rok.

## <a name="configure-backup-from-the-file-share-pane"></a>Konfigurowanie kopii zapasowej z okienka udziału plików

W poniższych krokach wyjaśniono, jak skonfigurować kopię zapasową dla poszczególnych udziałów plików z odpowiedniego okienka udziału plików:

1. W [Azure Portal](https://portal.azure.com/)otwórz konto magazynu hostowania udziału plików, którego kopię zapasową chcesz wrócić.

1. Na koncie magazynu wybierz kafelek z etykietą **Udziały plików.** Możesz również przejść do **folderu Udziały** plików za pośrednictwem spisu treści dla konta magazynu.

   ![Konto magazynu](./media/backup-afs/storage-account.png)

1. Na liście udziałów plików powinny zostać wyświetlony wszystkie udziały plików obecne na koncie magazynu. Wybierz udział plików, którego kopię zapasową chcesz wrócić.

   ![Lista udziałów plików](./media/backup-afs/file-shares-list.png)

1. Wybierz **pozycję Kopia** zapasowa w sekcji Operacje okienka udziału plików.  Okienko **Konfigurowanie kopii** zapasowej zostanie załadowane po prawej stronie.

   ![Konfigurowanie okienka kopii zapasowej](./media/backup-afs/configure-backup.png)

1. W przypadku wyboru magazynu usługi Recovery Services wykonaj jedną z następujących czynności:

    * Jeśli masz już magazyn, wybierz przycisk radiowy **Wybierz** istniejący magazyn usługi Recovery Services, a następnie wybierz jeden z istniejących magazynów **z** menu rozwijanego Nazwa magazynu.

       ![Wybieranie istniejącego magazynu](./media/backup-afs/select-existing-vault.png)

    * Jeśli nie masz magazynu, wybierz przycisk radiowy Utwórz nowy magazyn usługi **Recovery** Services. Określ nazwę magazynu. Jest on tworzony w tym samym regionie co udział plików. Domyślnie magazyn jest tworzony w tej samej grupie zasobów co udział plików. Jeśli chcesz wybrać inną grupę zasobów, wybierz **link** Utwórz nową poniżej listy rozwijanej **Typ** zasobu i określ nazwę grupy zasobów. Kliknij przycisk **OK**, aby kontynuować.

       ![Tworzenie nowego magazynu](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Jeśli konto magazynu jest zarejestrowane w magazynie lub istnieje kilka chronionych udziałów na koncie magazynu hostującym udział plików, który chcesz chronić, nazwa magazynu usługi Recovery Services zostanie wstępnie wypełniona i nie będzie można go edytować Dowiedz się więcej [tutaj.](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-)

1. W przypadku **zaznaczenia opcji Zasady** kopii zapasowej wykonaj jedną z następujących czynności:

    * Pozostaw zasady domyślne. Zaplanuje tworzenie codziennych kopii zapasowych z okresem przechowywania 30 dni.

    * Wybierz istniejące zasady tworzenia kopii zapasowych,  jeśli je masz, z menu rozwijanego Zasady kopii zapasowych.

       ![Wybieranie zasad kopii zapasowych](./media/backup-afs/choose-backup-policy.png)

    * Utwórz nowe zasady z przechowywaniem codziennie/co tydzień/co miesiąc/co rok zgodnie z wymaganiami.  

         1. Wybierz tekst **linku Utwórz nowe** zasady.

         2. Po **prawej stronie** zostanie otwarte okienko kontekstowe Zasady tworzenia kopii zapasowych. Określ nazwę zasad w polu tekstowym i wybierz okres przechowywania zgodnie z wymaganiami. Domyślnie włączona jest tylko opcja dziennego przechowywania. Jeśli chcesz mieć przechowywanie co tydzień, co miesiąc lub co rok, zaznacz odpowiednie pole wyboru i podaj żądaną wartość przechowywania.

         3. Po określeniu wartości przechowywania i prawidłowej nazwy zasad wybierz przycisk **OK.**

            ![Tworzenie nowych zasad kopii zapasowych](./media/backup-afs/create-new-backup-policy.png)

1. Wybierz **pozycję Włącz kopię** zapasową, aby rozpocząć ochronę udziału plików.

   ![Wybierz pozycję Włącz tworzenie kopii zapasowej](./media/backup-afs/select-enable-backup.png)

1. Postęp konfiguracji można śledzić w powiadomieniach portalu lub monitorując zadania tworzenia kopii zapasowej w magazynie, za pomocą których chronisz udział plików.

   ![Powiadomienia portalu](./media/backup-afs/portal-notifications.png)

1. Po zakończeniu operacji konfigurowania kopii zapasowej wybierz pozycję **Kopia** zapasowa w sekcji **Operacje** okienka udziału plików. Po prawej stronie zostanie załadowana lista okienka kontekstowego **Vault Essentials.** W tym miejscu można wyzwalać operacje tworzenia kopii zapasowej i przywracania na żądanie.

   ![Podstawowe informacje o magazynie](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Uruchamianie zadania tworzenia kopii zapasowej na żądanie

Czasami może być konieczne wygenerowanie migawki kopii zapasowej lub punktu odzyskiwania poza godzinami zaplanowanym w zasadach tworzenia kopii zapasowej. Częstą przyczyną generowania kopii zapasowej na żądanie jest skonfigurowanie zasad tworzenia kopii zapasowych. Zgodnie z harmonogramem w zasadach tworzenia kopii zapasowych tworzenie migawki może potrwać kilka godzin lub dni. Aby chronić dane przed rozpoczęciem obowiązywania zasad tworzenia kopii zapasowych, zainicjuj tworzenie kopii zapasowej na żądanie. Tworzenie kopii zapasowej na żądanie jest często wymagane przed zaplanowanym zmianą udziałów plików.

### <a name="from-the-recovery-services-vault"></a>Z magazynu usługi Recovery Services

1. Otwórz magazyn usługi Recovery Services, który został użyty do kopii zapasowej udziału plików. W **okienku Przegląd** wybierz pozycję **Elementy kopii zapasowej** w **sekcji Chronione** elementy.

   ![Wybieranie elementów kopii zapasowej](./media/backup-afs/backup-items.png)

1. Po wybraniu pozycji **Elementy kopii zapasowej** obok okienka **Przegląd** zostanie wyświetlone nowe okienko z listą wszystkich typów zarządzania kopiami zapasowymi. 

   ![Lista typów zarządzania kopiami zapasowymi](./media/backup-afs/backup-management-types.png)

1. Z listy **Typ zarządzania kopiami zapasowymi** wybierz **pozycję Azure Storage (Azure Files).** Zostanie wyświetlona lista wszystkich udziałów plików i odpowiednich kont magazynu, których kopię zapasową pozyskano przy użyciu tego magazynu.

   ![Elementy kopii zapasowej usługi Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. Z listy udziałów plików platformy Azure wybierz udział plików, którego potrzebujesz. Zostaną **wyświetlone szczegóły elementu** kopii zapasowej. W menu **Element kopii zapasowej** wybierz pozycję **Kopia zapasowa teraz.** Ponieważ to zadanie tworzenia kopii zapasowej jest na żądanie, z punktem odzyskiwania nie są skojarzone żadne zasady przechowywania.

   ![Wybierz pozycję Kopia zapasowa teraz](./media/backup-afs/backup-now.png)

1. Zostanie **otwarte okienko Tworzenie kopii** zapasowej teraz. Określ ostatni dzień zachowywania punktu odzyskiwania. Maksymalny okres przechowywania kopii zapasowej na żądanie wynosi 10 lat.

   ![Wybieranie daty przechowywania](./media/backup-afs/retention-date.png)

1. Wybierz **przycisk OK,** aby potwierdzić uruchamiane zadanie tworzenia kopii zapasowej na żądanie.

1. Monitoruj powiadomienia portalu, aby śledzić ukończenie zadania tworzenia kopii zapasowej. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu. Wybierz pozycję **Zadania tworzenia kopii**  >  **zapasowej W toku.**

### <a name="from-the-file-share-pane"></a>W okienku udziału plików

1. Otwórz okienko Przegląd **udziału** plików, dla którego chcesz utworzyć kopię zapasową na żądanie.

1. Wybierz **pozycję Kopia** zapasowa w **sekcji** Operacja. Po prawej stronie zostanie załadowana lista okienka kontekstowego **Vault Essentials.** Wybierz **pozycję Kopia zapasowa** teraz, aby utworzyć kopię zapasową na żądanie.

   ![Wybierz pozycję Kopia zapasowa teraz](./media/backup-afs/select-backup-now.png)

1. Zostanie **otwarte okienko Tworzenie kopii zapasowej** teraz. Określ okres przechowywania punktu odzyskiwania. Maksymalny okres przechowywania kopii zapasowej na żądanie wynosi 10 lat.

   ![Zachowywanie daty kopii zapasowej](./media/backup-afs/retain-backup-date.png)

1. Wybierz przycisk **OK**, aby potwierdzić.

>[!NOTE]
>Azure Backup blokuje konto magazynu podczas konfigurowania ochrony dla dowolnego udziału plików na odpowiednim koncie. Zapewnia to ochronę przed przypadkowym usunięciem konta magazynu z kopii zapasowej udziałów plików.

## <a name="best-practices"></a>Najlepsze rozwiązania

* Nie usuwaj migawek utworzonych przez Azure Backup. Usunięcie migawek może spowodować utratę punktów odzyskiwania i/lub błędy przywracania.

* Nie usuwaj blokady konta magazynu przez Azure Backup. Jeśli usuniesz blokadę, twoje konto magazynu będzie podatne na przypadkowe usunięcie, a jeśli zostanie usunięte, utracisz migawki lub kopie zapasowe.

## <a name="next-steps"></a>Następne kroki

Instrukcje:

* [Przywracanie udziałów plików platformy Azure](restore-afs.md)
* [Zarządzanie kopiami zapasami udziałów plików platformy Azure](manage-afs-backup.md)
