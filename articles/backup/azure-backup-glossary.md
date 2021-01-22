---
title: Azure Backup słownik
description: W tym artykule opisano warunki pomocne podczas korzystania z Azure Backup.
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: 121258665ab275fdcffd618e7c0cf1b3e0537e70
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661480"
---
# <a name="azure-backup-glossary"></a>Azure Backup słownik

Ten słownik terminów może być przydatny podczas korzystania z Azure Backup.

>[!NOTE]
>
> - Warunki oznaczone prefiksem "(termin specyficzny dla obciążenia)" odnoszą się do tych warunków, które mają zastosowanie tylko w kontekście określonego podzestawu obciążeń obsługiwanych przez Azure Backup.
> - Warunki, które są często używane w dokumentacji Azure Backup, ale odnoszą się do innych usług platformy Azure, można uzyskać bezpośredni link do dokumentacji odpowiedniej usługi platformy Azure.

## <a name="afs-azure-file-shares"></a>AFS (udziały plików platformy Azure)

Zapoznaj się z [dokumentacją Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

## <a name="alternate-location-recovery"></a>Odzyskiwanie lokalizacji alternatywnej

Odzyskiwanie wykonane z punktu odzyskiwania do lokalizacji innej niż oryginalna lokalizacja, w której zostały wykonane kopie zapasowe. W przypadku korzystania z kopii zapasowej maszyny wirtualnej platformy Azure oznacza to przywrócenie maszyny wirtualnej na serwerze innym niż oryginalny serwer, na którym wykonano kopie zapasowe. W przypadku korzystania z kopii zapasowej udziału plików platformy Azure oznacza to przywrócenie danych do udziału plików innego niż udział plików z kopią zapasową.

## <a name="application-consistent-backup"></a>Kopia zapasowa spójna na poziomie aplikacji

(Termin specyficzny dla obciążenia)

Kopie zapasowe spójne z aplikacjami przechwytują zawartość pamięci i oczekujące operacje we/wy. Migawki spójne z aplikacjami używają składnika [zapisywania usługi VSS VSS (](#vss-windows-volume-shadow-copy-service) lub skryptów pre lub post dla systemu Linux), aby zapewnić spójność danych aplikacji przed wystąpieniem kopii zapasowej. [Dowiedz się więcej](backup-azure-vms-introduction.md).

## <a name="azure-resource-manager-arm-templates"></a>Szablony usługi Azure Resource Manager (ARM)

Zapoznaj się z [dokumentacją dotyczącą szablonów ARM](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview).

## <a name="autoprotection-for-databases"></a>Ochrona autoprotection (dla baz danych)

(Termin specyficzny dla obciążenia)

Automatyczna ochrona to funkcja, która umożliwia automatyczną ochronę wszystkich baz danych w autonomicznym wystąpieniu SQL Server lub SQL Server zawsze włączone grupy dostępności. Program nie tylko umożliwia tworzenie kopii zapasowych dla istniejących baz danych, ale również ochronę wszystkich baz danych, które mogą zostać dodane w przyszłości.

## <a name="availability-storage-replication-types"></a>Dostępność (typy replikacji magazynu)

Azure Backup oferuje trzy typy replikacji, aby zapewnić wysoką dostępność magazynu i danych:

### <a name="lrs"></a>LRS

[Magazyn lokalnie nadmiarowy (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage) replikuje dane kopii zapasowej trzy razy (tworzy trzy kopie danych kopii zapasowej) w jednostce skalowania magazynu w centrum danych. Wszystkie kopie danych kopii zapasowej istnieją w tym samym regionie. LRS to niska cena opcji chroniącej dane kopii zapasowej przed awariami sprzętu lokalnego.

### <a name="grs"></a>GRS

[Magazyn geograficznie nadmiarowy (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) jest ustawieniem domyślnym i zalecaną opcją replikacji. GRS replikuje dane kopii zapasowej do regionu pomocniczego, setki kilometrów od lokalizacji głównej danych źródłowych. GRS koszty więcej niż LRS, ale GRS zapewnia wyższy poziom trwałości danych kopii zapasowej, nawet jeśli wystąpi awaria regionalna.

>[!NOTE]
>W przypadku magazynów GRS z włączoną funkcją przywracania między regionami magazyn kopii zapasowych jest uaktualniany z GRS do RA-GRS (magazyn Geo-Redundant dostępu do odczytu).

### <a name="zrs"></a>ZRS

[Magazyn strefowo nadmiarowy (ZRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#zone-redundant-storage) replikuje dane kopii zapasowej w [strefach dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview#availability-zones), gwarantując miejsce zamieszkania i odporność danych kopii zapasowych w tym samym regionie. Należy utworzyć kopię zapasową krytycznych obciążeń, które wymagają danych znajdujących się w [miejscu](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) na platformie ZRS.

## <a name="azure-command-line-interface-cli"></a>Interfejs wiersza polecenia platformy Azure

Zapoznaj się z [dokumentacją interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/what-is-azure-cli).

## <a name="azure-policy"></a>Azure Policy

Zapoznaj się z [dokumentacją Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview).

## <a name="azure-powershell"></a>Azure PowerShell

Zapoznaj się z [dokumentacją Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

Zapoznaj się z [dokumentacją Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).

## <a name="azure-disk-encryption-ade"></a>Usługa Azure Disk Encryption (ADE)

Zapoznaj się z [dokumentacją Azure Disk Encryption](https://docs.microsoft.com/azure/security/fundamentals/azure-disk-encryption-vms-vmss).

## <a name="backend-storage--cloud-storage--backup-storage"></a>Magazyn zaplecza/magazyn w chmurze/magazyn kopii zapasowych

Rzeczywisty magazyn używany przez wystąpienie kopii zapasowej. Obejmuje rozmiar wszystkich punktów przechowywania istniejących dla wystąpienia kopii zapasowej (zgodnie z definicją w zasadach tworzenia kopii zapasowej i przechowywania).

## <a name="bare-metal-backup"></a>Kopia zapasowa systemu operacyjnego

Kopia zapasowa plików systemu operacyjnego i wszystkich danych na woluminach krytycznych, z wyjątkiem danych użytkownika. Zgodnie z definicją kopia zapasowa systemu operacyjnego obejmuje kopię zapasową stanu systemowego. Zapewnia ochronę, gdy komputer nie zostanie uruchomiony i konieczne będzie odzyskanie wszystkiego. [Dowiedz się więcej](backup-mabs-system-state-and-bmr.md).

## <a name="backup-extensions--vm-extensions"></a>Rozszerzenia kopii zapasowej/rozszerzenia maszyn wirtualnych

(Specyficzne dla typu obciążenia maszyny wirtualnej platformy Azure)

Rozszerzenia maszyny wirtualnej platformy Azure to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji na maszynach wirtualnych platformy Azure. Azure Backup tworzenia kopii zapasowych maszyn wirtualnych platformy Azure przez zainstalowanie rozszerzenia agenta maszyny wirtualnej platformy Azure uruchomionego na komputerze. Azure Backup automatycznie zarządza tymi rozszerzeniami, a użytkownicy nie muszą ręcznie aktualizować tych rozszerzeń.

## <a name="backup-instance--backup-item"></a>Wystąpienie kopii zapasowej/element kopii zapasowej

Źródło danych kopii zapasowej w magazynie z określonymi zasadami tworzenia kopii zapasowych i przechowywania stanowi wystąpienie kopii zapasowej lub element kopii zapasowej.

## <a name="backup-rule--backup-policy"></a>Reguła tworzenia kopii zapasowej/zasady tworzenia kopii zapasowych

Reguła tworzenia kopii zapasowych to zdefiniowana przez użytkownika Reguła określająca, kiedy i jak często mają być wykonywane kopie zapasowe w źródle danych. W przypadku niektórych typów obciążeń zasady tworzenia kopii zapasowych umożliwiają również określenie metody migawki, która ma zostać zastosowana do źródła danych (pełna, przyrostowa, różnicowa). Zasady tworzenia kopii zapasowych są często tworzone jako kombinacja reguł i zasad przechowywania kopii zapasowych.

## <a name="backup-vault"></a>Magazyn kopii zapasowych

Zasób Azure Resource Manager typu *Microsoft. dataprotection/BackupVaults*. Obecnie magazyny kopii zapasowych są używane do tworzenia kopii zapasowych baz danych platformy Azure dla serwera PostgreSQL. [Dowiedz się więcej na temat magazynów kopii zapasowych](backup-azure-recovery-services-vault-overview.md).

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (ciągłość działania i odzyskiwanie po awarii)

BCDR obejmuje zestaw procesów, które organizacja musi zastosować, aby zapewnić, że aplikacje i obciążenia są uruchomione i działają w trakcie planowanej i nieplanowanej usługi lub awarii platformy Azure, z minimalnym zakłóceniem działania firmy. [Dowiedz się więcej](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview) na temat różnych usług oferowanych przez platformę Azure, które mogą pomóc w tworzeniu strategii BCDR.

## <a name="churn"></a>Mlek

Procent zmian danych, których kopia zapasowa ma zostać wykonana między dwoma kolejnymi kopiami zapasowymi. Może to być spowodowane dodaniem nowych danych lub modyfikacją lub usunięciem istniejących danych.

## <a name="crash-consistent-backup"></a>Kopia zapasowa spójna na poziomie awarii

(Termin specyficzny dla obciążenia)

Migawki spójne z awarią są zwykle wykonywane, gdy maszyna wirtualna platformy Azure jest zamykana w momencie tworzenia kopii zapasowej. Przechwytywane i tworzone są kopie zapasowe tylko danych istniejących na dysku w momencie tworzenia kopii zapasowej. [Dowiedz się więcej](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="cross-region-restore-crr"></a>Przywracanie między regionami (CRR)

Jako jedna z [opcji przywracania](backup-azure-arm-restore-vms.md#restore-options), przywracanie między regionami (CRR) umożliwia przywracanie elementów kopii zapasowych w regionie pomocniczym, który jest [sparowanym regionem platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).

## <a name="data-box"></a>Pole danych

Zapoznaj się z [dokumentacją dotyczącą pól danych](https://docs.microsoft.com/azure/databox/data-box-overview).

## <a name="datasource"></a>Źródło danych

Zasób (zasób platformy Azure, serwer proxy lub zasób lokalny), który jest kandydatem do utworzenia kopii zapasowej. Na przykład maszyna wirtualna platformy Azure lub udział plików platformy Azure.

## <a name="dpm-data-protection-manager"></a>Program DPM (Data Protection Manager)

(Termin specyficzny dla obciążenia)

Zapoznaj się z [dokumentacją programu DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview).

## <a name="expressroute"></a>ExpressRoute

Zapoznaj się z [dokumentacją ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

## <a name="file-system-consistent-backup"></a>Spójna kopia zapasowa systemu plików

(Termin specyficzny dla obciążenia)

Spójne kopie zapasowe systemu plików zapewniają spójność, pobierając migawkę wszystkich plików w tym samym czasie. [Dowiedz się więcej](backup-azure-vms-introduction.md#snapshot-consistency).

## <a name="frontend-storage--source-size"></a>Rozmiar magazynu frontonu/źródła

Rozmiar danych, których kopia zapasowa ma zostać utworzona dla źródła danych. Rozmiar frontonu źródła danych określa swoją liczbę [chronionych wystąpień](#protected-instance) .

## <a name="full-backup"></a>Pełna kopia zapasowa

W przypadku pełnych kopii zapasowych kopia całego źródła danych jest przechowywana dla każdej kopii zapasowej.

## <a name="gfs-backup-policy"></a>Zasady tworzenia kopii zapasowych GFS

Zasady tworzenia kopii zapasowych GFS (Dziadk-ojciec-syn) to takie, które umożliwiają definiowanie tygodniowych, miesięcznych i rocznych harmonogramów tworzenia kopii zapasowych oprócz dziennego harmonogramu tworzenia kopii zapasowych. Cotygodniowe kopie zapasowe są znane "Sons", co oznacza, że miesięczne kopie zapasowe są znane jako "ojciec", a roczne kopie zapasowe są znane jako "dziadka". Każdy z tych zestawów kopii zapasowych można skonfigurować tak, aby był przechowywany w różnych okresach czasu, co pozwala na dodatkowe dostosowanie opcji przechowywania kopii zapasowych. Zasady GFS są przydatne w przypadku przechowywania kopii zapasowych przez długi czas w większym stopniu.

## <a name="iaas-vms--azure-vms"></a>Maszyny wirtualne IaaS/maszyny wirtualne platformy Azure

Zapoznaj się z [dokumentacją maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machines/).

## <a name="incremental-backup"></a>Przyrostowa kopia zapasowa

Przyrostowe kopie zapasowe przechowują tylko te bloki, które uległy zmianie od czasu utworzenia poprzedniej kopii zapasowej.

## <a name="instant-restore"></a>Natychmiastowe przywracanie

(Termin określony dla obciążenia) Natychmiastowe przywracanie polega na przywróceniu maszyny bezpośrednio z migawki kopii zapasowej, a nie z kopii migawki w magazynie. Natychmiastowe przywracanie jest szybsze niż przywrócenie z magazynu. Liczba dostępnych natychmiastowych punktów przywracania zależy od czasu przechowywania skonfigurowanego dla migawek. Obecnie dotyczy tylko kopii zapasowych maszyny wirtualnej platformy Azure.

## <a name="iops"></a>Liczba operacji we/wy na sekundę

Operacje wejścia/wyjścia na sekundę.

## <a name="item-level-restore"></a>Przywracanie na poziomie elementu

(Termin specyficzny dla obciążenia)

Przywracanie pojedynczych plików lub folderów z punktu odzyskiwania.

## <a name="job"></a>Zadanie

Zadanie związane z kopią zapasową, które jest tworzone przez użytkownika lub usługę Azure Backup. Zadania mogą być zaplanowane lub na żądanie (ad hoc). Istnieją różne typy zadań — kopia zapasowa, przywracanie, konfiguracja ochrony i tak dalej. [Dowiedz się więcej o zadaniach](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

## <a name="mabs--azure-backup-server"></a>SERWERA usługi MAB/Azure Backup Server

(Termin specyficzny dla obciążenia)

Za pomocą Azure Backup Server można chronić obciążenia aplikacji, takie jak maszyny wirtualne funkcji Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange i klienci z systemem Windows, z poziomu pojedynczej konsoli. Dziedziczy ona większość funkcji tworzenia kopii zapasowej obciążenia z programu DPM, ale z kilkoma różnicami. [Dowiedz się więcej](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>Dyski zarządzane

Zapoznaj się z [dokumentacją dotyczącą dysków zarządzanych](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview).

## <a name="mars-agent"></a>Agent MARS

(Termin specyficzny dla obciążenia)

Agent MARS jest również znany jako agent **Azure Backup agentem** **Recovery Services**, który jest używany przez Azure Backup do tworzenia kopii zapasowych danych z maszyn lokalnych i maszyn wirtualnych platformy Azure do magazynu Recovery Services kopii zapasowych na platformie Azure. [Dowiedz się więcej](backup-support-matrix-mars-agent.md).

## <a name="nsg-network-security-group"></a>SIECIOWEJ grupy zabezpieczeń (sieciowa Grupa zabezpieczeń)

Zapoznaj się z [dokumentacją sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview).

## <a name="offline-seeding"></a>Rozmieszczanie w trybie offline

Rozmieszczanie w trybie offline odnosi się do procesu transferu początkowej (pełnej) kopii zapasowej w trybie offline bez użycia przepustowości sieci. Zapewnia mechanizm kopiowania danych kopii zapasowej na fizyczne urządzenia magazynujące, które następnie są wysyłane do pobliskiego centrum danych platformy Azure i przekazywane do magazynu Recovery Servicesowego. [Dowiedz się więcej](offline-backup-overview.md).

## <a name="on-demand-backup--ad-hoc-backup"></a>Tworzenie kopii zapasowej na żądanie/tworzenie kopii zapasowych ad hoc

Zadanie tworzenia kopii zapasowej, które jest wyzwalane przez użytkownika w oparciu o jednorazową potrzebę, a nie na podstawie harmonogramu tworzenia kopii zapasowych (zasad), który został skonfigurowany dla zasobu.

## <a name="original-location-recovery-olr"></a>Odzyskiwanie do oryginalnej lokalizacji (OLR)

Odzyskiwanie wykonane z punktu przywracania do lokalizacji źródłowej, z której wykonano kopie zapasowe, zastępując je stanem przechowywanym w punkcie odzyskiwania. W przypadku korzystania z kopii zapasowej maszyny wirtualnej platformy Azure oznacza to przywrócenie maszyny wirtualnej na oryginalnym serwerze, na którym wykonano kopie zapasowe. W przypadku korzystania z kopii zapasowej udziału plików platformy Azure oznacza to przywrócenie danych do udziału plików kopii zapasowej.

## <a name="passphrase"></a>Danym

(Termin specyficzny dla obciążenia)

Hasło jest używane do szyfrowania i odszyfrowywania danych podczas tworzenia kopii zapasowej lub przywracania lokalnego lub maszyny lokalnej przy użyciu agenta MARS na platformie lub z platformy Azure.

## <a name="private-endpoint"></a>Prywatny punkt końcowy

Zapoznaj się z [dokumentacją prywatnego punktu końcowego](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).

## <a name="protected-instance"></a>Chronione wystąpienie

Chronione wystąpienie odwołuje się do komputera, fizycznego lub wirtualnego serwera używanego do konfigurowania kopii zapasowych na platformie Azure.  Z **punktu widzenia rozliczenia** liczba chronionych wystąpień dla komputera jest funkcją rozmiaru frontonu. W związku z tym pojedyncze wystąpienie kopii zapasowej (na przykład maszyna wirtualna utworzona na platformie Azure) może odpowiadać wielu chronionym wystąpieniu, w zależności od jego rozmiaru frontonu. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/backup/).

## <a name="rbac-role-based-access-control"></a>RBAC (kontrola dostępu oparta na rolach)

Zapoznaj się z [dokumentacją RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>Punkt odzyskiwania/punkt przywracania/punkt przechowywania/punkt w czasie (PIT)

Kopia pierwotnych danych, których kopia zapasowa jest tworzona. Punkt przechowywania jest skojarzony z sygnaturą czasową, aby można było użyć tej funkcji do przywrócenia elementu do określonego punktu w czasie.

## <a name="recovery-services-vault"></a>Magazyn usługi Recovery Services

Zasób Azure Resource Manager typu *Microsoft. RecoveryServices/magazyny*. Obecnie magazyny Recovery Services są używane do tworzenia kopii zapasowych następujących obciążeń: maszyny wirtualne platformy Azure, SQL na maszynach wirtualnych platformy Azure, SAP HANA na maszynach wirtualnych platformy Azure i udziały plików platformy Azure. Jest on również używany do tworzenia kopii zapasowych obciążeń lokalnych przy użyciu usług MARS, Azure Backup Server (serwera usługi MAB) i System Center DPM. [Dowiedz się więcej o magazynach Recovery Services](backup-azure-recovery-services-vault-overview.md).

## <a name="resource-group"></a>Grupa zasobów

Zapoznaj się z [dokumentacją Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#what-is-a-resource-group).

## <a name="rest-api"></a>Interfejs API REST

Zapoznaj się z [dokumentacją interfejsu API REST platformy Azure](https://docs.microsoft.com/rest/api/azure/).

## <a name="retention-rule"></a>Reguła przechowywania

Zdefiniowana przez użytkownika Reguła określająca, jak długo mają być przechowywane kopie zapasowe.

## <a name="rpo-recovery-point-objective"></a>CEL punktu odzyskiwania

Cel punktu odzyskiwania wskazuje maksymalną utratę danych, która jest możliwa w scenariuszu utraty danych. Jest to określane przez częstotliwość tworzenia kopii zapasowych.

## <a name="rto-recovery-time-objective"></a>RTO (cel czasu odzyskiwania)

RTO wskazuje maksymalny możliwy czas, w którym dane mogą zostać przywrócone do ostatniego dostępnego punktu w czasie po utracie danych.

## <a name="scheduled-backup"></a>Zaplanowana kopia zapasowa

Zadanie tworzenia kopii zapasowej, które jest automatycznie wyzwalane przez zasady tworzenia kopii zapasowej skonfigurowane dla danego elementu.

## <a name="secondary-region--paired-region"></a>Region pomocniczy/sparowany

Para regionalna składa się z dwóch regionów w tej samej lokalizacji geograficznej. Jeden jest regionem podstawowym, a drugi to region pomocniczy. Sparowane regiony są używane przez niektóre usługi platformy Azure (w tym Azure Backup z ustawieniami GRS) w celu zapewnienia ciągłości działania i ochrony przed utratą danych. [Dowiedz się więcej](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="soft-delete"></a>Usuwanie nietrwałe

Usuwanie nietrwałe jest funkcją, która pomaga chronić przed przypadkowym usunięciem danych kopii zapasowej. W przypadku usuwania nietrwałego, nawet jeśli złośliwy aktor usuwa kopię zapasową (lub przypadkowo usunięto dane kopii zapasowej), dane kopii zapasowej są przechowywane przez dłuższy czas, co pozwala na odzyskanie tego elementu kopii zapasowej bez utraty danych. [Dowiedz się więcej](backup-azure-security-feature-cloud.md).

## <a name="snapshot"></a>Snapshot

Migawka to pełna kopia tylko do odczytu wirtualnego dysku twardego (VHD) lub udziału plików platformy Azure. Dowiedz się więcej na temat [migawek dysków](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk) i [migawek plików](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files).

## <a name="storage-account"></a>Konto magazynu

Zapoznaj się z [dokumentacją konta magazynu](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

## <a name="subscription"></a>Subskrypcja

Subskrypcja platformy Azure to kontener logiczny, który służy do aprowizowania zasobów na platformie Azure. Zawiera szczegóły dotyczące wszystkich zasobów, takich jak maszyny wirtualne, bazy danych i inne.

## <a name="system-state-backup"></a>Kopia zapasowa stanu systemu

(Termin specyficzny dla obciążenia)

Tworzy kopię zapasową plików systemu operacyjnego. Ta kopia zapasowa umożliwia odzyskanie, gdy komputer zostanie uruchomiony, ale pliki systemowe i rejestr zostaną utracone. [Dowiedz się więcej](backup-mabs-system-state-and-bmr.md).

## <a name="tenant"></a>Dzierżawa

Dzierżawa reprezentuje organizację. Jest to dedykowane wystąpienie usługi Azure AD, którą organizacja lub deweloper aplikacji otrzymuje po utworzeniu relacji z firmą Microsoft, na przykład zarejestrowaniu się na platformie Azure, w usłudze Microsoft Intune lub Microsoft 365.

## <a name="unmanaged-disk"></a>Dysk niezarządzany

Zapoznaj się z [dokumentacją dotyczącą dysków niezarządzanych](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance#azure-unmanaged-disks).

## <a name="vault"></a>Magazyn

Jednostka magazynu na platformie Azure, która przechowuje dane kopii zapasowej. Jest to również jednostka RBAC i rozliczenia. Obecnie istnieją dwa typy magazynów — magazyn Recovery Services i magazyn kopii zapasowych.

## <a name="vault-credentials"></a>Poświadczenia magazynu

Plik poświadczeń magazynu to certyfikat wygenerowany przez portal dla każdego magazynu. Ta wartość jest używana podczas rejestrowania serwera lokalnego w magazynie. [Dowiedz się więcej](backup-azure-dpm-introduction.md).

## <a name="vnet-virtual-network"></a>Sieć wirtualna (Virtual Network)

Zapoznaj się z [dokumentacją sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows Usługa kopiowania woluminów w tle)

Zapoznaj się z [dokumentacją usługi VSS](https://docs.microsoft.com/windows-server/storage/file-server/volume-shadow-copy-service).

## <a name="next-steps"></a>Następne kroki

- [Przegląd Azure Backup](backup-overview.md)
- [Azure Backup architektura i składniki](backup-architecture.md)
