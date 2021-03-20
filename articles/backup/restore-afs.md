---
title: Przywracanie udziałów plików platformy Azure
description: Dowiedz się, w jaki sposób używać Azure Portal do przywracania całego udziału plików lub określonych plików z punktu przywracania utworzonego przez Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: 35ca0936ae6efba716adb51f43326cdd5bfa2d98
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89376778"
---
# <a name="restore-azure-file-shares"></a>Przywracanie udziałów plików platformy Azure

W tym artykule wyjaśniono, jak używać Azure Portal do przywracania całego udziału plików lub określonych plików z punktu przywracania utworzonego przez [Azure Backup](./backup-overview.md).

Ten artykuł obejmuje następujące zagadnienia:

* Przywróć pełny udział plików platformy Azure.
* Przywróć pojedyncze pliki lub foldery.
* Śledź stan operacji przywracania.

## <a name="steps-to-perform-a-restore-operation"></a>Procedura wykonywania operacji przywracania

Aby wykonać operację przywracania, wykonaj następujące kroki.

### <a name="select-the-file-share-to-restore"></a>Wybierz udział plików do przywrócenia

1. W [Azure Portal](https://portal.azure.com/)otwórz magazyn Recovery Services, który został użyty do skonfigurowania kopii zapasowej udziału plików.

1. W okienku Przegląd wybierz pozycję **elementy kopii zapasowej** w sekcji **chronione elementy** .

    ![Wybierz elementy kopii zapasowej](./media/restore-afs/backup-items.png)

1. Po wybraniu **pozycji elementy kopii zapasowej** w okienku Przegląd zostanie otwarte nowe okienko zawierające listę wszystkich typów zarządzania kopiami zapasowymi.

    ![Typy zarządzania kopiami zapasowymi](./media/restore-afs/backup-management.png)

1. W okienku **elementy kopii zapasowej** w obszarze **Typ zarządzania kopiami zapasowymi** wybierz pozycję **Azure Storage (Azure Files)**. Zostanie wyświetlona lista wszystkich udziałów plików i odpowiednich kont magazynu, których kopię zapasową utworzono przy użyciu tego magazynu.

    ![Lista wszystkich udziałów plików](./media/restore-afs/file-shares.png)

1. Z listy udziałów plików platformy Azure wybierz udział plików, dla którego chcesz wykonać operację przywracania.

### <a name="full-share-recovery"></a>Odzyskiwanie pełnego udziału

Można użyć tej opcji przywracania, aby przywrócić pełny udział plików w lokalizacji oryginalnej lub alternatywnej.

1. Wybierz opcję **Przywróć udział** w okienku **elementu kopia zapasowa** , która jest wyświetlana po wybraniu udziału plików do przywrócenia w kroku 5 sekcji [Wybierz udział plików do przywrócenia](#select-the-file-share-to-restore) .

   ![Wybierz pozycję Przywróć udział](./media/restore-afs/restore-share.png)

1. Po wybraniu opcji **Przywróć udział** zostanie otwarte okienko **przywracanie** . Aby wybrać punkt przywracania, który ma być używany do wykonywania operacji przywracania, wybierz **opcję Wybierz** tekst linku poniżej pola tekstowego **punkt przywracania** .

    ![Wybierz pozycję punkt przywracania, wybierając pozycję Wybierz.](./media/restore-afs/select-restore-point.png)

1. Po prawej stronie zostanie otwarte okienko kontekstu **Wybierz punkt przywracania** z listą punktów przywracania dostępnych dla wybranego udziału plików. Wybierz punkt przywracania, którego chcesz użyć do wykonania operacji przywracania, a następnie wybierz **przycisk OK**.

    ![Wybierz punkt przywracania](./media/restore-afs/restore-point.png)

    >[!NOTE]
    >Domyślnie okienko **Wybierz punkt przywracania** zawiera listę punktów przywracania z ostatnich 30 dni. Aby wyszukać punkty przywracania utworzone w określonym czasie trwania, określ zakres, wybierając odpowiedni **czas rozpoczęcia** i **godzinę zakończenia** , a następnie wybierz przycisk **Odśwież** .

1. Następnym krokiem jest wybranie **lokalizacji przywracania**. W sekcji **miejsce docelowe odzyskiwania** określ miejsce lub sposób przywracania danych. Wybierz jedną z następujących dwóch opcji, korzystając z przycisku przełącznika:

    * **Oryginalna lokalizacja**: Przywróć pełny udział plików w tej samej lokalizacji, w której znajduje się oryginalne źródło.
    * **Lokalizacja alternatywna**: Przywróć pełny udział plików w alternatywnej lokalizacji i Zachowaj oryginalny udział plików w formacie.

#### <a name="restore-to-the-original-location-full-share-recovery"></a>Przywracanie do oryginalnej lokalizacji (pełne udostępnianie udziałów)

1. Wybierz opcję **Oryginalna lokalizacja** jako **lokalizację docelową odzyskiwania**, a następnie wybierz, czy pominąć lub zastąpić w przypadku konfliktów, wybierając odpowiednią opcję z listy rozwijanej **w przypadku konfliktów** .

1. Wybierz pozycję **Przywróć** , aby rozpocząć operację przywracania.

    ![Wybierz pozycję Przywróć, aby rozpocząć](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-full-share-recovery"></a>Przywracanie do lokalizacji alternatywnej (pełne udziały odzyskiwania)

1. Wybierz **lokalizację alternatywną** jako **miejsce docelowe odzyskiwania**.
1. Wybierz docelowe konto magazynu, do którego ma zostać przywrócona zawartość kopii zapasowej z listy rozwijanej **konto magazynu** .
1. Na liście rozwijanej **Wybierz udział plików** są wyświetlane udziały plików znajdujące się na koncie magazynu wybranym w kroku 2. Wybierz udział plików, do którego ma zostać przywrócona zawartość kopii zapasowej.
1. W polu **Nazwa folderu** Określ nazwę folderu, który ma zostać utworzony w docelowym udziale plików z przywróconą zawartością.
1. Wybierz, czy pominąć, czy zastąpić, jeśli występują konflikty.
1. Po wprowadzeniu odpowiednich wartości we wszystkich polach wybierz pozycję **Przywróć** , aby rozpocząć operację przywracania.

    ![Wybierz lokalizację alternatywną](./media/restore-afs/alternate-location.png)

### <a name="item-level-recovery"></a>Odzyskiwanie na poziomie elementu

Ta opcja przywracania służy do przywracania pojedynczych plików lub folderów w lokalizacji oryginalnej lub alternatywnej.

1. Wybierz opcję **odzyskiwanie plików** w okienku **element kopii zapasowej** , która jest wyświetlana po wybraniu udziału plików do przywrócenia w kroku 5 sekcji [Wybierz udział plików do przywrócenia](#select-the-file-share-to-restore) .

    ![Wybierz odzyskiwanie plików](./media/restore-afs/file-recovery.png)

1. Po wybraniu opcji **odzyskiwanie plików** zostanie otwarte okienko **przywracanie** . Aby wybrać punkt przywracania, który ma być używany do wykonywania operacji przywracania, zaznacz pole tekstowe **Wybierz** link poniżej pola tekstowego **punkt przywracania** .

    ![Wybierz pozycję punkt przywracania, wybierając link wybierz](./media/restore-afs/select-restore-point.png)

1. Po prawej stronie zostanie otwarte okienko kontekstu **Wybierz punkt przywracania** z listą punktów przywracania dostępnych dla wybranego udziału plików. Wybierz punkt przywracania, którego chcesz użyć do wykonania operacji przywracania, a następnie wybierz **przycisk OK**.

    ![Wybierz punkt przywracania](./media/restore-afs/restore-point.png)

1. Następnym krokiem jest wybranie **lokalizacji przywracania**. W sekcji **miejsce docelowe odzyskiwania** określ miejsce lub sposób przywracania danych. Wybierz jedną z następujących dwóch opcji, korzystając z przycisku przełącznika:

    * **Oryginalna lokalizacja**: Przywróć wybrane pliki lub foldery do tego samego udziału plików, co oryginalne źródło.
    * **Lokalizacja alternatywna**: Przywróć wybrane pliki lub foldery do lokalizacji alternatywnej i Zachowaj oryginalną zawartość udziału plików.

#### <a name="restore-to-the-original-location-item-level-recovery"></a>Przywróć do oryginalnej lokalizacji (odzyskiwanie na poziomie elementu)

1. Wybierz opcję **Oryginalna lokalizacja** jako **lokalizację docelową odzyskiwania**, a następnie wybierz, czy pominąć lub zastąpić, jeśli występują konflikty, wybierając odpowiednią opcję z listy rozwijanej **w przypadku konfliktów** .

    ![Oryginalna lokalizacja odzyskiwania na poziomie elementu](./media/restore-afs/original-location-item-level.png)

1. Aby wybrać pliki lub foldery, które chcesz przywrócić, wybierz przycisk **Dodaj plik** . Spowoduje to otwarcie okienka kontekstowego po prawej stronie, wyświetlając zawartość punktu odzyskiwania udziału plików, który został wybrany do przywracania.

    ![Wybierz pozycję Dodaj plik](./media/restore-afs/add-file.png)

1. Zaznacz pole wyboru odpowiadające plikowi lub folderowi, który chcesz przywrócić, a następnie wybierz **pozycję Wybierz**.

    ![Wybierz plik lub folder](./media/restore-afs/select-file-folder.png)

1. Powtórz kroki od 2 do 4, aby wybrać wiele plików lub folderów do przywrócenia.
1. Po wybraniu wszystkich elementów, które mają zostać przywrócone, wybierz pozycję **Przywróć** , aby rozpocząć operację przywracania.

    ![Wybierz pozycję Przywróć, aby rozpocząć](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location-item-level-recovery"></a>Przywracanie do lokalizacji alternatywnej (odzyskiwanie na poziomie elementu)

1. Wybierz **lokalizację alternatywną** jako **miejsce docelowe odzyskiwania**.
1. Wybierz docelowe konto magazynu, do którego ma zostać przywrócona zawartość kopii zapasowej z listy rozwijanej **konto magazynu** .
1. Na liście rozwijanej **Wybierz udział plików** są wyświetlane udziały plików znajdujące się na koncie magazynu wybranym w kroku 2. Wybierz udział plików, do którego ma zostać przywrócona zawartość kopii zapasowej.
1. W polu **Nazwa folderu** Określ nazwę folderu, który ma zostać utworzony w docelowym udziale plików z przywróconą zawartością.
1. Wybierz, czy pominąć, czy zastąpić, jeśli występują konflikty.
1. Aby wybrać pliki lub foldery, które chcesz przywrócić, wybierz przycisk **Dodaj plik** . Spowoduje to otwarcie okienka kontekstowego po prawej stronie zawierającej zawartość punktu odzyskiwania udziału plików, który został wybrany do przywracania.

    ![Wybierz elementy, które mają zostać przywrócone do lokalizacji alternatywnej](./media/restore-afs/restore-to-alternate-location.png)

1. Zaznacz pole wyboru odpowiadające plikowi lub folderowi, który chcesz przywrócić, a następnie wybierz **pozycję Wybierz**.

    ![Wybierz miejsce docelowe odzyskiwania](./media/restore-afs/recovery-destination.png)

1. Powtórz kroki od 6 do 8, aby wybrać wiele plików lub folderów do przywrócenia.
1. Po wybraniu wszystkich elementów, które mają zostać przywrócone, wybierz pozycję **Przywróć** , aby rozpocząć operację przywracania.

    ![Wybierz przycisk OK po wybraniu wszystkich plików](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>Śledzenie operacji przywracania

Po zainicjowaniu operacji przywracania usługa Backup tworzy zadanie śledzenia. Azure Backup wyświetla powiadomienia dotyczące zadania w portalu. Aby wyświetlić operacje dla zadania, wybierz hiperłącze powiadomienia.

![Wybierz hiperlink powiadomień](./media/restore-afs/notifications-link.png)

Możesz również monitorować postęp przywracania z magazynu Recovery Services:

1. Otwórz magazyn Recovery Services z którego została wyzwolona operacja przywracania.
1. W okienku Przegląd wybierz pozycję **zadania tworzenia kopii zapasowych** w sekcji **monitorowanie** , aby zobaczyć stan operacji uruchomionych dla różnych obciążeń.

    ![Wybierz zadania tworzenia kopii zapasowej](./media/restore-afs/backup-jobs.png)

1. Wybierz nazwę obciążenia odpowiadającą udziałowi plików, aby wyświetlić więcej szczegółów na temat operacji przywracania, takich jak **dane przesyłane** i **Liczba przywróconych plików**.

    ![Zobacz przywrócone szczegóły](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zarządzać kopiami zapasowymi udziałów plików platformy Azure](manage-afs-backup.md).
