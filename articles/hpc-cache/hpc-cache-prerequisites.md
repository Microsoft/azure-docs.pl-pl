---
title: Azure HPC Cache wymagań wstępnych
description: Wymagania wstępne dotyczące korzystania z Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/14/2021
ms.author: v-erkel
ms.openlocfilehash: 3cddbba3dca64561d7e2b7b27715152a26a8c9e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717589"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Wymagania wstępne dotyczące Azure HPC Cache

Przed użyciem Azure Portal do utworzenia nowego Azure HPC Cache, upewnij się, że środowisko spełnia te wymagania.

## <a name="video-overviews"></a>Przeglądy wideo

Obejrzyj te klipy wideo, aby uzyskać szybki przegląd składników systemu i tego, czego potrzebują do wspólnej pracy.

(Kliknij obraz wideo lub link do obejrzenia).

* [Jak to działa](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) — wyjaśnia, jak Azure HPC Cache z magazynem i klientami

  [![obraz miniatury wideo: Azure HPC Cache: jak to działa (kliknij, aby odwiedzić stronę wideo)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Wymagania wstępne —](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) opis wymagań dotyczących magazynu NAS, usługi Azure Blob Storage, dostępu do sieci i dostępu klienta

  [![obraz miniatury wideo: Azure HPC Cache: Wymagania wstępne (kliknij, aby odwiedzić stronę wideo)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Przeczytaj pozostałą część tego artykułu, aby uzyskać konkretne zalecenia.

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Zalecana jest płatna subskrypcja.

## <a name="network-infrastructure"></a>Infrastruktura sieciowa

Przed użyciem pamięci podręcznej należy skonfigurować dwa wymagania wstępne dotyczące sieci:

* Dedykowana podsieć dla Azure HPC Cache wystąpienia
* Obsługa systemu DNS, dzięki czemu pamięć podręczna może uzyskać dostęp do magazynu i innych zasobów

### <a name="cache-subnet"></a>Podsieć pamięci podręcznej

Sieć Azure HPC Cache dedykowaną podsieć o tych cechach:

* Podsieć musi mieć co najmniej 64 dostępne adresy IP.
* Podsieć nie może hostowania żadnych innych maszyn wirtualnych, nawet w przypadku powiązanych usług, takich jak maszyny klienckie.
* Jeśli używasz wielu Azure HPC Cache, każde z nich wymaga własnej podsieci.

Najlepszym rozwiązaniem jest utworzenie nowej podsieci dla każdej pamięci podręcznej. Możesz utworzyć nową sieć wirtualną i podsieć w ramach tworzenia pamięci podręcznej.

### <a name="dns-access"></a>Dostęp DNS

Pamięć podręczna musi mieć system DNS, aby uzyskać dostęp do zasobów poza siecią wirtualną. W zależności od zasobów, których używasz, może być konieczne skonfigurowanie dostosowanego serwera DNS i skonfigurowanie przekazywania między tym serwerem a Azure DNS serwerami:

* Aby uzyskać dostęp do punktów końcowych usługi Azure Blob Storage i innych zasobów wewnętrznych, potrzebny jest serwer DNS oparty na platformie Azure.
* Aby uzyskać dostęp do magazynu lokalnego, należy skonfigurować niestandardowy serwer DNS, który może rozpoznać nazwy hostów magazynu. Należy to zrobić przed utworzeniem pamięci podręcznej.

Jeśli używasz tylko usługi Blob Storage, możesz użyć domyślnego serwera DNS dostarczonego przez platformę Azure dla pamięci podręcznej. Jeśli jednak potrzebujesz dostępu do magazynu lub innych zasobów poza platformą Azure, musisz utworzyć niestandardowy serwer DNS i skonfigurować go do przekazywania wszelkich żądań rozpoznawania specyficznych dla platformy Azure do Azure DNS serwera.

Aby użyć niestandardowego serwera DNS, należy wykonać następujące kroki konfiguracji przed utworzeniem pamięci podręcznej:

* Utwórz sieć wirtualną, która będzie hostować Azure HPC Cache.
* Utwórz serwer DNS.
* Dodaj serwer DNS do sieci wirtualnej pamięci podręcznej.

  Wykonaj następujące kroki, aby dodać serwer DNS do sieci wirtualnej w Azure Portal:

  1. Otwórz sieć wirtualną w Azure Portal.
  1. Wybierz pozycję Serwery DNS z menu Ustawienia na pasku bocznym.
  1. Wybierz pozycję Niestandardowy
  1. Wprowadź adres IP serwera DNS w polu .

Prosty serwer DNS może również służyć do równoważenia obciążenia połączeń klientów między wszystkimi dostępnymi punktami instalacji pamięci podręcznej.

Dowiedz się więcej o sieciach wirtualnych platformy Azure i konfiguracjach serwera DNS w [tesłudze Name resolution for resources in Azure virtual networks (Rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure).](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="permissions"></a>Uprawnienia

Przed rozpoczęciem tworzenia pamięci podręcznej sprawdź te wymagania wstępne dotyczące uprawnień.

* Wystąpienie pamięci podręcznej musi mieć możliwość tworzenia wirtualnych interfejsów sieciowych ( NIC). Użytkownik, który tworzy pamięć podręczną, musi mieć wystarczające uprawnienia w subskrypcji, aby utworzyć karty sieciowe.

* W przypadku korzystania z usługi Blob Storage Azure HPC Cache autoryzacji w celu uzyskania dostępu do konta magazynu. Użyj kontroli dostępu opartej na rolach (RBAC) platformy Azure, aby zapewnić pamięci podręcznej dostęp do magazynu obiektów blob. Wymagane są dwie role: Współautor konta magazynu i Współautor danych obiektu blob magazynu.

  Postępuj zgodnie z instrukcjami w [te tematu Dodawanie obiektów docelowych magazynu,](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) aby dodać role.

## <a name="storage-infrastructure"></a>Infrastruktura magazynu
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

Pamięć podręczna obsługuje kontenery obiektów blob platformy Azure, eksporty magazynu sprzętowego systemu plików NFS i kontenery obiektów blob usługi ADLS zainstalowane w systemie plików NFS (obecnie w wersji zapoznawczej). Dodaj obiekty docelowe magazynu po utworzeniu pamięci podręcznej.

Każdy typ magazynu ma określone wymagania wstępne.

### <a name="blob-storage-requirements"></a>Wymagania dotyczące magazynu obiektów blob

Jeśli chcesz używać usługi Azure Blob Storage z pamięcią podręczną, potrzebujesz zgodnego konta magazynu i pustego kontenera obiektów blob lub kontenera wypełnionego sformatowanym danymi usługi Azure HPC Cache zgodnie z opisem w temacie Przenoszenie danych do usługi [Azure Blob Storage.](hpc-cache-ingest.md)

> [!NOTE]
> Różne wymagania dotyczą magazynu obiektów blob zainstalowanego w ramach systemu plików NFS. Aby uzyskać szczegółowe informacje, zapoznaj się z wymaganiami magazynu [ADLS-NFS.](#nfs-mounted-blob-adls-nfs-storage-requirements-preview)

Utwórz konto przed podjęciem próby dodania miejsca docelowego magazynu. Nowy kontener można utworzyć podczas dodawania obiektu docelowego.

Aby utworzyć zgodne konto magazynu, użyj jednej z tych kombinacji:

| Wydajność | Typ | Replikacja | Warstwa dostępu |
|--|--|--|--|
| Standardowa (Standard) | StorageV2 (ogólnego przeznaczenia wersja 2)| Magazyn lokalnie nadmiarowy (LRS) lub magazyn strefowo nadmiarowy (ZRS) | Gorąca |
| Premium | Blokowe obiekty blob | Magazyn lokalnie nadmiarowy (LRS) | Gorąca |

Konto magazynu musi być dostępne z prywatnej podsieci pamięci podręcznej. Jeśli Twoje konto używa prywatnego punktu końcowego lub publicznego punktu końcowego ograniczonego do określonych sieci wirtualnych, upewnij się, że włączyć dostęp z podsieci pamięci podręcznej. (Otwarty publiczny punkt końcowy nie jest zalecany).

Dobrym rozwiązaniem jest użycie konta magazynu w tym samym regionie świadczenia usługi Azure co pamięć podręczna.

Należy również udzielić aplikacji pamięci podręcznej dostępu do konta usługi Azure Storage, jak wspomniano w [powyższej witrynie Permissions](#permissions)(Uprawnienia). Postępuj zgodnie z procedurą w [tece Dodawanie obiektów docelowych magazynu,](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) aby nadać pamięci podręcznej wymagane role dostępu. Jeśli nie jesteś właścicielem konta magazynu, musisz wykonać ten krok.

### <a name="nfs-storage-requirements"></a>Wymagania dotyczące magazynu NFS
<!-- linked from configuration.md -->

Jeśli korzystasz z systemu magazynowania NFS (na przykład lokalnego sprzętowego systemu NAS), upewnij się, że spełnia on te wymagania. Aby zweryfikować te ustawienia, może być konieczne spór z administratorami sieci lub menedżerami zapory dla systemu magazynowania (lub centrum danych).

> [!NOTE]
> Tworzenie miejsca docelowego magazynu nie powiedzie się, jeśli pamięć podręczna nie ma wystarczającego dostępu do systemu magazynu NFS.

Więcej informacji znajduje się w te tematach Troubleshoot NAS configuration and NFS storage target issues (Rozwiązywanie problemów z konfiguracją serwera NAS i [obiektem docelowym magazynu NFS).](troubleshoot-nas.md)

* Łączność sieciowa: Azure HPC Cache dostępu do sieci o wysokiej przepustowości między podsiecią pamięci podręcznej a centrum danych systemu NFS. [Zalecana jest expressRoute](../expressroute/index.yml) lub podobny dostęp. W przypadku korzystania z sieci VPN może być konieczne skonfigurowanie jej tak, aby blokowała protokół TCP MSS na poziomie 1350, aby upewnić się, że duże pakiety nie są blokowane. Przeczytaj temat [Ograniczenia rozmiaru pakietów sieci VPN,](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) aby uzyskać dodatkową pomoc w rozwiązywaniu problemów z ustawieniami sieci VPN.

* Dostęp do portów: pamięć podręczna musi mieć dostęp do określonych portów TCP/UDP w systemie magazynu. Różne typy magazynów mają różne wymagania dotyczące portów.

  Aby sprawdzić ustawienia systemu magazynu, wykonaj tę procedurę.

  * Wydaj `rpcinfo` polecenie dla systemu magazynu, aby sprawdzić wymagane porty. Poniższe polecenie wyświetla listę portów i formatuje odpowiednie wyniki w tabeli. (Użyj adresu IP systemu w miejsce *<storage_IP>).*

    To polecenie można wydać z dowolnego klienta systemu Linux, który ma zainstalowaną infrastrukturę systemu plików NFS. Jeśli używasz klienta w podsieci klastra, może to również pomóc zweryfikować łączność między podsiecią a systemem magazynu.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Upewnij się, że wszystkie porty zwrócone przez zapytanie zezwalają na nieograniczony ruch ``rpcinfo`` Azure HPC Cache podsieci użytkownika.

  * Jeśli nie możesz użyć polecenia , upewnij się, że te często używane porty zezwalają na ruch przychodzący `rpcinfo` i wychodzący:

    | Protokół | Port  | Usługa  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | po instalacji   |
    | TCP/UDP  | 4047  | status   |

    Niektóre systemy używają różnych numerów portów dla tych usług — zapoznaj się z dokumentacją systemu magazynowania, aby mieć pewność.

  * Sprawdź ustawienia zapory, aby upewnić się, że zezwalają na ruch na wszystkich wymaganych portach. Pamiętaj, aby sprawdzić zapory używane na platformie Azure, a także zapory lokalne w centrum danych.

* Dostęp główny (odczyt/zapis): pamięć podręczna łączy się z systemem back-end jako identyfikator użytkownika 0. Sprawdź następujące ustawienia w systemie magazynu:
  
  * Włącz `no_root_squash` opcję . Ta opcja zapewnia, że zdalny użytkownik główny może uzyskać dostęp do plików należących do katalogu głównego.

  * Sprawdź zasady eksportu, aby upewnić się, że nie obejmują one ograniczeń dostępu głównego z podsieci pamięci podręcznej.

  * Jeśli magazyn ma eksporty, które są podkatalogami innego eksportu, upewnij się, że pamięć podręczna ma dostęp główny do najniższego segmentu ścieżki. Aby [uzyskać szczegółowe informacje, przeczytaj](troubleshoot-nas.md#allow-root-access-on-directory-paths) artykuł na temat rozwiązywania problemów z dostępem głównym do ścieżek katalogów w docelowym magazynie systemu plików NFS.

* Magazyn końcowy systemu plików NFS musi być zgodną platformą sprzętową/programową. Skontaktuj się z zespołem Azure HPC Cache, aby uzyskać szczegółowe informacje.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements-preview"></a>Wymagania dotyczące magazynu obiektów blob zainstalowanych w ramach systemu plików NFS (ADLS-NFS) (wersja zapoznawcza)

Azure HPC Cache można również użyć kontenera obiektów blob zainstalowanego przy użyciu protokołu NFS jako miejsca docelowego magazynu.

> [!NOTE]
> Obsługa protokołu NFS 3.0 dla usługi Azure Blob Storage jest w publicznej wersji zapoznawczej. Dostępność jest ograniczona, a funkcje mogą ulec zmianie od teraz do czasu, gdy funkcja stanie się ogólnie dostępna. Nie używaj technologii w wersji zapoznawczej w systemach produkcyjnych.
>
> Przeczytaj więcej na temat tej funkcji w wersji zapoznawczej w systemie obsługi protokołu [NFS 3.0 w usłudze Azure Blob Storage.](../storage/blobs/network-file-system-protocol-support.md)

Wymagania dotyczące konta magazynu różnią się w przypadku obiektu docelowego magazynu obiektów blob ADLS-NFS i standardowego obiektu docelowego magazynu obiektów blob. Postępuj zgodnie z instrukcjami w te tematu Mount [Blob storage by using the Network File System (NFS) 3.0 protocol](../storage/blobs/network-file-system-protocol-support-how-to.md) carefully to create and configure the NFS-enabled storage account (Dokładne instrukcje instalacji usługi Blob Storage przy użyciu protokołu NFS 3.0), aby utworzyć i skonfigurować konto magazynu z obsługą systemu plików NFS.

To jest ogólne omówienie kroków. Te kroki mogą ulec zmianie, dlatego bieżące szczegóły należy zawsze znaleźć w [instrukcjach adls-NFS.](../storage/blobs/network-file-system-protocol-support-how-to.md)

1. Upewnij się, że potrzebne funkcje są dostępne w regionach, w których planujesz pracować.

1. Włącz funkcję protokołu NFS dla subskrypcji. Zrób to *przed* utworzeniem konta magazynu.

1. Utwórz bezpieczną sieć wirtualną dla konta magazynu. Należy użyć tej samej sieci wirtualnej dla konta magazynu z obsługą systemu plików NFS i dla Azure HPC Cache. (Nie używaj tej samej podsieci co pamięć podręczna).

1. Utwórz konto magazynu.

   * Zamiast używać ustawień konta magazynu dla standardowego konta magazynu obiektów blob, postępuj zgodnie z instrukcjami w dokumencie z [instrukcjami.](../storage/blobs/network-file-system-protocol-support-how-to.md) Obsługiwany typ konta magazynu może różnić się w zależności od regionu świadczenia usługi Azure.

   * W sekcji Sieć wybierz prywatny punkt końcowy w utworzonej bezpiecznej sieci wirtualnej (zalecane) lub wybierz publiczny punkt końcowy z ograniczonym dostępem z bezpiecznej sieci wirtualnej.

   * Nie zapomnij ukończyć sekcji Zaawansowane, w której jest włączany dostęp do systemu plików NFS.

   * Nadaj aplikacji pamięci podręcznej dostęp do konta usługi Azure Storage zgodnie z powyższymi [uprawnieniami.](#permissions) Można to zrobić przy pierwszym utworzeniu obiektu docelowego magazynu. Postępuj zgodnie z procedurą w [tece Dodawanie obiektów docelowych magazynu,](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) aby zapewnić pamięci podręcznej wymagane role dostępu.

     Jeśli nie jesteś właścicielem konta magazynu, wykonaj ten krok.

Dowiedz się więcej na temat używania obiektów docelowych magazynu ADLS-NFS z Azure HPC Cache w teście [Use NFS-mounted blob storage with Azure HPC Cache](nfs-blob-considerations.md)( Używanie magazynu obiektów blob zainstalowanego w ramach systemu plików NFS z Azure HPC Cache.

## <a name="set-up-azure-cli-access-optional"></a>Konfigurowanie dostępu do interfejsu wiersza polecenia platformy Azure (opcjonalnie)

Jeśli chcesz utworzyć interfejs wiersza polecenia platformy Azure Azure HPC Cache zarządzać nimi z poziomu interfejsu wiersza polecenia platformy Azure, musisz zainstalować oprogramowanie interfejsu wiersza polecenia i rozszerzenie hpc-cache. Postępuj zgodnie z instrukcjami [w te tematu Konfigurowanie interfejsu wiersza polecenia platformy Azure dla Azure HPC Cache](az-cli-prerequisites.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie wystąpienia Azure HPC Cache na](hpc-cache-create.md) Azure Portal
