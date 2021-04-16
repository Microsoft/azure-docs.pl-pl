---
title: Odzyskiwanie danych z Azure Backup Server
description: Odzyskaj chronione dane do magazynu usługi Recovery Services z dowolnego Azure Backup Server zarejestrowanego w tym magazynie.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 780c88175397fb06e704e57062ae5c6d3b93d8b8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519592"
---
# <a name="recover-data-from-azure-backup-server"></a>Odzyskiwanie danych z usługi Azure Backup Server

Możesz użyć Azure Backup Server, aby odzyskać dane, których kopię zapasową masz kopię zapasową, do magazynu usługi Recovery Services. Proces ten jest zintegrowany z konsolą zarządzania Azure Backup Server i jest podobny do przepływu pracy odzyskiwania dla innych składników Azure Backup zarządzania.

> [!NOTE]
> Ten artykuł dotyczy wersji [System Center Data Protection Manager 2012 R2](https://support.microsoft.com/kb/3065246)z aktualizacją UR7 lub nowszej w połączeniu z najnowszym [agentem Azure Backup.](https://aka.ms/azurebackup_agent)
>
>

Aby odzyskać dane z Azure Backup Server:

1. Na karcie **Odzyskiwanie** konsoli zarządzania Azure Backup Server wybierz pozycję "Dodaj zewnętrzny **program DPM"** (w lewym górnym rogu ekranu).

    ![Dodawanie zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Pobierz  nowe poświadczenia magazynu z magazynu skojarzonego z magazynem **usługi Azure Backup Server,** w którym są odzyskiwane dane, wybierz serwer Azure Backup Server z listy  serwerów usługi Azure Backup zarejestrowanych w magazynie usługi Recovery Services i podaj hasło szyfrowania skojarzone z serwerem, którego dane są odzyskiwane.

    ![Poświadczenia zewnętrzne programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Tylko Azure Backup serwerów skojarzonych z tym samym magazynem rejestracji mogą odzyskać dane innych użytkowników.
   >
   >

    Po pomyślnym Azure Backup Server serwera zewnętrznego można przeglądać dane serwera zewnętrznego i lokalnego Azure Backup Server na **karcie Odzyskiwanie.**
3. Przejrzyj listę dostępnych serwerów produkcyjnych chronionych przez zewnętrzne Azure Backup Server i wybierz odpowiednie źródło danych.

    ![Przeglądanie zewnętrznego serwera DPM](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Wybierz **miesiąc i rok z** listy rozwijanej  **Punkty** odzyskiwania, wybierz wymaganą datę odzyskiwania dla czasu utworzenia punktu odzyskiwania, a następnie wybierz pozycję **Godzina odzyskiwania.**

    W dolnym okienku zostanie wyświetlona lista plików i folderów, które można przeglądać i odzyskiwać w dowolnej lokalizacji.

    ![Zewnętrzne punkty odzyskiwania serwera PROGRAMU DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Kliknij prawym przyciskiem myszy odpowiedni element i wybierz polecenie **Odzyskaj.**

    ![Odzyskiwanie zewnętrzne programu DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Przejrzyj wybór **opcji Odzyskaj.** Sprawdź dane i czas odzyskania kopii zapasowej, a także źródło, z którego została utworzona kopia zapasowa. Jeśli wybór jest niepoprawny, wybierz pozycję **Anuluj,** aby wrócić do karty odzyskiwania, aby wybrać odpowiedni punkt odzyskiwania. Jeśli wybór jest poprawny, wybierz pozycję **Dalej.**

    ![Podsumowanie odzyskiwania zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Wybierz **pozycję Odzyskaj do lokalizacji alternatywnej.** **Przejdź** do poprawnej lokalizacji odzyskiwania.

    ![Zewnętrzna lokalizacja odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Wybierz opcję powiązaną z **tworzeniem kopii,** **pomiń** lub **zastąp .**

   * **Utwórz kopię** — tworzy kopię pliku w przypadku kolizji nazwy.
   * **Pomiń** — jeśli istnieje kolizja nazwy, plik nie zostanie odzyskany, co spowoduje pozostawinie oryginalnego pliku.
   * **Zastąp** — w przypadku kolizji nazwy zastępuje on istniejącą kopię pliku.

     Wybierz odpowiednią opcję, aby **przywrócić zabezpieczenia.** Można zastosować ustawienia zabezpieczeń komputera docelowego, na którym są odzyskiwane dane, lub ustawienia zabezpieczeń, które miały zastosowanie do produktu w momencie utworzenia punktu odzyskiwania.

     Określ, czy **po pomyślnym** zakończeniu odzyskiwania zostanie wysłane powiadomienie.

     ![Zewnętrzne powiadomienia dotyczące odzyskiwania programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Na **ekranie** Podsumowanie są dostępne opcje wybrane do tej pory. Po wybraniu opcji **Odzyskaj** dane są odzyskiwane do odpowiedniej lokalizacji lokalnej.

    ![Podsumowanie opcji odzyskiwania zewnętrznego programu DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Zadanie odzyskiwania można monitorować na **karcie Monitorowanie** Azure Backup Server.
   >
   >

    ![Monitorowanie odzyskiwania](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Możesz wybrać pozycję **Wyczyść zewnętrzny program DPM** na **karcie Odzyskiwanie** serwera DPM, aby usunąć widok zewnętrznego serwera DPM.

    ![Wyczyść zewnętrzny program DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Rozwiązywanie problemów z komunikatami o błędach

| Nie. | Komunikat o błędzie | Kroki rozwiązywania problemów |
|:---:|:--- |:--- |
| 1. |Ten serwer nie jest zarejestrowany w magazynie określonym przez poświadczenia magazynu. |**Przyczyna:** Ten błąd występuje, gdy wybrany plik poświadczeń magazynu nie należy do magazynu usługi Recovery Services skojarzonego z Azure Backup Server, na którym podejmowana jest próba odzyskiwania. <br> **Rozwiązanie:** Pobierz plik poświadczeń magazynu z magazynu usługi Recovery Services, w którym Azure Backup Server magazyn. |
| 2. |Dane możliwe do odzyskania są niedostępne lub wybrany serwer nie jest serwerem programu DPM. |**Przyczyna:** Nie ma żadnych innych serwerów Azure Backup zarejestrowanych w magazynie usługi Recovery Services lub serwery nie zostały jeszcze przekazane metadanych lub wybrany serwer nie jest serwerem Azure Backup Server (przy użyciu systemu Windows Server lub klienta systemu Windows). <br> **Rozwiązanie:** Jeśli istnieją inne serwery Azure Backup zarejestrowane w magazynie usługi Recovery Services, upewnij się, że zainstalowano Azure Backup agenta usługi Recovery Services. <br>Jeśli istnieją inne serwery Azure Backup zarejestrowane w magazynie usługi Recovery Services, poczekaj dzień po instalacji, aby rozpocząć proces odzyskiwania. Nocne zadanie przekaże metadane dla wszystkich chronionych kopii zapasowych do chmury. Dane będą dostępne do odzyskania. |
| 3. |Żaden inny serwer programu DPM nie jest zarejestrowany w tym magazynie. |**Przyczyna:** Nie ma żadnych innych serwerów Azure Backup, które są zarejestrowane w magazynie, z którego jest podejmowana próba odzyskiwania.<br>**Rozwiązanie:** Jeśli istnieją inne serwery Azure Backup zarejestrowane w magazynie usługi Recovery Services, upewnij się, że zainstalowano Azure Backup agenta usługi Recovery Services.<br>Jeśli istnieją inne serwery Azure Backup zarejestrowane w magazynie usługi Recovery Services, poczekaj dzień po instalacji, aby rozpocząć proces odzyskiwania. Nocne zadanie przesyła metadane wszystkich chronionych kopii zapasowych do chmury. Dane będą dostępne do odzyskania. |
| 4. |Podane hasło szyfrowania nie jest zgodne z kodem dostępu skojarzonym z następującym serwerem: **\<server name>** |**Przyczyna:** Hasło szyfrowania używane w procesie szyfrowania danych z danych usługi Azure Backup Server, które są odzyskiwane, nie jest zgodne z dostarczonym kodem dostępu szyfrowania. Agent nie może odszyfrować danych, dlatego odzyskiwanie kończy się niepowodzeniem.<br>**Rozwiązanie:** Podaj dokładnie to samo hasło szyfrowania skojarzone z Azure Backup Server, którego dane są odzyskiwane. |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi często zadawanymi pytaniami:

* [Często zadawane pytania dotyczące](backup-azure-vm-backup-faq.yml) kopii zapasowych maszyn wirtualnych platformy Azure
* [Często zadawane pytania](backup-azure-file-folder-backup-faq.yml) dotyczące agenta usługi Azure Backup
