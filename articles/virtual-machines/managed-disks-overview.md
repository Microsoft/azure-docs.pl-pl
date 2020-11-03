---
title: Przegląd Azure Disk Storage
description: Omówienie usługi Azure Managed disks, która obsługuje konta magazynu podczas korzystania z maszyn wirtualnych.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: contperfq1
ms.openlocfilehash: 839ba1f8a5215c9059cfeff6e2158165b154f1f7
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289359"
---
# <a name="introduction-to-azure-managed-disks"></a>Wprowadzenie do dysków zarządzanych na platformie Azure

Azure Managed disks to woluminy magazynu na poziomie bloku, które są zarządzane przez platformę Azure i używane z usługą Azure Virtual Machines. Dyski zarządzane są podobne do dysku fizycznego na serwerze lokalnym, ale zwirtualizowane. W przypadku dysków zarządzanych należy określić rozmiar dysku, typ dysku i zainicjować obsługę administracyjną dysku. Po udostępnieniu dysku platforma Azure obsługuje pozostałe.

Dostępne typy dysków to Ultra disks, dyski półprzewodnikowe w warstwie Premium (SSD), standardowe dysków SSD i standardowe dyski twarde (dysk twardy). Aby uzyskać informacje o poszczególnych typach dysków, zobacz [Wybieranie typu dysku dla maszyn wirtualnych IaaS](disks-types.md).

## <a name="benefits-of-managed-disks"></a>Zalety dysków zarządzanych

Przejdźmy do niektórych korzyści z używania usługi Managed Disks.

### <a name="highly-durable-and-available"></a>Wysoce trwałe i dostępne

Dyski zarządzane są przeznaczone do dostępności na 99,999%. W przypadku dysków zarządzanych osiąga się ją przez udostępnienie trzech replik danych, co zapewnia wysoką trwałość. Jeśli jedna lub nawet dwie repliki napotykają problemy, pozostałe repliki pomagają zapewnić trwałość danych i wysoką tolerancję na awarie. Ta architektura umożliwiła platformę Azure spójnie dostarczać trwałość klasy korporacyjnej dla dysków infrastruktury jako usługi (IaaS), a wiodący w branży ZERO% stawka niepowodzeń.

### <a name="simple-and-scalable-vm-deployment"></a>Proste i skalowalne wdrożenie maszyny wirtualnej

Korzystając z usługi Managed disks, można utworzyć do 50 000 **dysków** maszyn wirtualnych typu w ramach subskrypcji na region, co pozwala na tworzenie tysięcy **maszyn wirtualnych** w ramach jednej subskrypcji. Ta funkcja dodatkowo zwiększa skalowalność [zestawów skalowania maszyn wirtualnych](../virtual-machine-scale-sets/overview.md) , umożliwiając tworzenie do 1 000 maszyn wirtualnych w zestawie skalowania maszyn wirtualnych przy użyciu obrazu portalu Marketplace.

### <a name="integration-with-availability-sets"></a>Integracja z zestawami dostępności

Dyski zarządzane są zintegrowane z zestawami dostępności, aby upewnić się, że dyski [maszyn wirtualnych w zestawie dostępności](./manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) są wystarczająco odizolowane od siebie, aby uniknąć Single Point of Failure. Dyski są automatycznie umieszczane w różnych jednostkach skalowania magazynu (sygnatury). Jeśli sygnatura nie powiedzie się z powodu awarii sprzętu lub oprogramowania, tylko wystąpienia maszyn wirtualnych z dyskami w tych sygnaturach kończą się niepowodzeniem. Załóżmy na przykład, że aplikacja działa na pięciu maszynach wirtualnych, a maszyny wirtualne znajdują się w zestawie dostępności. Dyski dla tych maszyn wirtualnych nie będą przechowywane w tej samej sygnaturze, więc jeśli jedna sygnatura przestanie działać, inne wystąpienia aplikacji nadal będą uruchamiane.

### <a name="integration-with-availability-zones"></a>Integracja z usługą Strefy dostępności

Usługa Managed disks obsługuje [strefy dostępności](../availability-zones/az-overview.md), która jest ofertą wysokiej dostępności, która chroni aplikacje przed awariami centrów danych. Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Dzięki strefom dostępności platforma Azure oferuje najlepszą w branży umowę dotycząca poziomu usług (SLA) gwarantującą czas działania na poziomie 99,99%.

### <a name="azure-backup-support"></a>Obsługa Azure Backup

Aby chronić przed awariami regionalnymi, [Azure Backup](../backup/backup-overview.md) może służyć do tworzenia zadania tworzenia kopii zapasowej z użyciem kopii zapasowych opartych na czasie i zasad przechowywania kopii zapasowych. Dzięki temu można przeprowadzić maszynę wirtualną lub przywracanie dysków zarządzanych. Obecnie Azure Backup obsługuje dyski do 32 tebibyte (TiB) dysków. [Dowiedz się więcej](../backup/backup-support-matrix-iaas.md) o obsłudze kopii zapasowych maszyny wirtualnej platformy Azure.

### <a name="granular-access-control"></a>Szczegółowa kontrola dostępu

Za pomocą [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md) można przypisać określone uprawnienia dla dysku zarządzanego do jednego lub większej liczby użytkowników. W przypadku dysków zarządzanych są dostępne różne operacje, takie jak Odczyt, zapis (Tworzenie/aktualizowanie), usuwanie i pobieranie [identyfikatora URI sygnatury dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md) dla dysku. Można udzielić dostępu tylko do operacji wykonywanych przez osobę. Na przykład, jeśli nie chcesz, aby użytkownik skopiował dysk zarządzany do konta magazynu, możesz zrezygnować z udzielenia dostępu do akcji eksportowania dla tego dysku zarządzanego. Podobnie, jeśli nie chcesz, aby użytkownik korzystał z identyfikatora URI sygnatury dostępu współdzielonego w celu skopiowania dysku zarządzanego, możesz zrezygnować z przyznania tego uprawnienia do dysku zarządzanego.

### <a name="upload-your-vhd"></a>Przekazywanie wirtualnego dysku twardego

Przekazywanie bezpośrednie ułatwia przenoszenie dysku VHD na dysk zarządzany platformy Azure. Wcześniej musiałeś postępować zgodnie z większym procesem, który obejmuje przemieszczenie danych na koncie magazynu. Teraz jest mniej kroków. Łatwiejsze jest przekazywanie lokalnych maszyn wirtualnych na platformę Azure, przekazywanie ich do dużych dysków zarządzanych, a proces tworzenia kopii zapasowej i przywracania jest uproszczony. Zmniejsza to również koszty, umożliwiając przekazywanie danych do dysków zarządzanych bezpośrednio bez dołączania ich do maszyn wirtualnych. Możesz użyć bezpośredniego przekazywania, aby przekazać wirtualne dyski twarde o rozmiarze do 32 TiB.

Aby dowiedzieć się, jak przenieść dysk VHD na platformę Azure, zapoznaj się z artykułami [interfejsu wiersza polecenia](linux/disks-upload-vhd-to-managed-disk-cli.md) lub [programu PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md) .

## <a name="security"></a>Zabezpieczenia

### <a name="private-links"></a>Linki prywatne

W celu zaimportowania lub wyeksportowania wewnętrznego dysku zarządzanego do sieci można użyć obsługi linku prywatnego dla dysków zarządzanych. Linki prywatne umożliwiają generowanie identyfikatora URI sygnatury dostępu współdzielonego (SAS) powiązanego z niedołączonymi dyskami zarządzanymi i migawkami, których można użyć do eksportowania danych do innych regionów na potrzeby rozszerzania regionalnego, odzyskiwania po awarii i analizy śledczej. Możesz również użyć identyfikatora URI sygnatury dostępu współdzielonego, aby bezpośrednio przekazać dysk VHD do pustego dysku z lokalnego. Teraz możesz skorzystać z [prywatnych linków](../private-link/private-link-overview.md) , aby ograniczyć eksport i import dysków zarządzanych, tak aby mogły one wystąpić tylko w ramach sieci wirtualnej platformy Azure. Linki prywatne umożliwiają zapewnienie, że dane są przesyłane tylko w ramach bezpiecznej sieci szkieletowej firmy Microsoft.

Aby dowiedzieć się, jak włączyć linki prywatne do importowania lub eksportowania dysku zarządzanego, zapoznaj się z artykułami dotyczącymi [interfejsu wiersza polecenia](linux/disks-export-import-private-links-cli.md) lub [portalu](disks-enable-private-links-for-import-export-portal.md) .

### <a name="encryption"></a>Szyfrowanie

Dyski zarządzane oferują dwa różne rodzaje szyfrowania. Pierwszy jest szyfrowanie po stronie serwera (SSE), które jest wykonywane przez usługę magazynu. Druga z nich to Azure Disk Encryption (ADE), którą można włączyć na dyskach systemu operacyjnego i danych dla maszyn wirtualnych.

#### <a name="server-side-encryption"></a>Szyfrowanie po stronie serwera

Szyfrowanie po stronie serwera zapewnia szyfrowanie w spoczynku i zabezpiecza dane zgodnie z zobowiązaniami dotyczącymi bezpieczeństwa i zgodności w organizacji. Szyfrowanie po stronie serwera jest domyślnie włączone dla wszystkich dysków zarządzanych, migawek i obrazów we wszystkich regionach, w których są dostępne usługi Managed Disks. (Dyski tymczasowe, z drugiej strony, nie są szyfrowane za pomocą szyfrowania po stronie serwera, o ile nie zostanie włączone szyfrowanie na hoście; zapoznaj się z tematem [role dysków: dyski tymczasowe](#temporary-disk)).

Możesz zezwolić na platformę Azure do zarządzania kluczami, które są kluczami zarządzanymi przez platformę, lub samodzielnie zarządzać kluczami. są to klucze zarządzane przez klienta. Aby uzyskać szczegółowe informacje, odwiedź stronę dotyczącą [szyfrowania Azure Disk Storage po stronie serwera](windows/disk-encryption.md) .


#### <a name="azure-disk-encryption"></a>Usługa Azure Disk Encryption

Azure Disk Encryption pozwala na szyfrowanie dysków systemu operacyjnego i danych używanych przez maszynę wirtualną IaaS. To szyfrowanie obejmuje dyski zarządzane. W przypadku systemu Windows dyski są szyfrowane przy użyciu standardowej technologii szyfrowania funkcji BitLocker. W przypadku systemu Linux dyski są szyfrowane przy użyciu technologii DM-Crypt. Proces szyfrowania jest zintegrowany z usługą Azure Key Vault, aby umożliwić kontrolowanie kluczy szyfrowania dysków i zarządzanie nimi. Aby uzyskać więcej informacji, zobacz [Azure Disk Encryption dla maszyn wirtualnych](linux/disk-encryption-overview.md) z systemem Linux lub [Azure Disk Encryption dla maszyn wirtualnych z systemem Windows](windows/disk-encryption-overview.md).

## <a name="disk-roles"></a>Role dysku

Na platformie Azure istnieją trzy role dysków głównych: dysk danych, dysk systemu operacyjnego i dysk tymczasowy. Role te są mapowane na dyski dołączone do maszyny wirtualnej.

![Role dysków w działaniu](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Dysk danych

Dysk danych to dysk zarządzany połączony z maszyną wirtualną w celu przechowywania danych aplikacji lub innych danych, które należy zachować. Dyski danych są rejestrowane jako dyski SCSI i są oznaczone wybraną literą. Każdy dysk z danymi ma maksymalną pojemność wynoszącą 32 767 gibibajtach (GiB). Rozmiar maszyny wirtualnej określa liczbę dysków z danymi, które można dołączyć do niej, oraz typ magazynu, którego można użyć do hostowania dysków.

### <a name="os-disk"></a>Dysk systemu operacyjnego

Każda maszyna wirtualna ma jeden dołączony dysk systemu operacyjnego. Ten dysk systemu operacyjnego ma wstępnie zainstalowany system operacyjny, który został wybrany podczas tworzenia maszyny wirtualnej. Ten dysk zawiera wolumin rozruchowy.

Ten dysk ma maksymalną pojemność wynoszącą 4 095 GiB.

### <a name="temporary-disk"></a>Dysk tymczasowy

Większość maszyn wirtualnych zawiera dysk tymczasowy, który nie jest dyskiem zarządzanym. Dysk tymczasowy zapewnia krótkoterminowy magazyn dla aplikacji i procesów, który jest przeznaczony tylko do przechowywania danych, takich jak pliki stron lub plików wymiany. Dane na dysku tymczasowym mogą zostać utracone podczas [zdarzenia konserwacji](./manage-availability.md?toc=%252fazure%252fvirtual-machines%252fwindows%252ftoc.json#understand-vm-reboots---maintenance-vs-downtime) lub po ponownym [wdrożeniu maszyny wirtualnej](troubleshooting/redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json). Po pomyślnym przeprowadzeniu standardowego ponownego uruchomienia maszyny wirtualnej dane na dysku tymczasowym będą utrwalane. Aby uzyskać więcej informacji o maszynach wirtualnych bez dysków tymczasowych, zobacz [rozmiary maszyn wirtualnych platformy Azure, na których nie ma lokalnego dysku tymczasowego](azure-vms-no-temp-disk.md).

Na maszynach wirtualnych z systemem Linux na platformie Azure dysk tymczasowy jest zazwyczaj/dev/sdb i na maszynach wirtualnych systemu Windows dysk tymczasowy jest D: domyślnie. Dysk tymczasowy nie jest szyfrowany przez szyfrowanie po stronie serwera, chyba że zostanie włączone szyfrowanie na hoście.

## <a name="managed-disk-snapshots"></a>Migawki dysków zarządzanych

Migawka dysku zarządzanego jest tylko do odczytu pełna kopia dysku zarządzanego, która jest domyślnie przechowywana jako standardowy dysk zarządzany. Za pomocą migawek można tworzyć kopie zapasowe dysków zarządzanych w dowolnym momencie. Te migawki istnieją niezależnie od dysku źródłowego i mogą być używane do tworzenia nowych dysków zarządzanych. 

Migawki są rozliczane na podstawie użytego rozmiaru. Na przykład, jeśli utworzysz migawkę dysku zarządzanego o pojemności 64 GiB i rzeczywistej używanej wielkości danych wynoszącej 10 GiB, ta migawka jest naliczana tylko za użyty rozmiar danych wynoszący 10 GiB. Używany rozmiar migawek można zobaczyć, przeglądając [raport użycia platformy Azure](../cost-management-billing/understand/review-individual-bill.md). Na przykład jeśli używany rozmiar danych migawki to 10 GiB, Raport **dziennego** użycia będzie zawierał 10 GIB/(31 dni) = 0,3226 jako ilość zużytą.

Aby dowiedzieć się więcej na temat tworzenia migawek dla dysków zarządzanych, zobacz następujące zasoby:

- [Tworzenie migawki dysku zarządzanego w systemie Windows](windows/snapshot-copy-managed-disk.md)
- [Tworzenie migawki dysku zarządzanego w systemie Linux](linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Obrazy

Dyski zarządzane obsługują również tworzenie zarządzanego obrazu niestandardowego. Możesz utworzyć obraz z niestandardowego wirtualnego dysku twardego na koncie magazynu lub bezpośrednio z maszyny wirtualnej z uogólnioną (Sysprep). Ten proces przechwytuje pojedynczy obraz. Ten obraz zawiera wszystkie dyski zarządzane skojarzone z maszyną wirtualną, w tym dyski systemu operacyjnego i danych. Ten zarządzany obraz niestandardowy umożliwia tworzenie setek maszyn wirtualnych przy użyciu obrazu niestandardowego bez konieczności kopiowania kont magazynu i zarządzania nimi.

Aby uzyskać informacje na temat tworzenia obrazów, zobacz następujące artykuły:

- [Jak przechwycić zarządzany obraz uogólnionej maszyny wirtualnej na platformie Azure](windows/capture-image-resource.md)
- [Uogólnianie i przechwytywanie maszyny wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure](linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Obrazy i migawki

Ważne jest, aby zrozumieć różnicę między obrazami i migawkami. Za pomocą dysków zarządzanych można utworzyć obraz uogólnionej maszyny wirtualnej, która została cofnięta. Ten obraz zawiera wszystkie dyski dołączone do maszyny wirtualnej. Ten obraz służy do tworzenia maszyny wirtualnej i zawiera wszystkie dyski.

Migawka to kopia dysku w punkcie w czasie, w którym wykonywana jest migawka. Ma zastosowanie tylko do jednego dysku. Jeśli masz maszynę wirtualną, która ma jeden dysk (dysk systemu operacyjnego), możesz wykonać migawkę lub obraz, a następnie utworzyć maszynę wirtualną na podstawie migawki lub obrazu.

Migawka nie ma świadomości dotyczącej żadnego dysku z wyjątkiem tego, który zawiera. Sprawia to, że może to powodować problemy z użyciem w scenariuszach wymagających koordynacji wielu dysków, takich jak rozłożenie. Migawki muszą mieć możliwość koordynacji ze sobą i nie są obecnie obsługiwane.

## <a name="disk-allocation-and-performance"></a>Alokacja i wydajność dysku

Na poniższym diagramie przedstawiono alokację przepustowości i liczby operacji we/wy na sekundę w czasie rzeczywistym dla dysków przy użyciu systemu udostępniania trzech poziomów:

![System obsługi administracyjnej trzech poziomów przedstawiający przepustowość i alokację IOPS](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Wstępne Inicjowanie obsługi określa liczbę operacji we/wy na dysku i przypisanie przepustowości.  Na drugim poziomie Host serwera obliczeniowego implementuje obsługę dysków SSD, stosując ją tylko do danych przechowywanych na dysku SSD serwera, który obejmuje dyski z buforowaniem (ReadWrite i ReadOnly), a także dyski lokalne i tymczasowe. Na koniec Inicjowanie obsługi sieci maszyn wirtualnych odbywa się na trzecim poziomie dla wszystkich operacji we/wy, które Host obliczeń wysyła do zaplecza usługi Azure Storage. W tym schemacie wydajność maszyny wirtualnej zależy od różnych czynników, od tego, jak maszyna wirtualna używa lokalnego dysku SSD, do liczby podłączonych dysków, a także typu wydajności i buforowania dysków, które zostały podłączone.

Na przykład te ograniczenia Standard_DS1v1 maszyna wirtualna jest niedostępna przed osiągnięciem możliwości 5 000 IOPS dysku P30, niezależnie od tego, czy jest buforowana, czy nie, ze względu na limity na dyskach SSD i sieci:

![Standard_DS1v1 przykład alokacji](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Platforma Azure używa priorytetowego kanału sieciowego dla ruchu dyskowego, który uzyskuje pierwszeństwo przed innym niskim priorytetem ruchu sieciowego. Ułatwia to dyskom zachowanie oczekiwanej wydajności w przypadku rywalizacji o sieć. Podobnie usługa Azure Storage obsługuje rywalizacje zasobów i inne problemy w tle z automatycznym równoważeniem obciążenia. Usługa Azure Storage przydziela wymagane zasoby podczas tworzenia dysku oraz stosuje aktywne i ponownie aktywne równoważenie zasobów w celu obsługi ruchu. Dzięki temu dyski mogą utrzymać oczekiwaną liczbę operacji we/wy na sekundę i przepływności. Możesz użyć metryk na poziomie maszyny wirtualnej i na poziomie dysku do śledzenia wydajności i konfiguracji alertów zgodnie z wymaganiami.

Zapoznaj się z naszym [projektem w celu uzyskania wysokiej wydajności](premium-storage-performance.md) artykułu, aby poznać najlepsze rozwiązania dotyczące optymalizowania konfiguracji maszyn wirtualnych i dysków, dzięki czemu można osiągnąć żądaną wydajność

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz uzyskać więcej szczegółów na temat dysków zarządzanych, zapoznaj się z tematem: [Lepsza odporność maszyn wirtualnych platformy Azure za pomocą Managed disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).

Dowiedz się więcej na temat poszczególnych typów dysków oferowanych przez platformę Azure, które są odpowiednie dla Twoich potrzeb, i poznaj ich cele wydajności w naszym artykule na temat typów dysków.

> [!div class="nextstepaction"]
> [Wybieranie typu dysku dla maszyn wirtualnych IaaS](disks-types.md)