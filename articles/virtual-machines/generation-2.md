---
title: Pomoc techniczna platformy Azure dla maszyn wirtualnych 2. generacji
description: Omówienie pomocy technicznej platformy Azure dla maszyn wirtualnych 2. generacji
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/26/2021
ms.author: jushiman
ms.openlocfilehash: dcc599daaa8d124c7188165ff5e024767a5e3cd7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672641"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Obsługa maszyn wirtualnych 2. generacji na platformie Azure

Obsługa maszyn wirtualnych 2. generacji jest teraz dostępna na platformie Azure. Nie można zmienić generacji maszyny wirtualnej po jej utworzeniu. przed wybraniem generacji zapoznaj się z uwagami na tej stronie.

Maszyny wirtualne generacji 2 obsługują kluczowe funkcje, które nie są obsługiwane przez maszyny wirtualne generacji 1. Te funkcje obejmują zwiększoną ilość pamięci, rozszerzenia funkcji ochrony oprogramowania firmy Intel (Intel SGX) i zwirtualizowaną pamięć trwałą (vPMEM). Maszyny wirtualne 2. generacji działające lokalnie mają pewne funkcje, które nie są jeszcze obsługiwane na platformie Azure. Aby uzyskać więcej informacji, zobacz sekcję [funkcje i możliwości](#features-and-capabilities) .

Maszyny wirtualne generacji 2 wykorzystują nową architekturę rozruchową opartą na interfejsie UEFI zamiast architektury opartej na systemie BIOS używanej przez maszyny wirtualne generacji 1. W porównaniu z maszynami wirtualnymi generacji 1 maszyny wirtualne generacji 2 mogą mieć ulepszony czas rozruchu i instalacji. Aby zapoznać się z omówieniem maszyn wirtualnych 2. generacji i niektórych różnic między generacjami 1 i 2, zobacz temat [Tworzenie maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Rozmiary maszyn wirtualnych generacji 2

Maszyny wirtualne generacji 1 są obsługiwane przez wszystkie rozmiary maszyn wirtualnych na platformie Azure (z wyjątkiem maszyn wirtualnych z serii Mv2). Platforma Azure oferuje teraz obsługę generacji 2 dla następującej wybranej serii maszyn wirtualnych:


* [Seria B](sizes-b-series-burstable.md)
* [Seria DCsv2](dcv2-series.md)
* [Seria DSv2](dv2-dsv2-series.md)
* [Seria Dsv3](dv3-dsv3-series.md)
* [Seria Dsv4](dv4-dsv4-series.md)
* [Seria Dasv4](dav4-dasv4-series.md)
* [Seria Ddsv4](ddv4-ddsv4-series.md)
* [Seria Esv3](ev3-esv3-series.md)
* [Seria Esv4](ev4-esv4-series.md)
* [Seria Easv4](eav4-easv4-series.md)
* [Seria Edsv4](edv4-edsv4-series.md)
* [Seria Fsv2](fsv2-series.md)
* [Seria GS](sizes-previous-gen.md#gs-series)
* [Seria HB](hb-series.md)
* [Seria HC](hc-series.md)
* [Seria Ls](sizes-previous-gen.md#ls-series) 
* [Seria Lsv2](lsv2-series.md)
* [Seria M](m-series.md)
* [Mv2 — seria](mv2-series.md)<sup>1</sup>
* [Seria NCv2](ncv2-series.md) 
* [Seria NCv3](ncv3-series.md)
* [Seria ND](nd-series.md)
* [Seria NVv3](nvv3-series.md)
* [Seria NVv4](nvv4-series.md)
* [Seria NCasT4_v3](nct4-v3-series.md)

<sup>1</sup> Mv2 — seria nie obsługuje obrazów maszyn wirtualnych generacji 1 i obsługuje tylko podzestaw obrazów generacji 2. Szczegóły można znaleźć w [dokumentacji Mv2](mv2-series.md) .


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
> Konkretne rozmiary maszyn wirtualnych, takie jak Mv2-Series, obsługują tylko podzbiór tych obrazów — szczegółowe informacje można znaleźć w dokumentacji odpowiedniego rozmiaru maszyny wirtualnej.

## <a name="on-premises-vs-azure-generation-2-vms"></a>Lokalna a maszyny wirtualne 2. generacji platformy Azure

Platforma Azure nie obsługuje obecnie niektórych funkcji, które są obsługiwane przez funkcję Hyper-V w przypadku maszyn wirtualnych 2. generacji.

| Funkcja generacji 2                | Lokalna funkcja Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Bezpieczny rozruch                         | :heavy_check_mark:  | Z zaufanym uruchomieniem (wersja zapoznawcza)   |
| Chroniona maszyna wirtualna                         | :heavy_check_mark:  | y   |
| vTPM                                | :heavy_check_mark:  | Z zaufanym uruchomieniem (wersja zapoznawcza)  |
| Zabezpieczenia oparte na wirtualizacji (VBS) | :heavy_check_mark:  | Z zaufanym uruchomieniem (wersja zapoznawcza)   |
| VHDX format                         | :heavy_check_mark:  | y   |

Aby uzyskać więcej informacji, zobacz temat [Zaufane uruchomienia (wersja zapoznawcza)](trusted-launch.md).

## <a name="features-and-capabilities"></a>Funkcje i możliwości

### <a name="generation-1-vs-generation-2-features"></a>Funkcje generacji 1 a generacja 2

| Cecha | Pierwsza generacja | Druga generacja |
|---------|--------------|--------------|
| Rozruch             | PCAT                      | UEFI                               |
| Kontrolery dysków | IDE                       | SCSI                               |
| Rozmiary maszyn wirtualnych         | Wszystkie rozmiary maszyn wirtualnych | [Zobacz dostępne rozmiary](#generation-2-vm-sizes) |

### <a name="generation-1-vs-generation-2-capabilities"></a>Możliwości generacji 1 a generacja 2

| Możliwość | Pierwsza generacja | Druga generacja |
|------------|--------------|--------------|
| Dysk systemu operacyjnego > 2 TB                    | y                | :heavy_check_mark: |
| Niestandardowy dysk/obraz/system operacyjny wymiany         | :heavy_check_mark: | :heavy_check_mark: |
| Obsługa zestawu skalowania maszyn wirtualnych | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Tworzenie/przywracanie kopii zapasowych                    | :heavy_check_mark: | :heavy_check_mark: |
| Galeria obrazów udostępnionych              | :heavy_check_mark: | :heavy_check_mark: |
| [Szyfrowanie dysków Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md)             | :heavy_check_mark: | :heavy_check_mark:                |
| [Szyfrowanie po stronie serwera](disk-encryption.md)            | :heavy_check_mark: | :heavy_check_mark: |

## <a name="creating-a-generation-2-vm"></a>Tworzenie maszyny wirtualnej generacji 2

### <a name="marketplace-image"></a>Obraz z witryny Marketplace

W Azure Portal lub interfejsie wiersza polecenia platformy Azure można tworzyć maszyny wirtualne 2. generacji z obrazu portalu Marketplace, który obsługuje rozruch z interfejsem UEFI.

#### <a name="azure-portal"></a>Azure Portal

Poniżej przedstawiono procedurę tworzenia maszyny wirtualnej generacji 2 (Gen2) w Azure Portal.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Wybierz pozycję **Utwórz zasób**.
1. Po lewej stronie kliknij pozycję **Zobacz wszystko** w witrynie Azure Marketplace.
1. Wybierz obraz, który obsługuje Gen2.
1. Kliknij pozycję **Utwórz**.
1. Na karcie **Zaawansowane** w sekcji **generacja maszyny wirtualnej** wybierz opcję **generacji 2** .
1. Na karcie **podstawowe** w obszarze **szczegóły wystąpienia** przejdź do pozycji **rozmiar** i Otwórz blok **Wybieranie rozmiaru maszyny wirtualnej** .
1. Wybierz [obsługiwaną maszynę wirtualną generacji 2](#generation-2-vm-sizes).
1. Przejdź do pozostałych stron, aby zakończyć tworzenie maszyny wirtualnej.

![Wybierz maszynę wirtualną generacji 1 lub Gen 2](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Możesz również użyć programu PowerShell do utworzenia maszyny wirtualnej, bezpośrednio odwołującej się do jednostki SKU generacji 1 lub 2.

Na przykład użyj następującego polecenia cmdlet programu PowerShell, aby wyświetlić listę jednostek SKU w `WindowsServer` ofercie.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Jeśli tworzysz maszynę wirtualną z systemem Windows Server 2012 jako system operacyjny, wybierz jednostkę SKU maszyny wirtualnej generacji 1 (BIOS) lub 2 (UEFI), która wygląda następująco:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Zapoznaj się z sekcją [funkcje i możliwości](#features-and-capabilities) , aby zapoznać się z bieżącą listą obsługiwanych obrazów z portalu Marketplace.

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Alternatywnie możesz użyć interfejsu wiersza polecenia platformy Azure, aby wyświetlić dostępne obrazy generacji 2 wymienione przez **wydawcę**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

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

  Dyski systemu operacyjnego o rozmiarze większym niż 2 TiB są nowe do maszyn wirtualnych 2. generacji. Domyślnie dyski systemu operacyjnego są mniejsze niż 2 TiB dla maszyn wirtualnych 2. generacji. Można zwiększyć rozmiar dysku do zalecanej maksymalnie 4 TiB. Użyj interfejsu wiersza polecenia platformy Azure lub Azure Portal, aby zwiększyć rozmiar dysku systemu operacyjnego. Informacje o sposobach rozszerzania dysków w programie można znaleźć w temacie **zmiana rozmiaru dysku** dla [systemu Windows](./windows/expand-os-disk.md) lub [Linux](./linux/resize-os-disk-gpt-partition.md).

  Aby zwiększyć rozmiar dysku systemu operacyjnego z Azure Portal:

  1. W Azure Portal przejdź do strony właściwości maszyny wirtualnej.
  1. Aby wyłączyć i cofnąć alokację maszyny wirtualnej, wybierz przycisk **Zatrzymaj** .
  1. W sekcji **dyski** wybierz dysk systemu operacyjnego, który chcesz zwiększyć.
  1. W sekcji **dyski** wybierz pozycję **Konfiguracja**, a następnie zaktualizuj **rozmiar** do wartości, która ma zostać wybrana.
  1. Wróć do strony właściwości maszyny wirtualnej i **Uruchom** maszynę wirtualną.
  
  Może zostać wyświetlone ostrzeżenie dotyczące dysków systemu operacyjnego większych niż 2 TiB. Ostrzeżenie nie dotyczy maszyn wirtualnych 2. generacji. Jednak rozmiary dysków systemu operacyjnego większe niż 4 TiB nie są obsługiwane.

* **Czy maszyny wirtualne generacji 2 obsługują przyspieszone sieci?**  
    Tak. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny wirtualnej za pomocą przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-cli.md).

* **Czy maszyny wirtualne generacji 2 obsługują bezpieczny rozruch lub vTPM na platformie Azure?**
    Zarówno vTPM, jak i bezpieczny rozruch są funkcjami zaufanego uruchamiania (wersja zapoznawcza) dla maszyn wirtualnych 2. generacji. Aby uzyskać więcej informacji, zobacz [Zaufane uruchomienie](trusted-launch.md).
    
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

Dowiedz się więcej o [zaufaniu uruchamiania (wersja zapoznawcza)](trusted-launch-portal.md) z maszynami wirtualnymi generacji 2.

Dowiedz się więcej o [maszynach wirtualnych 2. generacji w funkcji Hyper-V](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
