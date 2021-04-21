---
title: Często zadawane pytania dotyczące Azure Files | Microsoft Docs
description: Uzyskaj odpowiedzi na Azure Files często zadawanych pytań. Udziały plików platformy Azure można zainstalować współbieżnie we wdrożeniach w chmurze lub lokalnych wdrożeniach systemu Windows, Linux lub macOS.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 4d7123aa22d95e3e4c3850be775ddad96f28d280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785311"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Często zadawane pytania dotyczące usługi Azure Files
[Azure Files](storage-files-introduction.md) udostępnia w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego protokołu bloku komunikatów serwera [(SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) i protokołu sieciowego systemu plików [(NFS)](https://en.wikipedia.org/wiki/Network_File_System) (wersja zapoznawcza). Udziały plików platformy Azure można zainstalować współbieżnie we wdrożeniach lokalnych lub w chmurze systemów Windows, Linux i macOS. Udziały plików platformy Azure można również buforować na maszynach z systemem Windows Server przy użyciu Azure File Sync, aby szybko uzyskać dostęp blisko miejsca, w którym są używane dane.

W tym artykule znajdują się odpowiedzi na często zadawane Azure Files dotyczące funkcji, w tym korzystania z Azure File Sync z Azure Files. Jeśli nie widzisz odpowiedzi na swoje pytanie, możesz skontaktować się z nami za pośrednictwem następujących kanałów (w kolejności eskalacji):

1. Sekcja komentarzy w tym artykule.
2. [Microsoft Q&Pytanie dotyczące usługi Azure Storage.](/answers/topics/azure-file-storage.html)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Pomoc techniczna Microsoft. Aby utworzyć nowy wniosek o pomoc techniczną,  w Azure Portal pomocy wybierz przycisk Pomoc **i obsługa techniczna,** a następnie wybierz pozycję **Nowy wniosek o pomoc techniczną.**

## <a name="general"></a>Ogólne
* <a id="why-files-useful"></a>
  **W jaki sposób Azure Files przydatne?**  
   Za pomocą Azure Files można tworzyć udziały plików w chmurze bez konieczności zarządzania obciążeniem serwera fizycznego, urządzenia lub urządzenia. Ta monotonna praca obejmuje m.in. stosowanie aktualizacji systemu operacyjnego i zastępowanie złych dysków. Aby dowiedzieć się więcej na temat scenariuszy, Azure Files mogą ci pomóc, zobacz [Dlaczego Azure Files jest przydatna.](storage-files-introduction.md#why-azure-files-is-useful)

* <a id="file-access-options"></a>
  **Jakie są różne sposoby uzyskiwania dostępu do plików w Azure Files?**  
    Udziały plików SMB można zainstalowane na komputerze lokalnym przy użyciu protokołu SMB 3.0 lub użyć narzędzi, takich jak [Eksplorator usługi Storage,](https://storageexplorer.com/) aby uzyskać dostęp do plików w udziałach plików. Udziały plików NFS można zainstalowane na komputerze lokalnym przez skopiowanie/wklejenie skryptu dostarczonego przez Azure Portal. Z aplikacji można uzyskać dostęp do plików w udziałach plików platformy Azure przy użyciu bibliotek klienta magazynu, interfejsów API REST, programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

* <a id="what-is-afs"></a>
  **Co to jest Azure File Sync?**  
    Za pomocą usługi Azure File Sync można scentralizować udziały plików organizacji w programie Azure Files przy zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Azure File Sync przekształca maszyny z systemem Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Możesz użyć dowolnego protokołu dostępnego w systemie Windows Server, aby uzyskać dostęp do danych lokalnie, w tym protokołu SMB, sieciowego systemu plików (NFS) i usługi protokół transferu plików Service (FTPS). Możesz mieć tyle pamięci podręcznych, ile potrzebujesz na całym świecie.

* <a id="files-versus-blobs"></a>
  **Dlaczego dla moich danych należy używać udziału plików platformy Azure, a usługi Azure Blob Storage?**  
    Azure Files usługę Azure Blob Storage oferują sposoby przechowywania dużych ilości danych w chmurze, ale są one przydatne do nieco innych celów. 
    
    Usługa Azure Blob Storage jest przydatna w przypadku aplikacji natywnych dla chmury o dużej skali, które muszą przechowywać dane bez struktury. Aby zmaksymalizować wydajność i skalę, usługa Azure Blob Storage jest prostszą abstrakcją magazynu niż prawdziwy system plików. Dostęp do usługi Azure Blob Storage można uzyskać tylko za pośrednictwem bibliotek klienckich opartych na protokole REST (lub bezpośrednio za pośrednictwem protokołu opartego na protokole REST).

    Azure Files jest w szczególności systemem plików. Azure Files zawiera wszystkie abstrakcje plików, które znasz i uwielbiasz od lat pracy z lokalnymi systemami operacyjnymi. Podobnie jak usługa Azure Blob Storage Azure Files interfejs REST i biblioteki klienckie oparte na interfejsie REST. W przeciwieństwie do usługi Azure Blob Storage Azure Files dostęp do udziałów plików platformy Azure za pomocą SMB lub NFS. Udziały plików mogą być instalowane bezpośrednio na lokalnych lub w chmurze maszyn wirtualnych z systemem Windows, Linux lub macOS bez konieczności pisania kodu ani dołączania specjalnych sterowników do systemu plików. Możesz również buforować udziały plików SMB platformy Azure na lokalnych serwerach plików przy użyciu Azure File Sync, aby uzyskać szybki dostęp blisko miejsca, w którym są używane dane. 
   
    Aby uzyskać bardziej szczegółowy opis różnic między usługami Azure Files i Azure Blob Storage, zobacz Introduction to the core Azure Storage services (Wprowadzenie do podstawowych usług [Azure Storage).](../common/storage-introduction.md) Aby dowiedzieć się więcej na temat usługi Azure Blob Storage, zobacz [Wprowadzenie do usługi Blob Storage.](../blobs/storage-blobs-introduction.md)

* <a id="files-versus-disks"></a>**Dlaczego warto używać udziału plików platformy Azure zamiast dysków platformy Azure?**  
    Dysk w usłudze Azure Disks jest po prostu dyskiem. Aby uzyskać wartość z usługi Azure Disks, musisz dołączyć dysk do maszyny wirtualnej działającej na platformie Azure. Dysków platformy Azure można używać dla wszystkich dysków używanych na serwerze lokalnym. Można go używać jako dysku systemu operacyjnego, jako miejsca wymiany dla systemu operacyjnego lub jako dedykowanego magazynu dla aplikacji. Interesującym zastosowaniem funkcji Dyski platformy Azure jest utworzenie serwera plików w chmurze do użycia w tych samych miejscach, w których można użyć udziału plików platformy Azure. Wdrażanie serwera plików w usłudze Azure Virtual Machines to wysokowydajny sposób na uzyskiwanie magazynu plików na platformie Azure, gdy wymagane są opcje wdrażania, które obecnie nie są obsługiwane przez Azure Files. 

    Jednak z kilku powodów uruchomienie serwera plików z usługą Azure Disks jako magazynem back-end jest znacznie droższe niż korzystanie z udziału plików platformy Azure. Po pierwsze, oprócz płacenia za magazyn dyskowy, musisz również płacić za wydatki związane z uruchamianiem co najmniej jednej maszyny wirtualnej platformy Azure. Po drugie należy również zarządzać maszynami wirtualnych używanymi do uruchamiania serwera plików. Na przykład użytkownik jest odpowiedzialny za uaktualnienia systemu operacyjnego. Jeśli ostatecznie wymagane jest buforowanie danych lokalnie, skonfigurowanie technologii replikacji, takich jak rozproszony system plików Replication (DFSR), należy do Ciebie.

    Jednym z podejść do uzyskania najlepszych rozwiązań zarówno z usługi Azure Files, jak i serwera plików hostowanej w usłudze Azure Virtual Machines (oprócz używania dysków platformy Azure jako magazynu back-end) jest zainstalowanie usługi Azure File Sync na serwerze plików hostowany na maszynie wirtualnej w chmurze. Jeśli udział plików platformy Azure znajduje się w tym samym regionie co serwer plików, możesz włączyć obsługę warstw w chmurze i ustawić maksymalną ilość wolnego miejsca (99%). Zapewnia to minimalną duplikację danych. Można również używać dowolnych aplikacji z serwerami plików, takich jak aplikacje, które wymagają obsługi protokołu NFS.

    Aby uzyskać informacje o opcji konfigurowania serwera plików o wysokiej wydajności i wysokiej dostępnej na platformie Azure, zobacz [Deploying IaaS VM guest clusters in Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)(Wdrażanie klastrów gościa maszyn wirtualnych IaaS w usłudze Microsoft Azure). Aby uzyskać bardziej szczegółowy opis różnic między usługami Azure Files i Azure Disks, zobacz Introduction to the core Azure Storage services (Wprowadzenie do podstawowych [usług Azure Storage).](../common/storage-introduction.md) Aby dowiedzieć się więcej na temat dysków platformy Azure, [zobacz Omówienie Dyski zarządzane Azure.](../../virtual-machines/managed-disks-overview.md)

* <a id="get-started"></a>
  **Jak mogę rozpocząć korzystanie z usługi Azure Files?**  
   Rozpoczęcie pracy z Azure Files jest łatwe. Najpierw utwórz udział [plików SMB](storage-how-to-create-file-share.md) lub jak utworzyć udział [NFS, a](storage-files-how-to-create-nfs-shares.md)następnie zainstaluj go w preferowanym systemie operacyjnym: 

  * [Zainstaluj udział SMB w systemie Windows](storage-how-to-use-files-windows.md)
  * [Zainstaluj udział SMB w systemie Linux](storage-how-to-use-files-linux.md)
  * [Zainstaluj udział SMB w systemie macOS](storage-how-to-use-files-mac.md)
  * [Zainstaluj udział plików NFS](storage-files-how-to-mount-nfs-shares.md)

    Aby uzyskać bardziej szczegółowe wskazówki dotyczące wdrażania udziału plików platformy Azure w celu zastąpienia produkcyjnych udziałów plików w organizacji, zobacz Planning for an Azure Files deployment (Planowanie wdrożenia [produkcyjnego).](storage-files-planning.md)

* <a id="redundancy-options"></a>
  **Jakie opcje nadmiarowości magazynu są obsługiwane przez Azure Files?**  
    Obecnie program Azure Files magazyn lokalnie nadmiarowy (LRS), magazyn strefowo nadmiarowy (ZRS), magazyn geograficznie nadmiarowy (GRS) i magazyn geograficznie i strefowo nadmiarowy (GZRS). Azure Files Premium obecnie obsługuje tylko usługi LRS i ZRS.

* <a id="tier-options"></a>
  **Jakie warstwy magazynowania są obsługiwane w Azure Files?**  
    Azure Files obsługuje dwie warstwy magazynowania: Premium i Standardowa. Udziały plików w standardowych wersjach są tworzone na kontach magazynu ogólnego przeznaczenia (GPv1 lub GPv2), a udziały plików w chmurze Premium są tworzone w ramach konta magazynu FileStorage. Dowiedz się więcej na temat tworzenia [standardowych udziałów plików i](storage-how-to-create-file-share.md) [udziałów plików Premium.](./storage-how-to-create-file-share.md) 
    
    > [!NOTE]
    > Nie można tworzyć udziałów plików platformy Azure z kont usługi Blob Storage ani kont magazynu *Ogólnego* przeznaczenia Premium (GPv1 lub GPv2). Udziały plików platformy  Azure w standardowych wersjach muszą być tworzone tylko na kontach ogólnego przeznaczenia w standardowych wersjach, a udziały plików platformy Azure w chmurze Premium muszą być tworzone tylko w ramach konta magazynu FileStorage. *Konta* magazynu ogólnego przeznaczenia (GPv1 i GPv2) w chmurze są tylko dla stronicowych obiektów blob w chmurze Premium. 

* <a id="file-locking"></a>
  **Czy Azure Files blokowanie plików?**  
    Tak, Azure Files obsługuje blokowanie plików w stylu SMB/Windows, [zobacz szczegóły](/rest/api/storageservices/managing-file-locks).

* <a id="give-us-feedback"></a>
  **Chcę zobaczyć konkretną funkcję dodaną do Azure Files. Czy możesz go dodać?**  
    Zespół Azure Files jest zainteresowany każdą opinią na temat naszej usługi. Zagłosuj na żądania funkcji na [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Z niecierpliwością czekamy na wiele nowych funkcji.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Jakie regiony są obsługiwane w Azure File Sync?**  
    Listę dostępnych regionów można znaleźć w sekcji [Dostępność regionów](../file-sync/file-sync-planning.md#azure-file-sync-region-availability) w przewodniku planowania Azure File Sync regionów. Będziemy stale dodawać obsługę dodatkowych regionów, w tym regionów niedostępnych.

* <a id="cross-domain-sync"></a>
  **Czy mogę mieć serwery przyłączone do domeny i nie przyłączone do domeny w tej samej grupie synchronizacji?**  
    Tak. Grupa synchronizacji może zawierać punkty końcowe serwera, które mają różne członkostwa w usłudze Active Directory, nawet jeśli nie są przyłączone do domeny. Mimo że ta konfiguracja działa technicznie, nie zaleca się tego jako typowej konfiguracji, ponieważ listy kontroli dostępu (ACL), które są zdefiniowane dla plików i folderów na jednym serwerze, mogą nie być wymuszane przez inne serwery w grupie synchronizacji. Aby uzyskać najlepsze wyniki, zaleca się synchronizację między serwerami, które znajdują się w tym samym lesie usługi Active Directory, między serwerami, które znajdują się w różnych lasach usługi Active Directory, ale które mają ustanowioną relację zaufania lub między serwerami, które nie znajdują się w domenie. Zaleca się unikanie używania kombinacji tych konfiguracji.

* <a id="afs-change-detection"></a>
  **Utworzono plik bezpośrednio w moim udziałach plików platformy Azure przy użyciu funkcji SMB lub portalu. Jak długo trwa synchronizowanie pliku z serwerami w grupie synchronizacji?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Jak długo trwa przekazywanie Azure File Sync 1TiB danych?**
  
    Wydajność zależy od ustawień środowiska, konfiguracji i od tego, czy jest to synchronizacja początkowa, czy bieżąca synchronizacja. Aby uzyskać więcej informacji, [zobacz Azure File Sync metryki wydajności](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Co się stanie, jeśli ten sam plik zostanie zmieniony na dwóch serwerach mniej więcej w tym samym czasie?**  
    Azure File Sync używa prostej strategii rozwiązywania konfliktów: zachować zarówno zmiany w plikach, które są zmieniane w dwóch punktach końcowych w tym samym czasie. Ostatnio napisana zmiana zachowuje oryginalną nazwę pliku. Starszy plik (określony przez LastWriteTime) ma nazwę punktu końcowego i numer konfliktu dołączony do nazwy pliku. W przypadku punktów końcowych serwera nazwa punktu końcowego jest nazwą serwera. W przypadku punktów końcowych w chmurze nazwa punktu końcowego to **Cloud**. Nazwa jest zgodna z następującą taksonomią: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Na przykład pierwszy konflikt CompanyReport.docx stanie się CompanyReport-CentralServer.docx centralserver, gdzie wystąpił starszy zapis. Drugi konflikt nosiłby nazwę CompanyReport-CentralServer-1.docx. Azure File Sync obsługuje 100 plików konfliktów na plik. Po osiągnięciu maksymalnej liczby plików konfliktów synchronizacja pliku nie powiedzie się, dopóki liczba plików konfliktów nie będzie mniejsza niż 100.

* <a id="afs-storage-redundancy"></a>
  **Czy magazyn geograficznie nadmiarowy jest obsługiwany Azure File Sync?**  
    Tak, Azure Files magazyn lokalnie nadmiarowy (LRS) i magazyn geograficznie nadmiarowy (GRS). Jeśli zainicjujesz tryb failover konta magazynu między sparowanych regionami z konta skonfigurowanego dla magazynu GRS, firma Microsoft zaleca traktowanie nowego regionu jako kopii zapasowej tylko danych. Azure File Sync nie rozpocznie się automatyczna synchronizacja z nowym regionem podstawowym. 

* <a id="sizeondisk-versus-size"></a>
  **Dlaczego właściwość *Rozmiar* dysku dla pliku nie jest dopasowana do właściwości *Size* po użyciu Azure File Sync?**  
  Zobacz Understand Azure File Sync cloud tiering (Informacje [o Azure File Sync w chmurze).](../file-sync/file-sync-cloud-tiering-overview.md#tiered-vs-locally-cached-file-behavior)

* <a id="is-my-file-tiered"></a>
  **Jak sprawdzić, czy plik został warstwowy?**  
  Zobacz Understanding Cloud Tiering (Opis [warstw w chmurze).](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-check-if-your-files-are-being-tiered)

* <a id="afs-recall-file"></a>**Plik, którego chcę użyć, został warstwowy. Jak przywołać plik na dysku, aby używać go lokalnie?**  
  Zobacz Understanding Cloud Tiering (Opis [warstw w chmurze).](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk)

* <a id="afs-force-tiering"></a>
  **Jak mogę wymusz, aby plik lub katalog był warstwowy?**  
  Zobacz Understanding Cloud Tiering (Opis [warstw w chmurze).](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-force-a-file-or-directory-to-be-tiered)

* <a id="afs-effective-vfs"></a>
  **Jak jest *interpretowane wolne* miejsce na woluminie, gdy na woluminie znajduje się wiele punktów końcowych serwera?**  
  Zobacz Understanding Cloud Tiering (Opis [warstw w chmurze).](../file-sync/file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume)
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Mam wyłączone warstwy w chmurze. Dlaczego w lokalizacji punktu końcowego serwera znajdują się pliki warstwowe?**  
    Istnieją dwie przyczyny, dla których pliki warstwowe mogą istnieć w lokalizacji punktu końcowego serwera:

    - Podczas dodawania nowego punktu końcowego serwera do istniejącej grupy synchronizacji, jeśli wybierzesz pierwszą opcję przestrzeni nazw odwołania lub opcję tylko odwołaj przestrzeń nazw dla początkowego trybu pobierania, pliki będą wyświetlane jako pliki warstwowe, dopóki nie zostaną pobrane lokalnie. Aby tego uniknąć, wybierz opcję Unikaj plików warstwowych dla początkowego trybu pobierania. Aby ręcznie odwołać pliki, użyj polecenia cmdlet [Invoke-StorageSyncFileRecall.](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk)

    - Jeśli w punkcie końcowym serwera włączono obsługę warstw w chmurze, a następnie wyłączono jej obsługę, pliki pozostaną warstwowe do czasu ich dostępu.

* <a id="afs-tiered-files-not-showing-thumbnails"></a>
  **Dlaczego moje pliki warstwowe nie pokazują miniatur ani podglądów w Eksplorator Windows?**  
    W przypadku plików warstwowych miniatury i podglądy nie będą widoczne w punkcie końcowym serwera. To zachowanie jest oczekiwane, ponieważ funkcja pamięci podręcznej miniatur w systemie Windows celowo pomija odczytywanie plików za pomocą atrybutu offline. Po włączeniu obsługi warstw w chmurze odczytywanie plików warstwowych może spowodować ich pobranie (przywołanie).

    To zachowanie nie jest specyficzne dla Azure File Sync, Eksplorator Windows wyświetla "szary znak X" dla wszystkich plików, które mają ustawiony atrybut offline. Ikona X będzie wyświetlana podczas uzyskiwania dostępu do plików za pośrednictwem SMB. Aby uzyskać szczegółowe wyjaśnienie tego zachowania, zobacz [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

    Aby uzyskać pytania dotyczące zarządzania plikami warstwami, zobacz [Jak zarządzać plikami warstw.](../file-sync/file-sync-how-to-manage-tiered-files.md)

* <a id="afs-files-excluded"></a>
  **Które pliki lub foldery są automatycznie wykluczane przez Azure File Sync?**  
  Zobacz [Pominięte pliki.](../file-sync/file-sync-planning.md#files-skipped)

* <a id="afs-os-support"></a>
  **Czy można używać Azure File Sync z systemem Windows Server 2008 R2, Linux lub urządzeniem magazynu dołączonym do sieci (NAS)?**  
    Obecnie program Azure File Sync tylko systemy Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2. W tej chwili nie mamy żadnych innych planów, które możemy udostępnić, ale jesteśmy otwarci na obsługę dodatkowych platform w zależności od zapotrzebowania klientów. Poświęć nam na [Azure Files UserVoice,](https://feedback.azure.com/forums/217298-storage/category/180670-files) które platformy chcesz obsługiwać.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Dlaczego pliki warstwowe istnieją poza przestrzenią nazw punktu końcowego serwera?**  
    Przed Azure File Sync 3 Azure File Sync plików warstwowych poza punkt końcowy serwera, ale na tym samym woluminie co punkt końcowy serwera. Nie ma to wpływu na operacje kopiowania, przeniesienie plików niewarstwowych i przeniesienie plików warstwowych do innych woluminów. Przyczyną tego zachowania było niejawne założenie, że Eksplorator plików i inne interfejsy API systemu Windows mają operacje przenoszenia na tym samym woluminie są (prawie) operacjami natychmiastowej zmiany nazwy. Oznacza to, że przenoszenie Eksplorator plików lub innych metod przenoszenia (takich jak wiersz polecenia lub program PowerShell) będzie wydawać się nieosiągalne podczas Azure File Sync wywołuje dane z chmury. Począwszy od Azure File Sync agenta w wersji [3.0.12.0,](../file-sync/file-sync-release-notes.md#supported-versions)program Azure File Sync umożliwia przeniesienie pliku warstwowego poza punkt końcowy serwera. Pozwalamy uniknąć negatywnych efektów, zezwalając na to, aby plik warstwowy istniał jako plik warstwowy poza punktem końcowym serwera, a następnie przywołując plik w tle. Oznacza to, że przenoszenie na tym samym woluminie jest natychmiastowe, a my pracujemy nad odwołaniem pliku na dysk po zakończeniu przenoszenia. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Mam problem z Azure File Sync na serwerze (synchronizacja, warstwy w chmurze itp.). Czy należy usunąć i ponownie utworzyć punkt końcowy serwera?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Czy mogę przenieść usługę synchronizacji magazynu i/lub konto magazynu do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD?**  
   Tak, usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów, subskrypcji lub dzierżawy usługi Azure AD. Po przeniesionej usłudze synchronizacji magazynu lub koncie magazynu należy udzielić aplikacji Microsoft.StorageSync dostępu do konta magazynu (zobacz Upewnij się, Azure File Sync ma dostęp do [konta magazynu).](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)

    > [!Note]  
    > Podczas tworzenia punktu końcowego w chmurze usługa synchronizacji magazynu i konto magazynu muszą znajdować się w tej samej dzierżawie usługi Azure AD. Po utworzeniu punktu końcowego w chmurze usługę synchronizacji magazynu i konto magazynu można przenieść do różnych dzierżaw usługi Azure AD.
    
* <a id="afs-ntfs-acls"></a>
  **Czy Azure File Sync acl ntfs na poziomie katalogu/pliku wraz z danymi przechowywanymi w Azure Files?**

    Od 24 lutego 2020 r. nowe i istniejące listy ACL z warstwą usługi Azure File Sync będą utrwalane w formacie NTFS, a modyfikacje list ACL wprowadzone bezpośrednio do udziału plików platformy Azure zostaną zsynchronizowane ze wszystkimi serwerami w grupie synchronizacji. Wszelkie zmiany dotyczące ACL wprowadzone w Azure Files zostaną zsynchronizowane za pośrednictwem usługi Azure File Sync. Podczas kopiowania danych do usługi Azure Files upewnij się, że używasz narzędzia do kopiowania, które obsługuje niezbędną "wierność" do kopiowania atrybutów, sygnatur czasowych i ACL do udziału plików platformy Azure — za pośrednictwem SMB lub REST. W przypadku korzystania z narzędzi do kopiowania platformy Azure, takich jak AzCopy, ważne jest, aby używać najnowszej wersji. Sprawdź [tabelę narzędzi do kopiowania](storage-files-migration-overview.md#file-copy-tools) plików, aby uzyskać omówienie narzędzi do kopiowania na platformie Azure, aby upewnić się, że możesz skopiować wszystkie ważne metadane pliku.

    Jeśli włączono obsługę Azure Backup w udziałach plików zarządzanych przez usługę File Sync, można nadal przywracać biblioteki ACL plików w ramach przepływu pracy przywracania kopii zapasowej. Działa to zarówno w przypadku całego udziału, jak i poszczególnych plików/katalogów.

    Jeśli używasz migawek w ramach rozwiązania do samodzielnego tworzenia kopii zapasowych dla udziałów plików zarządzanych przez synchronizację plików, Twoje adresy ACL mogą nie zostać prawidłowo przywrócone do ACL systemu plików NTFS, jeśli migawki zostały wykonane przed 24 lutego 2020 r. W takim przypadku należy rozważyć skontaktowanie się z pomoc techniczna platformy Azure.

* <a id="afs-lastwritetime"></a>
  **Czy Azure File Sync zsynchronizować czas LastWriteTime dla katalogów?**  
    Nie, Azure File Sync nie synchronizuje czasu LastWriteTime dla katalogów. Jest to celowe.
    
## <a name="security-authentication-and-access-control"></a>Zabezpieczenia, uwierzytelnianie i kontrola dostępu
* <a id="ad-support"></a>
**Czy uwierzytelnianie oparte na tożsamościach i kontrola dostępu są obsługiwane przez Azure Files?**  
    
    Tak, Azure Files uwierzytelnianie oparte na tożsamościach i kontrolę dostępu. Możesz wybrać jeden z dwóch sposobów korzystania z kontroli dostępu opartej na tożsamościach: lokalna usługa Active Directory Domain Services lub Azure Active Directory Domain Services (Azure AD DS). Usługa lokalna Active Directory Domain Services (AD DS) obsługuje uwierzytelnianie przy użyciu maszyn przyłącznych do domeny usługi AD DS, zarówno lokalnych, jak i na platformie Azure, w celu uzyskania dostępu do udziałów plików platformy Azure za pośrednictwem protokołu SMB. Azure AD DS za pośrednictwem protokołu SMB dla usługi Azure Files umożliwia maszynom Azure AD DS z systemem Windows przyłączone do domeny uzyskiwanie dostępu do udziałów, katalogów i plików przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, [zobacz Overview of Azure Files identity-based authentication support for SMB access (Omówienie](storage-files-active-directory-overview.md)obsługi uwierzytelniania opartego na tożsamościach dla dostępu za pomocą protokołu SMB). 

    Azure Files oferuje dwa dodatkowe sposoby zarządzania kontrolą dostępu:

    - Sygnatur dostępu współdzielonego (SAS) można używać do generowania tokenów z określonymi uprawnieniami, które są ważne przez określony przedział czasu. Na przykład można wygenerować token z dostępem tylko do odczytu do określonego pliku, który ma 10-minutowy czas wygaśnięcia. Każdy, kto posiada token, gdy token jest ważny, ma dostęp tylko do odczytu do tego pliku przez te 10 minut. Klucze sygnatury dostępu współdzielonych są obsługiwane tylko za pośrednictwem interfejsu API REST lub w bibliotekach klienckich. Udział plików platformy Azure należy zainstalować za pośrednictwem SMB przy użyciu kluczy konta magazynu.

    - Azure File Sync zachowuje i replikuje wszystkie dyskrecjalnie lub daCL (oparte na usłudze Active Directory lub lokalne) do wszystkich punktów końcowych serwera, z którym jest synchronizowana. 
    
    Pełną reprezentację wszystkich protokołów obsługiwanych w usługach Azure Storage można znaleźć w te tematach [Authorizing access to Azure Storage](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Autoryzowanie dostępu do usługi Azure Storage). 
    
* <a id="encryption-at-rest"></a>
**Jak mogę upewnić się, że mój udział plików platformy Azure jest szyfrowany w spoczynku?**  

    Tak. Aby uzyskać więcej informacji, [zobacz Azure szyfrowanie usługi Storage](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Jak mogę zapewnić dostęp do określonego pliku za pomocą przeglądarki internetowej?**  

    Sygnatur dostępu współdzielonych można używać do generowania tokenów z określonymi uprawnieniami, które są ważne przez określony przedział czasu. Na przykład można wygenerować token, który zapewnia dostęp tylko do odczytu do określonego pliku przez określony czas. Każda osoba mająca adres URL może uzyskać dostęp do pliku bezpośrednio z dowolnej przeglądarki internetowej, gdy token jest prawidłowy. Klucz sygnatury dostępu współdzielowego można łatwo wygenerować z interfejsu użytkownika, takiego Eksplorator usługi Storage.

* <a id="file-level-permissions"></a>
**Czy można określić uprawnienia tylko do odczytu lub tylko do zapisu w folderach w ramach udziału?**  

    Jeśli zainstalujemy udział plików przy użyciu funkcji SMB, nie będziecie mieć kontroli uprawnień na poziomie folderu. Jeśli jednak tworzysz sygnaturę dostępu współdzielonych przy użyciu interfejsu API REST lub bibliotek klienckich, możesz określić uprawnienia tylko do odczytu lub tylko do zapisu w folderach w ramach udziału.

* <a id="ip-restrictions"></a>
**Czy mogę zaimplementować ograniczenia adresów IP dla udziału plików platformy Azure?**  

    Tak. Dostęp do udziału plików platformy Azure można ograniczyć na poziomie konta magazynu. Aby uzyskać więcej informacji, zobacz Konfigurowanie zapór i sieci wirtualnych [usługi Azure Storage.](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

* <a id="data-compliance-policies"></a>
**Jakie zasady zgodności danych Azure Files obsługuje?**  

   Azure Files działa na tej samej architekturze magazynu, która jest używana w innych usługach magazynu w usłudze Azure Storage. Azure Files stosuje te same zasady zgodności danych, które są używane w innych usługach Azure Storage. Aby uzyskać więcej informacji na temat zgodności danych usługi Azure Storage, zapoznaj się z ofertą [rozwiązań do](../common/storage-compliance-offerings.md)obsługi zgodności usługi Azure Storage i przejdź do Centrum zaufania Firmy [Microsoft.](https://microsoft.com/trustcenter/default.aspx)

* <a id="file-auditing"></a>
**Jak mogę sprawdzić dostęp do plików i zmiany w Azure Files?**

  Dostępne są dwie opcje, które zapewniają funkcje inspekcji dla Azure Files:
  - Jeśli użytkownicy bezpośrednio uzyskuje dostęp do udziału plików platformy Azure, dzienniki usługi [Azure Storage (wersja zapoznawcza)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) mogą służyć do śledzenia zmian plików i dostępu użytkowników. Tych dzienników można używać do rozwiązywania problemów, a żądania są rejestrowane na zasadzie starań.
  - Jeśli użytkownicy mają dostęp do udziału plików platformy Azure za pośrednictwem systemu [](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) Windows Server z zainstalowanym agentem usługi Azure File Sync, należy użyć zasad inspekcji lub produktu innej firmy do śledzenia zmian plików i dostępu użytkowników w systemie Windows Server. 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS & Azure AD DS uwierzytelniania
* <a id="ad-support-devices"></a>
**Czy Azure Files Azure Active Directory Domain Services (Azure AD DS) obsługuje dostęp za pomocą protokołu SMB przy użyciu poświadczeń usługi Azure AD z urządzeń przyłączone do usługi Azure AD lub zarejestrowane w niej?**

    Nie, ten scenariusz nie jest obsługiwany.

* <a id="ad-vm-subscription"></a>
**Czy mogę uzyskać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD z maszyny wirtualnej w ramach innej subskrypcji?**

    Jeśli subskrypcja, w ramach której jest wdrożony udział plików, jest skojarzona z tą samą dzierżawą usługi Azure AD co wdrożenie usługi Azure AD DS, do którego maszyna wirtualna jest przyłączona do domeny, możesz uzyskać dostęp do udziałów plików platformy Azure przy użyciu tych samych poświadczeń usługi Azure AD. Ograniczenie nie jest nakładane na subskrypcję, ale na skojarzoną dzierżawę usługi Azure AD.
    
* <a id="ad-support-subscription"></a>
**Czy mogę włączyć Azure AD DS lokalnego lub lokalnego uwierzytelniania AD DS udziałów plików platformy Azure przy użyciu dzierżawy usługi Azure AD, która różni się od dzierżawy głównej udziału plików platformy Azure?**

    Nie, Azure Files obsługuje tylko Azure AD DS lub lokalną integrację AD DS z dzierżawą usługi Azure AD, która znajduje się w tej samej subskrypcji co udział plików. Z dzierżawą usługi Azure AD można skojarzyć tylko jedną subskrypcję. To ograniczenie dotyczy zarówno Azure AD DS, jak i lokalnych metod AD DS uwierzytelniania. W przypadku uwierzytelniania przy użyciu AD DS lokalnego poświadczenia AD DS muszą być zsynchronizowane z usługą [Azure AD,](../../active-directory/hybrid/how-to-connect-install-roadmap.md) z którym jest skojarzone konto magazynu.

* <a id="ad-linux-vms"></a>
**Czy Azure AD DS lub lokalne uwierzytelnianie AD DS udziałów plików platformy Azure obsługuje maszyny wirtualne z systemem Linux?**

    Nie, uwierzytelnianie z maszyn wirtualnych z systemem Linux nie jest obsługiwane.

* <a id="ad-aad-smb-afs"></a>
**Czy udziały plików zarządzane przez Azure File Sync obsługują Azure AD DS lub lokalne AD DS uwierzytelniania?**

    Tak, możesz włączyć Azure AD DS lub lokalne uwierzytelnianie AD DS w udziału plików zarządzanym przez usługę Azure File Sync. Zmiany w katalogach/plikach ACL NTFS na lokalnych serwerach plików będą warstwowe Azure Files i odwrotnie.

* <a id="ad-aad-smb-files"></a>
**Jak sprawdzić, czy włączono uwierzytelnianie AD DS na moim koncie magazynu i pobrać informacje o domenie?**

    Aby uzyskać instrukcje, zobacz [tutaj](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled).

* <a id=""></a>
**Czy Azure Files uwierzytelnianie usługi Azure AD obsługuje maszyny wirtualne z systemem Linux?**

    Nie, uwierzytelnianie z maszyn wirtualnych z systemem Linux nie jest obsługiwane.

* <a id="ad-multiple-forest"></a>
**Czy lokalne uwierzytelnianie AD DS udziałów plików platformy Azure obsługuje integrację ze środowiskiem AD DS przy użyciu wielu lasów?**    

    Azure Files lokalne uwierzytelnianie AD DS integruje się tylko z lasem usługi domeny, w których zarejestrowano konto magazynu. Aby obsługiwać uwierzytelnianie z innego lasu, środowisko musi mieć poprawnie skonfigurowane zaufanie lasu. Sposób Azure Files w usłudze AD DS prawie taki sam jak zwykły serwer plików, w którym tworzy tożsamość (konto logowania komputera lub usługi) w usłudze AD DS uwierzytelniania. Jedyna różnica polega na tym, że zarejestrowana nazwa SPN konta magazynu kończy się literą "file.core.windows.net", co nie jest zgodne z sufiksem domeny. Skontaktuj się z administratorem domeny, aby sprawdzić, czy wymagana jest jakakolwiek aktualizacja zasad routingu sufiksów, aby włączyć uwierzytelnianie wielu lasów ze względu na inny sufiks domeny. Poniżej przedstawiono przykład konfigurowania zasad routingu sufiksów.
    
    Przykład: Gdy użytkownicy w lesie domena chce uzyskać dostęp do udziału plików przy użyciu konta magazynu zarejestrowanego w domenie w lesie B, nie będzie to automatycznie działać, ponieważ jednostka usługi konta magazynu nie ma sufiksu pasującego do sufiksu żadnej domeny w lesie A. Możemy rozwiązać ten problem, ręcznie konfigurując regułę routingu sufiksu z lasu A do lasu B dla niestandardowego sufiksu "file.core.windows.net".
    Najpierw należy dodać nowy sufiks niestandardowy w lesie B. Upewnij się, że masz odpowiednie uprawnienia administracyjne do zmiany konfiguracji, a następnie wykonaj następujące kroki:   
    1. Logowanie do domeny komputera przyłączone do lasu B
    2.  Otwórz konsolę "Active Directory Domains and Trusts" (Domeny i relacje zaufania usługi Active Directory)
    3.  Kliknij prawym przyciskiem myszy pozycję "Active Directory Domains and Trusts" (Domeny i relacje zaufania usługi Active Directory)
    4.  Kliknij pozycję "Właściwości"
    5.  Kliknij pozycję "Dodaj"
    6.  Dodaj "file.core.windows.net" jako sufiksy nazwy UPN
    7.  Kliknij pozycję "Zastosuj", a następnie przycisk "OK", aby zamknąć kreatora
    
    Następnie dodaj regułę routingu sufiksu w lesie A, aby przekierowywano ją do lasu B.
    1.  Logowanie do domeny komputera przyłączone do lasu A
    2.  Otwórz konsolę "Active Directory Domains and Trusts" (Domeny i relacje zaufania usługi Active Directory)
    3.  Kliknij prawym przyciskiem myszy domenę, do której chcesz uzyskać dostęp do udziału plików, a następnie kliknij kartę "Relacje zaufania" i wybierz domenę lasu B z wychodzących relacji zaufania. Jeśli nie skonfigurowano relacji zaufania między dwoma lasami, należy najpierw skonfigurować relację zaufania
    4.  Kliknij pozycję "Właściwości..." następnie "Routing sufiksów nazw"
    5.  Sprawdź, czy jest file.core.windows.net sufiks "*.file.core.windows.net". Jeśli nie, kliknij pozycję "Odśwież"
    6.  Wybierz pozycję "*.file.core.windows.net", a następnie kliknij pozycję "Włącz" i "Zastosuj"

* <a id=""></a>
**Jakie regiony są dostępne na Azure Files AD DS uwierzytelniania?**

    Aby uzyskać szczegółowe [AD DS, zapoznaj się z AD DS dostępnością](storage-files-identity-auth-active-directory-enable.md#regional-availability) regionalną.
    
* <a id="ad-aad-smb-afs"></a>
**Czy mogę korzystać Azure Files uwierzytelniania usługi Active Directory (AD) w udziałach plików zarządzanych przez usługę Azure File Sync?**

    Tak, można włączyć uwierzytelnianie usługi AD w udziałach plików zarządzanych przez usługę Azure File Sync. Zmiany w katalogach/plikach ACL systemu plików NTFS na lokalnych serwerach plików będą warstwowe Azure Files i odwrotnie.

* <a id="ad-aad-smb-files"></a>
**Czy istnieje różnica w tworzeniu konta komputera lub konta logowania do usługi do reprezentowania mojego konta magazynu w usłudze AD?**

    Utworzenie konta [komputera (domyślnego)](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) lub [konta](/windows/win32/ad/about-service-logon-accounts) logowania do usługi nie ma żadnej różnicy w sposobu, w jaki uwierzytelnianie będzie działać Azure Files. Możesz wybrać sposób reprezentowania konta magazynu jako tożsamości w środowisku usługi AD. Domyślny typ DomainAccountType ustawiony w Join-AzStorageAccountForAuth cmdlet to konto komputera. Jednak wiek wygaśnięcia hasła skonfigurowany w środowisku usługi AD może się różnić w przypadku konta logowania do komputera lub usługi. Należy to wziąć pod uwagę w przypadku aktualizacji hasła tożsamości konta magazynu w usłudze [AD.](./storage-files-identity-ad-ds-update-password.md)
 
* <a id="ad-support-rest-apis"></a>
**Czy istnieją interfejsy API REST do obsługi get/set/copy katalogu/pliku ACL systemu Windows?**

    Tak, obsługujemy interfejsy API REST, które mogą pobrać, ustawić lub skopiować adresy ACL systemu plików NTFS dla katalogów lub plików w przypadku korzystania z interfejsu API REST [2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (lub nowszego). Obsługujemy również utrwalanielsów ACL systemu Windows w narzędziach opartych na rest: [AzCopy w wersji 10.4+](https://github.com/Azure/azure-storage-azcopy/releases).

* <a id="ad-support-rest-apis"></a>
**Jak usunąć buforowane poświadczenia z kluczem konta magazynu i usunąć istniejące połączenia SMB przed zainicjowaniem nowego połączenia przy użyciu poświadczeń usługi Azure AD lub AD?**

    Możesz wykonać dwa kroki poniższego procesu, aby usunąć zapisane poświadczenia skojarzone z kluczem konta magazynu i usunąć połączenie SMB: 
    1. Uruchom poniższe polecenie cmdlet w Cmd.exe Windows, aby usunąć poświadczenia. Jeśli nie możesz go znaleźć, oznacza to, że poświadczenia nie zostały utrwalone i możesz pominąć ten krok.
    
       cmdkey /delete:Domain:target=storage-account-name.file.core.windows.net
    
    2. Usuń istniejące połączenie z udziałem plików. Ścieżkę instalacji można określić jako literę zainstalowanego dysku lub ścieżkę storage-account-name.file.core.windows.net instalacji.
    
       net use <drive-letter/share-path> /delete

## <a name="network-file-system"></a>Sieciowy system plików

* <a id="when-to-use-nfs"></a>
**Kiedy należy używać systemu Azure Files NFS?**

    Zobacz [Udziały NFS (wersja zapoznawcza)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Jak mogę kopii zapasowej danych przechowywanych w udziałach NFS?**

    Tworzenie kopii zapasowych danych w udziałach NFS można aranżować przy użyciu znanych narzędzi, takich jak rsync, lub produktów jednego z naszych partnerów kopii zapasowych innych firm. Wielu partnerów kopii zapasowych, w tym [Commvault,](https://documentation.commvault.com/commvault/v11/article?p=92634.htm) [Firm](https://www.veeam.com/blog/?p=123438)i [Veritas,](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) było częścią naszej początkowej wersji zapoznawczej i rozszerzyło swoje rozwiązania do pracy z zarówno SMB 3.0, jak i NFS 4.1 na Azure Files.

* <a id="migrate-nfs-data"></a>
**Czy można migrować istniejące dane do udziału NFS?**

    W obrębie regionu można przenosić dane przy użyciu standardowych narzędzi, takich jak scp, rsync lub SSHFS. Ponieważ Azure Files NFS jest dostępny z wielu wystąpień obliczeniowych jednocześnie, można zwiększyć szybkość kopiowania przy użyciu przekazywania równoległego. Jeśli chcesz dostarczać dane spoza regionu, użyj sieci VPN lub usługi ExpressRoute, aby zainstalować dane w systemie plików z lokalnego centrum danych.

## <a name="on-premises-access"></a>Dostęp lokalny

* <a id="port-445-blocked"></a>
**Mój isp or IT blocks Port 445 which is failing Azure Files mount. Co mam zrobić?**

    Informacje o różnych [sposobach obejścia zablokowanego portu 445](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)można znaleźć tutaj. Azure Files zezwala na połączenia tylko przy użyciu SMB 3.0 (z obsługą szyfrowania) spoza regionu lub centrum danych. Protokół SMB 3.0 wprowadził wiele funkcji zabezpieczeń, w tym szyfrowanie kanału, które jest bardzo bezpieczne w użyciu przez Internet. Jednak możliwe, że port 445 został zablokowany z powodu historycznych przyczyn luk w zabezpieczeniach znalezionych w niższych wersjach SMB. W idealnym przypadku port powinien być blokowany tylko dla ruchu SMB 1.0, a port SMB 1.0 powinien być wyłączony na wszystkich klientach.

* <a id="expressroute-not-required"></a>
**Czy muszę używać Azure ExpressRoute do nawiązywania połączenia z Azure Files lub używania Azure File Sync lokalnie?**  

    Nie. Usługa ExpressRoute nie jest wymagana do uzyskiwania dostępu do udziału plików platformy Azure. W przypadku instalowanie udziału plików platformy Azure bezpośrednio w środowisku lokalnym wymagane jest tylko otwarcie portu 445 (ruch wychodzący TCP) na potrzeby dostępu do Internetu (jest to port używany przez protokół SMB do komunikacji). Jeśli używasz usługi Azure File Sync, wymagany jest tylko port 443 (ruch wychodzący TCP) dla dostępu za pośrednictwem protokołu HTTPS (nie jest wymagany protokół SMB). Można jednak użyć *expressRoute* z jedną z tych opcji dostępu.

* <a id="mount-locally"></a>
**Jak zainstalować udział plików platformy Azure na komputerze lokalnym?**  

    Udział plików można zainstalować przy użyciu protokołu SMB, jeśli port 445 (ruch wychodzący TCP) jest otwarty, a klient obsługuje protokół SMB 3.0 (na przykład w przypadku korzystania z systemu Windows 10 lub Windows Server 2016). Jeśli port 445 jest blokowany przez zasady organizacji lub przez twojego isp, możesz użyć Azure File Sync, aby uzyskać dostęp do udziału plików platformy Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Jak mogę kopii zapasowej udziału plików platformy Azure?**  
    Okresowych [migawek udziałów można używać](storage-snapshots-files.md) w celu ochrony przed przypadkowym usunięciem. Można również użyć narzędzia AzCopy, narzędzia Robocopy lub narzędzia do tworzenia kopii zapasowych innej firmy, które może tworzyć kopie zapasowe zainstalowanego udziału plików. Azure Backup kopie zapasowe Azure Files. Dowiedz się więcej na [temat kopii zapasowej udziałów plików platformy Azure, Azure Backup](../../backup/backup-afs.md).

## <a name="share-snapshots"></a>Migawki udziałów

### <a name="share-snapshots-general"></a>Migawki udziałów: Ogólne
* <a id="what-are-snaphots"></a>
**Co to są migawki udziałów plików?**  
    Migawki udziałów plików platformy Azure mogą być wykorzystywane do tworzenia wersji udziałów plików tylko do odczytu. Możesz również użyć Azure Files, aby skopiować wcześniejszą wersję zawartości z powrotem do tego samego udziału, do alternatywnej lokalizacji na platformie Azure lub do lokalizacji lokalnej w celu większej liczby modyfikacji. Aby dowiedzieć się więcej na temat migawek udziałów, zobacz [Omówienie udostępniania migawek.](storage-snapshots-files.md)

* <a id="where-are-snapshots-stored"></a>
**Gdzie są przechowywane migawki udziałów?**  
    Migawki udziałów są przechowywane na tym samym koncie magazynu co udział plików.

* <a id="snapshot-consistency"></a>
**Czy migawki udziałów są spójne z aplikacją?**  
    Nie, migawki udziałów nie są spójne z aplikacją. Użytkownik musi opróżnić zapis z aplikacji do udziału przed rozpoczęciem tworzenia migawki udziału.

* <a id="snapshot-limits"></a>
**Czy istnieją ograniczenia liczby migawek udziałów, których mogę użyć?**  
    Tak. Azure Files przechowywać maksymalnie 200 migawek udziałów. Migawki udziałów nie są wliczane do limitu przydziału udziałów, dlatego nie ma żadnego limitu całkowitego miejsca używanego przez wszystkie migawki udziałów. Nadal obowiązują limity konta magazynu. Po 200 migawkach udziałów należy usunąć starsze migawki, aby utworzyć nowe migawki udziałów.

* <a id="snapshot-cost"></a>
**Ile kosztuje udostępnianie migawek?**  
    W przypadku migawek będą stosowane standardowe transakcje i koszt magazynu w standardowych magazynach. Migawki mają charakter przyrostowy. Migawka podstawowa to sam udział. Wszystkie kolejne migawki są przyrostowe i będą przechowywać tylko różnice w poprzednich migawkach. Oznacza to, że zmiany różnicowe, które będą widoczne na rachunku, będą minimalne, jeśli wartość współczynnika zmian obciążenia jest minimalna. Zobacz [stronę cennika,](https://azure.microsoft.com/pricing/details/storage/files/) aby uzyskać Azure Files cennika. Obecnie sposobem na przyjrzenie się rozmiarowi użytej przez migawkę udziału jest porównanie rozliczanej pojemności z używaną pojemnością. Pracujemy nad narzędziami usprawniających raportowanie.

* <a id="ntfs-acls-snaphsots"></a>
**Czy w migawkach udziałów są utrwalane adresy ACL systemu plików NTFS w katalogach i plikach?**  
    ACL systemu plików NTFS w katalogach i plikach są utrwalane w migawkach udziałów.

### <a name="create-share-snapshots"></a>Tworzenie migawek udziałów
* <a id="file-snaphsots"></a>
**Czy mogę utworzyć migawkę udziału pojedynczych plików?**  
    Migawki udziałów są tworzone na poziomie udziału plików. Można przywrócić poszczególne pliki z migawki udziału plików, ale nie można tworzyć migawek udziałów na poziomie plików. Jeśli jednak masz migawkę udziału na poziomie udziału i chcesz wyświetlić listę migawek udziałów,  w których określony plik uległ zmianie, możesz to zrobić w obszarze Poprzednie wersje udziału zainstalowanego w systemie Windows. 
    
    Jeśli potrzebujesz funkcji migawki pliku, daj nam znać na Azure Files [UserVoice.](https://feedback.azure.com/forums/217298-storage/category/180670-files)

* <a id="encrypted-snapshots"></a>
**Czy można tworzyć migawki udziałów zaszyfrowanego udziału plików?**  
    Możesz utworzyć migawkę udziałów plików platformy Azure z włączonym szyfrowaniem w spoczynku. Pliki z migawki udziału można przywrócić do zaszyfrowanego udziału plików. Jeśli udział jest zaszyfrowany, migawka udziału również jest zaszyfrowana.

* <a id="geo-redundant-snaphsots"></a>
**Czy migawki udziałów są geograficznie nadmiarowe?**  
    Migawki udziałów mają taką samą nadmiarowość jak udział plików platformy Azure, dla którego zostały wykonane. Jeśli wybrano magazyn geograficznie nadmiarowy dla konta, migawka udziału jest również przechowywana nadmiarowo w sparowanych regionach.

### <a name="manage-share-snapshots"></a>Zarządzanie migawkami udziałów
* <a id="browse-snapshots-linux"></a>
**Czy mogę przeglądać migawki udziałów z systemu Linux?**  
    Za pomocą interfejsu wiersza polecenia platformy Azure można tworzyć, przeglądać i przywracać migawki udziałów w systemie Linux oraz przeglądać je.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Czy mogę skopiować migawki udziałów na inne konto magazynu?**  
    Pliki z migawek udziałów można kopiować do innej lokalizacji, ale nie można kopiować samych migawek udziałów.

### <a name="restore-data-from-share-snapshots"></a>Przywracanie danych z migawek udziałów
* <a id="promote-share-snapshot"></a>
**Czy mogę promować migawkę udziału do udziału podstawowego?**  
    Dane z migawki udziału można skopiować do dowolnego innego miejsca docelowego. Nie można promować migawki udziału do udziału podstawowego.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Czy mogę przywrócić dane z migawki udziału na inne konto magazynu?**  
    Tak. Pliki z migawki udziału można skopiować do oryginalnej lokalizacji lub do lokalizacji alternatywnej, która obejmuje to samo konto magazynu lub inne konto magazynu w tym samym regionie lub w różnych regionach. Pliki można również kopiować do lokalizacji lokalnej lub do dowolnej innej chmury.    
  
### <a name="clean-up-share-snapshots"></a>Oczyszczanie migawek udziałów
* <a id="delete-share-keep-snapshots"></a>
**Czy mogę usunąć swój udział, ale nie usunąć migawek udziałów?**  
    Jeśli masz aktywne migawki udziałów w swoim udziału, nie możesz usunąć udziału. Za pomocą interfejsu API można usuwać migawki udziałów wraz z tym udziałem. Możesz również usunąć zarówno migawki udziału, jak i udział w Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**Co się stanie z migawkami udziałów w przypadku usunięcia konta magazynu?**  
    Jeśli usuniesz konto magazynu, migawki udziałów również zostaną usunięte.

## <a name="billing-and-pricing"></a>Rozliczenia i cennik
* <a id="vm-file-share-network-traffic"></a>
**Czy ruch sieciowy między maszyną wirtualną platformy Azure i udziałem plików platformy Azure jest liczony jako zewnętrzna przepustowość naliczana w ramach subskrypcji?**  
    Jeśli udział plików i maszyna wirtualna znajdują się w tym samym regionie świadczenia usługi Azure, nie ma dodatkowych opłat za ruch między udziałem plików a maszyną wirtualną. Jeśli udział plików i maszyna wirtualna znajdują się w różnych regionach, opłaty za ruch między nimi są naliczane jako przepustowość zewnętrzna.

* <a id="share-snapshot-price"></a>
**Ile kosztują migawki udziałów?**  
     W okresie zapoznawczym nie są naliczane opłaty za pojemność migawki udziału. Obowiązują standardowe koszty wychodzące i transakcji magazynu. Po ogólnym udostępnieniu opłaty za pojemność i transakcje dotyczące migawek udziałów będą naliczane za subskrypcje.
     
     Migawki udziałów mają charakter przyrostowy. Migawka udziału podstawowego to sam udział. Wszystkie kolejne migawki udziałów są przyrostowe i przechowują tylko różnicę względem poprzedniej migawki udziału. Naliczane są tylko faktury za zmienioną zawartość. Jeśli masz udział ze 100 GiB danych, ale od ostatniej migawki udziału zmienił się tylko 5 GiB, migawka udziału zużywa tylko 5 dodatkowych GiB, a opłaty są naliczane za 105 GiB. Aby uzyskać więcej informacji na temat transakcji i standardowych opłat za ruch wychodzący, zobacz [stronę Cennik.](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="scale-and-performance"></a>Skalowalność i wydajność
* <a id="files-scale-limits"></a>
**Jakie są limity skalowania Azure Files?**  
    Aby uzyskać informacje o celach dotyczących skalowalności i wydajności dla Azure Files, [zobacz Azure Files cele dotyczące skalowalności i wydajności.](storage-files-scale-targets.md)

* <a id="need-larger-share"></a>
**Jakie rozmiary są dostępne dla udziałów plików platformy Azure?**  
    Rozmiary udziałów plików platformy Azure (Premium i Standardowa) można skalować do 100 TiB. Zapoznaj się z sekcją Dołączanie do większych udziałów plików [(warstwa Standardowa)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) przewodnika planowania, aby uzyskać instrukcje dołączania do większych udziałów plików w warstwie Standardowa.

* <a id="lfs-performance-impact"></a>
**Czy rozszerzenie limitu przydziału udziału plików ma wpływ na moje obciążenia lub Azure File Sync?**
    
    Nie. Rozszerzenie limitu przydziału nie będzie miało wpływu na obciążenia ani Azure File Sync.

* <a id="open-handles-quota"></a>
**Ilu klientów może jednocześnie uzyskać dostęp do tego samego pliku?**   
    Limit przydziału pojedynczego pliku wynosi 2000 otwartych dojść. Jeśli masz 2000 otwartych dojść, jest wyświetlany komunikat o błędzie informujący o osiągnięciu limitu przydziału.

* <a id="zip-slow-performance"></a>
**Moja wydajność jest niska, gdy rozpakować pliki w Azure Files. Co mam zrobić?**  
    Aby przesłać dużą liczbę plików do programu Azure Files, zaleca się użycie programu AzCopy (dla systemu Windows w wersji zapoznawczej dla systemów Linux i UNIX) lub programu Azure PowerShell. Te narzędzia zostały zoptymalizowane pod kątem transferu sieciowego.

* <a id="slow-perf-windows-81-2012r2"></a>
**Dlaczego moja wydajność jest niska po instalacji udziału plików platformy Azure w systemie Windows Server 2012 R2 lub Windows 8.1?**  
    Istnieje znany problem podczas instalowanie udziału plików platformy Azure w systemach Windows Server 2012 R2 i Windows 8.1. Ten problem został poprawiony w aktualizacji zbiorczej z kwietnia 2014 r. dla systemów Windows 8.1 i Windows Server 2012 R2. Aby uzyskać optymalną wydajność, upewnij się, że ta poprawka została zastosowana we wszystkich wystąpieniach systemów Windows Server 2012 R2 i Windows 8.1 windows server 2012 R2. (Poprawki systemu Windows powinny być zawsze odbierane za pośrednictwem Windows Update). Aby uzyskać więcej informacji, zobacz skojarzony artykuł Baza wiedzy Microsoft Knowledge Base Niska wydajność podczas uzyskiwania dostępu do Azure Files z programu [Windows 8.1 lub Server 2012 R2.](https://support.microsoft.com/kb/3114025)

## <a name="features-and-interoperability-with-other-services"></a>Funkcje i współdziałanie z innymi usługami
* <a id="cluster-witness"></a>
**Czy mogę użyć udziału plików platformy Azure jako obrazu udziału *plików* dla klastra trybu failover systemu Windows Server?**  
    Obecnie ta konfiguracja nie jest obsługiwana w przypadku udziału plików platformy Azure. Aby uzyskać więcej informacji na temat sposobu konfiguracji tej usługi dla usługi Azure Blob Storage, zobacz Deploy a Cloud Witness for a Failover Cluster (Wdrażanie zdarzenia w chmurze dla klastra [trybu failover).](/windows-server/failover-clustering/deploy-cloud-witness)

* <a id="containers"></a>
**Czy mogę zainstalować udział plików platformy Azure w wystąpieniu kontenera platformy Azure?**  
    Tak, udziały plików platformy Azure są dobrą opcją, jeśli chcesz utrwalać informacje poza okresem istnienia wystąpienia kontenera. Aby uzyskać więcej informacji, zobacz Mount an Azure file share with Azure Container instances (Zainstaluj udział [plików platformy Azure za pomocą usługi Azure Container Instances).](../../container-instances/container-instances-volume-azure-files.md)

* <a id="rest-rename"></a>
**Czy w interfejsie API REST istnieje operacja zmiany nazwy?**  
    Nie w tej chwili.

* <a id="nested-shares"></a>
**Czy mogę skonfigurować zagnieżdżone udziały? Innymi słowy, udział w ramach udziału?**  
    Nie. Udział plików *to sterownik* wirtualny, który można zainstalować, dlatego zagnieżdżone udziały nie są obsługiwane.

* <a id="ibm-mq"></a>
**Jak mogę używać Azure Files z programem IBM MQ?**  
    Firma IBM opublikowała dokument, który pomaga klientom ibm MQ Azure Files z usługą IBM. Aby uzyskać więcej informacji, [zobacz How to set up an IBM MQ multi-instance queue manager with Microsoft Azure Files service (Jak](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)skonfigurować menedżera kolejek z wieloma wystąpieniami programu IBM MQ przy użyciu usługi Microsoft Azure Files).

## <a name="see-also"></a>Zobacz też
* [Rozwiązywanie problemów Azure Files w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Rozwiązywanie problemów Azure Files w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Rozwiązywanie problemów z usługą Azure File Sync](../file-sync/file-sync-troubleshoot.md)
