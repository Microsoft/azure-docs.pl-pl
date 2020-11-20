---
title: Wysoka dostępność maszyn wirtualnych platformy Azure dla oprogramowania SAP NetWeaver na SLES | Microsoft Docs
description: Przewodnik wysokiej dostępności dla oprogramowania SAP NetWeaver na SUSE Linux Enterprise Server dla aplikacji SAP
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
ms.openlocfilehash: 508fff16f96d0dfddb77b219bb545fb8580b4048
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958901"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server dla aplikacji SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych, instalowania platformy klastra i instalowania systemu SAP NetWeaver 7,50 o wysokiej dostępności.
W przykładowych konfiguracjach polecenia instalacji itp. Użyto wystąpienia ASCS o numerze 00, wykres WYWOŁUJĄCYCH wystąpienia o numerze 02 oraz identyfikatora systemu SAP NW1. Nazwy zasobów (na przykład Virtual Machines, Virtual Networks) w przykładzie założono, że do tworzenia zasobów użyto [zbieżnego szablonu][template-converged] z identyfikatorem systemowym SAP NW1.

Najpierw przeczytaj następujące informacje i dokumenty SAP

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
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Przewodniki dla systemu SUSE SAP ha Best Practices][suse-ha-guide] Przewodniki zawierają wszystkie informacje wymagane do skonfigurowania NetWeaver HA i replikacji systemu SAP HANA w środowisku lokalnym. Użyj tych przewodników jako ogólnego planu bazowego. Zapewniają one wiele bardziej szczegółowych informacji.
* [Informacje o wersji w programie SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Omówienie

Aby zapewnić wysoką dostępność, rozwiązanie SAP NetWeaver wymaga serwera NFS. Serwer NFS jest skonfigurowany w osobnym klastrze i może być używany przez wiele systemów SAP.

![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-suse/ha-suse.png)

Serwer plików NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver wykres WYWOŁUJĄCYCH, a SAP HANA Database używają wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Zalecamy korzystanie z usługi [równoważenia obciążenia w warstwie Standardowa](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia (A) SCS i wykres WYWOŁUJĄCYCH.

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


## <a name="setting-up-a-highly-available-nfs-server"></a>Konfigurowanie serwera NFS o wysokiej dostępności

Rozwiązanie SAP NetWeaver wymaga udostępnionego magazynu dla katalogu transportowego i profilu. Przeczytaj [wysoką dostępność dla systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server][nfs-ha] , jak skonfigurować serwer NFS dla oprogramowania SAP NetWeaver.

## <a name="setting-up-ascs"></a>Konfigurowanie (A) SCS

Możesz użyć szablonu platformy Azure z usługi GitHub do wdrożenia wszystkich wymaganych zasobów platformy Azure, w tym maszyn wirtualnych, zestawu dostępności i modułu równoważenia obciążenia, lub ręcznie wdrożyć zasoby.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux za pomocą szablonu platformy Azure

Portal Azure Marketplace zawiera obraz dla SUSE Linux Enterprise Server dla aplikacji SAP 12, których można użyć do wdrażania nowych maszyn wirtualnych. Obraz portalu Marketplace zawiera agenta zasobów dla oprogramowania SAP NetWeaver.

Możesz użyć jednego z szablonów szybkiego startu w usłudze GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, moduł równoważenia obciążenia, zestaw dostępności itd. Wykonaj następujące kroki, aby wdrożyć szablon:

1. Otwórz [szablon ASCS/SCS o wiele identyfikatorów SID][template-multisid-xscs] lub [szablon zbieżny][template-converged] w Azure Portal. 
   Szablon ASCS/SCS tworzy tylko reguły równoważenia obciążenia dla wystąpień SAP NetWeaver ASCS/SCS i wykres WYWOŁUJĄCYCH (tylko Linux), podczas gdy zbieżny szablon tworzy również reguły równoważenia obciążenia dla bazy danych (na przykład Microsoft SQL Server lub SAP HANA). Jeśli planujesz zainstalować system oparty na oprogramowaniu SAP NetWeaver i chcesz również zainstalować bazę danych na tych samych komputerach, użyj [zbieżnego szablonu][template-converged].
1. Wprowadź następujące parametry
   1. Prefiks zasobu (tylko szablon ASCS/SCS o wiele identyfikatorów SID)  
      Wprowadź prefiks, którego chcesz użyć. Wartość jest używana jako prefiks dla wdrożonych zasobów.
   3. Identyfikator systemu SAP (tylko zbieżny szablon)  
      Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla wdrożonych zasobów.
   4. Typ stosu  
      Wybierz typ stosu SAP NetWeaver
   5. Typ systemu operacyjnego  
      Wybierz jedną z dystrybucji systemu Linux. Na potrzeby tego przykładu wybierz pozycję SLES 12 BYOS
   6. Typ bazy danych  
      Wybierz platformę HANA
   7. Rozmiar systemu SAP.  
      Ilość punktów SAP udostępnianych przez nowy system. Jeśli nie masz pewności, ile punktów SAP wymaga system, skontaktuj się z partnerem technologii SAP lub integratorem systemu
   8. Dostępność systemu  
      Wybierz HA
   9. Nazwa użytkownika administratora i hasło administratora  
      Zostanie utworzony nowy użytkownik, którego można użyć do zalogowania się na komputerze.
   10. Identyfikator podsieci  
   Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Identyfikator zazwyczaj wygląda tak, jak **&lt; Identyfikator &gt; subskrypcji**/subscriptions//ResourceGroups/nazwa **&lt; grupy &gt; zasobów**/Providers/Microsoft.Network/virtualNetworks/nazwa **&lt; sieci &gt; wirtualnej**/Subnets/**&lt; nazwa &gt; podsieci**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pośrednictwem Azure Portal

Najpierw należy utworzyć maszyny wirtualne dla tego klastra systemu plików NFS. Następnie należy utworzyć moduł równoważenia obciążenia i użyć maszyn wirtualnych w puli zaplecza.

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Tworzenie zestawu dostępności  
   Ustaw maksymalną domenę aktualizacji
1. Utwórz maszynę wirtualną 1  
   Użyj co najmniej SLES4SAP 12 SP1, w tym przykładzie obraz SLES4SAP 12 SP1 https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   Używane są SLES dla aplikacji SAP 12 SP1  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Tworzenie maszyny wirtualnej 2  
   Użyj co najmniej SLES4SAP 12 SP1, w tym przykładzie obraz SLES4SAP 12 SP1 https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   Używane są SLES dla aplikacji SAP 12 SP1  
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
         * Powtórz powyższe kroki, aby utworzyć adres IP dla wykres WYWOŁUJĄCYCH (na przykład **10.0.0.8** i **NW1-AERS-zaplecza**)
   1. Tworzenie puli zaplecza
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pule zaplecza, a następnie kliknij przycisk Dodaj.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **NW1-zaplecza**)
      1. Kliknij pozycję Dodaj maszynę wirtualną.
      1. Wybierz maszynę wirtualną
      1. Wybierz Maszyny wirtualne klastra SCS i ich adresy IP.
      1. Kliknij pozycję Dodaj.
   1. Tworzenie sond kondycji
      1. Port 620 **00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **NW1-ASCS-HP**)
         1. Wybierz pozycję TCP jako protokół, port 620 **00**, Zachowaj interwał 5 i próg złej kondycji 2
         1. Kliknij przycisk OK.
      1. Port 621 **02** dla ASCS wykres wywołujących
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla wykres WYWOŁUJĄCYCH (na przykład 621 **02** i **NW1-AERS-HP**)
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
      1. Port 620 **00** dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji, a następnie kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej sondy kondycji (na przykład **NW1-ASCS-HP**)
         1. Wybierz pozycję TCP jako protokół, port 620 **00**, Zachowaj interwał 5 i próg złej kondycji 2
         1. Kliknij przycisk OK.
      1. Port 621 **02** dla ASCS wykres wywołujących
         * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla wykres WYWOŁUJĄCYCH (na przykład 621 **02** i **NW1-AERS-HP**)
   1. Reguły równoważenia obciążenia
      1. 32 **00** TCP dla ASCS
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **NW1-lb-3200**)
         1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **NW1-ASCS-fronton**)
         1. Utrzymywanie protokołu **TCP**, wprowadź port **3200**
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Upewnij się, że włączono zmiennoprzecinkowy adres IP**
         1. Kliknij przycisk OK.
      1. Dodatkowe porty dla ASCS
         * Powtórz powyższe kroki dla portów 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 i TCP dla ASCS
      1. Dodatkowe porty dla ASCS wykres WYWOŁUJĄCYCH
         * Powtórz powyższe kroki dla portów 33 **02**, 5 **02** 13, 5 **02** 14, 5 **02** 16 i TCP dla ASCS wykres wywołujących

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.  

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

   ```
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   ```

1. **[A]** zaktualizuj agentów zasobów SAP  
   
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

1. **[A]** rozpoznawanie nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Przygotowanie do instalacji oprogramowania SAP NetWeaver

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

1. **[A]** Skonfiguruj AutoFS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Utwórz plik z

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Uruchom ponownie AutoFS, aby zainstalować nowe udziały

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
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

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** Instalowanie oprogramowania SAP NetWeaver ASCS  

   Zainstaluj oprogramowanie SAP NetWeaver ASCS jako element główny w pierwszym węźle przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla ASCS, na przykład <b>NW1-ASCS</b>, <b>10.0.0.7</b> i numer wystąpienia, który został użyty do sondowania modułu równoważenia obciążenia, na przykład <b>00</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w/usr/SAP/**NW1**/ASCS **00**, spróbuj ustawić właściciela i grupę folderu ASCS **00** , a następnie ponów próbę.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Utwórz zasób wirtualnego adresu IP i sondę kondycji dla wystąpienia wykres wywołujących

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** Instalowanie oprogramowania SAP NetWeaver wykres wywołujących

   Zainstaluj program SAP NetWeaver wykres WYWOŁUJĄCYCH jako element główny w drugim węźle przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla wykres WYWOŁUJĄCYCH, na przykład <b>NW1-AERS</b>, <b>10.0.0.8</b> i numer wystąpienia, który został użyty do sondowania modułu równoważenia obciążenia, na przykład <b>02</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Użyj SWPM SP 20 PL 05 lub wyższej. Niższa wersja nie ustawia prawidłowo uprawnień i instalacja zakończy się niepowodzeniem.

   Jeśli instalacja nie powiedzie się w celu utworzenia podfolderu w/usr/SAP/**NW1**/ERS **02**, spróbuj ustawić właściciela i grupę folderu wykres wywołujących **02** i ponów próbę.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** dostosowuje profile wystąpienia ASCS/SCS i wykres wywołujących
 
   * Profil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
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

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** Skonfiguruj utrzymywanie aktywności

   Komunikacja między serwerem aplikacji SAP NetWeaver a ASCS/SCS jest kierowana przez programowy moduł równoważenia obciążenia. Moduł równoważenia obciążenia rozłącza nieaktywne połączenia po upływie konfigurowalnego limitu czasu. Aby tego uniknąć, należy ustawić parametr w profilu SAP NetWeaver ASCS/SCS, jeśli jest używany ENSA1, i zmienić ustawienia systemu Linux `keepalive` na wszystkich serwerach SAP dla obu ENSA1/ENSA2. Aby uzyskać więcej informacji, przeczytaj temat [SAP Note 1410736][1410736] .

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]** Skonfiguruj użytkowników SAP po instalacji

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** Dodaj usługi ASCS i wykres wywołujących SAP do pliku sapservice

   Dodaj wpis usługi ASCS do drugiego węzła i skopiuj wpis usługi wykres WYWOŁUJĄCYCH do pierwszego węzła.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** tworzenie zasobów klastra SAP

W przypadku korzystania z architektury Server 1 Architecture (ENSA1) Zdefiniuj zasoby w następujący sposób:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  System SAP wprowadził obsługę dla kolejki serwera 2, w tym replikację, w przypadku oprogramowania SAP NW 7,52. Począwszy od programu ABAP platform 1809, w kolejce serwer 2 jest instalowany domyślnie. Zobacz temat SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) dla obsługi kolejki serwera 2.
Jeśli używana jest architektura Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), zdefiniuj zasoby w następujący sposób:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  W przypadku uaktualniania ze starszej wersji i przełączania do kolejki serwera 2, zobacz uwagi dotyczące oprogramowania SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Upewnij się, że klaster ma stan OK i że wszystkie zasoby zostały uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Przygotowywanie serwera aplikacji SAP NetWeaver

Niektóre bazy danych wymagają wykonania instalacji wystąpienia bazy danych na serwerze aplikacji. Przygotuj maszyny wirtualne serwera aplikacji, aby mogły z nich korzystać w tych przypadkach.

W poniższych krokach założono, że serwer aplikacji jest instalowany na serwerze innym niż serwery ASCS/SCS i HANA. W przeciwnym razie niektóre kroki (takie jak Konfigurowanie rozpoznawania nazw hostów) nie są wymagane.

1. Konfigurowanie systemu operacyjnego

   Zmniejsz rozmiar zanieczyszczonej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [niska wydajność zapisu na serwerach z systemem SLES 11/12 i dużą ilością pamięci RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Konfiguracja rozpoznawania nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

   ```bash
   sudo vi /etc/hosts
   ```

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Tworzenie katalogu sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Konfigurowanie AutoFS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Utwórz nowy plik z

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Uruchom ponownie AutoFS, aby zainstalować nowe udziały

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Konfiguruj plik wymiany

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

## <a name="install-database"></a>Instalowanie bazy danych

W tym przykładzie system SAP NetWeaver jest instalowany na SAP HANA. Dla tej instalacji można użyć każdej obsługiwanej bazy danych. Aby uzyskać więcej informacji na temat instalowania SAP HANA na platformie Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines][sap-hana-ha]. Listę obsługiwanych baz danych można znaleźć w temacie [SAP Note 1928533][1928533].

1. Uruchamianie instalacji wystąpienia bazy danych SAP

   Zainstaluj wystąpienie bazy danych SAP NetWeaver jako element główny przy użyciu wirtualnej nazwy hosta, która jest mapowana na adres IP konfiguracji frontonu modułu równoważenia obciążenia dla bazy danych, na przykład <b>NW1-DB</b> i <b>10.0.0.13</b>.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalacja serwera aplikacji SAP NetWeaver

Wykonaj następujące kroki, aby zainstalować serwer aplikacji SAP.

1. Przygotuj serwer aplikacji

   Aby przygotować serwer aplikacji, wykonaj kroki opisane w rozdziale " [Przygotowywanie serwera aplikacji SAP NetWeaver](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) " powyżej.

1. Zainstaluj serwer aplikacji SAP NetWeaver

   Zainstaluj podstawowy lub dodatkowy serwer aplikacji SAP NetWeaver.

   SAPINST_REMOTE_ACCESS_USER parametru sapinst można użyć, aby zezwolić użytkownikowi niebędącemu głównym na łączenie się z sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizacja SAP HANA bezpiecznego magazynu

   Zaktualizuj SAP HANA bezpiecznego magazynu, aby wskazywał nazwę wirtualną konfiguracji replikacji systemu SAP HANA.

   Uruchom następujące polecenie, aby wyświetlić listę wpisów
   <pre><code>hdbuserstore List
   </code></pre>

   Powinno to być lista wszystkich wpisów i powinna wyglądać podobnie do
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   Dane wyjściowe wskazują, że adres IP wpisu domyślnego wskazuje maszynę wirtualną, a nie adres IP modułu równoważenia obciążenia. Ten wpis należy zmienić, aby wskazywał wirtualną nazwę hosta usługi równoważenia obciążenia. Upewnij się, że używasz tego samego portu (**30313** w danych wyjściowych powyżej) i nazwy bazy danych (**HN1** w danych wyjściowych powyżej)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

Poniższe testy to kopia przypadków testowych w przewodnikach z najlepszymi rozwiązaniami dla systemu SUSE. Są one kopiowane dla wygody użytkownika. Zawsze należy przeczytać przewodniki dotyczące najlepszych rozwiązań i wykonać wszystkie dodatkowe testy, które mogły zostać dodane.

1. Test HAGetFailoverConfig, HACheckConfig i HACheckFailoverConfig

   Uruchom następujące polecenia jako \<sapsid> adm w węźle, w którym jest aktualnie uruchomione wystąpienie ASCS. Jeśli polecenie nie powiedzie się z powodu niewystarczającej ilości pamięci, może to być spowodowane przez myślniki w nazwie hosta. Jest to znany problem, który zostanie rozwiązany przez SUSE w pakiecie SAP-SUSE-Cluster-Connector.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. Ręczne Migrowanie wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenia jako główne, aby zmigrować wystąpienie ASCS.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Test HAFailoverToNode

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenia jako \<sapsid> adm, aby zmigrować wystąpienie ASCS.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Symulowanie awarii węzła

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenie jako element główny w węźle, w którym jest uruchomione wystąpienie ASCS

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Jeśli używasz SBD, Pacemaker nie powinien automatycznie rozpoczynać się w zamkniętym węźle. Stan po ponownym uruchomieniu węzła powinien wyglądać następująco.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Użyj następujących poleceń, aby uruchomić Pacemaker w zamkniętym węźle, wyczyść komunikaty SBD i wyczyść zasoby zakończone niepowodzeniem.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Testowanie ręcznego ponownego uruchomienia wystąpienia ASCS

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Utwórz blokadę do kolejki, na przykład Edytuj użytkownika w usłudze Transaction su01. Uruchom następujące polecenia jako \<sapsid> adm w węźle, w którym działa wystąpienie ASCS. Polecenia zatrzymają wystąpienie ASCS i uruchomią je ponownie. W przypadku korzystania z architektury Server 1 w kolejce w tym teście oczekuje się, że blokada kolejki zostanie utracona. W przypadku korzystania z architektury serwera 2 w kolejce zostanie zachowany. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Wystąpienie ASCS powinno być teraz wyłączone w Pacemaker

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Uruchom ponownie wystąpienie ASCS w tym samym węźle.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Blokada kolejki transakcji su01 powinna zostać utracona, a zaplecze powinny zostać zresetowane. Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Przerwij proces serwera komunikatów

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenia jako główne, aby zidentyfikować proces serwera komunikatów i Kasuj go.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Jeśli serwer wiadomości zostanie skasowany tylko raz, zostanie on uruchomiony ponownie przez sapstart. Jeśli zakończysz go często, Pacemaker będzie ostatecznie przenieść wystąpienie ASCS do innego węzła. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia ASCS i wykres WYWOŁUJĄCYCH po teście.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Kasuj proces serwera z kolejki

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Uruchom następujące polecenia jako element główny w węźle, w którym uruchomiono wystąpienie ASCS, aby skasować serwer z kolejki.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Wystąpienie ASCS powinno natychmiast przejść w tryb failover do innego węzła. Wystąpienie wykres WYWOŁUJĄCYCH powinno również przechodzić w tryb failover po rozpoczęciu wystąpienia ASCS. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia ASCS i wykres WYWOŁUJĄCYCH po teście.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Kasuj proces serwera replikacji z kolejki

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenie jako element główny w węźle, w którym uruchomiono wystąpienie wykres WYWOŁUJĄCYCH, aby skasować proces serwera replikacji z kolejki.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Jeśli uruchamiasz polecenie tylko raz, sapstart uruchomi proces ponownie. Jeśli uruchomisz go często, sapstart nie uruchomi ponownie procesu, a zasób stanie się zatrzymany. Uruchom następujące polecenia jako główne, aby wyczyścić stan zasobu wystąpienia wykres WYWOŁUJĄCYCH po teście.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Kasuj proces sapstartsrvnia w kolejce

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Uruchom następujące polecenia jako element główny w węźle, w którym działa ASCS.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   Proces sapstartsrv powinien być zawsze ponownie uruchamiany przez agenta zasobów Pacemaker. Stan zasobu po teście:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [HA for SAP NW na maszynach wirtualnych platformy Azure w systemie SLES for SAP — Przewodnik dotyczący wiele identyfikatorów SID](./high-availability-guide-suse-multi-sid.md)
* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
