---
title: Konfigurowanie operacyjnej kopii zapasowej dla obiektów blob platformy Azure
description: Informacje na temat konfigurowania i zarządzania operacyjną kopią zapasową obiektów blob platformy Azure (w wersji zapoznawczej)
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 0dc490842389ba9286799aef5d37c1cf7c1ba64e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051076"
---
# <a name="configure-operational-backup-for-azure-blobs-in-preview"></a>Konfigurowanie operacyjnej kopii zapasowej dla obiektów blob platformy Azure (w wersji zapoznawczej)

Azure Backup pozwala łatwo skonfigurować operacyjną kopię zapasową w celu ochrony blokowych obiektów BLOB na kontach magazynu. W tym artykule wyjaśniono, jak skonfigurować operacyjną kopię zapasową na co najmniej jednym koncie magazynu przy użyciu Azure Portal. W tym artykule omówiono następujące zagadnienia:

- Rzeczy, które należy znać przed rozpoczęciem
- Tworzenie magazynu kopii zapasowych
- Przyznawanie uprawnień do magazynu kopii zapasowych na kontach magazynu, które mają być chronione
- Tworzenie zasad kopii zapasowych
- Konfigurowanie operacyjnej kopii zapasowej na co najmniej jednym koncie magazynu
- Wpływ na konta magazynu kopii zapasowej

## <a name="before-you-start"></a>Przed rozpoczęciem

- Operacyjna kopia zapasowa obiektów BLOB to lokalne rozwiązanie do tworzenia kopii zapasowej, które przechowuje dane przez określony czas na źródłowym koncie magazynu. To rozwiązanie nie utrzymuje dodatkowej kopii danych w magazynie.
- To rozwiązanie pozwala zachować dane do przywrócenia przez maksymalnie 360 dni. Długotrwałe czasy przechowywania mogą jednak prowadzić do dłuższego czasu podczas operacji przywracania.
- Rozwiązanie może służyć do wykonywania przywracania tylko do konta magazynu źródłowego i może spowodować zastąpienie danych.
- W przypadku usunięcia kontenera z konta magazynu przez wywołanie operacji usuwania kontenera nie można przywrócić tego kontenera przy użyciu operacji przywracania. Zamiast usuwać cały kontener, Usuń pojedyncze obiekty blob, jeśli chcesz je później przywrócić. Ponadto firma Microsoft zaleca włączenie usuwania nietrwałego dla kontenerów, oprócz operacyjnych kopii zapasowych, w celu ochrony przed przypadkowym usunięciem kontenerów.
- Zapoznaj się z [matrycą pomocy technicznej](blob-backup-support-matrix.md) , aby dowiedzieć się więcej o obsługiwanych scenariuszach, ograniczeniach i dostępności.

## <a name="create-a-backup-vault"></a>Tworzenie magazynu kopii zapasowych

[Magazyn kopii zapasowych](backup-vault-overview.md) to jednostka zarządzania, która przechowuje punkty odzyskiwania utworzone w czasie i udostępnia interfejs do wykonywania operacji związanych z tworzeniem kopii zapasowych. Obejmują one wykonywanie kopii zapasowych na żądanie, wykonywanie operacji przywracania i tworzenie zasad kopii zapasowych. Mimo że operacyjna kopia zapasowa obiektów BLOB jest lokalną kopią zapasową i nie zapisuje danych w magazynie, magazyn jest wymagany dla różnych operacji zarządzania.

>[!NOTE]
>Magazyn kopii zapasowych jest nowym zasobem, który jest używany do tworzenia kopii zapasowych nowych obsługiwanych obciążeń i jest inny niż istniejący magazyn Recovery Services.

Aby uzyskać instrukcje dotyczące sposobu tworzenia magazynu kopii zapasowych, zobacz [dokumentację magazynu kopii zapasowych](backup-vault-overview.md#create-a-backup-vault).

## <a name="grant-permissions-to-the-backup-vault-on-storage-accounts"></a>Przyznawanie uprawnień do magazynu kopii zapasowych na kontach magazynu

Operacyjna kopia zapasowa chroni również konto magazynu (zawierające obiekty blob do ochrony) przed przypadkowym usunięciem przez zastosowanie blokady Delete należącej do kopii zapasowej. Wymaga to, aby Magazyn kopii zapasowych miał pewne uprawnienia na kontach magazynu, które muszą być chronione. Aby wygoda była używana, te uprawnienia zostały skonsolidowane w ramach roli współautor kopii zapasowej konta magazynu. Postępuj zgodnie z poniższymi instrukcjami dla kont magazynu, które muszą być chronione:

1. Na koncie magazynu, które ma być chronione, przejdź do **karty Access Control (IAM)** w okienku nawigacji po lewej stronie.
1. Wybierz pozycję **Dodaj przypisania ról** , aby przypisać wymaganą rolę.

    ![Dodawanie przypisań ról](./media/blob-backup-configure-manage/add-role-assignments.png)

1. W okienku Dodaj przypisanie roli:

    1. W obszarze **rola** wybierz pozycję **współautor kopii zapasowej konta magazynu**.
    1. W obszarze **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi**.
    1. Wpisz **nazwę magazynu kopii zapasowych** , który chcesz chronić obiekty blob na tym koncie magazynu, i wybierz te same wartości z wyników wyszukiwania.
    1. Po zakończeniu wybierz pozycję **Zapisz**.

        ![Opcje przypisywania ról](./media/blob-backup-configure-manage/role-assignment-options.png)

        >[!NOTE]
        >Aby przypisanie roli zaczęło obowiązywać, poczekaj maksymalnie 10 minut.

## <a name="create-a-backup-policy"></a>Tworzenie zasad kopii zapasowych

Zasady tworzenia kopii zapasowych zwykle decydują o przechowywaniu i harmonogramie kopii zapasowych. Ponieważ operacyjna kopia zapasowa obiektów BLOB ma charakter ciągły, nie jest potrzebny harmonogram wykonywania kopii zapasowych. Zasady są zasadniczo konieczne do określenia okresu przechowywania. Zasady tworzenia kopii zapasowych można skonfigurować i ponownie użyć w celu skonfigurowania kopii zapasowej wielu kont magazynu w magazynie.

Poniżej przedstawiono procedurę tworzenia zasad tworzenia kopii zapasowych dla operacyjnej kopii zapasowej obiektów blob:

1. W magazynie kopii zapasowych przejdź do **zasad tworzenia kopii zapasowych** i wybierz pozycję **+ Dodaj** , aby rozpocząć tworzenie zasad tworzenia kopii zapasowych.

    ![Zasady tworzenia kopii zapasowych](./media/blob-backup-configure-manage/backup-policies.png)

1. Na karcie **podstawy** Podaj nazwę zasad tworzenia kopii zapasowych i wybierz **obiekty blob platformy Azure** jako typ źródła danych. Możesz również wyświetlić szczegóły wybranego magazynu.

    ![Tworzenie zasad kopii zapasowych](./media/blob-backup-configure-manage/create-backup-policy.png)

    >[!NOTE]
    >Mimo że zostanie wyświetlona **nadmiarowość magazynu kopii zapasowych** w magazynie, nadmiarowość nie jest w rzeczywistości stosowana do operacyjnej kopii zapasowej obiektów blob, ponieważ kopia zapasowa ma charakter lokalny i nie przechowuje żadnych danych w magazynie kopii zapasowych. Magazyn kopii zapasowych to jednostka zarządzania ułatwiająca zarządzanie ochroną blokowych obiektów BLOB na kontach magazynu.

1. Na karcie **zasady tworzenia kopii zapasowych** można określić szczegóły przechowywania. Zobaczysz już regułę przechowywania o nazwie **default** z okresem przechowywania wynoszącym 30 dni. Jeśli chcesz edytować czas przechowywania, użyj ikony **Edytuj regułę przechowywania** , aby edytować i określić czas trwania, dla którego dane mają być zachowywane. Możesz określić przechowywanie do 360 dni.

    ![Karta zasady kopii zapasowej](./media/blob-backup-configure-manage/backup-policy-tab.png)

    >[!NOTE]
    >Przywrócenie przez długi czas trwania może prowadzić do ukończenia operacji przywracania. Ponadto czas potrzebny do przywrócenia zestawu danych jest oparty na liczbie operacji zapisu i usuwania dokonanych w okresie przywracania. Na przykład konto z 1 000 000 obiektami o 3 000 obiektach dodane dziennie, a 1 000 obiektów, które zostały usunięte dziennie, będzie wymagało około dwóch godzin, aby przywrócić je do punktu 30 dni w przeszłości. Okres przechowywania i przywrócenie ponad 90 dni w przeszłości nie będą zalecane dla konta z tą szybkością zmiany.

1. W okienku **Recenzja + tworzenie** Sprawdź wszystkie szczegóły zasad i wybierz pozycję **Utwórz** po zakończeniu, aby zakończyć tworzenie zasad. Powiadomienie zostanie potwierdzone po utworzeniu zasad tworzenia kopii zapasowych i będzie gotowe do użycia.

    ![Przeglądanie i tworzenie zasad](./media/blob-backup-configure-manage/review-create.png)

## <a name="configure-backup"></a>Konfigurowanie kopii zapasowych

Tworzenie kopii zapasowych obiektów BLOB jest konfigurowane na poziomie konta magazynu. Wszystkie obiekty blob na koncie magazynu są chronione za pomocą kopii zapasowej.

Aby rozpocząć konfigurowanie kopii zapasowej:

1. Wyszukaj **centrum kopii zapasowych** na pasku wyszukiwania.
1. Przejdź do **omówienia**  ->  **+ kopia zapasowa**.

    ![Omówienie centrum kopii zapasowych](./media/blob-backup-configure-manage/backup-center-overview.png)

1. Na karcie **Inicjowanie: Konfigurowanie kopii zapasowej** wybierz pozycję **obiekty blob platformy Azure (Azure Storage)** jako typ źródła danych.

    ![Inicjowanie: Konfigurowanie karty Kopia zapasowa](./media/blob-backup-configure-manage/initiate-configure-backup.png)

1. Na karcie **podstawowe** Określ **obiekty blob platformy Azure (Azure Storage)** jako typ **źródła danych** i wybierz magazyn kopii zapasowych, do którego chcesz skojarzyć konta magazynu. Szczegóły wybranego magazynu można wyświetlić w okienku.

    ![Karta Podstawowe](./media/blob-backup-configure-manage/basics-tab.png)

1. Następnie wybierz zasady tworzenia kopii zapasowej, które mają być używane do określania przechowywania. Szczegóły wybranych zasad można wyświetlić w dolnej części ekranu. W kolumnie magazyn danych operacyjnych jest widoczne przechowywanie zdefiniowane w zasadach. "Operacyjna" oznacza, że dane są przechowywane lokalnie na źródłowym koncie magazynu.

    ![Wybieranie zasad kopii zapasowych](./media/blob-backup-configure-manage/choose-backup-policy.png)

    Można również utworzyć nowe zasady tworzenia kopii zapasowych. W tym celu wybierz pozycję **Utwórz nową** i wykonaj poniższe czynności:

    1. Podaj nazwę zasad, które chcesz utworzyć. Upewnij się, że inne pola zawierają poprawny typ źródła danych i nazwę magazynu.
    1. Na karcie **zasady tworzenia kopii zapasowych** wybierz ikonę Edytuj regułę przechowywania, aby edytować i określić czas, przez który mają być zachowywane dane. Możesz określić przechowywanie do 360 dni. Przywrócenie przez długi czas trwania może prowadzić do ukończenia operacji przywracania.

        ![Utwórz nowe zasady kopii zapasowej](./media/blob-backup-configure-manage/new-backup-policy.png)

    1. Po wykonaniu tych czynności wybierz pozycję **Przegląd + Utwórz** , aby utworzyć zasady tworzenia kopii zapasowych.

1. Następnie należy wybrać konta magazynu, dla których chcesz skonfigurować ochronę obiektów BLOB. Możesz wybrać wiele kont magazynu jednocześnie i wybrać **pozycję Wybierz**.

    Należy jednak upewnić się, że wybrany magazyn ma uprawnienia wymagane do skonfigurowania kopii zapasowych na kontach magazynu, jak opisano powyżej w [przydzieleniu uprawnień do magazynu kopii zapasowych na kontach magazynu](#grant-permissions-to-the-backup-vault-on-storage-accounts).

    ![Wybierz zasoby do utworzenia kopii zapasowej](./media/blob-backup-configure-manage/select-resources.png)

    Kopia zapasowa sprawdza, czy magazyn ma wystarczające uprawnienia, aby umożliwić Konfigurowanie kopii zapasowej na wybranych kontach magazynu.

    ![Kopia zapasowa sprawdza poprawność uprawnień](./media/blob-backup-configure-manage/validate-permissions.png)

    Jeśli walidacja powoduje błędy (podobnie jak w przypadku jednego z kont magazynu na zrzucie ekranu), przejdź do wybranych kont magazynu i przypisz odpowiednie role zgodnie z opisem w [tym miejscu](#grant-permissions-to-the-backup-vault-on-storage-accounts)i wybierz pozycję ponownie **Weryfikuj**. Nowe przypisanie roli może zająć do 10 minut.

1. Po pomyślnej weryfikacji dla wszystkich wybranych kont magazynu Kontynuuj **przeglądanie i Konfigurowanie** , aby skonfigurować kopię zapasową. Zobaczysz powiadomienie informujące o stanie konfigurowania ochrony i jego zakończeniu.

## <a name="effects-on-backed-up-storage-accounts"></a>Wpływ na konta magazynu z kopią zapasową

Po skonfigurowaniu kopii zapasowej, zmiany dotyczące blokowych obiektów BLOB na kontach magazynu są śledzone, a dane są zachowywane zgodnie z zasadami tworzenia kopii zapasowych. Należy zauważyć następujące zmiany na kontach magazynu, dla których skonfigurowano tworzenie kopii zapasowej:

- Na koncie magazynu są włączone poniższe możliwości. Można je wyświetlić na karcie **Ochrona danych** na koncie magazynu.
  - Przywracanie do punktu w czasie dla kontenerów: przechowywanie określone w zasadach tworzenia kopii zapasowych
  - Usuwanie nietrwałe dla obiektów blob: przechowywanie określone w zasadach tworzenia kopii zapasowych + 5 dni
  - Przechowywanie wersji dla obiektów BLOB
  - Źródło zmian obiektu BLOB

  Jeśli na koncie magazynu skonfigurowanym do tworzenia kopii zapasowych włączono już  **przywracanie do punktu w czasie dla kontenerów** lub **usuwanie nietrwałe dla obiektów BLOB** (przed skonfigurowaniem tworzenia kopii zapasowej), kopia zapasowa gwarantuje, że przechowywanie jest co najmniej zgodnie z definicją w zasadach tworzenia kopii zapasowych. W związku z tym dla każdej właściwości:

  - Jeśli przechowywanie w zasadach tworzenia kopii zapasowych jest większe niż przechowywanie pierwotnie obecne na koncie magazynu: przechowywanie na koncie magazynu jest modyfikowane zgodnie z zasadami tworzenia kopii zapasowych.
  - Jeśli przechowywanie w zasadach tworzenia kopii zapasowych jest mniejsze niż przechowywanie pierwotnie obecne na koncie magazynu: przechowywanie na koncie magazynu pozostanie bez zmian w pierwotnym ustawionym czasie.

  ![Karta Ochrona danych](./media/blob-backup-configure-manage/data-protection.png)

- Na chronionym koncie magazynu jest stosowana **Blokada usuwania** . Blokada jest przeznaczona do ochrony przed przypadkowym usunięciem konta magazynu. Można je wyświetlić w obszarze **blokady konta magazynu**  >  .

    ![Usuwanie blokad](./media/blob-backup-configure-manage/delete-lock.png)

## <a name="manage-operational-backup"></a>Zarządzanie operacyjną kopią zapasową

Aby zarządzać wszystkimi kopiami zapasowymi, można użyć centrum kopii zapasowych jako pojedynczego okienka Glass. W przypadku operacyjnych kopii zapasowych dla obiektów blob platformy Azure można użyć centrum kopii zapasowych, aby wykonać następujące czynności:

- Jak widać powyżej, można użyć go do tworzenia magazynów i zasad tworzenia kopii zapasowych. Możesz również wyświetlić wszystkie magazyny i zasady w ramach wybranych subskrypcji.
- Centrum kopii zapasowych zapewnia łatwy sposób monitorowania stanu ochrony chronionych kont magazynu oraz kont magazynu, dla których kopia zapasowa nie jest obecnie skonfigurowana.
- Kopię zapasową można skonfigurować dla każdego konta magazynu za pomocą przycisku **+ kopia zapasowa** .
- Można inicjować Przywracanie za pomocą przycisku **Przywróć** i śledzić przywracanie przy użyciu **zadań tworzenia kopii zapasowej**. Aby uzyskać więcej informacji dotyczących wykonywania operacji przywracania, zobacz [przywracanie obiektów blob platformy Azure](blob-backup-support-matrix.md).
- Analizuj użycie kopii zapasowej za pomocą raportów kopii zapasowych.

    ![Centrum kopii zapasowych](./media/blob-backup-configure-manage/backup-center.png)

Aby uzyskać więcej informacji, zobacz [Omówienie centrum kopii zapasowych (wersja zapoznawcza)](backup-center-overview.md).

## <a name="next-steps"></a>Następne kroki

- [Przywracanie obiektów blob platformy Azure](blob-restore.md)
