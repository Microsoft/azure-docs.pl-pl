---
title: Rozwiązywanie problemów z tworzeniem kopii zapasowej stanu systemu
description: W tym artykule dowiesz się, jak rozwiązywać problemy z tworzeniem kopii zapasowej stanu systemu dla lokalnych serwerów z systemem Windows.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 07e101fe87fb3f5db0bb716f0bc9ea6f8773aa3e
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518562"
---
# <a name="troubleshoot-system-state-backup"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowej stanu systemu

W tym artykule opisano rozwiązania problemów, które mogą wystąpić podczas korzystania z kopii zapasowej stanu systemu.

## <a name="basic-troubleshooting"></a>Podstawowe rozwiązywanie problemów

Przed rozpoczęciem rozwiązywania problemów z tworzeniem kopii zapasowej stanu systemu zalecamy wykonanie następujących kroków weryfikacji:

- [Upewnij Microsoft Azure agent usługi Recovery Services (MARS) jest aktualny](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Upewnij się, że istnieje łączność sieciowa między agentem usługi MARS i platformą Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Upewnij się, że usługa Microsoft Azure Recovery Services jest uruchomiona (w konsoli usługi). W razie potrzeby uruchom ponownie i spróbuj ponownie wykonać operację
- [Upewnij się, że 5–10% wolnego miejsca na woluminie jest dostępne na potrzeby lokalizacji folderu plików tymczasowych](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Sprawdź, czy inny proces lub oprogramowanie antywirusowe nie zakłóca działania usługi Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Zaplanowane tworzenie kopii zapasowej kończy się niepowodzeniem, ale ręczne tworzenie kopii zapasowej działa](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- Upewnij się, że w systemie operacyjnym zainstalowano najnowsze aktualizacje
- [Upewnij się, że nieobsługiwane dyski i pliki z nieobsługiwanymi atrybutami są wykluczone z kopii zapasowej](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Upewnij się, że **zegar systemowy** chronionego systemu ma skonfigurowaną prawidłową strefę czasową <br>
- [Upewnij się, że na serwerze zainstalowano program .NET Framework w wersji 4.5.2 lub nowszej](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Jeśli próbujesz ponownie zarejestrować **serwer w** magazynie, możesz: <br>
  - Upewnij się, że agent został odinstalowany na serwerze i usunięty z portalu <br>
  - Użyj tego samego hasła, które zostało początkowo użyte podczas rejestrowania serwera <br>
- Jeśli jest to kopia zapasowa w trybie offline, przed rozpoczęciem operacji tworzenia kopii zapasowej w trybie offline upewnij się, że program Azure PowerShell w wersji 3.7.0 jest zainstalowany zarówno na komputerze źródłowym, jak i na komputerze kopii zapasowej
- [Zagadnienia dotyczące uruchamiania agenta kopii zapasowej na maszynie wirtualnej platformy Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Ograniczenie

- Odzyskiwanie na inny sprzęt przy użyciu odzyskiwania stanu systemu nie jest zalecane przez firmę Microsoft
- Kopia zapasowa stanu systemu obsługuje obecnie "lokalne" serwery z systemem Windows. Ta funkcja nie jest dostępna dla maszyn wirtualnych platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem rozwiązywania problemów z kopią zapasową stanu systemu Azure Backup, wykonaj następujące sprawdzanie wymagań wstępnych.  

### <a name="verify-windows-server-backup-is-installed"></a>Sprawdź, Kopia zapasowa systemu Windows Server jest zainstalowany

Upewnij Kopia zapasowa systemu Windows Server, że program jest zainstalowany i włączony na serwerze. Aby sprawdzić stan instalacji, uruchom to polecenie programu PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Jeśli w danych wyjściowych **wyświetlany** jest stan instalacji jako **dostępny,** oznacza to, że funkcja tworzenia kopii zapasowej systemu Windows Server jest dostępna dla instalacji, ale nie jest zainstalowana na serwerze. Jeśli jednak Kopia zapasowa systemu Windows Server nie jest zainstalowany, użyj jednej z poniższych metod, aby go zainstalować.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metoda 1. Instalowanie Kopia zapasowa systemu Windows Server przy użyciu programu PowerShell

Aby zainstalować Kopia zapasowa systemu Windows Server przy użyciu programu PowerShell, uruchom następujące polecenie:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metoda 2. Instalowanie Kopia zapasowa systemu Windows Server przy użyciu Menedżer serwera

Aby zainstalować Kopia zapasowa systemu Windows Server przy użyciu Menedżer serwera, wykonaj następujące kroki:

1. W **programie Server Manger** wybierz **pozycję Dodaj role i funkcje.** Zostanie **wyświetlony kreator Dodawanie ról i** funkcji.

    ![Pulpit nawigacyjny](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Wybierz **pozycję Typ instalacji** i wybierz pozycję **Dalej.**

    ![Typ instalacji](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Wybierz serwer z puli serwerów i wybierz pozycję **Dalej.** W roli serwera pozostaw wybór domyślny i wybierz pozycję **Dalej.**
4. Wybierz **Kopia zapasowa systemu Windows Server** na **karcie Funkcje** i wybierz pozycję **Dalej.**

    ![Okno Wybierania funkcji](./media/backup-azure-system-state-troubleshoot/features.png)

5. Na karcie **Potwierdzenie** wybierz pozycję **Zainstaluj,** aby rozpocząć proces instalacji.
6. Na **karcie Wyniki** zostanie w nim Kopia zapasowa systemu Windows Server została pomyślnie zainstalowana w systemie Windows Server.

    ![Wyniki instalacji](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Uprawnienie do informacji o woluminie systemowym

Upewnij się, że system lokalny ma pełną kontrolę nad **folderem Informacje** o woluminie systemowym znajdującego się na woluminie, na którym zainstalowano system Windows. Zazwyczaj jest to **C:\System Volume Information**. Tworzenie kopii zapasowej systemu Windows Server może się nie powieść, jeśli powyższe uprawnienia nie są prawidłowo ustawione.

### <a name="dependent-services"></a>Usługi zależne

Upewnij się, że poniższe usługi są w stanie uruchomienia:

**Nazwa usługi** | **Typ uruchamiania**
--- | ---
Zdalne wywołanie procedury (RPC) | Automatyczny
COM+ Event System(EventSystem) | Automatyczny
System Event Notification Service (SENS) | Automatyczny
Kopiowanie woluminów w tle (VSS) | Ręcznie
Dostawca kopiowania oprogramowania w tle (SWPRV) firmy Microsoft | Ręcznie

### <a name="validate-windows-server-backup-status"></a>Sprawdzanie Kopia zapasowa systemu Windows Server stanu

Aby sprawdzić Kopia zapasowa systemu Windows Server stanu, wykonaj następujące czynności:

- Upewnij się, że program WSB PowerShell jest uruchomiony

  - Uruchom program PowerShell z podwyższonym poziomem uprawnień i upewnij się, że nie `Get-WBJob` zwraca on następującego błędu:

    > [!WARNING]
    > Get-WBJob: Termin "Get-WBJob" nie jest rozpoznawany jako nazwa polecenia cmdlet, funkcji, pliku skryptu lub programu obsługiwanego. Sprawdź pisownię nazwy lub jeśli ścieżka została uwzględniona, sprawdź, czy ścieżka jest poprawna, i spróbuj ponownie.

    - Jeśli ten błąd nie powiedzie się, zainstaluj ponownie Kopia zapasowa systemu Windows Server na maszynie serwera, jak wspomniano w kroku 1 wymagań wstępnych.

  - Upewnij się, że kopia zapasowa programu WSB działa prawidłowo, uruchamiając następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Zastąp znak X literą dysku woluminu, w którym chcesz przechowywać kopię zapasową stanu systemu obrazu.

    - Okresowo sprawdzaj stan zadania, uruchamiając polecenie `Get-WBJob` w programie PowerShell z podwyższonym poziomem uprawnień
    - Po zakończeniu zadania tworzenia kopii zapasowej sprawdź końcowy stan zadania, uruchamiając `Get-WBJob -Previous 1` polecenie

Jeśli zadanie zakończy się niepowodzeniem, wskazuje problem z programem WSB, który może spowodować niepowodzenie tworzenia kopii zapasowych stanu systemu agenta MARS.

## <a name="common-errors"></a>Typowe błędy

### <a name="vss-writer-timeout-error"></a>Błąd limitu czasu usługi VSS Writer

| Objaw | Przyczyna | Rozwiązanie
| -- | -- | --
| — Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: "Zadanie usługi WSB nie powiodło się z błędami usługi VSS. Sprawdź dzienniki zdarzeń usługi VSS, aby rozwiązać ten problem"<br/><br/> - Następujący dziennik błędów znajduje się w dziennikach zdarzeń aplikacji usługi VSS: "A VSS writer has rejected an event with error 0x800423f2, the writer's timeout expired between the Freeze and Freezew events".| Nie można ukończyć zapisu usługi VSS w odpowiednim czasie z powodu braku zasobów procesora CPU i pamięci na maszynie <br/><br/> Inne oprogramowanie do tworzenia kopii zapasowych już korzysta ze writera usługi VSS, ponieważ nie można ukończyć operacji migawki wyniku dla tej kopii zapasowej | Poczekaj, aż procesor CPU/pamięć zostanie uwolniona w systemie lub przerwać procesy, które mają za dużo pamięci/procesora CPU, i spróbuj ponownie wykonać operację. <br/><br/>  Poczekaj na ukończenie trwającej kopii zapasowej i spróbuj wykonać operację w późniejszym momencie, gdy na maszynie nie są uruchomione żadne kopie zapasowe.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Niewystarczająca ilość miejsca na dysku do powiększania kopii w tle

| Objaw | Rozwiązanie
| -- | --
| - Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: Tworzenie kopii zapasowej nie powiodło się, ponieważ wolumin kopii w tle nie mógł rosnąć z powodu niewystarczającej ilości miejsca na dysku na woluminach zawierających pliki systemowe <br/><br/> - Następujący dziennik błędów/ostrzeżeń znajduje się w dziennikach zdarzeń systemu volsnap: "Brak wystarczającej ilości miejsca na dysku na woluminie C: aby powiększać magazyn kopii w tle dla kopii w tle C: z powodu tego błędu wszystkie kopie w tle woluminu C: są narażone na usunięcie" | - Wolne miejsce w wyróżniony wolumin w dzienniku zdarzeń, dzięki czemu jest wystarczająca ilość miejsca dla kopii w tle, aby rosnąć podczas tworzenia kopii zapasowej jest w toku <br/><br/> — Podczas konfigurowania miejsca na kopie w tle można ograniczyć ilość miejsca używanego do kopiowania w tle. Aby uzyskać więcej informacji, zobacz ten [artykuł](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>Zablokowana partycja EFI

| Objaw | Rozwiązanie
| -- | --
| Agent MARS kończy się niepowodzeniem z komunikatem o błędzie: "Tworzenie kopii zapasowej stanu systemu nie powiodło się, ponieważ partycja systemowa EFI jest zablokowana. Może to być spowodowane dostępem do partycji systemowej przez zabezpieczenia innych firm lub kopię zapasową oprogramowania" | — Jeśli problem jest spowodowany przez oprogramowanie zabezpieczające innej firmy, należy skontaktować się z dostawcą oprogramowania antywirusowego, aby umożliwić agentowi MARS <br/><br/> — Jeśli jest uruchomione oprogramowanie do tworzenia kopii zapasowych innej firmy, poczekaj na jego zakończenie, a następnie ponów próbę wykonania kopii zapasowej

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat stanu systemu Windows w Resource Manager, zobacz Back up Windows Server System State (Resource Manager kopii [zapasowej stanu systemu Windows Server).](backup-azure-system-state.md)
