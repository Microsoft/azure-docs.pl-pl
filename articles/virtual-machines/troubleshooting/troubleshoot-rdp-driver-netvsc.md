---
title: Rozwiązywanie problemów z netvsc.sys podczas zdalnego nawiązywania połączenia z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 na platformie Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z połączeniem RDP związanym z netsvc.sys w przypadku nawiązywania połączenia z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 na platformie Azure.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 0f5a414f00ffa50114f090fc19f37b8a85428547
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509005"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Nie można nawiązać zdalnego połączenia z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 na platformie Azure z powodu netvsc.sys

W tym artykule wyjaśniono, jak rozwiązać problem polegający na tym, że podczas łączenia z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 (VM) na hoście funkcji Hyper-V Server 2016 nie ma połączenia sieciowego.

## <a name="symptoms"></a>Objawy

Nie można nawiązać połączenia z maszyną wirtualną z systemem Windows 10 lub Windows Server 2016 przy użyciu usługi Remote Desktop Protocol (RDP). W przypadku [diagnostyki rozruchu](boot-diagnostics.md)ekran przedstawia czerwoną skrzyżowanie za pośrednictwem karty interfejsu sieciowego (nic). Oznacza to, że maszyna wirtualna nie ma łączności po całkowitym załadowaniu systemu operacyjnego.

Zazwyczaj ten problem występuje w [kompilacjach](https://support.microsoft.com/help/4093120/) systemu Windows 14393 i [kompilacji 15063](https://support.microsoft.com/help/4015583/). Jeśli wersja systemu operacyjnego jest nowsza niż ta wersja, ten artykuł nie dotyczy Twojego scenariusza. Aby sprawdzić wersję systemu, Otwórz sesję CMD w [funkcji konsola dostępu szeregowego](serial-console-windows.md), a następnie uruchom polecenie **Ver**.

## <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli wersja zainstalowanego netvsc.sys pliku systemowego to **10.0.14393.594** lub **10.0.15063.0**. Te wersje netvsc.sys mogą uniemożliwić współdziałanie systemu z platformą Azure.


## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy [wykonać migawkę dysku systemowego](../windows/snapshot-copy-managed-disk.md) , której dotyczy dana maszyna wirtualna, jako kopię zapasową. Aby rozwiązać ten problem, użyj konsoli szeregowej lub [napraw maszynę wirtualną w trybie offline](#repair-the-vm-offline) , dołączając dysk systemowy maszyny wirtualnej do maszyny wirtualnej odzyskiwania.


### <a name="use-the-serial-console"></a>Korzystanie z konsoli szeregowej

Połącz się z [konsolą szeregową, Otwórz wystąpienie programu PowerShell](serial-console-windows.md), a następnie wykonaj poniższe kroki.

> [!NOTE]
> Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, przejdź do sekcji [Naprawa maszyny wirtualnej w trybie offline](#repair-the-vm-offline) .

1. Uruchom następujące polecenie w wystąpieniu programu PowerShell, aby uzyskać wersję pliku (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Pobierz odpowiednią aktualizację do nowego lub istniejącego dysku z danymi, który jest dołączony do działającej maszyny wirtualnej z tego samego regionu:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562)   lub nowsza
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) lub nowsza

3. Odłącz dysk narzędzia z działającej maszyny wirtualnej, a następnie dołącz go do uszkodzonej maszyny wirtualnej.

4. Uruchom następujące polecenie, aby zainstalować aktualizację na maszynie wirtualnej:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Uruchom ponownie maszynę wirtualną.

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

1. [Dołącz dysk systemowy do maszyny wirtualnej odzyskiwania](./troubleshoot-recovery-disks-portal-windows.md).

2. Uruchom Pulpit zdalny połączenie z maszyną wirtualną odzyskiwania.

3. Upewnij się, że dysk jest oflagowany jako **online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku przypisaną do dołączonego dysku systemowego.

4. Utwórz kopię folderu **\Windows\System32\config** w przypadku konieczności wycofania zmian.

5. Na maszynie ratowniczej Uruchom Edytor rejestru (regedit.exe).

6. Wybierz klucz **HKEY_LOCAL_MACHINE** , a następnie wybierz pozycję **plik**  >  **Załaduj gałąź** z menu.

7. Zlokalizuj plik SYSTEMowy w folderze **\Windows\System32\config** .

8. Wybierz pozycję **Otwórz**, wpisz **BROKENSYSTEM** jako nazwę, rozwiń klucz **HKEY_LOCAL_MACHINE** , a następnie odszukaj dodatkowy klucz o nazwie **BROKENSYSTEM**.

9. Przejdź do następującej lokalizacji:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. W każdym podkluczu (np. 0000) Przejrzyj wartość **DriverDesc** , która jest wyświetlana jako **karta sieciowa Microsoft Hyper-V**.

11. W podkluczu należy przejrzeć wartość **DriverVersion** , która jest wersja sterownika karty sieciowej maszyny wirtualnej.

12. Pobierz odpowiednią aktualizację:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562)   lub nowsza
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) lub nowsza

13. Dołącz dysk systemowy jako dysk danych na maszynie wirtualnej, na której można pobrać aktualizację.

14. Uruchom następujące polecenie, aby zainstalować aktualizację na maszynie wirtualnej:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Uruchom następujące polecenie, aby odinstalować gałęzie:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Odłącz dysk systemowy i ponownie utwórz maszynę wirtualną](./troubleshoot-recovery-disks-portal-windows.md).

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną platformy Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , aby szybko rozwiązać swój problem.
