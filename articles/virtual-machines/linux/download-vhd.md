---
title: Pobieranie wirtualnego dysku twardego z systemem Linux z platformy Azure
description: Pobierz dysk VHD z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure i Azure Portal.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: f0c5e51665b4b1d31e7d2b3e25e7be31b481d0d9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203171"
---
# <a name="download-a-linux-vhd-from-azure"></a>Pobieranie wirtualnego dysku twardego z systemem Linux z platformy Azure

W tym artykule dowiesz się, jak pobrać plik wirtualnego dysku twardego z systemem Linux z platformy Azure przy użyciu Azure Portal. 

## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać wirtualnego dysku twardego z platformy Azure, jeśli jest on dołączony do uruchomionej maszyny wirtualnej. Musisz zatrzymać maszynę wirtualną, aby pobrać dysk VHD. 

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2.  Z menu po lewej stronie wybierz pozycję **Virtual Machines**.
3.  Z listy wybierz maszynę wirtualną.
4.  Na stronie maszyny wirtualnej wybierz pozycję **Zatrzymaj**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Pokazuje przycisk menu, aby zatrzymać maszynę wirtualną.":::

## <a name="generate-sas-url"></a>Generuj adres URL SAS

Aby pobrać plik VHD, należy wygenerować adres URL [sygnatury dostępu współdzielonego (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) . Po wygenerowaniu adresu URL zostanie do niego przypisany czas wygaśnięcia.

1. W menu strony dla maszyny wirtualnej wybierz pozycję **dyski**.
2. Wybierz dysk systemu operacyjnego dla maszyny wirtualnej, a następnie wybierz pozycję **eksport dysku**.
1. W razie potrzeby zaktualizuj wartość **adresu URL (w sekundach)** , aby zapewnić wystarczającą ilość czasu na ukończenie pobierania. Wartość domyślna to 3600 sekund (1 godzina).
3. Wybierz pozycję **Generuj adres URL**.
 
      
## <a name="download-vhd"></a>Pobierz dysk VHD

1.  W obszarze wygenerowanego adresu URL wybierz pozycję **Pobierz plik VHD**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Pokazuje przycisk pobierania dysku VHD.":::

2.  Aby rozpocząć pobieranie, może być konieczne wybranie opcji **Zapisz** w przeglądarce. Domyślna nazwa pliku VHD jest *ABCD*.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [przekazać i utworzyć maszynę wirtualną z systemem Linux z dysku niestandardowego przy użyciu interfejsu wiersza polecenia platformy Azure](upload-vhd.md). 
- [Zarządzanie dyskami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure](tutorial-manage-disks.md).
