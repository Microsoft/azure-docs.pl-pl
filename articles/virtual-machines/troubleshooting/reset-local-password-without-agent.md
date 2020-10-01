---
title: Resetowanie lokalnego hasła systemu Windows bez agenta platformy Azure | Microsoft Docs
description: Jak zresetować hasło lokalnego konta użytkownika systemu Windows, gdy Agent gościa platformy Azure nie jest zainstalowany lub działa na maszynie wirtualnej
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: c7e6772799d98cd2997a1fe6b48efe1c7632cfaa
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91598361"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Resetowanie lokalnego hasła systemu Windows dla maszyny wirtualnej platformy Azure w trybie offline
Możesz zresetować lokalne hasło systemu Windows maszyny wirtualnej na platformie Azure przy użyciu [Azure Portal lub Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pod warunkiem, że zainstalowano agenta gościa platformy Azure. Ta metoda jest podstawowym sposobem resetowania hasła dla maszyny wirtualnej platformy Azure. Jeśli wystąpią problemy z agentem gościa platformy Azure, który nie odpowiada, lub instalacja nie powiedzie się po przesłaniu obrazu niestandardowego, można ręcznie zresetować hasło systemu Windows. W tym artykule szczegółowo opisano sposób resetowania hasła do konta lokalnego przez dołączenie źródłowego dysku wirtualnego systemu operacyjnego do innej maszyny wirtualnej. Kroki opisane w tym artykule nie dotyczą kontrolerów domeny systemu Windows. 

> [!WARNING]
> Ta metoda powinna być używana w ostateczności. Zawsze należy próbować zresetować hasło przy użyciu [Azure Portal lub Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

## <a name="overview-of-the-process"></a>Przegląd procesu
Podstawowe kroki służące do przeprowadzania lokalnego resetowania hasła dla maszyny wirtualnej z systemem Windows na platformie Azure, gdy nie ma dostępu do agenta gościa platformy Azure:

1. Zatrzymaj zaatakowaną maszynę wirtualną.
1. Utwórz migawkę dysku systemu operacyjnego dla maszyny wirtualnej.
1. Utwórz kopię dysku systemu operacyjnego na podstawie migawki.
1. Dołącz i zainstaluj skopiowany dysk systemu operacyjnego na innej maszynie wirtualnej z systemem Windows, a następnie utwórz pliki konfiguracji na dysku. Pliki będą pomocne w zresetowaniu hasła.
1. Odinstalowanie i odłączenie skopiowanego dysku systemu operacyjnego z maszyny wirtualnej Rozwiązywanie problemów.
1. Zamień dysk systemu operacyjnego na zaatakowaną maszynę wirtualną.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Szczegółowe kroki dla maszyny wirtualnej z wdrożeniem Menedżer zasobów

> [!NOTE]
> Kroki nie dotyczą kontrolerów domeny systemu Windows. Działa tylko na serwerze autonomicznym lub serwerze, który jest członkiem domeny.

Przed podjęciem próby wykonania poniższych kroków zawsze należy spróbować zresetować hasło przy użyciu [Azure Portal lub Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Przed rozpoczęciem upewnij się, że masz kopię zapasową maszyny wirtualnej.

1. Utwórz migawkę dysku systemu operacyjnego z zaatakowaną maszyną wirtualną, Stwórz dysk na podstawie migawki, a następnie Dołącz dysk do maszyny wirtualnej rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyną wirtualną z systemem Windows przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu Azure Portal](troubleshoot-recovery-disks-portal-windows.md).
2. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów przy użyciu Pulpit zdalny.
3. Utwórz `gpt.ini` na `\Windows\System32\GroupPolicy` dysku ŹRÓDŁOWEJ maszyny wirtualnej (jeśli gpt.ini istnieje, Zmień nazwę na gpt.ini. bak):
   
   > [!WARNING]
   > Upewnij się, że nie utworzysz przypadkowo następujących plików w folderze C:\Windows, dysk systemu operacyjnego do rozwiązywania problemów z maszyną wirtualną. Utwórz następujące pliki na dysku systemu operacyjnego dla źródłowej maszyny wirtualnej, która jest dołączana jako dysk danych.
   
   * Dodaj następujące wiersze do `gpt.ini` utworzonego pliku:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini.png" alt-text="Utwórz gpt.ini":::

4. Utwórz `scripts.ini` w `\Windows\System32\GroupPolicy\Machine\Scripts\` . Upewnij się, że ukryte foldery są wyświetlane. W razie konieczności Utwórz `Machine` foldery lub `Scripts` . 
   
   * Dodaj następujący wiersz `scripts.ini` utworzonego pliku:
     
     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-1.png" alt-text="Utwórz gpt.ini" <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Utwórz gpt.ini":::
   
    Podczas definiowania nowego hasła należy spełnić skonfigurowane wymagania dotyczące złożoności hasła dla maszyny wirtualnej.

6. W Azure Portal Odłącz dysk od maszyny wirtualnej rozwiązywania problemów.

7. [Zmień dysk systemu operacyjnego dla maszyny wirtualnej, której to dotyczy](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Po uruchomieniu nowej maszyny wirtualnej Nawiąż połączenie z maszyną wirtualną przy użyciu Pulpit zdalny z nowym hasłem określonym w `FixAzureVM.cmd` skrypcie.

9. Z sesji zdalnej na nową maszynę wirtualną Usuń następujące pliki, aby wyczyścić środowisko:
    
    * Z%windir%\System32\GroupPolicy\Machine\Scripts\Startup
      * Usuń FixAzureVM. cmd
    * Z%windir%\System32\GroupPolicy\Machine\Scripts
      * Usuń scripts.ini
    * Z%windir%\System32\GroupPolicy
      * Usuń gpt.ini (jeśli gpt.ini istniała wcześniej, a następnie zmień nazwę gpt.ini pliku na gpt.ini.

## <a name="detailed-steps-for-classic-vm"></a>Szczegółowe kroki dla klasycznej maszyny wirtualnej

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Kroki nie dotyczą kontrolerów domeny systemu Windows. Działa tylko na serwerze autonomicznym lub serwerze, który jest członkiem domeny.

Przed podjęciem próby wykonania poniższych kroków zawsze należy spróbować zresetować hasło przy użyciu [Azure Portal lub Azure PowerShell](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) . Przed rozpoczęciem upewnij się, że masz kopię zapasową maszyny wirtualnej. 

1. Usuń zaatakowaną maszynę wirtualną w Azure Portal. Usunięcie maszyny wirtualnej powoduje usunięcie tylko metadanych, odwołania do maszyny wirtualnej na platformie Azure. Dyski wirtualne są zachowywane po usunięciu maszyny wirtualnej:
   
   * Wybierz maszynę wirtualną w Azure Portal, a następnie kliknij pozycję *Usuń*:
     
     :::image type="content" source="./media/reset-local-password-without-agent/delete-vm-classic.png" alt-text="Utwórz gpt.ini":::

2. Dołącz dysk systemu operacyjnego źródłowej maszyny wirtualnej do rozwiązywania problemów z maszyną wirtualną. Maszyna wirtualna rozwiązywania problemów musi znajdować się w tym samym regionie co dysk systemu operacyjnego źródłowej maszyny wirtualnej (np `West US` .):
   
   1. Wybierz maszynę wirtualną Rozwiązywanie problemów w Azure Portal. Kliknij pozycję *dyski*  |  *Dołącz istniejące*:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-existing-classic.png" alt-text="Utwórz gpt.ini":::
     
   2. Wybierz pozycję *plik VHD* , a następnie wybierz konto magazynu, które zawiera ŹRÓDŁową maszynę wirtualną:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-storage-account-classic.png" alt-text="Utwórz gpt.ini":::
     
   3. Zaznacz pole wyboru *Pokaż klasyczne konta magazynu*, a następnie wybierz kontener źródłowy. Kontener źródłowy jest zazwyczaj *dyskami VHD*:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-classic.png" alt-text="Utwórz gpt.ini":::

      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png" alt-text="Utwórz gpt.ini":::
     
   4. Wybierz wirtualny dysk twardy systemu operacyjnego do dołączenia. Kliknij przycisk *Wybierz* , aby zakończyć proces:
     
      :::image type="content" source="./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png" alt-text="Utwórz gpt.ini":::

   5. Kliknij przycisk OK, aby dołączyć dysk

      :::image type="content" source="./media/reset-local-password-without-agent/disks-attach-okay-classic.png" alt-text="Utwórz gpt.ini":::

3. Połącz się z maszyną wirtualną rozwiązywania problemów przy użyciu Pulpit zdalny i upewnij się, że dysk systemu operacyjnego źródłowej maszyny wirtualnej jest widoczny:

   1. Wybierz maszynę wirtualną Rozwiązywanie problemów w Azure Portal i kliknij przycisk *Połącz*.

   2. Otwórz pobrany plik RDP. Wprowadź nazwę użytkownika i hasło do rozwiązywania problemów z maszyną wirtualną.

   3. W Eksploratorze plików Wyszukaj dołączony dysk danych. Jeśli wirtualny dysk twardy maszyny wirtualnej jest jedynym dyskiem danych dołączonym do maszyny wirtualnej rozwiązywania problemów, powinien to być dysk F:
     
      :::image type="content" source="./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png" alt-text="Utwórz gpt.ini":::

4. Utwórz `gpt.ini` na `\Windows\System32\GroupPolicy` dysku ŹRÓDŁOWEJ maszyny wirtualnej (Jeśli `gpt.ini` istnieje, Zmień nazwę na `gpt.ini.bak` ):
   
   > [!WARNING]
   > Upewnij się, że nie utworzysz przypadkowo następujących plików w programie `C:\Windows` , dysk systemu operacyjnego do rozwiązywania problemów z maszyną wirtualną. Utwórz następujące pliki na dysku systemu operacyjnego dla źródłowej maszyny wirtualnej, która jest dołączana jako dysk danych.
   
   * Dodaj następujące wiersze do `gpt.ini` utworzonego pliku:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-gpt-ini-classic.png" alt-text="Utwórz gpt.ini":::

5. Utwórz `scripts.ini` w `\Windows\System32\GroupPolicy\Machine\Scripts\` . Upewnij się, że ukryte foldery są wyświetlane. W razie konieczności Utwórz `Machine` foldery lub `Scripts` .
   
   * Dodaj następujący wiersz `scripts.ini` utworzonego pliku:

     ```
     [Startup]
     0CmdLine=FixAzureVM.cmd
     0Parameters=
     ```
     
     :::image type="content" source="./media/reset-local-password-without-agent/create-scripts-ini-classic-1.png" alt-text="Utwórz gpt.ini" <username> /add
    ```

    :::image type="content" source="./media/reset-local-password-without-agent/create-fixazure-cmd-1.png" alt-text="Utwórz gpt.ini":::
   
    Podczas definiowania nowego hasła należy spełnić skonfigurowane wymagania dotyczące złożoności hasła dla maszyny wirtualnej.

7. W Azure Portal Odłącz dysk od maszyny wirtualnej rozwiązywania problemów:
   
   1. Wybierz maszynę wirtualną Rozwiązywanie problemów w Azure Portal, kliknij pozycję *dyski*.
   
   2. Wybierz dysk danych dołączony w kroku 2, kliknij przycisk **Odłącz**, a następnie kliknij przycisk **OK**.

     :::image type="content" source="./media/reset-local-password-without-agent/data-disks-classic.png" alt-text="Utwórz gpt.ini":::
     
     :::image type="content" source="./media/reset-local-password-without-agent/detach-disk-classic.png" alt-text="Utwórz gpt.ini":::

8. Utwórz maszynę wirtualną na podstawie dysku systemu operacyjnego źródłowej maszyny wirtualnej:
   
     :::image type="content" source="./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png" alt-text="Utwórz gpt.ini":::

     :::image type="content" source="./media/reset-local-password-without-agent/choose-subscription-classic.png" alt-text="Utwórz gpt.ini":::

     :::image type="content" source="./media/reset-local-password-without-agent/create-vm-classic.png" alt-text="Utwórz gpt.ini":::

## <a name="complete-the-create-virtual-machine-experience"></a>Ukończ środowisko tworzenia maszyny wirtualnej

1. Po uruchomieniu nowej maszyny wirtualnej Nawiąż połączenie z maszyną wirtualną przy użyciu Pulpit zdalny z nowym hasłem określonym w `FixAzureVM.cmd` skrypcie.

2. Z sesji zdalnej na nową maszynę wirtualną Usuń następujące pliki, aby wyczyścić środowisko:
    
    * Wniosek `%windir%\System32\GroupPolicy\Machine\Scripts\Startup\`
      * usuwa `FixAzureVM.cmd`
    * Wniosek `%windir%\System32\GroupPolicy\Machine\Scripts`
      * usuwa `scripts.ini`
    * Wniosek `%windir%\System32\GroupPolicy`
      * Usuń `gpt.ini` (Jeśli `gpt.ini` istniały wcześniej i zmieniono jego nazwę na `gpt.ini.bak` , Zmień nazwę `.bak` pliku z powrotem na `gpt.ini` )

## <a name="next-steps"></a>Następne kroki
Jeśli nadal nie można nawiązać połączenia przy użyciu Pulpit zdalny, zobacz [Przewodnik rozwiązywania problemów](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)z protokołem RDP. [Szczegółowy przewodnik rozwiązywania problemów](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) z PROTOKOŁem RDP dotyczy metod rozwiązywania problemów, a nie konkretnych kroków. Możesz również [otworzyć żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) , aby uzyskać pomoc techniczną.
