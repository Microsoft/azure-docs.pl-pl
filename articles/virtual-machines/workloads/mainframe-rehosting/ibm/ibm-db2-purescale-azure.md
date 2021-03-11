---
title: IBM DB2 pureScale na platformie Azure
description: W tym artykule przedstawiono architekturę do uruchamiania środowiska programu IBM DB2 pureScale na platformie Azure.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 08e7a594fba3e660ea1a2a8561bcdeed236b1dfb
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561589"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale na platformie Azure

Środowisko IBM DB2 pureScale zapewnia klaster bazy danych dla platformy Azure o wysokiej dostępności i skalowalności w systemach operacyjnych Linux. W tym artykule przedstawiono architekturę uruchamiania programu DB2 pureScale na platformie Azure.

## <a name="overview"></a>Omówienie

Przedsiębiorstwa mają długie użycie tradycyjnych platform zarządzania relacyjnymi bazami danych (RDBMS) do zaspokajania potrzeb związanych z przetwarzaniem transakcji online (OLTP). W tych dniach wiele przeprowadzi migrację środowisk baz danych opartych na dyskach mainframe na platformę Azure w celu zwiększenia wydajności, obniżenia kosztów i utrzymania stałej struktury kosztów operacyjnych. Migracja jest często pierwszym krokiem w przypadku modernizacji starszej platformy. 

Niedawno klient przedsiębiorstwa przeobsługiwał swoje środowisko firmy IBM DB2 działające w systemie z/OS do programu IBM DB2 pureScale na platformie Azure. Rozwiązanie klastrowe bazy danych DB2 pureScale zapewnia wysoką dostępność i skalowalność w systemach operacyjnych Linux. Klient programu DB2 pomyślnie uruchomił jako autonomiczne, skalowalne wystąpienie na jednej maszynie wirtualnej (VM) w dużym systemie skalowania na platformie Azure przed zainstalowaniem programu DB2 pureScale. 

Chociaż nie jest identyczny z oryginalnym środowiskiem, program IBM DB2 pureScale w systemie Linux oferuje podobne funkcje wysokiej dostępności i skalowalności, jak program IBM DB2 dla systemu z/OS działający w równoległej konfiguracji Sysplex na komputerze mainframe. W tym scenariuszu klaster jest połączony za pośrednictwem protokołu iSCSI z udostępnionym klastrem magazynu. Korzystamy z systemu plików GlusterFS, bezpłatnego, skalowalnego, rozproszonego systemu plików typu open source, który jest zoptymalizowany pod kątem magazynu w chmurze. Jednak firma IBM nie obsługuje już tego rozwiązania. Aby zachować pomoc techniczną od firmy IBM, musisz użyć obsługiwanego systemu plików zgodnego z technologią iSCSI. Firma Microsoft oferuje Bezpośrednie miejsca do magazynowania (S2D) jako opcję

W tym artykule opisano architekturę używaną podczas migracji platformy Azure. Aby przetestować konfigurację, klient Red Hat Linux 7,4. Ta wersja jest dostępna w portalu Azure Marketplace. Przed wybraniem dystrybucji systemu Linux upewnij się, że aktualnie obsługiwane wersje zostały zweryfikowane. Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją dla programu [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) i [GlusterFS](https://docs.gluster.org/en/latest/).

Ten artykuł stanowi punkt początkowy planu implementacji bazy danych DB2. Wymagania biznesowe będą się różnić, ale zastosowanie ma ten sam wzorzec podstawowy. Tego wzorca architektury można także użyć do obsługi aplikacji OLAP (Online Analytical Processing) na platformie Azure.

W tym artykule nie omówiono różnic i możliwych zadań migracji dotyczących przenoszenia bazy danych programu IBM DB2 dla systemu z/OS do programu IBM DB2 pureScale działającej w systemie Linux. Nie zapewniają natomiast szacunków rozmiarów i analiz obciążeń na potrzeby przechodzenia z programu DB2 z/OS do DB2 pureScale. 

Aby ułatwić podejmowanie decyzji na temat najlepszej architektury DB2 pureScale dla danego środowiska, zalecamy całkowite oszacowanie rozmiarów i naprowadzenie hipotez. Upewnij się, że w systemie źródłowym zauważasz Sysplex równoległy z/OS na architekturę udostępniania danych, konfigurację funkcji sprzęgania i dane statystyczne użycia funkcji rozproszonego przetwarzania danych (DDF).

> [!NOTE]
> W tym artykule opisano jedno podejście do migracji bazy danych DB2, ale istnieją inne. Na przykład program DB2 pureScale może być również uruchamiany w zwirtualizowanych środowiskach lokalnych. Firma IBM obsługuje bazy danych DB2 na Microsoft Hyper-V w różnych konfiguracjach. Aby uzyskać więcej informacji, zobacz [Architektura wirtualizacji pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) w programie DB2 w centrum wiedzy firmy IBM.

## <a name="architecture"></a>Architektura

Aby zapewnić wysoką dostępność i skalowalność na platformie Azure, możesz użyć udostępnionej architektury danych dla programu DB2 pureScale. W ramach migracji klienta użyto następującej przykładowej architektury.

![DB2 pureScale na maszynach wirtualnych platformy Azure z informacjami o magazynie i sieci](media/pureScaleArchitecture.png "DB2 pureScale na maszynach wirtualnych platformy Azure z informacjami o magazynie i sieci")


Na diagramie przedstawiono warstwy logiczne, które są zbędne dla klastra DB2 pureScale. Obejmują one maszyny wirtualne dla klienta programu, na potrzeby zarządzania, dla aparatu bazy danych i magazynu udostępnionego. 

Oprócz węzłów aparatu bazy danych diagram zawiera dwa węzły używane do buforowania klastra (CFs). Dla samego aparatu bazy danych są używane co najmniej dwa węzły. Serwer DB2, który należy do klastra pureScale, jest nazywany członkiem. 

Klaster jest połączony za pośrednictwem protokołu iSCSI z trzema udostępnionym klastrem magazynu w celu zapewnienia skalowalnego w poziomie magazynu i wysokiej dostępności. Program DB2 pureScale jest instalowany na maszynach wirtualnych platformy Azure z systemem Linux.

To podejście jest szablonem, który można zmodyfikować na potrzeby rozmiaru i skali organizacji. Jest on oparty na następujących kwestiach:

-   Co najmniej dwa elementy członkowskie bazy danych są połączone z co najwyżej dwoma węzłami CF. Węzły zarządzają globalną pulą buforów (GBP) dla pamięci współdzielonej i usług globalnego zarządzania blokadą (GLM) w celu kontrolowania dostępu współdzielonego i blokowania zawartości z aktywnych członków. Jeden węzeł CF działa jako podstawowy, a drugi jako pomocniczy węzeł CF trybu failover. Aby uniknąć single point of failure w środowisku, klaster programu DB2 pureScale wymaga co najmniej czterech węzłów.

-   Magazyn udostępniony o wysokiej wydajności (pokazany w P30 rozmiarze na diagramie). Każdy węzeł używa tego magazynu.

-   Sieci o wysokiej wydajności dla członków danych i magazynu udostępnionego.

### <a name="compute-considerations"></a>Zagadnienia dotyczące obliczeń

W tej architekturze są uruchamiane warstwy aplikacji, magazynu i danych w usłudze Azure Virtual Machines. [Skrypty instalacji wdrożenia](https://aka.ms/db2onazure) tworzą następujące elementy:

-   Klaster programu DB2 pureScale. Typ zasobów obliczeniowych, które są potrzebne na platformie Azure, zależy od konfiguracji. Ogólnie rzecz biorąc, można użyć dwóch metod:

    -   Używaj sieci w stylu wielowęzłowym (HPC) o wysokiej wydajności, gdzie małe i średnie wystąpienia uzyskują dostęp do magazynu udostępnionego. W przypadku tego typu konfiguracji HPC [maszyny wirtualne](../../../sizes.md) z serii L zoptymalizowane pod kątem pamięci na platformie Azure zapewniają wymaganą moc obliczeniową.

    -   Użyj mniejszej liczby wystąpień maszyn wirtualnych dla aparatów danych. W przypadku dużych wystąpień największe zoptymalizowane pod kątem pamięci maszyny wirtualne z [serii M](https://azure.microsoft.com/pricing/details/virtual-machines/series/) są idealnym rozwiązaniem w przypadku intensywnych obciążeń w pamięci. Może być konieczne użycie dedykowanego wystąpienia, w zależności od rozmiaru partycji logicznej (LPAR) używanej do uruchamiania bazy danych DB2.

-   W programie DB2 CF są używane maszyny wirtualne zoptymalizowane pod kątem pamięci, takie jak seria E lub seria L.

-   Udostępniony klaster magazynu, który używa standardowych \_ \_ maszyn wirtualnych DS4 v2 z systemem Linux.

-   Serwera przesiadkowego zarządzania jest standardową \_ \_ maszyną wirtualną DS2 v2 z systemem Linux.  Alternatywą jest usługa Azure bastionu, która zapewnia bezpieczne środowisko RDP/SSH dla wszystkich maszyn wirtualnych w sieci wirtualnej.

-   Klient jest standardową \_ \_ maszyną wirtualną DS3 v2 z systemem Windows (używany do testowania).

-   *Opcjonalne*. Serwer monitora. Jest to niezbędny tylko w przypadku niektórych wcześniejszych wersji programu DB2 pureScale. W tym przykładzie użyto standardowej \_ \_ maszyny wirtualnej DS3 v2 z systemem Linux (używanej w programie DB2 pureScale).

> [!NOTE]
> Klaster DB2 pureScale wymaga co najmniej dwóch wystąpień bazy danych DB2. Wymaga również wystąpienia pamięci podręcznej i wystąpienia Menedżera blokad.

### <a name="storage-considerations"></a>Zagadnienia dotyczące magazynu

Podobnie jak w przypadku oprogramowania Oracle, DB2 pureScale to blok o wysokiej wydajności, skalowalny w poziomie bazę danych. Zalecamy użycie największej opcji dysku [SSD platformy Azure Premium](../../../disks-types.md) , która odpowiada Twoim potrzebom. Mniejsze opcje magazynu mogą być odpowiednie dla środowisk deweloperskich i testowych, natomiast w środowiskach produkcyjnych często potrzebna jest większa pojemność magazynu. Przykładowa architektura używa [P30](https://azure.microsoft.com/pricing/details/managed-disks/) ze względu na stosunek liczby operacji we/wy do rozmiaru i ceny. Bez względu na rozmiar, użyj Premium Storage w celu uzyskania najlepszej wydajności.

W programie DB2 pureScale jest stosowana współdzielona architektura, w której wszystkie dane są dostępne we wszystkich węzłach klastra. Usługa Premium Storage musi być współdzielona przez wiele wystąpień, zarówno na żądanie, jak i na dedykowanych wystąpieniach.

Duży klaster programu DB2 pureScale może wymagać 200 terabajtów (TB) lub większej liczby udostępnionych magazynów w warstwie Premium z liczbą IOPS 100 000. Program DB2 pureScale obsługuje interfejs blokowy iSCSI, którego można używać na platformie Azure. Interfejs iSCSI wymaga udostępnionego klastra magazynów, który można zaimplementować przy użyciu funkcji S2D lub innego narzędzia. Ten typ rozwiązania tworzy urządzenie sieci SAN (sieci vSAN) na platformie Azure. Program DB2 pureScale używa sieci vSAN do zainstalowania klastrowanego systemu plików, który jest używany do udostępniania danych między maszynami wirtualnymi.

### <a name="networking-considerations"></a>Zagadnienia dotyczące pracy w sieci

Firma IBM zaleca sieci InfiniBand dla wszystkich członków w klastrze programu DB2 pureScale. Program DB2 pureScale używa również zdalnego bezpośredniego dostępu do pamięci (RDMA), jeśli jest dostępny dla CFs.

Podczas instalacji należy utworzyć [grupę zasobów](../../../../azure-resource-manager/management/overview.md) platformy Azure w celu uwzględnienia wszystkich maszyn wirtualnych. Ogólnie rzecz biorąc, należy grupować zasoby na podstawie ich okresu istnienia i zarządzania nimi. Maszyny wirtualne w tej architekturze wymagają [przyspieszonej sieci](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Jest to funkcja platformy Azure, która zapewnia spójne, niezwykle niskie opóźnienia sieci za pośrednictwem wirtualizacji we/wy z jednym elementem głównym (SR-IOV) do maszyny wirtualnej.

Każda maszyna wirtualna platformy Azure jest wdrażana w sieci wirtualnej, która ma podsieci: Main, Gluster FS front end (gfsfe), Gluster FS back end (bfsbe), DB2 pureScale (db2be) i DB2 pureScale front end (db2fe). Skrypt instalacji tworzy również podstawowe [karty sieciowe](../../../windows/multiple-nics.md) na maszynach wirtualnych w podsieci głównej.

Użyj [sieciowych grup zabezpieczeń](../../../../virtual-network/virtual-network-vnet-plan-design-arm.md) , aby ograniczyć ruch sieciowy w sieci wirtualnej i wyizolować podsieci.

Na platformie Azure usługa DB2 pureScale musi używać protokołu TCP/IP jako połączenia sieciowego dla magazynu.

## <a name="next-steps"></a>Następne kroki

-   [Wdróż tę architekturę na platformie Azure](deploy-ibm-db2-purescale-azure.md)