---
title: Pobieranie wirtualnego dysku twardego systemu Windows z platformy Azure
description: Pobierz wirtualny dysk twardy systemu Windows przy użyciu Azure Portal.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 32b9753b79273ce747d00cba077dd8a5ee6d724d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565291"
---
# <a name="download-a-windows-vhd-from-azure"></a>Pobieranie wirtualnego dysku twardego systemu Windows z platformy Azure

Z tego artykułu dowiesz się, jak pobrać plik wirtualnego dysku twardego (VHD) systemu Windows z platformy Azure przy użyciu Azure Portal.

## <a name="optional-generalize-the-vm"></a>Opcjonalnie: uogólnij maszynę wirtualną

Jeśli chcesz użyć wirtualnego dysku [](tutorial-custom-images.md) twardego jako obrazu do utworzenia innych maszyn wirtualnych, użyj narzędzia [Sysprep,](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) aby uogólnić system operacyjny. W przeciwnym razie należy utworzyć kopię dysku dla każdej maszyny wirtualnej, którą chcesz utworzyć.

Aby użyć wirtualnego dysku twardego jako obrazu do tworzenia innych maszyn wirtualnych, uogólnij maszynę wirtualną.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. [Połącz się z maszyną wirtualną](connect-logon.md). 
3. Na maszynie wirtualnej otwórz okno wiersza polecenia jako administrator.
4. Zmień katalog na *%windir%\system32\sysprep* i uruchom sysprep.exe.
5. W oknie dialogowym Narzędzie przygotowywania systemu wybierz pozycję **Enter System Out-of-Box Experience (OOBE)** i upewnij się, że **wybrano** pozycję Uogólnij.
6. W opcje zamykania, wybierz **opcję Zamknij**, a następnie kliknij przycisk **OK.** 

Jeśli nie chcesz uogólniać bieżącej maszyny wirtualnej, możesz nadal utworzyć uogólniony obraz, tworząc najpierw migawkę dysku systemu [operacyjnego,](#alternative-snapshot-the-vm-disk)tworząc nową maszynę wirtualną na jej pomocą, a następnie uogólnijąc kopię.

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Wirtualnego dysku twardego nie można pobrać z platformy Azure, jeśli jest on dołączony do uruchomionej maszyny wirtualnej. Jeśli chcesz zachować działanie maszyny wirtualnej, możesz utworzyć [migawkę, a następnie pobrać migawkę](#alternative-snapshot-the-vm-disk).

1. W menu Centrum w Azure Portal kliknij pozycję **Virtual Machines**.
1. Wybierz maszynę wirtualną z listy.
1. W bloku maszyny wirtualnej kliknij pozycję **Zatrzymaj**.

### <a name="alternative-snapshot-the-vm-disk"></a>Alternatywa: tworzenie migawki dysku maszyny wirtualnej

Zrób migawkę dysku do pobrania.

1. Wybierz maszynę wirtualną w [portalu.](https://portal.azure.com)
2. Wybierz **pozycję Dyski** w menu po lewej stronie, a następnie wybierz dysk, który chcesz utworzyć migawkę. Zostaną wyświetlone szczegóły dysku.  
3. Wybierz **pozycję Utwórz migawkę** z menu w górnej części strony. Zostanie **otwarta strona Tworzenie** migawki.
4. W **nazwa**, wpisz nazwę migawki. 
5. W **przypadku typu migawki** wybierz **opcję Pełne lub** **Przyrostowe.**
6. Gdy wszystko będzie gotowe, wybierz **pozycję Przejrzyj i utwórz.**

Migawka zostanie utworzona wkrótce, a następnie może zostać użyta do pobrania lub utworzenia innej maszyny wirtualnej.

> [!NOTE]
> Jeśli nie zatrzymasz najpierw maszyny wirtualnej, migawka nie będzie czysta. Migawka będzie w tym samym stanie, w którym maszyna wirtualna była zasilana lub ulegała awarii w momencie utworzenia migawki.  Chociaż zwykle jest to bezpieczne, może to spowodować problemy, jeśli uruchomione aplikacje działające w czasie nie były odporne na awarie.
>  
> Ta metoda jest zalecana tylko w przypadku maszyn wirtualnych z pojedynczym dyskiem systemu operacyjnego. Maszyny wirtualne z co najmniej jednym dyskiem danych należy zatrzymać przed pobraniem lub przed utworzeniem migawki dla dysku systemu operacyjnego i każdego dysku danych.

## <a name="generate-download-url"></a>Generowanie adresu URL pobierania

Aby pobrać plik VHD, należy wygenerować adres URL sygnatury dostępu współdzielonego [(SAS).](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) Po wygenerowaniu adresu URL do adresu URL jest przypisywany czas wygaśnięcia.

1. Na stronie maszyny wirtualnej kliknij pozycję **Dyski** w menu po lewej stronie.
1. Wybierz dysk systemu operacyjnego dla maszyny wirtualnej.
1. Na stronie dysku wybierz pozycję Eksport dysku **z** menu po lewej stronie.
1. Domyślny czas wygaśnięcia adresu URL to *3600* sekund (jedna godzina). Może być konieczne zwiększenie tej liczby w przypadku dysków systemu operacyjnego Windows lub dużych dysków danych. **Zwykle wystarczy 36000** sekund (10 godzin).
1. Kliknij **pozycję Generuj adres URL.**

> [!NOTE]
> Czas wygaśnięcia jest zwiększany z domyślnego, aby zapewnić wystarczającą ilość czasu na pobranie dużego pliku VHD dla systemu operacyjnego Windows Server. Pobieranie dużych wirtualnych dysków twardych może potrwać do kilku godzin w zależności od połączenia i rozmiaru maszyny wirtualnej. 
> 
> 

## <a name="download-vhd"></a>Pobieranie wirtualnego dysku twardego

1. Pod wygenerowanym adresem URL kliknij pozycję Pobierz plik VHD.
1. Może być konieczne kliknięcie **przycisku Zapisz** w przeglądarce, aby rozpocząć pobieranie. Domyślna nazwa pliku VHD to *abcd*.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak przekazać plik VHD na platformę Azure.](upload-generalized-managed.md) 
- [Tworzenie dysków zarządzanych z dysków nieza zarządzanych na koncie magazynu.](attach-disk-ps.md)
- [Zarządzanie dyskami platformy Azure przy użyciu programu PowerShell.](tutorial-manage-data-disk.md)
