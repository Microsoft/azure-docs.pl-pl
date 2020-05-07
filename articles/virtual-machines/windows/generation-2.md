---
title: Pomoc techniczna platformy Azure dla maszyn wirtualnych 2. generacji
description: Omówienie pomocy technicznej platformy Azure dla maszyn wirtualnych 2. generacji
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: bf690ad3ad38632834a92c4a743b1cb584beaf65
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838828"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Obsługa maszyn wirtualnych 2. generacji na platformie Azure

Obsługa maszyn wirtualnych 2. generacji jest teraz dostępna na platformie Azure. Nie można zmienić generacji maszyny wirtualnej po jej utworzeniu. przed wybraniem generacji zapoznaj się z uwagami na tej stronie.

Maszyny wirtualne generacji 2 obsługują kluczowe funkcje, które nie są obsługiwane przez maszyny wirtualne generacji 1. Te funkcje obejmują zwiększoną ilość pamięci, rozszerzenia funkcji ochrony oprogramowania firmy Intel (Intel SGX) i zwirtualizowaną pamięć trwałą (vPMEM). Maszyny wirtualne 2. generacji działające lokalnie mają pewne funkcje, które nie są jeszcze obsługiwane na platformie Azure. Aby uzyskać więcej informacji, zobacz sekcję [funkcje i możliwości](#features-and-capabilities) .

Maszyny wirtualne generacji 2 wykorzystują nową architekturę rozruchową opartą na interfejsie UEFI zamiast architektury opartej na systemie BIOS używanej przez maszyny wirtualne generacji 1. W porównaniu z maszynami wirtualnymi generacji 1 maszyny wirtualne generacji 2 mogą mieć ulepszony czas rozruchu i instalacji. Aby zapoznać się z omówieniem maszyn wirtualnych 2. generacji i niektórych różnic między generacjami 1 i 2, zobacz temat [Tworzenie maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Rozmiary maszyn wirtualnych generacji 2

Maszyny wirtualne generacji 1 są obsługiwane przez wszystkie rozmiary maszyn wirtualnych na platformie Azure (z wyjątkiem maszyn wirtualnych z serii Mv2). Platforma Azure oferuje teraz obsługę generacji 2 dla następującej wybranej serii maszyn wirtualnych:

* [Seria B](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [Seria DCsv2](../dcv2-series.md)
* Seria [DSv2](../dv2-dsv2-series.md) i [Dsv3](../dv3-dsv3-series.md)
* [Seria Dasv4](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series)
* [Seria Esv3](../ev3-esv3-series.md)
* [Seria Easv4](https://docs.microsoft.com/azure/virtual-machines/eav4-easv4-series)
* [Seria Fsv2](../fsv2-series.md)
* [Seria GS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [Seria HB](../hb-series.md)
* [Seria HC](../hc-series.md)
* Seria [ls](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) i seria [Lsv2](../lsv2-series.md)
* [Seria M](../m-series.md)
* [Mv2 — seria](../mv2-series.md)<sup>1</sup>
* Seria [NCv2](../ncv2-series.md) i [Seria NCV3](../ncv3-series.md)
* [Seria ND](../nd-series.md)
* [Seria NVv3](../nvv3-series.md)

<sup>1</sup> Mv2 — seria nie obsługuje obrazów maszyn wirtualnych generacji 1 i obsługuje tylko podzestaw obrazów generacji 2. Szczegóły można znaleźć w [dokumentacji Mv2](https://docs.microsoft.com/azure/virtual-machines/mv2-series) .

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Obrazy maszyn wirtualnych generacji 2 w witrynie Azure Marketplace

Maszyny wirtualne 2. generacji obsługują następujące obrazy w portalu Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Enterprise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16,04, 18,04, 19,04, 19,10 
* RHEL 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 7,0
* Cent OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4
* Oracle Linux 7,7, 7,7-CI

> [!NOTE]
> Konkretne rozmiary maszyn wirtualnych, takie jak Mv2, mogą obsługiwać tylko podzestaw tych obrazów — szczegółowe informacje można znaleźć w dokumentacji odpowiedniego rozmiaru maszyny wirtualnej.

## <a name="on-premises-vs-azure-generation-2-vms"></a>Lokalna a maszyny wirtualne 2. generacji platformy Azure

Platforma Azure nie obsługuje obecnie niektórych funkcji, które są obsługiwane przez funkcję Hyper-V w przypadku maszyn wirtualnych 2. generacji.

| Funkcja generacji 2                | Lokalna funkcja Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Bezpieczny rozruch                         | :heavy_check_mark:  | y   |
| Chroniona maszyna wirtualna                         | :heavy_check_mark:  | y   |
| vTPM                                | :heavy_check_mark:  | y   |
| Zabezpieczenia oparte na wirtualizacji (VBS) | :heavy_check_mark:  | y   |
| VHDX format                         | :heavy_check_mark:  | y   |

## <a name="features-and-capabilities"></a>Funkcje i możliwości

### <a name="generation-1-vs-generation-2-features"></a>Funkcje generacji 1 a generacja 2

| Funkcja | Pierwsza generacja | Druga generacja |
|---------|--------------|--------------|
| Wiódł             | PCAT                      | UEFI                               |
| Kontrolery dysków | IDE                       | SCSI                               |
| Rozmiary maszyn wirtualnych         | Wszystkie rozmiary maszyn wirtualnych | Tylko maszyny wirtualne obsługujące usługę Premium Storage |

### <a name="generation-1-vs-generation-2-capabilities"></a>Możliwości generacji 1 a generacja 2

| Możliwość | Pierwsza generacja | Druga generacja |
|------------|--------------|--------------|
| Dysk systemu operacyjnego > 2 TB                    | y                | :heavy_check_mark: |
| Niestandardowy dysk/obraz/system operacyjny wymiany         | :heavy_check_mark: | :heavy_check_mark: |
| Obsługa zestawu skalowania maszyn wirtualnych | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Tworzenie kopii zapasowej/przywracanie                    | :heavy_check_mark: | :heavy_check_mark: |
| Galeria obrazów udostępnionych              | :heavy_check_mark: | :heavy_check_mark: |
| Szyfrowanie dysków Azure             | :heavy_check_mark: | y                |

## <a name="creating-a-generation-2-vm"></a>Tworzenie maszyny wirtualnej generacji 2

### <a name="marketplace-image"></a>Obraz witryny Marketplace

W Azure Portal lub interfejsie wiersza polecenia platformy Azure można tworzyć maszyny wirtualne 2. generacji z obrazu portalu Marketplace, który obsługuje rozruch z interfejsem UEFI.

#### <a name="azure-portal"></a>Azure Portal

Poniżej przedstawiono procedurę tworzenia maszyny wirtualnej generacji 2 (Gen2) w Azure Portal.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Wybierz pozycję **Utwórz zasób**.
1. Po lewej stronie kliknij pozycję **Zobacz wszystko** w witrynie Azure Marketplace.
1. Wybierz obraz, który obsługuje Gen2.
1. Kliknij przycisk **Utwórz**.
1. Na karcie **Zaawansowane** w sekcji **generacja maszyny wirtualnej** wybierz opcję **generacji 2** .
1. Na karcie **podstawowe** w obszarze **szczegóły wystąpienia**przejdź do pozycji **rozmiar** i Otwórz blok **Wybieranie rozmiaru maszyny wirtualnej** .
1. Wybierz [obsługiwaną maszynę wirtualną generacji 2](#generation-2-vm-sizes).
1. Przejdź przez [przepływ tworzenia Azure Portal](quick-create-portal.md) , aby zakończyć tworzenie maszyny wirtualnej.

![Wybierz maszynę wirtualną generacji 1 lub Gen 2](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Możesz również użyć programu PowerShell do utworzenia maszyny wirtualnej, bezpośrednio odwołującej się do jednostki SKU generacji 1 lub 2.

Na przykład użyj następującego polecenia cmdlet programu PowerShell, aby wyświetlić listę jednostek SKU w `WindowsServer` ofercie.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Alternatywnie możesz użyć interfejsu wiersza polecenia platformy Azure, aby wyświetlić dostępne obrazy generacji 2 wymienione przez **wydawcę**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Jeśli tworzysz maszynę wirtualną z systemem Windows Server 2012 jako system operacyjny, wybierz jednostkę SKU maszyny wirtualnej generacji 1 (BIOS) lub 2 (UEFI), która wygląda następująco:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Zapoznaj się z sekcją [funkcje i możliwości](#features-and-capabilities) , aby zapoznać się z bieżącą listą obsługiwanych obrazów z portalu Marketplace.

### <a name="managed-image-or-managed-disk"></a>Zarządzany obraz lub dysk zarządzany

Maszynę wirtualną generacji 2 można utworzyć na podstawie zarządzanego obrazu lub dysku zarządzanego w taki sam sposób jak w przypadku tworzenia maszyny wirtualnej generacji 1.

### <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych

Można również tworzyć maszyny wirtualne 2. generacji przy użyciu zestawów skalowania maszyn wirtualnych. W interfejsie wiersza polecenia platformy Azure Użyj zestawów skalowania platformy Azure, aby utworzyć maszyny wirtualne 2. generacji.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* **Czy maszyny wirtualne 2. generacji są dostępne we wszystkich regionach świadczenia usługi Azure?**  
    Tak. Ale nie wszystkie [rozmiary maszyn wirtualnych generacji 2](#generation-2-vm-sizes) są dostępne w każdym regionie. Dostępność maszyny wirtualnej generacji 2 zależy od dostępności rozmiaru maszyny wirtualnej.

* **Czy istnieją różnice cenowe między maszynami wirtualnymi generacji 1 i 2?**  
   Nie.

* **Mam plik VHD z lokalnej maszyny wirtualnej generacji 2. Czy można użyć tego pliku VHD do utworzenia maszyny wirtualnej generacji 2 na platformie Azure?**
  Tak, możesz przenieść plik VHD 2. generacji na platformę Azure i użyć go do utworzenia maszyny wirtualnej generacji 2. Wykonaj następujące kroki, aby to zrobić:
    1. Przekaż plik VHD do konta magazynu w tym samym regionie, w którym chcesz utworzyć maszynę wirtualną.
    1. Utwórz dysk zarządzany na podstawie pliku VHD. Ustaw właściwość generacja funkcji Hyper-V na v2. Następujące polecenia programu PowerShell ustawiają Właściwość generowania funkcji Hyper-V podczas tworzenia dysku zarządzanego.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Po udostępnieniu dysku utwórz maszynę wirtualną, dołączając ten dysk. Utworzona maszyna wirtualna będzie maszyną wirtualną 2. generacji.
    Po utworzeniu maszyny wirtualnej generacji 2 można opcjonalnie uogólnić obraz tej maszyny wirtualnej. Uogólnienie obrazu pozwala na utworzenie wielu maszyn wirtualnych.

* **Jak mogę zwiększyć rozmiar dysku systemu operacyjnego?**  
  Dyski systemu operacyjnego o rozmiarze większym niż 2 TB są nowe do maszyn wirtualnych 2. generacji. Domyślnie dyski systemu operacyjnego są mniejsze niż 2 TB w przypadku maszyn wirtualnych 2. generacji. Można zwiększyć rozmiar dysku do zalecanej maksymalnie 4 TB. Użyj interfejsu wiersza polecenia platformy Azure lub Azure Portal, aby zwiększyć rozmiar dysku systemu operacyjnego. Aby uzyskać informacje o sposobach rozszerzania dysków, zobacz [zmiana rozmiaru dysku](expand-os-disk.md).

  Aby zwiększyć rozmiar dysku systemu operacyjnego z Azure Portal:

  1. W Azure Portal przejdź do strony właściwości maszyny wirtualnej.
  1. Aby wyłączyć i cofnąć alokację maszyny wirtualnej, wybierz przycisk **Zatrzymaj** .
  1. W sekcji **dyski** wybierz dysk systemu operacyjnego, który chcesz zwiększyć.
  1. W sekcji **dyski** wybierz pozycję **Konfiguracja**, a następnie zaktualizuj **rozmiar** do wartości, która ma zostać wybrana.
  1. Wróć do strony właściwości maszyny wirtualnej i **Uruchom** maszynę wirtualną.
  
  Może pojawić się ostrzeżenie dotyczące dysków systemu operacyjnego większych niż 2 TB. Ostrzeżenie nie dotyczy maszyn wirtualnych 2. generacji. Jednak rozmiary dysków systemu operacyjnego większe niż 4 TB *nie są zalecane.*

* **Czy maszyny wirtualne generacji 2 obsługują przyspieszone sieci?**  
    Tak. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej za pomocą przyspieszonej sieci](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Czy plik VHDX jest obsługiwany w generacji 2?**  
    Nie, maszyny wirtualne generacji 2 obsługują tylko dysk VHD.

* **Czy maszyny wirtualne generacji 2 obsługują platformę Azure Ultra Disk Storage?**  
    Tak.

* **Czy można migrować maszynę wirtualną z generacji 1 do generacji 2?**  
    Nie, nie można zmienić generacji maszyny wirtualnej po jej utworzeniu. Jeśli chcesz przełączać się między generacjami maszyn wirtualnych, Utwórz nową maszynę wirtualną innej generacji.

* **Dlaczego mój rozmiar maszyny wirtualnej nie jest włączony w selektorze rozmiaru, gdy próbuję utworzyć maszynę wirtualną Gen2?**

    Można to rozwiązać, wykonując następujące czynności:

    1. Sprawdź, czy właściwość **generacja maszyny wirtualnej** ma ustawioną wartość **Gen 2** na karcie **Zaawansowane** .
    1. Sprawdź, czy szukasz [rozmiaru maszyny wirtualnej, który obsługuje maszyny wirtualne Gen2](#generation-2-vm-sizes).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [maszynach wirtualnych 2. generacji w funkcji Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

* Dowiedz się, jak [przygotować wirtualny dysk twardy](prepare-for-upload-vhd-image.md) do przekazania z systemów lokalnych na platformę Azure.
