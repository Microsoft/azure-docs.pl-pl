---
title: Zarządzanie kopiami zapasowymi programu MARS Agent i monitorowanie ich
description: Dowiedz się, jak zarządzać kopiami zapasowymi agentów usług odzyskiwania platformy Microsoft Azure (MARS) i monitorować go przy użyciu usługi Azure Backup.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: a88ec4dc9283114e06eed424172dbb958850c2e9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025105"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Zarządzanie kopiami zapasowymi agentów usług odzyskiwania platformy Microsoft Azure (MARS) przy użyciu usługi Azure Backup

W tym artykule opisano sposób zarządzania plikami i folderami, których kopia zapasowa jest za pomocą agenta usług odzyskiwania platformy Microsoft Azure.

## <a name="modify-a-backup-policy"></a>Modyfikowanie zasad tworzenia kopii zapasowych

Podczas modyfikowania zasad tworzenia kopii zapasowych można dodawać nowe elementy, usuwać istniejące elementy z kopii zapasowej lub wykluczać pliki z kopii zapasowej przy użyciu ustawień wykluczeń.

- **Dodaj elementy** użyj tej opcji tylko do dodawania nowych elementów do kopii zapasowej. Aby usunąć istniejące elementy, użyj opcji **Usuń elementy** lub **Ustawienia wykluczeń.**  
- **Usuń elementy** użyj tej opcji, aby usunąć elementy z kopii zapasowej.
  - Użyj **ustawień wykluczeń, aby** usunąć wszystkie elementy w woluminie zamiast **usuwać elementy**.
  - Wyczyszczenie wszystkich wyborów na woluminie powoduje, że stare kopie zapasowe elementów, które mają być przechowywane zgodnie z ustawieniami przechowywania w czasie ostatniej kopii zapasowej, bez możliwości modyfikacji.
  - Ponowne wybranie tych elementów prowadzi do pierwszej pełnej kopii zapasowej i nowe zmiany zasad nie są stosowane do starych kopii zapasowych.
  - Odznaczanie całego woluminu zachowuje poprzednią kopię zapasową bez możliwości modyfikowania zasad przechowywania.
- **Ustawienia wykluczeń** używają tej opcji, aby wykluczyć określone elementy z kopii zapasowej.

### <a name="add-new-items-to-existing-policy"></a>Dodawanie nowych elementów do istniejących zasad

1. W **obszarze Akcje**kliknij pozycję **Zaplanuj kopię zapasową**.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Na karcie **Wybierz element zasad** wybierz pozycję **Modyfikuj harmonogram tworzenia kopii zapasowych dla plików i folderów,** a następnie kliknij przycisk **Dalej**.

    ![Wybierz elementy zasad](./media/backup-azure-manage-mars/select-policy-items.png)

3. Na karcie **Modyfikowanie lub zatrzymywać tworzenie kopii zapasowych** wybierz pozycję **Wprowadzanie zmian w elementach lub godzinach tworzenia kopii zapasowych,** a następnie kliknij przycisk **Dalej**.

    ![Modyfikowanie lub planowanie tworzenia kopii zapasowych](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Na karcie **Wybierz elementy do kopii zapasowej** kliknij pozycję Dodaj **elementy,** aby dodać elementy, których kopię zapasową chcesz utworzyć.

    ![Modyfikowanie lub planowanie tworzenia kopii zapasowych dodatkowych elementów](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. W oknie **Wybierz elementy** zaznacz muchy lub foldery, które chcesz dodać, a następnie kliknij przycisk **OK**.

    ![Zaznaczanie elementów](./media/backup-azure-manage-mars/select-item.png)

6. Wykonaj kolejne kroki i kliknij przycisk **Zakończ,** aby zakończyć operację.

### <a name="add-exclusion-rules-to-existing-policy"></a>Dodawanie reguł wykluczeń do istniejących zasad

Możesz dodać reguły wykluczeń, aby pominąć pliki i foldery, których nie chcesz archiwizować. Można to zrobić podczas definiowania nowych zasad lub modyfikowania istniejących zasad.

1. W okienku Akcje kliknij pozycję **Zaplanuj kopię zapasową**. Przejdź do **pozycji Wybierz elementy do kopii zapasowej** i kliknij pozycję Ustawienia **wykluczeń**.

    ![Zaznaczanie elementów](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. W **obszarze Ustawienia wykluczeń**kliknij pozycję **Dodaj wykluczenie**.

    ![Zaznaczanie elementów](./media/backup-azure-manage-mars/add-exclusion.png)

3. W **obszarze Wybierz elementy do wykluczenia**przeglądaj pliki i foldery oraz zaznacz elementy, które chcesz wykluczyć, a następnie kliknij przycisk **OK**.

    ![Zaznaczanie elementów](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Domyślnie wszystkie **podfoldery** w wybranych folderach są wykluczone. Można to zmienić, wybierając **tak** lub **nie**. Można edytować i określać typy plików do wykluczenia, jak pokazano poniżej:

    ![Zaznaczanie elementów](./media/backup-azure-manage-mars/subfolders-type.png)

5. Wykonaj kolejne kroki i kliknij przycisk **Zakończ,** aby zakończyć operację.

### <a name="remove-items-from-existing-policy"></a>Usuwanie elementów z istniejących zasad

1. W okienku Akcje kliknij pozycję **Zaplanuj kopię zapasową**. Przejdź do **pozycji Wybierz elementy do kopii zapasowej**. Z listy zaznacz pliki i foldery, które chcesz usunąć z harmonogramu tworzenia kopii zapasowych, a następnie kliknij pozycję **Usuń elementy**.

    ![Zaznaczanie elementów](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Należy zachować ostrożność po całkowitym usunięciu woluminu z zasad.  Jeśli musisz dodać go ponownie, zostanie on potraktowany jako nowy wolumin. Następna zaplanowana kopia zapasowa wykona wstępną kopię zapasową (pełną kopię zapasową) zamiast przyrostowej kopii zapasowej. Jeśli musisz tymczasowo usunąć i dodać elementy później, zaleca się użycie **ustawień wykluczeń** zamiast **usuń elementy,** aby zapewnić przyrostową kopię zapasową zamiast pełnej kopii zapasowej.

2. Wykonaj kolejne kroki i kliknij przycisk **Zakończ,** aby zakończyć operację.

## <a name="stop-protecting-files-and-folder-backup"></a>Zatrzymywać ochronę kopii zapasowej plików i folderów

Istnieją dwa sposoby, aby zatrzymać ochronę kopii zapasowej plików i folderów:

- **Zatrzymaj ochronę i zachowaj dane kopii zapasowej**.
  - Ta opcja spowoduje zatrzymanie ochrony wszystkich przyszłych zadań tworzenia kopii zapasowych.
  - Usługa Azure Backup będzie nadal zachowywać wszystkie istniejące punkty odzyskiwania.  
  - Będziesz mógł przywrócić dane kopii zapasowej dla niewygasłych punktów odzyskiwania.
  - Jeśli zdecydujesz się wznowić ochronę, możesz użyć opcji *Ponownie włącz harmonogram tworzenia kopii zapasowych.* Następnie dane będą przechowywane na podstawie nowych zasad przechowywania.
- **Zatrzymaj ochronę i usuń dane kopii zapasowej**.
  - Ta opcja uniemożliwi ochronę danych wszystkim przyszłym zamówień kopii zapasowej i usunie wszystkie punkty odzyskiwania.
  - Otrzymasz usuniętą wiadomość e-mail z powiadomieniem o danych kopii zapasowej z wiadomością *Dane dla tego elementu kopii zapasowej zostały usunięte. Dane te będą tymczasowo dostępne przez 14 dni, po czym zostaną trwale usunięte,* a zalecane działanie *Reprotect elementu kopii zapasowej w ciągu 14 dni, aby odzyskać dane.*
  - Aby wznowić ochronę, należy ponownie wykonać w ciągu 14 dni od operacji usuwania.

### <a name="stop-protection-and-retain-backup-data"></a>Zatrzymaj ochronę i zachowaj dane kopii zapasowej

1. Otwórz konsolę zarządzania mars, przejdź do **okienka Akcje**i **wybierz pozycję Zaplanuj kopię zapasową**.

    ![Modyfikowanie lub zatrzymywać zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/mars-actions.png)
1. Na stronie **Wybierz element zasad** wybierz pozycję **Modyfikuj harmonogram tworzenia kopii zapasowych dla plików i folderów,** a następnie kliknij przycisk **Dalej**.

    ![Modyfikowanie lub zatrzymywać zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Na stronie **Modyfikowanie lub zatrzymywać zaplanowaną kopię zapasową** wybierz pozycję **Zatrzymaj korzystanie z tego harmonogramu tworzenia kopii zapasowych, ale zachowaj zapisane kopie zapasowe, dopóki harmonogram nie zostanie ponownie aktywowany**. Następnie wybierz przycisk **Dalej**.

    ![Modyfikowanie lub zatrzymywać zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. W **obszarze Wstrzymaj zaplanowaną kopię zapasową** przejrzyj informacje i kliknij przycisk **Zakończ**.

    ![Modyfikowanie lub zatrzymywać zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. W **obszarze Modyfikuj proces tworzenia kopii zapasowej** sprawdź, czy wstrzymanie tworzenia kopii zapasowych harmonogramu jest w stanie sukcesu i kliknij przycisk **Zamknij,** aby zakończyć.

### <a name="stop-protection-and-delete-backup-data"></a>Zatrzymaj ochronę i usuń dane kopii zapasowej

1. Otwórz konsolę zarządzania mars, przejdź do okienka **Akcje** i wybierz pozycję **Zaplanuj kopię zapasową**.
2. Na stronie **Modyfikowanie lub zatrzymywać zaplanowaną kopię zapasową** wybierz pozycję **Zatrzymaj korzystanie z tego harmonogramu tworzenia kopii zapasowych i usuń wszystkie zapisane kopie zapasowe**. Następnie wybierz przycisk **Dalej**.

    ![Modyfikowanie lub zatrzymywać zaplanowaną kopię zapasową.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stronie **Zatrzymaj zaplanowaną kopię zapasową** wybierz pozycję **Zakończ**.

    ![Zatrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zostanie wyświetlony monit o wprowadzenie kodu PIN zabezpieczeń (osobistego numeru identyfikacyjnego), który należy wygenerować ręcznie. Aby to zrobić, najpierw zaloguj się do witryny Azure portal.
5. Przejdź do pozycji**Właściwości****ustawień** >  **magazynu** > usług odzyskiwania .
6. W obszarze **Kod PIN zabezpieczeń**wybierz pozycję **Generuj**. Skopiuj ten kod PIN. Kod PIN jest ważny tylko przez pięć minut.
7. W konsoli zarządzania wklej kod PIN, a następnie wybierz przycisk **OK**.

    ![Generowanie kodu PIN zabezpieczeń.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stronie **Modyfikuj postęp kopii zapasowej** zostanie wyświetlony następujący komunikat: *Usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po tym czasie dane kopii zapasowej zostaną trwale usunięte.*  

    ![Usuń infrastrukturę kopii zapasowej.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po usunięciu lokalnych elementów kopii zapasowej wykonaj następne kroki z portalu.

## <a name="re-enable-protection"></a>Ponowne włączenie ochrony

Jeśli ochrona została zatrzymana podczas przechowywania danych i zdecydowano się wznowić ochronę, można ponownie włączyć harmonogram tworzenia kopii zapasowych przy użyciu modyfikuj zasady tworzenia kopii zapasowych.

1. W **obszarze Akcje** wybierz pozycję **Zaplanuj tworzenie kopii zapasowej**.
1. Wybierz **pozycję Włącz ponownie harmonogram tworzenia kopii zapasowych. Można również zmodyfikować elementy lub godziny tworzenia kopii zapasowych** i kliknąć **przycisk Dalej**.<br>

    ![Usuń infrastrukturę kopii zapasowej.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. W **obszarze Wybierz elementy do kopii zapasowej**kliknij przycisk **Dalej**.

    ![Usuń infrastrukturę kopii zapasowej.](./media/backup-azure-manage-mars/re-enable-next.png)
1. W **obszarze Określanie harmonogramu kopii zapasowych**określ harmonogram tworzenia kopii zapasowych i kliknij przycisk **Dalej**.
1. W **obszarze Wybierz zasady przechowywania**określ czas przechowywania i kliknij przycisk **Dalej**.
1. Na koniec na ekranie **Potwierdzenia** przejrzyj szczegóły zasad i kliknij przycisk **Zakończ**.

## <a name="re-generate-passphrase"></a>Ponowne generowanie hasła

Hasło jest używane do szyfrowania i odszyfrowywania danych podczas tworzenia kopii zapasowej lub przywracania komputera lokalnego lub lokalnego przy użyciu agenta MARS na platformie Azure lub z platformy Azure. Jeśli zgubiłeś lub zapomniałeś hasła, możesz ponownie wygenerować hasło (pod warunkiem, że komputer jest nadal zarejestrowany w magazynie usług odzyskiwania i kopia zapasowa jest skonfigurowana), wykonując następujące kroki:

- W konsoli agenta MARS przejdź do**właściwości zmiany** **okienka** > akcji >. Następnie przejdź do **karty Szyfrowanie**.<br>
- Zaznacz pole wyboru **Zmień hasło.**<br>
- Wprowadź nowe hasło lub kliknij przycisk **Generuj hasło**.
- Kliknij **przycisk Przeglądaj,** aby zapisać nowe hasło.

    ![Generowanie hasła.](./media/backup-azure-manage-mars/passphrase.png)
- Kliknij **przycisk OK,** aby zastosować zmiany.  Jeśli [funkcja zabezpieczeń](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) jest włączona w witrynie Azure portal dla magazynu usług odzyskiwania, zostanie wyświetlony monit o wprowadzenie numeru PIN zabezpieczeń. Aby otrzymać numer PIN, wykonaj czynności opisane w tym [artykule](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations).<br>
- Wklej kod PIN zabezpieczeń z portalu i kliknij przycisk **OK,** aby zastosować zmiany.<br>

    ![Generowanie hasła.](./media/backup-azure-manage-mars/passphrase2.png)
- Upewnij się, że hasło jest bezpiecznie zapisywane w lokalizacji alternatywnej (innej niż komputer źródłowy), najlepiej w usłudze Azure Key Vault. Śledź wszystkie hasła, jeśli masz wiele maszyn, które są archiwizowane z agentami MARS.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat obsługiwanych scenariuszy i ograniczeń, zobacz [Macierz pomocy technicznej dla agenta MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- Dowiedz się więcej [o zachowaniu przechowywania zasad tworzenia kopii zapasowych na żądanie](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
