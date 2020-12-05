---
title: SAP HANA skalowalny w poziomie HSR-Pacemaker z SLES na maszynach wirtualnych platformy Azure Rozwiązywanie problemów | Microsoft Docs
description: Przewodnik po sprawdzaniu i rozwiązywaniu problemów ze złożoną konfiguracją wysokiej dostępności SAP HANA skalowania w poziomie na podstawie replikacji systemu SAP HANA (HSR) i Pacemaker na SLES 12 SP3 uruchomionych na maszynach wirtualnych platformy Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: 87758100299eb170a7950a1a7a2c6bd2029b27fb
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621556"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Weryfikowanie i rozwiązywanie problemów SAP HANA skalowalnej w poziomie konfiguracji wysokiej dostępności w SLES 12 SP3 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Ten artykuł pomaga sprawdzić konfigurację klastra Pacemaker na potrzeby skalowania w poziomie SAP HANA, które są uruchamiane na maszynach wirtualnych platformy Azure. Konfiguracja klastra została wdrożona w połączeniu z replikacją systemu SAP HANA (HSR) i pakietem SUSE RPM SAPHanaSR-skalowania. Wszystkie testy zostały wykonane tylko w systemie SUSE SLES 12 SP3. Sekcje artykułu obejmują różne obszary i zawierają przykładowe polecenia i fragmenty z plików konfiguracji. Zalecamy te przykłady jako metodę weryfikacji i sprawdzania całego klastra.



## <a name="important-notes"></a>Ważne uwagi

Wszystkie testy SAP HANA skalowanie w poziomie w połączeniu z replikacją systemu SAP HANA i Pacemaker zostały wykonane tylko przy użyciu SAP HANA 2,0. Wersja systemu operacyjnego była SUSE Linux Enterprise Server 12 dodatku SP3 dla aplikacji SAP. Najnowszy pakiet RPM, SAPHanaSR-ScaleOut z SUSE, został użyty do skonfigurowania klastra Pacemaker.
SUSE opublikował [szczegółowy opis tej instalacji zoptymalizowanej pod kątem wydajności][sles-hana-scale-out-ha-paper].

W przypadku typów maszyn wirtualnych, które są obsługiwane w SAP HANA skalowanie w poziomie, sprawdź [katalog SAP HANA Certified IaaS][sap-hana-iaas-list].

> [!NOTE]
> Ten artykuł zawiera odwołania do *głównego* terminu i serwera *podrzędnego*, warunki, które nie są już używane przez firmę Microsoft. Po usunięciu tych warunków z oprogramowania usuniemy je z tego artykułu.

Wystąpił problem techniczny związany z SAP HANA skalowaniem w poziomie w połączeniu z wieloma podsieciami i vNICs oraz konfigurowaniem HSR. Obowiązkowe jest korzystanie z najnowszych poprawek SAP HANA 2,0, w których ten problem został rozwiązany. Obsługiwane są następujące wersje SAP HANA: 

* rev 2.00.024.04 lub nowszy 
* rev 2.00.032 lub nowszy

Jeśli potrzebujesz pomocy technicznej z firmy SUSE, postępuj zgodnie z tym [przewodnikiem][suse-pacemaker-support-log-files]. Zbierz wszystkie informacje dotyczące klastra o wysokiej dostępności (HA) SAP HANA zgodnie z opisem w artykule. Obsługa systemu SUSE wymaga tych informacji do dalszej analizy.

Podczas testowania wewnętrznego Konfiguracja klastra została pomylona przez normalne, bezpieczne zamknięcie maszyny wirtualnej za pośrednictwem Azure Portal. Dlatego zalecamy przetestowanie klastra w trybie failover przy użyciu innych metod. Użyj metod, takich jak wymuszanie jądra awaryjnego lub wyłączenie sieci lub Migrowanie zasobu **MSL** . Szczegółowe informacje znajdują się w poniższych sekcjach. Przyjęto, że standardowe zamknięcie jest wykonywane z zamiarem. Najlepszym przykładem zamierzonego zamknięcia jest konserwacja. Szczegółowe informacje znajdują się w temacie [Planowana konserwacja](#planned-maintenance).

Ponadto podczas wewnętrznego testowania Konfiguracja klastra została pomylona po ręcznym przejęciu SAP HANA, gdy klaster był w trybie konserwacji. Zalecamy ręczne przełączenie go z powrotem przed zakończeniem trybu konserwacji klastra. Innym rozwiązaniem jest wyzwolenie trybu failover przed przełączeniem klastra w tryb konserwacji. Aby uzyskać więcej informacji, zobacz [Planowana konserwacja](#planned-maintenance). W dokumentacji narzędzia SUSE opisano, jak można zresetować klaster w ten sposób przy użyciu polecenia **CRM** . Jednak wymienione wcześniej podejście było niezawodne podczas wewnętrznego testowania i nigdy nie wykazało żadnych nieoczekiwanych efektów ubocznych.

W przypadku korzystania z polecenia programu **CRM Migrowanie** należy wyczyścić konfigurację klastra. Dodaje ograniczenia lokalizacji, z którymi może się nie wiedzieć. Ograniczenia te wpływają na zachowanie klastra. Zobacz więcej szczegółów w obszarze [Planowana konserwacja](#planned-maintenance).



## <a name="test-system-description"></a>Opis systemu testowego

 W celu przeprowadzenia weryfikacji i certyfikacji w SAP HANA skalowania w poziomie, użyto instalacji. Obejmuje dwa systemy z trzema SAP HANA węzłami każdy: jeden wzorzec i dwóch procesów roboczych. W poniższej tabeli wymieniono nazwy maszyn wirtualnych i wewnętrzne adresy IP. Wszystkie poniższe próbki weryfikacyjne zostały wykonane na tych maszynach wirtualnych. Korzystając z tych nazw maszyn wirtualnych i adresów IP w przykładach poleceń, można lepiej zrozumieć polecenia i ich dane wyjściowe:


| Typ węzła | Nazwa maszyny wirtualnej | Adres IP |
| --- | --- | --- |
| Węzeł główny w lokacji 1 | HSO-Hana-VM-S1-0 | 10.0.0.30 |
| Węzeł procesu roboczego 1 w lokacji 1 | HSO-Hana-VM-S1-1 | 10.0.0.31 |
| Węzeł roboczy 2 w lokacji 1 | HSO-Hana-VM-S1-2 | 10.0.0.32 |
| | | |
| Węzeł główny w lokacji 2 | HSO-Hana-VM-S2-0 | 10.0.0.40 |
| Węzeł procesu roboczego 1 w lokacji 2 | HSO-Hana-VM-S2-1 | 10.0.0.41 |
| Węzeł procesu roboczego 2 w lokacji 2 | HSO-Hana-VM-S2-2  | 10.0.0.42 |
| | | |
| Węzeł producenta większości | HSO-Hana-DM | 10.0.0.13 |
| Serwer urządzenia SBD | HSO-Hana-SBD | 10.0.0.19 |
| | | |
| Serwer NFS 1 | HSO-NFS-VM-0 | 10.0.0.15 |
| Serwer NFS 2 | HSO-NFS-VM-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Wiele podsieci i vNICs

Poniższe zalecenia dotyczące sieci SAP HANA, trzy podsieci zostały utworzone w ramach jednej sieci wirtualnej platformy Azure. SAP HANA skalowanie w poziomie na platformie Azure musi być zainstalowane w trybie nieudostępnionym. Oznacza to, że każdy węzeł używa lokalnych woluminów dysków dla **/Hana/Data** i **/Hana/log**. Ponieważ węzły używają tylko woluminów dysków lokalnych, nie trzeba definiować oddzielnej podsieci dla magazynu:

- 10.0.2.0/24 do SAP HANA komunikacji między węzłami
- 10.0.1.0/24 na potrzeby replikacji systemu SAP HANA (HSR)
- 10.0.0.0/24 dla wszystkiego innego

Aby uzyskać informacje dotyczące konfiguracji SAP HANA związanej z korzystaniem z wielu sieci, zobacz [SAP HANA global.ini](#sap-hana-globalini).

Każda maszyna wirtualna w klastrze ma trzy vNICs, które odpowiadają liczbie podsieci. [Jak utworzyć maszynę wirtualną z systemem Linux na platformie Azure z wieloma kartami interfejsu sieciowego][azure-linux-multiple-nics] opis potencjalnego problemu dotyczącego routingu na platformie Azure podczas wdrażania maszyny wirtualnej z systemem Linux. Ten konkretny artykuł routingu ma zastosowanie tylko do użycia wielu vNICs. Problem jest rozwiązany przez SUSE na domyślne w SLES 12 SP3. Aby uzyskać więcej informacji, zobacz [wiele kart sieciowych z chmurą w usłudze EC2 i platformie Azure][suse-cloud-netconfig].


Aby sprawdzić, czy SAP HANA jest prawidłowo skonfigurowany do korzystania z wielu sieci, uruchom następujące polecenia. Najpierw należy sprawdzić poziom systemu operacyjnego, że wszystkie trzy wewnętrzne adresy IP dla wszystkich trzech podsieci są aktywne. Jeśli zdefiniowano podsieci z różnymi zakresami adresów IP, należy dostosować polecenia:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Następujące przykładowe dane wyjściowe pochodzą z drugiego węzła procesu roboczego w lokacji 2. Można zobaczyć trzy różne wewnętrzne adresy IP z eth0, eth1 i eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Następnie sprawdź porty SAP HANA serwera nazw i HSR. SAP HANA powinien nasłuchiwać odpowiednich podsieci. W zależności od numeru wystąpienia SAP HANA należy dostosować polecenia. Dla systemu testowego numer wystąpienia to **00**. Istnieją różne sposoby, aby dowiedzieć się, które porty są używane. 

Poniższa instrukcja SQL zwraca identyfikator wystąpienia, numer wystąpienia i inne informacje:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Aby znaleźć poprawne numery portów, można na przykład na platformie HANA Studio w obszarze **Konfiguracja** lub za pośrednictwem instrukcji SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Aby znaleźć każdy port używany w stosie oprogramowania SAP, w tym SAP HANA, Wyszukaj [porty TCP/IP wszystkich produktów SAP][sap-list-port-numbers].

W przypadku wystąpienia numer **00** w systemie testowym SAP HANA 2,0 numer portu serwera nazw to **30001**. Numer portu dla komunikacji metadanych HSR to **40002**. Jedną z opcji jest zalogowanie się do węzła procesu roboczego, a następnie sprawdzenie usług węzła głównego. W tym artykule zaewidencjonowano węzeł roboczy 2 w lokacji 2 próbujący połączyć się z węzłem głównym w lokacji 2.

Sprawdź nazwę portu serwera:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Aby udowodnić, że komunikacja między węzłami korzysta z podsieci **10.0.2.0/24**, wynik powinien wyglądać podobnie do poniższego przykładowego danych wyjściowych.
Tylko połączenie za pośrednictwem podsieci **10.0.2.0/24** powinno zakończyć się pomyślnie:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Teraz sprawdź, czy port HSR **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Aby udowodnić, że komunikacja HSR korzysta z podsieci **10.0.1.0/24**, wynik powinien wyglądać jak w następujących przykładowych danych wyjściowych.
Tylko połączenie za pośrednictwem podsieci **10.0.1.0/24** powinno zakończyć się pomyślnie:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


Plik konfiguracji **Corosync** musi być poprawny w każdym węźle w klastrze, w tym w węźle większość twórców. Jeśli dołączenie klastra do węzła nie działa zgodnie z oczekiwaniami, Utwórz lub skopiuj **/etc/Corosync/Corosync.conf** ręcznie we wszystkich węzłach i ponownie uruchom usługę. 

Przykładem jest zawartość **Corosync. conf** z systemu testowego.

Pierwsza sekcja to **Totem**, zgodnie z opisem w temacie [Instalacja klastra](./high-availability-guide-suse-pacemaker.md#cluster-installation), Krok 11. Można zignorować wartość **mcastaddr**. Zachowaj istniejący wpis. Wpisy dotyczące **tokenów** i **konsensusu** muszą być ustawione zgodnie z [dokumentacją SAP HANA Microsoft Azure][sles-pacemaker-ha-guide].

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

Druga sekcja, **Rejestrowanie** nie zmieniła się z podanym wartością domyślną:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

Trzecia sekcja zawiera **powstanie**. Wszystkie węzły klastra muszą być wyświetlane z **NodeId**:

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

W ostatniej sekcji **kworum** należy prawidłowo ustawić wartość **expected_votes** . Musi to być liczba węzłów, w tym węzeł producent większości. A wartość **two_node** musi być **równa 0**. Nie usuwaj wpisu. Ustaw wartość na **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Uruchom ponownie usługę za pomocą **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>Urządzenie SBD

Sposób konfigurowania urządzenia SBD na maszynie wirtualnej platformy Azure jest opisany w temacie [ogrodzenie SBD](./high-availability-guide-suse-pacemaker.md#sbd-fencing).

Najpierw sprawdź, czy maszyna wirtualna serwera SBD ma wpisy listy ACL dla każdego węzła w klastrze. Uruchom następujące polecenie na maszynie wirtualnej serwera SBD:


<pre><code>
targetcli ls
</code></pre>


W systemie testowym dane wyjściowe polecenia wyglądają podobnie jak w poniższym przykładzie. Nazwy list ACL, takie jak **IQN. 2006-04. HSO-DB-0. local: HSO-DB-0** muszą zostać wprowadzone jako odpowiadające im nazwy inicjatora na maszynach wirtualnych. Każda maszyna wirtualna musi być inna.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Następnie sprawdź, czy nazwy inicjatora na wszystkich maszynach wirtualnych są różne i odpowiadają wcześniej pokazanym wpisom. Ten przykład pochodzi z węzła Worker 1 w lokacji 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Dane wyjściowe wyglądają podobnie jak w poniższym przykładzie:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Następnie sprawdź, czy **odnajdywanie** działa prawidłowo. Uruchom następujące polecenie na każdym węźle klastra przy użyciu adresu IP maszyny wirtualnej serwera SBD:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Dane wyjściowe powinny wyglądać jak w następującym przykładzie:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Następnym punktem dowodu jest sprawdzenie, czy węzeł widzi urządzenie SDB. Sprawdź je w każdym węźle, w tym w węźle większość twórców:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Dane wyjściowe powinny wyglądać jak w poniższym przykładzie. Nazwy mogą jednak się różnić. Nazwa urządzenia może ulec zmianie po ponownym uruchomieniu maszyny wirtualnej:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

W zależności od stanu systemu Czasami pomocne jest ponowne uruchomienie usług iSCSI w celu rozwiązania problemów. Następnie uruchom następujące polecenia:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Z dowolnego węzła można sprawdzić, czy wszystkie węzły są **jasne**. Upewnij się, że używasz prawidłowej nazwy urządzenia w określonym węźle:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Dane wyjściowe powinny być **wyczyszczone** dla każdego węzła w klastrze:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Inna SBD Check to opcja **zrzutu** polecenia **SBD** . W tym przykładowym poleceniu i danych wyjściowych z węzła producent większości, nazwa urządzenia to **SDD**, a nie **SDM**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Dane wyjściowe, niezależnie od nazwy urządzenia, powinny wyglądać tak samo na wszystkich węzłach:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

Jednym z nich jest możliwość wysłania komunikatu do innego węzła. Aby wysłać komunikat do węzła procesu roboczego 2 w lokacji 2, uruchom następujące polecenie w węźle procesu roboczego 1 w lokacji 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Na docelowej stronie maszyny wirtualnej **HSO-Hana-VM-S2-2** w tym przykładzie można znaleźć następujący wpis w **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Sprawdź, czy wpisy w **/etc/sysconfig/SBD** odpowiadają opisowi [konfiguracji Pacemaker na SUSE Linux Enterprise Server na platformie Azure](./high-availability-guide-suse-pacemaker.md#sbd-fencing). Sprawdź, czy ustawienie uruchamiania w **/etc/iSCSI/iscsid.conf** jest ustawione na automatyczne.

Następujące wpisy są ważne w **/etc/sysconfig/SBD**. W razie potrzeby Dostosuj wartość **identyfikatora** :

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Sprawdź ustawienie uruchamiania w **/etc/iSCSI/iscsid.conf**. Wymagane ustawienie powinno nastąpić za pomocą następującego polecenia **iscsiadm** , które opisano w dokumentacji. Weryfikuj i dostosowuj ręcznie przy użyciu **VI** , jeśli jest inny.

To polecenie ustawia zachowanie uruchamiania:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Wprowadź ten wpis w **/etc/iSCSI/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Podczas testowania i weryfikacji po ponownym uruchomieniu maszyny wirtualnej urządzenie SBD nie było już widoczne w niektórych przypadkach. Wystąpił niezgodność między ustawieniem uruchamiania a YaST2. Aby sprawdzić ustawienia, wykonaj następujące czynności:

1. Uruchom YaST2.
2. Wybierz pozycję **Network Services** po lewej stronie.
3. Przewiń w dół po prawej stronie do **inicjatora iSCSI** i wybierz go.
4. Na następnym ekranie pod kartą **usługi** zobaczysz unikatową nazwę inicjatora dla węzła.
5. Przed nazwą inicjatora upewnij się, że wartość **początkowa usługi** jest ustawiona na **podczas uruchamiania**.
6. Jeśli tak nie jest, ustaw ją na **podczas uruchamiania** , a nie **ręcznie**.
7. Następnie Przełącz górną kartę do **połączonych obiektów docelowych**.
8. Na ekranie **połączone obiekty docelowe** powinna zostać wyświetlona pozycja urządzenia SBD, na przykład: **10.0.0.19:3260 IQN. 2006-04. dbhso. local: dbhso**.
9. Sprawdź, czy wartość **uruchamiania** jest ustawiona na **rozruch**.
10. Jeśli nie, wybierz **Edytuj** i Zmień.
11. Zapisz zmiany i Zamknij YaST2.



## <a name="pacemaker"></a>Pacemaker

Po poprawnym skonfigurowaniu wszystkiego można uruchomić następujące polecenie na każdym węźle, aby sprawdzić stan usługi Pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

Góra danych wyjściowych powinna wyglądać jak w poniższym przykładzie. Należy pamiętać, że stan po **aktywnym** jest pokazywany jako **załadowany** i **aktywny (uruchomiony)**. Stan po **załadowaniu** musi być pokazywany jako **włączony**.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Jeśli ustawienie jest nadal **wyłączone**, uruchom następujące polecenie:

<pre><code>
systemctl enable pacemaker
</code></pre>

Aby wyświetlić wszystkie skonfigurowane zasoby w Pacemaker, uruchom następujące polecenie:

<pre><code>
crm status
</code></pre>

Dane wyjściowe powinny wyglądać jak w poniższym przykładzie. Jest to dokładne, że zasoby **CLN** i **MSL** są wyświetlane jako zatrzymane na maszynie wirtualnej z większością maszyn wirtualnych, **HSO-Hana-DM**. Nie ma SAP HANA instalacji w węźle producent większości. Zasoby **CLN** i **MSL** są wyświetlane jako zatrzymane. Ważne jest, aby wyświetlić poprawną łączną liczbę maszyn wirtualnych, **7**. Wszystkie maszyny wirtualne będące częścią klastra muszą być wyświetlane ze stanem **online**. Bieżący podstawowy węzeł główny musi zostać prawidłowo rozpoznany. W tym przykładzie jest to **HSO-Hana-VM-S1-0**:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Ważną funkcją Pacemaker jest tryb konserwacji. W tym trybie można wprowadzać modyfikacje bez wywoływania natychmiastowej akcji klastra. Przykładem jest ponowne uruchomienie maszyny wirtualnej. Typowym przypadkiem użycia jest planowana konserwacja systemu operacyjnego lub infrastruktury platformy Azure. Zapoznaj się z [planowaną konserwacją](#planned-maintenance). Użyj następującego polecenia, aby ustawić Pacemaker w tryb konserwacji:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Po sprawdzeniu **stanu programu CRM** należy zauważyć, że wszystkie zasoby są oznaczone jako **niezarządzane**. W tym stanie klaster nie reaguje na żadne zmiany, takie jak uruchamianie lub zatrzymywanie SAP HANA.
Poniższy przykład przedstawia dane wyjściowe polecenia stanu programu **CRM** , gdy klaster jest w trybie konserwacji:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              **_ Resource management is DISABLED _*_
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


To przykładowe polecenie pokazuje, jak zakończyć tryb konserwacji klastra:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Kolejne polecenie _ *CRM** pobiera kompletną konfigurację klastra do edytora, więc można ją edytować. Po zapisaniu zmian klaster uruchamia odpowiednie akcje:

<pre><code>
crm configure edit
</code></pre>

Aby sprawdzić kompletną konfigurację klastra, użyj opcji programu **CRM show** :

<pre><code>
crm configure show
</code></pre>



Po awarii zasobów klastra polecenie **CRM status** wyświetla listę **akcji zakończonych niepowodzeniem**. Zobacz następujący przykład danych wyjściowych:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

Należy przeprowadzić oczyszczanie klastra po wystąpieniu błędów. Ponownie Użyj polecenia **CRM** i użyj polecenia **Oczyść** , aby usunąć te nieudane wpisy akcji. Nazwij odpowiedni zasób klastra w następujący sposób:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Polecenie powinno zwrócić dane wyjściowe podobne do następującego przykładu:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Tryb failover lub przejmowanie

Jak opisano w [ważnych uwagach](#important-notes), nie należy używać standardowego bezpiecznego zamykania do testowania pracy w trybie failover klastra ani SAP HANA przejmowania HSR. Zamiast tego zaleca się wyzwolenie awaryjnego jądra, wymuszenie migracji zasobów lub prawdopodobnie zamknięcie wszystkich sieci na poziomie systemu operacyjnego maszyny wirtualnej. Inną metodą jest polecenie **CRM \<node\> Standby** . Zapoznaj się z [dokumentem SUSE][sles-12-ha-paper]. 

Następujące trzy przykładowe polecenia mogą wymusić przełączenie klastra w tryb failover:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Zgodnie z opisem w [planowanej konserwacji](#planned-maintenance), dobrym sposobem monitorowania działań klastra jest uruchomienie **SAPHanaSR-showAttr** przy użyciu polecenia **Watch** :

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Pomaga również sprawdzić stan SAP HANA krajobrazu pochodzący ze skryptu SAP Python. Konfiguracja klastra szuka tej wartości stanu. Zostanie ona wyczyszczona, gdy sądzisz o awarii węzła procesu roboczego. Jeśli węzeł procesu roboczego ulegnie awarii, SAP HANA nie natychmiast zwróci błędu dotyczącego kondycji całego systemu skalowalnego w poziomie. 

Istnieje kilka ponownych prób, aby uniknąć niepotrzebnych przełączeń w tryb failover. Klaster reaguje tylko wtedy, gdy stan zmieni się z **OK**, wartość zwracana **4**, na **błąd**, zwraca wartość **1**. Jest to poprawne, jeśli dane wyjściowe z **SAPHanaSR-showAttr** pokazują maszynę wirtualną z stanem **offline**. Nie ma jeszcze działania, aby przełączać podstawowe i pomocnicze. Żadna aktywność klastra nie zostanie wyzwolona, dopóki SAP HANA nie zwróci błędu.

Możesz monitorować SAP HANA stan kondycji poziomej jako użytkownik **\<HANA SID\> adm** , wywołując skrypt SAP Python w następujący sposób. Może być konieczne dostosowanie ścieżki:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Dane wyjściowe tego polecenia powinny wyglądać podobnie jak w poniższym przykładzie. W kolumnie **stan hosta** i **ogólny stan hosta** są oba ważne. Rzeczywiste dane wyjściowe są szersze, z dodatkowymi kolumnami.
Aby Tabela wyjściowa była bardziej czytelna w tym dokumencie, większość kolumn po prawej stronie została usunięta:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Istnieje inne polecenie, aby sprawdzić bieżące działania klastra. Zobacz następujące polecenie i końcowy wynik po zausunięciu węzła głównego lokacji głównej. Można wyświetlić listę akcji przejścia, takich jak **promowanie** dawnego węzła głównego wzorca, **HSO-Hana-VM-S2-0**, jako nowego podstawowego serwera głównego. Jeśli wszystko jest szczegółowe i wszystkie działania zostaną zakończone, lista **Zbiorcza tego przejścia** musi być pusta.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Planowana konserwacja 

Istnieją różne przypadki użycia związane z planowaną konserwacją. Jednym z pytań jest to, czy jest to tylko konserwacja infrastruktury, taka jak zmiana na poziomie systemu operacyjnego i Konfiguracja dysku lub uaktualnienie platformy HANA.
Dodatkowe informacje można znaleźć w dokumentach z usługi SUSE, takich jak w przypadku nieprzerwanego [przestoju][sles-zero-downtime-paper] lub [scenariusza optymalizacji wydajności SAP HANA SR][sles-12-for-sap]. Te dokumenty zawierają również przykłady pokazujące sposób ręcznej migracji elementu podstawowego.

Wykonano intensywne testowanie wewnętrzne, aby zweryfikować przypadek użycia konserwacji infrastruktury. Aby uniknąć problemów związanych z migracją podstawowego, należy zawsze migrować podstawową przed przełączeniem klastra w tryb konserwacji. W ten sposób nie jest konieczne, aby klaster zapominał o poprzedniej sytuacji: która była stroną podstawową i która była pomocnicza.

W tym przypadku istnieją dwie różne sytuacje:

- **Planowana konserwacja na bieżącym pomocniczym**. W takim przypadku można po prostu przełączyć klaster do trybu konserwacji i wykonać pracę na pomocniczym bez wpływu na klaster.

- **Planowana konserwacja na bieżącym serwerze podstawowym**. Aby użytkownicy mogli kontynuować pracę w trakcie konserwacji, należy wymusić przejście w tryb failover. W tym podejściu należy wyzwolić klaster w trybie failover przez Pacemaker, a nie tylko na poziomie HSR SAP HANA. Instalator Pacemaker automatycznie wyzwala przejęcie SAP HANA. Należy również wykonać tryb failover przed przełączeniem klastra w tryb konserwacji.

Procedura konserwacji w bieżącej lokacji dodatkowej jest następująca:

1. Przełączenie klastra w tryb konserwacji.
2. Wykonanie pracy w lokacji dodatkowej. 
3. Zakończ tryb konserwacji klastra.

Procedura konserwacji w bieżącej lokacji głównej jest bardziej złożona:

1. Ręcznie Wyzwól przełączenie w tryb failover lub SAP HANA przejmowanie za pośrednictwem migracji zasobów Pacemaker. Poniżej znajdują się szczegółowe informacje.
2. SAP HANA w dawnej lokacji głównej zostanie zamknięty przez Instalatora klastra.
3. Przełączenie klastra w tryb konserwacji.
4. Po zakończeniu prac konserwacyjnych Zarejestruj poprzednią podstawową jako nową lokację dodatkową.
5. Wyczyść konfigurację klastra. Poniżej znajdują się szczegółowe informacje.
6. Zakończ tryb konserwacji klastra.


Migrowanie zasobu dodaje wpis do konfiguracji klastra. Przykład wymusza przejście w tryb failover. Przed zakończeniem trybu konserwacji należy oczyścić te wpisy. Zobacz Poniższy przykład.

Najpierw Wymuś tryb failover klastra przez Migrowanie zasobu **MSL** do bieżącego pomocniczego węzła głównego. To polecenie wyświetla ostrzeżenie, że utworzono **ograniczenie przenoszenia** :

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Sprawdź proces trybu failover za pomocą polecenia **SAPHanaSR-showAttr**. Aby monitorować stan klastra, Otwórz dedykowane okno powłoki i uruchom polecenie z **zegarkiem**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Dane wyjściowe powinny zawierać ręczną pracę awaryjną. Dawny pomocniczy węzeł główny został **podwyższony**, w tym przykładzie **HSO-Hana-VM-S2-0**. Dawna lokacja główna została zatrzymana, **LSS** wartość **1** dla dawnego głównego węzła głównego **HSO-Hana-VM-S1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Po przejściu do trybu failover klastra i SAP HANA przejęcia klastra Przełącz go w tryb konserwacji, zgodnie z opisem w [Pacemaker](#pacemaker).

Polecenia **SAPHanaSR-showAttr** i **CRM** nie wskazują żadnych informacji o ograniczeniach utworzonych przez migrację zasobów. Jedną z opcji, aby te ograniczenia były widoczne, jest wyświetlenie kompletnej konfiguracji zasobów klastra przy użyciu następującego polecenia:

<pre><code>
crm configure show
</code></pre>

W ramach konfiguracji klastra znajdziesz nowe ograniczenie lokalizacji spowodowane przez poprzednią ręczną migrację zasobów. Ten przykładowy wpis rozpoczyna się od **lokalizacji CLI**:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Niestety takie ograniczenia mogą mieć wpływ na ogólne zachowanie klastra. Dlatego należy je usunąć ponownie przed przełączeniem całego systemu. Za pomocą polecenia **odmigrowania** można wyczyścić ograniczenia lokalizacji, które zostały utworzone wcześniej. Nazewnictwo może być nieco trudne. Nie próbuje on migrować zasobu z powrotem do oryginalnej maszyny wirtualnej, z której została zmigrowana. Po prostu usuwa ograniczenia lokalizacji, a także zwraca odpowiednie informacje podczas uruchamiania polecenia:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Po zakończeniu konserwacji należy zatrzymać tryb konserwacji klastra, jak pokazano w [Pacemaker](#pacemaker).



## <a name="hb_report-to-collect-log-files"></a>hb_report zbierać pliki dziennika

Aby analizować problemy z klastrem Pacemaker, jest to przydatne i wymagane przez pomoc techniczną SUSE do uruchomienia narzędzia **hb_report** . Gromadzi wszystkie ważne pliki dziennika, które należy analizować, co się stało. To wywołanie przykładowe używa czasu rozpoczęcia i zakończenia w przypadku wystąpienia konkretnego zdarzenia. Zobacz też [Ważne uwagi](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Polecenie informuje, gdzie umieszcza skompresowane pliki dziennika:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Następnie można wyodrębnić poszczególne pliki za pośrednictwem standardowego polecenia **tar** :

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Podczas przeglądania wyodrębnionych plików można znaleźć wszystkie pliki dziennika. Większość z nich została umieszczona w oddzielnych katalogach dla każdego węzła w klastrze:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


W określonym zakresie czasu bieżący węzeł główny **HSO-Hana-VM-S1-0** został zamknięty. Wpisy powiązane z tym zdarzeniem można znaleźć w **dzienniku dziennika. Dziennik**:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Innym przykładem jest plik dziennika Pacemaker na pomocniczym serwerze głównym, który stał się nowym głównym serwerem głównym. Ten fragment pokazuje, że stan nieprzerwanego podstawowego węzła głównego jest ustawiony na **tryb offline**:

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


Poniższe fragmenty pochodzą z pliku SAP HANA **global.ini** w lokacji klastra 2. W tym przykładzie przedstawiono wpisy rozpoznawania nazwy hosta dla różnych sieci SAP HANA komunikacji między węzłami i HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

Rozwiązanie klastrowe udostępnia interfejs przeglądarki, który oferuje graficznego interfejsu użytkownika dla użytkowników, którzy preferują menu i grafikę, aby wszystkie polecenia były dostępne na poziomie powłoki.
Aby użyć interfejsu przeglądarki, Zastąp **\<node\>** wartość rzeczywistym węzłem SAP HANA w poniższym adresie URL. Następnie wprowadź poświadczenia klastra ( **klaster** użytkownika):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Ten zrzut ekranu przedstawia pulpit nawigacyjny klastra:


![Pulpit nawigacyjny klastra Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Ten przykład pokazuje ograniczenia lokalizacji spowodowane przez migrację zasobów klastra zgodnie z opisem w [planowanej konserwacji](#planned-maintenance):


![Ograniczenia listy Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Możesz również przekazać dane wyjściowe **hb_report** w Hawk w obszarze **historia**, jak pokazano poniżej. Zobacz hb_report, aby zbierać pliki dziennika: 

![Hawk hb_report przekazywanie danych wyjściowych](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Za pomocą **Eksploratora historii** można następnie przejść przez wszystkie przejścia klastra zawarte w **hb_report** danych wyjściowych:

![Hawk przejścia hb_report w danych wyjściowych](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Ten końcowy zrzut ekranu przedstawia sekcję **szczegółów** pojedynczego przejścia. Klaster zareaguje na awarię podstawowego węzła głównego, węzeł **HSO-Hana-VM-S1-0**. Teraz można promować węzeł pomocniczy jako nowy wzorzec **HSO-Hana-VM-S2-0**:

![Hawk pojedyncze przejście](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Następne kroki

W tym przewodniku rozwiązywania problemów opisano wysoką dostępność SAP HANA w konfiguracji skalowalnej w poziomie. Oprócz bazy danych inny ważnym składnikiem SAP krajobrazu jest stos SAP NetWeaver. Dowiedz się więcej o [wysokiej dostępności dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure, które korzystają z serwera SUSE Enterprise Linux][sap-nw-ha-guide-sles].
