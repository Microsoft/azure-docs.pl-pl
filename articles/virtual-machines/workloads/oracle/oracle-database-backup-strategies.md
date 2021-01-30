---
title: Oracle Database w strategiach usługi Azure Virtual Machines Backup
description: Opcje tworzenia kopii zapasowych baz danych Oracle w środowisku maszyn wirtualnych z systemem Linux systemu Azure.
author: cro27
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: adb455e7a49ead1e8ba8e9845a4e56542528d0b1
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99064001"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Oracle Database w strategiach tworzenia kopii zapasowych maszyn wirtualnych z systemem Linux

Kopie zapasowe bazy danych chronią bazę danych przed utratą danych ze względu na awarię składnika magazynu i awarię centrum danych. Mogą to być również sposoby odzyskiwania po błędzie ludzkim i sposób klonowania bazy danych na potrzeby tworzenia i testowania. 

Na platformie Azure, ponieważ cały magazyn jest wysoce nadmiarowy, a utrata jednego lub większej liczby dysków nie prowadzi do awarii bazy danych, kopie zapasowe są najczęściej używane do ochrony przed błędami ludzkimi w celu klonowania operacji lub zachowywania danych w celach prawnych. Są one również środkami ochrony przed awariami regionalnymi, gdy technologia odzyskiwania po awarii, taka jak DataGuard, nie jest używana. W takim przypadku kopie zapasowe muszą być przechowywane w różnych regionach platformy Azure przy użyciu replikacji geograficznie nadmiarowej, tak aby były dostępne poza podstawowym regionem bazy danych.

## <a name="azure-storage"></a>Azure Storage 

[Usługi Azure Storage](../../../storage/common/storage-introduction.md) to rozwiązanie magazynu w chmurze firmy Microsoft dla nowoczesnych scenariuszy magazynowania danych. Usługa Azure Storage oferuje wiele usług, których można użyć do zainstalowania magazynu zewnętrznego na maszynie wirtualnej z systemem Linux systemu Azure, która jest odpowiednia jako nośnik kopii zapasowych baz danych Oracle. Do zainicjowania operacji tworzenia kopii zapasowej lub przywracania i kopiowania kopii zapasowej do/z usługi Azure Storage jest wymagane narzędzie do tworzenia kopii zapasowej, takie jak Oracle RMAN.
 
Usługi Azure Storage oferują następujące korzyści:

-  Niezawodność i wysoka dostępność. Nadmiarowość zapewnia bezpieczeństwo danych w przypadku przejściowych awarii sprzętu. Wszystkie magazyny są domyślnie trzykrotnie dublowane. Możesz również wybrać replikację danych między centrami danych lub regionami geograficznymi, aby uzyskać dodatkową ochronę z katastrofami lokalnego lub klęski żywiołowej. Dane replikowane w ten sposób pozostają wysoce dostępne w przypadku awarii.

-  Zapewnienia. Wszystkie dane zapisywane na koncie usługi Azure Storage są szyfrowane przez usługę. Usługa Azure Storage zapewnia precyzyjną kontrolę nad tym, kto ma dostęp do danych.

-  Strategia. Usługa Azure Storage została zaprojektowana jako wysoce skalowalne rozwiązanie spełniające potrzeby związane z magazynowaniem danych i wydajnością współczesnych aplikacji.

-  Zarządzanych. Platforma Azure obsługuje konserwację sprzętu, aktualizacje i problemy krytyczne.

-  Dostępność. Dane w usłudze Azure Storage są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Firma Microsoft udostępnia biblioteki klienckie usługi Azure Storage w różnych językach, w tym .NET, Java, Node.js, Python, PHP, Ruby, go i innych, a także interfejs API REST. Usługa Azure Storage obsługuje skrypty programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Witryna Azure Portal oraz Eksplorator usługi Azure Storage oferują proste rozwiązania wizualne do pracy z danymi.

Platforma Azure Storage obejmuje następujące usługi danych, które są odpowiednie do użycia jako nośnik kopii zapasowych bazy danych Oracle:

-  Azure Blobs: wysoce skalowalny magazyn obiektów dla danych tekstowych i binarnych. Obejmuje również obsługę analizy danych Big Data za pomocą Data Lake Storage Gen2.

-  Azure Files: zarządzane udziały plików dla wdrożeń w chmurze lub lokalnych.

-  Azure disks: woluminy magazynu na poziomie bloku dla maszyn wirtualnych platformy Azure.

### <a name="cross-regional-storage-mounting"></a>Instalowanie magazynu między regionami regionalnymi

Możliwość dostępu do magazynu kopii zapasowych w różnych regionach to istotny aspekt BCDR i przydatny do klonowania baz danych z kopii zapasowych w różnych regionach geograficznych. Magazyn w chmurze Azure oferuje pięć poziomów [nadmiarowości](../../../storage/common/storage-redundancy.md)
- Magazyn lokalnie nadmiarowy [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) , w którym dane są replikowane trzy razy w jednej lokalizacji fizycznej w regionie podstawowym.  
- Magazyn strefowo nadmiarowy [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) , w którym dane są chronione przez LRS w regionie podstawowym i replikowane synchronicznie w trzech strefach dostępności (az) w regionie podstawowym. Każdy AZ jest LRS chroniony. 
- Geo-Redundant Storage [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) , w którym dane są chronione przez LRS w regionie podstawowym i zreplikowane asynchronicznie do regionu pomocniczego, w którym jest również chronione przez LRS.
- Magazyn Geograficznie nadmiarowy [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) kopiuje dane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym przy użyciu ZRS. Następnie dane są kopiowane asynchronicznie do pojedynczej lokalizacji fizycznej w regionie pomocniczym. We wszystkich lokalizacjach dane są chronione przez LRS. 
- Dostęp do odczytu Geo-Redundant Storage [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) i dostęp do odczytu strefy geograficznej [(Ra-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) umożliwia dostęp tylko do odczytu do danych replikowanych do regionu pomocniczego. 

#### <a name="blob-storage-and-file-storage"></a>Magazyn obiektów blob i magazyn plików

W przypadku korzystania z Azure Files z protokołami SMB lub NFS 4,1 (wersja zapoznawcza) do zainstalowania jako magazyn kopii zapasowych, należy pamiętać, że Azure Files nie obsługuje magazynu geograficznie nadmiarowego do odczytu (RA-GRS) i geograficznie nadmiarowego magazynu (RA-GZRS). 

Ponadto jeśli wymaganie magazynu kopii zapasowych jest większe niż 5 TiB Azure Files wymaga włączonej [funkcji dużych udziałów plików](../../../storage/files/storage-files-planning.md) , która nie obsługuje GRS lub GZRS nadmiarowości — obsługiwana jest tylko LRS. 

Usługa Azure Blob zainstalowana przy użyciu protokołu NFS 3,0 (wersja zapoznawcza) obecnie obsługuje tylko nadmiarowość LRS i ZRS.  

Obiekt blob platformy Azure skonfigurowany z dowolną opcją nadmiarowości można zainstalować za pomocą Blobfuse.

#### <a name="recovery-services-vault"></a>Magazyn usługi Recovery Services 

Magazyn usługi Recovery Services to jednostka, która przechowuje utworzone punkty odzyskiwania oraz udostępnia interfejs umożliwiający wykonywanie operacji tworzenia kopii zapasowych. Obejmują one wykonywanie kopii zapasowych na żądanie, wykonywanie operacji przywracania i tworzenie zasad kopii zapasowych.
Azure Backup automatycznie obsługuje magazyn dla magazynu. Należy określić sposób replikowania tego magazynu podczas tworzenia. Nie można go zmienić po włączeniu elementów w magazynie. W celu zapewnienia nadmiarowości regionalnej wybierz ustawienie geograficznie nadmiarowe.

Jeśli zamierzasz przywrócić do pomocniczego, [sparowany region platformy Azure](../../../best-practices-availability-paired-regions.md) Włącz funkcję [przywracania między regionami](../../../backup/backup-create-rs-vault.md) . Po włączeniu przywracania między regionami magazyn kopii zapasowych jest przenoszony z GRS do magazynu geograficznie nadmiarowego do odczytu (RA-GRS). 

### <a name="azure-blob-storage"></a>Azure Blob Storage

Usługa Azure Blob Storage to wysoce opłacalna, trwała i bezpieczna usługi magazynu oparte na chmurze służące do przechowywania dużych ilości danych bez struktury i są idealnym nośnikiem używanym do Oracle Database kopii zapasowych. Usługę Azure Blob Storage można zainstalować na maszynie wirtualnej platformy Azure w systemie Linux przy użyciu protokołu lub Blobfuse systemu plików NFS w wersji 3.0.

#### <a name="azure-blob-blobfuse"></a>Blobfuse obiektów blob platformy Azure

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) to projekt typu "open source" utworzony w celu zapewnienia wirtualnego systemu plików obsługiwanego przez usługę Azure Blob Storage. Używa biblioteki Open Source libfuse do komunikowania się z modułem jądra BEZPIECZNIKa systemu Linux i implementuje operacje systemu plików przy użyciu Azure Storage Blob interfejsów API REST. Blobfuse jest obecnie dostępna dla dystrybucji Ubuntu i CentOS/RedHat. Jest ona również dostępna do Kubernetes przy użyciu [sterownika CSI](https://github.com/kubernetes-sigs/blob-csi-driver). 

Jednocześnie w całym regionie świadczenia usługi Azure i działa ze wszystkimi typami kont magazynu, w tym ogólnego przeznaczenia v1/v2 i Azure Data Lake Store Gen2, wydajność oferowana przez Blobfuse była mniejsza niż w przypadku alternatywnych protokołów, takich jak SMB lub NFS. W celu zapewnienia przydatności jako nośnika kopii zapasowej bazy danych zalecamy użycie protokołów SMB lub [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) do zainstalowania usługi Azure Blob Storage. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v 3.0 (wersja zapoznawcza)

Pomoc techniczna platformy Azure dla protokołu system plików NFS w wersji 3.0 jest teraz w wersji zapoznawczej. Obsługa systemu [plików NFS](../../../storage/blobs/network-file-system-protocol-support.md) umożliwia klientom z systemem Windows i Linux zainstalowanie kontenera magazynu obiektów BLOB na maszynie wirtualnej platformy Azure. 

W systemie NFS 3,0 publiczna wersja zapoznawcza obsługuje konta magazynu GPV2 z wydajnością warstwy Standardowa w następujących regionach: 
- Australia Wschodnia
- Korea Środkowa
- Południowo-środkowe stany USA. 

Aby zapewnić bezpieczeństwo sieci, konto magazynu używane na potrzeby instalacji systemu plików NFS musi być zawarte w sieci wirtualnej. Zabezpieczenia Azure Active Directory (AD) i listy kontroli dostępu (ACL) nie są jeszcze obsługiwane na kontach, na których włączono obsługę protokołu NFS 3,0.

### <a name="azure-files"></a>Azure Files

[Azure Files](../../../storage/files/storage-files-introduction.md) to oparty na chmurze, w pełni zarządzany system plików rozproszonych, który można zainstalować w lokalnych lub opartych na chmurze klientach z systemem Windows, Linux lub macOS.

Azure Files oferuje w pełni zarządzane udziały plików dla wielu platform w chmurze, które są dostępne za pośrednictwem protokołu bloku komunikatów serwera (SMB) i protokołu sieciowego systemu plików (NFS) (wersja zapoznawcza). Azure Files nie obsługuje obecnie dostępu do protokołu wieloprotokołowego, więc udział może być tylko udziałem NFS lub udziałem SMB. Zalecamy określenie protokołu, który najlepiej odpowiada Twoim potrzebom przed utworzeniem udziałów plików platformy Azure.

Udziały plików platformy Azure mogą być również chronione za pomocą Azure Backup do magazynu usługi Recovery Services, co zapewnia dodatkową warstwę ochrony kopii zapasowych programu Oracle RMAN.

#### <a name="azure-files-nfs-v41-preview"></a>Azure Files NFS v 4.1 (wersja zapoznawcza)

Udziały plików platformy Azure można instalować w dystrybucjach systemu Linux przy użyciu protokołu system plików NFS w wersji 4.1. W wersji zapoznawczej istnieją różne ograniczenia dotyczące obsługiwanych funkcji, które opisano [tutaj](../../../storage/files/storage-files-how-to-mount-nfs-shares.md). 

W wersji zapoznawczej Azure Files system plików NFS v 4.1 również jest ograniczony do następujących [regionów](../../../storage/files/storage-files-how-to-mount-nfs-shares.md):
- Wschodnie stany USA (LRS i ZRS)
- Wschodnie stany USA 2
- Zachodnie stany USA 2
- West Europe
- Southeast Asia
- Południowe Zjednoczone Królestwo
- Australia Wschodnia

#### <a name="azure-files-smb-30"></a>Azure Files SMB 3,0

Udziały plików platformy Azure można instalować w dystrybucjach systemu Linux przy użyciu klienta jądra SMB. Protokół Common Internet File System (CIFS), dostępny w dystrybucji systemu Linux, jest dialektem protokołu SMB. Podczas instalowania Azure Files SMB na maszynach wirtualnych z systemem Linux jest on instalowany jako system plików CIFS, a pakiet CIFS musi być zainstalowany. 

Azure Files protokół SMB jest ogólnie dostępny we wszystkich regionach świadczenia usługi Azure i ma takie same charakterystyki wydajności, jak protokoły NFS 3.0 i v 4.1 i dlatego jest obecnie zalecaną metodą udostępniania nośników magazynu kopii zapasowych na maszynach wirtualnych platformy Azure Linux.  

Dostępne są dwie obsługiwane wersje protokołu SMB, SMB 2,1 i SMB 3,0, które są zalecane, ponieważ obsługuje szyfrowanie podczas przesyłania. Jednak różne wersje jądra systemu Linux mają różne wsparcie dla protokołów SMB 2,1 i 3,0 i należy [sprawdzić tabelę, aby zapewnić, że](../../../storage/files/storage-how-to-use-files-linux.md) aplikacja obsługuje protokół SMB 3,0. 

Ponieważ Azure Files jest zaprojektowana jako usługa udziału plików dla wielu użytkowników, istnieją pewne cechy, które należy dostosować w taki sposób, aby były bardziej odpowiednie jako nośniki magazynu kopii zapasowych. Zalecane jest wyłączenie buforowania i ustawienie identyfikatorów użytkowników i grup dla tworzonych plików.

## <a name="azure-netapp-files"></a>Azure NetApp Files

Usługa [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) to kompletne rozwiązanie magazynu dla baz danych Oracle na maszynach wirtualnych platformy Azure. Oparty na klasie korporacyjnej o wysokiej wydajności, mierzonym magazynie plików obsługuje dowolny typ obciążenia i ma wysoką dostępność. Wraz ze sterownikiem usługi Oracle Direct NFS (dNFS) Azure NetApp Files zapewnia wysoce zoptymalizowaną warstwę magazynu dla Oracle Database.

Azure NetApp Files zapewnia wydajne kopie migawek opartych na magazynie w źródłowym systemie magazynu, który używa mechanizmu przekierowania na zapis (wiersz). Podczas gdy kopie migawek są niezwykle szybkie i są przywracane, są one używane tylko jako pierwsze sposoby obrony, które mogą uwzględniać większość wymaganych operacji przywracania danej organizacji, co jest często odzyskiwane po błędzie ludzkim. Jednak kopie migawek nie są kompletną kopią zapasową. Aby objąć wszystkie wymagania dotyczące kopii zapasowych i przywracania, zewnętrzne repliki migawek i/lub inne kopie zapasowe muszą być tworzone w zdalnej lokalizacji geograficznej, aby chronić przed awarią regionalną. Aby dowiedzieć się więcej o używaniu plików NetApp dla baz danych Oracle na platformie Azure, przeczytaj ten [raport](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf).

## <a name="azure-backup-service"></a>Usługa Azure Backup

[Azure Backup](../../../backup/backup-overview.md) to w pełni zarządzana PaaS, która zapewnia proste, bezpieczne i ekonomiczne rozwiązania do tworzenia kopii zapasowych danych i odzyskiwania ich z chmury Microsoft Azure. Azure Backup umożliwia tworzenie kopii zapasowych i przywracanie lokalnych klientów, maszyn wirtualnych platformy Azure, udziałów Azure Files oraz baz danych SQL Server, Oracle, MySQL, PostreSQL i SAP HANA na maszynach wirtualnych platformy Azure. 

usługa Azure Backup umożliwia wykonywanie niezależnych i odizolowanych kopii zapasowych, co chroni przed przypadkowym zniszczeniem oryginalnych danych. Kopie zapasowe są przechowywane w [magazynie Recovery Services](../../../backup/backup-azure-recovery-services-vault-overview.md) przy użyciu wbudowanego zarządzania punktami odzyskiwania. Konfiguracja i skalowalność są proste, kopie zapasowe są optymalizowane i można je łatwo przywrócić w razie potrzeby. Używa podstawowej mocy i nieograniczonej skali chmury platformy Azure w celu zapewnienia wysokiej dostępności bez narzutu na konserwację i monitorowanie. Azure Backup nie ogranicza ilości przesyłanych danych przychodzących lub wychodzących lub opłaty za transferowane dane, a dane są zabezpieczane podczas przesyłania i przechowywania. 

W celu zapewnienia trwałości Azure Backup oferuje wiele typów replikacji, aby zapewnić wysoką dostępność danych kopii zapasowej.

- Magazyn lokalnie nadmiarowy [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) replikuje dane trzy razy (tworzy trzy kopie danych) w jednostce skalowania magazynu w centrum danych.
- Magazyn Geograficznie nadmiarowy [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) jest opcją domyślną i zalecaną replikacją. Magazyn GRS replikuje dane do regionu pomocniczego (setki kilometrów od lokalizacji głównej danych źródłowych).

Magazyn utworzony za pomocą nadmiarowości GRS obejmuje opcję konfigurowania funkcji [przywracania między regionami](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) , która umożliwia przywracanie danych w pomocniczym regionie platformy Azure.

Usługa Azure Backup udostępnia [platformę](../../../backup/backup-azure-linux-app-consistent.md) do osiągnięcia spójności aplikacji podczas tworzenia kopii zapasowych maszyn wirtualnych z systemami Windows i Linux w przypadku różnych aplikacji, takich jak Oracle, MySQL, Mongo DB, SAP HANA i PostGreSQL, nazywane migawek spójnych z aplikacjami. Obejmuje to Wywoływanie skryptu wstępnego (w celu przełączenia w tryb spoczynku) przed wykonaniem migawki dysków i wywołaniem skryptu po zakończeniu wykonywania operacji po wykonaniu migawki, aby przywrócić aplikacje do trybu normalnego. Podczas gdy przykładowe skrypty wstępne i skrypty są udostępniane w serwisie GitHub, jego zadaniem jest tworzenie i obsługa tych skryptów. W przypadku programu Oracle baza danych musi być w trybie ARCHIVELOG, aby umożliwić tworzenie kopii zapasowych online, a odpowiednie polecenia rozpoczynania i kończenia tworzenia kopii zapasowej bazy danych są uruchamiane w skryptach pre i post, które należy utworzyć i obsługiwać. 

Azure Backup udostępnia teraz [udoskonaloną strukturę pre-Script i po skrypcie](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), obecnie w wersji zapoznawczej, w której usługa Azure Backup udostępnia spakowane skrypty wstępne i skrypty dla wybranych aplikacji. Azure Backup użytkownicy muszą mieć nazwę aplikacji, a następnie kopia zapasowa maszyny wirtualnej platformy Azure automatycznie wywoła odpowiednie skrypty wstępne i skrypty. Spakowane skrypty przed i skrypty będą obsługiwane przez zespół Azure Backup, dzięki czemu użytkownicy będą mogli zapewnić pomoc techniczną, własność i ważność tych skryptów. Obecnie obsługiwane aplikacje dla ulepszonej struktury to Oracle i MySQL, a w przyszłości można oczekiwać większej liczby typów aplikacji. Migawka będzie pełną kopią magazynu, a nie przyrostową lub kopią na migawce zapisu, więc jest efektywnym czynnikiem do przywrócenia bazy danych z programu.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie Oracle Database — Szybki Start](oracle-database-quick-create.md)
- [Utwórz kopię zapasową Oracle Database do Azure Files](oracle-database-backup-azure-storage.md)
- [Tworzenie kopii zapasowej Oracle Database przy użyciu usługi Azure Backup](oracle-database-backup-azure-backup.md)


