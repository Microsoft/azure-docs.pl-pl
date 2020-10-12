---
title: Błąd zatrzymania systemu Windows —
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5b3ed56a-5a11-4ff9-9ee8-76aea4a5689b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/26/2020
ms.author: v-mibufo
ms.openlocfilehash: c04f3b27c7214dcf821c7698796bfaea399b947d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86509107"
---
# <a name="windows-stop-error---0x000000ef-critical-process-died"></a>Błąd zatrzymania systemu Windows — #0x000000EF "proces krytyczny padł"

W tym artykule przedstawiono kroki rozwiązywania problemów, w których proces krytyczny jest w trakcie rozruchu na maszynie wirtualnej platformy Azure.

## <a name="symptom"></a>Objaw

W przypadku korzystania z [diagnostyki rozruchu](./boot-diagnostics.md) w celu wyświetlenia zrzutu ekranu maszyny wirtualnej zobaczysz, że zrzut ekranu wyświetla komunikat o błędzie *#0x000000EF* z *procesem krytycznym komunikat padł*.

!["Komputer napotkał problem i wymaga ponownego uruchomienia. Właśnie zbieramy pewne informacje o błędzie, a następnie można uruchomić ponownie. (ukończono # #%) Jeśli chcesz dowiedzieć się więcej, możesz przeszukać w trybie online w późniejszym czasie dla tego błędu: 0x000000EF "](media/troubleshoot-guide-critical-process-died/1.jpg)

## <a name="cause"></a>Przyczyna

Zwykle jest to spowodowane tym, że krytyczny proces systemu zakończy się niepowodzeniem podczas rozruchu. Więcej informacji o problemach dotyczących procesów krytycznych można znaleźć w artykule "[sprawdzanie usterek 0xEF: CRITICAL_PROCESS_DIED](/windows-hardware/drivers/debugger/bug-check-0xef--critical-process-died)".

## <a name="solution"></a>Rozwiązanie

### <a name="process-overview"></a>Przegląd procesu:

1. Utwórz maszynę wirtualną naprawy i uzyskaj do niej dostęp.
2. Napraw wszystkie uszkodzenia systemu operacyjnego.
3. **Zalecane**: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci.
4. Skompiluj ponownie maszynę wirtualną.

> [!NOTE]
> W przypadku wystąpienia tego błędu rozruchu system operacyjny gościa nie działa. Aby rozwiązać ten problem, nastąpi Rozwiązywanie problemów w trybie offline.

### <a name="create-and-access-a-repair-vm"></a>Tworzenie maszyny wirtualnej naprawy i uzyskiwanie do niej dostępu

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować maszynę wirtualną naprawy.
2. Korzystanie z Podłączanie pulpitu zdalnego łączenia się z maszyną wirtualną naprawy.

### <a name="fix-any-os-corruption"></a>Napraw wszystkie uszkodzenia systemu operacyjnego

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień.
2. Uruchom następujące polecenie modułu sprawdzania plików systemowych (SFC):

   `sfc /scannow /offbootdir=<BOOT DISK DRIVE>:\ /offwindir=<BROKEN DISK DRIVE>:\windows`

   * Gdzie < dysk rozruchowy > jest woluminem rozruchowym maszyny wirtualnej naprawy (zazwyczaj "C:") i < uszkodzony dysk > będzie literą dysku dla dołączonego dysku z uszkodzonej maszyny wirtualnej. Zastąp znaki większe niż/mniejsze niż symbole, a także tekst zawarty w nich, np. "< tekst tutaj >" z odpowiednią literą.

3. Następnie użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) , aby ponownie połączyć maszynę wirtualną i sprawdzić, czy maszyna wirtualna jest uruchamiana.
4. Jeśli maszyna wirtualna nadal nie jest uruchamiana, należy kontynuować zbieranie pliku zrzutu pamięci.

### <a name="collect-the-memory-dump-file"></a>Zbierz plik zrzutu pamięci

Jeśli problem będzie nadal występować po uruchomieniu programu SFC, analiza pliku zrzutu pamięci będzie potrzebna do określenia przyczyny problemu. Aby zebrać plik zrzutu pamięci, wykonaj następujące kroki:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Dołącz dysk systemu operacyjnego do nowej maszyny wirtualnej naprawy

1. Wykonaj [kroki 1-3 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , aby przygotować nową maszynę wirtualną naprawy.
2. Korzystanie z Podłączanie pulpitu zdalnego łączenia się z maszyną wirtualną naprawy.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Lokalizowanie pliku zrzutu i przesyłanie biletu pomocy technicznej

3. Na stronie napraw maszynę wirtualną przejdź do folderu systemu Windows na dołączonym dysku systemu operacyjnego. Jeśli litera sterownika przypisana do dołączonego dysku systemu operacyjnego to *F*, należy przejść do *F:\Windows*.
4. Zlokalizuj plik *Memory. dmp* , a następnie [Prześlij bilet pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) przy użyciu pliku zrzutu pamięci.

   > [!NOTE]
   > Jeśli nie możesz znaleźć pliku zrzutu, wykonaj poniższe czynności, aby włączyć Zbieranie zrzutów pamięci i konsolę seryjną, a następnie wróć do tej sekcji i powtórz kroki opisane w powyższym zadaniu, aby zebrać plik zrzutu pamięci.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Zalecane: przed odbudowaniem maszyny wirtualnej Włącz zbieranie danych z konsoli szeregowej i zrzutu pamięci

Aby włączyć Zbieranie zrzutów pamięci i konsolę seryjną, uruchom następujący skrypt:

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (Uruchom jako administrator).
2. Uruchom następujące polecenia:

   Włącz konsolę seryjną

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

   Zastąp elementy większe niż lub mniejsze niż symbole oraz tekst w nich, np. "< tekst tutaj >".

3. Sprawdź, czy ilość wolnego miejsca na dysku systemu operacyjnego jest taka sama jak rozmiar pamięci (RAM) na maszynie wirtualnej.

Jeśli na dysku systemu operacyjnego nie ma wystarczającej ilości miejsca, należy zmienić lokalizację, w której zostanie utworzony plik zrzutu pamięci, i odwołać się do dowolnego dysku danych dołączonego do maszyny wirtualnej z wystarczającą ilością wolnego miejsca. Aby zmienić lokalizację, Zastąp "% główny_katalog_systemowy%" literą dysku (na przykład "F:") dysku danych w poniższych poleceniach.

#### <a name="suggested-configuration-to-enable-os-dump"></a>Sugerowana konfiguracja do włączenia zrzutu systemu operacyjnego

**Załaduj przerwany dysk systemu operacyjnego**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Włącz w ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Włącz w ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Zwolnij przerwany dysk systemu operacyjnego:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Odbuduj oryginalną maszynę wirtualną

Aby ponownie połączyć maszynę wirtualną, użyj [kroku 5 poleceń naprawy maszyny wirtualnej](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
