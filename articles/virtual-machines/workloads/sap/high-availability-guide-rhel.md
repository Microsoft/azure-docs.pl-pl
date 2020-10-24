---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla oprogramowania SAP NW w systemie RHEL | Microsoft Docs
description: Platforma Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: 9b4684f8d9a6bd04a11961632b616258db7344a3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487572"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Platforma Azure Virtual Machines wysoka dostępność dla oprogramowania SAP NetWeaver na Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych, instalowania platformy klastra i instalowania systemu SAP NetWeaver 7,50 o wysokiej dostępności.
W przykładowych konfiguracjach polecenia instalacji itp. Użyto wystąpienia ASCS o numerze 00, wykres WYWOŁUJĄCYCH wystąpienia o numerze 02 oraz identyfikatora systemu SAP NW1. Nazwy zasobów (na przykład Virtual Machines, Virtual Networks) w przykładzie założono, że do tworzenia zasobów użyto [szablonu ASCS/SCS][template-multisid-xscs] z prefiksem zasobu NW1.

Najpierw przeczytaj następujące informacje i dokumenty SAP

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
* [Dokumentacja produktu dla magazynu Red Hat Gluster](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [NetWeaver SAP w klastrze Pacemaker](https://access.redhat.com/articles/3150081)
* Ogólna dokumentacja RHEL
  * [Omówienie Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Informacje o wysokiej dostępności Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurowanie ASCS/wykres WYWOŁUJĄCYCH dla SAP NetWeaver z zasobami autonomicznymi w RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [Skonfiguruj rozwiązanie SAP S/4HANA ASCS/wykres WYWOŁUJĄCYCH za pomocą autonomicznej kolejki serwer 2 (ENSA2) w Pacemaker na RHEL ](https://access.redhat.com/articles/3974941)
* Dokumentacja usługi RHEL specyficznej dla platformy Azure:
  * [Zasady obsługi klastrów RHEL o wysokiej dostępności — Microsoft Azure Virtual Machines jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i Konfigurowanie Red Hat Enterprise Linux 7,4 (i nowszych) High-Availability klastra na Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Omówienie

Aby zapewnić wysoką dostępność, rozwiązanie SAP NetWeaver wymaga magazynu udostępnionego. GlusterFS jest skonfigurowany w oddzielnym klastrze i może być używany przez wiele systemów SAP.

![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver wykres WYWOŁUJĄCYCH, a SAP HANA Database używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Zalecamy korzystanie z usługi [równoważenia obciążenia w warstwie Standardowa](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia (A) SCS i wykres WYWOŁUJĄCYCH.

### <a name="ascs"></a>Z SCS

* Konfiguracja frontonu
  * Adres IP 10.0.0.7
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
  * Adres IP 10.0.0.8
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

## <a name="setting-up-glusterfs"></a>Konfigurowanie GlusterFS

Rozwiązanie SAP NetWeaver wymaga udostępnionego magazynu dla katalogu transportowego i profilu. Przeczytaj [GlusterFS na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver][glusterfs-ha] na temat konfigurowania GlusterFS dla oprogramowania SAP NetWeaver.

## <a name="setting-up-ascs"></a>Konfigurowanie (A) SCS

Możesz użyć szablonu platformy Azure z usługi GitHub do wdrożenia wszystkich wymaganych zasobów platformy Azure, w tym maszyn wirtualnych, zestawu dostępności i modułu równoważenia obciążenia, lub ręcznie wdrożyć zasoby.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux za pomocą szablonu platformy Azure

Portal Azure Marketplace zawiera obraz Red Hat Enterprise Linux, który służy do wdrażania nowych maszyn wirtualnych. Możesz użyć jednego z szablonów szybkiego startu w usłudze GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, moduł równoważenia obciążenia, zestaw dostępności itd. Wykonaj następujące kroki, aby wdrożyć szablon:

1. Otwórz [szablon ASCS/SCS][template-multisid-xscs] na Azure Portal  
1. Wprowadź następujące parametry
   1. Prefiks zasobu  
      Wprowadź prefiks, którego chcesz użyć. Wartość jest używana jako prefiks dla wdrożonych zasobów.
   1. Typ stosu  
      Wybierz typ stosu SAP NetWeaver
   1. Typ systemu operacyjnego  
      Wybierz jedną z dystrybucji systemu Linux. Na potrzeby tego przykładu wybierz pozycję RHEL 7
   1. Typ bazy danych  
      Wybierz platformę HANA
   1. Liczba systemów SAP  
      Liczba systemów SAP uruchomionych w tym klastrze. Wybierz pozycję 1.
   1. Dostępność systemu  
      Wybierz HA
   1. Nazwa użytkownika administratora, hasło administratora lub klucz SSH  
      Zostanie utworzony nowy użytkownik, którego można użyć do zalogowania się na komputerze.
   1. Identyfikator podsieci  
   Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Identyfikator zazwyczaj wygląda tak, jak** &lt; Identyfikator &gt; subskrypcji**/subscriptions//ResourceGroups/nazwa** &lt; grupy &gt; zasobów**/Providers/Microsoft.Network/virtualNetworks/nazwa** &lt; sieci &gt; wirtualnej**/Subnets/** &lt; nazwa &gt; podsieci**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pośrednictwem Azure Portal

Najpierw musisz utworzyć maszyny wirtualne dla tego klastra. Następnie należy utworzyć moduł równoważenia obciążenia i użyć maszyn wirtualnych w puli zaplecza.

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Tworzenie zestawu dostępności  
   Ustaw maksymalną domenę aktualizacji
1. Utwórz maszynę wirtualną 1  
   Użyj co najmniej RHEL 7, w tym przykładzie obrazu Red Hat Enterprise Linux 7,4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Tworzenie maszyny wirtualnej 2  
   Użyj co najmniej RHEL 7, w tym przykładzie obrazu Red Hat Enterprise Linux 7,4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Dodaj co najmniej jeden dysk danych do obu maszyn wirtualnych  
   Dyski danych są używane dla `<SAPSID` katalogu>/usr/SAP/
1. Tworzenie modułu równoważenia obciążenia (wewnętrznego, standardowego):  
   1. Utwórz adresy IP frontonu
      1. Adres IP 10.0.0.7 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pula adresów IP frontonu, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **NW1-ASCS-fronton**)
         1. Ustaw przypisanie na static i wprowadź adres IP (na przykład **10.0.0.7**)
         1. Kliknij przycisk OK.
      1. 10.0.0.8 adresu IP dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki, aby utworzyć adres IP dla wykres WYWOŁUJĄCYCH (na przykład **10.0.0.8** i **NW1-AERS-fronton**)
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pule zaplecza, a następnie kliknij przycisk Dodaj.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **NW1-zaplecza**)
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz pozycję Maszyna wirtualna.
      1. Wybierz Maszyny wirtualne klastra SCS i ich adresy IP.
      1. Kliknij pozycję Dodaj.
   1. Tworzenie sond kondycji
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **NW1-ASCS-HP**)
         1. Wybierz pozycję TCP jako protokół, port 620**00**, Zachowaj interwał 5 i próg złej kondycji 2
         1. Kliknij przycisk OK.
      1. Port 621**02** dla ASCS wykres wywołujących
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla wykres WYWOŁUJĄCYCH (na przykład 621**02** i **NW1-AERS-HP**)
   1. Reguły równoważenia obciążenia
      1. Reguły równoważenia obciążenia dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **NW1-lb-ASCS**)
         1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **NW1-ASCS-fronton**, **NW1-zaplecze** i **NW1-ASCS-HP**)
         1. Wybieranie **portów ha**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Upewnij się, że włączono zmiennoprzecinkowy adres IP**
         1. Kliknij przycisk OK.
         * Powtórz powyższe kroki, aby utworzyć reguły równoważenia obciążenia dla wykres WYWOŁUJĄCYCH (na przykład **NW1-lb-wykres wywołujących**)
1. Alternatywnie, jeśli scenariusz wymaga podstawowego modułu równoważenia obciążenia (wewnętrznego), wykonaj następujące czynności:  
   1. Utwórz adresy IP frontonu
      1. Adres IP 10.0.0.7 dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pula adresów IP frontonu, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **NW1-ASCS-fronton**)
         1. Ustaw przypisanie na static i wprowadź adres IP (na przykład **10.0.0.7**)
         1. Kliknij przycisk OK.
      1. 10.0.0.8 adresu IP dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki, aby utworzyć adres IP dla wykres WYWOŁUJĄCYCH (na przykład **10.0.0.8** i **NW1-AERS-fronton**)
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pule zaplecza, a następnie kliknij przycisk Dodaj.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **NW1-zaplecza**)
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz utworzony wcześniej zestaw dostępności
      1. Wybierz Maszyny wirtualne klastra SCS
      1. Kliknij przycisk OK.
   1. Tworzenie sond kondycji
      1. Port 620**00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **NW1-ASCS-HP**)
         1. Wybierz pozycję TCP jako protokół, port 620**00**, Zachowaj interwał 5 i próg złej kondycji 2
         1. Kliknij przycisk OK.
      1. Port 621**02** dla ASCS wykres wywołujących
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla wykres WYWOŁUJĄCYCH (na przykład 621**02** i **NW1-AERS-HP**)
   1. Reguły równoważenia obciążenia
      1. 32**00** TCP dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **NW1-lb-3200**)
         1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **NW1-ASCS-fronton**)
         1. Utrzymywanie protokołu **TCP**, wprowadź port **3200**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Upewnij się, że włączono zmiennoprzecinkowy adres IP**
         1. Kliknij przycisk OK.
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki dla portów 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 i TCP dla ASCS
      1. Dodatkowe porty dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki dla portów 33**02**, 5**02**13, 5**02**14, 5**02**16 i TCP dla ASCS wykres wywołujących

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.  

> [!Note]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych protokołu TCP na maszynach wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie sygnatur czasowych protokołu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.IPv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w temacie [Konfigurowanie Pacemaker Red Hat Enterprise Linux na platformie Azure](high-availability-guide-rhel-pacemaker.md) , aby utworzyć podstawowy klaster Pacemaker dla tego (a) serwera SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

Następujące elementy są poprzedzone **[A]** -dotyczy wszystkie węzły, **[1]** — dotyczy tylko węzła 1 lub **[2]** — dotyczy tylko węzła 2.

1. **[A]** rozpoznawanie nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** Tworzenie katalogów udostępnionych

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** Zainstaluj klienta GlusterFS i inne wymagania

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** Sprawdź wersję zasobów agentów — SAP

   Upewnij się, że wersja zainstalowanych pakietów Resource-Agents — SAP to co najmniej 3.9.5 -124. el7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** Dodawanie wpisów instalacji

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Zainstaluj nowe udziały

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Skonfiguruj plik wymiany

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

1. **[A]** konfiguracja RHEL

   Konfigurowanie RHEL zgodnie z opisem w temacie SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalowanie oprogramowania SAP NetWeaver ASCS/wykres WYWOŁUJĄCYCH

1. **[1]** Utwórz zasób wirtualnego adresu IP i sondę kondycji dla wystąpienia ASCS

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** Instalowanie oprogramowania SAP NetWeaver ASCS  

   Zainstaluj oprogramowanie SAP NetWeaver ASCS jako element główny w pierwszym węźle przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla ASCS, na przykład <b>NW1-ASCS</b>, <b>10.0.0.7</b> i numer wystąpienia, który został użyty do sondowania modułu równoważenia obciążenia, na przykład <b>00</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w/usr/SAP/**NW1**/ASCS**00**, spróbuj ustawić właściciela i grupę folderu ASCS**00** , a następnie ponów próbę.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Utwórz zasób wirtualnego adresu IP i sondę kondycji dla wystąpienia wykres wywołujących

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** Instalowanie oprogramowania SAP NetWeaver wykres wywołujących  

   Zainstaluj program SAP NetWeaver wykres WYWOŁUJĄCYCH jako element główny w drugim węźle przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla wykres WYWOŁUJĄCYCH, na przykład <b>NW1-AERS</b>, <b>10.0.0.8</b> i numer wystąpienia, który został użyty do sondowania modułu równoważenia obciążenia, na przykład <b>02</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w/usr/SAP/**NW1**/ERS**02**, spróbuj ustawić właściciela i grupę folderu wykres wywołujących**02** i ponów próbę.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** dostosowuje profile wystąpienia ASCS/SCS i wykres wywołujących

   * Profil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   W przypadku programów ENSA1 i ENSA2 upewnij się, że `keepalive` parametry systemu operacyjnego zostały ustawione zgodnie z opisem w temacie SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).    

   * Profil wykres WYWOŁUJĄCYCH

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** Skonfiguruj utrzymywanie aktywności

   Komunikacja między serwerem aplikacji SAP NetWeaver a ASCS/SCS jest kierowana przez programowy moduł równoważenia obciążenia. Moduł równoważenia obciążenia rozłącza nieaktywne połączenia po upływie konfigurowalnego limitu czasu. Aby tego uniknąć, należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS, jeśli jest używany ENSA1, i zmienić ustawienia systemu Linux `keepalive` na wszystkich serwerach SAP dla obu ENSA1/ENSA2. Aby uzyskać więcej informacji, przeczytaj temat [SAP Note 1410736][1410736] .

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]** Zaktualizuj plik/usr/SAP/sapservices

   Aby zapobiec uruchamianiu wystąpień przez skrypt uruchamiania sapinit, wszystkie wystąpienia zarządzane przez Pacemaker muszą być oznaczone jako komentarze z pliku/usr/SAP/sapservices. Nie należy dodawać komentarzy do wystąpienia SAP HANA, jeśli będzie on używany z usługą HANA SR.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** tworzenie zasobów klastra SAP

  W przypadku korzystania z architektury Server 1 Architecture (ENSA1) Zdefiniuj zasoby w następujący sposób:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   System SAP wprowadził obsługę dla kolejki serwera 2, w tym replikację, w przypadku oprogramowania SAP NW 7,52. Począwszy od programu ABAP platform 1809, w kolejce serwer 2 jest instalowany domyślnie. Zobacz temat SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) dla obsługi kolejki serwera 2.
   W przypadku korzystania z programu w ramach architektury Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) Zainstaluj agenta zasobów agentów zasobów — SAP-4.1.1-12.el7.x86_64 lub nowszego i zdefiniuj zasoby w następujący sposób:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   W przypadku uaktualniania ze starszej wersji i przełączania do kolejki serwera 2, zobacz uwagi dotyczące oprogramowania SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Przekroczenia limitów czasu w powyższej konfiguracji są tylko przykłady i konieczne może być dostosowanie do określonej instalacji SAP. 

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** Dodaj reguły zapory dla ASCS i wykres wywołujących na obu węzłach

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowywanie serwera aplikacji SAP NetWeaver

Niektóre bazy danych wymagają wykonania instalacji wystąpienia bazy danych na serwerze aplikacji. Przygotuj maszyny wirtualne serwera aplikacji, aby mogły z nich korzystać w tych przypadkach.

W poniższych krokach założono, że serwer aplikacji jest instalowany na serwerze innym niż serwery ASCS/SCS i HANA. W przeciwnym razie niektóre kroki (takie jak Konfigurowanie rozpoznawania nazw hostów) nie są wymagane.

1. Konfiguracja rozpoznawania nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. Tworzenie katalogu sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Zainstaluj klienta GlusterFS i inne wymagania

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Dodawanie wpisów instalacji

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Zainstaluj nowe udziały

   <pre><code>sudo mount -a
   </code></pre>

1. Konfiguruj plik wymiany
 
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

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie system SAP NetWeaver jest instalowany na SAP HANA. Dla tej instalacji można użyć każdej obsługiwanej bazy danych. Aby uzyskać więcej informacji na temat instalowania SAP HANA na platformie Azure, zobacz [wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure w Red Hat Enterprise Linux][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. Uruchamianie instalacji wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako element główny przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla bazy danych, na przykład <b>NW1-DB</b> i <b>10.0.0.13</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalacja serwera aplikacji SAP NetWeaver

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP.

1. Przygotuj serwer aplikacji

   Aby przygotować serwer aplikacji, wykonaj kroki opisane w rozdziale " [Przygotowywanie serwera aplikacji SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) " powyżej.

1. Zainstaluj serwer aplikacji SAP NetWeaver

   Zainstaluj podstawowy lub dodatkowy serwer aplikacji SAP NetWeaver.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizacja SAP HANA bezpiecznego magazynu

   Zaktualizuj SAP HANA bezpiecznego magazynu, aby wskazywał nazwę wirtualną konfiguracji replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów jako \<sapsid> adm

   <pre><code>hdbuserstore List
   </code></pre>

   Powinno to być lista wszystkich wpisów i powinna wyglądać podobnie do
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   Dane wyjściowe wskazują, że adres IP wpisu domyślnego wskazuje maszynę wirtualną, a nie adres IP modułu równoważenia obciążenia. Ten wpis należy zmienić, aby wskazywał wirtualną nazwę hosta usługi równoważenia obciążenia. Upewnij się, że używasz tego samego portu (**30313** w danych wyjściowych powyżej) i nazwy bazy danych (**HN1** w danych wyjściowych powyżej)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

1. Ręczne Migrowanie wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenia jako główne, aby zmigrować wystąpienie ASCS.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Symulowanie awarii węzła

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenie jako element główny w węźle, w którym jest uruchomione wystąpienie ASCS

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   Stan po ponownym uruchomieniu węzła powinien wyglądać następująco.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   Użyj poniższego polecenia, aby wyczyścić zasoby zakończone niepowodzeniem.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Przerwij proces serwera komunikatów

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenia jako główne, aby zidentyfikować proces serwera komunikatów i Kasuj go.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Jeśli serwer wiadomości zostanie skasowany tylko raz, zostanie on ponownie uruchomiony przez program `sapstart` . Jeśli zakończysz go często, Pacemaker będzie ostatecznie przenieść wystąpienie ASCS do innego węzła. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia ASCS i wykres WYWOŁUJĄCYCH po teście.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Kasuj proces serwera z kolejki

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenia jako element główny w węźle, w którym uruchomiono wystąpienie ASCS, aby skasować serwer z kolejki.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Wystąpienie ASCS powinno natychmiast przejść w tryb failover do innego węzła. Wystąpienie wykres WYWOŁUJĄCYCH powinno również przechodzić w tryb failover po rozpoczęciu wystąpienia ASCS. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia ASCS i wykres WYWOŁUJĄCYCH po teście.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Kasuj proces serwera replikacji z kolejki

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenie jako element główny w węźle, w którym uruchomiono wystąpienie wykres WYWOŁUJĄCYCH, aby skasować proces serwera replikacji z kolejki.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Jeśli uruchamiasz polecenie tylko raz, `sapstart` program uruchomi ponownie ten proces. Jeśli uruchomisz go często, program `sapstart` nie uruchomi ponownie procesu, a zasób stanie się zatrzymany. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia wykres WYWOŁUJĄCYCH po teście.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Kasuj proces sapstartsrvnia w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenia jako element główny w węźle, w którym działa ASCS.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   Proces sapstartsrv powinien być zawsze ponownie uruchamiany przez agenta zasobów Pacemaker w ramach monitorowania. Stan zasobu po teście:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [HA for SAP NW na maszynach wirtualnych platformy Azure w systemie RHEL for SAP — Przewodnik dotyczący wiele identyfikatorów SID](./high-availability-guide-rhel-multi-sid.md)
* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak stworzyć wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
