---
title: Rozwiązywanie problemów z kopią zapasową stanu systemu
description: W tym artykule dowiesz się, jak rozwiązywać problemy z tworzeniem kopii zapasowej stanu systemu dla lokalnych serwerów z systemem Windows.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 7c8e68da1c5da7b25d1385a82bf7dcc2f876306d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89376285"
---
# <a name="troubleshoot-system-state-backup"></a>Rozwiązywanie problemów z kopią zapasową stanu systemu

W tym artykule opisano rozwiązania problemów, które mogą występować podczas tworzenia kopii zapasowej stanu systemu.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Przed rozpoczęciem rozwiązywania problemów z kopiami zapasowymi stanu systemu zalecamy wykonanie następujących kroków weryfikacji:

- [Upewnij się, że agent Microsoft Azure Recovery Services (MARS) jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zapewnianie łączności sieciowej między agentem MARS i platformą Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Upewnij się, że usługa Microsoft Azure Recovery Services jest uruchomiona (w konsoli usługi). W razie potrzeby uruchom ponownie, a następnie spróbuj ponownie wykonać operację.
- [Upewnij się, że ilość wolnego miejsca na woluminie 5-10% jest dostępna w lokalizacji folderu tymczasowego](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Sprawdź, czy inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Zaplanowane tworzenie kopii zapasowej kończy się niepowodzeniem, ale ręczne tworzenie kopii zapasowej działa](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- Upewnij się, że w systemie operacyjnym zainstalowano najnowsze aktualizacje
- [Upewnij się, że nieobsługiwane dyski i pliki z nieobsługiwanymi atrybutami są wyłączone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Upewnij się, że **zegar systemowy** w chronionym systemie jest skonfigurowany pod kątem poprawnej strefy czasowej <br>
- [Upewnij się, że na serwerze zainstalowano program .NET Framework w wersji 4.5.2 lub nowszej](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Jeśli próbujesz ponownie **zarejestrować serwer** w magazynie,: <br>
  - Upewnij się, że agent zostanie odinstalowany na serwerze i usunięty z portalu <br>
  - Użyj tego samego hasła, które zostało początkowo użyte podczas rejestrowania serwera <br>
- Jeśli jest to kopia zapasowa offline, przed rozpoczęciem operacji tworzenia kopii zapasowej w trybie offline upewnij się, że Azure PowerShell wersja 3.7.0 jest zainstalowana na komputerze źródłowym i skopiuj
- [Uwzględnianie, gdy Agent kopii zapasowej jest uruchomiony na maszynie wirtualnej platformy Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Ograniczenie

- Odzyskiwanie do innego sprzętu przy użyciu funkcji odzyskiwania stanu systemu nie jest zalecane przez firmę Microsoft
- Kopia zapasowa stanu systemu obsługuje obecnie serwery z systemem Windows "lokalne". Ta funkcja jest niedostępna dla maszyn wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem rozwiązywania problemów z kopiami zapasowymi stanu systemu za pomocą Azure Backup wykonaj następujące czynności.  

### <a name="verify-windows-server-backup-is-installed"></a>Sprawdź, czy Kopia zapasowa systemu Windows Server jest zainstalowana

Upewnij się, że na serwerze jest zainstalowana i włączona Kopia zapasowa systemu Windows Server. Aby sprawdzić stan instalacji, uruchom następujące polecenie programu PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Jeśli w danych wyjściowych zostanie wyświetlony **stan instalacji** **dostępny**, oznacza to, że funkcja kopia zapasowa systemu Windows Server jest dostępna dla instalacji, ale nie jest zainstalowana na serwerze. Jeśli jednak Kopia zapasowa systemu Windows Server nie jest zainstalowana, użyj jednej z poniższych metod, aby ją zainstalować.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metoda 1. Instalowanie Kopia zapasowa systemu Windows Server przy użyciu programu PowerShell

Aby zainstalować Kopia zapasowa systemu Windows Server przy użyciu programu PowerShell, uruchom następujące polecenie:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metoda 2. Instalowanie Kopia zapasowa systemu Windows Server przy użyciu Menedżer serwera

Aby zainstalować Kopia zapasowa systemu Windows Server przy użyciu Menedżer serwera, wykonaj następujące czynności:

1. W oknie **Menedżer serwera** wybierz pozycję **Dodaj role i funkcje**. Zostanie wyświetlony **Kreator dodawania ról i funkcji** .

    ![Pulpit nawigacyjny](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Wybierz **Typ instalacji** i wybierz przycisk **dalej**.

    ![Typ instalacji](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Wybierz serwer z puli serwerów, a następnie wybierz pozycję **dalej**. W roli serwera pozostaw wybór domyślny i wybierz pozycję **dalej**.
4. Wybierz pozycję **kopia zapasowa systemu Windows Server** na karcie **funkcje** i wybierz pozycję **dalej**.

    ![Wybierz okno funkcji](./media/backup-azure-system-state-troubleshoot/features.png)

5. Na karcie **potwierdzenie** wybierz pozycję **Zainstaluj** , aby rozpocząć proces instalacji.
6. Na karcie **wyniki** zostanie wyświetlona funkcja kopia zapasowa systemu Windows Server pomyślnie zainstalowana na serwerze z systemem Windows.

    ![Wyniki instalacji](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Uprawnienie do informacji o woluminie systemowym

Upewnij się, że SYSTEM lokalny ma pełną kontrolę nad folderem **informacji o woluminie systemowym** , który znajduje się w woluminie, na którym zainstalowano system Windows. Zwykle jest to **C:\System informacje o woluminie**. Kopia zapasowa systemu Windows Server może zakończyć się niepowodzeniem, jeśli powyższe uprawnienia nie są ustawione prawidłowo.

### <a name="dependent-services"></a>Usługi zależne

Upewnij się, że poniższe usługi są w stanie uruchomienia:

**Nazwa usługi** | **Typ uruchomienia**
--- | ---
Zdalne wywołanie procedury (RPC) | Automatyczny
System zdarzeń modelu COM+ (EventSystem) | Automatyczny
Usługa powiadamiania o zdarzeniach systemowych (SENS) | Automatyczny
Kopiowanie woluminów w tle (VSS) | Ręcznie
Dostawca kopiowania oprogramowania w tle firmy Microsoft (SWPRV) | Ręcznie

### <a name="validate-windows-server-backup-status"></a>Weryfikuj stan Kopia zapasowa systemu Windows Server

Aby sprawdzić stan Kopia zapasowa systemu Windows Server, wykonaj następujące czynności:

- Upewnij się, że program WSB PowerShell jest uruchomiony

  - Uruchom `Get-WBJob` program PowerShell z podwyższonym poziomem uprawnień i upewnij się, że nie zwróci następującego błędu:

    > [!WARNING]
    > Get-WBJob: termin "Get-WBJob" nie jest rozpoznawany jako nazwa polecenia cmdlet, funkcji, pliku skryptu ani programu interoperacyjnego. Sprawdź pisownię nazwy lub, jeśli ścieżka została uwzględniona, sprawdź, czy ścieżka jest poprawna, i spróbuj ponownie.

    - Jeśli ten błąd nie powiedzie się, należy ponownie zainstalować funkcję Kopia zapasowa systemu Windows Server na komputerze serwera, jak wspomniano w kroku 1 wymagań wstępnych.

  - Upewnij się, że kopia zapasowa WSB działa prawidłowo, uruchamiając następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Zastąp znak X literą dysku woluminu, na którym chcesz przechowywać kopię zapasową stanu systemu.

    - Okresowe sprawdzanie stanu zadania przez uruchomienie `Get-WBJob` polecenia z podwyższonym poziomem uprawnień programu PowerShell
    - Po zakończeniu zadania tworzenia kopii zapasowej Sprawdź stan końcowy zadania, uruchamiając `Get-WBJob -Previous 1` polecenie

Jeśli zadanie nie powiedzie się, wskazuje to na problem z WSB, który spowoduje niepowodzenie tworzenia kopii zapasowych stanu systemu agenta MARS.

## <a name="common-errors"></a>Typowe błędy

### <a name="vss-writer-timeout-error"></a>Błąd limitu czasu składnika zapisywania usługi VSS

| Objaw | Przyczyna | Rozwiązanie
| -- | -- | --
| -Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: "zadanie WSB nie powiodło się z błędami usługi VSS. Sprawdź dzienniki zdarzeń usługi VSS, aby rozwiązać ten problem.<br/><br/> — Następujący dziennik błędów znajduje się w dziennikach zdarzeń aplikacji usługi VSS: "składnik zapisywania usługi VSS odrzucił zdarzenie z błędem 0x800423f2, upłynął limit czasu modułu zawieszania i odblokowywania."| Nie można wykonać składnika zapisywania usługi VSS w czasie z powodu braku zasobów procesora i pamięci na maszynie <br/><br/> Inne oprogramowanie do tworzenia kopii zapasowych korzysta już z składnika zapisywania usługi VSS, ponieważ nie można ukończyć operacji migawki wyniku dla tej kopii zapasowej | Poczekaj na zwolnienie procesora CPU/pamięci w systemie lub Przerwij procesy, które zajmują zbyt dużo pamięci/procesora, i spróbuj wykonać operację ponownie. <br/><br/>  Poczekaj na zakończenie trwającej kopii zapasowej i spróbuj wykonać operację w późniejszym czasie, gdy na maszynie nie są uruchomione żadne kopie zapasowe.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Za mało miejsca na dysku, aby zwiększyć ilość kopii w tle

| Objaw | Rozwiązanie
| -- | --
| -Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: wykonywanie kopii zapasowej nie powiodło się, ponieważ nie można było zwiększyć woluminu kopii w tle z powodu niewystarczającej ilości miejsca na woluminach <br/><br/> — Następujący dziennik błędów/ostrzeżeń jest obecny w dzienniku zdarzeń systemu Volsnap: "za mało miejsca na dysku w woluminie C:, aby zwiększyć magazyn kopii w tle dla kopii w tle C: z powodu tego błędu wszystkie kopie w tle woluminu C: są zagrożone. | -Zwolnij miejsce na wyróżnionym woluminie w dzienniku zdarzeń, tak że jest wystarczająca ilość miejsca do zwiększenia kopii w tle, gdy kopia zapasowa jest w toku <br/><br/> — Podczas konfigurowania obszaru kopiowania w tle możemy ograniczyć ilość miejsca używanego do kopiowania w tle. Aby uzyskać więcej informacji, zobacz ten [artykuł](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>Zablokowana partycja EFI

| Objaw | Rozwiązanie
| -- | --
| Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: "kopia zapasowa stanu systemu nie powiodła się, ponieważ partycja systemowa EFI jest Może to być spowodowane dostępem do partycji systemowej przez zabezpieczenia innej firmy lub oprogramowanie do tworzenia kopii zapasowych. | — Jeśli problem jest spowodowany przez oprogramowanie zabezpieczeń innej firmy, należy skontaktować się z dostawcą oprogramowania antywirusowego, aby umożliwić mu agenta MARS <br/><br/> — Jeśli działa oprogramowanie do tworzenia kopii zapasowych innej firmy, zaczekaj na jego zakończenie, a następnie ponów próbę wykonania kopii zapasowej

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat stanu systemu Windows w ramach wdrażania Menedżer zasobów, zobacz [Tworzenie kopii zapasowej stanu systemu Windows Server](backup-azure-system-state.md) .
