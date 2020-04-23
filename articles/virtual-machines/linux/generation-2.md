---
title: Obsługa platformy Azure dla maszyn wirtualnych generacji 2
description: Omówienie obsługi platformy Azure dla maszyn wirtualnych generacji 2
author: ju-shim
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: ae76c30f63c87f7e741fff31792d520fb144b93b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084281"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Obsługa maszyn wirtualnych generacji 2 na platformie Azure

Obsługa maszyn wirtualnych generacji 2 jest teraz dostępna na platformie Azure. Nie można zmienić generacji maszyny wirtualnej po jej utworzeniu, więc zapoznaj się z zagadnieniami na tej stronie przed wybraniem generacji.

Maszyny wirtualne generacji 2 obsługują kluczowe funkcje, które nie są obsługiwane na maszynach wirtualnych generacji 1. Funkcje te obejmują zwiększoną ilość pamięci, rozszerzenia Intel Software Guard Extensions (Intel SGX) i zwirtualizowaną pamięć trwałą (vPMEM). Maszyny wirtualne generacji 2 działające lokalnie mają pewne funkcje, które nie są jeszcze obsługiwane na platformie Azure. Aby uzyskać więcej informacji, zobacz [funkcje i możliwości](#features-and-capabilities) sekcji.

Maszyny wirtualne generacji 2 używają nowej architektury rozruchowej opartej na UEFI, a nie architektury opartej na systemie BIOS używanej przez maszyny wirtualne generacji 1. W porównaniu z maszynami wirtualnymi generacji 1 generacji 2 maszyny wirtualne mogły wydłuszyć czas rozruchu i instalacji. Aby uzyskać przegląd maszyn wirtualnych generacji 2 i niektórych różnic między generacją 1 i generacją 2, zobacz [Czy należy utworzyć maszynę wirtualną generacji 1 lub 2 w trybie Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Rozmiary maszyn wirtualnych generacji 2

Maszyny wirtualne generacji 1 są obsługiwane przez wszystkie rozmiary maszyn wirtualnych na platformie Azure (z wyjątkiem maszyn wirtualnych z serii Mv2). Platforma Azure oferuje teraz obsługę generacji 2 dla następujących wybranych serii maszyn wirtualnych:

* [Seria B](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [Seria DCsv2](../dcv2-series.md)
* [Seria DSv2](../dv2-dsv2-series.md) i [dsv3](../dv3-dsv3-series.md)
* [Seria Esv3](../ev3-esv3-series.md)
* [Seria Fsv2](../fsv2-series.md)
* [Seria GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [Seria HB](../hb-series.md)
* [Seria HC](../hc-series.md)
* [Seria LS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) i [seria Lsv2](../lsv2-series.md)
* [Seria Mv2](../mv2-series.md)
* [Seria NCv2](../ncv2-series.md) i [seria NCv3](../ncv3-series.md)
* [Seria ND](../nd-series.md)
* [Seria NVv3](../nvv3-series.md)

> [!NOTE]
> Użycie obrazów maszyn wirtualnych generacji 2 dla maszyn wirtualnych z serii Mv2 jest ogólnie dostępne, ponieważ seria Mv2 współpracuje wyłącznie z obrazami maszyn wirtualnych generacji 2. Obrazy maszyn wirtualnych generacji 1 nie są obsługiwane na maszynach wirtualnych z serii Mv2. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Obrazy maszyn wirtualnych generacji 2 w portalu Azure Marketplace

Maszyny wirtualne generacji 2 obsługują następujące obrazy z Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10
* SUSE Linux Enterprise Server 15 zw.
* SUSE Linux Enterprise Server 12 zw.
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 7.0
* Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4
* Oracle Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>Maszyny wirtualne lokalnej a maszyny wirtualne generacji 2 platformy Azure

Platforma Azure nie obsługuje obecnie niektórych funkcji obsługiwanych przez lokalną funkcję Hyper-V dla maszyn wirtualnych generacji 2.

| Funkcja generacji 2                | Lokalna funkcja Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Bezpieczny rozruch                         | :heavy_check_mark:  | :x:   |
| Chroniona maszyna wirtualna                         | :heavy_check_mark:  | :x:   |
| vTPM (polski)                                | :heavy_check_mark:  | :x:   |
| Zabezpieczenia oparte na wirtualizacji (VBS) | :heavy_check_mark:  | :x:   |
| VHDX format                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Funkcje i możliwości

### <a name="generation-1-vs-generation-2-features"></a>Funkcje generacji 1 a generacji 2

| Funkcja | Pierwsza generacja | Druga generacja |
|---------|--------------|--------------|
| Rozruchu             | Z o.o.         | Uefi |
| Kontrolery dysków | IDE          | SCSI |
| Rozmiary maszyn wirtualnych         | Wszystkie rozmiary maszyn wirtualnych | Tylko maszyny wirtualne obsługujące pamięć masową w wersji premium |

### <a name="generation-1-vs-generation-2-capabilities"></a>Możliwości generacji 1 a generacji 2

| Możliwości | Pierwsza generacja | Druga generacja |
|------------|--------------|--------------|
| Dysk systemu operacyjnego > 2 TB                    | :x:                | :heavy_check_mark: |
| Niestandardowy dysk/obraz/swap OS         | :heavy_check_mark: | :heavy_check_mark: |
| Obsługa zestawu skalowania maszyny wirtualnej | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Tworzenie kopii zapasowych/przywracanie                    | :heavy_check_mark: | :heavy_check_mark: |
| Galeria zdjęć udostępnionych              | :heavy_check_mark: | :heavy_check_mark: |
| Szyfrowanie dysków platformy Azure             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Tworzenie maszyny wirtualnej generacji 2

### <a name="marketplace-image"></a>Obraz portalu Marketplace

W witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure można utworzyć maszyny wirtualne generacji 2 z obrazu portalu Marketplace, który obsługuje rozruch UEFI.

#### <a name="azure-portal"></a>Azure Portal

Poniżej znajdują się kroki, aby utworzyć generacji 2 (Gen2) maszyny Wirtualnej w witrynie Azure portal.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
1. Wybierz pozycję **Utwórz zasób**.
1. Kliknij **pozycję Zobacz wszystkie** z portalu Azure Marketplace po lewej stronie.
1. Wybierz obraz, który obsługuje gen2.
1. Kliknij przycisk **Utwórz**.
1. Na karcie **Zaawansowane** w sekcji **Generowanie maszyn wirtualnych** wybierz opcję **Gen 2.**
1. Na karcie **Podstawy** w obszarze **Szczegóły wystąpienia**przejdź do **opcji Rozmiar** i otwórz blok Wybierz rozmiar **maszyny Wirtualnej.**
1. Wybierz [obsługiwaną maszynę wirtualną generacji 2](#generation-2-vm-sizes).
1. Przejdź przez [przepływ tworzenia witryny Azure portal,](quick-create-portal.md) aby zakończyć tworzenie maszyny Wirtualnej.

![Wybierz maszynę wirtualną Gen 1 lub Gen 2](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Za pomocą programu PowerShell można również utworzyć maszynę wirtualną, bezpośrednio odwołując się do jednostki SKU generacji 1 lub generacji 2.

Na przykład użyj następującego polecenia cmdlet programu PowerShell, `WindowsServer` aby uzyskać listę jednostek SKU w ofercie.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Alternatywnie można użyć interfejsu wiersza polecenia platformy Azure, aby wyświetlić wszystkie dostępne obrazy generacji 2 wymienione przez **program Publisher**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Jeśli tworzysz maszynę wirtualną z systemem Windows Server 2012 jako systemem operacyjnym, wybierzesz jednostkę SKU maszyny wirtualnej generacji 1 (BIOS) lub generację 2 (UEFI), która wygląda następująco:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Zobacz sekcję [Funkcje i możliwości,](#features-and-capabilities) aby uzyskać aktualną listę obsługiwanych obrazów w portalu Marketplace.

### <a name="managed-image-or-managed-disk"></a>Obraz zarządzany lub dysk zarządzany

Maszynę wirtualną generacji 2 można utworzyć z obrazu zarządzanego lub dysku zarządzanego w taki sam sposób, jak maszyna wirtualna generacji 1.

### <a name="virtual-machine-scale-sets"></a>Zestawy skalowania maszyn wirtualnych

Maszyny wirtualne generacji 2 można również tworzyć przy użyciu zestawów skalowania maszyny wirtualnej. W interfejsu wiersza polecenia platformy Azure użyj zestawów skalowania platformy Azure, aby utworzyć maszyny wirtualne generacji 2.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

* **Czy maszyny wirtualne generacji 2 są dostępne we wszystkich regionach platformy Azure?**  
    Tak. Ale nie wszystkie [rozmiary maszyn wirtualnych generacji 2](#generation-2-vm-sizes) są dostępne w każdym regionie. Dostępność maszyny Wirtualnej generacji 2 zależy od dostępności rozmiaru maszyny Wirtualnej.

* **Czy istnieje różnica w cenie między generacją 1 a generacją 2 maszyn wirtualnych?**  
    Nie.

* **Mam plik vhd z mojej lokalnej generacji 2 VM. Czy można użyć tego pliku vhd do utworzenia maszyny Wirtualnej generacji 2 na platformie Azure?**
  Tak, możesz przenieść plik vhd generacji 2 na platformę Azure i użyć go do utworzenia maszyny Wirtualnej generacji 2. Wykonaj następujące czynności:
    1. Przekaż .vhd do konta magazynu w tym samym regionie, w którym chcesz utworzyć maszynę wirtualną.
    1. Utwórz dysk zarządzany z pliku vhd. Ustaw właściwość Generacji funkcji Hyper-V na V2. Następujące polecenia programu PowerShell ustawiają właściwość generowania funkcji Hyper-V podczas tworzenia dysku zarządzanego.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Gdy dysk jest dostępny, utwórz maszynę wirtualną, dołączając ten dysk. Utworzona maszyna wirtualna będzie generacji 2 maszyny Wirtualnej.
    Po utworzeniu maszyny Wirtualnej generacji 2 można opcjonalnie uogólniać obraz tej maszyny Wirtualnej. Uogólniając obraz można go używać do tworzenia wielu maszyn wirtualnych.

* **Jak zwiększyć rozmiar dysku systemu operacyjnego?**  
  Dyski systemu operacyjnego większe niż 2 TB są nowe do generacji 2 maszyn wirtualnych. Domyślnie dyski systemu operacyjnego są mniejsze niż 2 TB dla generacji 2 maszyn wirtualnych. Można zwiększyć rozmiar dysku do zalecanego maksimum 4 TB. Użyj interfejsu wiersza polecenia platformy Azure lub witryny Azure portal, aby zwiększyć rozmiar dysku systemu operacyjnego. Aby uzyskać informacje dotyczące programowego rozszerzania dysków, zobacz [Ponowne rozmiary dysku](expand-disks.md).

  Aby zwiększyć rozmiar dysku systemu operacyjnego z witryny Azure portal:

  1. W witrynie Azure portal przejdź do strony właściwości maszyny Wirtualnej.
  1. Aby zamknąć i zdelocytować maszynę wirtualną, wybierz przycisk **Zatrzymaj.**
  1. W sekcji **Dyski** wybierz dysk systemu operacyjnego, który chcesz zwiększyć.
  1. W sekcji **Dyski** wybierz pozycję **Konfiguracja**i zaktualizuj **rozmiar** do żądanej wartości.
  1. Wróć do strony właściwości maszyny Wirtualnej i **uruchom** maszynę wirtualną.

  Może zostać wyświetlone ostrzeżenie dla dysków systemu operacyjnego o pojemności większej niż 2 TB. Ostrzeżenie nie ma zastosowania do maszyn wirtualnych generacji 2. Jednak rozmiary dysku systemu operacyjnego większe niż 4 TB nie są *zalecane.*

* **Czy maszyny wirtualne generacji 2 obsługują przyspieszoną sieć?**  
    Tak. Aby uzyskać więcej informacji, zobacz [Tworzenie maszyny Wirtualnej z przyspieszoną siecią](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Czy VHDX jest obsługiwany w generacji 2?**  
    Nie, generacji 2 maszyny wirtualne obsługują tylko VHD.

* **Czy maszyny wirtualne generacji 2 obsługują usługę Azure Ultra Disk Storage?**  
    Tak.

* **Czy mogę przeprowadzić migrację maszyny Wirtualnej z generacji 1 do generacji 2?**  
    Nie, nie można zmienić generowania maszyny Wirtualnej po jej utworzeniu. Jeśli chcesz przełączać się między generacjami maszyn wirtualnych, utwórz nową maszynę wirtualną innej generacji.

* **Dlaczego rozmiar maszyny Wirtualnej nie jest włączony w selektorze rozmiaru podczas próby utworzenia maszyny Wirtualnej Gen2?**

    Można to rozwiązać, wykonując następujące czynności:

    1. Sprawdź, czy właściwość **generowania maszyny Wirtualnej** jest ustawiona na **Gen 2** na karcie **Zaawansowane.**
    1. Sprawdź, czy szukasz [rozmiaru maszyny Wirtualnej obsługującej maszyny wirtualne Gen2](#generation-2-vm-sizes).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [maszynach wirtualnych generacji 2 w funkcji Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
