---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla oprogramowania SAP NW na SLES z Azure NetApp Files | Microsoft Docs
description: Przewodnik wysokiej dostępności dla oprogramowania SAP NetWeaver na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: b8f2054296c5b4e567699924305b7f3385f30835
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486219"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP

[dbms-guide]:dbms_guide_general.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych, instalowania platformy klastra i instalowania systemu SAP NetWeaver 7,50 o wysokiej dostępności przy użyciu [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
W przykładowych konfiguracjach, poleceniach instalacji itp. wystąpienie ASCS ma numer 00, wystąpienie wykres WYWOŁUJĄCYCH o numerze 01, podstawowe wystąpienie aplikacji (PAS) to 02, a wystąpienie aplikacji (AAS) to 03. Identyfikator systemowy SAP QAS jest używany. 

W tym artykule wyjaśniono, jak uzyskać wysoką dostępność aplikacji SAP NetWeaver za pomocą Azure NetApp Files. Warstwa bazy danych nie jest szczegółowo omówiona w tym artykule.

Przeczytaj najpierw następujące informacje i dokumenty SAP:

* [Dokumentacja Azure NetApp Files][anf-azure-doc] 
* Nota SAP [1928533][1928533], która ma:  
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure
* Uwaga dotycząca protokołu SAP [2015553][2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2205917][2205917] zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga dotycząca oprogramowania SAP [1944799][1944799] SAP HANA wytycznych dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga dotycząca oprogramowania SAP [2178632][2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498][2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692][2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca protokołu SAP [1984787][1984787] zawiera ogólne informacje o SUSE Linux Enterprise Server 12.
* Uwaga dotycząca programu SAP [1999351][1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* Społeczność Community WIKI] ( https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zawiera wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Przewodniki dla systemu SUSE SAP ha Best Practices][suse-ha-guide] Przewodniki zawierają wszystkie informacje wymagane do skonfigurowania NetWeaver HA i replikacji systemu SAP HANA w środowisku lokalnym. Użyj tych przewodników jako ogólnego planu bazowego. Zapewniają one wiele bardziej szczegółowych informacji.
* [Informacje o wersji w programie SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Omówienie

Wysoka dostępność (HA) dla usług SAP NetWeaver Central Services wymaga magazynu udostępnionego.
Aby osiągnąć ten dostęp w systemie SUSE Linux, konieczne było skompilowanie oddzielnego klastra NFS o wysokiej dostępności. 

Teraz można osiągnąć rozwiązanie SAP NetWeaver HA przy użyciu magazynu udostępnionego, wdrożonego na Azure NetApp Files. Używanie Azure NetApp Files dla magazynu udostępnionego eliminuje konieczność dodatkowego [klastra NFS](./high-availability-guide-suse-nfs.md). Pacemaker jest nadal wymagana w przypadku wysokiej dostępności usług SAP NetWeaver Central Services (ASCS/SCS).


![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver wykres WYWOŁUJĄCYCH, a SAP HANA Database używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure [moduł równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md) jest wymagany do używania wirtualnego adresu IP. Zalecamy korzystanie z usługi [równoważenia obciążenia w warstwie Standardowa](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia (A) SCS i wykres WYWOŁUJĄCYCH.

### <a name="ascs"></a>Z SCS

* Konfiguracja frontonu
  * Adres IP 10.1.1.20
* Port sondy
  * Port 620<strong> &lt; Nr &gt; </strong>
* Reguły równoważenia obciążenia
  * W przypadku używania usługa Load Balancer w warstwie Standardowa wybierz pozycję **porty ha**
  * W przypadku korzystania z Load Balancer podstawowych Utwórz reguły równoważenia obciążenia dla następujących portów
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 36<strong> &lt; Nr &gt; </strong> TCP
    * 39<strong> &lt; Nr &gt; </strong> TCP
    * 81<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>13 TCP
    * 5<strong> &lt; Nr &gt; </strong>14 TCP
    * 5<strong> &lt; Nr &gt; </strong>16 TCP

### <a name="ers"></a>Wykres WYWOŁUJĄCYCH

* Konfiguracja frontonu
  * Adres IP 10.1.1.21
* Port sondy
  * Port 621<strong> &lt; Nr &gt; </strong>
* Reguły równoważenia obciążenia
  * W przypadku używania usługa Load Balancer w warstwie Standardowa wybierz pozycję **porty ha**
  * W przypadku korzystania z Load Balancer podstawowych Utwórz reguły równoważenia obciążenia dla następujących portów
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 33<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>13 TCP
    * 5<strong> &lt; Nr &gt; </strong>14 TCP
    * 5<strong> &lt; Nr &gt; </strong>16 TCP

* Konfiguracja zaplecza
  * Połączono z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra programu (A) SCS/wykres WYWOŁUJĄCYCH


## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Konfigurowanie infrastruktury Azure NetApp Files 

Rozwiązanie SAP NetWeaver wymaga udostępnionego magazynu dla katalogu transportowego i profilu.  Przed przystąpieniem do konfigurowania infrastruktury usługi Azure NetApp Files zapoznaj się z [dokumentacją Azure NetApp Files][anf-azure-doc]. Sprawdź, czy wybrany region platformy Azure oferuje Azure NetApp Files. Poniższy link przedstawia dostępność Azure NetApp Files według regionu platformy Azure: [Azure NetApp Files dostępność według regionu platformy Azure][anf-avail-matrix].

Pliki usługi Azure NetApp są dostępne w kilku [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Przed wdrożeniem Azure NetApp Files należy zażądać dołączenia do Azure NetApp Files, postępując zgodnie z instrukcjami dotyczącymi [usługi Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Wdrażanie zasobów Azure NetApp Files  

W tych krokach przyjęto założenie, że wdrożono już [Virtual Network platformy Azure](../../../virtual-network/virtual-networks-overview.md). Zasoby Azure NetApp Files i maszyny wirtualne, w których zostaną zainstalowane zasoby Azure NetApp Files, muszą być wdrożone w tej samej Virtual Network platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure.  

1. Jeśli jeszcze tego nie zrobiono, zażądaj dołączenia [do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).  

2. Utwórz konto NetApp w wybranym regionie świadczenia usługi Azure, postępując zgodnie z [instrukcjami dotyczącymi tworzenia konta NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  
3. Skonfiguruj pulę pojemności Azure NetApp Files, postępując zgodnie z [instrukcjami dotyczącymi konfigurowania Azure NetApp Files puli pojemności](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  
Architektura SAP NetWeaver w tym artykule używa puli pojemności Azure NetApp Files w wersji Premium. Zalecamy Azure NetApp Files jednostki SKU Premium dla obciążenia aplikacji SAP NetWeaver na platformie Azure.  

4. Delegowanie podsieci do plików NetApp platformy Azure zgodnie z opisem w [instrukcji delegowania podsieci do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Wdróż woluminy Azure NetApp Files, postępując zgodnie z [instrukcjami, aby utworzyć wolumin dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Wdróż woluminy w wyznaczeniej [podsieci](/rest/api/virtualnetwork/subnets)Azure NetApp Files. Adresy IP woluminów NetApp platformy Azure są przypisywane automatycznie. Należy pamiętać, że zasoby Azure NetApp Files i maszyny wirtualne platformy Azure muszą znajdować się w tym samym Virtual Network platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure. W tym przykładzie używamy dwóch Azure NetApp Files woluminów: SAP<b>QAS</b> i trans. Ścieżki plików, które są zainstalowane do odpowiednich punktów instalacji, to/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys itd.  

   1. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASCS)
   3. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>wykres wywołujących)
   5. transakcja zbiorcza (nfs://10.1.0.4/trans)
   6. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)

   
W tym przykładzie użyto Azure NetApp Files dla wszystkich systemów plików SAP NetWeaver, aby zademonstrować, jak Azure NetApp Files może być używany. Systemy plików SAP, które nie muszą być instalowane za pośrednictwem systemu plików NFS, można również wdrożyć jako [usługę Azure Disk Storage](../../disks-types.md#premium-ssd) . W tym przykładzie <b>a-e</b> musi znajdować się w Azure NetApp Files i <b>f-g</b> (czyli/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) można wdrożyć jako usługę Azure Disk Storage. 

### <a name="important-considerations"></a>Istotne zagadnienia

Rozważając Azure NetApp Files dla architektury SAP NetWeaver w systemie SUSE wysokiej dostępności, weź pod uwagę następujące ważne zagadnienia:

- Minimalna Pula pojemności to 4 TiB. Rozmiar puli pojemności można zwiększyć w 1 przyrostach TiB.
- Minimalny wolumin to 100 GiB
- Azure NetApp Files i wszystkie maszyny wirtualne, w których zostaną zainstalowane woluminy Azure NetApp Files, muszą znajdować się w tej samej Virtual Network platformy Azure lub w [równorzędnych sieciach wirtualnych](../../../virtual-network/virtual-network-peering-overview.md) w tym samym regionie. Azure NetApp Files dostęp za pośrednictwem komunikacji równorzędnej sieci wirtualnej w tym samym regionie jest obecnie obsługiwany. Dostęp do usługi Azure NetApp za pośrednictwem globalnej komunikacji równorzędnej nie jest jeszcze obsługiwany.
- Wybrana Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files.
- Azure NetApp Files oferuje [zasady eksportowania](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): można kontrolować dozwolonych klientów, typ dostępu (odczyt&zapis, tylko do odczytu itp.). 
- Funkcja Azure NetApp Files nie jest jeszcze dostępna dla strefy. Obecnie Azure NetApp Files funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Weź pod uwagę potencjalne konsekwencje opóźnienia w niektórych regionach świadczenia usługi Azure. 
- Woluminy Azure NetApp Files można wdrożyć jako woluminy NFSv3 lub NFSv 4.1. Oba protokoły są obsługiwane dla warstwy aplikacji SAP (ASCS/wykres WYWOŁUJĄCYCH, serwery aplikacji SAP). 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Ręczne wdrażanie maszyn wirtualnych z systemem Linux za pośrednictwem Azure Portal

Najpierw należy utworzyć woluminy Azure NetApp Files. Wdróż maszyny wirtualne. Następnie należy utworzyć moduł równoważenia obciążenia i użyć maszyn wirtualnych w pulach zaplecza.

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Utwórz zestaw dostępności dla ASCS  
   Ustaw maksymalną domenę aktualizacji
1. Utwórz maszynę wirtualną 1  
   Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie jest używany obraz SLES4SAP 12 SP3  
   Wybierz zestaw dostępności utworzony wcześniej dla ASCS  
1. Tworzenie maszyny wirtualnej 2  
   Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie jest używany obraz SLES4SAP 12 SP3  
   Wybierz zestaw dostępności utworzony wcześniej dla ASCS  
1. Utwórz zestaw dostępności dla wystąpień aplikacji SAP (PAS, AAS)    
   Ustaw maksymalną domenę aktualizacji
1. Tworzenie maszyny wirtualnej 3  
   Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie jest używany obraz SLES4SAP 12 SP3  
   Wybierz zestaw dostępności utworzony wcześniej dla platformy PAS/AAS   
1. Tworzenie maszyny wirtualnej 4  
   Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie jest używany obraz SLES4SAP 12 SP3  
   Wybierz zestaw dostępności utworzony wcześniej dla platformy PAS/AAS  

## <a name="disable-id-mapping-if-using-nfsv41"></a>Wyłącz mapowanie identyfikatora (jeśli jest używany NFSv 4.1)

Instrukcje zawarte w tej sekcji mają zastosowanie tylko w przypadku korzystania z Azure NetApp Files woluminów z protokołem NFSv 4.1. Wykonaj konfigurację na wszystkich maszynach wirtualnych, gdzie zostaną zainstalowane woluminy Azure NetApp Files NFSv 4.1.  

1. Sprawdź ustawienie domeny systemu plików NFS. Upewnij się, że domena jest skonfigurowana jako domyślna domena Azure NetApp Files, tj., **`defaultv4iddomain.com`** a mapowanie jest ustawione na wartość **nikt**.  

    > [!IMPORTANT]
    > Upewnij się, że na maszynie wirtualnej ustawiono domenę systemu plików NFS `/etc/idmapd.conf` zgodną z domyślną konfiguracją domeny na Azure NetApp Files: **`defaultv4iddomain.com`** . Jeśli istnieje niezgodność między konfiguracją domeny na kliencie NFS (tj. maszyną wirtualną) a serwerem NFS, tj. konfiguracją usługi Azure NetApp, wówczas uprawnienia do plików na woluminach NetApp platformy Azure, które są zainstalowane na maszynach wirtualnych, będą wyświetlane jako `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** Sprawdź poprawność `nfs4_disable_idmapping` . Powinna być ustawiona na wartość **Y**. Aby utworzyć strukturę katalogów, w której `nfs4_disable_idmapping` znajduje się, wykonaj polecenie instalacji. Nie będzie można ręcznie utworzyć katalogu w obszarze/sys/modules, ponieważ dostęp jest zarezerwowany dla jądra/sterowników.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.1.0.4:/sapmnt/<b>qas</b> /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>


## <a name="setting-up-ascs"></a>Konfigurowanie (A) SCS

W tym przykładzie zasoby zostały wdrożone ręcznie za pośrednictwem [Azure Portal](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Wdróż Azure Load Balancer ręcznie za pośrednictwem Azure Portal

Najpierw należy utworzyć woluminy Azure NetApp Files. Wdróż maszyny wirtualne. Następnie należy utworzyć moduł równoważenia obciążenia i użyć maszyn wirtualnych w puli zaplecza.

1. Tworzenie modułu równoważenia obciążenia (wewnętrznego, standardowego):  
   1. Utwórz adresy IP frontonu
      1. Adres IP 10.1.1.20 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pula adresów IP frontonu, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **frontonu). QAS. ASCS**)
         1. Ustaw przypisanie na static i wprowadź adres IP (na przykład **10.1.1.20**)
         1. Kliknij przycisk OK.
      1. 10.1.1.21 adresu IP dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki w obszarze "a", aby utworzyć adres IP dla wykres WYWOŁUJĄCYCH (na przykład **10.1.1.21** i **fronton. QAS. Wykres WYWOŁUJĄCYCH**)
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pule zaplecza, a następnie kliknij przycisk Dodaj.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **zaplecze. QAS**)
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz maszynę wirtualną
      1. Wybierz Maszyny wirtualne klastra SCS i ich adresy IP.
      1. Kliknij pozycję Dodaj.
   1. Tworzenie sond kondycji
      1. Port 620 **00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **kondycja. QAS. ASCS**)
         1. Wybierz pozycję TCP jako protokół, port 620 **00**, Zachowaj interwał 5 i próg złej kondycji 2
         1. Kliknij przycisk OK.
      1. Port 621 **01** dla ASCS wykres wywołujących
            * Powtórz powyższe kroki w sekcji "c", aby utworzyć sondę kondycji dla wykres WYWOŁUJĄCYCH (na przykład 621 **01** i **kondycja). QAS. Wykres WYWOŁUJĄCYCH**)
   1. Reguły równoważenia obciążenia
      1. Tworzenie puli zaplecza dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **LB. QAS. ASCS**)
         1. Wybierz adres IP frontonu dla ASCS, puli zaplecza i sondy kondycji utworzonej wcześniej (na przykład **frontonu). QAS. ASCS**, **zaplecze. QAS** i **kondycja. QAS. ASCS**)
         1. Wybieranie **portów ha**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Upewnij się, że włączono zmiennoprzecinkowy adres IP**
         1. Kliknij przycisk OK.
         * Powtórz powyższe kroki, aby utworzyć reguły równoważenia obciążenia dla wykres WYWOŁUJĄCYCH (na przykład **LB. QAS. Wykres WYWOŁUJĄCYCH**)
1. Alternatywnie, jeśli scenariusz wymaga podstawowego modułu równoważenia obciążenia (wewnętrznego), wykonaj następujące czynności:  
   1. Utwórz adresy IP frontonu
      1. Adres IP 10.1.1.20 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pula adresów IP frontonu, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **frontonu). QAS. ASCS**)
         1. Ustaw przypisanie na static i wprowadź adres IP (na przykład **10.1.1.20**)
         1. Kliknij przycisk OK.
      1. 10.1.1.21 adresu IP dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki w obszarze "a", aby utworzyć adres IP dla wykres WYWOŁUJĄCYCH (na przykład **10.1.1.21** i **fronton. QAS. Wykres WYWOŁUJĄCYCH**)
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pule zaplecza, a następnie kliknij przycisk Dodaj.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **zaplecze. QAS**)
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz zestaw dostępności utworzony wcześniej dla ASCS 
      1. Wybierz Maszyny wirtualne klastra SCS
      1. Kliknij przycisk OK.
   1. Tworzenie sond kondycji
      1. Port 620 **00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **kondycja. QAS. ASCS**)
         1. Wybierz pozycję TCP jako protokół, port 620 **00**, Zachowaj interwał 5 i próg złej kondycji 2
         1. Kliknij przycisk OK.
      1. Port 621 **01** dla ASCS wykres wywołujących
            * Powtórz powyższe kroki w sekcji "c", aby utworzyć sondę kondycji dla wykres WYWOŁUJĄCYCH (na przykład 621 **01** i **kondycja). QAS. Wykres WYWOŁUJĄCYCH**)
   1. Reguły równoważenia obciążenia
      1. 32 **00** TCP dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **LB. QAS. ASCS. 3200**)
         1. Wybierz adres IP frontonu dla ASCS, puli zaplecza i sondy kondycji utworzonej wcześniej (na przykład **frontonu). QAS. ASCS**)
         1. Utrzymywanie protokołu **TCP**, wprowadź port **3200**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Upewnij się, że włączono zmiennoprzecinkowy adres IP**
         1. Kliknij przycisk OK.
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki w obszarze "d" dla portów 36 **00**, 39 **00**, 81 **00**, 5 **00**, 5 **00** 14, 5 **00** 16 i TCP dla ASCS
      1. Dodatkowe porty dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki w obszarze "d" dla portów 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 i TCP dla ASCS wykres wywołujących

      
      > [!IMPORTANT]
      > Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.  

      > [!Note]
      > Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > Nie należy włączać sygnatur czasowych protokołu TCP na maszynach wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie sygnatur czasowych protokołu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.IPv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w temacie [Konfigurowanie Pacemaker SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) , aby utworzyć podstawowy klaster Pacemaker dla tego (a) serwera SCS.

### <a name="installation"></a>Instalacja

Następujące elementy są poprzedzone **[A]** -dotyczy wszystkie węzły, **[1]** — dotyczy tylko węzła 1 lub **[2]** — dotyczy tylko węzła 2.

1. **[A]** Zainstaluj łącznik SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Znany problem związany z używaniem łącznika w nazwach hostów został rozwiązany z wersją **3.1.1** pakietu **SAP-SUSE-Cluster-Connector**. Upewnij się, że używasz co najmniej wersji 3.1.1 pakietu SAP-SUSE-Cluster-Connector, jeśli w nazwie hosta są używane węzły klastra z kreską. W przeciwnym razie klaster nie będzie działał. 

   Upewnij się, że zainstalowano nową wersję łącznika klastra SAP SUSE. Stary został wywołany sap_suse_cluster_connector, a nowy o nazwie **SAP-SUSE-Cluster-Connector**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
    # Information for package sap-suse-cluster-connector:
    # ---------------------------------------------------
    # Repository     : SLE-12-SP3-SAP-Updates
    # Name           : sap-suse-cluster-connector
    # Version        : 3.1.0-8.1
    # Arch           : noarch
    # Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
    # Support Level  : Level 3
    # Installed Size : 45.6 KiB
    # Installed      : Yes
    # Status         : up-to-date
    # Source package : sap-suse-cluster-connector-3.1.0-8.1.src
    # Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** zaktualizuj agentów zasobów SAP  
   
   Poprawka do pakietu zasobów-agentów jest wymagana do korzystania z nowej konfiguracji, która została opisana w tym artykule. Możesz sprawdzić, czy poprawka jest już zainstalowana przy użyciu następującego polecenia

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Dane wyjściowe powinny być podobne do

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Jeśli polecenie grep nie odnajdzie parametru IS_ERS, należy zainstalować poprawkę wymienioną na [stronie pobierania systemu SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]** rozpoznawanie nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

4. **[1]** utwórz katalogi SAP na woluminie Azure NetApp Files.  
   Zainstaluj tymczasowo wolumin Azure NetApp Files na jednej z maszyn wirtualnych i utwórz katalogi SAP (ścieżki plików).  

   ```
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    # If using NFSv3
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 10.1.0.4:/sapQAS /saptmp
    # If using NFSv4.1
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 10.1.0.4:/sapQAS /saptmp
    # create the SAP directories
    sudo cd /saptmp
    sudo mkdir -p sapmntQAS
    sudo mkdir -p usrsapQASascs
    sudo mkdir -p usrsapQASers
    sudo mkdir -p usrsapQASsys
    sudo mkdir -p usrsapQASpas
    sudo mkdir -p usrsapQASaas
    # unmount the volume and delete the temporary directory
    sudo cd ..
    sudo umount /saptmp
    sudo rmdir /saptmp
    ``` 

## <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

1. **[A]** Tworzenie katalogów udostępnionych

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** Skonfiguruj `autofs`

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Jeśli używasz NFSv3, Utwórz plik z:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   Jeśli używasz NFSv 4.1, Utwórz plik z:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   > [!NOTE]
   > Przed zainstalowaniem woluminów upewnij się, że jest zgodna z wersją protokołu NFS Azure NetApp Files woluminów. Jeśli woluminy Azure NetApp Files są tworzone jako woluminy NFSv3, użyj odpowiedniej konfiguracji NFSv3. Jeśli woluminy Azure NetApp Files są tworzone jako woluminy NFSv 4.1, postępuj zgodnie z instrukcjami, aby wyłączyć mapowanie identyfikatorów i upewnij się, że używasz odpowiedniej konfiguracji NFSv 4.1. W tym przykładzie woluminy Azure NetApp Files zostały utworzone jako woluminy NFSv3.  
   
   Uruchom ponownie `autofs` , aby zainstalować nowe udziały
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** Skonfiguruj plik wymiany

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Uruchom ponownie agenta, aby aktywować zmianę

   <pre><code>sudo service waagent restart
   </code></pre>

### <a name="installing-sap-netweaver-ascsers"></a>Instalowanie oprogramowania SAP NetWeaver ASCS/wykres WYWOŁUJĄCYCH

1. **[1]** Utwórz zasób wirtualnego adresu IP i sondę kondycji dla wystąpienia ASCS

   > [!IMPORTANT]
   > Ostatnie testy ujawniły sytuacje, w których netcat przestaje odpowiadać na żądania z powodu zaległości i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań modułu równoważenia obciążenia platformy Azure, a przestawny adres IP stał się niedostępny.  
   > W przypadku istniejących klastrów Pacemaker zalecamy w przeszłości zastępowanie netcat socat. Obecnie zalecamy korzystanie z agenta zasobów Azure-lb, który jest częścią agentów zasobów pakietu, z następującymi wymaganiami dotyczącymi wersji pakietu:
   > - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej równa "Resource-Agents-4.3.018. a7fb5035-3.30.1.  
   > - W przypadku programu SLES 15/15 z dodatkiem SP1 wersja musi być co najmniej równa "Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  
   >
   > Należy pamiętać, że zmiana będzie wymagała krótkiego przestoju.  
   > W przypadku istniejących klastrów Pacemaker, jeśli konfiguracja została już zmieniona tak, aby korzystała z socat zgodnie z opisem w obszarze [Ograniczanie poziomu wykrywania w usłudze azure Load-Balancer](https://www.suse.com/support/kb/doc/?id=7024128), nie ma potrzeby natychmiastowego przełączenia do agenta zasobów platformy Azure.

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** Instalowanie oprogramowania SAP NetWeaver ASCS  

   Zainstaluj oprogramowanie SAP NetWeaver ASCS jako element główny w pierwszym węźle przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla ASCS, na przykład <b>anftstsapvh</b>, <b>10.1.1.20</b> i numer wystąpienia, które zostało użyte do sondowania modułu równoważenia obciążenia, na przykład <b>00</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst. Aby zainstalować oprogramowanie SAP przy użyciu nazwy hosta wirtualnego, można użyć parametru SAPINST_USE_HOSTNAME.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w/usr/SAP/**QAS**/ASCS **00**, spróbuj ustawić właściciela i grupę folderu ASCS **00**  , a następnie ponów próbę. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** Utwórz zasób wirtualnego adresu IP i sondę kondycji dla wystąpienia wykres wywołujących

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS azure-lb port=621<b>01</b>
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** Instalowanie oprogramowania SAP NetWeaver wykres wywołujących

   Zainstaluj program SAP NetWeaver wykres WYWOŁUJĄCYCH jako element główny w drugim węźle przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla wykres WYWOŁUJĄCYCH, na przykład <b>anftstsapers</b>, <b>10.1.1.21</b> i numer wystąpienia, które zostało użyte do sondowania modułu równoważenia obciążenia, na przykład <b>01</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst. Aby zainstalować oprogramowanie SAP przy użyciu nazwy hosta wirtualnego, można użyć parametru SAPINST_USE_HOSTNAME.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Użyj SWPM SP 20 PL 05 lub wyższej. Niższa wersja nie ustawia prawidłowo uprawnień i instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie powiedzie się, utwórz podfolder w/usr/SAP/**QAS**/ERS **01**, spróbuj ustawić właściciela i grupę folderu wykres wywołujących **01** i ponów próbę.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** dostosowuje profile wystąpienia ASCS/SCS i wykres wywołujących
 
   * Profil ASCS/SCS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   W przypadku programów ENSA1 i ENSA2 upewnij się, że `keepalive` parametry systemu operacyjnego zostały ustawione zgodnie z opisem w temacie SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

   * Profil wykres WYWOŁUJĄCYCH

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]** Skonfiguruj utrzymywanie aktywności

   Komunikacja między serwerem aplikacji SAP NetWeaver a ASCS/SCS jest kierowana przez programowy moduł równoważenia obciążenia. Moduł równoważenia obciążenia rozłącza nieaktywne połączenia po upływie konfigurowalnego limitu czasu. Aby tego uniknąć, należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS, jeśli jest używany ENSA1, i zmienić ustawienia systemu Linux `keepalive` na wszystkich serwerach SAP dla obu ENSA1/ENSA2. Aby uzyskać więcej informacji, przeczytaj temat [SAP Note 1410736][1410736] .

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

7. **[A]** Skonfiguruj użytkowników SAP po instalacji

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** Dodaj do pliku usługi ASCS i wykres wywołujących SAP `sapservice`

   Dodaj wpis usługi ASCS do drugiego węzła i skopiuj wpis usługi wykres WYWOŁUJĄCYCH do pierwszego węzła.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** tworzenie zasobów klastra SAP

W przypadku korzystania z architektury Server 1 Architecture (ENSA1) Zdefiniuj zasoby w następujący sposób:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   System SAP wprowadził obsługę dla kolejki serwera 2, w tym replikację, w przypadku oprogramowania SAP NW 7,52. Począwszy od programu ABAP platform 1809, w kolejce serwer 2 jest instalowany domyślnie. Zobacz temat SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) dla obsługi kolejki serwera 2.
Jeśli używana jest architektura Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), zdefiniuj zasoby w następujący sposób:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   W przypadku uaktualniania ze starszej wersji i przełączania do kolejki serwera 2, zobacz uwagi dotyczące oprogramowania SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowywanie serwera aplikacji SAP NetWeaver 

Niektóre bazy danych wymagają wykonania instalacji wystąpienia bazy danych na serwerze aplikacji. Przygotuj maszyny wirtualne serwera aplikacji, aby mogły z nich korzystać w tych przypadkach.

W poniższych krokach założono, że serwer aplikacji jest instalowany na serwerze innym niż serwery ASCS/SCS i HANA. W przeciwnym razie niektóre kroki (takie jak Konfigurowanie rozpoznawania nazw hostów) nie są wymagane.

Następujące elementy są poprzedzone znakiem **[A]** — dotyczy zarówno platformy pas, jak i AAS, **[P]** — dotyczy tylko platformy pas lub **[S]** — dotyczy tylko AAS.


1. **[A]** Skonfiguruj system operacyjny

   Zmniejsz rozmiar zanieczyszczonej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [niska wydajność zapisu na serwerach z systemem SLES 11/12 i dużą ilością pamięci RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** rozpoznawanie nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

   ```bash
   sudo vi /etc/hosts
   ```

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** Utwórz katalog sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** Utwórz katalog pas

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** Utwórz katalog AAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** Konfiguruj `autofs` na pas

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Jeśli używasz NFSv3, Utwórz nowy plik z:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Jeśli jest używany program NFSv 4.1, Utwórz nowy plik z:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Uruchom ponownie `autofs` , aby zainstalować nowe udziały

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** Skonfiguruj `autofs` w usłudze AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Jeśli używasz NFSv3, Utwórz nowy plik z:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Jeśli jest używany program NFSv 4.1, Utwórz nowy plik z:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Uruchom ponownie `autofs` , aby zainstalować nowe udziały

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Skonfiguruj plik wymiany

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Uruchom ponownie agenta, aby aktywować zmianę

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie system SAP NetWeaver jest instalowany na SAP HANA. Dla tej instalacji można użyć każdej obsługiwanej bazy danych. Aby uzyskać więcej informacji na temat instalowania SAP HANA na platformie Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines][sap-hana-ha]. Listę obsługiwanych baz danych można znaleźć w temacie [SAP Note 1928533][1928533].

* Uruchamianie instalacji wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako element główny przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla bazy danych.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalacja serwera aplikacji SAP NetWeaver

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP.

1. **[A]** Przygotuj serwer aplikacji, postępując zgodnie z instrukcjami opisanymi w rozdziale [serwer aplikacji SAP NetWeaver](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) powyżej, aby przygotować serwer aplikacji.

2. **[A]** Zainstaluj serwer aplikacji SAP NetWeaver Zainstaluj podstawowy lub dodatkowy serwer aplikacji SAP NetWeaver.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** aktualizowanie SAP HANA bezpiecznego magazynu

   Zaktualizuj SAP HANA bezpiecznego magazynu, aby wskazywał nazwę wirtualną konfiguracji replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów
   <pre><code>
   hdbuserstore List
   </code></pre>

   Powinno to być lista wszystkich wpisów i powinna wyglądać podobnie do
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   Dane wyjściowe wskazują, że adres IP wpisu domyślnego wskazuje maszynę wirtualną, a nie adres IP modułu równoważenia obciążenia. Ten wpis należy zmienić, aby wskazywał wirtualną nazwę hosta usługi równoważenia obciążenia. Upewnij się, że używasz tego samego portu (**30313** w danych wyjściowych powyżej) i nazwy bazy danych (**QAS** w danych wyjściowych powyżej)!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

Poniższe testy to kopia przypadków testowych w [przewodnikach z najlepszymi rozwiązaniami dla systemu SUSE][suse-ha-guide]. Są one kopiowane dla wygody użytkownika. Zawsze należy przeczytać przewodniki dotyczące najlepszych rozwiązań i wykonać wszystkie dodatkowe testy, które mogły zostać dodane.

1. Testowanie HAGetFailoverConfig, HACheckConfig i HACheckFailoverConfig

   Uruchom następujące polecenia jako \<sapsid> adm w węźle, w którym jest aktualnie uruchomione wystąpienie ASCS. Jeśli polecenie nie powiedzie się z powodu niewystarczającej ilości pamięci, może to być spowodowane przez myślniki w nazwie hosta. Jest to znany problem, który zostanie rozwiązany przez SUSE w pakiecie SAP-SUSE-Cluster-Connector.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Ręczne Migrowanie wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Uruchom następujące polecenia jako główne, aby zmigrować wystąpienie ASCS.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Test HAFailoverToNode

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Uruchom następujące polecenia jako \<sapsid> adm, aby zmigrować wystąpienie ASCS.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Symulowanie awarii węzła 

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenie jako element główny w węźle, w którym jest uruchomione wystąpienie ASCS

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Jeśli używasz SBD, Pacemaker nie powinien automatycznie rozpoczynać się w zamkniętym węźle. Stan po ponownym uruchomieniu węzła powinien wyglądać następująco.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Użyj następujących poleceń, aby uruchomić Pacemaker w zamkniętym węźle, wyczyść komunikaty SBD i wyczyść zasoby zakończone niepowodzeniem.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Testowanie ręcznego ponownego uruchomienia wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Utwórz blokadę do kolejki, na przykład Edytuj użytkownika w usłudze Transaction su01. Uruchom następujące polecenia jako <sapsid \> adm w węźle, w którym działa wystąpienie ASCS. Polecenia zatrzymają wystąpienie ASCS i uruchomią je ponownie. W przypadku korzystania z architektury Server 1 w kolejce w tym teście oczekuje się, że blokada kolejki zostanie utracona. W przypadku korzystania z architektury serwera 2 w kolejce zostanie zachowany. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Wystąpienie ASCS powinno być teraz wyłączone w Pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Uruchom ponownie wystąpienie ASCS w tym samym węźle.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Blokada kolejki transakcji su01 powinna zostać utracona, jeśli jest używana architektura replikacji serwera w kolejce 1, a zaplecze powinno zostać zresetowane. Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Przerwij proces serwera komunikatów

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenia jako główne, aby zidentyfikować proces serwera komunikatów i Kasuj go.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Jeśli serwer wiadomości zostanie skasowany tylko raz, zostanie on ponownie uruchomiony przez program `sapstart` . Jeśli zakończysz go często, Pacemaker będzie ostatecznie przenieść wystąpienie ASCS do innego węzła. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia ASCS i wykres WYWOŁUJĄCYCH po teście.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Kasuj proces serwera z kolejki

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Uruchom następujące polecenia jako element główny w węźle, w którym uruchomiono wystąpienie ASCS, aby skasować serwer z kolejki.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   Wystąpienie ASCS powinno natychmiast przejść w tryb failover do innego węzła. Wystąpienie wykres WYWOŁUJĄCYCH powinno również przechodzić w tryb failover po rozpoczęciu wystąpienia ASCS. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia ASCS i wykres WYWOŁUJĄCYCH po teście.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Kasuj proces serwera replikacji z kolejki

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenie jako element główny w węźle, w którym uruchomiono wystąpienie wykres WYWOŁUJĄCYCH, aby skasować proces serwera replikacji z kolejki.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Jeśli uruchamiasz polecenie tylko raz, `sapstart` program uruchomi ponownie ten proces. Jeśli uruchomisz go często, program `sapstart` nie uruchomi ponownie procesu, a zasób stanie się zatrzymany. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia wykres WYWOŁUJĄCYCH po teście.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Kasuj proces sapstartsrvnia w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Uruchom następujące polecenia jako element główny w węźle, w którym działa ASCS.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Proces sapstartsrv powinien być zawsze ponownie uruchamiany przez agenta zasobów Pacemaker. Stan zasobu po teście:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [HA for SAP NW na maszynach wirtualnych platformy Azure w systemie SLES for SAP — Przewodnik dotyczący wiele identyfikatorów SID](./high-availability-guide-suse-multi-sid.md)
* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]