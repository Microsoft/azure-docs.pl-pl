---
title: Pobieranie wirtualnego dysku twardego systemu Linux z platformy Azure
description: Pobierz wirtualny dysk twardy systemu Linux przy użyciu interfejsu wiersza polecenia platformy Azure Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 8def06990b72d6e08127e8c4f16e0dfd87905d4f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565189"
---
# <a name="download-a-linux-vhd-from-azure"></a>Pobieranie wirtualnego dysku twardego systemu Linux z platformy Azure

Z tego artykułu dowiesz się, jak pobrać plik wirtualnego dysku twardego (VHD) systemu Linux z platformy Azure przy użyciu Azure Portal. 

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać wirtualnego dysku twardego z platformy Azure, jeśli jest on dołączony do uruchomionej maszyny wirtualnej. Jeśli chcesz zachować działanie maszyny wirtualnej, możesz utworzyć [migawkę, a następnie pobrać migawkę](#alternative-snapshot-the-vm-disk).

Aby zatrzymać maszynę wirtualną:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  W menu po lewej stronie wybierz **pozycję Virtual Machines**.
3.  Wybierz maszynę wirtualną z listy.
4.  Na stronie maszyny wirtualnej wybierz pozycję **Zatrzymaj**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Wyświetla przycisk menu, aby zatrzymać maszynę wirtualną.":::

### <a name="alternative-snapshot-the-vm-disk"></a>Alternatywa: tworzenie migawki dysku maszyny wirtualnej

Zrób migawkę dysku do pobrania.

1. Wybierz maszynę wirtualną w [portalu.](https://portal.azure.com)
2. Wybierz **pozycję Dyski** w menu po lewej stronie, a następnie wybierz dysk, który chcesz utworzyć migawkę. Zostaną wyświetlone szczegóły dysku.  
3. Wybierz **pozycję Utwórz migawkę** z menu w górnej części strony. Zostanie **otwarta strona Tworzenie** migawki.
4. W **nazwa**, wpisz nazwę migawki. 
5. W **przypadku typu migawki** wybierz **opcję Pełne lub** **Przyrostowe.**
6. Gdy wszystko będzie gotowe, wybierz **pozycję Przejrzyj i utwórz**.

Migawka zostanie utworzona wkrótce, a następnie może zostać użyta do pobrania lub utworzenia innej maszyny wirtualnej.

> [!NOTE]
> Jeśli nie zatrzymasz najpierw maszyny wirtualnej, migawka nie będzie czysta. Migawka będzie w tym samym stanie, w którym maszyna wirtualna była zasilana lub ulegała awarii w momencie utworzenia migawki.  Chociaż zwykle jest to bezpieczne, może to spowodować problemy, jeśli uruchomione aplikacje działające w czasie nie były odporne na awarie.
>  
> Ta metoda jest zalecana tylko w przypadku maszyn wirtualnych z jednym dyskiem systemu operacyjnego. Maszyny wirtualne z co najmniej jednym dyskiem danych należy zatrzymać przed pobraniem lub przed utworzeniem migawki dla dysku systemu operacyjnego i każdego dysku danych.

## <a name="generate-sas-url"></a>Generowanie adresu URL sygnatury dostępu współdzielonego

Aby pobrać plik VHD, należy wygenerować adres URL sygnatury dostępu współdzielonego [(SAS).](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) Po wygenerowaniu adresu URL do adresu URL jest przypisywany czas wygaśnięcia.

1. W menu strony maszyny wirtualnej wybierz pozycję **Dyski**.
2. Wybierz dysk systemu operacyjnego dla maszyny wirtualnej, a następnie wybierz **pozycję Eksportuj dysk.**
1. W razie potrzeby zaktualizuj wartość adresu URL wygasa w **(w sekundach),** aby zapewnić wystarczającą ilość czasu na ukończenie pobierania. Wartość domyślna to 3600 sekund (jedna godzina).
3. Wybierz **pozycję Generuj adres URL.**
 
      
## <a name="download-vhd"></a>Pobieranie wirtualnego dysku twardego

1.  Pod wygenerowanym adresem URL wybierz **pozycję Pobierz plik VHD.**

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Wyświetla przycisk pobierania dysku VHD.":::

2.  Aby rozpocząć **pobieranie,** może być konieczne wybranie w przeglądarce przycisku Zapisz. Domyślna nazwa pliku VHD to *abcd*.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [przekazać i utworzyć maszynę wirtualną z systemem Linux na dysku niestandardowym przy użyciu interfejsu wiersza polecenia platformy Azure.](upload-vhd.md) 
- [Zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.](tutorial-manage-disks.md)
