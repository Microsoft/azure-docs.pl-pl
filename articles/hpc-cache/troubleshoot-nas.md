---
title: Rozwiązywanie problemów z pamięcią podręczną magazynu systemu Azure HPC
description: Porady umożliwiające uniknięcie i rozwiązywanie błędów konfiguracji oraz innych problemów, które mogą spowodować niepowodzenie podczas tworzenia miejsca docelowego magazynu NFS
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: ed31a2202a0eacbdc257e127e111d21215c27de4
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258201"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Rozwiązywanie problemów z konfiguracją serwera NAS i miejscem docelowym magazynu NFS

Ten artykuł zawiera rozwiązania niektórych typowych błędów konfiguracji i inne problemy, które mogą uniemożliwić Dodawanie systemu magazynu NFS do pamięci podręcznej usługi Azure HPC jako miejsca docelowego magazynu.

Ten artykuł zawiera szczegółowe informacje na temat sprawdzania portów i sposobu włączania dostępu głównego do systemu NAS. Zawiera również szczegółowe informacje o mniejszych typowych problemach, które mogą spowodować niepowodzenie tworzenia miejsca docelowego magazynu NFS.

> [!TIP]
> Przed rozpoczęciem korzystania z tego przewodnika zapoznaj się z [wymaganiami wstępnymi dotyczącymi magazynu NFS](hpc-cache-prerequisites.md#nfs-storage-requirements).

Jeśli rozwiązanie do problemu nie zostało uwzględnione w tym miejscu, [Otwórz bilet pomocy technicznej](hpc-cache-support-ticket.md) , aby usługa i pomoc techniczna firmy Microsoft mogły współdziałać z nim w celu zbadania i rozwiązania problemu.

## <a name="check-port-settings"></a>Sprawdź ustawienia portu

Pamięć podręczna platformy Azure HPC wymaga dostępu do odczytu/zapisu do kilku portów UDP/TCP w systemie magazynu NAS zaplecza. Upewnij się, że te porty są dostępne w systemie NAS oraz że ruch jest dozwolony dla tych portów za pomocą wszelkich zapór między systemem magazynu a podsiecią pamięci podręcznej. Aby można było zweryfikować tę konfigurację, może być konieczne skontaktowanie się z zaporą i administratorami sieci dla centrum danych.

Porty różnią się w przypadku systemów magazynowania od różnych dostawców, dlatego należy sprawdzić wymagania systemu podczas konfigurowania miejsca docelowego magazynu.

Ogólnie rzecz biorąc pamięć podręczna musi mieć dostęp do tych portów:

| Protokół | Port  | Usługa  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | Instalacja   |
| TCP/UDP  | 4047  | status   |

Aby uzyskać informacje o określonych portach wymaganych przez system, użyj następującego ``rpcinfo`` polecenia. Poniższe polecenie wyświetla listę portów i formatuje odpowiednie wyniki w tabeli. (Użyj adresu IP systemu zamiast *<storage_IP>* termin).

To polecenie można wydać z dowolnego klienta systemu Linux z zainstalowaną infrastrukturą NFS. Jeśli używasz klienta w podsieci klastra, może on również pomóc w sprawdzeniu łączności między podsiecią a systemem magazynu.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Upewnij się, że wszystkie porty zwrócone przez ``rpcinfo`` zapytanie zezwalają na nieograniczony ruch z podsieci platformy Azure HPC.

Sprawdź te ustawienia zarówno na serwerze NAS, jak i na wszystkich zaporach między systemem magazynu a podsiecią pamięci podręcznej.

## <a name="check-root-access"></a>Sprawdź dostęp do katalogu głównego

Pamięć podręczna Azure HPC wymaga dostępu do eksportów systemu magazynu w celu utworzenia miejsca docelowego magazynu. W odniesieniu do tego należy zainstalować eksporty jako identyfikator użytkownika 0.

Różne systemy magazynu używają różnych metod do włączenia tego dostępu:

* Serwery z systemem Linux ``no_root_squash`` są zazwyczaj dodawane do wyeksportowanej ścieżki w programie ``/etc/exports`` .
* Systemy NetApp i EMC zazwyczaj kontrolują dostęp z regułami eksportu, które są powiązane z określonymi adresami IP lub sieciami.

W przypadku używania reguł eksportu należy pamiętać, że pamięć podręczna może używać wielu różnych adresów IP z podsieci pamięci podręcznej. Zezwalaj na dostęp z pełnego zakresu możliwych adresów IP podsieci.

> [!NOTE]
> Mimo że pamięć podręczna wymaga dostępu głównego do systemu magazynu zaplecza, można ograniczyć dostęp dla klientów łączących się za pomocą pamięci podręcznej. Aby uzyskać szczegółowe informacje, zobacz [Kontrola dostępu klienta](access-policies.md#root-squash) .

Skontaktuj się z dostawcą magazynu NAS, aby włączyć odpowiedni poziom dostępu do pamięci podręcznej.

### <a name="allow-root-access-on-directory-paths"></a>Zezwalaj na dostęp do katalogu głównego w ścieżkach katalogów
<!-- linked in prereqs article -->

W przypadku systemów NAS, które eksportują katalogi hierarchiczne, pamięć podręczna Azure HPC wymaga dostępu głównego do poszczególnych poziomów eksportu.

Na przykład system może pokazać trzy eksporty podobne do następujących:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Eksport ``/ifs/accounting/payroll`` jest elementem podrzędnym ``/ifs/accounting`` i ``/ifs/accounting`` jest samym elementem podrzędnym ``/ifs`` .

W przypadku dodania ``payroll`` eksportu jako miejsca docelowego magazynu pamięci podręcznej HPC pamięć podręczna jest instalowana ``/ifs/`` i uzyskuje dostęp do katalogu listy płac. Aby uzyskać dostęp do eksportu, pamięć podręczna Azure HPC musi mieć dostęp do katalogu głównego ``/ifs`` ``/ifs/accounting/payroll`` .

Ten wymóg jest związany ze sposobem, w jaki pamięć podręczna indeksuje pliki i unika kolizji plików, przy użyciu uchwytów plików udostępnianych przez system magazynu.

System NAS z hierarchicznymi eksportami może dać różne dojścia do plików dla tego samego pliku, jeśli plik jest pobierany z różnych eksportów. Na przykład klient może zainstalować ``/ifs/accounting`` plik i uzyskać do niego dostęp ``payroll/2011.txt`` . Inny klient instaluje ``/ifs/accounting/payroll`` i uzyskuje dostęp do pliku ``2011.txt`` . W zależności od sposobu przypisywania dojść do plików przez system magazynu te dwa komputery klienckie mogą otrzymać ten sam plik z różnymi dojściami do plików (jeden dla ``<mount2>/payroll/2011.txt`` i jeden dla ``<mount3>/2011.txt`` ).

System magazynu zaplecza przechowuje wewnętrzne aliasy dojść do plików, ale pamięć podręczna platformy Azure HPC nie może określić, które uchwyty plików w jego indeksie odwołują się do tego samego elementu. Dlatego istnieje możliwość, że pamięć podręczna może mieć różne zapisy w pamięci podręcznej dla tego samego pliku i zastosować zmiany niepoprawnie, ponieważ nie wie, że są one tego samego pliku.

Aby uniknąć tej potencjalnej kolizji plików w przypadku plików w wielu eksportach, pamięć podręczna Azure HPC automatycznie instaluje skrócony dostępny eksport w ścieżce ( ``/ifs`` w przykładzie) i używa dojścia do pliku podawanego przez ten eksport. Jeśli wielokrotne eksporty używają tej samej ścieżki podstawowej, pamięć podręczna Azure HPC potrzebuje dostępu głównego do tej ścieżki.

<!-- ## Enable export listing

The NAS must list its exports when the Azure HPC Cache queries it.

On most NFS storage systems, you can test this by sending the following query from a Linux client: ``showmount -e <storage IP address>``

Use a Linux client from the same virtual network as your cache, if possible.

If that command doesn't list the exports, the cache will have trouble connecting to your storage system. Work with your NAS vendor to enable export listing.  -->

## <a name="adjust-vpn-packet-size-restrictions"></a>Dostosowywanie ograniczeń rozmiaru pakietu sieci VPN
<!-- link in prereqs article and configuration article -->

W przypadku sieci VPN między pamięcią podręczną a urządzeniem NAS, Sieć VPN może blokować pełny rozmiar 1500-bajtowych pakietów Ethernet. Ten problem może wystąpić, jeśli duże wymiany między serwerem NAS i wystąpieniem pamięci podręcznej platformy Azure HPC nie zostaną ukończone, ale mniejsze aktualizacje działają zgodnie z oczekiwaniami.

Nie istnieje prosty sposób, aby stwierdzić, czy system ma ten problem, chyba że znasz szczegółowe informacje o konfiguracji sieci VPN. Oto kilka metod, które mogą pomóc w sprawdzeniu tego problemu.

* Korzystając ze detektorów pakietów po obu stronach sieci VPN, można wykryć, które pakiety zostały pomyślnie przeniesione.
* Jeśli sieć VPN zezwala na polecenia ping, można testować wysyłanie pakietu o pełnym rozmiarze.

  Uruchom polecenie ping za pośrednictwem sieci VPN do serwera NAS, korzystając z tych opcji. (Użyj adresu IP systemu magazynu zamiast *storage_IP<wartość>* ).

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Oto opcje polecenia:

  * ``-M do`` -Nie fragmentuj
  * ``-c 1`` -Wyślij tylko jeden pakiet
  * ``-s 1472`` -Ustaw rozmiar ładunku na 1472 bajtów. Jest to maksymalny rozmiar ładunku 1500-bajtowego po rozliczeniu za obciążenie sieci Ethernet.

  Odpowiedź oznaczająca powodzenie wygląda następująco:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Jeśli polecenie ping kończy się niepowodzeniem z 1472 bajtów, prawdopodobnie wystąpił problem z rozmiarem pakietu.

Aby rozwiązać ten problem, może być konieczne skonfigurowanie ograniczania rozmiaru do sieci VPN w celu zapewnienia, że system zdalny prawidłowo wykryje maksymalny rozmiar ramki. Przeczytaj [dokumentację VPN Gateway protokołu IPSec/IKE](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) , aby dowiedzieć się więcej.

W niektórych przypadkach zmiana ustawienia MTU dla pamięci podręcznej platformy Azure HPC na 1400 może pomóc. Jednakże w przypadku ograniczenia jednostki MTU w pamięci podręcznej należy również ograniczyć ustawienia MTU dla klientów i systemów magazynowania zaplecza, które współpracują z pamięcią podręczną. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie dodatkowych ustawień pamięci podręcznej platformy Azure HPC](configuration.md#adjust-mtu-value) .

## <a name="check-for-acl-security-style"></a>Sprawdź dostępność stylu zabezpieczeń listy ACL

Niektóre systemy NAS używają hybrydowego stylu zabezpieczeń, który łączy listy kontroli dostępu (ACL) z tradycyjnymi zabezpieczeniami POSIX lub UNIX.

Jeśli system raportuje swój styl zabezpieczeń jako UNIX lub POSIX bez uwzględnienia akronimu "list ACL", ten problem nie ma wpływu na użytkownika.

W przypadku systemów, które używają list ACL, pamięć podręczna Azure HPC wymaga śledzenia dodatkowych wartości specyficznych dla użytkownika w celu kontrolowania dostępu do pliku. Jest to realizowane przez włączenie pamięci podręcznej dostępu. Nie ma kontroli związanej z użytkownikiem w celu włączenia pamięci podręcznej dostępu, ale można otworzyć bilet pomocy technicznej w celu zażądania włączenia go dla docelowych magazynów, których dotyczy problem, w systemie pamięci podręcznej.

## <a name="next-steps"></a>Następne kroki

Jeśli masz problem, który nie został uwzględniony w tym artykule, [Otwórz bilet pomocy technicznej](hpc-cache-support-ticket.md) , aby uzyskać pomoc dla ekspertów.
