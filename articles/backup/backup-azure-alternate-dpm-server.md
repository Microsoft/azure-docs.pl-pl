---
title: Odzyskiwanie danych z Azure Backup Server
description: Odzyskaj dane chronione do magazynu Recovery Services z dowolnego Azure Backup Server zarejestrowanego w tym magazynie.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: ed8c937f97ec7a74662a8b46a354b0a6db39a2b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98806017"
---
# <a name="recover-data-from-azure-backup-server"></a>Odzyskiwanie danych z usługi Azure Backup Server

Za pomocą Azure Backup Server można odzyskać dane, których kopię zapasową utworzono w magazynie Recovery Services. Ten proces jest zintegrowany z konsolą zarządzania Azure Backup Server i jest podobny do przepływu pracy odzyskiwania dla innych składników Azure Backup.

> [!NOTE]
> Ten artykuł dotyczy programu [System Center Data Protection Manager 2012 R2 z pakietem UR7 lub nowszym](https://support.microsoft.com/kb/3065246), w połączeniu z [najnowszym agentem Azure Backup](https://aka.ms/azurebackup_agent).
>
>

Aby odzyskać dane z Azure Backup Server:

1. Na karcie **odzyskiwanie** w konsoli zarządzania Azure Backup Server wybierz pozycję **"Dodaj zewnętrzny program DPM"** (w lewym górnym rogu ekranu).

    ![Dodaj zewnętrzny program DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Pobierz nowe **poświadczenia magazynu** z magazynu skojarzonego z **Azure Backup Server** , w którym dane są odzyskiwane, wybierz Azure Backup Server z listy serwerów Azure Backup zarejestrowanych w magazynie Recovery Services i podaj **hasło szyfrowania** skojarzone z serwerem, którego dane są odzyskiwane.

    ![Zewnętrzne poświadczenia programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Tylko Azure Backup serwery skojarzone z tym samym magazynem rejestracji mogą odzyskać dane z innych danych.
   >
   >

    Po pomyślnym dodaniu Azure Backup Server zewnętrznego można przeglądać dane na serwerze zewnętrznym i Azure Backup Server lokalnym na karcie **odzyskiwanie** .
3. Przeglądaj dostępną listę serwerów produkcyjnych chronionych przez zewnętrzne Azure Backup Server i wybierz odpowiednie źródło danych.

    ![Przeglądaj zewnętrzny serwer DPM](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Wybierz **miesiąc i rok** z listy rozwijanej **punkty odzyskiwania** , wybierz wymaganą **datę odzyskiwania** dla momentu utworzenia punktu odzyskiwania, a następnie wybierz **czas odzyskiwania**.

    W dolnym okienku zostanie wyświetlona lista plików i folderów, które mogą być przeglądane i odzyskiwane do dowolnej lokalizacji.

    ![Punkty odzyskiwania zewnętrznego serwera DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Kliknij prawym przyciskiem myszy odpowiedni element i wybierz polecenie **Odzyskaj**.

    ![Zewnętrzne odzyskiwanie programu DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Przejrzyj **zaznaczenie opcji Odzyskaj**. Sprawdź dane i czas odzyskiwania kopii zapasowej, a także źródło, z którego została utworzona kopia zapasowa. Jeśli zaznaczenie jest nieprawidłowe, wybierz pozycję **Anuluj** , aby przejść z powrotem do karty odzyskiwanie, aby wybrać odpowiedni punkt odzyskiwania. Jeśli zaznaczenie jest prawidłowe, wybierz pozycję **dalej**.

    ![Podsumowanie zewnętrznego odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Wybierz opcję **Odzyskaj do lokalizacji alternatywnej**. **Przejdź** do odpowiedniej lokalizacji odzyskiwania.

    ![Alternatywna Lokalizacja odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Wybierz opcję powiązaną z poleceniem **Utwórz kopię**, **Pomiń** lub **Zastąp**.

   * **Utwórz kopię** — tworzy kopię pliku w przypadku kolizji nazw.
   * **Pomiń** — Jeśli występuje kolizja nazw, nie odzyska pliku, co pozostawia oryginalny plik.
   * **Zastąp** — Jeśli występuje kolizja nazw, zastępuje istniejącą kopię pliku.

     Wybierz odpowiednią opcję, aby **przywrócić zabezpieczenia**. Można zastosować ustawienia zabezpieczeń komputera docelowego, na którym dane są odzyskiwane, lub ustawienia zabezpieczeń, które mają zastosowanie do produktu w momencie utworzenia punktu odzyskiwania.

     Określ, czy po pomyślnym zakończeniu odzyskiwania zostanie wysłane **powiadomienie** .

     ![Powiadomienia o zewnętrznym odzyskiwaniu programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Ekran **Podsumowanie** zawiera listę opcji wybranych do tej pory. Po wybraniu opcji **Odzyskaj** dane zostaną odzyskane do odpowiedniej lokalizacji lokalnej.

    ![Podsumowanie opcji odzyskiwania zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Zadanie odzyskiwania można monitorować na karcie **monitorowanie** Azure Backup Server.
   >
   >

    ![Monitorowanie odzyskiwania](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Aby usunąć widok zewnętrznego serwera DPM, możesz wybrać opcję **Wyczyść zewnętrzny program DPM** na karcie **odzyskiwanie** serwera programu DPM.

    ![Wyczyść zewnętrzny program DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Rozwiązywanie problemów dotyczących komunikatów o błędach

| Nie. | Komunikat o błędzie | Kroki rozwiązywania problemów |
|:---:|:--- |:--- |
| 1. |Ten serwer nie jest zarejestrowany w magazynie określonym przez poświadczenia magazynu. |**Przyczyna:** Ten błąd pojawia się, gdy wybrany plik poświadczeń magazynu nie należy do magazynu Recovery Services skojarzonego z Azure Backup Server, na którym podjęto próbę odzyskania. <br> **Rozwiązanie:** Pobierz plik poświadczeń magazynu z magazynu Recovery Services, do którego zarejestrowano Azure Backup Server. |
| 2. |Odzyskiwalne dane nie są dostępne lub wybrany serwer nie jest serwerem DPM. |**Przyczyna:** Nie ma innych serwerów Azure Backup zarejestrowanych w magazynie Recovery Services lub serwery nie przesłały jeszcze metadanych lub wybrany serwer nie jest Azure Backup Serverem (przy użyciu systemu Windows Server lub klienta systemu Windows). <br> **Rozwiązanie:** Jeśli istnieją inne Azure Backup serwery zarejestrowane w magazynie Recovery Services, upewnij się, że jest zainstalowany najnowszy Agent Azure Backup. <br>Jeśli istnieją inne Azure Backup serwery zarejestrowane w magazynie Recovery Services, poczekaj na dzień po zakończeniu instalacji, aby rozpocząć proces odzyskiwania. Nocne zadanie przekaże metadane dla wszystkich chronionych kopii zapasowych do chmury. Dane będą dostępne do odzyskania. |
| 3. |Żaden inny serwer DPM nie jest zarejestrowany w tym magazynie. |**Przyczyna:** Nie ma innych serwerów Azure Backup zarejestrowanych w magazynie, z którego jest podejmowana próba odzyskania.<br>**Rozwiązanie:** Jeśli istnieją inne Azure Backup serwery zarejestrowane w magazynie Recovery Services, upewnij się, że jest zainstalowany najnowszy Agent Azure Backup.<br>Jeśli istnieją inne Azure Backup serwery zarejestrowane w magazynie Recovery Services, poczekaj na dzień po zakończeniu instalacji, aby rozpocząć proces odzyskiwania. Nocne zadanie przekazuje metadane dla wszystkich chronionych kopii zapasowych do chmury. Dane będą dostępne do odzyskania. |
| 4. |Podane hasło szyfrowania nie jest zgodne z hasłem skojarzonym z następującym serwerem: **\<server name>** |**Przyczyna:** Hasło szyfrowania używane w procesie szyfrowania danych z odzyskiwanych danych Azure Backup Server nie jest zgodne z podanym hasłem szyfrowania. Agent nie może odszyfrować danych, więc odzyskiwanie nie powiedzie się.<br>**Rozwiązanie:** Podaj dokładnie to samo hasło szyfrowania, które jest skojarzone z Azure Backup Server którego dane są odzyskiwane. |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi często zadawanymi pytaniami:

* [Często zadawane pytania](backup-azure-vm-backup-faq.yml) dotyczące kopii zapasowych maszyn wirtualnych platformy Azure
* [Często zadawane pytania](backup-azure-file-folder-backup-faq.md) dotyczące agenta usługi Azure Backup
