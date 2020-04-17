---
title: Rozwiązywanie problemów z obiektami docelowymi pamięci masowej pamięci podręcznej HPC usługi Azure
description: Wskazówki dotyczące unikania i rozwiązywania błędów konfiguracji i innych problemów, które mogą powodować awarie podczas tworzenia obiektu docelowego magazynu systemu plików NFS
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: rohogue
ms.openlocfilehash: 0a24530810a448a713c01efbc8933b9f22d15b3b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536373"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Rozwiązywanie problemów z konfiguracją serwera NAS i docelowymi pamięcią masową systemu plików NFS

W tym artykule przedstawiono rozwiązania niektórych typowych błędów konfiguracji i innych problemów, które mogą uniemożliwić usługi Azure HPC Cache dodanie systemu magazynu NFS jako miejsca docelowego magazynu.

Ten artykuł zawiera szczegółowe informacje na temat sprawdzania portów i włączania dostępu administratora do systemu NAS. Zawiera również szczegółowe informacje na temat mniej typowych problemów, które mogą spowodować niepowodzenie tworzenia miejsca docelowego magazynu NFS.

> [!TIP]
> Przed użyciem tego przewodnika należy zapoznać [się z wymaganiami wstępnymi dla obiektów docelowych magazynu NFS](hpc-cache-prereqs.md#nfs-storage-requirements).

Jeśli rozwiązanie problemu nie jest tutaj uwzględnione, [otwórz bilet pomocy technicznej,](hpc-cache-support-ticket.md) aby usługa i pomoc techniczna firmy Microsoft mogły współpracować z Tobą w celu zbadania i rozwiązania problemu.

## <a name="check-port-settings"></a>Sprawdzanie ustawień portu

Pamięć podręczna HPC platformy Azure wymaga dostępu do odczytu/zapisu do kilku portów UDP/TCP w zapleczu systemu pamięci masowej NAS. Upewnij się, że te porty są dostępne w systemie NAS, a także, że ruch jest dozwolony do tych portów za pośrednictwem zapór między systemem pamięci masowej a podsiecią pamięci podręcznej. Aby zweryfikować tę konfigurację, może być konieczna współpraca z administratorami zapory i sieci dla centrum danych.

Porty różnią się dla systemów pamięci masowej od różnych dostawców, więc sprawdź wymagania systemu podczas konfigurowania miejsca docelowego magazynu.

Ogólnie rzecz biorąc pamięć podręczna wymaga dostępu do tych portów:

| Protocol (Protokół) | Port  | Usługa  |
|----------|-------|----------|
| Protokół TCP/UDP  | 111   | rpcbind  |
| Protokół TCP/UDP  | 2049  | NFS      |
| Protokół TCP/UDP  | 4045  | nlockmgr |
| Protokół TCP/UDP  | 4046  | zamontowany   |
| Protokół TCP/UDP  | 4047  | status   |

Aby poznać określone porty potrzebne do ``rpcinfo`` systemu, użyj następującego polecenia. To polecenie poniżej wyświetla porty i formatuje odpowiednie wyniki w tabeli. (Użyj adresu IP systemu zamiast *<storage_IP>* terminem).

To polecenie można wydać z dowolnego klienta systemu Linux z zainstalowaną infrastrukturą NFS. Jeśli używasz klienta wewnątrz podsieci klastra, może również pomóc weryfikowaniu łączności między podsiecią a systemem magazynowania.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Upewnij się, że wszystkie porty zwrócone przez kwerendę zezwalają ``rpcinfo`` na nieograniczony ruch z podsieci pamięci podręcznej HPC platformy Azure.

Sprawdź te ustawienia zarówno na samym serwerze NAS, jak i na wszystkich zapór między systemem pamięci masowej a podsiecią pamięci podręcznej.

## <a name="check-root-access"></a>Sprawdź dostęp do roota

Pamięć podręczna HPC usługi Azure potrzebuje dostępu do eksportu systemu magazynu, aby utworzyć miejsce docelowe magazynu. W szczególności montuje eksport jako identyfikator użytkownika 0.

Różne systemy pamięci masowej używają różnych metod, aby umożliwić ten dostęp:

* Serwery Linuksa zazwyczaj ``no_root_squash`` dodają ``/etc/exports``do wyeksportowanego ścieżki w .
* Systemy NetApp i EMC zazwyczaj kontrolują dostęp za pomocą reguł eksportu powiązanych z określonymi adresami IP lub sieciami.

Jeśli używasz reguł eksportu, należy pamiętać, że pamięć podręczna może używać wielu różnych adresów IP z podsieci pamięci podręcznej. Zezwalaj na dostęp z pełnego zakresu możliwych adresów IP podsieci.

> [!NOTE]
> Domyślnie pamięć podręczna HPC usługi Azure zmniejsza dostęp do roota. Przeczytaj [pozycję Konfigurowanie dodatkowych ustawień pamięci podręcznej,](configuration.md#configure-root-squash) aby uzyskać szczegółowe informacje.

Współpracuj z dostawcą pamięci masowej NAS, aby włączyć odpowiedni poziom dostępu do pamięci podręcznej.

### <a name="allow-root-access-on-directory-paths"></a>Zezwalaj na dostęp do katalogu na ścieżkach katalogów
<!-- linked in prereqs article -->

W przypadku systemów NAS, które eksportują katalogi hierarchiczne, pamięć podręczna HPC azure potrzebuje dostępu administratora do każdego poziomu eksportu.

Na przykład system może pokazać trzy eksporty, takie jak te:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

Eksport ``/ifs/accounting/payroll`` jest dzieckiem ``/ifs/accounting``, ``/ifs/accounting`` i sam jest ``/ifs``dzieckiem .

Jeśli dodasz ``payroll`` eksport jako miejsce docelowe magazynu pamięci ``/ifs/`` podręcznej HPC, pamięć podręczna faktycznie instaluje i uzyskuje dostęp do katalogu listy płac stamtąd. Dlatego pamięć podręczna HPC ``/ifs`` usługi Azure ``/ifs/accounting/payroll`` wymaga dostępu administratora, aby uzyskać dostęp do eksportu.

To wymaganie jest związane ze sposobem, w jaki pamięć podręczna indeksuje pliki i pozwala uniknąć kolizji plików przy użyciu uchwytów plików, które zapewnia system magazynowania.

System NAS z eksportem hierarchicznym może dać różne uchwyty plików dla tego samego pliku, jeśli plik jest pobierany z różnych eksportów. Na przykład klient może ``/ifs/accounting`` zainstalować plik ``payroll/2011.txt``i uzyskać do niego dostęp. Inny klient ``/ifs/accounting/payroll`` montuje i ``2011.txt``uzyskuje dostęp do pliku . W zależności od tego, jak system magazynowania przypisuje uchwyty plików, ci dwaj ``<mount2>/payroll/2011.txt`` klienci ``<mount3>/2011.txt``mogą otrzymać ten sam plik z różnymi uchwytami plików (jeden dla i jeden dla ).

System magazynu zaplecza przechowuje aliasy wewnętrzne dla uchwytów plików, ale pamięć podręczna HPC platformy Azure nie może stwierdzić, które uchwyty pliku w indeksie odwołują się do tego samego elementu. Możliwe więc, że pamięć podręczna może mieć różne zapisy buforowane dla tego samego pliku i zastosować zmiany niepoprawnie, ponieważ nie wie, że są one tym samym plikiem.

Aby uniknąć tego możliwego kolizji plików w wielu eksportuch, pamięć podręczna HPC``/ifs`` usługi Azure automatycznie montuje najpłytsze dostępne eksportu w ścieżce (w przykładzie) i używa dojścia pliku podanego z tego eksportu. Jeśli wiele eksportów używa tej samej ścieżki podstawowej, pamięć podręczna HPC usługi Azure wymaga dostępu administratora do tej ścieżki.

## <a name="enable-export-listing"></a>Włącz listę eksportu
<!-- link in prereqs article -->

Serwer NAS musi wyświetlić listę swoich eksportów, gdy pamięć podręczna HPC platformy Azure wysyła zapytanie.

W większości systemów pamięci masowej NFS można to przetestować, wysyłając następującą kwerendę z klienta systemu Linux:``showmount -e <storage IP address>``

Użyj klienta systemu Linux z tej samej sieci wirtualnej co pamięć podręczna, jeśli to możliwe.

Jeśli to polecenie nie zawiera listy eksportu, pamięć podręczna będzie miała problemy z połączeniem się z systemem magazynu. Współpracuj z dostawcą serwera NAS, aby włączyć listę eksportu.

## <a name="adjust-vpn-packet-size-restrictions"></a>Dostosowywanie ograniczeń rozmiaru pakietów sieci VPN
<!-- link in prereqs article and configuration article -->

Jeśli masz sieć VPN między pamięcią podręczną a urządzeniem NAS, sieć VPN może blokować pełnowymiarowe pakiety Ethernet o rozmiarze 1500 bajtów. Ten problem może być taki problem, jeśli duża wymiana między serwerem NAS a wystąpieniem pamięci podręcznej HPC platformy Azure nie zostanie ukończona, ale mniejsze aktualizacje działają zgodnie z oczekiwaniami.

Nie ma prostego sposobu, aby stwierdzić, czy twój system ma ten problem, chyba że znasz szczegóły konfiguracji sieci VPN. Oto kilka metod, które mogą pomóc w sprawdzeniu tego problemu.

* Użyj sniffers pakietów po obu stronach sieci VPN, aby wykryć, które pakiety zostały pomyślnie przesłane.
* Jeśli sieć VPN zezwala na polecenia ping, możesz przetestować wysyłanie pełnowymiarowego pakietu.

  Uruchom polecenie ping za pośrednictwem sieci VPN na serwerze NAS z tymi opcjami. (Użyj adresu IP systemu pamięci masowej zamiast *<storage_IP>* wartość).

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Oto opcje w poleceniu:

  * ``-M do``- Nie fragmentuj
  * ``-c 1``- Wyślij tylko jeden pakiet
  * ``-s 1472``- Ustaw rozmiar ładunku na 1472 bajtów. Jest to maksymalny rozmiar ładunku dla pakietu 1500 bajtów po uwzględnieniu narzutów Ethernet.

  Odpowiedź oznaczająca powodzenie wygląda następująco:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Jeśli ping nie powiedzie się z 1472 bajtów, prawdopodobnie istnieje problem z rozmiarem pakietu.

Aby rozwiązać ten problem, może być konieczne skonfigurowanie mocowania MSS w sieci VPN, aby system zdalny prawidłowo wykrył maksymalny rozmiar klatki. Przeczytaj [dokumentację parametrów IPsec/IKE bramy sieci VPN,](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) aby dowiedzieć się więcej.

W niektórych przypadkach może pomóc zmiana ustawienia jednostki MTU dla pamięci podręcznej HPC platformy Azure na 1400. Jeśli jednak ograniczysz jednostkę MTU w pamięci podręcznej, należy również ograniczyć ustawienia jednostki MTU dla klientów i systemów magazynu zaplecza, które współdziałają z pamięcią podręczną. Przeczytaj [pozycję Konfigurowanie dodatkowych ustawień pamięci podręcznej HPC platformy Azure,](configuration.md#adjust-mtu-value) aby uzyskać szczegółowe informacje.

## <a name="check-for-acl-security-style"></a>Sprawdzanie stylu zabezpieczeń listy ACL

Niektóre systemy NAS używają hybrydowego stylu zabezpieczeń, który łączy listy kontroli dostępu (ACL) z tradycyjnymi zabezpieczeniami POSIX lub UNIX.

Jeśli system zgłasza swój styl zabezpieczeń jako UNIX lub POSIX bez dołączania akronimu "ACL", ten problem nie ma wpływu na Ciebie.

W przypadku systemów korzystających z list ACL pamięć podręczna HPC usługi Azure musi śledzić dodatkowe wartości specyficzne dla użytkownika, aby kontrolować dostęp do plików. Odbywa się to przez włączenie pamięci podręcznej dostępu. Nie ma formantu skierowanego do użytkownika, aby włączyć pamięć podręczną dostępu, ale można otworzyć bilet pomocy technicznej, aby zażądać, aby był włączony dla obiektów docelowych magazynu, którego dotyczy problem w systemie pamięci podręcznej.

## <a name="next-steps"></a>Następne kroki

Jeśli masz problem, który nie został rozwiązany w tym artykule, [otwórz bilet pomocy technicznej,](hpc-cache-support-ticket.md) aby uzyskać pomoc eksperta.
