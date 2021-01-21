---
title: Maszyna wirtualna platformy Azure nie odpowiada podczas stosowania zasad zabezpieczeń do systemu
description: W tym artykule przedstawiono kroki rozwiązywania problemów z zawieszeniem ekranu ładowania, gdy maszyna wirtualna nie odpowiada, podczas stosowania zasad zabezpieczeń do systemu na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b5629
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.author: v-mibufo
ms.openlocfilehash: 047c8afbfe7b489e5c3ac0ccb677f6fc021443a8
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632643"
---
# <a name="azure-vm-is-unresponsive-while-applying-security-policy-to-the-system"></a>Maszyna wirtualna platformy Azure nie odpowiada podczas stosowania zasad zabezpieczeń do systemu

W tym artykule przedstawiono kroki rozwiązywania problemów, gdy system operacyjny zawiesza się i przestaje odpowiadać podczas stosowania zasad zabezpieczeń na maszynie wirtualnej platformy Azure.

## <a name="symptoms"></a>Objawy

W przypadku korzystania z [diagnostyki rozruchu](boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetla system operacyjny zablokowany podczas rozruchu z komunikatem:

> "Stosowanie zasad zabezpieczeń do systemu".

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy.png" alt-text="Zrzut ekranu uruchamiania systemu Windows Server 2012 R2 jest zablokowany.":::

:::image type="content" source="media/unresponsive-vm-apply-security-policy/apply-policy-2.png" alt-text="Zrzut ekranu startowego systemu operacyjnego jest zablokowany.":::

## <a name="cause"></a>Przyczyna

Istnieje mnóstwo potencjalnych przyczyn tego problemu. Nie będzie można znać źródła, dopóki nie zostanie przeprowadzona analiza zrzutu pamięci.

## <a name="resolution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu

> [!TIP]
> Jeśli masz najnowszą kopię zapasową maszyny wirtualnej, możesz spróbować [przywrócić maszynę wirtualną z kopii zapasowej](../../backup/backup-azure-arm-restore-vms.md) , aby rozwiązać problem z rozruchem.

1. [Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu](#create-and-access-a-repair-vm)
2. [Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci](#enable-serial-console-and-memory-dump-collection)
3. [Kompiluj ponownie maszynę wirtualną](#rebuild-the-vm)
4. [Zbierz plik zrzutu pamięci](#collect-the-memory-dump-file)

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) , aby przygotować maszynę wirtualną naprawy.
2. Użyj Podłączanie pulpitu zdalnego połączyć się z maszyną wirtualną naprawy.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

Aby włączyć Zbieranie zrzutów pamięci i konsolę seryjną, uruchom następujący skrypt:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Utwórz listę danych magazynu BCD i określ identyfikator modułu ładującego rozruchu, który będzie używany w następnym kroku.

     1. W przypadku maszyny wirtualnej 1. generacji wprowadź następujące polecenie i zanotuj identyfikator wymieniony poniżej:

        ```console
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```

        W poleceniu Zamień na \<BOOT PARTITON> literę partycji na dysku dołączonym, który zawiera folder rozruchowy.

        :::image type="content" source="media/unresponsive-vm-apply-security-policy/store-data.png" alt-text="Na diagramie przedstawiono dane wyjściowe listy magazyn BCD na maszynie wirtualnej generacji 1, która zawiera listę w obszarze ładujący rozruchu systemu Windows numer identyfikacyjny.":::

     2. W przypadku maszyny wirtualnej 2. generacji wprowadź następujące polecenie i zanotuj wymieniony identyfikator:

        ```console
        bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
        ```

        - W poleceniu Zastąp \<LETTER OF THE EFI SYSTEM PARTITION> literą partycji systemowej EFI.
        - Pomocne może być uruchomienie konsoli zarządzania dyskami w celu zidentyfikowania odpowiedniej partycji systemowej oznaczonej jako "partycja systemowa EFI".
        - Identyfikator może być unikatowym identyfikatorem GUID lub może być domyślnym "Bootmgr".
3. Uruchom następujące polecenia, aby włączyć konsolę szeregową:

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    ```

    ```console
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

    - W poleceniu Zamień na \<VOLUME LETTER WHERE THE BCD FOLDER IS> literę folderu BCD.
    - W poleceniu Zastąp \<BOOT LOADER IDENTIFIER> wartość identyfikatorem znalezionym w poprzednim kroku.
4. Sprawdź, czy ilość wolnego miejsca na dysku systemu operacyjnego jest większa niż rozmiar pamięci (RAM) na maszynie wirtualnej.

    1. Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci. Zamiast tworzyć plik na dysku systemu operacyjnego, można odwołać się do dowolnego innego dysku danych dołączonego do maszyny wirtualnej z wystarczającą ilością wolnego miejsca. Aby zmienić lokalizację, Zastąp "% główny_katalog_systemowy%" literą dysku (na przykład "F:") dysku danych w poleceniach wymienionych poniżej.
    2. Wprowadź poniższe polecenia (zalecana konfiguracja zrzutu):

        Załaduj przerwany dysk systemu operacyjnego:

        ```console
        REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
        ```

        Włącz w ControlSet001:

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Włącz w ControlSet002:

        ```console
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
        ```

        Zwolnij przerwany dysk systemu operacyjnego:

        ```console
        REG UNLOAD HKLM\BROKENSYSTEM
        ```

### <a name="rebuild-the-vm"></a>Kompiluj ponownie maszynę wirtualną

Aby ponownie połączyć maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .

### <a name="collect-the-memory-dump-file"></a>Zbierz plik zrzutu pamięci

Aby rozwiązać ten problem, należy najpierw zebrać plik zrzutu pamięci dla awarii i skontaktować się z pomocą techniczną pliku zrzutu pamięci. Aby zebrać plik zrzutu, wykonaj następujące kroki:

1. Dołącz dysk systemu operacyjnego do nowej maszyny wirtualnej naprawy:

    - Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) , aby przygotować nową maszynę wirtualną naprawy.
    - Użyj Podłączanie pulpitu zdalnego połączyć się z maszyną wirtualną naprawy.

2. Znajdź plik zrzutu i prześlij bilet pomocy technicznej:

    - Na stronie napraw maszynę wirtualną przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Jeśli litera dysku przypisana do dołączonego dysku systemu operacyjnego to `F`, należy przejść do folderu `F:\Windows`.
    - Zlokalizuj plik Memory. dmp, a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu pamięci.
    - Jeśli masz problemy z lokalizowaniem pliku Memory. dmp, możesz zamiast tego użyć [wywołań przerwań, które nie są maskowane (NMI) w konsoli szeregowej](serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Aby [wygenerować plik zrzutu awaryjnego za pomocą wywołań NMI](/windows/client-management/generate-kernel-or-complete-crash-dump), można przejść do przewodnika.

## <a name="next-steps"></a>Następne kroki

Jeśli występują problemy podczas stosowania zasad lokalnych użytkowników i grup, zobacz [, że maszyna wirtualna nie odpowiada podczas stosowania zasady grupy zasad lokalnych użytkowników i grup](unresponsive-vm-apply-group-policy.md)