---
title: Przygotowywanie i dostosowywanie głównego obrazu wirtualnego dysku twardego — Azure
description: Przygotowywanie, dostosowywanie i przekazywanie obrazu wzorca pulpitu wirtualnego systemu Windows na platformę Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ca92e6e888ead6bbd79c7a8d524516ee95e8c45d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092935"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Przygotowywanie i dostosowywanie głównego obrazu wirtualnego dysku twardego

W tym artykule przedstawiono sposób przygotowania obrazu głównego wirtualnego dysku twardego (VHD) do przekazania do platformy Azure, w tym sposobu tworzenia maszyn wirtualnych i instalowania na nich oprogramowania. Te instrukcje dotyczą konfiguracji specyficznej dla pulpitu wirtualnego systemu Windows, która może być używana z istniejącymi procesami w organizacji.

>[!IMPORTANT]
>Zalecamy używanie obrazu z galerii obrazów platformy Azure. Jeśli jednak potrzebujesz użyć niestandardowego obrazu, upewnij się, że na maszynie wirtualnej nie jest jeszcze zainstalowany agent pulpitu wirtualnego systemu Windows. Użycie niestandardowego obrazu z agentem pulpitu wirtualnego systemu Windows może spowodować problemy z obrazem, np. blokowanie rejestracji i zapobieganie nawiązywaniu połączeń sesji użytkownika.  

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Wiele sesji systemu Windows 10 Enterprise jest dostępnych w galerii obrazów platformy Azure. Dostępne są dwie opcje dostosowywania tego obrazu.

Pierwszą opcją jest zainicjowanie obsługi administracyjnej maszyny wirtualnej na platformie Azure, postępując zgodnie z instrukcjami w temacie [Tworzenie maszyny wirtualnej z zarządzanego obrazu](../virtual-machines/windows/create-vm-generalized-managed.md), a następnie przejście do [przygotowania i instalacji oprogramowania](set-up-customize-master-image.md#software-preparation-and-installation).

Druga opcja polega na utworzeniu obrazu lokalnie przez pobranie obrazu, udostępnienie maszyny wirtualnej funkcji Hyper-V i dostosowanie jej do Twoich potrzeb, co obejmuje poniższą sekcję.

### <a name="local-image-creation"></a>Tworzenie obrazu lokalnego

Po pobraniu obrazu do lokalizacji lokalnej Otwórz **Menedżera funkcji Hyper-V** , aby utworzyć maszynę wirtualną z SKOPIOWAnym dyskiem VHD. Poniższe instrukcje są prostą wersją, ale więcej szczegółowych instrukcji można znaleźć w temacie [Tworzenie maszyny wirtualnej w funkcji Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Aby utworzyć maszynę wirtualną za pomocą skopiowanego wirtualnego dysku twardego:

1. Otwórz **Kreatora nowej maszyny wirtualnej**.

2. Na stronie Określanie generacji wybierz opcję **generacja 1**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę Określanie generacji. Zaznaczona jest opcja "generacja 1".](media/a41174fd41302a181e46385e1e701975.png)

3. W obszarze Typ punktu kontrolnego Wyłącz punkty kontrolne, usuwając zaznaczenie pola wyboru.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający sekcję typ punktu kontrolnego na stronie punktów kontrolnych.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Aby wyłączyć punkty kontrolne, można również uruchomić następujące polecenie cmdlet w programie PowerShell.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Dysk stały

Jeśli utworzysz maszynę wirtualną na podstawie istniejącego wirtualnego dysku twardego, domyślnie zostanie utworzony dysk dynamiczny. Można go zmienić na dysk stały, wybierając pozycję **Edytuj dysk...** , jak pokazano na poniższej ilustracji. Aby uzyskać bardziej szczegółowe instrukcje, zobacz [Przygotowywanie wirtualnego dysku twardego systemu Windows lub dysku VHDX do przekazania do platformy Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający opcję Edytuj dysk.](media/35772414b5a0f81f06f54065561d1414.png)

Możesz również uruchomić następujące polecenie cmdlet programu PowerShell, aby zmienić dysk na dysk stały.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Przygotowanie oprogramowania i instalacja

W tej sekcji opisano sposób przygotowania i instalacji usługi FSLogix oraz usługi Windows Defender oraz niektórych podstawowych opcji konfiguracji aplikacji i rejestru obrazu.

Jeśli instalujesz Microsoft 365 aplikacje dla przedsiębiorstw i usługi OneDrive na maszynie wirtualnej, przejdź do pozycji [Zainstaluj pakiet Office na głównym obrazie wirtualnego dysku twardego](install-office-on-wvd-master-image.md) i postępuj zgodnie z instrukcjami, aby zainstalować aplikacje. Po zakończeniu Wróć do tego artykułu.

Jeśli użytkownicy muszą uzyskać dostęp do określonych aplikacji biznesowych, zalecamy ich zainstalowanie po zakończeniu instrukcji tej sekcji.

### <a name="set-up-user-profile-container-fslogix"></a>Konfigurowanie kontenera profilu użytkownika (FSLogix)

Aby dołączyć kontener FSLogix jako część obrazu, postępuj zgodnie z instrukcjami w temacie [Tworzenie kontenera profilu dla puli hostów przy użyciu udziału plików](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Funkcję kontenera FSLogix można testować za pomocą [tego przewodnika Szybki Start](/fslogix/configure-cloud-cache-tutorial/).

### <a name="configure-windows-defender"></a>Konfiguruj usługę Windows Defender

Jeśli usługa Windows Defender jest skonfigurowana na maszynie wirtualnej, upewnij się, że jest ona skonfigurowana pod kątem braku skanowania całej zawartości plików VHD i VHDX podczas załączników.

Ta konfiguracja usuwa tylko skanowanie plików VHD i VHDX podczas załączników, ale nie wpływa na skanowanie w czasie rzeczywistym.

Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania usługi Windows Defender w systemie Windows Server, zobacz [Konfigurowanie wykluczeń programu antywirusowego Windows Defender w systemie Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Aby dowiedzieć się więcej o tym, jak skonfigurować usługę Windows Defender do wykluczania niektórych plików z skanowania, zobacz [Konfigurowanie i weryfikowanie wykluczeń na podstawie rozszerzenia pliku i lokalizacji folderu](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Wyłącz aktualizacje automatyczne

Aby wyłączyć aktualizacje automatyczne za pośrednictwem zasady grupy lokalnego:

1. Otwórz **Edytor lokalnych zasad grupy \\ Szablony administracyjne \\ składników systemu Windows \\ Windows Update**.
2. Kliknij prawym przyciskiem myszy pozycję **Skonfiguruj automatyczną aktualizację** i ustaw ją na wartość **wyłączone**.

Aby wyłączyć aktualizacje automatyczne, można również uruchomić następujące polecenie w wierszu polecenia.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Określ układ startowy dla komputerów z systemem Windows 10 (opcjonalnie)

Uruchom to polecenie, aby określić układ startowy dla komputerów z systemem Windows 10.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Konfigurowanie przekierowania strefy czasowej

Przekierowania strefy czasowej można wymusić na poziomie zasady grupy, ponieważ wszystkie maszyny wirtualne w puli hostów są częścią tej samej grupy zabezpieczeń.

Aby przekierować strefy czasowe:

1. Na serwerze Active Directory Otwórz **Konsola zarządzania zasadami grupy**.
2. Rozwiń domenę i zasady grupy obiekty.
3. Kliknij prawym przyciskiem myszy **obiekt zasady grupy** , który został utworzony dla ustawień zasad grupy, a następnie wybierz polecenie **Edytuj**.
4. W **Edytor zarządzania zasadami grupy** przejdź do węzła zasady **konfiguracji**,  >    >  **Szablony administracyjne**  >  **składniki systemu Windows**  >  **usługi pulpitu zdalnego**  >  **pulpit zdalny Sesja hosta sesji**  >  **i przekierowania zasobów**.
5. Włącz ustawienie **Zezwalaj na przekierowywanie strefy czasowej** .

Możesz również uruchomić to polecenie na głównym obrazie, aby przekierować strefy czasowe:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Wyłącz wykrywanie magazynu

W przypadku hosta sesji usług pulpitu wirtualnego z systemem Windows 10 Enterprise lub Windows 10 Enterprise zalecamy wyłączenie wykrywania magazynu. Czujnik magazynu można wyłączyć w menu Ustawienia w obszarze **Magazyn**, jak pokazano na poniższym zrzucie ekranu:

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający menu magazyn w obszarze Ustawienia. Opcja "czujnik magazynu" jest wyłączona.](media/storagesense.png)

Możesz również zmienić to ustawienie za pomocą rejestru, uruchamiając następujące polecenie:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Uwzględnij dodatkową obsługę języka

Ten artykuł nie obejmuje konfigurowania języka i obsługi regionalnej. Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Dodawanie języków do obrazów systemu Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Funkcje na żądanie](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Funkcje języka i regionu na żądanie (FDZ)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Inne aplikacje i konfiguracja rejestru

W tej sekcji opisano konfigurację aplikacji i systemu operacyjnego. Wszystkie konfiguracje w tej sekcji są wykonywane za pomocą wpisów rejestru, które mogą być wykonywane przez narzędzia wiersza polecenia i regedit.

>[!NOTE]
>Najlepsze rozwiązania można zaimplementować w konfiguracji z obiektami zasady grupy (GPO) lub importami rejestru. Administrator może wybrać jedną z opcji na podstawie wymagań organizacji.

Aby uzyskać informacje o kolekcji centrów danych telemetrycznych w wielu sesjach systemu Windows 10 Enterprise, uruchom następujące polecenie:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Uruchom następujące polecenie, aby naprawić awarie programu Watson:

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Wprowadź następujące polecenia w Edytorze rejestru, aby rozwiązać problem z obsługą rozpoznawania 5 k. Aby można było włączyć stos równoległy, należy uruchomić polecenia.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Przygotowywanie obrazu do przekazania do platformy Azure

Po zakończeniu konfiguracji i zainstalowaniu wszystkich aplikacji postępuj zgodnie z instrukcjami w temacie [przygotowanie wirtualnego dysku twardego systemu Windows lub dysku VHDX do przekazania do platformy Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) w celu przygotowania obrazu.

Po przygotowaniu obrazu do przekazania upewnij się, że maszyna wirtualna pozostaje w stanie wyłączenia lub cofnięcia przydziału.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Przekazywanie obrazu głównego do konta magazynu na platformie Azure

Ta sekcja ma zastosowanie tylko wtedy, gdy obraz wzorcowy został utworzony lokalnie.

Poniższe instrukcje przedstawiają sposób przekazania obrazu wzorcowego do konta usługi Azure Storage. Jeśli nie masz jeszcze konta usługi Azure Storage, postępuj zgodnie z instrukcjami w [tym artykule](/azure/developer/javascript/tutorial-vscode-static-website-node-03) , aby go utworzyć.

1. Przekonwertuj obraz maszyny wirtualnej (VHD) na stały, jeśli jeszcze tego nie zrobiono. Jeśli nie przekonwertujesz obrazu na naprawione, nie można pomyślnie utworzyć obrazu.

2. Przekaż wirtualny dysk twardy do kontenera obiektów BLOB na koncie magazynu. Możesz szybko przekazać za pomocą [narzędzia Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/). Aby dowiedzieć się więcej o narzędziu Eksplorator usługi Storage, zobacz [ten artykuł](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows).

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający okno wyszukiwania narzędzia Eksplorator usługi Microsoft Azure Storage. Pole wyboru "Przekaż pliki VHD lub VHDX jako stronicowe obiekty blob (zalecane)" jest zaznaczone.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Następnie przejdź do Azure Portal w przeglądarce i wyszukaj ciąg "images" (obrazy). Wyszukiwanie powinno prowadzić do strony **Tworzenie obrazu** , jak pokazano na poniższym zrzucie ekranu:

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę Tworzenie obrazu Azure Portal uzupełniony o przykładowe wartości obrazu.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Po utworzeniu obrazu powinno zostać wyświetlone powiadomienie podobne do poniższego zrzutu ekranu:

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający powiadomienie "pomyślnie utworzono obraz".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz obraz, możesz utworzyć lub zaktualizować pule hostów. Aby dowiedzieć się więcej na temat tworzenia i aktualizowania pul hostów, zobacz następujące artykuły:

- [Tworzenie puli hostów za pomocą szablonu usługi Azure Resource Manager](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [Samouczek: Tworzenie puli hostów za pomocą witryny Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Tworzenie puli hostów przy użyciu programu PowerShell](create-host-pools-powershell.md)
- [Tworzenie kontenera profilu dla puli hostów za pomocą udziału plików](create-host-pools-user-profile.md)
- [Konfigurowanie metody równoważenia obciążenia usługi Windows Virtual Desktop](configure-host-pool-load-balancing.md)

Jeśli podczas przygotowywania lub dostosowywania obrazu wirtualnego dysku twardego wystąpił problem z łącznością, zapoznaj się z [przewodnikiem rozwiązywania problemów](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) , aby uzyskać pomoc.
