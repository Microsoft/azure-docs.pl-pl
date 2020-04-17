---
title: Wymagania wstępne dotyczące pamięci podręcznej HPC platformy Azure
description: Wymagania wstępne dotyczące korzystania z pamięci podręcznej HPC usługi Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: ab342dba5c8be2ff3793c0eb36926969b3e364e5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537291"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Wymagania wstępne dotyczące pamięci podręcznej HPC usługi Azure

Przed użyciem witryny Azure Portal do utworzenia nowej pamięci podręcznej HPC platformy Azure upewnij się, że środowisko spełnia te wymagania.

## <a name="azure-subscription"></a>Subskrypcja platformy Azure

Zalecana jest płatna subskrypcja.

## <a name="network-infrastructure"></a>Infrastruktura sieciowa

Przed użyciem pamięci podręcznej należy skonfigurować dwa wymagania wstępne związane z siecią:

* Dedykowana podsieć dla wystąpienia pamięci podręcznej HPC platformy Azure
* Obsługa systemu DNS, dzięki czemu pamięć podręczna może uzyskiwać dostęp do magazynu i innych zasobów

### <a name="cache-subnet"></a>Podsieć pamięci podręcznej

Pamięć podręczna HPC platformy Azure wymaga dedykowanej podsieci o następujących cechach:

* Podsieć musi mieć co najmniej 64 adresy IP.
* Podsieć nie może obsługiwać żadnych innych maszyn wirtualnych, nawet dla powiązanych usług, takich jak komputery klienckie.
* Jeśli używasz wielu wystąpień pamięci podręcznej HPC platformy Azure, każdy z nich potrzebuje własnej podsieci.

Najlepszym rozwiązaniem jest utworzenie nowej podsieci dla każdej pamięci podręcznej. Można utworzyć nową sieć wirtualną i podsieć w ramach tworzenia pamięci podręcznej.

### <a name="dns-access"></a>Dostęp do systemu DNS

Pamięć podręczna potrzebuje systemu DNS, aby uzyskać dostęp do zasobów poza siecią wirtualną. W zależności od używanych zasobów może być konieczne skonfigurowanie dostosowanego serwera DNS i skonfigurowanie przekazywania dalej między tym serwerem a serwerami DNS platformy Azure:

* Aby uzyskać dostęp do punktów końcowych magazynu obiektów Blob platformy Azure i innych zasobów wewnętrznych, potrzebny jest serwer DNS oparty na platformie Azure.
* Aby uzyskać dostęp do magazynu lokalnego, należy skonfigurować niestandardowy serwer DNS, który może rozpoznać nazwy hostów magazynu.

Jeśli potrzebujesz tylko dostępu do magazynu obiektów Blob, możesz użyć domyślnego serwera DNS dostarczonego przez platformę Azure dla pamięci podręcznej. Jeśli jednak potrzebujesz dostępu do innych zasobów, należy utworzyć niestandardowy serwer DNS i skonfigurować go do przesyłania dalej wszelkich żądań rozpoznawania specyficznych dla platformy Azure do serwera DNS platformy Azure.

Prosty serwer DNS może również służyć do równoważenia połączeń klientów między wszystkimi dostępnymi punktami instalacji pamięci podręcznej.

Dowiedz się więcej o sieciach wirtualnych platformy Azure i konfiguracjach serwerów DNS w [rozpoznawaniu nazw dla zasobów w sieciach wirtualnych platformy Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)

## <a name="permissions"></a>Uprawnienia

Sprawdź te wymagania wstępne związane z uprawnieniami przed rozpoczęciem tworzenia pamięci podręcznej.

* Wystąpienie pamięci podręcznej musi mieć możliwość tworzenia interfejsów sieci wirtualnej (NIC). Użytkownik, który tworzy pamięć podręczną musi mieć wystarczające uprawnienia w subskrypcji do tworzenia kart sieciowych.

* W przypadku korzystania z magazynu obiektów Blob pamięć podręczna HPC wymaga autoryzacji, aby uzyskać dostęp do konta magazynu. Użyj kontroli dostępu opartej na rolach (RBAC), aby nadać pamięci podręcznej dostęp do magazynu obiektów Blob. Wymagane są dwie role: współautor konta magazynu i współautor danych obiektów blob magazynu.

  Postępuj zgodnie z instrukcjami w [Dodaj obiekty docelowe magazynu,](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) aby dodać role.

## <a name="storage-infrastructure"></a>Infrastruktura pamięci masowej

Pamięć podręczna obsługuje kontenery obiektów Blob platformy Azure lub eksportowanie sprzętowej pamięci masowej systemu Plików NFS. Dodaj obiekty docelowe magazynu po utworzeniu pamięci podręcznej.

Każdy typ magazynu ma określone wymagania wstępne.

### <a name="blob-storage-requirements"></a>Wymagania dotyczące magazynu obiektów blob

Jeśli chcesz używać magazynu obiektów Blob platformy Azure z pamięcią podręczną, potrzebujesz zgodnego konta magazynu i pustego kontenera obiektów Blob lub kontenera wypełnionego danymi sformatowanymi w pamięci podręcznej HPC usługi Azure, zgodnie z opisem w [obszarze Przenieś dane do magazynu obiektów Blob platformy Azure.](hpc-cache-ingest.md)

Utwórz konto przed podjęciem próby dodania obiektu docelowego magazynu. Można utworzyć nowy kontener po dodaniu obiektu docelowego.

Aby utworzyć zgodne konto magazynu, użyj następujących ustawień:

* Wydajność: **Standard**
* Rodzaj konta: **StorageV2 (ogólnego przeznaczenia v2)**
* Replikacja: **Magazyn nadmiarowy lokalnie (LRS)**
* Warstwa dostępu (domyślnie): **Gorąca**

Dobrą praktyką jest używanie konta magazynu w tej samej lokalizacji co pamięć podręczna.

Należy również dać aplikacji pamięci podręcznej dostęp do konta usługi Azure storage, jak wspomniano w [uprawnienia powyżej.](#permissions) Wykonaj procedurę w [Dodaj obiekty docelowe magazynu,](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) aby nadać pamięci podręcznej wymagane role dostępu. Jeśli nie jesteś właścicielem konta magazynu, niech właściciel wykonaj ten krok.

### <a name="nfs-storage-requirements"></a>Wymagania dotyczące pamięci masowej nfs
<!-- linked from configuration.md -->

Jeśli używasz systemu pamięci masowej NFS (na przykład lokalnego sprzętowego systemu NAS), upewnij się, że spełnia te wymagania. W celu zweryfikowania tych ustawień może być konieczna współpraca z administratorami sieci lub menedżerami zapór dla systemu pamięci masowej (lub centrum danych).

> [!NOTE]
> Tworzenie obiektu docelowego magazynu zakończy się niepowodzeniem, jeśli pamięć podręczna nie ma wystarczającego dostępu do systemu magazynu NFS.

Więcej informacji znajduje się w [rozwiązaniu problemu z konfiguracją serwera NAS i problemami z docelowymi pamięciami masowymi systemu Plików NFS](troubleshoot-nas.md).

* **Łączność sieciowa:** Pamięć podręczna HPC platformy Azure wymaga dostępu do sieci o dużej przepustowości między podsiecią pamięci podręcznej a centrum danych systemu NFS. Zaleca się [wprowadzenie usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/) lub podobny dostęp. W przypadku korzystania z sieci VPN może być konieczne skonfigurowanie go do zaciskania protokołu TCP MSS w 1350, aby upewnić się, że duże pakiety nie są blokowane. Przeczytaj [ograniczenia rozmiaru pakietów sieci VPN,](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) aby uzyskać dodatkową pomoc w rozwiązywaniu problemów z ustawieniami sieci VPN.

* **Dostęp do portu:** Pamięć podręczna wymaga dostępu do określonych portów TCP/UDP w systemie pamięci masowej. Różne typy pamięci masowej mają różne wymagania dotyczące portów.

  Aby sprawdzić ustawienia systemu pamięci masowej, wykonaj tę procedurę.

  * Wydaj `rpcinfo` polecenie do systemu pamięci masowej, aby sprawdzić potrzebne porty. Poniższe polecenie zawiera listę portów i formatuje odpowiednie wyniki w tabeli. (Użyj adresu IP systemu zamiast *<storage_IP>* terminem).

    To polecenie można wydać z dowolnego klienta systemu Linux z zainstalowaną infrastrukturą NFS. Jeśli używasz klienta wewnątrz podsieci klastra, może również pomóc weryfikowaniu łączności między podsiecią a systemem magazynowania.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Upewnij się, że wszystkie porty zwrócone przez kwerendę zezwalają ``rpcinfo`` na nieograniczony ruch z podsieci pamięci podręcznej HPC platformy Azure.

  * Jeśli nie możesz użyć `rpcinfo` tego polecenia, upewnij się, że te powszechnie używane porty zezwalają na ruch przychodzący i wychodzący:

    | Protocol (Protokół) | Port  | Usługa  |
    |----------|-------|----------|
    | Protokół TCP/UDP  | 111   | rpcbind  |
    | Protokół TCP/UDP  | 2049  | NFS      |
    | Protokół TCP/UDP  | 4045  | nlockmgr |
    | Protokół TCP/UDP  | 4046  | zamontowany   |
    | Protokół TCP/UDP  | 4047  | status   |

    Niektóre systemy używają różnych numerów portów dla tych usług - zapoznaj się z dokumentacją systemu pamięci masowej, aby upewnić się.

  * Sprawdź ustawienia zapory, aby upewnić się, że zezwalają one na ruch na wszystkich wymaganych portach. Sprawdź zapory używane na platformie Azure, a także zapory lokalne w centrum danych.

* **Dostęp do katalogu:** Włącz `showmount` polecenie w systemie pamięci masowej. Usługa HpC Cache usługi Azure używa tego polecenia, aby sprawdzić, czy konfiguracja docelowego magazynu wskazuje prawidłowy eksport, a także upewnić się, że wiele instalacji nie ma dostępu do tych samych podkatalogów (ryzyko kolizji pliku).

  > [!NOTE]
  > Jeśli system pamięci masowej NFS korzysta z systemu operacyjnego ONTAP 9.2 netapp, **nie włączaj `showmount` **. Aby uzyskać pomoc, [skontaktuj się z pomocą techniczną firmy Microsoft.](hpc-cache-support-ticket.md)

  Dowiedz się więcej o dostępie do listy katalogów w [artykule rozwiązywanie problemów z](troubleshoot-nas.md#enable-export-listing)docelowym magazynem plików NFS .

* **Dostęp do roota** (odczyt/zapis): Pamięć podręczna łączy się z systemem zaplecza jako identyfikator użytkownika 0. Sprawdź te ustawienia w systemie pamięci masowej:
  
  * Włącz `no_root_squash`plik . Ta opcja gwarantuje, że zdalny użytkownik root może uzyskać dostęp do plików należących do roota.

  * Sprawdź zasady eksportu, aby upewnić się, że nie zawierają ograniczeń dostępu administratora z podsieci pamięci podręcznej.

  * Jeśli magazyn ma żadnych eksportu, które są podkatalogami innego eksportu, upewnij się, że pamięć podręczna ma dostęp do katalogu głównego do najniższego segmentu ścieżki. Przeczytaj [dostęp administratora do ścieżek katalogów](troubleshoot-nas.md#allow-root-access-on-directory-paths) w artykule rozwiązywania problemów z docelowym magazynem NFS, aby uzyskać szczegółowe informacje.

* Pamięć masowa nfs musi być kompatybilną platformą sprzętową/programową. Aby uzyskać szczegółowe informacje, skontaktuj się z zespołem usługi Azure HPC Cache.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie wystąpienia pamięci podręcznej HPC platformy Azure](hpc-cache-create.md) z witryny Azure portal
