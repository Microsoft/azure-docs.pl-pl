---
title: Omówienie usługi Kopia zapasowa Azure Disk
description: Poznaj rozwiązanie do tworzenia kopii zapasowych na dysku platformy Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 9449fdc57909cb143d381ae074913c79d24c8893
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107299"
---
# <a name="overview-of-azure-disk-backup"></a>Omówienie usługi Kopia zapasowa Azure Disk

Kopia zapasowa Azure Disk to natywne, oparte na chmurze rozwiązanie do tworzenia kopii zapasowych, chroniące dane w dyskach zarządzanych. Jest to proste, bezpieczne i ekonomiczne rozwiązanie, które umożliwia skonfigurowanie ochrony dysków zarządzanych w kilku krokach. Gwarantuje to, że możesz odzyskać dane w scenariuszu awarii.

Usługa Kopia zapasowa Azure Disk oferuje rozwiązanie gotowe, które umożliwia zarządzanie cyklem życia migawek dla dysków zarządzanych przez Automatyzowanie okresowego tworzenia migawek i przechowywanie ich na potrzeby skonfigurowanego czasu trwania przy użyciu zasad tworzenia kopii zapasowych. Można zarządzać migawkami dysków bez ponoszenia kosztów związanych z infrastrukturą oraz niepotrzebami niestandardowych skryptów ani narzutami związanymi z zarządzaniem. Jest to rozwiązanie do tworzenia kopii zapasowej spójnej na poziomie awarii, które pobiera kopie zapasowe z dysku zarządzanego do określonego momentu przy użyciu [migawek przyrostowych](../virtual-machines/disks-incremental-snapshots.md) z obsługą wielu kopii zapasowych dziennie. Jest to również rozwiązanie bez agenta i nie ma wpływu na wydajność aplikacji produkcyjnej. Obsługuje ona tworzenie kopii zapasowych i przywracanie dysków systemu operacyjnego i danych (w tym dysków udostępnionych), niezależnie od tego, czy są one obecnie dołączone do uruchomionej maszyny wirtualnej platformy Azure.

Jeśli jest wymagana spójna z aplikacjami kopia zapasowa maszyny wirtualnej, w tym dyski danych, lub opcja przywrócenia całej maszyny wirtualnej z kopii zapasowej, przywrócenia pliku lub folderu lub przywrócenia do regionu pomocniczego, użyj rozwiązania do [tworzenia kopii zapasowych maszyny wirtualnej platformy Azure](backup-azure-vms-introduction.md) . Azure Backup oferuje równoczesną obsługę kopii zapasowych dysków zarządzanych przy użyciu kopii zapasowej dysku oprócz rozwiązań do [tworzenia kopii zapasowych maszyn wirtualnych platformy Azure](./backup-azure-vms-introduction.md) . Jest to przydatne, gdy wymagane jest jednodniowe wykonywanie kopii zapasowych maszyn wirtualnych, a także częste kopie zapasowe dysków systemu operacyjnego lub konkretnego dysku z danymi, które są spójne, i nie mają wpływu na wydajność aplikacji produkcyjnej.

Usługa Kopia zapasowa Azure Disk jest zintegrowana z centrum kopii zapasowych, co zapewnia **jednolite środowisko zarządzania** na platformie Azure dla przedsiębiorstw, które ułatwiają monitorowanie, obsługę i analizowanie kopii zapasowych na dużą skalę.

## <a name="key-benefits-of-disk-backup"></a>Najważniejsze zalety tworzenia kopii zapasowych na dysku

Kopia zapasowa dysku platformy Azure to rozwiązanie bezagentowe i spójne, które korzysta z [migawek przyrostowych](../virtual-machines/disks-incremental-snapshots.md) i oferuje następujące korzyści:

- Częstsze i szybkie tworzenie kopii zapasowych bez przerywania pracy maszyny wirtualnej.
- Nie ma wpływu na wydajność aplikacji produkcyjnej.
- Brak problemów z zabezpieczeniami, ponieważ nie wymaga uruchamiania skryptów niestandardowych ani instalowania agentów.
- Ekonomiczne rozwiązanie do tworzenia kopii zapasowych określonych dysków w porównaniu do tworzenia kopii zapasowych całej maszyny wirtualnej.

Rozwiązanie do tworzenia kopii zapasowych na dysku Azure jest przydatne w następujących scenariuszach:

- Potrzeba częstego wykonywania kopii zapasowych dziennie bez quiescent aplikacji
- Aplikacje działające w scenariuszu klastra: klaster trybu failover systemu Windows Server i klastry Linux są zapisywane na dyskach udostępnionych
- Konkretna potrzeba dla tworzenia kopii zapasowych bez wykorzystania agentów z powodu problemów z bezpieczeństwem lub wydajnością aplikacji
- Tworzenie kopii zapasowej maszyny wirtualnej spójnej na poziomie aplikacji nie jest możliwe, ponieważ aplikacje biznesowe nie obsługują Usługa kopiowania woluminów w tle (VSS).

Rozważ użycie kopii zapasowej Azure Disk w scenariuszach, w których:

- aplikacja o znaczeniu krytycznym jest uruchomiona na maszynie wirtualnej platformy Azure, która wymaga wielu kopii zapasowych dziennie w celu spełnienia celu punktu odzyskiwania, ale bez wpływu na środowisko produkcyjne lub wydajność aplikacji
- organizacja lub regulacja branżowa ogranicza instalowanie agentów ze względu na kwestie związane z bezpieczeństwem
- wykonywanie niestandardowych skryptów pre lub post oraz wywoływanie zawieszania i odblokowywania na maszynach wirtualnych z systemem Linux w celu uzyskania kopii zapasowej spójnej na poziomie aplikacji powoduje nadmierne obciążenie związane z dostępnością obciążenia
- aplikacje kontenerowe uruchomione w usłudze Azure Kubernetes Service (klaster AKS) używają dysków zarządzanych jako magazynu trwałego. Dzisiaj trzeba utworzyć kopię zapasową dysku zarządzanego za pośrednictwem skryptów automatyzacji, które są trudne do zarządzania.
- dysk zarządzany zawiera krytyczne dane biznesowe, używane jako udział plików lub pliki kopii zapasowej bazy danych, a także chcesz zoptymalizować koszt kopii zapasowej bez inwestowania w kopie zapasowe maszyny wirtualnej platformy Azure
- Istnieje wiele maszyn wirtualnych z systemem Linux lub Windows (czyli maszyną wirtualną z tylko dyskiem systemu operacyjnego i bez dołączonych dysków danych), które obsługują komputery WebSerwer lub maszyny o niemniejszej kondycji, a także są środowiskiem przejściowym z ustawieniami konfiguracji aplikacji, a do ochrony dysku systemu operacyjnego potrzebne jest ekonomiczne rozwiązanie do tworzenia kopii zapasowych. Na przykład, aby wyzwolić szybką kopię zapasową na żądanie przed uaktualnieniem lub poprawką maszyny wirtualnej
- na maszynie wirtualnej jest uruchomiona konfiguracja systemu operacyjnego, która nie jest obsługiwana przez rozwiązanie do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure (na przykład Windows 2008 32-bit Server)

## <a name="how-the-backup-and-restore-process-works"></a>Jak działa proces tworzenia kopii zapasowej i przywracania

- Pierwszym krokiem w konfigurowaniu kopii zapasowych dla usługi Azure Managed disks jest utworzenie [magazynu kopii zapasowych](backup-vault-overview.md). Magazyn umożliwia skonsolidowany widok kopii zapasowych skonfigurowanych w różnych obciążeniach.

- Następnie utwórz zasady tworzenia kopii zapasowych, które pozwalają skonfigurować częstotliwość wykonywania kopii zapasowych i czas przechowywania.

- Aby skonfigurować kopię zapasową, przejdź do magazynu kopii zapasowych, przypisz zasady tworzenia kopii zapasowych, wybierz dysk zarządzany, dla którego należy utworzyć kopię zapasową, i podaj grupę zasobów, w której mają być przechowywane i zarządzane migawki. Azure Backup automatycznie wyzwala zaplanowane zadania tworzenia kopii zapasowej, które tworzą przyrostową migawkę dysku zgodnie z częstotliwością tworzenia kopii zapasowych. Starsze migawki są usuwane zgodnie z czasem trwania przechowywania określonym przez zasady tworzenia kopii zapasowych.

- Azure Backup używa [przyrostowych migawek](../virtual-machines/disks-incremental-snapshots.md#restrictions) dysku zarządzanego. Migawki przyrostowe to ekonomiczne, w danym momencie kopie zapasowe dysków zarządzanych, które są rozliczane w przypadku zmian różnicowych dysku od momentu ostatniej migawki. Są one zawsze przechowywane w najbardziej oszczędnym magazynie, w standardowym magazynie DYSKowym, niezależnie od typu magazynu dysków nadrzędnych. Pierwsza migawka dysku zajmie używany rozmiar dysku, a kolejne migawki przyrostowe przechowują zmiany różnicowe na dysku od momentu ostatniej migawki.

- Po skonfigurowaniu kopii zapasowej dysku zarządzanego zostanie utworzone wystąpienie kopii zapasowej w magazynie kopii zapasowych. Za pomocą wystąpienia kopii zapasowej można znaleźć kondycję operacji tworzenia kopii zapasowej, wyzwolić kopie zapasowe na żądanie i wykonać operacje przywracania. Możesz również wyświetlić kondycję kopii zapasowych w wielu magazynach i wystąpieniach kopii zapasowych za pomocą centrum kopii zapasowych, które udostępnia pojedyncze okienko widoku Glass.

- Aby przywrócić program, wystarczy wybrać punkt odzyskiwania, z którego ma zostać przywrócony dysk. Podaj grupę zasobów, w której ma zostać utworzony przywrócony dysk z migawki. Azure Backup udostępnia natychmiastowe środowisko przywracania, ponieważ migawki są przechowywane lokalnie w subskrypcji.

- Magazyn kopii zapasowych używa tożsamości zarządzanej do uzyskiwania dostępu do innych zasobów platformy Azure. Aby skonfigurować kopię zapasową dysku zarządzanego i przywrócić poprzednią kopię zapasową, zarządzana tożsamość magazynu kopii zapasowych wymaga zestawu uprawnień na dysku źródłowym, grupy zasobów migawek, w której tworzone są migawki, oraz docelowej grupy zasobów, w której ma zostać przywrócona kopia zapasowa. Uprawnienia do tożsamości zarządzanej można udzielić przy użyciu kontroli dostępu opartej na rolach (Azure RBAC). Tożsamość zarządzana to nazwa główna usługi typu specjalnego, która może być używana tylko z zasobami platformy Azure. Dowiedz się więcej o [tożsamościach zarządzanych](../active-directory/managed-identities-azure-resources/overview.md).

- Obecnie kopia zapasowa Azure Disk obsługuje operacyjną kopię zapasową dysków zarządzanych, a kopie zapasowe nie są kopiowane do magazynu magazynu kopii zapasowych. Zapoznaj się z [matrycą pomocy technicznej](disk-backup-support-matrix.md), aby uzyskać szczegółową listę obsługiwanych i nieobsługiwanych scenariuszy oraz dostępność regionów.

## <a name="pricing"></a>Ceny

Azure Backup oferuje rozwiązanie do zarządzania cyklem życia migawek do ochrony dysków platformy Azure. Migawki dysków utworzone przez Azure Backup są przechowywane w grupie zasobów w ramach subskrypcji platformy Azure i wiążą się z naliczeniem opłat za **Magazyn migawek** . Aby uzyskać więcej informacji na temat cen migawek, możesz odwiedzić stronę [cennika dysku zarządzanego](https://azure.microsoft.com/pricing/details/managed-disks/) . Ponieważ migawki nie są kopiowane do magazynu kopii zapasowych, Azure Backup nie nalicza opłaty za **chronione wystąpienie** , a koszt **magazynu kopii zapasowych** nie ma zastosowania. Ponadto migawki przyrostowe zajmują zmiany różnicowe od ostatniej migawki i są zawsze przechowywane w magazynie w warstwie Standardowa, niezależnie od typu magazynu dysków zarządzanych przez magazyn i są obciążane opłatami zgodnie z cennikiem magazynu w warstwie Standardowa. Dzięki temu tworzenie kopii zapasowej na dysku platformy Azure jest ekonomiczne.

## <a name="next-steps"></a>Następne kroki

- [Macierz obsługi kopii zapasowych dysków na platformie Azure](disk-backup-support-matrix.md)
