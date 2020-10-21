---
title: Wymagania wstępne dotyczące pamięci podręcznej platformy Azure HPC
description: Wymagania wstępne dotyczące korzystania z pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/03/2020
ms.author: v-erkel
ms.openlocfilehash: 39c890e9e6634413947a96b1ddac608ee44bd9a0
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92327403"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Wymagania wstępne dotyczące usługi Azure HPC cache

Przed rozpoczęciem korzystania z Azure Portal tworzenia nowej pamięci podręcznej platformy Azure HPC upewnij się, że środowisko spełnia te wymagania.

## <a name="video-overviews"></a>Przeglądy wideo

Obejrzyj te filmy wideo, aby zapoznać się z krótkim omówieniem składników systemu oraz o tym, czego potrzebują.

(Kliknij obraz wideo lub link, aby obejrzeć).

* [Jak to działa](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) — objaśnia sposób współdziałania pamięci podręcznej platformy Azure HPC z magazynem i klientami

  [![obraz miniatury wideo: Azure HPC cache: jak działa (kliknij, aby odwiedzić stronę wideo)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [Wymagania wstępne](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) — opis wymagań dotyczących magazynu serwera nas, usługi Azure Blob Storage, dostępu do sieci i dostępu klienta

  [![obraz miniatury wideo: Azure HPC cache: wymagania wstępne (kliknij, aby odwiedzić stronę wideo)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

Zapoznaj się z pozostałą częścią tego artykułu, aby uzyskać szczegółowe zalecenia.

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Zalecana jest płatna subskrypcja.

## <a name="network-infrastructure"></a>Infrastruktura sieciowa

Aby można było korzystać z pamięci podręcznej, należy skonfigurować dwa wymagania wstępne związane z siecią:

* Dedykowana podsieć dla wystąpienia pamięci podręcznej platformy Azure HPC
* Obsługa systemu DNS, aby pamięć podręczna mogła uzyskać dostęp do magazynu i innych zasobów

### <a name="cache-subnet"></a>Podsieć pamięci podręcznej

Pamięć podręczna Azure HPC wymaga dedykowanej podsieci z następującymi jakościami:

* Podsieć musi mieć co najmniej 64 adresów IP.
* Podsieć nie może obsługiwać żadnych innych maszyn wirtualnych, nawet w przypadku powiązanych usług takich jak komputery klienckie.
* W przypadku korzystania z wielu wystąpień pamięci podręcznej platformy Azure HPC każda z nich musi mieć własną podsieć.

Najlepszym rozwiązaniem jest utworzenie nowej podsieci dla każdej pamięci podręcznej. W ramach tworzenia pamięci podręcznej można utworzyć nową sieć wirtualną i podsieć.

### <a name="dns-access"></a>Dostęp do usługi DNS

Pamięć podręczna potrzebuje systemu DNS, aby uzyskać dostęp do zasobów poza jego siecią wirtualną. W zależności od zasobów, z których korzystasz, może być konieczne skonfigurowanie niestandardowego serwera DNS i skonfigurowanie przekierowania między tym serwerem i serwerami Azure DNS:

* Aby uzyskać dostęp do punktów końcowych usługi Azure Blob Storage i innych zasobów wewnętrznych, wymagany jest serwer DNS oparty na platformie Azure.
* Aby uzyskać dostęp do magazynu lokalnego, należy skonfigurować niestandardowy serwer DNS, który może rozpoznawać nazwy hostów magazynu.

Jeśli potrzebujesz dostępu do usługi BLOB Storage, możesz użyć domyślnego serwera DNS z systemem Azure dla pamięci podręcznej. Jeśli jednak potrzebujesz dostępu do innych zasobów, należy utworzyć niestandardowy serwer DNS i skonfigurować go tak, aby przesyłał do serwera Azure DNS wszystkie żądania rozpoznawania specyficzne dla platformy Azure.

Prosty serwer DNS może być również używany do równoważenia obciążenia połączeń klientów między wszystkimi dostępnymi punktami instalacji pamięci podręcznej.

Dowiedz się więcej na temat sieci wirtualnych platformy Azure i konfiguracji serwera DNS w temacie [rozpoznawanie nazw dla zasobów w sieciach wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Uprawnienia

Przed rozpoczęciem tworzenia pamięci podręcznej Sprawdź te wymagania wstępne związane z uprawnieniami.

* Wystąpienie pamięci podręcznej musi być w stanie tworzyć wirtualne interfejsy sieciowe (nic). Użytkownik tworzący pamięć podręczną musi mieć wystarczające uprawnienia w subskrypcji, aby można było tworzyć karty sieciowe.

* W przypadku korzystania z usługi BLOB Storage pamięć podręczna Azure HPC wymaga autoryzacji dostępu do konta magazynu. Aby zapewnić dostęp do pamięci podręcznej do magazynu obiektów blob, użyj kontroli dostępu opartej na rolach (Azure RBAC). Wymagane są dwie role: współautor konta magazynu i współautor danych obiektu blob magazynu.

  Postępuj zgodnie z instrukcjami w temacie [Dodawanie elementów docelowych magazynu](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) , aby dodać role.

## <a name="storage-infrastructure"></a>Infrastruktura magazynu

Pamięć podręczna obsługuje kontenery obiektów blob platformy Azure lub eksporty magazynu sprzętowego systemu plików NFS. Dodaj elementy docelowe magazynu po utworzeniu pamięci podręcznej.

Każdy typ magazynu ma określone wymagania wstępne.

### <a name="blob-storage-requirements"></a>Wymagania dotyczące magazynu obiektów BLOB

Jeśli chcesz używać usługi Azure Blob Storage z pamięcią podręczną, potrzebujesz zgodnego konta magazynu i pustego kontenera obiektów blob lub kontenera, który jest wypełniony danymi z pamięci podręcznej platformy Azure HPC, zgodnie z opisem w sekcji [przenoszenie danych do usługi Azure Blob Storage](hpc-cache-ingest.md).

Utwórz konto przed podjęciem próby dodania miejsca docelowego magazynu. Nowy kontener można utworzyć po dodaniu obiektu docelowego.

Aby utworzyć zgodne konto magazynu, użyj następujących ustawień:

* Wydajność: **standardowa**
* Rodzaj konta: **StorageV2 (ogólnego przeznaczenia w wersji 2)**
* Replikacja: **Magazyn lokalnie nadmiarowy (LRS)**
* Warstwa dostępu (domyślna): **gorąca**

Dobrym sposobem jest użycie konta magazynu w tej samej lokalizacji co pamięć podręczna.

Należy również nadać aplikacji pamięci podręcznej dostęp do konta usługi Azure [Storage, jak](#permissions)wspomniano powyżej. Postępuj zgodnie z procedurą w temacie [Dodawanie miejsc docelowych](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) w celu nadania pamięci podręcznej wymaganych ról dostępu. Jeśli nie jesteś właścicielem konta magazynu, jego właścicielem jest ten krok.

### <a name="nfs-storage-requirements"></a>Wymagania dotyczące magazynu NFS
<!-- linked from configuration.md -->

W przypadku korzystania z systemu magazynu NFS (na przykład lokalnego sprzętowego serwera NAS) Upewnij się, że spełnia on te wymagania. Aby sprawdzić te ustawienia, może być konieczne skontaktowanie się z administratorami sieci lub menedżerami zapory dla systemu magazynu (lub centrum danych).

> [!NOTE]
> Tworzenie miejsca docelowego magazynu zakończy się niepowodzeniem, jeśli pamięć podręczna ma niewystarczający dostęp do systemu magazynu NFS.

Więcej informacji znajduje się w temacie [Rozwiązywanie problemów z konfiguracją serwera nas i miejsca docelowego magazynu NFS](troubleshoot-nas.md).

* **Łączność sieciowa:** Pamięć podręczna Azure HPC wymaga dostępu do sieci o wysokiej przepustowości między podsiecią pamięci podręcznej a centrum danych systemu plików NFS. Zalecany jest [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) lub podobny dostęp. W przypadku korzystania z sieci VPN może być konieczne skonfigurowanie jej w celu zablokowania protokołu TCP o rozmiarze 1350, aby upewnić się, że duże pakiety nie są blokowane. Odczytaj [ograniczenia rozmiaru pakietów sieci VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) , aby uzyskać dodatkową pomoc w rozwiązywaniu problemów z ustawieniami sieci VPN.

* **Dostęp do portów:** Pamięć podręczna musi mieć dostęp do określonych portów TCP/UDP w systemie magazynu. Różne typy magazynów mają różne wymagania dotyczące portów.

  Aby sprawdzić ustawienia systemu magazynu, wykonaj poniższą procedurę.

  * Wydaj `rpcinfo` polecenie do systemu magazynu w celu sprawdzenia wymaganych portów. Poniższe polecenie wyświetla listę portów i formatuje odpowiednie wyniki w tabeli. (Użyj adresu IP systemu zamiast *<storage_IP>* termin).

    To polecenie można wydać z dowolnego klienta systemu Linux z zainstalowaną infrastrukturą NFS. Jeśli używasz klienta w podsieci klastra, może on również pomóc w sprawdzeniu łączności między podsiecią a systemem magazynu.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Upewnij się, że wszystkie porty zwrócone przez ``rpcinfo`` zapytanie zezwalają na nieograniczony ruch z podsieci platformy Azure HPC.

  * Jeśli nie możesz użyć `rpcinfo` polecenia, upewnij się, że te najczęściej używane porty zezwalają na ruch przychodzący i wychodzący:

    | Protokół | Port  | Usługa  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | Instalacja   |
    | TCP/UDP  | 4047  | status   |

    Niektóre systemy używają innych numerów portów dla tych usług — zapoznaj się z dokumentacją systemu magazynu, aby upewnić się, że.

  * Sprawdź ustawienia zapory, aby upewnić się, że zezwalają na ruch na wszystkich wymaganych portach. Pamiętaj, aby sprawdzić zapory używane na platformie Azure oraz zapory lokalne w centrum danych.

* **Dostęp do katalogu:** Włącz `showmount` polecenie w systemie magazynu. Pamięć podręczna Azure HPC korzysta z tego polecenia, aby sprawdzić, czy konfiguracja docelowa magazynu wskazuje na prawidłowy eksport, a także upewnić się, że wiele instalacji nie uzyskuje dostępu do tych samych podkatalogów (ryzyko kolizji plików).

  > [!NOTE]
  > Jeśli system magazynu NFS używa systemu operacyjnego NetApp ONTAP 9,2, nie **należy włączać `showmount` **. [Skontaktuj się z pomocą techniczną firmy Microsoft,](hpc-cache-support-ticket.md) Aby uzyskać pomoc.

  Dowiedz się więcej o dostępie do listy katalogów w [artykule dotyczącym rozwiązywania problemów](troubleshoot-nas.md#enable-export-listing)z miejscem DOCELOWYM magazynu NFS.

* **Dostęp do katalogu głównego** (odczyt/zapis): pamięć podręczna łączy się z systemem zaplecza jako identyfikator użytkownika 0. Sprawdź te ustawienia w systemie magazynu:
  
  * Włącz `no_root_squash` . Ta opcja zapewnia, że zdalny użytkownik główny może uzyskać dostęp do plików należących do katalogu głównego.

  * Zaznacz pole wyboru Eksportuj zasady, aby upewnić się, że nie obejmują one ograniczeń dotyczących dostępu głównego z podsieci pamięci podręcznej.

  * Jeśli magazyn zawiera jakiekolwiek eksporty, które są podkatalogami innego eksportu, upewnij się, że pamięć podręczna ma dostęp do najniższego segmentu ścieżki. Aby uzyskać szczegółowe informacje, Przeczytaj [temat dostęp do katalogu głównego w ścieżkach katalogów](troubleshoot-nas.md#allow-root-access-on-directory-paths) w artykule dotyczącym rozwiązywania problemów dotyczących magazynu NFS.

* Magazyn zaplecza systemu plików NFS musi być zgodnym sprzętem/platformą oprogramowania. Aby uzyskać szczegółowe informacje, skontaktuj się z zespołem usługi Azure HPC cache.

## <a name="set-up-azure-cli-access-optional"></a>Konfigurowanie dostępu do interfejsu wiersza polecenia platformy Azure (opcjonalnie)

Jeśli chcesz utworzyć pamięć podręczną platformy Azure HPC z poziomu interfejsu wiersza polecenia platformy Azure (Azure CLI) lub zarządzać nią, musisz zainstalować oprogramowanie CLI i rozszerzenie pamięci podręcznej HPC. Postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie interfejsu wiersza polecenia platformy Azure dla usługi Azure HPC cache](az-cli-prerequisites.md).

## <a name="next-steps"></a>Następne kroki

* [Utwórz wystąpienie pamięci podręcznej platformy Azure HPC](hpc-cache-create.md) na podstawie Azure Portal
