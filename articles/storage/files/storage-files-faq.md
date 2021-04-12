---
title: Często zadawane pytania dotyczące Azure Files | Microsoft Docs
description: Uzyskaj odpowiedzi na Azure Files często zadawanych pytań. Udziały plików platformy Azure można instalować jednocześnie w chmurze lub lokalnych wdrożeniach systemu Windows, Linux lub macOS.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: ec8104a5fd8d1c524f75c7a5173015115d85a253
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064311"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Często zadawane pytania dotyczące usługi Azure Files
[Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego [protokołu bloku komunikatów serwera (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) i [protokołu sieciowego systemu plików (NFS)](https://en.wikipedia.org/wiki/Network_File_System) (wersja zapoznawcza). Udziały plików platformy Azure można instalować jednocześnie w chmurze lub lokalnych wdrożeniach systemów Windows, Linux i macOS. Możesz również buforować udziały plików platformy Azure na maszynach z systemem Windows Server, używając Azure File Sync, aby szybko uzyskać dostęp do miejsca, w którym są używane dane.

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure Files funkcji i funkcji, w tym używania Azure File Sync z Azure Files. Jeśli nie widzisz odpowiedzi na pytanie, możesz skontaktować się z nami za pomocą następujących kanałów (w kolejności eskalacji):

1. Sekcja komentarzy w tym artykule.
2. [Microsoft Q&stronie pytania dotyczącej usługi Azure Storage](/answers/topics/azure-file-storage.html).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. pomoc techniczna firmy Microsoft. Aby utworzyć nowe żądanie obsługi, w Azure Portal na karcie **Pomoc** wybierz przycisk **Pomoc i obsługa techniczna** , a następnie wybierz pozycję **nowe żądanie obsługi**.

## <a name="general"></a>Ogólne
* <a id="why-files-useful"></a>
  **Jak Azure Files być przydatne?**  
   Za pomocą Azure Files można tworzyć udziały plików w chmurze bez konieczności zarządzania obciążeniem serwera fizycznego, urządzenia ani urządzenia. Firma Microsoft monotonous Ci, w tym stosowanie aktualizacji systemu operacyjnego i zastępowaniem uszkodzonych dysków. Aby dowiedzieć się więcej na temat scenariuszy, które Azure Files mogą pomóc, zobacz [dlaczego Azure Files jest przydatna](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Jakie są różne sposoby uzyskiwania dostępu do plików w Azure Files?**  
    Udziały plików SMB można instalować na komputerze lokalnym przy użyciu protokołu SMB 3,0 lub użyć narzędzi takich jak [Eksplorator usługi Storage](https://storageexplorer.com/) , aby uzyskać dostęp do plików w udziale plików. Udziały plików NFS można instalować na komputerze lokalnym przez kopiowanie/wklejanie skryptu dostarczonego przez Azure Portal. Z poziomu aplikacji możesz użyć bibliotek klienta magazynu, interfejsów API REST, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby uzyskać dostęp do plików w udziale plików platformy Azure.

* <a id="what-is-afs"></a>
  **Co to jest Azure File Sync?**  
    Za pomocą Azure File Sync można scentralizować udziały plików w organizacji w Azure Files, utrzymując elastyczność, wydajność i zgodność lokalnego serwera plików. Azure File Sync przekształca komputery z systemem Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego protokołu, który jest dostępny w systemie Windows Server, aby uzyskać dostęp do danych lokalnie, w tym SMB, sieciowego systemu plików (NFS) i usługi protokół transferu plików (FTPS). Na całym świecie możesz mieć dowolną liczbę pamięci podręcznych.

* <a id="files-versus-blobs"></a>
  **Dlaczego mam korzystać z udziału plików platformy Azure I usługi Azure Blob Storage?**  
    Azure Files i magazyn obiektów blob platformy Azure umożliwiają przechowywanie dużych ilości danych w chmurze, ale są one przydatne w nieco inny sposób. 
    
    Usługa Azure Blob Storage jest przydatna w przypadku dużych skalowalnych aplikacji w chmurze, które muszą przechowywać dane niestrukturalne. W celu zmaksymalizowania wydajności i skalowania usługa Azure Blob Storage jest prostszym abstrakcją pamięci masowej niż prawdziwy system plików. Dostęp do usługi Azure Blob Storage można uzyskać tylko za pośrednictwem bibliotek klienckich opartych na interfejsie REST (lub bezpośrednio za pośrednictwem protokołu REST).

    Azure Files to system plików. Azure Files ma wszystkie znane streszczenia plików i miłość od lat pracy z lokalnymi systemami operacyjnymi. Podobnie jak w przypadku usługi Azure Blob Storage, Azure Files oferuje Interfejs REST i biblioteki klienckie oparte na protokole REST. W przeciwieństwie do usługi Azure Blob Storage, Azure Files oferuje dostęp SMB lub NFS do udziałów plików platformy Azure. Udziały plików można instalować bezpośrednio w systemie Windows, Linux lub macOS, lokalnie lub na maszynach wirtualnych w chmurze, bez konieczności pisania kodu lub dołączania specjalnych sterowników do systemu plików. Można również buforować udziały plików SMB platformy Azure na lokalnych serwerach plików przy użyciu Azure File Sync, aby uzyskać szybki dostęp, w pobliżu miejsca, w którym są używane dane. 
   
    Aby zapoznać się z bardziej szczegółowym opisem różnic między Azure Files i magazynem obiektów blob platformy Azure, zobacz [wprowadzenie do podstawowych usług Azure Storage](../common/storage-introduction.md). Aby dowiedzieć się więcej o usłudze Azure Blob Storage, zobacz [wprowadzenie do usługi BLOB Storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Dlaczego mam korzystać z udziału plików platformy Azure, a nie z usługi Azure disks?**  
    Dysk w usłudze Azure disks jest po prostu dyskiem. Aby uzyskać wartość z dysków platformy Azure, należy dołączyć dysk do maszyny wirtualnej działającej na platformie Azure. Dysków platformy Azure można używać dla wszystkich elementów, z których można korzystać na serwerze lokalnym. Można go użyć jako dysku systemu operacyjnego, jako obszaru wymiany dla systemu operacyjnego lub jako dedykowanego magazynu dla aplikacji. Interesujące użycie usługi Azure disks polega na utworzeniu serwera plików w chmurze, który będzie używany w tych samych miejscach, w których może być używany udział plików platformy Azure. Wdrożenie serwera plików na platformie Azure Virtual Machines jest wysoce wydajnym sposobem uzyskiwania magazynu plików na platformie Azure, gdy wymagane są opcje wdrażania, które nie są obecnie obsługiwane przez Azure Files. 

    Jednak uruchomienie serwera plików z dyskami platformy Azure jako magazynu zaplecza zwykle jest znacznie droższe niż korzystanie z udziału plików platformy Azure z kilku powodów. Po pierwsze, oprócz płacenia za magazyn na dyskach, należy również płacić za wydatki związane z uruchamianiem co najmniej jednej maszyny wirtualnej platformy Azure. Po drugie należy również zarządzać maszynami wirtualnymi używanymi do uruchamiania serwera plików. Na przykład użytkownik jest odpowiedzialny za uaktualnienia systemu operacyjnego. Na koniec w przypadku założenia, że dane mają być buforowane lokalnie, można skonfigurować technologie replikacji, takie jak rozproszony system plików Replication (DFSR), i zarządzać nimi.

    Jednym z metod uzyskiwania najlepszych Azure Files i serwera plików hostowanego w usłudze Azure Virtual Machines (oprócz używania dysków platformy Azure jako magazynu zaplecza) jest zainstalowanie Azure File Sync na serwerze plików hostowanym na maszynie wirtualnej w chmurze. Jeśli udział plików platformy Azure znajduje się w tym samym regionie, w którym znajduje się serwer plików, można włączyć obsługę warstw w chmurze i ustawić wartość procentową ilości wolnego miejsca na maksymalną (99%). Zapewnia to minimalne duplikowanie danych. Możesz również użyć dowolnych aplikacji do serwerów plików, takich jak aplikacje wymagające obsługi protokołu NFS.

    Aby uzyskać informacje dotyczące opcji konfigurowania serwera plików o wysokiej wydajności i wysokiej dostępności na platformie Azure, zobacz [wdrażanie klastrów gościa maszyny wirtualnej IaaS w Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Aby zapoznać się z bardziej szczegółowym opisem różnic między Azure Files i dyskami platformy Azure, zobacz [wprowadzenie do podstawowych usług Azure Storage](../common/storage-introduction.md). Aby dowiedzieć się więcej o usłudze Azure disks, zobacz [Omówienie usługi azure Managed disks](../../virtual-machines/managed-disks-overview.md).

* <a id="get-started"></a>
  **Jak mogę rozpocząć korzystanie z Azure Files?**  
   Wprowadzenie do Azure Files jest proste. Najpierw [Utwórz udział plików SMB](storage-how-to-create-file-share.md) lub [sposób tworzenia udziału NFS](storage-files-how-to-create-nfs-shares.md), a następnie zainstaluj go w preferowanym systemie operacyjnym: 

  * [Instalowanie udziału SMB w systemie Windows](storage-how-to-use-files-windows.md)
  * [Instalowanie udziału SMB w systemie Linux](storage-how-to-use-files-linux.md)
  * [Instalowanie udziału SMB w macOS](storage-how-to-use-files-mac.md)
  * [Instalowanie udziału plików NFS](storage-files-how-to-mount-nfs-shares.md)

    Aby zapoznać się z bardziej szczegółowym przewodnikiem wdrażania udziału plików platformy Azure w celu zastąpienia produkcyjnych udziałów plików w organizacji, zobacz [Planowanie wdrożenia Azure Files](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Jakie opcje nadmiarowości magazynu są obsługiwane przez Azure Files?**  
    Obecnie Azure Files obsługuje Magazyn lokalnie nadmiarowy (LRS), magazyn strefowo nadmiarowy (ZRS), magazyn Geograficznie nadmiarowy (GRS) i magazyn Geograficznie nadmiarowy (GZRS). Azure Files warstwa Premium obecnie obsługuje tylko LRS i ZRS.

* <a id="tier-options"></a>
  **Jakie warstwy magazynowania są obsługiwane w Azure Files?**  
    Azure Files obsługuje dwie warstwy magazynowania: Premium i Standard. Standardowe udziały plików są tworzone na kontach magazynu ogólnego przeznaczenia (GPv1 lub GPv2), a udziały plików w warstwie Premium są tworzone na kontach magazynu FileStorage. Dowiedz się więcej na temat tworzenia [standardowych udziałów plików](storage-how-to-create-file-share.md) i [udziałów plików w warstwie Premium](./storage-how-to-create-file-share.md). 
    
    > [!NOTE]
    > Nie można tworzyć udziałów plików platformy Azure z kont magazynu obiektów blob lub ogólnego przeznaczenia (GPv1 lub GPv2) w *warstwie Premium* . Standardowe udziały plików platformy Azure muszą zostać utworzone wyłącznie w ramach *standardowych* kont ogólnego przeznaczenia, a udziały plików platformy Azure w warstwie Premium muszą zostać utworzone tylko na kontach magazynu FileStorage. Konta magazynu ogólnego przeznaczenia (GPv1 i GPv2) są przeznaczone tylko dla stron sieci Web *w warstwie Premium* . 

* <a id="file-locking"></a>
  **Czy Azure Files obsługuje blokowanie plików?**  
    Tak, Azure Files w pełni obsługuje blokowanie plików w stylu SMB/Windows, [Zobacz szczegóły](/rest/api/storageservices/managing-file-locks).

* <a id="give-us-feedback"></a>
  **Chcemy zobaczyć konkretną funkcję dodaną do Azure Files. Czy można je dodać?**  
    Zespół Azure Files ma na celu wysłuchanie wszelkich opinii na temat naszej usługi. Zagłosuj na żądania funkcji w witrynie [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Czekamy na poszukiwanie wielu nowych funkcji.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Jakie regiony są obsługiwane dla Azure File Sync?**  
    Listę dostępnych regionów można znaleźć w sekcji [dostępność regionu](storage-sync-files-planning.md#azure-file-sync-region-availability) w podręczniku planowania Azure File Sync. Ciągle będziemy dodawać obsługę dodatkowych regionów, w tym regionów niepublicznych.

* <a id="cross-domain-sync"></a>
  **Czy można korzystać z przyłączonych do domeny serwerów i przyłączonych do domeny w tej samej grupie synchronizacji?**  
    Tak. Grupa synchronizacji może zawierać punkty końcowe serwera, które mają różne członkostwa Active Directory, nawet jeśli nie są przyłączone do domeny. Mimo że ta konfiguracja technicznie działa, nie zalecamy jej jako typowej konfiguracji, ponieważ listy kontroli dostępu (ACL) zdefiniowane dla plików i folderów na jednym serwerze mogą nie być wymuszane przez inne serwery w grupie synchronizacji. Aby uzyskać najlepsze wyniki, zalecamy synchronizację między serwerami znajdującymi się w tym samym lesie Active Directory, między serwerami, które znajdują się w różnych Active Directory lasach, ale które ustanowiły relacje zaufania lub między serwerami, które nie należą do domeny. Zalecamy uniknięcie użycia kombinacji tych konfiguracji.

* <a id="afs-change-detection"></a>
  **Plik został utworzony bezpośrednio w udziale plików platformy Azure przy użyciu protokołu SMB lub w portalu. Jak długo trwa synchronizacja pliku z serwerami w grupie synchronizacji?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Jak długo trwa Azure File Sync przekazywania 1TiB danych?**
  
    Wydajność będzie się różnić w zależności od ustawień środowiska, konfiguracji i tego, czy jest to synchronizacja początkowa czy ciągła synchronizacja. Aby uzyskać więcej informacji, zobacz [Azure File Sync metryki wydajności](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Jeśli ten sam plik zostanie zmieniony na dwóch serwerach w tym samym czasie, co się dzieje?**  
    Azure File Sync korzysta z prostej strategii rozwiązywania konfliktów: zachowujemy zmiany w plikach, które są zmieniane w dwóch punktach końcowych w tym samym czasie. Ostatnio zapisywana zmiana zachowuje oryginalną nazwę pliku. Starszy plik (określony przez LastWriteTime) ma nazwę punktu końcowego i numer konfliktu dołączony do nazwy pliku. W przypadku punktów końcowych serwera nazwa punktu końcowego jest nazwą serwera. W przypadku punktów końcowych w chmurze nazwa punktu końcowego to **chmura**. Nazwa jest zgodna z tą taksonomią: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Na przykład pierwszy konflikt CompanyReport.docx byłby CompanyReport-CentralServer.docx, jeśli CentralServer jest miejsce, w którym wystąpił starszy zapis. Drugi konflikt zostałby nazwany CompanyReport-CentralServer-1.docx. Azure File Sync obsługuje pliki konfliktów 100 na plik. Po osiągnięciu maksymalnej liczby plików konfliktów plik nie zostanie zsynchronizowany, dopóki liczba plików konfliktów nie będzie mniejsza niż 100.

* <a id="afs-storage-redundancy"></a>
  **Czy Azure File Sync jest magazyn Geograficznie nadmiarowy?**  
    Tak, Azure Files obsługuje Magazyn lokalnie nadmiarowy (LRS) i magazyn Geograficznie nadmiarowy (GRS). Jeśli zainicjujesz tryb failover konta magazynu między sparowanymi regionami na koncie skonfigurowanym dla usługi GRS, firma Microsoft zaleca traktowanie nowego regionu jako kopii zapasowej tylko danych. Azure File Sync nie rozpoczyna automatycznej synchronizacji z nowym regionem podstawowym. 

* <a id="sizeondisk-versus-size"></a>
  **Dlaczego rozmiar właściwości *dysku* dla pliku jest zgodny z właściwością *size* po użyciu Azure File Sync?**  
  Zobacz artykuł [Omówienie obsługi warstw w chmurze Azure File Sync](storage-sync-cloud-tiering-overview.md#tiered-vs-locally-cached-file-behavior).

* <a id="is-my-file-tiered"></a>
  **Jak mogę sprawdzić, czy plik został warstwowy?**  
  Zobacz [, jak zarządzać plikami warstwowymi Azure File Sync](storage-sync-how-to-manage-tiered-files.md#how-to-check-if-your-files-are-being-tiered).

* <a id="afs-recall-file"></a>**Plik, którego chcę użyć, został warstwowy. Jak można przywołać plik na dysk, aby użyć go lokalnie?**  
  Zobacz [, jak zarządzać plikami warstwowymi Azure File Sync](storage-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk).

* <a id="afs-force-tiering"></a>
  **Jak mogę wymusić przeprowadzenie warstwowego pliku lub katalogu?**  
  Zobacz [, jak zarządzać plikami warstwowymi Azure File Sync](storage-sync-how-to-manage-tiered-files.md#how-to-force-a-file-or-directory-to-be-tiered).

* <a id="afs-effective-vfs"></a>
  **Jak jest interpretowane *wolne miejsce na woluminie* , gdy mam wiele punktów końcowych serwera na woluminie?**  
  Zobacz [wybieranie Azure File Sync zasad obsługi warstw w chmurze](storage-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume).
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Mam wyłączone warstwy chmury, dlaczego istnieją pliki warstwowe w lokalizacji punktu końcowego serwera?**  
    Istnieją dwa powody, dla których w lokalizacji punktu końcowego serwera mogą znajdować się pliki warstwowe:

    - W przypadku dodawania nowego punktu końcowego serwera do istniejącej grupy synchronizacji, jeśli wybierzesz opcję przywoływanie przestrzeni nazw lub opcji odwołaj tylko przestrzeń nazw dla początkowego trybu pobierania, pliki będą wyświetlane jako warstwowe, dopóki nie zostaną pobrane lokalnie. Aby tego uniknąć, zaznacz opcję Unikaj plików warstwowych w trybie pobierania początkowego. Aby ręcznie odwołać pliki, użyj polecenia cmdlet [Invoke-StorageSyncFileRecall](storage-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk) .

    - Jeśli Obsługa warstw w chmurze została włączona w punkcie końcowym serwera, a następnie wyłączona, pliki pozostaną warstwą do momentu uzyskania do nich dostępu.

* <a id="afs-tiered-files-not-showing-thumbnails"></a>
  **Dlaczego moje pliki warstwowe nie pokazują miniatur lub podglądów w Eksploratorze Windows?**  
    W przypadku plików warstwowych miniatury i podglądy nie będą widoczne w punkcie końcowym serwera. To zachowanie jest oczekiwane, ponieważ funkcja pamięci podręcznej miniatur w systemie Windows celowo pomija odczytywanie plików z atrybutem offline. Po włączeniu obsługi warstw w chmurze odczytywanie za pomocą plików warstwowych spowodowałoby ich pobranie (są one wywoływane).

    To zachowanie nie jest specyficzne dla Azure File Sync, Eksplorator Windows wyświetla "szary X" dla wszystkich plików, które mają ustawiony atrybut offline. Podczas uzyskiwania dostępu do plików za pośrednictwem protokołu SMB zobaczysz ikonę X. Szczegółowe wyjaśnienie tego zachowania można znaleźć w [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

    Pytania dotyczące zarządzania plikami warstwowymi można znaleźć w temacie [jak zarządzać plikami warstwowymi](storage-sync-how-to-manage-tiered-files.md).

* <a id="afs-files-excluded"></a>
  **Które pliki lub foldery są automatycznie wykluczane przez Azure File Sync?**  
  Zobacz [pliki pominięte](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Czy można użyć Azure File Sync z systemem Windows Server 2008 R2, Linux lub na moim urządzeniu magazynującym podłączonym do sieci (NAS)?**  
    Obecnie Azure File Sync obsługuje tylko systemy Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2. W tej chwili nie mamy żadnych innych planów, które możemy udostępnić, ale jesteśmy w stanie obsłużyć dodatkowe platformy na podstawie popytu klientów. Poinformuj nas o tym, co Azure Files platformę [UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) , które mają być obsługiwane przez firmę Microsoft.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Dlaczego pliki warstwowe istnieją poza przestrzenią nazw punktu końcowego serwera?**  
    Przed Azure File Sync agentem w wersji 3 Azure File Sync zablokował przenoszenie plików warstwowych poza punkt końcowy serwera, ale na tym samym woluminie, na którym znajduje się punkt końcowy serwera. Operacje kopiowania, przenoszenia plików niewarstwowych i przenoszenia warstwowych do innych woluminów nie były naruszone. Przyczyną tego zachowania było niejawne założenie, że Eksplorator plików i inne interfejsy API systemu Windows mają te operacje przenoszenia na tym samym woluminie (niemal) operacji zmiany nazwy. Oznacza to, że przeniesienie spowoduje, że Eksplorator plików lub inne metody przenoszenia (takie jak wiersz polecenia lub program PowerShell) nie odpowiada, podczas gdy Azure File Sync ponownie wywołuje dane z chmury. Począwszy od [Azure File Sync agenta w wersji 3.0.12.0](storage-files-release-notes.md#supported-versions), Azure File Sync umożliwi przeniesienie pliku warstwowego poza punkt końcowy serwera. Pozwalamy uniknąć negatywnych skutków wcześniej wymienionych przez umożliwienie, aby plik warstwowy istniał jako plik warstwowy spoza punktu końcowego serwera, a następnie ponownie wywołuje plik w tle. Oznacza to, że przenoszone na tym samym woluminie są chwilowe, a wszystkie prace do odwoływania pliku do dysku po zakończeniu przenoszenia. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Mam problem z Azure File Sync na serwerze (synchronizacja, Obsługa warstw w chmurze itp.). Czy należy usunąć i ponownie utworzyć mój punkt końcowy serwera?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Czy mogę przenieść usługę synchronizacji magazynu i/lub konto magazynu do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD?**  
   Tak, usługa synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD. Po przeniesieniu usługi synchronizacji magazynu lub konta magazynu musisz nadać aplikacji Microsoft. StorageSync dostęp do konta magazynu (Sprawdź, [czy Azure File Sync ma dostęp do konta magazynu](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Podczas tworzenia punktu końcowego w chmurze usługa synchronizacji magazynu i konto magazynu muszą znajdować się w tej samej dzierżawie usługi Azure AD. Po utworzeniu punktu końcowego w chmurze można przenieść usługę synchronizacji magazynu i konto magazynu do różnych dzierżawców usługi Azure AD.
    
* <a id="afs-ntfs-acls"></a>
  **Czy Azure File Sync zachować listy ACL na poziomie katalogu/pliku systemu plików NTFS wraz z danymi przechowywanymi w Azure Files?**

    Począwszy od 24 lutego 2020, nowe i istniejące listy kontroli dostępu w warstwach przez usługę Azure File Sync zostaną utrwalone w formacie systemu plików NTFS, a modyfikacje ACL wprowadzone bezpośrednio do udziału plików platformy Azure zostaną zsynchronizowane ze wszystkimi serwerami w grupie synchronizacji. Wszystkie zmiany list ACL wprowadzonych do Azure Files zostaną zsynchronizowane za pośrednictwem usługi Azure File Sync. Podczas kopiowania danych do Azure Files upewnij się, że używasz narzędzia do kopiowania, które obsługuje "wierność", aby skopiować atrybuty, sygnatury czasowe i listy kontroli dostępu do udziału plików platformy Azure — za pośrednictwem protokołu SMB lub REST. W przypadku korzystania z narzędzi do kopiowania platformy Azure, takich jak AzCopy, ważne jest, aby użyć najnowszej wersji. Zapoznaj się z [tabelą narzędzia kopiowania plików](storage-files-migration-overview.md#file-copy-tools) , aby zapoznać się z omówieniem narzędzi do kopiowania na platformie Azure w celu zagwarantowania, że można skopiować wszystkie ważne metadane pliku.

    Jeśli włączono Azure Backup w udziałach plików zarządzanych przez funkcję synchronizacji plików, listy ACL plików można nadal przywracać w ramach przepływu pracy przywracania kopii zapasowych. Działa to zarówno w przypadku całego udziału, jak i poszczególnych plików lub katalogów.

    W przypadku korzystania z migawek w ramach rozwiązania do samodzielnego zarządzania kopiami zapasowymi dla udziałów plików zarządzanych przez funkcję synchronizacji plików listy ACL mogą nie zostać przywrócone prawidłowo do list ACL systemu plików NTFS, jeśli migawki zostały wykonane przed 24 lutego, 2020. W takim przypadku warto skontaktować się z pomocą techniczną platformy Azure.

* <a id="afs-lastwritetime"></a>
  **Czy Azure File Sync synchronizować LastWriteTime dla katalogów?**  
    Nie, Azure File Sync nie synchronizuje LastWriteTime dla katalogów. Jest to celowe.
    
## <a name="security-authentication-and-access-control"></a>Zabezpieczenia, uwierzytelnianie i kontrola dostępu
* <a id="ad-support"></a>
**Czy uwierzytelnianie oparte na tożsamościach i kontrola dostępu są obsługiwane przez Azure Files?**  
    
    Tak, Azure Files obsługuje uwierzytelnianie oparte na tożsamościach i kontrola dostępu. Aby skorzystać z kontroli dostępu opartej na tożsamościach, można wybrać jeden z dwóch sposobów: lokalna Active Directory Domain Services lub Azure Active Directory Domain Services (Azure AD DS). Active Directory Domain Services lokalnego (AD DS) obsługuje uwierzytelnianie przy AD DS użyciu maszyn przyłączonych do domeny, lokalnie lub na platformie Azure, aby uzyskiwać dostęp do udziałów plików platformy Azure za pośrednictwem protokołu SMB. Usługa Azure AD DS Authentication za pośrednictwem protokołu SMB dla Azure Files umożliwia maszynom wirtualnym z systemem Windows przyłączonych do domeny usługi Azure AD DS dostęp do udziałów, katalogów i plików przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [omówienie Azure Files obsługi uwierzytelniania opartego na tożsamościach na potrzeby dostępu do protokołu SMB](storage-files-active-directory-overview.md). 

    Azure Files oferuje dwa dodatkowe sposoby zarządzania kontrolą dostępu:

    - Sygnatury dostępu współdzielonego (SAS) mogą być używane do generowania tokenów z określonymi uprawnieniami, które są prawidłowe przez określony przedział czasu. Na przykład można wygenerować token z dostępem tylko do odczytu do określonego pliku o 10-minutowym wygaśnięciu. Każda osoba, która posiada token, gdy token jest ważny, ma dostęp tylko do odczytu do tego pliku przez 10 minut. Klucze sygnatury dostępu współdzielonego są obsługiwane tylko za pośrednictwem interfejsu API REST lub bibliotek klienckich. Udział plików platformy Azure należy zainstalować za pośrednictwem protokołu SMB przy użyciu kluczy konta magazynu.

    - Azure File Sync zachowuje i replikuje wszystkie poufne listy kontroli dostępu lub listy DACL (niezależnie od tego, czy Active Directory lub lokalne) wszystkie punkty końcowe serwera, do których jest synchronizowana. 
    
    Aby uzyskać kompleksową reprezentację wszystkich protokołów obsługiwanych przez usługi Azure Storage, można przyjrzeć się do [autoryzacji dostępu do usługi Azure Storage](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) . 
    
* <a id="encryption-at-rest"></a>
**Jak upewnić się, że mój udział plików platformy Azure jest szyfrowany w spoczynku?**  

    Tak. Aby uzyskać więcej informacji, zobacz [Azure szyfrowanie usługi Storage](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Jak zapewnić dostęp do określonego pliku przy użyciu przeglądarki sieci Web?**  

    Sygnatury dostępu współdzielonego umożliwiają generowanie tokenów z określonymi uprawnieniami, które są prawidłowe przez określony przedział czasu. Na przykład można wygenerować token, który zapewnia dostęp tylko do odczytu do określonego pliku, przez określony czas. Każdy posiadacz adresu URL może uzyskać dostęp do pliku bezpośrednio z dowolnej przeglądarki sieci Web, gdy token jest prawidłowy. Możesz łatwo wygenerować klucz sygnatury dostępu współdzielonego z interfejsu użytkownika, takiego jak Eksplorator usługi Storage.

* <a id="file-level-permissions"></a>
**Czy można określić uprawnienia tylko do odczytu lub tylko do zapisu w folderach w ramach udziału?**  

    W przypadku zainstalowania udziału plików przy użyciu protokołu SMB nie ma uprawnień na poziomie folderów. Jeśli jednak utworzysz sygnaturę dostępu współdzielonego za pomocą interfejsu API REST lub bibliotek klienckich, możesz określić uprawnienia tylko do odczytu lub tylko do zapisu w folderach w udziale.

* <a id="ip-restrictions"></a>
**Czy mogę zaimplementować ograniczenia adresów IP dla udziału plików platformy Azure?**  

    Tak. Dostęp do udziału plików platformy Azure można ograniczyć na poziomie konta magazynu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Jakie zasady zgodności danych Azure Files obsługiwać?**  

   Azure Files uruchamiana na podstawie tej samej architektury magazynu, która jest używana w innych usługach magazynu w usłudze Azure Storage. Azure Files stosuje te same zasady zgodności danych, które są używane w innych usługach Azure Storage. Aby uzyskać więcej informacji na temat zgodności danych usługi Azure Storage, możesz zapoznać się z [ofertą zgodności z usługą Azure Storage](../common/storage-compliance-offerings.md), a następnie przejść do [Centrum zaufania firmy Microsoft](https://microsoft.com/trustcenter/default.aspx).

* <a id="file-auditing"></a>
**Jak przeprowadzić inspekcję dostępu do plików i zmian w Azure Files?**

  Dostępne są dwie opcje zapewniające funkcje inspekcji dla Azure Files:
  - Jeśli użytkownicy uzyskują bezpośredni dostęp do udziału plików platformy Azure, [dzienniki usługi Azure Storage (wersja zapoznawcza)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) mogą być używane do śledzenia zmian plików i dostępu użytkowników. Te dzienniki mogą służyć do rozwiązywania problemów, a żądania są rejestrowane na podstawie najlepszego wysiłku.
  - Jeśli użytkownicy uzyskują dostęp do udziału plików platformy Azure za pośrednictwem systemu Windows Server, na którym zainstalowano agenta Azure File Sync, użyj [zasad inspekcji](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) lub produktu innej firmy do śledzenia zmian plików i dostępu użytkowników w systemie Windows Server. 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS & uwierzytelniania AD DS platformy Azure
* <a id="ad-support-devices"></a>
**Czy uwierzytelnianie Azure Files Azure Active Directory Domain Services (Azure AD DS) obsługuje dostęp do protokołu SMB przy użyciu poświadczeń usługi Azure AD z urządzeń przyłączonych do lub zarejestrowanych w usłudze Azure AD?**

    Nie, ten scenariusz nie jest obsługiwany.

* <a id="ad-vm-subscription"></a>
**Czy mogę uzyskać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD z maszyny wirtualnej w ramach innej subskrypcji?**

    Jeśli subskrypcja, w ramach której wdrożono udział plików, jest skojarzona z tą samą dzierżawą usługi Azure AD co wdrożenie AD DS platformy Azure, do której przyłączono maszynę wirtualną, możesz uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu tych samych poświadczeń usługi Azure AD. Ograniczenie jest nakładane nie na subskrypcję, ale w skojarzonej dzierżawie usługi Azure AD.
    
* <a id="ad-support-subscription"></a>
**Czy można włączyć usługę Azure AD DS lub lokalne uwierzytelnianie AD DS dla udziałów plików platformy Azure przy użyciu dzierżawy usługi Azure AD, która różni się od podstawowej dzierżawy udziału plików platformy Azure?**

    Nie, Azure Files obsługuje tylko usługę Azure AD DS lub lokalną integrację AD DS z dzierżawą usługi Azure AD, która znajduje się w tej samej subskrypcji co udział plików. Tylko jedna subskrypcja może być skojarzona z dzierżawą usługi Azure AD. To ograniczenie dotyczy zarówno AD DS platformy Azure, jak i lokalnych metod uwierzytelniania AD DS. W przypadku korzystania z lokalnego AD DS na potrzeby uwierzytelniania [poświadczenia AD DS muszą zostać zsynchronizowane z usługą Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) , z którą skojarzone jest konto magazynu.

* <a id="ad-linux-vms"></a>
**Czy usługa Azure AD DS lub lokalne uwierzytelnianie AD DS dla udziałów plików platformy Azure obsługuje maszyny wirtualne z systemem Linux?**

    Nie, uwierzytelnianie z maszyn wirtualnych systemu Linux nie jest obsługiwane.

* <a id="ad-aad-smb-afs"></a>
**Czy udziały plików zarządzane przez Azure File Sync obsługują usługę Azure AD DS czy uwierzytelnianie lokalne AD DS?**

    Tak. AD DS platformy Azure lub lokalnego uwierzytelniania AD DS można włączyć w udziale plików zarządzanym przez Azure File Sync. Zmiany w katalogu/plikach list ACL systemu plików NTFS na lokalnych serwerach plików będą należeć do Azure Files i na odwrót.

* <a id="ad-aad-smb-files"></a>
**Jak sprawdzić, czy na moim koncie magazynu włączono uwierzytelnianie AD DS i pobrać informacje o domenie?**

    Aby uzyskać instrukcje, zobacz [tutaj](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled).

* <a id=""></a>
**Czy Azure Files uwierzytelnianie usługi Azure AD obsługuje maszyny wirtualne z systemem Linux?**

    Nie, uwierzytelnianie z maszyn wirtualnych systemu Linux nie jest obsługiwane.

* <a id="ad-multiple-forest"></a>
**Czy lokalne uwierzytelnianie AD DS dla udziałów plików platformy Azure obsługuje integrację ze środowiskiem AD DS przy użyciu wielu lasów?**    

    Azure Files lokalnego uwierzytelniania AD DS integruje się tylko z lasem usługi domeny, w której zarejestrowano konto magazynu. Aby można było obsługiwać uwierzytelnianie z innego lasu, środowisko musi mieć prawidłowo skonfigurowane zaufanie lasu. Sposób Azure Files rejestrowania w AD DS niemal taki sam jak zwykły serwer plików, w którym tworzy tożsamość (konto logowania komputera lub usługi) w AD DS na potrzeby uwierzytelniania. Jedyną różnicą jest to, że zarejestrowana nazwa SPN konta magazynu jest zakończona ciągiem "file.core.windows.net", który jest niezgodny z sufiksem domeny. Skontaktuj się z administratorem domeny, aby sprawdzić, czy dowolna aktualizacja zasad routingu sufiksu jest wymagana, aby umożliwić uwierzytelnianie wielu lasów ze względu na inny sufiks domeny. Poniżej przedstawiono przykład konfigurowania zasad routingu sufiksów.
    
    Przykład: gdy użytkownicy w lesie domeny chcą dotrzeć do udziału plików przy użyciu konta magazynu zarejestrowanego w domenie w lesie B, to nie będzie automatycznie działała, ponieważ nazwa główna usługi konta magazynu nie ma sufiksu pasującego do sufiksu dowolnej domeny w lesie A. Aby rozwiązać ten problem, należy ręcznie skonfigurować regułę routingu sufiksu z lasu A do lasu B dla niestandardowego sufiksu "file.core.windows.net".
    Najpierw należy dodać nowy sufiks niestandardowy w lesie B. Upewnij się, że masz odpowiednie uprawnienia administracyjne, aby zmienić konfigurację, a następnie wykonaj następujące kroki:   
    1. Logowanie do domeny komputera przyłączonej do lasu B
    2.  Otwórz konsolę "Active Directory domeny i relacje zaufania"
    3.  Kliknij prawym przyciskiem myszy pozycję "Active Directory domeny i relacje zaufania"
    4.  Kliknij pozycję "właściwości".
    5.  Kliknij pozycję "Dodaj"
    6.  Dodaj "file.core.windows.net" jako sufiksy nazw UPN
    7.  Kliknij przycisk "Zastosuj", a następnie "OK", aby zamknąć kreatora
    
    Następnie Dodaj regułę routingu sufiksów w lesie A, aby przekierować ją do lasu B.
    1.  Logowanie do domeny komputera przyłączonej do lasu A
    2.  Otwórz konsolę "Active Directory domeny i relacje zaufania"
    3.  Kliknij prawym przyciskiem myszy domenę, do której chcesz uzyskać dostęp do udziału plików, a następnie kliknij kartę "relacje zaufania" i wybierz domenę lasu B z zaufania wychodzącego. Jeśli nie skonfigurowano zaufania między tymi dwoma lasami, należy najpierw skonfigurować zaufanie
    4.  Kliknij pozycję "właściwości..." następnie "Nazwa routingu sufiksów"
    5.  Sprawdź, czy sufiks "*. file.core.windows.net" jest wyświetlany. Jeśli nie, kliknij przycisk "Odśwież"
    6.  Wybierz pozycję "*. file.core.windows.net", a następnie kliknij pozycję "Włącz" i "Zastosuj"

* <a id=""></a>
**Jakie regiony są dostępne do Azure Files AD DS uwierzytelniania?**

    Szczegółowe informacje znajdują się w [AD DS regionalnej dostępności](storage-files-identity-auth-active-directory-enable.md#regional-availability) .
    
* <a id="ad-aad-smb-afs"></a>
**Czy można korzystać z uwierzytelniania Azure Files Active Directory (AD) w udziałach plików zarządzanych przez Azure File Sync?**

    Tak, możesz włączyć uwierzytelnianie usługi AD w udziale plików zarządzanym przez usługę Azure File Sync. Zmiany w katalogu/plikach list ACL systemu plików NTFS na lokalnych serwerach plików będą należeć do Azure Files i na odwrót.

* <a id="ad-aad-smb-files"></a>
**Czy istnieje różnica w tworzeniu konta komputera lub konta logowania do usługi w celu reprezentowania konta magazynu w usłudze AD?**

    Utworzenie [konta komputera](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (domyślnego) lub [konta logowania do usługi](/windows/win32/ad/about-service-logon-accounts) nie ma żadnego wpływu na sposób działania uwierzytelniania Azure Files. Możesz wybrać własny wybór sposobu reprezentowania konta magazynu jako tożsamości w środowisku usługi AD. Domyślny DomainAccountType ustawiony w Join-AzStorageAccountForAuth polecenie cmdlet to konto komputera. Jednak okres ważności hasła skonfigurowany w środowisku usługi AD może być różny dla konta logowania komputera lub usługi i należy wziąć pod uwagę w przypadku [aktualizacji hasła tożsamości konta magazynu w usłudze AD](./storage-files-identity-ad-ds-update-password.md).
 
* <a id="ad-support-rest-apis"></a>
**Czy istnieją interfejsy API REST obsługujące pobieranie/Ustawianie/kopiowanie katalogów/list ACL systemu Windows?**

    Tak. Obsługujemy interfejsy API REST, które pobierają, ustawiają lub kopiują listy ACL systemu plików NTFS dla katalogów lub plików w przypadku korzystania z interfejsu API REST [2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (lub nowszego). Obsługujemy również utrwalanie list ACL systemu Windows w narzędziach opartych na platformie REST: [AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases).

* <a id="ad-support-rest-apis"></a>
**Jak usunąć buforowane poświadczenia z kluczem konta magazynu i usunąć istniejące połączenia SMB przed zainicjowaniem nowego połączenia z usługą Azure AD lub poświadczeniami usługi AD?**

    Aby usunąć zapisane poświadczenia skojarzone z kluczem konta magazynu i usunąć połączenie SMB, można wykonać poniższe dwa czynności: 
    1. Uruchom poniższe polecenie cmdlet w systemie Windows Cmd.exe, aby usunąć poświadczenie. Jeśli nie możesz znaleźć takiego poświadczenia, oznacza to, że nie zostało utrwalone poświadczenie i może pominąć ten krok.
    
       cmdkey/delete: domena: Target = Storage-account-name.file.core.windows.net
    
    2. Usuń istniejące połączenie z udziałem plików. Ścieżkę instalacji można określić jako literę dysku zainstalowanego lub ścieżkę storage-account-name.file.core.windows.net.
    
       net use <dysk-litera/udział-ścieżka>/Delete

## <a name="network-file-system"></a>Sieciowy system plików

* <a id="when-to-use-nfs"></a>
**Kiedy należy używać Azure Files NFS?**

    Zobacz [udziały NFS (wersja zapoznawcza)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Jak mogę dane kopii zapasowej przechowywane w udziałach NFS?**

    Tworzenie kopii zapasowych danych w udziałach NFS można organizować przy użyciu znanych narzędzi, takich jak rsync lub produkty z jednego z naszych partnerów tworzenia kopii zapasowych innych firm. Wielu partnerów kopii zapasowych, w tym [CommVault](https://documentation.commvault.com/commvault/v11/article?p=92634.htm), [Veeam](https://www.veeam.com/blog/?p=123438)i [Veritas](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) , były częścią naszego początkowej wersji zapoznawczej i przeprowadzono rozszerzone rozwiązania do pracy z protokołami SMB 3,0 i NFS 4,1 dla Azure Files.

* <a id="migrate-nfs-data"></a>
**Czy można migrować istniejące dane do udziału NFS?**

    W ramach regionu można przenieść dane za pomocą standardowych narzędzi, takich jak SCP, rsync lub SSHFS. Ponieważ do Azure Files NFS można uzyskać dostęp z wielu wystąpień obliczeniowych jednocześnie, można zwiększyć szybkość kopiowania przy użyciu równoległych operacji przekazywania. Jeśli chcesz przenieść dane spoza regionu, Użyj sieci VPN lub ExpressRoute do zainstalowania w systemie plików z lokalnego centrum danych.

## <a name="on-premises-access"></a>Dostęp lokalny

* <a id="port-445-blocked"></a>
**Mój usługodawca internetowy lub blokuje port 445, który kończy się niepowodzeniem Azure Files instalacji. Co mam zrobić?**

    Można dowiedzieć się więcej [na temat różnych sposobów obejścia zablokowanego portu 445 w tym miejscu](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked). Azure Files zezwala tylko na połączenia przy użyciu protokołu SMB 3,0 (z obsługą szyfrowania) spoza regionu lub centrum danych. Protokół SMB 3,0 wprowadził wiele funkcji zabezpieczeń, w tym szyfrowanie kanałów, które są bardzo bezpieczne do użycia w Internecie. Możliwe, że port 445 został zablokowany ze względu na historyczne przyczyny luk w zabezpieczeniach, które znajdują się w mniejszych wersjach protokołu SMB. W idealnym przypadku port powinien być blokowany tylko dla ruchu SMB 1,0, a protokół SMB 1,0 powinien być wyłączony na wszystkich klientach.

* <a id="expressroute-not-required"></a>
**Czy muszę używać usługi Azure ExpressRoute do nawiązywania połączenia z usługą Azure Files lub korzystania z Azure File Sync lokalnie?**  

    Nie. ExpressRoute nie jest wymagana w celu uzyskania dostępu do udziału plików platformy Azure. Jeśli instalujesz udział plików platformy Azure bezpośrednio lokalnie, wymagany jest tylko port 445 (ruch wychodzący TCP) otwarty na potrzeby dostępu do Internetu (jest to port wykorzystywany przez protokół SMB do komunikacji). Jeśli używasz Azure File Sync, wymagany jest tylko port 443 (ruch wychodzący TCP) dla dostępu HTTPS (brak wymaganego protokołu SMB). *Można* jednak używać ExpressRoute z jedną z tych opcji dostępu.

* <a id="mount-locally"></a>
**Jak zainstalować udział plików platformy Azure na komputerze lokalnym?**  

    Udział plików można zainstalować przy użyciu protokołu SMB, jeśli jest otwarty port 445 (ruch wychodzący TCP), a klient obsługuje protokół SMB 3,0 (na przykład jeśli używasz systemu Windows 10 lub Windows Server 2016). Jeśli port 445 jest blokowany przez zasady organizacji lub przez usługodawcę internetowego, możesz użyć Azure File Sync, aby uzyskać dostęp do udziału plików platformy Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Jak mogę utworzyć kopię zapasową mojego udziału plików platformy Azure?**  
    Można użyć okresowych [migawek udziałów](storage-snapshots-files.md) do ochrony przed przypadkowym usunięciem. Można również użyć narzędzia do tworzenia kopii zapasowych AzCopy, Robocopy lub innych firm, które umożliwia utworzenie kopii zapasowej zainstalowanego udziału plików. Azure Backup oferuje kopię zapasową Azure Files. Dowiedz się więcej [na temat tworzenia kopii zapasowych udziałów plików platformy Azure przez Azure Backup](../../backup/backup-afs.md).

## <a name="share-snapshots"></a>Migawki udziałów

### <a name="share-snapshots-general"></a>Migawki udziałów: ogólne
* <a id="what-are-snaphots"></a>
**Co to są migawki udziałów plików?**  
    Migawek udziałów plików platformy Azure można użyć do utworzenia wersji tylko do odczytu udziałów plików. Możesz również użyć Azure Files, aby skopiować wcześniejszą wersję zawartości z powrotem do tego samego udziału, do alternatywnej lokalizacji na platformie Azure lub lokalnie, aby uzyskać więcej modyfikacji. Aby dowiedzieć się więcej na temat migawek udziałów, zobacz [Omówienie udostępniania migawek](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Gdzie są przechowywane migawki udostępniania?**  
    Migawki udziałów są przechowywane na tym samym koncie magazynu co udział plików.

* <a id="snapshot-consistency"></a>
**Czy udział migawek jest spójny z aplikacjami?**  
    Nie, migawka udziałów nie jest spójna z aplikacją. Przed przeprowadzeniem migawki udziału użytkownik musi opróżnić zapisy z aplikacji do udziału.

* <a id="snapshot-limits"></a>
**Czy istnieją limity dotyczące liczby migawek udziałów, których można użyć?**  
    Tak. Azure Files może obsłużyć maksymalnie 200 migawek udziałów. Migawki udziałów nie są wliczane do limitu przydziału udziału, więc nie ma żadnego udziału na łączny obszar, który jest używany przez wszystkie migawki udziałów. Nadal obowiązują limity kont magazynu. W przypadku migawek udziałów 200 należy usunąć starsze migawki w celu utworzenia nowych migawek udziałów.

* <a id="snapshot-cost"></a>
**Ile kosztuje udział migawek?**  
    Do migawki są stosowane standardowe transakcje i standardowe opłaty za magazyn. Migawki mają charakter przyrostowy. Podstawową migawką jest sam udział. Wszystkie kolejne migawki są przyrostowe i będą przechowywać różnice z poprzedniej migawki. Oznacza to, że zmiany różnicowe, które będą widoczne na rachunku, będą minimalne, jeśli obciążenie obciążeń jest minimalne. Informacje o cenach standardowych Azure Files można znaleźć na [stronie z cennikiem](https://azure.microsoft.com/pricing/details/storage/files/) . Dzisiaj, aby zobaczyć rozmiar używany przez migawkę udziału, można porównać rozliczane zdolności produkcyjne z zużywaną pojemnością. Pracujemy nad narzędziami, aby usprawnić raportowanie.

* <a id="ntfs-acls-snaphsots"></a>
**Czy listy ACL systemu NTFS dotyczą katalogów i plików utrwalonych w migawkach udziałów?**  
    Listy ACL NTFS dotyczące katalogów i plików są utrwalane w migawkach udziałów.

### <a name="create-share-snapshots"></a>Tworzenie migawek udziałów
* <a id="file-snaphsots"></a>
**Czy mogę utworzyć migawkę udziału poszczególnych plików?**  
    Migawki udziałów są tworzone na poziomie udziału plików. Można przywrócić pojedyncze pliki z migawki udziału plików, ale nie można utworzyć migawek udziałów na poziomie pliku. Jeśli jednak masz migawkę udziału na poziomie udziału i chcesz wyświetlić migawki udziałów, w których określony plik został zmieniony, możesz to zrobić w **poprzednich wersjach** w udziale zainstalowanym w systemie Windows. 
    
    Jeśli potrzebujesz funkcji migawek plików, powiadom nas o [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Czy mogę utworzyć migawki udziałów dla zaszyfrowanego udziału plików?**  
    Możesz wykonać migawkę udziału udziałów plików platformy Azure z włączonym szyfrowaniem. Można przywrócić pliki z migawki udziału do zaszyfrowanego udziału plików. Jeśli udział jest szyfrowany, migawka udziału również jest zaszyfrowana.

* <a id="geo-redundant-snaphsots"></a>
**Czy moje migawki udostępniania są geograficznie nadmiarowe?**  
    Migawki udziałów mają taką samą nadmiarowość jak udział plików platformy Azure, dla którego zostały wykonane. W przypadku wybrania magazynu geograficznie nadmiarowego dla Twojego konta migawka udziału również jest przechowywana w sposób nadmiarowy w sparowanym regionie.

### <a name="manage-share-snapshots"></a>Zarządzanie migawkami udziałów
* <a id="browse-snapshots-linux"></a>
**Czy mogę przeglądać moje migawki udziałów z systemu Linux?**  
    Za pomocą interfejsu wiersza polecenia platformy Azure można tworzyć, wyświetlać, przeglądać i przywracać migawki udziałów w systemie Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Czy można skopiować migawki udziałów na inne konto magazynu?**  
    Można skopiować pliki z migawek udziałów do innej lokalizacji, ale nie można skopiować samych migawek udziałów.

### <a name="restore-data-from-share-snapshots"></a>Przywracanie danych z migawek udziałów
* <a id="promote-share-snapshot"></a>
**Czy mogę podnieść udział migawki do udziału podstawowego?**  
    Dane z migawki udziału można kopiować do innych miejsc docelowych. Nie można podwyższyć poziomu migawki udziału do udziału podstawowego.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Czy można przywrócić dane z migawki udostępniania na inne konto magazynu?**  
    Tak. Pliki z migawki udziału można skopiować do oryginalnej lokalizacji lub do alternatywnej lokalizacji, która zawiera to samo konto magazynu lub inne konto magazynu w tym samym regionie lub w różnych regionach. Możesz również kopiować pliki do lokalizacji lokalnej lub innej chmury.    
  
### <a name="clean-up-share-snapshots"></a>Czyszczenie migawek udziałów
* <a id="delete-share-keep-snapshots"></a>
**Czy mogę usunąć mój udział, ale nie usunąć moich migawek udostępniania?**  
    Jeśli masz aktywne migawki udziałów w udziale, nie możesz usunąć Twojego udziału. Możesz użyć interfejsu API, aby usunąć migawki udziałów wraz z udziałem. Można również usunąć zarówno migawki udziału, jak i udział w Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**Co się dzieje z moją migawką udziału w przypadku usunięcia konta magazynu?**  
    Usunięcie konta magazynu spowoduje również usunięcie migawek udziałów.

## <a name="billing-and-pricing"></a>Rozliczenia i Cennik
* <a id="vm-file-share-network-traffic"></a>
**Czy ruch sieciowy między maszyną wirtualną platformy Azure a udziałem plików platformy Azure jest liczony jako zewnętrzna przepustowość, która jest naliczana dla subskrypcji?**  
    Jeśli udział plików i maszyna wirtualna znajdują się w tym samym regionie świadczenia usługi Azure, nie ma dodatkowej opłaty za ruch między udziałem plików i maszyną wirtualną. Jeśli udział plików i maszyna wirtualna znajdują się w różnych regionach, ruch między nimi jest naliczany jako przepustowość zewnętrzna.

* <a id="share-snapshot-price"></a>
**Ile kosztuje udział migawek?**  
     W trakcie okresu zapoznawczego nie ma opłat za pojemność migawki udziału. Obowiązują standardowe dane wyjściowe magazynu i koszty transakcji. Po uzyskaniu ogólnej dostępności subskrypcje będą obciążane opłatami za zasoby i transakcje na migawek udziałów.
     
     Migawki udziałów mają charakter przyrostowy. Migawką udziału podstawowego jest sam udział. Wszystkie kolejne migawki udziałów są przyrostowe i przechowują tylko różnice z poprzedniej migawki udziału. Opłaty są naliczane tylko za zmienioną zawartość. Jeśli masz udział z 100 GiB danych, ale tylko 5 GiB uległo zmianie od czasu ostatniej migawki udziału, migawka udziału wykorzystuje tylko 5 dodatkowych GiB, a opłaty są naliczane za 105 GiB. Aby uzyskać więcej informacji na temat transakcji i standardowych opłat wychodzących, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skalowalność i wydajność
* <a id="files-scale-limits"></a>
**Jakie są limity skalowania Azure Files?**  
    Aby uzyskać informacje o skalowalności i wydajności dla Azure Files, zobacz [Azure Files cele dotyczące skalowalności i wydajności](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Jakie rozmiary są dostępne dla udziałów plików platformy Azure?**  
    Rozmiary udziałów plików platformy Azure (Premium i standard) można skalować w górę do 100 TiB. Zapoznaj się z sekcją dołączanie [do większej liczby udziałów plików (warstwa standardowa)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) przewodnika planowania dla instrukcji dołączania do większych udziałów plików dla warstwy Standardowa.

* <a id="lfs-performance-impact"></a>
**Czy zwiększenie przydziału udziału plików wpływa na moje obciążenia lub Azure File Sync?**
    
    Nie. Zwiększenie przydziału nie będzie miało wpływu na obciążenia lub Azure File Sync.

* <a id="open-handles-quota"></a>
**Ilu klientów może jednocześnie uzyskać dostęp do tego samego pliku?**   
    W pojedynczym pliku występuje limit przydziału liczby otwartych dojścia do 2 000. Jeśli masz otwarte dojścia do 2 000, zostanie wyświetlony komunikat o błędzie informujący o osiągnięciu limitu przydziału.

* <a id="zip-slow-performance"></a>
**Moja wydajność jest niska, gdy rozpakuję pliki w Azure Files. Co mam zrobić?**  
    Aby przenieść dużą liczbę plików do Azure Files, zalecamy użycie AzCopy (dla systemu Windows; w wersji zapoznawczej dla systemów Linux i UNIX) lub Azure PowerShell. Te narzędzia zostały zoptymalizowane pod kątem transferu sieciowego.

* <a id="slow-perf-windows-81-2012r2"></a>
**Dlaczego moja wydajność jest niska po zainstalowaniu udziału plików platformy Azure w systemie Windows Server 2012 R2 lub Windows 8.1?**  
    Występuje znany problem podczas instalowania udziału plików platformy Azure w systemie Windows Server 2012 R2 i Windows 8.1. Problem został poprawiony w aktualizacji zbiorczej 2014 kwietnia Windows 8.1 i Windows Server 2012 R2. W celu uzyskania optymalnej wydajności upewnij się, że dla wszystkich wystąpień systemu Windows Server 2012 R2 i Windows 8.1 zastosowano tę poprawkę. (Zawsze należy odbierać poprawki systemu Windows za Windows Update.) Aby uzyskać więcej informacji, zapoznaj się z artykułem dotyczącym powolnej wydajności programu Microsoft Knowledge Base w [przypadku uzyskiwania dostępu do Azure Files z Windows 8.1 lub Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkcje i współdziałanie z innymi usługami
* <a id="cluster-witness"></a>
**Czy mogę użyć mojego udziału plików platformy Azure jako *monitora udziału plików* dla mojego klastra trybu failover systemu Windows Server?**  
    Obecnie ta konfiguracja nie jest obsługiwana w przypadku udziału plików platformy Azure. Aby uzyskać więcej informacji na temat sposobu konfigurowania tego programu dla usługi Azure Blob Storage, zobacz [wdrażanie monitora chmury dla klastra trybu failover](/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Czy mogę zainstalować udział plików platformy Azure w ramach wystąpienia kontenera platformy Azure?**  
    Tak, udziały plików platformy Azure są dobrym rozwiązaniem, gdy chcesz utrwalać informacje poza okresem istnienia wystąpienia kontenera. Aby uzyskać więcej informacji, zobacz [Instalowanie udziału plików platformy Azure przy użyciu usługi Azure Container Instances](../../container-instances/container-instances-volume-azure-files.md).

* <a id="rest-rename"></a>
**Czy istnieje operacja zmiany nazwy w interfejsie API REST?**  
    Nie w tej chwili.

* <a id="nested-shares"></a>
**Czy można skonfigurować udziały zagnieżdżone? Innymi słowy, udział w udziale?**  
    Nie. Udział plików *jest* wirtualnym sterownikiem, który można zainstalować, więc udziały zagnieżdżone nie są obsługiwane.

* <a id="ibm-mq"></a>
**Jak mogę użyć Azure Files z IBM MQ?**  
    Firma IBM wydała dokument, który pomaga klientom firmy IBM MQ skonfigurować Azure Files z usługą firmy IBM. Aby uzyskać więcej informacji, zobacz [jak skonfigurować usługę zarządzania wieloma wystąpieniami programu IBM MQ z usługą Microsoft Azure Files](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Zobacz też
* [Rozwiązywanie problemów Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Rozwiązywanie problemów Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Rozwiązywanie problemów z usługą Azure File Sync](storage-sync-files-troubleshoot.md)
