---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla oprogramowania SAP NW na RHEL z Azure NetApp Files | Microsoft Docs
description: Ustanów wysoką dostępność dla oprogramowania SAP NW na maszynach wirtualnych platformy Azure RHEL z Azure NetApp Files.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: 0c5ebd3f7989458a0966fdc792cd3a8a9ea94acc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965276"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Platforma Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux z Azure NetApp Files dla aplikacji SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych, instalowania platformy klastra i instalowania systemu SAP NetWeaver 7,50 o wysokiej dostępności przy użyciu [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
W przykładowych konfiguracjach polecenia instalacji itp. Wystąpienie ASCS ma numer 00, wystąpienie wykres WYWOŁUJĄCYCH ma numer 01, podstawowe wystąpienie aplikacji (PAS) to 02, a wystąpienie aplikacji (AAS) to 03. Identyfikator systemowy SAP QAS jest używany. 

Warstwa bazy danych nie jest szczegółowo omówiona w tym artykule.  

Przeczytaj najpierw następujące informacje i dokumenty SAP:

* [Dokumentacja Azure NetApp Files][anf-azure-doc] 
* Nota SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure

* Uwaga dotycząca protokołu SAP [2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2002167] zalecane ustawienia systemu operacyjnego dla Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2009879] SAP HANA wytycznych dotyczących Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca programu SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [NetWeaver SAP w klastrze Pacemaker](https://access.redhat.com/articles/3150081)
* Ogólna dokumentacja RHEL
  * [Omówienie Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Informacje o wysokiej dostępności Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurowanie ASCS/wykres WYWOŁUJĄCYCH dla SAP NetWeaver z zasobami autonomicznymi w RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [Skonfiguruj rozwiązanie SAP S/4HANA ASCS/wykres WYWOŁUJĄCYCH za pomocą autonomicznej kolejki serwer 2 (ENSA2) w Pacemaker na RHEL ](https://access.redhat.com/articles/3974941)
* Dokumentacja RHEL specyficzna dla platformy Azure:
  * [Zasady obsługi klastrów RHEL o wysokiej dostępności — Microsoft Azure Virtual Machines jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i Konfigurowanie Red Hat Enterprise Linux 7,4 (i nowszych) High-Availability klastra na Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp aplikacje SAP na Microsoft Azure przy użyciu Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Omówienie

Wysoka dostępność (HA) dla usług SAP NetWeaver Central Services wymaga magazynu udostępnionego.
Aby osiągnąć ten dostęp w systemie Red Hat Linux, konieczne było skompilowanie oddzielnego klastra GlusterFS o wysokiej dostępności. 

Teraz można osiągnąć rozwiązanie SAP NetWeaver HA przy użyciu magazynu udostępnionego, wdrożonego na Azure NetApp Files. Używanie Azure NetApp Files dla magazynu udostępnionego eliminuje konieczność dodatkowego [klastra GlusterFS](./high-availability-guide-rhel-glusterfs.md). Pacemaker jest nadal wymagana w przypadku wysokiej dostępności usług SAP NetWeaver Central Services (ASCS/SCS).

![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver wykres WYWOŁUJĄCYCH, a SAP HANA Database używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Zalecamy korzystanie z usługi [równoważenia obciążenia w warstwie Standardowa](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia z oddzielnymi adresami IP frontonu dla (A) SCS i wykres WYWOŁUJĄCYCH.

### <a name="ascs"></a>Z SCS

* Konfiguracja frontonu
  * Adres IP 192.168.14.9
* Port sondy
  * Port 620<strong> &lt; Nr &gt; </strong>
* Reguły równoważenia obciążenia
  * W przypadku używania usługa Load Balancer w warstwie Standardowa wybierz pozycję **porty ha**
  * 32<strong> &lt; Nr &gt; </strong> TCP
  * 36<strong> &lt; Nr &gt; </strong> TCP
  * 39<strong> &lt; Nr &gt; </strong> TCP
  * 81<strong> &lt; Nr &gt; </strong> TCP
  * 5<strong> &lt; Nr &gt; </strong>13 TCP
  * 5<strong> &lt; Nr &gt; </strong>14 TCP
  * 5<strong> &lt; Nr &gt; </strong>16 TCP

### <a name="ers"></a>Wykres WYWOŁUJĄCYCH

* Konfiguracja frontonu
  * Adres IP 192.168.14.10
* Port sondy
  * Port 621<strong> &lt; Nr &gt; </strong>
* Reguły równoważenia obciążenia
  * W przypadku używania usługa Load Balancer w warstwie Standardowa wybierz pozycję **porty ha**
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

5. Wdróż woluminy Azure NetApp Files, postępując zgodnie z [instrukcjami, aby utworzyć wolumin dla Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Wdróż woluminy w wyznaczeniej [podsieci](/rest/api/virtualnetwork/subnets)Azure NetApp Files. Adresy IP woluminów NetApp platformy Azure są przypisywane automatycznie. Należy pamiętać, że zasoby Azure NetApp Files i maszyny wirtualne platformy Azure muszą znajdować się w tym samym Virtual Network platformy Azure lub w równorzędnych sieciach wirtualnych platformy Azure. W tym przykładzie używamy dwóch Azure NetApp Files woluminów: SAP<b>QAS</b> i transSAP. Ścieżki plików, które są zainstalowane do odpowiednich punktów instalacji, to/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys itd.  

   1. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASCS)
   3. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>wykres wywołujących)
   5. Volume transSAP (nfs://192.168.24.4/transSAP)
   6. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)
  
W tym przykładzie użyto Azure NetApp Files dla wszystkich systemów plików SAP NetWeaver, aby zademonstrować, jak Azure NetApp Files może być używany. Systemy plików SAP, które nie muszą być instalowane za pośrednictwem systemu plików NFS, można również wdrożyć jako [usługę Azure Disk Storage](../../disks-types.md#premium-ssd) . W tym przykładzie <b>a-e</b> musi znajdować się w Azure NetApp Files i <b>f-g</b> (czyli/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) można wdrożyć jako usługę Azure Disk Storage. 

### <a name="important-considerations"></a>Istotne zagadnienia

Rozważając Azure NetApp Files dla architektury SAP NetWeaver w systemie SUSE wysokiej dostępności, weź pod uwagę następujące ważne zagadnienia:

- Minimalna Pula pojemności to 4 TiB. Rozmiar puli pojemności można zwiększyć w 1 TiB przyrostów.
- Minimalny wolumin to 100 GiB
- Azure NetApp Files i wszystkie maszyny wirtualne, w których zostaną zainstalowane woluminy Azure NetApp Files, muszą znajdować się w tej samej Virtual Network platformy Azure lub w [równorzędnych sieciach wirtualnych](../../../virtual-network/virtual-network-peering-overview.md) w tym samym regionie. Azure NetApp Files dostęp za pośrednictwem komunikacji równorzędnej sieci wirtualnej w tym samym regionie jest obecnie obsługiwany. Dostęp do usługi Azure NetApp za pośrednictwem globalnej komunikacji równorzędnej nie jest jeszcze obsługiwany.
- Wybrana Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files.
- Azure NetApp Files oferuje [zasady eksportowania](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): można kontrolować dozwolonych klientów, typ dostępu (odczyt&zapis, tylko do odczytu itp.). 
- Funkcja Azure NetApp Files nie jest jeszcze dostępna dla strefy. Obecnie Azure NetApp Files funkcja nie jest wdrażana we wszystkich strefach dostępności w regionie świadczenia usługi Azure. Weź pod uwagę potencjalne konsekwencje opóźnienia w niektórych regionach świadczenia usługi Azure. 
- Woluminy Azure NetApp Files można wdrożyć jako woluminy NFSv3 lub NFSv 4.1. Oba protokoły są obsługiwane dla warstwy aplikacji SAP (ASCS/wykres WYWOŁUJĄCYCH, serwery aplikacji SAP). 

## <a name="setting-up-ascs"></a>Konfigurowanie (A) SCS

W tym przykładzie zasoby zostały wdrożone ręcznie za pośrednictwem [Azure Portal](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pośrednictwem Azure Portal

Najpierw należy utworzyć woluminy Azure NetApp Files. Wdróż maszyny wirtualne. Następnie należy utworzyć moduł równoważenia obciążenia i użyć maszyn wirtualnych w puli zaplecza.

1. Tworzenie modułu równoważenia obciążenia (wewnętrznego, standardowego):  
   1. Utwórz adresy IP frontonu
      1. Adres IP 192.168.14.9 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pula adresów IP frontonu, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **frontonu). QAS. ASCS**)
         1. Ustaw przypisanie na static i wprowadź adres IP (na przykład **192.168.14.9**)
         1. Kliknij przycisk OK.
      1. 192.168.14.10 adresu IP dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki w obszarze "a", aby utworzyć adres IP dla wykres WYWOŁUJĄCYCH (na przykład **192.168.14.10** i **fronton. QAS. Wykres WYWOŁUJĄCYCH**)
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pule zaplecza, a następnie kliknij przycisk Dodaj.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **zaplecze. QAS**)
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz pozycję Maszyna wirtualna. 
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
      1. Reguły równoważenia obciążenia dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **LB. QAS. ASCS**)
         1. Wybierz adres IP frontonu dla ASCS, puli zaplecza i sondy kondycji utworzonej wcześniej (na przykład **frontonu). QAS. ASCS**, **zaplecze. QAS** i **kondycja. QAS. ASCS**)
         1. Wybieranie **portów ha**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Upewnij się, że włączono zmiennoprzecinkowy adres IP**
         1. Kliknij przycisk OK.
         * Powtórz powyższe kroki, aby utworzyć reguły równoważenia obciążenia dla wykres WYWOŁUJĄCYCH (na przykład **LB. QAS. Wykres WYWOŁUJĄCYCH**)
1. Alternatywnie, jeśli scenariusz wymaga podstawowego modułu równoważenia obciążenia (wewnętrznego), wykonaj następujące czynności:  
   1. Utwórz adresy IP frontonu
      1. Adres IP 192.168.14.9 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pula adresów IP frontonu, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **frontonu). QAS. ASCS**)
         1. Ustaw przypisanie na static i wprowadź adres IP (na przykład **192.168.14.9**)
         1. Kliknij przycisk OK.
      1. 192.168.14.10 adresu IP dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki w obszarze "a", aby utworzyć adres IP dla wykres WYWOŁUJĄCYCH (na przykład **192.168.14.10** i **fronton. QAS. Wykres WYWOŁUJĄCYCH**)
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
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia, a następnie kliknij przycisk Dodaj.
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
      > Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.  

      > [!Note]
      > Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > Nie należy włączać sygnatur czasowych protokołu TCP na maszynach wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie sygnatur czasowych protokołu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.IPv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Wyłącz mapowanie identyfikatora (jeśli jest używany NFSv 4.1)

Instrukcje zawarte w tej sekcji mają zastosowanie tylko w przypadku korzystania z Azure NetApp Files woluminów z protokołem NFSv 4.1. Wykonaj konfigurację na wszystkich maszynach wirtualnych, gdzie zostaną zainstalowane woluminy Azure NetApp Files NFSv 4.1.  

1. Sprawdź ustawienie domeny systemu plików NFS. Upewnij się, że domena jest skonfigurowana jako domyślna domena Azure NetApp Files, tj., **`defaultv4iddomain.com`** a mapowanie jest ustawione na wartość **nikt**.  

    > [!IMPORTANT]
    > Upewnij się, że na maszynie wirtualnej ustawiono domenę systemu plików NFS `/etc/idmapd.conf` zgodną z domyślną konfiguracją domeny na Azure NetApp Files: **`defaultv4iddomain.com`** . Jeśli istnieje niezgodność między konfiguracją domeny na kliencie NFS (tj. maszyną wirtualną) a serwerem NFS, tj. konfiguracją usługi Azure NetApp, wówczas uprawnienia do plików na woluminach NetApp platformy Azure, które są zainstalowane na maszynach wirtualnych, będą wyświetlane jako `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
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
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Aby uzyskać więcej informacji na temat zmiany `nfs4_disable_idmapping` parametru, zobacz https://access.redhat.com/solutions/1749883 .

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w temacie [Konfigurowanie Pacemaker Red Hat Enterprise Linux na platformie Azure](high-availability-guide-rhel-pacemaker.md) , aby utworzyć podstawowy klaster Pacemaker dla tego (a) serwera SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

Następujące elementy są poprzedzone **[A]** -dotyczy wszystkie węzły, **[1]** — dotyczy tylko węzła 1 lub **[2]** — dotyczy tylko węzła 2.

1. **[A]** rozpoznawanie nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

    ```
    sudo vi /etc/hosts
    ```

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** utwórz katalogi SAP na woluminie Azure NetApp Files.  
   Zainstaluj tymczasowo wolumin Azure NetApp Files na jednej z maszyn wirtualnych i utwórz katalogi SAP (ścieżki plików).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
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

1. **[A]** Tworzenie katalogów udostępnionych

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** Zainstaluj klienta NFS i inne wymagania

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Sprawdź wersję zasobów agentów — SAP

   Upewnij się, że wersja zainstalowanych pakietów Resource-Agents — SAP to co najmniej 3.9.5 -124. el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Dodawanie wpisów instalacji

   Jeśli używasz NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Jeśli jest używany program NFSv 4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Przed zainstalowaniem woluminów upewnij się, że jest zgodna z wersją protokołu NFS Azure NetApp Files woluminów. Jeśli woluminy Azure NetApp Files są tworzone jako woluminy NFSv3, użyj odpowiedniej konfiguracji NFSv3. Jeśli woluminy Azure NetApp Files są tworzone jako woluminy NFSv 4.1, postępuj zgodnie z instrukcjami, aby wyłączyć mapowanie identyfikatorów i upewnij się, że używasz odpowiedniej konfiguracji NFSv 4.1. W tym przykładzie woluminy Azure NetApp Files zostały utworzone jako woluminy NFSv3.  

   Zainstaluj nowe udziały

   ```
   sudo mount -a  
   ```

1. **[A]** Skonfiguruj plik wymiany

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Uruchom ponownie agenta, aby aktywować zmianę

   ```
   sudo service waagent restart
   ```

1. **[A]** konfiguracja RHEL

   Konfigurowanie RHEL zgodnie z opisem w temacie SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalowanie oprogramowania SAP NetWeaver ASCS/wykres WYWOŁUJĄCYCH

1. **[1]** Utwórz zasób wirtualnego adresu IP i sondę kondycji dla wystąpienia ASCS

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Instalowanie oprogramowania SAP NetWeaver ASCS  

   Zainstaluj oprogramowanie SAP NetWeaver ASCS jako element główny w pierwszym węźle przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla ASCS, na przykład <b>anftstsapvh</b>, <b>192.168.14.9</b> i numer wystąpienia, które zostało użyte do sondowania modułu równoważenia obciążenia, na przykład <b>00</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w/usr/SAP/**QAS**/ASCS **00**, spróbuj ustawić właściciela i grupę folderu ASCS **00** , a następnie ponów próbę.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Utwórz zasób wirtualnego adresu IP i sondę kondycji dla wystąpienia wykres wywołujących

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Instalowanie oprogramowania SAP NetWeaver wykres wywołujących  

   Zainstaluj program SAP NetWeaver wykres WYWOŁUJĄCYCH jako element główny w drugim węźle przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla wykres WYWOŁUJĄCYCH, na przykład <b>anftstsapers</b>, <b>192.168.14.10</b> i numer wystąpienia, które zostało użyte do sondowania modułu równoważenia obciążenia, na przykład <b>01</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Jeśli instalacja nie powiedzie się, utwórz podfolder w/usr/SAP/**QAS**/ERS **01**, spróbuj ustawić właściciela i grupę folderu wykres wywołujących **01** i ponów próbę.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** dostosowuje profile wystąpienia ASCS/SCS i wykres wywołujących

   * Profil ASCS/SCS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   W przypadku programów ENSA1 i ENSA2 upewnij się, że `keepalive` parametry systemu operacyjnego zostały ustawione zgodnie z opisem w temacie SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

   * Profil wykres WYWOŁUJĄCYCH

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Skonfiguruj utrzymywanie aktywności

   Komunikacja między serwerem aplikacji SAP NetWeaver a ASCS/SCS jest kierowana przez programowy moduł równoważenia obciążenia. Moduł równoważenia obciążenia rozłącza nieaktywne połączenia po upływie konfigurowalnego limitu czasu. Aby tego uniknąć, należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS, jeśli jest używany ENSA1, i zmienić ustawienia systemu Linux `keepalive` na wszystkich serwerach SAP dla obu ENSA1/ENSA2. Aby uzyskać więcej informacji, przeczytaj temat [SAP Note 1410736][1410736] .

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   ```

1. **[A]** Zaktualizuj plik/usr/SAP/sapservices

   Aby zapobiec uruchamianiu wystąpień przez skrypt uruchamiania sapinit, wszystkie wystąpienia zarządzane przez Pacemaker muszą być oznaczone jako komentarze z pliku/usr/SAP/sapservices. Nie należy dodawać komentarzy do wystąpienia SAP HANA, jeśli będzie on używany z usługą HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** tworzenie zasobów klastra SAP

   W przypadku korzystania z architektury Server 1 Architecture (ENSA1) Zdefiniuj zasoby w następujący sposób:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   System SAP wprowadził obsługę dla kolejki serwera 2, w tym replikację, w przypadku oprogramowania SAP NW 7,52. Począwszy od programu ABAP platform 1809, w kolejce serwer 2 jest instalowany domyślnie. Zobacz temat SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) dla obsługi kolejki serwera 2.
   W przypadku korzystania z programu w ramach architektury Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) Zainstaluj agenta zasobów agentów zasobów — SAP-4.1.1-12.el7.x86_64 lub nowszego i zdefiniuj zasoby w następujący sposób:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   W przypadku uaktualniania ze starszej wersji i przełączania do kolejki serwera 2, zobacz uwagi dotyczące oprogramowania SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Przekroczenia limitów czasu w powyższej konfiguracji są tylko przykłady i konieczne może być dostosowanie do określonej instalacji SAP. 

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** Dodaj reguły zapory dla ASCS i wykres wywołujących na obu węzłach Dodaj reguły zapory dla ASCS i wykres wywołujących na obu węzłach.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowywanie serwera aplikacji SAP NetWeaver

   Niektóre bazy danych wymagają wykonania instalacji wystąpienia bazy danych na serwerze aplikacji. Przygotuj maszyny wirtualne serwera aplikacji, aby mogły z nich korzystać w tych przypadkach.  

   W poniższych krokach założono, że serwer aplikacji jest instalowany na serwerze innym niż serwery ASCS/SCS i HANA. W przeciwnym razie niektóre kroki (takie jak Konfigurowanie rozpoznawania nazw hostów) nie są wymagane.  

   Następujące elementy są poprzedzone znakiem **[A]** — dotyczy zarówno platformy pas, jak i AAS, **[P]** — dotyczy tylko platformy pas lub **[S]** — dotyczy tylko AAS.  

1. **[A]** rozpoznawanie nazw hostów można użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach:  

   ```
   sudo vi /etc/hosts
   ```

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta w taki sposób, aby odpowiadały Twojemu środowisku.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Utwórz katalog sapmnt Utwórz katalog sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** Zainstaluj klienta NFS i inne wymagania  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Dodawanie wpisów instalacji  
   Jeśli używasz NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Jeśli jest używany program NFSv 4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Zainstaluj nowe udziały

   ```
   sudo mount -a
   ```

1. **[P]** Utwórz i zainstaluj katalog pas  
   Jeśli używasz NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Jeśli jest używany program NFSv 4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** Utwórz i zainstaluj katalog AAS  
   Jeśli używasz NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Jeśli jest używany program NFSv 4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** Skonfiguruj plik wymiany
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Uruchom ponownie agenta, aby aktywować zmianę

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie system SAP NetWeaver jest instalowany na SAP HANA. Dla tej instalacji można użyć każdej obsługiwanej bazy danych. Aby uzyskać więcej informacji na temat instalowania SAP HANA na platformie Azure, zobacz [wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure w Red Hat Enterprise Linux][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. Uruchamianie instalacji wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako element główny przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla bazy danych.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Instalacja serwera aplikacji SAP NetWeaver

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP.

1. Przygotuj serwer aplikacji

   Aby przygotować serwer aplikacji, wykonaj kroki opisane w rozdziale " [Przygotowywanie serwera aplikacji SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) " powyżej.

1. Zainstaluj serwer aplikacji SAP NetWeaver

   Zainstaluj podstawowy lub dodatkowy serwer aplikacji SAP NetWeaver.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Aktualizacja SAP HANA bezpiecznego magazynu

   Zaktualizuj SAP HANA bezpiecznego magazynu, aby wskazywał nazwę wirtualną konfiguracji replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów jako \<sapsid> adm

   ```
   hdbuserstore List
   ```

   Powinno to być lista wszystkich wpisów i powinna wyglądać podobnie do
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   Dane wyjściowe wskazują, że adres IP wpisu domyślnego wskazuje maszynę wirtualną, a nie adres IP modułu równoważenia obciążenia. Ten wpis należy zmienić, aby wskazywał wirtualną nazwę hosta usługi równoważenia obciążenia. Upewnij się, że używasz tego samego portu (**30313** w danych wyjściowych powyżej) i nazwy bazy danych (**QAS** w danych wyjściowych powyżej)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

1. Ręczne Migrowanie wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Uruchom następujące polecenia jako główne, aby zmigrować wystąpienie ASCS.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Symulowanie awarii węzła

   Stan zasobu przed rozpoczęciem testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Uruchom następujące polecenie jako element główny w węźle, w którym jest uruchomione wystąpienie ASCS

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   Stan po ponownym uruchomieniu węzła powinien wyglądać następująco.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Użyj poniższego polecenia, aby wyczyścić zasoby zakończone niepowodzeniem.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Przerwij proces serwera komunikatów

   Stan zasobu przed rozpoczęciem testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Uruchom następujące polecenia jako główne, aby zidentyfikować proces serwera komunikatów i Kasuj go.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Jeśli serwer wiadomości zostanie skasowany tylko raz, zostanie on ponownie uruchomiony przez program `sapstart` . Jeśli zakończysz go często, Pacemaker będzie ostatecznie przenieść wystąpienie ASCS do innego węzła. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia ASCS i wykres WYWOŁUJĄCYCH po teście.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Kasuj proces serwera z kolejki

   Stan zasobu przed rozpoczęciem testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Uruchom następujące polecenia jako element główny w węźle, w którym uruchomiono wystąpienie ASCS, aby skasować serwer z kolejki.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   Wystąpienie ASCS powinno natychmiast przejść w tryb failover do innego węzła. Wystąpienie wykres WYWOŁUJĄCYCH powinno również przechodzić w tryb failover po rozpoczęciu wystąpienia ASCS. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia ASCS i wykres WYWOŁUJĄCYCH po teście.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Kasuj proces serwera replikacji z kolejki

   Stan zasobu przed rozpoczęciem testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Uruchom następujące polecenie jako element główny w węźle, w którym uruchomiono wystąpienie wykres WYWOŁUJĄCYCH, aby skasować proces serwera replikacji z kolejki.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Jeśli uruchamiasz polecenie tylko raz, `sapstart` program uruchomi ponownie ten proces. Jeśli uruchomisz go często, program `sapstart` nie uruchomi ponownie procesu, a zasób stanie się zatrzymany. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia wykres WYWOŁUJĄCYCH po teście.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Kasuj proces sapstartsrvnia w kolejce

   Stan zasobu przed rozpoczęciem testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Uruchom następujące polecenia jako element główny w węźle, w którym działa ASCS.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Proces sapstartsrv powinien być zawsze ponownie uruchamiany przez agenta zasobów Pacemaker w ramach monitorowania. Stan zasobu po teście:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Następne kroki

* [HA for SAP NW na maszynach wirtualnych platformy Azure w systemie RHEL for SAP — Przewodnik dotyczący wiele identyfikatorów SID](./high-availability-guide-rhel-multi-sid.md)
* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak stworzyć wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
