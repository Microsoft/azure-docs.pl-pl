---
title: Często zadawane pytania (często zadawane pytania) dotyczące usług Azure Files | Dokumenty firmy Microsoft
description: Znajdź odpowiedzi na często zadawane pytania dotyczące usługi Azure Files.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 01b5f87c2557e2195573b90766ee45e001798cca
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537699"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Często zadawane pytania dotyczące usługi Azure Files
[Usługa Azure Files](storage-files-introduction.md) oferuje w pełni zarządzane udziały plików w chmurze, które są dostępne za pośrednictwem standardowego [protokołu Bloku Komunikatów serwera (SMB).](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) Udziały plików platformy Azure można zainstalować jednocześnie w chmurze lub lokalnie we wdrożeniach systemów Windows, Linux i macOS. Można również buforować udziały plików platformy Azure na komputerach z systemem Windows Server przy użyciu usługi Azure File Sync dla szybkiego dostępu w pobliżu miejsca, w którym dane są używane.

W tym artykule odniewany jest odpowiedzi na często zadawane pytania dotyczące funkcji i funkcji usługi Azure Files, w tym korzystanie z usługi Azure File Sync z usługą Azure Files. Jeśli nie widzisz odpowiedzi na swoje pytanie, możesz skontaktować się z nami za pośrednictwem następujących kanałów (w kolejności eskalacji):

1. Komentarze w tym artykule.
2. [Forum usługi Azure Storage](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
3. [Głos użytkownika plików platformy Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Pomoc techniczna firmy Microsoft. Aby utworzyć nowe żądanie pomocy technicznej, w witrynie Azure portal na karcie **Pomoc** wybierz przycisk **Pomoc + pomoc,** a następnie wybierz pozycję Nowe żądanie pomocy **technicznej**.

## <a name="general"></a>Ogólne
* <a id="why-files-useful"></a>
  **W jaki sposób usługa Azure Files jest przydatna?**  
   Usługi Azure Files można używać do tworzenia udziałów plików w chmurze, bez odpowiedzialności za zarządzanie obciążeniem serwera fizycznego, urządzenia lub urządzenia. Wykonujemy monotonną pracę za Ciebie, w tym stosowanie aktualizacji systemu operacyjnego i zastępowanie uszkodzonych dysków. Aby dowiedzieć się więcej o scenariuszach, w których usługa Azure Files może ci pomóc, zobacz [Dlaczego usługa Azure Files jest przydatna.](storage-files-introduction.md#why-azure-files-is-useful)

* <a id="file-access-options"></a>
  **Jakie są różne sposoby uzyskiwania dostępu do plików w usłudze Azure Files?**  
    Udział plików można zainstalować na komputerze lokalnym przy użyciu protokołu SMB 3.0 lub za pomocą narzędzi, takich jak [Eksplorator magazynu,](https://storageexplorer.com/) aby uzyskać dostęp do plików w udziale plików. W aplikacji można użyć bibliotek klienta magazynu, interfejsów API REST, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby uzyskać dostęp do plików w udziale plików platformy Azure.

* <a id="what-is-afs"></a>
  **Co to jest usługa Azure File Sync?**  
    Za pomocą usługi Azure File Sync można scentralizować udziały plików organizacji w usłudze Azure Files, zachowując elastyczność, wydajność i zgodność lokalnego serwera plików. Usługa Azure File Sync przekształca komputery z systemem Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu dostępnego w systemie Windows Server, aby uzyskać dostęp do danych lokalnie, w tym SMB, Network File System (NFS) i File Transfer Protocol Service (FTPS). Możesz mieć tyle pamięci podręcznych, ile potrzebujesz na całym świecie.

* <a id="files-versus-blobs"></a>
  **Dlaczego warto używać udziału plików platformy Azure w porównaniu z magazynem obiektów Blob platformy Azure dla moich danych?**  
    Usługi Azure Files i Azure Blob storage oferują sposoby przechowywania dużych ilości danych w chmurze, ale są one przydatne do nieco innych celów. 
    
    Magazyn obiektów Blob platformy Azure jest przydatne dla masowej skali, natywne dla chmury aplikacji, które muszą przechowywać dane bez struktury. Aby zmaksymalizować wydajność i skalę, magazyn obiektów Blob platformy Azure jest prostszą abstrakcją magazynu niż prawdziwy system plików. Dostęp do magazynu obiektów Blob platformy Azure można uzyskać tylko za pośrednictwem bibliotek klienta opartych na rest (lub bezpośrednio za pośrednictwem protokołu opartego na rest.

    Usługa Azure Files jest w szczególności systemem plików. Usługa Azure Files zawiera wszystkie streszczenia plików, które znasz i kochasz z lat pracy z lokalnymi systemami operacyjnymi. Podobnie jak usługa Azure Blob Storage, usługa Azure Files oferuje interfejs REST i biblioteki klienckie oparte na rest. W przeciwieństwie do usługi Azure Blob Storage usługa Azure Files oferuje dostęp SMB do udziałów plików platformy Azure. Za pomocą SMB można zainstalować udział plików platformy Azure bezpośrednio w systemie Windows, Linux lub macOS, lokalnie lub w maszynach wirtualnych w chmurze, bez pisania kodu lub dołączania żadnych specjalnych sterowników do systemu plików. Można również buforować udziały plików platformy Azure na lokalnych serwerach plików przy użyciu usługi Azure File Sync dla szybkiego dostępu, w pobliżu miejsca, w którym dane są używane. 
   
    Aby uzyskać bardziej szczegółowy opis różnic między usługą Azure Files i magazynem obiektów Blob platformy Azure, zobacz [Wprowadzenie do podstawowych usług Azure Storage.](../common/storage-introduction.md) Aby dowiedzieć się więcej o magazynie obiektów Blob platformy Azure, zobacz [Wprowadzenie do magazynu obiektów Blob.](../blobs/storage-blobs-introduction.md)

* <a id="files-versus-disks"></a>**Dlaczego miałbym używać udziału plików platformy Azure zamiast dysków platformy Azure?**  
    Dysk w usłudze Azure Disks jest po prostu dyskiem. Aby uzyskać wartość z usługi Azure Disks, należy dołączyć dysk do maszyny wirtualnej, która jest uruchomiona na platformie Azure. Usługi Azure Disks może służyć do wszystkiego, co można użyć dysku na serwerze lokalnym. Można go używać jako dysku systemowego systemu operacyjnego, jako miejsca wymiany dla systemu operacyjnego lub jako dedykowanego magazynu dla aplikacji. Interesujące zastosowanie dla dysków platformy Azure jest utworzenie serwera plików w chmurze do użycia w tych samych miejscach, w których można użyć udziału plików platformy Azure. Wdrażanie serwera plików w maszynach wirtualnych platformy Azure to wysokowydajny sposób na uzyskanie magazynu plików na platformie Azure, gdy potrzebujesz opcji wdrażania, które obecnie nie są obsługiwane przez usługi Azure Files (takie jak obsługa protokołu NFS lub magazyn w wersji premium). 

    Jednak uruchomienie serwera plików z dyskami platformy Azure jako magazynu zaplecza zazwyczaj jest znacznie droższe niż przy użyciu udziału plików platformy Azure, z kilku powodów. Po pierwsze, oprócz płacenia za magazyn dysku, należy również zapłacić za koszt uruchomienia jednej lub więcej maszyn wirtualnych platformy Azure. Po drugie należy również zarządzać maszynami wirtualnymi, które są używane do uruchamiania serwera plików. Na przykład użytkownik jest odpowiedzialny za uaktualnienia systemu operacyjnego. Na koniec, jeśli ostatecznie wymagają danych, które mają być buforowane w środowisku lokalnym, to do Ciebie, aby skonfigurować i zarządzać technologii replikacji, takich jak replikacja rozproszonego systemu plików (DFSR), aby to się stało.

    Jednym z podejść do uzyskania najlepszych usług zarówno usługi Azure Files, jak i serwera plików, który jest hostowany w maszynach wirtualnych platformy Azure (oprócz używania dysków platformy Azure jako magazynu zaplecza) jest zainstalowanie synchronizacji plików platformy Azure na serwerze plików hostowanym na maszynie wirtualnej w chmurze. Jeśli udział plików platformy Azure znajduje się w tym samym regionie co serwer plików, można włączyć warstwowanie w chmurze i ustawić maksymalną wartość procentową wolnego miejsca (99%). Zapewnia to minimalne powielanie danych. Można również używać dowolnych aplikacji z serwerami plików, takich jak aplikacje wymagające obsługi protokołu NFS.

    Aby uzyskać informacje na temat opcji konfigurowania serwera plików o wysokiej wydajności i wysokiej dostępności na platformie Azure, zobacz [Wdrażanie klastrów gości maszyn wirtualnych IaaS na platformie Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Aby uzyskać bardziej szczegółowy opis różnic między usługą Azure Files i dyskami platformy Azure, zobacz [Wprowadzenie do podstawowych usług Azure Storage.](../common/storage-introduction.md) Aby dowiedzieć się więcej o dyskach platformy Azure, zobacz [Omówienie omówienia dysków zarządzanych platformy Azure](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>
  **Jak rozpocząć korzystanie z usługi Azure Files?**  
   Rozpoczęcie pracy z usługą Azure Files jest łatwe. Najpierw [utwórz udział plików,](storage-how-to-create-file-share.md)a następnie zamontuj go w preferowanym systemie operacyjnym: 

  * [Montaż w systemie Windows](storage-how-to-use-files-windows.md)
  * [Montaż w systemie Linux](storage-how-to-use-files-linux.md)
  * [Montaż w systemie macOS](storage-how-to-use-files-mac.md)

    Aby uzyskać bardziej szczegółowy przewodnik dotyczący wdrażania udziału plików platformy Azure w celu zastąpienia udziałów plików produkcyjnych w organizacji, zobacz [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Jakie opcje nadmiarowości magazynu są obsługiwane przez usługi Azure Files?**  
    Obecnie usługa Azure Files obsługuje magazynowanie lokalne nadmiarowe (LRS), magazyn strefowy nadmiarowy (ZRS), magazyn geograficznie nadmiarowy (GRS) i magazyn geograficzny nadmiarowy (GZRS) (wersja zapoznawcza). Planujemy obsługiwać magazyn geograficzny dostępu do odczytu (RA-GRS) w przyszłości, ale nie mamy harmonogramów do udostępnienia w tej chwili.

* <a id="tier-options"></a>
  **Jakie warstwy magazynu są obsługiwane w usłudze Azure Files?**  
    Usługa Azure Files obsługuje dwie warstwy magazynu: premium i standard. Standardowe udziały plików są tworzone w ogólnego przeznaczenia (GPv1 lub GPv2) kont magazynu i udziałów plików premium są tworzone na kontach magazynu FileStorage. Dowiedz się więcej o tworzeniu [standardowych udziałów plików](storage-how-to-create-file-share.md) i [udziałów plików premium](storage-how-to-create-premium-fileshare.md). 
    
    > [!NOTE]
    > Nie można utworzyć udziałów plików platformy Azure z kont magazynu obiektów Blob lub kont magazynu *ogólnego przeznaczenia premium* (GPv1 lub GPv2). Standardowe udziały plików platformy Azure muszą być tworzone tylko na *standardowych* kontach ogólnego przeznaczenia, a udziały plików platformy Azure w wersji premium muszą być tworzone tylko na kontach magazynu FileStorage. Konta magazynu ogólnego przeznaczenia *premium* (GPv1 i GPv2) są przeznaczone tylko dla obiektów blob strony premium. 

* <a id="give-us-feedback"></a>
  **Naprawdę chcę zobaczyć określoną funkcję dodaną do usługi Azure Files. Czy możesz go dodać?**  
    Zespół usługi Azure Files jest zainteresowany zapoznaniem się ze wszystkimi opiniami na temat naszej usługi. Proszę głosować na żądania funkcji w [usłudze Azure Files UserVoice!](https://feedback.azure.com/forums/217298-storage/category/180670-files) Z niecierpliwością czekamy na wiele nowych funkcji.

  **Czy usługa Azure Files obsługuje blokowanie plików?**  
    Tak, usługa Azure Files w pełni obsługuje blokowanie plików w stylu SMB/Windows, [zobacz szczegóły](https://docs.microsoft.com/rest/api/storageservices/managing-file-locks). 
    
## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Jakie regiony są obsługiwane dla usługi Azure File Sync?**  
    Listę dostępnych regionów można znaleźć w sekcji [Dostępność regionu](storage-sync-files-planning.md#azure-file-sync-region-availability) w przewodniku planowania synchronizacji plików platformy Azure. Będziemy stale dodawać wsparcie dla dodatkowych regionów, w tym regionów niepublicznych.

* <a id="cross-domain-sync"></a>
  **Czy mogę mieć serwery przyłączone do domeny i nieprzyłączone do domeny w tej samej grupie synchronizacji?**  
    Tak. Grupa synchronizacji może zawierać punkty końcowe serwera, które mają różne członkostwa w usłudze Active Directory, nawet jeśli nie są przyłączone do domeny. Chociaż ta konfiguracja działa technicznie, nie zaleca się tego jako typowej konfiguracji, ponieważ listy kontroli dostępu (ACL), które są zdefiniowane dla plików i folderów na jednym serwerze, mogą nie być wymuszane przez inne serwery w grupie synchronizacji. Aby uzyskać najlepsze wyniki, zaleca się synchronizację między serwerami, które znajdują się w tym samym lesie usługi Active Directory, między serwerami, które znajdują się w różnych lasach usługi Active Directory, ale które nawiązały relacje zaufania, lub między serwerami, które nie znajdują się w domenie. Zaleca się unikanie używania kombinacji tych konfiguracji.

* <a id="afs-change-detection"></a>
  **Utworzono plik bezpośrednio w moim udziale plików platformy Azure przy użyciu SMB lub w portalu. Jak długo trwa synchronizacja pliku z serwerami w grupie synchronizacji?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Jeśli ten sam plik zostanie zmieniony na dwóch serwerach w przybliżeniu w tym samym czasie, co się stanie?**  
    Usługa Azure File Sync używa prostej strategii rozwiązywania konfliktów: zachowujemy obie zmiany w plikach, które są zmieniane na dwóch serwerach w tym samym czasie. Ostatnio napisana zmiana zachowuje oryginalną nazwę pliku. Starszy plik ma komputer "źródłowy" i numer konfliktu dołączany do nazwy. Wynika z tej taksonomii: 
   
    \<Nazwa plikuWithoutExtension\>-\<MachineName\>\[-#\]. \<wew.\>  

    Na przykład pierwszy konflikt CompanyReport.docx stanie się CompanyReport-CentralServer.docx, jeśli CentralServer jest, gdzie starsze zapis wystąpił. Drugi konflikt będzie nazwany CompanyReport-CentralServer-1.docx. Usługa Azure File Sync obsługuje 100 plików konfliktów na plik. Po osiągnięciu maksymalnej liczby plików konfliktów plik nie zostanie zsynchronizowany, dopóki liczba plików konfliktów nie będzie mniejsza niż 100.

* <a id="afs-storage-redundancy"></a>
  **Czy magazyn geograficznie nadmiarowy jest obsługiwany dla usługi Azure File Sync?**  
    Tak, usługa Azure Files obsługuje zarówno magazyn nadmiarowy lokalnie (LRS), jak i magazyn geograficznie nadmiarowy (GRS). Jeśli zainicjujesz funkcję pracy awaryjnej konta magazynu między sparowanymi regionami z konta skonfigurowanego dla grs, firma Microsoft zaleca, aby traktować nowy region jako kopię zapasową tylko danych. Usługa Azure File Sync nie rozpoczyna automatycznie synchronizacji z nowym regionem podstawowym. 

* <a id="sizeondisk-versus-size"></a>
  **Dlaczego właściwość *Rozmiar na dysku* dla pliku nie jest zgodna z właściwością *Size* po użyciu usługi Azure File Sync?**  
  Zobacz [Opis warstw w chmurze](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Jak sprawdzić, czy plik został warstwowy?**  
  Zobacz [Opis warstw w chmurze](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Plik, którego chcę użyć, został warstwowy. Jak mogę przywołać plik na dysku, aby używać go lokalnie?**  
  Zobacz [Opis warstw w chmurze](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Jak wymusić warstwowe tworzenie pliku lub katalogu?**  
  Zobacz [Opis warstw w chmurze](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **Jak jest interpretowane *wolne miejsce na woluminie,* gdy na woluminie jest wiele punktów końcowych serwera?**  
  Zobacz [Opis warstw w chmurze](storage-sync-cloud-tiering.md#afs-effective-vfs).

* <a id="afs-files-excluded"></a>
  **Które pliki lub foldery są automatycznie wykluczane przez usługę Azure File Sync?**  
  Zobacz [Pliki pominięte](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Czy mogę używać usługi Azure File Sync z systemem Windows Server 2008 R2, Linux lub moim podłączonym do sieci urządzeniem magazynu (NAS)?**  
    Obecnie usługa Azure File Sync obsługuje tylko systemy Windows Server 2019, Windows Server 2016 i Windows Server 2012 R2. W tej chwili nie mamy żadnych innych planów, które możemy udostępnić, ale jesteśmy otwarci na obsługę dodatkowych platform w zależności od popytu klientów. Daj nam znać na [platformie Azure Files UserVoice,](https://feedback.azure.com/forums/217298-storage/category/180670-files) jakie platformy chcesz, abyśmy wspierali.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Dlaczego pliki warstwowe istnieją poza obszarem nazw punktu końcowego serwera?**  
    Przed agentem usługi Azure File Sync w wersji 3 usługa Azure File Sync zablokowała przenoszenie plików warstwowych poza punkt końcowy serwera, ale na tym samym woluminie co punkt końcowy serwera. Operacje kopiowania, przenoszenie plików niewarstwowych i przenoszenie warstwowych na inne woluminy pozostały nienaruszone. Powodem tego zachowania było niejawne założenie, że Eksplorator plików i inne interfejsy API systemu Windows mają, że operacje przenoszenia na tym samym woluminie są (prawie) natychmiastowe operacje zmiany nazwy. Oznacza to, że ruchy sprawi, że Eksplorator plików lub inne metody przenoszenia (takie jak wiersz polecenia lub Program PowerShell) nie będą odpowiadać, podczas gdy usługa Azure File Sync przywołuje dane z chmury. Począwszy od [agenta usługi Azure File Sync w wersji 3.0.12.0,](storage-files-release-notes.md#supported-versions)usługa Azure File Sync umożliwia przeniesienie pliku warstwowego poza punkt końcowy serwera. Unikamy negatywnych skutków wcześniej wymienionych, zezwalając plik warstwowy istnieć jako plik warstwowy poza punktem końcowym serwera, a następnie przywołując plik w tle. Oznacza to, że ruchy na tym samym woluminie są natychmiastowe i wykonujemy całą pracę, aby przywołać plik na dysk po zakończeniu przenoszenia. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Mam problem z synchronizacją plików platformy Azure na moim serwerze (synchronizacja, warstwa w chmurze itp.). Czy należy usunąć i ponownie utworzyć punkt końcowy serwera?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Czy mogę przenieść usługę synchronizacji magazynu i/lub konto magazynu do innej grupy zasobów lub subskrypcji?**  
   Tak, usługę synchronizacji magazynu i/lub konto magazynu można przenieść do innej grupy zasobów lub subskrypcji w ramach istniejącej dzierżawy usługi Azure AD. Jeśli konto magazynu zostanie przeniesione, należy przyznać usłudze hybrydowej synchronizacji plików dostęp do konta magazynu (zobacz [Upewnienie się, że usługa Azure File Sync ma dostęp do konta magazynu).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)

    > [!Note]  
    > Usługa Azure File Sync nie obsługuje przenoszenia subskrypcji do innej dzierżawy usługi Azure AD.
    
* <a id="afs-ntfs-acls"></a>
  **Czy usługa Azure File Sync zachowuje listy ACL NTFS na poziomie katalogu/pliku wraz z danymi przechowywanymi w plikach platformy Azure?**

    Od 24 lutego 2020 r. nowe i istniejące listy ACL warstwowe według synchronizacji plików platformy Azure będą zachowywane w formacie NTFS, a modyfikacje listy ACL wprowadzone bezpośrednio do udziału plików platformy Azure zostaną zsynchronizowane ze wszystkimi serwerami w grupie synchronizacji. Wszelkie zmiany w listach ACL wprowadzone do usługi Azure Files zostaną zsynchronizowane za pośrednictwem synchronizacji plików platformy Azure. Podczas kopiowania danych do usługi Azure Files upewnij się, że używasz SMB, aby uzyskać dostęp do udziału i zachować listy ACL. Istniejące narzędzia oparte na rest, takie jak AzCopy lub Storage Explorer, nie są zachowywane w listach ACL.

    Jeśli usługa Azure Backup została włączona w udziałach zarządzanych plików synchronizacji plików, listy ACL plików mogą być nadal przywracane w ramach przepływu pracy przywracania kopii zapasowej. Działa to w przypadku całego udziału lub poszczególnych plików/katalogów.

    Jeśli używasz migawek jako części samodzielnie zarządzanego rozwiązania do tworzenia kopii zapasowych dla udziałów plików zarządzanych przez synchronizację plików, listy ACL mogą nie zostać przywrócone prawidłowo do list ACL NTFS, jeśli migawki zostały wykonane przed 24 lutego 2020 r. W takim przypadku należy rozważyć skontaktowanie się z pomocą techniczną platformy Azure.
    
## <a name="security-authentication-and-access-control"></a>Bezpieczeństwo, uwierzytelnianie i kontrola dostępu
* <a id="ad-support"></a>
**Czy uwierzytelnianie oparte na tożsamości i kontrola dostępu są obsługiwane przez usługi Azure Files?**  
    
    Tak, usługa Azure Files obsługuje uwierzytelnianie oparte na tożsamościach i kontrolę dostępu. Można wybrać jeden z dwóch sposobów korzystania z kontroli dostępu opartej na tożsamości: lokalne Usługi domenowe Active Directory (wersja zapoznawcza) lub Usługi domenowe Usługi domenowe Active Directory platformy Azure (Usługi Usługi Azure AD DS). Lokalne usługi domenowe Active Directory (AD DS) obsługują uwierzytelnianie przy użyciu maszyn przyłączonych do domeny usług AD DS, lokalnie lub na platformie Azure, w celu uzyskania dostępu do udziałów plików platformy Azure za pośrednictwem kontrolera SMB. Uwierzytelnianie usług Azure AD DS za pośrednictwem kontrolera SMB dla usług Azure Files umożliwia maszynom wirtualnym systemu Windows przyłączonych do domeny usług Azure AD DS dostęp do udziałów, katalogów i plików przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Omówienie obsługi uwierzytelniania opartego na tożsamości usługi Azure Files dla dostępu do SMB](storage-files-active-directory-overview.md). 

    Usługa Azure Files oferuje dwa dodatkowe sposoby zarządzania kontrolą dostępu:

    - Za pomocą sygnatur dostępu współdzielonego (SAS) można wygenerować tokeny, które mają określone uprawnienia i które są prawidłowe dla określonego przedziału czasu. Na przykład można wygenerować token z dostępem tylko do odczytu do określonego pliku, który ma 10-minutowy wygaśnięcia. Każdy, kto posiada token, gdy token jest prawidłowy, ma dostęp tylko do odczytu do tego pliku przez te 10 minut. Klucze sygnatury dostępu współdzielonego są obsługiwane tylko za pośrednictwem interfejsu API REST lub bibliotek klienckich. Należy zainstalować udział plików platformy Azure za pośrednictwem SMB przy użyciu kluczy konta magazynu.

    - Usługa Azure File Sync zachowuje i replikuje wszystkie dyskrecjonalne listy ACL lub DACL (oparte na usłudze Active Directory lub lokalne) do wszystkich punktów końcowych serwera, z którymi jest synchronizowana. Ponieważ system Windows Server może już uwierzytelniać się za pomocą usługi Active Directory, usługa Azure File Sync jest skuteczną opcją zatrzymania, dopóki nie nadejdzie pełna obsługa uwierzytelniania opartego na usłudze Active Directory i obsługi listy ACL.
    
    Aby uzyskać kompleksową reprezentację wszystkich protokołów obsługiwanych w usługach Usługi Azure Storage, można zapoznać się z [instrukcją autoryzowania dostępu do usługi Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 

* <a id="ad-support-devices"></a>
**Czy uwierzytelnianie usługi domenowe Azure Files Active Directory (Usługi Azure AD DS) obsługuje dostęp do SMB przy użyciu poświadczeń usługi Azure AD z urządzeń przyłączonych do usługi Azure AD lub zarejestrowanych w usłudze Azure AD?**

    Nie, ten scenariusz nie jest obsługiwany.

* <a id="ad-support-rest-apis"></a>
**Czy istnieją interfejsy API REST do obsługi list ALs Get/Set/Copy directory/file NTFS?**

    Tak, obsługujemy interfejsy API REST, które otrzymują, ustawiają lub kopiują listy ACL NTFS dla katalogów lub plików podczas korzystania z interfejsu API REST 2019-07-07.Yes, we supporty REST API that get, set, or copy NTFS ACL for directories or files when using the [2019-07-07](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (or noce) REST API.

* <a id="ad-vm-subscription"></a>
**Czy mogę uzyskać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD z maszyny Wirtualnej w ramach innej subskrypcji?**

    Jeśli subskrypcja, w ramach której jest wdrażany udział plików jest skojarzony z tej samej dzierżawy usługi Azure AD jako wdrożenia usług Ad DS, do którego maszyna wirtualna jest przyłączona do domeny, można następnie uzyskać dostęp do udziałów plików platformy Azure przy użyciu tych samych poświadczeń usługi Azure AD. Ograniczenie jest nakładane nie na subskrypcję, ale na skojarzoną dzierżawę usługi Azure AD.
    
* <a id="ad-support-subscription"></a>
**Czy mogę włączyć usługi Azure AD DS lub lokalne uwierzytelnianie usług AD DS dla udziałów plików platformy Azure przy użyciu dzierżawy usługi Azure AD, która różni się od głównej dzierżawy udziału plików platformy Azure?**

    Nie, usługa Azure Files obsługuje tylko integrację usług Azure AD DS lub lokalnych usług AD DS z dzierżawą usługi Azure AD, która znajduje się w tej samej subskrypcji co udział plików. Tylko jedna subskrypcja może być skojarzona z dzierżawą usługi Azure AD. To ograniczenie dotyczy zarówno usług Azure AD DS, jak i lokalnych metod uwierzytelniania usług AD DS. Podczas korzystania z lokalnych usług AD DS do uwierzytelniania poświadczenia usług AD DS muszą być synchronizowane z [usługą Azure AD,](../../active-directory/hybrid/how-to-connect-install-roadmap.md) z którą jest skojarzone konto magazynu.

* <a id="ad-linux-vms"></a>
**Czy uwierzytelnianie usług AD DS lub lokalne usługi AD DS dla udziałów plików platformy Azure obsługuje maszyny wirtualne z systemem Linux?**

    Nie, uwierzytelnianie z maszyn wirtualnych z systemem Linux nie jest obsługiwane.

* <a id="ad-aad-smb-afs"></a>
**Czy udziały plików zarządzane przez usługę Azure File Sync obsługują usługi Azure AD DS lub lokalne uwierzytelnianie usług AD DS (w wersji zapoznawczej)?**

    Tak, można włączyć usługi Azure AD DS lub lokalne uwierzytelnianie usług AD DS w udziale plików zarządzanym przez usługę Azure File Sync. Zmiany w listach ACL NTFS katalogu/pliku na lokalnych serwerach plików będą warstwowe w warstwie usługi Azure Files i odwrotnie.

* <a id="ad-aad-smb-files"></a>
**Jak sprawdzić, czy włączono uwierzytelnianie usług AD DS na moim koncie magazynu i pobrać informacje o domenie?**

    Aby uzyskać instrukcje, zobacz [tutaj](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#1-enable-ad-authentication-for-your-account).
    
* <a id="encryption-at-rest"></a>
**Jak mogę się upewnić, że mój udział plików platformy Azure jest szyfrowany w spoczynku?**  

    Tak. Aby uzyskać więcej informacji, zobacz [Szyfrowanie usługi Azure Storage](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Jak mogę zapewnić dostęp do określonego pliku za pomocą przeglądarki internetowej?**  

    Za pomocą podpisów dostępu współdzielonego można wygenerować tokeny, które mają określone uprawnienia i które są prawidłowe dla określonego przedziału czasu. Na przykład można wygenerować token, który daje dostęp tylko do odczytu do określonego pliku, przez określony czas. Każdy, kto posiada adres URL, może uzyskać dostęp do pliku bezpośrednio z dowolnej przeglądarki internetowej, gdy token jest prawidłowy. Klucz podpisu dostępu współdzielonego można łatwo wygenerować z interfejsu użytkownika, takiego jak Eksplorator magazynu.

* <a id="file-level-permissions"></a>
**Czy można określić uprawnienia tylko do odczytu lub tylko do zapisu w folderach w ramach udziału?**  

    Jeśli instalujesz udział plików przy użyciu SMB, nie masz kontroli na poziomie folderu nad uprawnieniami. Jednak jeśli podpis dostępu współdzielonego przy użyciu interfejsu API REST lub bibliotek klienta można określić uprawnienia tylko do odczytu lub tylko do zapisu w folderach w ramach udziału.

* <a id="ip-restrictions"></a>
**Czy można zaimplementować ograniczenia ip dla udziału plików platformy Azure?**  

    Tak. Dostęp do udziału plików platformy Azure może być ograniczony na poziomie konta magazynu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Jakie zasady zgodności danych obsługuje usługa Azure Files?**  

   Usługa Azure Files działa na tej samej architekturze magazynu, która jest używana w innych usługach magazynu w usłudze Azure Storage. Usługa Azure Files stosuje te same zasady zgodności danych, które są używane w innych usługach magazynu platformy Azure. Aby uzyskać więcej informacji na temat zgodności danych usługi Azure Storage, można zapoznać się z [ofertami zgodności usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)i przejść do Centrum zaufania firmy [Microsoft](https://microsoft.com/trustcenter/default.aspx).
   
### <a name="ad-authentication"></a>Uwierzytelnianie usługi AD
* <a id=""></a>
**Czy uwierzytelnianie usługi Azure Files azure ad obsługuje maszyny wirtualne z systemem Linux?**

    Nie, uwierzytelnianie z maszyn wirtualnych z systemem Linux nie jest obsługiwane.

* <a id="ad-multiple-forest"></a>
**Czy lokalne uwierzytelnianie usług AD DS dla udziałów plików platformy Azure obsługuje integrację ze środowiskiem usług AD DS przy użyciu wielu lasów?**    

    Lokalne uwierzytelnianie usług AD DS usługi Azure Files integruje się tylko z lasem usługi domeny, do których jest zarejestrowane konto magazynu. Aby obsługa uwierzytelniania z innego lasu, środowisko musi mieć zaufanie lasu poprawnie skonfigurowane. Sposób, w jaki usługa Azure Files rejestruje się w usługach AD DS prawie tak samo jak zwykły serwer plików, gdzie tworzy tożsamość (konto logowania komputera lub usługi) w usługach AD DS w celu uwierzytelniania. Jedyną różnicą jest to, że zarejestrowana nazwa SPN konta magazynu kończy się na "file.core.windows.net", który nie jest zgodny z sufiksem domeny. Skonsultuj się z administratorem domeny, aby sprawdzić, czy aktualizacja zasad routingu DNS jest wymagana do włączenia wielokrotnego uwierzytelniania lasu z powodu innego sufiksu domeny.

* <a id=""></a>
**Jakie regiony są dostępne dla uwierzytelniania usług AD DS usługi Azure Files (w wersji zapoznawczej)?**

    Szczegółowe informacje można znaleźć w [regionalnej dostępności usług AD DS.](storage-files-identity-auth-active-directory-enable.md#regional-availability)
    
* <a id="ad-aad-smb-afs"></a>
**Czy mogę korzystać z uwierzytelniania (wersji zapoznawczej) usługi Azure Files Active Directory (preview) w udziałach plików zarządzanych przez usługę Azure File Sync?**

    Tak, można włączyć uwierzytelnianie usługi AD w udziale plików zarządzanym przez synchronizację plików platformy Azure. Zmiany w listach ACL NTFS katalogu/pliku na lokalnych serwerach plików będą warstwowe w warstwie usługi Azure Files i odwrotnie.

* <a id="ad-aad-smb-files"></a>
**Jak sprawdzić, czy włączono uwierzytelnianie usługi AD na moim koncie magazynu i informacje o domenie usługi AD?**

    Można zapoznać się z instrukcjami podanymi [w tym miejscu,](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#enable-ad-authentication-for-your-account) aby sprawdzić, czy uwierzytelnianie usługi Azure Files AD jest włączone na koncie magazynu i pobrać informacje o domenie usługi AD.

* <a id="ad-aad-smb-files"></a>
**Czy istnieje jakaś różnica w tworzeniu konta logowania komputera lub konta logowania usługi do reprezentowania mojego konta magazynu w usłudze AD?**

    Utworzenie [konta komputera](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (domyślnie) lub konta logowania [do usługi](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) nie ma różnicy w sposobie działania uwierzytelniania z usługą Azure Files. Można dokonać własnego wyboru, jak reprezentować konto magazynu jako tożsamość w środowisku usługi AD. Domyślny zestaw domainaccounttype w połączce cmdlet Join-AzStorageAccountForAuth to konto komputera. Jednak wiek wygaśnięcia hasła skonfigurowany w środowisku usługi AD może być różny dla konta logowania komputera lub usługi i należy wziąć to pod uwagę w [przypadku aktualizacji hasła tożsamości konta magazynu w usłudze AD](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#5-update-ad-account-password).

## <a name="on-premises-access"></a>Dostęp lokalny

* <a id="port-445-blocked"></a>
**Mój usługodawca lub it blokuje port 445, który nie jest w stanie zainstalować usługi Azure Files. Co należy zrobić?**

    Możesz dowiedzieć się o [różnych sposobach obejścia zablokowanego portu 445 tutaj](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked). Usługa Azure Files zezwala tylko na połączenia przy użyciu SMB 3.0 (z obsługą szyfrowania) spoza regionu lub centrum danych. Protokół SMB 3.0 wprowadził wiele funkcji zabezpieczeń, w tym szyfrowanie kanałów, które jest bardzo bezpieczne w użyciu przez Internet. Jednak możliwe, że port 445 został zablokowany ze względu na historyczne przyczyny luk znalezionych w niższych wersjach SMB. W idealnym przypadku port powinien być zablokowany tylko dla ruchu SMB 1.0 i SMB 1.0 powinny być wyłączone na wszystkich klientach.

* <a id="expressroute-not-required"></a>
**Czy muszę używać usługi Azure ExpressRoute do łączenia się z usługą Azure Files lub do korzystania z usługi Azure File Sync lokalnie?**  

    Nie. Usługa ExpressRoute nie jest wymagana do uzyskiwania dostępu do udziału plików platformy Azure. Jeśli montujesz udział plików platformy Azure bezpośrednio lokalnie, wszystko, co jest wymagane, to mieć port 445 (TCP wychodzących) otwarty dla dostępu do Internetu (jest to port, który SMB używa do komunikowania się). Jeśli używasz usługi Azure File Sync, wszystko, co jest wymagane jest port 443 (TCP wychodzących) dla dostępu HTTPS (nie wymagane SMB). *Można* jednak użyć usługi ExpressRoute z jedną z tych opcji dostępu.

* <a id="mount-locally"></a>
**Jak zainstalować udział plików platformy Azure na komputerze lokalnym?**  

    Udział plików można zainstalować przy użyciu protokołu SMB, jeśli port 445 (wychodzący TCP) jest otwarty, a klient obsługuje protokół SMB 3.0 (na przykład, jeśli używasz systemu Windows 10 lub Windows Server 2016). Jeśli port 445 jest zablokowany przez zasady organizacji lub przez usługodawcę, możesz użyć usługi Azure File Sync, aby uzyskać dostęp do udziału plików platformy Azure.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Jak wykonać em utworzenie kopii zapasowej udziału plików platformy Azure?**  
    W celu ochrony przed przypadkowymi usunięciami można używać [okresowych migawek udziału.](storage-snapshots-files.md) Można również użyć AzCopy, Robocopy lub narzędzia do tworzenia kopii zapasowych innej firmy, które może wykonać kopię zapasową zainstalowanego udziału plików. Usługa Azure Backup oferuje tworzenie kopii zapasowych plików platformy Azure. Dowiedz się więcej o [tworzenie kopii zapasowych udziałów plików platformy Azure przez usługę Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files).

## <a name="share-snapshots"></a>Migawki udziałów

### <a name="share-snapshots-general"></a>Udostępnianie migawek: Ogólne
* <a id="what-are-snaphots"></a>
**Co to są migawki udziału plików?**  
    Migawek udziału plików platformy Azure służy do tworzenia wersji tylko do odczytu udziałów plików. Można również użyć usługi Azure Files, aby skopiować wcześniejszą wersję zawartości z powrotem do tego samego udziału, do alternatywnej lokalizacji na platformie Azure lub lokalnie, aby uzyskać więcej modyfikacji. Aby dowiedzieć się więcej o migawkach udostępniania, zobacz [omówienie migawki udostępniania](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Gdzie są przechowywane migawki mojego udziału?**  
    Migawki udziału są przechowywane na tym samym koncie magazynu co udział plików.

* <a id="snapshot-consistency"></a>
**Czy migawki udostępniania są spójne z aplikacją?**  
    Nie, migawki udziału nie są spójne z aplikacją. Użytkownik musi opróżnić zapisy z aplikacji do udziału przed podjęciem migawki udziału.

* <a id="snapshot-limits"></a>
**Czy istnieją ograniczenia dotyczące liczby migawek udostępniania, których mogę użyć?**  
    Tak. Usługa Azure Files może zachować maksymalnie 200 migawek udziału. Migawki udziału nie są wliczane do przydziału udziału, więc nie ma limitu na jedną akcję dla całkowitej przestrzeni, która jest używana przez wszystkie migawki udziału. Limity kont magazynu nadal obowiązują. Po 200 migawek udziału, należy usunąć starsze migawki, aby utworzyć nowe migawki udziału.

* <a id="snapshot-cost"></a>
**Ile kosztują migawki udziału?**  
    Standardowa transakcja i standardowy koszt magazynowania będą miały zastosowanie do migawki. Migawki mają charakter przyrostowy. Migawka podstawowa jest sam udział. Wszystkie kolejne migawki są przyrostowe i będą przechowywać tylko różnice z poprzedniej migawki. Oznacza to, że zmiany delta, które będą widoczne na rachunku będzie minimalna, jeśli churn obciążenia jest minimalna. Zobacz [stronę cennika](https://azure.microsoft.com/pricing/details/storage/files/) informacji o cenach standardowych plików azure. Obecnie sposobem na przyjrzenie się rozmiarowi zużytemu przez migawkę udziału jest porównanie rozliczanej pojemności z używaną pojemnością. Pracujemy nad narzędziami usprawniającymi raportowanie.

* <a id="ntfs-acls-snaphsots"></a>
**Czy listy ACL NTFS w katalogach i plikach są utrwalane w migawkach udziału?**  
    Listy ACL NTFS w katalogach i plikach są utrwalane w migawkach udziału.

### <a name="create-share-snapshots"></a>Tworzenie migawek udziałów
* <a id="file-snaphsots"></a>
**Czy mogę utworzyć migawkę udziału poszczególnych plików?**  
    Migawki udziału są tworzone na poziomie udziału plików. Można przywrócić poszczególne pliki z migawki udziału plików, ale nie można tworzyć migawek udziału na poziomie pliku. Jeśli jednak wykonaliśmy migawkę udziału na poziomie udziału i chcesz wyświetlić listę migawek udostępniania, w których zmienił się określony plik, można to zrobić w obszarze **Poprzednie wersje** w udziale zainstalowanym w systemie Windows. 
    
    Jeśli potrzebujesz funkcji migawki pliku, poinformuj nas o tym na platformie [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Czy można tworzyć migawki udziału zaszyfrowanego udziału plików?**  
    Możesz zrobić migawkę udziału udziałów plików platformy Azure, które mają włączone szyfrowanie w spoczynku. Pliki można przywrócić z migawki udziału do zaszyfrowanego udziału plików. Jeśli twój udział jest zaszyfrowany, migawka udziału również jest szyfrowana.

* <a id="geo-redundant-snaphsots"></a>
**Czy migawki mojego udziału są geobeksochłodowe?**  
    Migawki udziału mają taką samą nadmiarowość jak udział plików platformy Azure, dla którego zostały podjęte. Jeśli wybrano magazyn geograficznie nadmiarowy dla swojego konta, migawka udziału jest również przechowywana nadmiarowo w sparowanym regionie.

### <a name="manage-share-snapshots"></a>Zarządzanie migawkami udziału
* <a id="browse-snapshots-linux"></a>
**Czy mogę przeglądać migawki udostępniania z linuksa?**  
    Za pomocą interfejsu wiersza polecenia platformy Azure można tworzyć, listy, przeglądać i przywracać migawki udziału w systemie Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Czy mogę skopiować migawki udziału na inne konto magazynu?**  
    Można kopiować pliki z migawek udziału do innej lokalizacji, ale nie można kopiować migawek udziału samodzielnie.

### <a name="restore-data-from-share-snapshots"></a>Przywracanie danych z migawek udziału
* <a id="promote-share-snapshot"></a>
**Czy mogę promować migawkę udziału do udziału podstawowego?**  
    Można skopiować dane z migawki udziału do dowolnego innego miejsca docelowego. Nie można podwyższyć migawki udziału do udziału podstawowego.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Czy mogę przywrócić dane z migawki udziału na innym koncie magazynu?**  
    Tak. Pliki z migawki udziału można skopiować do oryginalnej lokalizacji lub do lokalizacji alternatywnej, która zawiera to samo konto magazynu lub inne konto magazynu w tym samym regionie lub w różnych regionach. Można również kopiować pliki do lokalizacji lokalnej lub do dowolnej innej chmury.    
  
### <a name="clean-up-share-snapshots"></a>Czyszczenie migawek udziału
* <a id="delete-share-keep-snapshots"></a>
**Czy mogę usunąć udział, ale nie usunąć migawek udziału?**  
    Jeśli masz aktywne migawki udziału w udziale, nie możesz usunąć udziału. Za pomocą interfejsu API można usunąć migawki udziału wraz z udziałem. Można również usunąć zarówno migawki udziału i udział w witrynie Azure portal.

* <a id="delete-share-with-snapshots"></a>
**Co się stanie z migawkami udziału, jeśli usunę konto magazynu?**  
    Jeśli usuniesz swoje konto magazynu, migawki udziału również zostaną usunięte.

## <a name="billing-and-pricing"></a>Rozliczenia i ceny
* <a id="vm-file-share-network-traffic"></a>
**Czy ruch sieciowy między maszyną wirtualną platformy Azure a udziałem plików platformy Azure jest liczony jako przepustowość zewnętrzna naliczana do subskrypcji?**  
    Jeśli udział plików i maszyna wirtualna znajdują się w tym samym regionie platformy Azure, nie ma żadnych dodatkowych opłat za ruch między udziałem plików a maszyną wirtualną. Jeśli udział plików i maszyna wirtualna znajdują się w różnych regionach, ruch między nimi jest naliczany jako przepustowość zewnętrzna.

* <a id="share-snapshot-price"></a>
**Ile kosztują migawki udziału?**  
     Podczas podglądu nie ma żadnych opłat za pojemność migawki udziału. Obowiązują standardowe koszty wyjścia z magazynu i koszty transakcji. Po ogólnej dostępności subskrypcje będą naliczane za pojemność i transakcje na migawkach udziałów.
     
     Migawki udziału mają charakter przyrostowy. Migawka udziału podstawowego jest sam udział. Wszystkie kolejne migawki udziału są przyrostowe i przechowują tylko różnicę od poprzedniej migawki udziału. Naliczane są tylko za zmienioną zawartość. Jeśli masz udział z 100 GiB danych, ale tylko 5 GiB zmienił się od czasu ostatniej migawki udziału, migawka udziału zużywa tylko 5 dodatkowych GiB, a opłaty są rozliczane za 105 GiB. Aby uzyskać więcej informacji na temat transakcji i standardowych opłat wychodzących, zobacz [stronę Cennik](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skalowalność i wydajność
* <a id="files-scale-limits"></a>
**Jakie są limity skali plików platformy Azure?**  
    Aby uzyskać informacje o skalowalności i docelowych wydajności dla usług Azure Files, zobacz [Usługi Azure Files skalowalności i docelowych wydajności](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Jakie rozmiary są dostępne dla udziałów plików platformy Azure?**  
    Rozmiary udziału plików platformy Azure (premium i standard) można skalować do 100 TiB. Zobacz [onboard do większych udziałów plików (warstwa standardowa)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) sekcji przewodnik planowania instrukcji dołączania do większych udziałów plików dla warstwy standardowej.

* <a id="lfs-performance-impact"></a>
**Czy rozszerzenie przydziału udziału plików wpływa na moje obciążenia lub synchronizację plików platformy Azure?**
    
    Nie. Rozszerzenie przydziału nie wpłynie na obciążenia ani na synchronizację plików platformy Azure.

* <a id="open-handles-quota"></a>
**Ilu klientów może jednocześnie uzyskać dostęp do tego samego pliku?**   
    Istnieje przydział 2000 otwartych dojść w jednym pliku. Gdy masz 2000 otwartych dojść, wyświetlany jest komunikat o błędzie informujący o osiągnięciu przydziału.

* <a id="zip-slow-performance"></a>
**Moja wydajność jest niska, gdy rozpaszę pliki w usłudze Azure Files. Co należy zrobić?**  
    Aby przenieść dużą liczbę plików do usługi Azure Files, zaleca się użycie AzCopy (dla systemu Windows; w wersji zapoznawczej dla systemów Linux i UNIX) lub programu Azure PowerShell. Narzędzia te zostały zoptymalizowane pod kątem transferu sieciowego.

* <a id="slow-perf-windows-81-2012r2"></a>
**Dlaczego moja wydajność jest niska po zainstalowaniu udziału plików platformy Azure w systemie Windows Server 2012 R2 lub Windows 8.1?**  
    Istnieje znany problem podczas instalowania udziału plików platformy Azure w systemach Windows Server 2012 R2 i Windows 8.1. Problem został poprawiony w aktualizacji zbiorczej z kwietnia 2014 r. dla systemów Windows 8.1 i Windows Server 2012 R2. Aby uzyskać optymalną wydajność, upewnij się, że wszystkie wystąpienia systemów Windows Server 2012 R2 i Windows 8.1 mają zastosowaną poprawkę. (Zawsze powinny być otrzymywane poprawki systemu Windows za pośrednictwem witryny Windows Update). Aby uzyskać więcej informacji, zobacz skojarzony artykuł z bazy wiedzy Microsoft Knowledge Base [Niska wydajność podczas uzyskiwania dostępu do plików platformy Azure z systemu Windows 8.1 lub Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funkcje i interoperacyjność z innymi usługami
* <a id="cluster-witness"></a>
**Czy mogę używać mojego udziału plików platformy Azure jako *monitora udziału plików* dla mojego klastra trybu failover systemu Windows Server?**  
    Obecnie ta konfiguracja nie jest obsługiwana dla udziału plików platformy Azure. Aby uzyskać więcej informacji na temat konfigurowania tej usługi dla magazynu obiektów Blob platformy Azure, zobacz [Wdrażanie monitora chmury dla klastra trybu failover.](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)

* <a id="containers"></a>
**Czy mogę zainstalować udział plików platformy Azure w wystąpieniu kontenera platformy Azure?**  
    Tak, udziały plików platformy Azure są dobrym rozwiązaniem, gdy chcesz utrwalić informacje poza okres istnienia wystąpienia kontenera. Aby uzyskać więcej informacji, zobacz [Instalowanie udziału plików platformy Azure za pomocą wystąpień usługi Azure Container](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>
**Czy istnieje operacja zmiany nazwy w interfejsie API REST?**  
    Nie w tej chwili.

* <a id="nested-shares"></a>
**Czy można skonfigurować udziały zagnieżdżone? Innymi słowy, udział w udziale?**  
    Nie. Udział plików *jest* wirtualnym sterownikiem, który można zainstalować, więc zagnieżdżone udziały nie są obsługiwane.

* <a id="ibm-mq"></a>
**Jak korzystać z usługi Azure Files z ibm mq?**  
    Firma IBM wydała dokument, który pomaga klientom IBM MQ skonfigurować usługę Azure Files za pomocą usługi IBM. Aby uzyskać więcej informacji, zobacz [Jak skonfigurować menedżera kolejek wielu wystąpień IBM MQ za pomocą usługi Microsoft Azure Files](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Zobacz też
* [Rozwiązywanie problemów z plikami platformy Azure w systemie Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Rozwiązywanie problemów z plikami platformy Azure w systemie Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Rozwiązywanie problemów z usługą Azure File Sync](storage-sync-files-troubleshoot.md)
