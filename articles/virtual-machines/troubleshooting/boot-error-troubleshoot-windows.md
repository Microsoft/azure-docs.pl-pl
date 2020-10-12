---
title: Usługa Azure Virtual Machines Shutdown jest zablokowana po ponownym uruchomieniu, zamknięciu lub zatrzymywaniu usług | Microsoft Docs
description: Ten artykuł pomaga w rozwiązywaniu problemów z błędami usługi w usłudze Azure Windows Virtual Machines.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: e5ab1262def78da4971ea6e5535f3ac915a38ec8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526762"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Zamykanie maszyny wirtualnej systemu Windows Azure jest wstrzymywane po "ponownym uruchomieniu", "zamykanie" lub "zatrzymywanie usług"

W tym artykule przedstawiono procedurę rozwiązywania problemów dotyczących "ponownego uruchamiania", "zamykania" lub "zatrzymywania usług", które mogą wystąpić podczas ponownego uruchamiania maszyny wirtualnej z systemem Windows w Microsoft Azure.

## <a name="symptoms"></a>Objawy

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej może pojawić się zrzut ekranu przedstawiający komunikat "ponowne uruchomienie", "zamykanie usługi" lub "zatrzymywanie usług".

![Ponowne uruchamianie, zamykanie i zatrzymywanie usług ekranu](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Przyczyna

System Windows używa procesu zamykania do wykonywania operacji konserwacyjnych systemu i przetwarzania zmian, takich jak aktualizacje, role i funkcje. Nie jest zalecane przerywanie tego procesu krytycznego do momentu jego zakończenia. W zależności od liczby aktualizacji/zmian i rozmiaru maszyny wirtualnej proces może zająć dużo czasu. Jeśli proces zostanie zatrzymany, istnieje możliwość, że system operacyjny stał się uszkodzony. Przerwać proces tylko wtedy, gdy trwa zbyt długo.

## <a name="solution"></a>Rozwiązanie

### <a name="collect-a-process-memory-dump"></a>Zbieranie zrzutu pamięci procesu

1. Pobierz [Narzędzie ProcDump](http://download.sysinternals.com/files/Procdump.zip) do nowego lub istniejącego dysku danych, który jest dołączony do działającej maszyny wirtualnej z tego samego regionu.

2. Odłącz dysk zawierający pliki, które są konieczne z działającej maszyny wirtualnej, a następnie Dołącz dysk do uszkodzonej maszyny wirtualnej. Wywołujemy ten dysk jako **Narzędzie**.

Użyj [konsoli szeregowej](./serial-console-windows.md) , aby wykonać następujące czynności:

1. Otwórz program PowerShell w środowisku administracyjnym i sprawdź, czy usługa przestanie reagować na zatrzymanie.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. W administracyjnym CMD Pobierz identyfikator PID usługi niereagującej.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Pobierz próbkę zrzutu pamięci z procesu, który nie odpowiada <STOPPING SERVICE> .

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Teraz Kasuj proces nieodpowiadający, aby odblokować proces zamykania.

   ``
   taskkill /PID <PID> /t /f
   ``

Po ponownym uruchomieniu systemu operacyjnego, jeśli zostanie on uruchomiony normalnie, wystarczy upewnić się, że spójność systemu operacyjnego jest prawidłowa. Jeśli zostanie zgłoszone uszkodzenie, uruchom następujące polecenie, dopóki dysk nie zostanie zwolniony:

``
dism /online /cleanup-image /restorehealth
``

Jeśli nie można zebrać zrzutu pamięci procesu lub ten problem jest cykliczny i wymagana jest analiza głównej przyczyny, Kontynuuj zbieranie zrzutu pamięci systemu operacyjnego poniżej, Kontynuuj, aby otworzyć żądanie pomocy technicznej.

### <a name="collect-an-os-memory-dump"></a>Zbieranie zrzutu pamięci systemu operacyjnego

Jeśli problem nie zostanie rozwiązany po oczekiwaniu na zmiany w procesie, należy zebrać plik zrzutu pamięci i skontaktować się z pomocą techniczną. Aby zebrać plik zrzutu, wykonaj następujące kroki:

**Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania**

1. Utwórz migawkę dysku systemu operacyjnego z zaatakowaną maszyną wirtualną jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

2. [Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania](./troubleshoot-recovery-disks-portal-windows.md).

3. Pulpit zdalny do maszyny wirtualnej odzyskiwania.

4. Jeśli dysk systemu operacyjnego jest zaszyfrowany, przed przejściem do następnego kroku należy wyłączyć szyfrowanie. Aby uzyskać więcej informacji, zobacz [odszyfrowywanie zaszyfrowanego dysku systemu operacyjnego na maszynie wirtualnej, której nie można uruchomić](./troubleshoot-bitlocker-boot-error.md#solution).

**Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej**

1. Na maszynie wirtualnej odzyskiwania przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Jeśli litera sterownika, która jest przypisana do dołączonego dysku systemu operacyjnego, to F, należy przejść do F:\Windows.

2. Zlokalizuj plik Memory. dmp, a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu.

Jeśli nie możesz znaleźć pliku zrzutu, przejdź do następnego kroku, aby włączyć dziennik zrzutów i konsolę seryjną.

**Włącz dziennik zrzutów i konsolę seryjną**

Aby włączyć dziennik zrzutów i konsolę szeregową, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).

2. Uruchom poniższy skrypt:

   W tym skrypcie Załóżmy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zastąp ją odpowiednią wartością w maszynie wirtualnej.

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
   
   REM Enable Serial Console
   bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
   bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
   bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
   bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
   bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   
   REM Suggested configuration to enable OS Dump
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Sprawdź, czy na dysku jest wystarczająca ilość miejsca, aby przydzielić pamięć jako pamięć RAM, która zależy od rozmiaru wybieranego dla tej maszyny wirtualnej.

4. Jeśli nie ma wystarczającej ilości miejsca lub maszyna wirtualna jest duża (G, GS lub E), możesz zmienić lokalizację, w której ten plik zostanie utworzony, i odwołać się do dowolnego innego dysku z danymi, który jest dołączony do maszyny wirtualnej. Aby zmienić lokalizację, należy zmienić następujący klucz:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Odłącz dysk systemu operacyjnego, a następnie ponownie podłącz dysk systemu operacyjnego do maszyny wirtualnej, której to dotyczy](./troubleshoot-recovery-disks-portal-windows.md).

6. Uruchom maszynę wirtualną i uzyskaj dostęp do konsoli szeregowej.

7. Wybierz pozycję Wyślij niemaskowane przerwanie (NMI) w celu wyzwolenia zrzutu pamięci.

   ![Wyślij przerwanie bez maskowania](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Ponownie Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania, Zbierz plik zrzutu.

## <a name="contact-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Po zebraniu pliku zrzutu skontaktuj się z pomocą techniczną firmy Microsoft, aby określić główną przyczynę.
