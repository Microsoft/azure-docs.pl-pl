---
title: Zarządzanie kopiami zapasowymi agenta MARS i ich monitorowanie
description: Informacje o sposobach zarządzania kopiami zapasowymi agenta Microsoft Azure Recovery Services (MARS) i ich monitorowania za pomocą usługi Azure Backup.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: b3b648ca27a407640b42932fe2ed7c32f5109114
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89145573"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Zarządzanie kopiami zapasowymi agentów Microsoft Azure Recovery Services (MARS) za pomocą usługi Azure Backup

W tym artykule opisano sposób zarządzania plikami i folderami, których kopię zapasową utworzono przy użyciu agenta Microsoft Azure Recovery Services.

## <a name="modify-a-backup-policy"></a>Modyfikowanie zasad tworzenia kopii zapasowych

Podczas modyfikowania zasad tworzenia kopii zapasowych można dodać nowe elementy, usunąć istniejące elementy z kopii zapasowej lub wykluczyć pliki z kopii zapasowej za pomocą ustawień wykluczania.

- **Dodaj elementy** Użyj tej opcji tylko w przypadku dodawania nowych elementów do kopii zapasowej. Aby usunąć istniejące elementy, użyj opcji **Usuń elementy** lub **Ustawienia wykluczenia** .  
- **Usuń elementy** Użyj tej opcji, aby usunąć elementy z kopii zapasowej.
  - Użyj **ustawień wykluczeń** , aby usunąć wszystkie elementy w woluminie zamiast **usuwać elementy**.
  - Czyszczenie wszystkich zaznaczeń w woluminie powoduje, że stare kopie zapasowe elementów są zachowywane zgodnie z ustawieniami przechowywania w czasie wykonywania ostatniej kopii zapasowej bez zakresu modyfikacji.
  - Ponowne wybór tych elementów powoduje, że nowe kopie zapasowe nie są stosowane do starych kopii w ramach programu.
  - Odwybór całego woluminu zachowuje poprzednią kopię zapasową bez żadnego zakresu modyfikowania zasad przechowywania.
- **Ustawienia wykluczania** używają tej opcji, aby wykluczyć konkretne elementy z kopii zapasowej.

### <a name="add-new-items-to-existing-policy"></a>Dodawanie nowych elementów do istniejących zasad

1. W obszarze **Akcje**wybierz pozycję **Zaplanuj kopię zapasową**.

    ![Planowanie tworzenia kopii zapasowej systemu Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Na karcie **Wybierz element zasad** i wybierz pozycję **Modyfikuj harmonogram tworzenia kopii zapasowych dla plików i folderów** , a następnie wybierz przycisk **dalej**.

    ![Wybierz elementy zasad](./media/backup-azure-manage-mars/select-policy-items.png)

3. W obszarze **Modyfikuj lub Zatrzymaj Planowanie kopii zapasowej** wybierz pozycję **Wprowadź zmiany do elementów kopii zapasowej lub godziny** , a następnie wybierz przycisk **dalej**.

    ![Modyfikuj lub Zaplanuj kopię zapasową](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Na karcie **Wybierz elementy do utworzenia kopii zapasowej** wybierz pozycję **Dodaj elementy** , aby dodać elementy, dla których chcesz utworzyć kopię zapasową.

    ![Modyfikuj lub Zaplanuj Dodawanie elementów kopii zapasowej](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. W oknie **Wybieranie elementów** wybierz pliki lub foldery, które chcesz dodać, a następnie wybierz przycisk **OK**.

    ![Wybierz elementy](./media/backup-azure-manage-mars/select-item.png)

6. Wykonaj następne kroki i wybierz pozycję **Zakończ** , aby ukończyć operację.

### <a name="add-exclusion-rules-to-existing-policy"></a>Dodawanie reguł wykluczeń do istniejących zasad

Można dodać reguły wykluczeń, aby pominąć pliki i foldery, których kopie zapasowe nie mają być tworzone. Można to zrobić podczas definiowania nowych zasad lub modyfikowania istniejących zasad.

1. W okienku Akcje wybierz pozycję **Zaplanuj kopię zapasową**. Przejdź do **pozycji Wybierz elementy do utworzenia kopii zapasowej** i wybierz pozycję **ustawienia wykluczania**.

    ![Ustawienia wykluczania](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. W obszarze **ustawienia wykluczania**wybierz pozycję **Dodaj wykluczenie**.

    ![Dodaj wykluczenie](./media/backup-azure-manage-mars/add-exclusion.png)

3. Z **pozycji Wybierz elementy do wykluczenia**, Przejrzyj pliki i foldery, a następnie wybierz elementy, które chcesz wykluczyć, a następnie wybierz **przycisk OK**.

    ![Wybierz elementy do wykluczenia](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Domyślnie wszystkie **podfoldery** w wybranych folderach są wykluczone. Możesz to zmienić, wybierając opcję **tak** lub **nie**. Można edytować i określić typy plików do wykluczenia, jak pokazano poniżej:

    ![Wybierz typy podfolderów](./media/backup-azure-manage-mars/subfolders-type.png)

5. Wykonaj następne kroki i wybierz pozycję **Zakończ** , aby ukończyć operację.

### <a name="remove-items-from-existing-policy"></a>Usuń elementy z istniejących zasad

1. W okienku Akcje wybierz pozycję **Zaplanuj kopię zapasową**. Przejdź do **pozycji Wybierz elementy do utworzenia kopii zapasowej**. Z listy wybierz pliki i foldery, które chcesz usunąć z harmonogramu kopii zapasowych, a następnie wybierz pozycję **Usuń elementy**.

    ![Wybierz elementy do usunięcia](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Po całkowitym usunięciu woluminu z zasad należy zachować ostrożność.  Jeśli musisz dodać go ponownie, będzie on traktowany jako nowy wolumin. Następna zaplanowana kopia zapasowa wykona początkową kopię zapasową (pełną kopię zapasową) zamiast przyrostowej kopii zapasowej. Jeśli trzeba tymczasowo usunąć i dodać elementy później, zaleca się użycie **ustawień wykluczenia** zamiast **usuwania elementów** , aby zapewnić przyrostową kopię zapasową zamiast pełnej kopii zapasowej.

2. Wykonaj następne kroki i wybierz pozycję **Zakończ** , aby ukończyć operację.

## <a name="stop-protecting-files-and-folder-backup"></a>Zatrzymaj ochronę plików i folderów kopii zapasowych

Istnieją dwa sposoby na zatrzymanie ochrony kopii zapasowych plików i folderów:

- **Zatrzymaj ochronę i Zachowaj dane kopii zapasowej**.
  - Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej z ochrony.
  - Usługa Azure Backup będzie nadal utrzymywać wszystkie istniejące punkty odzyskiwania.  
  - Będzie można przywrócić dane z kopii zapasowej dla niewygasłych punktów odzyskiwania.
  - Jeśli zdecydujesz się wznowić ochronę, możesz użyć opcji *Włącz ponownie harmonogram tworzenia kopii zapasowych* . Następnie dane zostaną zachowane na podstawie nowych zasad przechowywania.
- **Zatrzymaj ochronę i Usuń dane kopii zapasowej**.
  - Ta opcja spowoduje zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowej z ochrony danych i usunięcie wszystkich punktów odzyskiwania.
  - Otrzymasz wiadomość e-mail z alertem dotyczącym usuwania danych kopii zapasowej z komunikatem, *że dane dla tego elementu kopii zapasowej zostały usunięte. Te dane będą tymczasowo dostępne przez 14 dni, po upływie których zostanie trwale usunięte* i zalecana Akcja *ponownie Włącz ochronę elementu kopii zapasowej w ciągu 14 dni, aby odzyskać dane.*
  - Aby wznowić ochronę, ponownie Włącz ochronę w ciągu 14 dni od operacji usuwania.

### <a name="stop-protection-and-retain-backup-data"></a>Zatrzymywanie ochrony i zachowywanie danych kopii zapasowej

1. Otwórz konsolę zarządzania MARS, przejdź do **okienka Akcje**, a **następnie wybierz pozycję Zaplanuj kopię zapasową**.

    ![Wybieranie harmonogramu kopii zapasowej](./media/backup-azure-manage-mars/mars-actions.png)
1. Na stronie **Wybierz element zasad** wybierz pozycję **Modyfikuj harmonogram tworzenia kopii zapasowych plików i folderów** , a następnie wybierz przycisk **dalej**.

    ![Wybierz element zasad](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Na stronie **Modyfikuj lub Zatrzymaj zaplanowaną kopię zapasową** wybierz pozycję **Zatrzymaj przy użyciu tego harmonogramu tworzenia kopii zapasowych, ale przechowuj przechowywane kopie zapasowe do momentu ponownego aktywowania harmonogramu**. Następnie wybierz pozycję **Dalej**.

    ![Zatrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. W obszarze **Wstrzymaj zaplanowaną kopię zapasową**Przejrzyj informacje i wybierz pozycję **Zakończ**.

    ![Wstrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. W obszarze **Modyfikowanie postępu tworzenia kopii zapasowej**Sprawdź, czy harmonogram wykonywania kopii zapasowej jest w stanie powodzenie i wybierz pozycję **Zamknij** , aby zakończyć.

### <a name="stop-protection-and-delete-backup-data"></a>Zatrzymywanie ochrony i usuwanie danych kopii zapasowej

1. Otwórz konsolę zarządzania MARS, przejdź do okienka **Akcje** , a następnie wybierz pozycję **Zaplanuj kopię zapasową**.
2. Na stronie **Modyfikuj lub Zatrzymaj zaplanowaną kopię zapasową** wybierz pozycję **Zatrzymaj korzystanie z tego harmonogramu kopii zapasowych i Usuń wszystkie przechowywane kopie zapasowe**. Następnie wybierz pozycję **Dalej**.

    ![Zmodyfikuj lub Zatrzymaj zaplanowaną kopię zapasową.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na stronie **Zatrzymaj zaplanowaną kopię zapasową** wybierz pozycję **Zakończ**.

    ![Zatrzymaj zaplanowaną kopię zapasową i wybierz pozycję Zakończ](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Zostanie wyświetlony monit o wprowadzenie numeru PIN zabezpieczeń (osobistego numeru identyfikacyjnego), który należy wygenerować ręcznie. Aby to zrobić, najpierw Zaloguj się do Azure Portal.
5. Przejdź do obszaru **Recovery Services**  >  **Settings**  >  **Właściwości**ustawień magazynu.
6. W obszarze **zabezpieczający numer PIN**wybierz pozycję **Generuj**. Skopiuj ten kod PIN. Numer PIN jest prawidłowy tylko przez pięć minut.
7. W konsoli zarządzania Wklej kod PIN, a następnie wybierz przycisk **OK**.

    ![Generuj zabezpieczający numer PIN.](./media/backup-azure-delete-vault/security-pin.png)

8. Na stronie **Modyfikowanie postępu tworzenia kopii zapasowej** zostanie wyświetlony następujący komunikat: *usunięte dane kopii zapasowej będą przechowywane przez 14 dni. Po upływie tego czasu dane kopii zapasowej zostaną trwale usunięte.*  

    ![Modyfikowanie postępu tworzenia kopii zapasowej](./media/backup-azure-delete-vault/deleted-backup-data.png)

Po usunięciu lokalnych elementów kopii zapasowej wykonaj kolejne kroki z portalu.

## <a name="re-enable-protection"></a>Włącz ponownie ochronę

Jeśli ochrona zostanie zatrzymana podczas zachowywania danych i podjęta zostanie decyzja o wznowieniu ochrony, można ponownie włączyć harmonogram tworzenia kopii zapasowych przy użyciu opcji Modyfikuj zasady tworzenia kopii zapasowych.

1. W obszarze **Akcje** wybierz pozycję **Zaplanuj kopię zapasową**.
1. Wybierz pozycję **Włącz ponownie harmonogram tworzenia kopii zapasowych. Możesz również zmodyfikować elementy lub godziny tworzenia kopii zapasowej** , a następnie wybrać przycisk **dalej**.<br>

    ![Włącz ponownie harmonogram tworzenia kopii zapasowych](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. W obszarze **Wybierz elementy do utworzenia kopii zapasowej**wybierz pozycję **dalej**.

    ![Wybierz elementy do wykonania kopii zapasowej](./media/backup-azure-manage-mars/re-enable-next.png)
1. W obszarze **Określ harmonogram kopii zapasowych**Określ harmonogram tworzenia kopii zapasowych i wybierz pozycję **dalej**.
1. W obszarze **Wybierz zasady przechowywania**Określ czas trwania przechowywania i wybierz pozycję **dalej**.
1. Na koniec na ekranie **potwierdzenia** Sprawdź szczegóły zasad i wybierz pozycję **Zakończ**.

## <a name="re-generate-passphrase"></a>Wygeneruj ponownie hasło

Hasło jest używane do szyfrowania i odszyfrowywania danych podczas tworzenia kopii zapasowej lub przywracania lokalnego lub maszyny lokalnej przy użyciu agenta MARS na platformie lub z platformy Azure. Jeśli utracisz lub nie pamiętasz hasła, możesz ponownie wygenerować hasło (pod warunkiem, że maszyna jest nadal zarejestrowana w magazynie Recovery Services i jest konfigurowana kopia zapasowa), wykonując następujące czynności:

1. W konsoli agenta Mars przejdź do **okienka Akcje**  >  **Zmień właściwości** >. Następnie przejdź do **karty szyfrowanie**.<br>
1. Zaznacz pole wyboru **Zmień hasło** .<br>
1. Wprowadź nowe hasło lub wybierz pozycję **Generuj hasło**.
1. Wybierz pozycję **Przeglądaj** , aby zapisać nowe hasło.

    ![Generuj hasło.](./media/backup-azure-manage-mars/passphrase.png)

1. Wybierz **przycisk OK** , aby zastosować zmiany.  Jeśli [Funkcja zabezpieczenia](./backup-azure-security-feature.md#enable-security-features) jest włączona na Azure Portal magazynu Recovery Services, zostanie wyświetlony monit o wprowadzenie numeru PIN zabezpieczeń. Aby odebrać kod PIN, wykonaj kroki opisane w tym [artykule](./backup-azure-security-feature.md#authentication-to-perform-critical-operations).<br>
1. Wklej zabezpieczający numer PIN z portalu i wybierz **przycisk OK** , aby zastosować zmiany.<br>

    ![Wklej zabezpieczający numer PIN](./media/backup-azure-manage-mars/passphrase2.png)
1. Upewnij się, że hasło jest bezpiecznie zapisane w lokalizacji alternatywnej (innej niż maszyna źródłowa), najlepiej w Azure Key Vault. Śledź wszystkie hasła, jeśli masz kopię zapasową wielu maszyn z agentami MARS.

## <a name="managing-backup-data-for-unavailable-machines"></a>Zarządzanie danymi kopii zapasowej dla niedostępnych maszyn

W tej sekcji omówiono scenariusz, w którym maszyna źródłowa chroniona za pomocą usług MARS nie jest już dostępna, ponieważ została usunięta, uszkodzona, zainfekowana przez oprogramowanie chroniące przed złośliwym oprogramowaniem lub zlikwidowane.

W przypadku tych maszyn usługa Azure Backup zapewnia, że najnowszy punkt odzyskiwania nie wygaśnie (oznacza to, że nie zostanie oczyszczony) zgodnie z regułami przechowywania określonymi w zasadach tworzenia kopii zapasowych. W związku z tym można bezpiecznie przywrócić maszynę.  Należy wziąć pod uwagę następujące scenariusze, które można wykonać na danych kopii zapasowej:

### <a name="scenario-1-the-source-machine-is-unavailable-and-you-no-longer-need-to-retain-backup-data"></a>Scenariusz 1: maszyna źródłowa jest niedostępna i nie jest już konieczne zachowywanie danych kopii zapasowej

- Kopię zapasową danych można usunąć z Azure Portal, wykonując kroki opisane w [tym artykule](backup-azure-delete-vault.md#delete-protected-items-on-premises).

### <a name="scenario-2-the-source-machine-is-unavailable-and-you-need-to-retain-backup-data"></a>Scenariusz 2: maszyna źródłowa jest niedostępna i należy zachować dane kopii zapasowej

Zarządzanie zasadami tworzenia kopii zapasowych w usłudze MARS odbywa się za pomocą konsoli MARS, a nie za pomocą portalu. Jeśli musisz zwiększyć ustawienia przechowywania dla istniejących punktów odzyskiwania przed ich wygaśnięciem, należy przywrócić maszynę, zainstalować konsolę MARS i rozłożyć zasady.

- Aby przywrócić maszynę, wykonaj następujące czynności:
  1. [Przywracanie maszyny wirtualnej na alternatywną maszynę docelową](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)
  1. Utwórz ponownie maszynę docelową o tej samej nazwie hosta co maszyna źródłowa
  1. Zainstaluj agenta i zarejestruj się ponownie w tym samym magazynie i przy użyciu tego samego hasła
  1. Uruchom klienta MARS, aby zwiększyć czas przechowywania zgodnie z wymaganiami
- Nowo przywrócona maszyna chroniona przy użyciu usług MARS będzie nadal tworzyć kopie zapasowe.  

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o obsługiwanych scenariuszach i ograniczeniach, zapoznaj się z [matrycą pomocy technicznej dla agenta Mars](./backup-support-matrix-mars-agent.md).
- Dowiedz się więcej [na temat zachowania przechowywania zasad tworzenia kopii zapasowej na żądanie](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
- Aby uzyskać więcej często zadawanych pytań, zobacz [często zadawane pytania dotyczące agenta Mars](backup-azure-file-folder-backup-faq.md).
