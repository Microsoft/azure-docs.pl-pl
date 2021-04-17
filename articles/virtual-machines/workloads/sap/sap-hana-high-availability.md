---
title: Wysoka dostępność usługi SAP HANA maszyn wirtualnych platformy Azure w systemie SLES | Microsoft Docs
description: Wysoka dostępność zasobów SAP HANA maszyn wirtualnych platformy Azure na platformie SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: ea1296fd4e31c2deaed79e980ab764c523a2bfd7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364366"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Wysoka dostępność zasobów SAP HANA maszyn wirtualnych platformy Azure na platformie SUSE Linux Enterprise Server

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[401162]:https://launchpad.support.sap.com/#/notes/401162

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json

W przypadku tworzenia aplikacji lokalnych można użyć replikacji systemu HANA lub magazynu udostępnionego w celu ustanowienia wysokiej dostępności dla SAP HANA.
Na maszynach wirtualnych platformy Azure replikacja systemu HANA na platformie Azure jest obecnie jedyną obsługiwaną funkcją wysokiej dostępności. SAP HANA Replikacja składa się z jednego węzła podstawowego i co najmniej jednego węzła pomocniczego. Zmiany danych w węźle podstawowym są replikowane do węzła pomocniczego synchronicznie lub asynchronicznie.

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych, instalowania struktury klastra oraz instalowania i SAP HANA replikacji systemu.
W przykładowych konfiguracjach są używane polecenia instalacji, numer wystąpienia **03** i identyfikator systemu HANA **HN1.**

Najpierw zapoznaj się z następującymi uwagami i dokumentami SAP:

* Uwaga [SAP 1928533,]która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure obsługiwanych w przypadku wdrażania oprogramowania SAP.
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane kombinacje oprogramowania SAP, systemu operacyjnego i bazy danych.
  * Wymagana wersja jądra SAP dla systemów Windows i Linux na Microsoft Azure.
* Uwaga [2015553] oprogramowania SAP zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* Uwaga [SAP 2205917] ma zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server dla aplikacji SAP.
* Uwaga SAP [1944799] ma SAP HANA wytyczne dotyczące SUSE Linux Enterprise Server dla aplikacji SAP.
* Uwaga [sap 2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania zgłaszanych dla oprogramowania SAP na platformie Azure.
* Uwaga SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga sap [2243692 zawiera] informacje o licencjonowaniu sap w systemie Linux na platformie Azure.
* Uwaga [sap 1984787] zawiera ogólne informacje o SUSE Linux Enterprise Server 12.
* Uwaga [sap 1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzonym rozszerzeniem monitorowania platformy Azure dla oprogramowania SAP.
* Uwaga [sap 401162] zawiera informacje na temat sposobu unikania używania "adresu już w użyciu" podczas konfigurowania replikacji systemu HANA.
* [Strona WIKI sap Community zawiera](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) wszystkie wymagane uwagi dotyczące oprogramowania SAP dla systemu Linux.
* [SAP HANA certyfikowanych platform IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Przewodnik planowania Virtual Machines wdrażania oprogramowania SAP][planning-guide] w systemie Linux na platformie Azure.
* [Wdrożenie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][deployment-guide] (w tym artykule).
* [Przewodnik wdrażania Virtual Machines DBMS dla oprogramowania SAP w systemie Linux na platformie Azure.][dbms-guide]
* [SUSE Linux Enterprise Server najlepszych rozwiązań dla aplikacji SAP Applications 12 SP3][sles-for-sap-bp]
  * Konfigurowanie infrastruktury SAP HANA SR Optimized Infrastructure (SLES for SAP Applications 12 SP1). Przewodnik zawiera wszystkie informacje wymagane do skonfigurowania SAP HANA replikacji systemu na potrzeby tworzenia aplikacji lokalnych. Użyj tego przewodnika jako linii bazowej.
  * Konfigurowanie infrastruktury SAP HANA SR Cost Optimized Infrastructure (SLES for SAP Applications 12 SP1)

## <a name="overview"></a>Omówienie

Aby uzyskać wysoką dostępność, SAP HANA na dwóch maszynach wirtualnych. Dane są replikowane przy użyciu replikacji systemu HANA.

![SAP HANA o wysokiej dostępności](./media/sap-hana-high-availability/ha-suse-hana.png)

SAP HANA konfiguracji replikacji systemu korzysta z dedykowanej wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure do korzystania z wirtualnego adresu IP wymagany jest usługa równoważenia obciążenia. Na poniższej liście przedstawiono konfigurację usługi równoważenia obciążenia:

* Konfiguracja frontonu: adres IP 10.0.0.13 dla hn1-db
* Konfiguracja serwera końcowego: połączone z podstawowymi interfejsami sieciowym wszystkich maszyn wirtualnych, które powinny być częścią replikacji systemu HANA
* Port sondy: port 62503
* Reguły równoważenia obciążenia: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Wdrażanie dla systemu Linux

Agent zasobów dla SAP HANA znajduje się w SUSE Linux Enterprise Server dla aplikacji SAP.
Plik Azure Marketplace obraz dla aplikacji SUSE Linux Enterprise Server SAP Applications 12, który umożliwia wdrażanie nowych maszyn wirtualnych.

### <a name="deploy-with-a-template"></a>Wdrażanie za pomocą szablonu

Aby wdrożyć wszystkie wymagane zasoby, możesz użyć jednego z szablonów szybkiego startu dostępnych w usłudze GitHub. Szablon wdraża maszyny wirtualne, usługę równoważenia obciążenia, zestaw dostępności i tak dalej.
Aby wdrożyć szablon, wykonaj następujące kroki:

1. Otwórz szablon [bazy danych][template-multisid-db] lub [szablon zbieżny][template-converged] w Azure Portal. 
    Szablon bazy danych tworzy reguły równoważenia obciążenia tylko dla bazy danych. Szablon zbieżny tworzy również reguły równoważenia obciążenia dla wystąpienia usług ASCS/SCS i ERS (tylko system Linux). Jeśli planujesz zainstalowanie systemu opartego na programie SAP NetWeaver i chcesz zainstalować wystąpienie usługi ASCS/SCS na tych samych maszynach, użyj [zbieżnego szablonu][template-converged].

1. Wprowadź następujące parametry:
    - **Identyfikator systemu SAP:** wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla wdrażanych zasobów.
    - **Typ stosu:**(ten parametr ma zastosowanie tylko w przypadku używania szablonu zbieżnego). Wybierz typ stosu SAP NetWeaver.
    - **Typ systemu operacyjnego:** wybierz jedną z dystrybucji systemu Linux. W tym przykładzie wybierz **pozycję SLES 12.**
    - **Typ bazy danych:** wybierz **pozycję HANA.**
    - **Rozmiar systemu Sap:** wprowadź liczbę systemów SAPS, które ma udostępnić nowy system. Jeśli nie masz pewności, ilu systemów SAP wymaga system, zapytaj partnera technologii SAP lub integratora systemu.
    - **Dostępność systemu:** wybierz pozycję **HA**.
    - **Nazwa użytkownika i hasło administratora:** tworzony jest nowy użytkownik, który może służyć do logowania się na maszynie.
    - **Nowa lub istniejąca podsieć:** określa, czy należy utworzyć nową sieć wirtualną i podsieć, czy użyć istniejącej podsieci. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz pozycję **Istniejąca.**
    - **Identyfikator podsieci:** jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, do której powinna zostać przypisana maszyna wirtualna, nadaj identyfikator tej konkretnej podsieci. Identyfikator zazwyczaj wygląda **następująco: /subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**.

### <a name="manual-deployment"></a>Wdrożenie ręczne

> [!IMPORTANT]
> Upewnij się, że wybierany system operacyjny ma certyfikat SAP SAP HANA dla określonych typów maszyn wirtualnych, których używasz. Listę certyfikowanych SAP HANA maszyn wirtualnych i wydań systemu operacyjnego dla tych typów można znaleźć w te SAP HANA [certyfikowanych platform IaaS.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Pamiętaj, aby kliknąć szczegóły wymienionego typu maszyny wirtualnej, aby uzyskać pełną listę wersji SAP HANA obsługiwanych wersji systemu operacyjnego dla określonego typu maszyny wirtualnej
>  

1. Utwórz grupę zasobów.
1. Utwórz sieć wirtualną.
1. Utwórz zestaw dostępności.
   - Ustaw maksymalną domenę aktualizacji.
1. Tworzenie wewnętrznego (wewnętrznego) równoważenia obciążenia. Zalecamy użycie [standardowego równoważenia obciążenia.](../../../load-balancer/load-balancer-overview.md)
   - Wybierz sieć wirtualną utworzoną w kroku 2.
1. Utwórz maszynę wirtualną 1.
   - Użyj obrazu SLES4SAP w galerii platformy Azure, który jest obsługiwany SAP HANA dla wybranego typu maszyny wirtualnej.
   - Wybierz zestaw dostępności utworzony w kroku 3.
1. Utwórz maszynę wirtualną 2.
   - Użyj obrazu SLES4SAP w galerii platformy Azure, który jest obsługiwany SAP HANA dla wybranego typu maszyny wirtualnej.
   - Wybierz zestaw dostępności utworzony w kroku 3. 
1. Dodawanie dysków danych.

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, [zobacz Azure Load balancer Limitations (Ograniczenia usługi Azure Load Balancer).](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wd wdrażaj drugą kartę sieciową.   

> [!Note]
> Jeśli maszyny wirtualne bez publicznych adresów IP zostaną umieszczone w puli zaplecza wewnętrznego (bez publicznego adresu IP) usługi równoważenia obciążenia platformy Azure w standardowych wersjach, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja zezwalania na routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz Public [endpoint connectivity for Virtual Machines using Azure usługa Load Balancer w warstwie Standardowa in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md)(Publiczna łączność punktu końcowego dla klientów korzystających z usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności oprogramowania SAP).  

1. W przypadku korzystania ze standardowego równoważenia obciążenia wykonaj następujące kroki konfiguracji:
   1. Najpierw utwórz pulę adresów IP frontonia:
   
      1. Otwórz równoważenie obciążenia, wybierz **pulę adresów IP frontonia** i wybierz pozycję **Dodaj.**
      1. Wprowadź nazwę nowej puli adresów IP frontendu (na przykład **hana-frontend).**
      1. Ustaw **przypisanie na** **statyczny** i wprowadź adres IP (na przykład **10.0.0.13).**
      1. Wybierz przycisk **OK**.
      1. Po utworzeniu nowej puli adresów IP frontonia zanotuj adres IP puli.
   
   1. Następnie utwórz pulę back-end:
   
      1. Otwórz równoważenie obciążenia, wybierz **pozycję Pule zaplecza** i wybierz pozycję **Dodaj.**
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **hana-backend).**
      1. Wybierz **Virtual Network**.
      1. Wybierz **pozycję Dodaj maszynę wirtualną.**
      1. Wybierz pozycję ** Maszyna wirtualna**.
      1. Wybierz maszyny wirtualne klastra SAP HANA i ich adresy IP.
      1. Wybierz pozycję **Dodaj**.
   
   1. Następnie utwórz sondę kondycji:
   
      1. Otwórz usługę równoważenia obciążenia, wybierz **pozycję Sondy kondycji** i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej sondy kondycji (na przykład **hana-hp).**
      1. Wybierz **tcp** jako protokół i port 625 **03.** Zachowaj wartość **Interwał** ustawioną na 5, a **wartość** progowa W złej kondycji na 2.
      1. Wybierz przycisk **OK**.
   
   1. Następnie utwórz reguły równoważenia obciążenia:
   
      1. Otwórz równoważenie obciążenia, wybierz **pozycję Reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
      1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **hana-lb).**
      1. Wybierz utworzony wcześniej adres IP frontonia, pulę zaplecza i sondę kondycji (na przykład **hana-frontend,** **hana-backend** i **hana-hp).**
      1. Wybierz pozycję **Porty HA.**
      1. Upewnij się, że **włączyć zmienny adres IP**.
      1. Wybierz przycisk **OK**.

1. Alternatywnie, jeśli twój scenariusz decyduje o użyciu podstawowego równoważenia obciążenia, wykonaj następujące kroki konfiguracji:
   1. Najpierw utwórz pulę adresów IP frontonia:
   
      1. Otwórz równoważenie obciążenia, wybierz **pulę adresów IP frontonia** i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli adresów IP frontendu (na przykład **hana-frontend).**
      1. Ustaw **przypisanie na** **statyczny** i wprowadź adres IP (na przykład **10.0.0.13).**
      1. Wybierz przycisk **OK**.
      1. Po utworzeniu nowej puli adresów IP frontonia zanotuj adres IP puli.
   
   1. Następnie utwórz pulę back-end:
   
      1. Otwórz równoważenie obciążenia, wybierz **pozycję Pule zaplecza** i wybierz pozycję **Dodaj.**
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **hana-backend).**
      1. Wybierz **pozycję Dodaj maszynę wirtualną.**
      1. Wybierz zestaw dostępności utworzony w kroku 3.
      1. Wybierz maszyny wirtualne klastra SAP HANA klastra.
      1. Wybierz przycisk **OK**.
   
   1. Następnie utwórz sondę kondycji:
   
      1. Otwórz usługę równoważenia obciążenia, wybierz **pozycję Sondy kondycji** i wybierz pozycję **Dodaj.**
      1. Wprowadź nazwę nowej sondy kondycji (na przykład **hana-hp).**
      1. Wybierz **TCP** jako protokół i port 625 **03.** Zachowaj wartość **Interwał** ustawioną na 5, a **wartość** progowa W złej kondycji na 2.
      1. Wybierz przycisk **OK**.
   
   1. W SAP HANA 1.0 utwórz reguły równoważenia obciążenia:
   
      1. Otwórz równoważenie obciążenia, wybierz **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
      1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład hana-lb-3 **03** 15).
      1. Wybierz utworzony wcześniej adres IP frontonia, pulę zaplecza i sondę kondycji (na przykład **hana-frontend).**
      1. Zachowaj wartość **protokołu** **TCP** i wprowadź port 3 **03** 15.
      1. Zwiększ limit **czasu bezczynności do** 30 minut.
      1. Upewnij się, że **włączyć zmienny adres IP**.
      1. Wybierz przycisk **OK**.
      1. Powtórz te kroki dla portu 3 **03** 17.
   
   1. W SAP HANA 2.0 utwórz reguły równoważenia obciążenia dla systemowej bazy danych:
   
      1. Otwórz równoważenie obciążenia, wybierz **pozycję Reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
      1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład hana-lb-3 **03** 13).
      1. Wybierz utworzony wcześniej adres IP frontonia, pulę zaplecza i sondę kondycji (na przykład **hana-frontend).**
      1. Zachowaj protokół **ustawiony** na **TCP** i wprowadź port 3 **03** 13.
      1. Zwiększ limit **czasu bezczynności do** 30 minut.
      1. Upewnij się, że **włączyć zmienny adres IP**.
      1. Wybierz przycisk **OK**.
      1. Powtórz te kroki dla portu 3 **03** 14.
   
   1. W SAP HANA 2.0 najpierw utwórz reguły równoważenia obciążenia dla bazy danych dzierżawy:
   
      1. Otwórz równoważenie obciążenia, wybierz **pozycję Reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
      1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład hana-lb-3 **03** 40).
      1. Wybierz utworzony wcześniej adres IP frontonia, pulę zaplecza i sondę kondycji (na przykład **hana-frontend).**
      1. Zachowaj protokół **ustawiony** na **TCP** i wprowadź port 3 **03** 40.
      1. Zwiększ limit **czasu bezczynności do** 30 minut.
      1. Upewnij się, że **włączyć zmienny adres IP**.
      1. Wybierz przycisk **OK**.
      1. Powtórz te kroki dla portów 3 **03** 41 i 3 **03** 42.

   Aby uzyskać więcej informacji na temat wymaganych [](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) portów dla usługi SAP HANA, przeczytaj rozdział Połączenia z bazami danych dzierżaw w przewodniku bazy danych dzierżawy usługi [SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) lub uwaga [sap 2388694.][2388694]

> [!IMPORTANT]
> Nie włączaj znaczników czasu TCP na maszyn wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie znaczników czasu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.ipv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe [informacje, Load Balancer sond kondycji](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Zobacz też uwaga SAP [2382421.](https://launchpad.support.sap.com/#/notes/2382421) 

## <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w [tesłudze Setting up Pacemaker on SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) (Konfigurowanie programu Pacemaker na platformie Azure), aby utworzyć podstawowy klaster Pacemaker dla tego serwera HANA. Możesz użyć tego samego klastra Pacemaker dla SAP HANA i SAP NetWeaver (A)SCS.

## <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

W krokach w tej sekcji są następujące prefiksy:
- **[A]**: Krok ma zastosowanie do wszystkich węzłów.
- **[1]**: Krok dotyczy tylko węzła 1.
- **[2]**: Ten krok dotyczy tylko węzła 2 klastra Pacemaker.

1. **[A]** Skonfiguruj układ dysku: **Menedżer woluminów logicznych (LVM).**

   Zalecamy używanie lvm woluminów, które przechowują dane i pliki dziennika. W poniższym przykładzie przyjęto założenie, że do maszyn wirtualnych są dołączone cztery dyski danych, które są używane do tworzenia dwóch woluminów.

   Lista wszystkich dostępnych dysków:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Przykładowe dane wyjściowe:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Utwórz woluminy fizyczne dla wszystkich dysków, których chcesz użyć:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Utwórz grupę woluminów dla plików danych. Użyj jednej grupy woluminów dla plików dziennika i jednej dla udostępnionego katalogu SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Utwórz woluminy logiczne. Wolumin liniowy jest tworzony, gdy używasz bez `lvcreate` `-i` przełącznika. Zalecamy utworzenie woluminu rozłożonego w celu lepszej wydajności we/wy i dopasowanie rozmiarów rozłożonych do wartości udokumentowanych SAP HANA [konfiguracji magazynu maszyny wirtualnej.](./hana-vm-operations-storage.md) Argumentem powinna być liczba podstawowych woluminów fizycznych, a `-i` `-I` argumentem jest rozmiar paska. W tym dokumencie dwa woluminy fizyczne są używane dla woluminu danych, więc `-i` argument przełącznika jest ustawiony na **2**. Rozmiar rozłożony woluminu danych to **256KiB.** Jeden wolumin fizyczny jest używany dla woluminu dziennika, więc nie lub przełączniki `-i` `-I` są jawnie używane dla poleceń woluminu dziennika.  

   > [!IMPORTANT]
   > Użyj przełącznika i ustaw go na liczbę bazowego woluminu fizycznego w przypadku używania więcej niż jednego woluminu fizycznego dla każdego woluminu danych, dziennika `-i` lub woluminów udostępnionych. Użyj `-I` przełącznika , aby określić rozmiar paska podczas tworzenia woluminu rozłożonego.  
   > Zobacz [SAP HANA magazynu maszyny wirtualnej,](./hana-vm-operations-storage.md) aby uzyskać zalecane konfiguracje magazynu, w tym rozmiary rozłożone i liczbę dysków.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   Utwórz katalogi instalacji i skopiuj UUID wszystkich woluminów logicznych:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Utwórz `fstab` wpisy dla trzech woluminów logicznych:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Wstaw następujący wiersz w `/etc/fstab` pliku:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Zainstaluj nowe woluminy:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Skonfiguruj układ dysku: **Dyski zwykłego systemu**.

   W przypadku systemów demonstracyjnych pliki danych i dzienników platformy HANA można umieścić na jednym dysku. Utwórz partycję na stronie /dev/disk/azure/scsi1/lun0 i sformatuj ją za pomocą systemu plików xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Wstaw ten wiersz w pliku /etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Utwórz katalog docelowy i zainstaluj dysk:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Skonfiguruj rozpoznawania nazw hostów dla wszystkich hostów.

   Możesz użyć serwera DNS lub zmodyfikować plik /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwę hosta następującymi poleceniami:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze w pliku /etc/hosts. Zmień adres IP i nazwę hosta, aby dopasować je do środowiska:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Zainstaluj SAP HANA o wysokiej dostępności:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Aby zainstalować SAP HANA replikacji systemu, postępuj zgodnie z rozdziałem 4 przewodnika po scenariuszu optymalizacji wydajności SAP HANA [SR.](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/)

1. **[A]** Uruchom **program hdblcm** z dysku DVD HANA. Wprowadź następujące wartości w wierszu polecenia:
   * Wybierz instalację: wprowadź **1**.
   * Wybierz dodatkowe składniki do instalacji: wprowadź **1**.
   * Wprowadź ścieżkę instalacji [/hana/shared]: wybierz klawisz Enter.
   * Wprowadź nazwę hosta lokalnego [..]: wybierz klawisz Enter.
   * Czy chcesz dodać dodatkowe hosty do systemu? (y/n) [n]: wybierz klawisz Enter.
   * Wprowadź SAP HANA systemowego: wprowadź identyfikator SID platformy HANA, na przykład: **HN1**.
   * Wprowadź numer wystąpienia [00]: wprowadź numer wystąpienia HANA. Wprowadź **wartość 03,** jeśli używasz szablonu platformy Azure lub w sekcji ręcznego wdrażania tego artykułu.
   * Wybierz pozycję Tryb bazy danych/ Wprowadź indeks [1]: wybierz klawisz Enter.
   * Wybierz pozycję Użycie systemu / Wprowadź indeks [4]: wybierz wartość użycia systemu.
   * Wprowadź lokalizację woluminów danych [/hana/data/HN1]: wybierz klawisz Enter.
   * Wprowadź lokalizację woluminów dziennika [/hana/log/HN1]: wybierz klawisz Enter.
   * Ograniczać maksymalną alokację pamięci? [n]: wybierz klawisz Enter.
   * Wprowadź nazwę hosta certyfikatu dla hosta "..." [...]: Wybierz klawisz Enter.
   * Wprowadź hasło użytkownika programu SAP Host Agent (sapadm): wprowadź hasło użytkownika agenta hosta.
   * Potwierdź hasło użytkownika programu SAP Host Agent (sapadm): wprowadź ponownie hasło użytkownika agenta hosta, aby potwierdzić.
   * Wprowadź hasło administratora systemu (hdbadm): wprowadź hasło administratora systemu.
   * Potwierdź hasło administratora systemu (hdbadm): wprowadź ponownie hasło administratora systemu, aby potwierdzić.
   * Wprowadź wartość Katalog macierzysty administratora systemu [/usr/sap/HN1/home]: wybierz klawisz Enter.
   * Wprowadź nazwę Powłoki logowania administratora systemu [/bin/sh]: wybierz klawisz Enter.
   * Wprowadź identyfikator użytkownika administratora systemu [1001]: wybierz klawisz Enter.
   * Wprowadź identyfikator grupy użytkowników (sapsys) [79]: wybierz klawisz Enter.
   * Wprowadź hasło użytkownika bazy danych (SYSTEM): wprowadź hasło użytkownika bazy danych.
   * Potwierdź hasło użytkownika bazy danych (SYSTEM): wprowadź ponownie hasło użytkownika bazy danych, aby potwierdzić.
   * Uruchom ponownie system po ponownym uruchomieniu komputera? [n]: wybierz klawisz Enter.
   * Czy chcesz kontynuować? (y/n): zweryfikuj podsumowanie. Wprowadź **y,** aby kontynuować.

1. **[A]** Uaktualnij agenta hosta SAP.

   Pobierz najnowsze archiwum agenta hosta SAP z centrum [oprogramowania SAP i][sap-swcenter] uruchom następujące polecenie, aby uaktualnić agenta. Zastąp ścieżkę do archiwum, aby wskazać pobrany plik:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurowanie SAP HANA replikacji systemu w programie 2.0

W krokach w tej sekcji są następujące prefiksy:

* **[A]**: Krok ma zastosowanie do wszystkich węzłów.
* **[1]**: Krok dotyczy tylko węzła 1.
* **[2]**: Ten krok dotyczy tylko węzła 2 klastra Pacemaker.

1. **[1] Utwórz** bazę danych dzierżawy.

   Jeśli używasz programu SAP HANA 2.0 lub MDC, utwórz bazę danych dzierżawy dla systemu SAP NetWeaver. Zastąp **NW1** identyfikatorem SID systemu SAP.

   Wykonaj następujące polecenie jako <hanasid \> adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1] Skonfiguruj** replikację systemu w pierwszym węźle:

   Kopii zapasowej baz danych w <hanasid \> adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Skopiuj systemowe pliki PKI do lokacji dodatkowej:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Utwórz lokację główną:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2] Skonfiguruj** replikację systemu w drugim węźle:
    
   Zarejestruj drugi węzeł, aby rozpocząć replikację systemu. Uruchom następujące polecenie jako <hanasid \> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurowanie SAP HANA replikacji systemu w programie 1.0

W krokach w tej sekcji są następujące prefiksy:

* **[A]**: Krok ma zastosowanie do wszystkich węzłów.
* **[1]**: Krok dotyczy tylko węzła 1.
* **[2]**: Ten krok dotyczy tylko węzła 2 klastra Pacemaker.

1. **[1]** Utwórz wymaganych użytkowników.

   Uruchom następujące polecenie jako katalog główny. Pamiętaj, aby zastąpić ciągi pogrubione (identyfikator systemu HANA **HN1** i numer wystąpienia **03**) wartościami SAP HANA instalacji:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Utwórz wpis magazynu kluczy.

   Uruchom następujące polecenie jako katalog główny, aby utworzyć nowy wpis magazynu kluczy:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1] Kopii** zapasowej bazy danych.

   Kopii zapasowej baz danych jako katalogu głównego:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Jeśli korzystasz z instalacji z wieloma dzierżawami, należy również wrócić do kopii zapasowej bazy danych dzierżawy:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1] Skonfiguruj** replikację systemu w pierwszym węźle.

   Utwórz lokację główną jako <hanasid \> adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2] Skonfiguruj** replikację systemu w węźle pomocniczym.

   Zarejestruj lokację dodatkową jako <hanasid \> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>Implementowanie wpięć replikacji systemu języka Python SAPHanaSR

Jest to ważny krok w celu zoptymalizowania integracji z klastrem i ulepszenia wykrywania, gdy jest wymagane włączenie klastra w tryb failover. Zdecydowanie zaleca się skonfigurowanie zaczepienia SAPHanaSR w języku Python.    

1. **[A]** Zainstaluj "wpięcie replikacji systemu" platformy HANA. Węzły zaczepienia muszą być zainstalowane w obu węzłach usługi HANA DB.           

   > [!TIP]
   > Sprawdź, czy pakiet SAPHanaSR jest co najmniej w wersji 0.153, aby móc korzystać z funkcji podłączania języka Python SAPHanaSR.       
   > Wpięcie języka Python można zaimplementować tylko dla platformy HANA 2.0.        

   1. Przygotuj wpięcie jako `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Zatrzymaj hanę na obu węzłach. Wykonaj jako <sid \> adm:  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Dostosuj `global.ini` ustawienia na każdym węźle klastra.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** Klaster wymaga konfiguracji programu sudoers w każdym węźle klastra <sid \> adm. W tym przykładzie jest to osiągane przez utworzenie nowego pliku. Wykonaj polecenia jako `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```
Aby uzyskać więcej informacji na temat implementacji wpięcie replikacji systemu SAP HANA, zobacz [Konfigurowanie dostawców HA/DR platformy HANA.](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-sr-guide-PerfOpt-12/index.html#_set_up_sap_hana_hadr_providers)  

3. **[A]** Uruchom SAP HANA obu węzłach. Wykonaj jako <sid \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1] Zweryfikuj** instalację wpięć. Wykonaj polecenie <\> sid adm w aktywnej lokacji replikacji systemu HANA.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-08 22:18:15.877583 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:18:46.531564 ha_dr_SAPHanaSR SFAIL
     # 2021-04-08 22:21:26.816573 ha_dr_SAPHanaSR SOK

    ```

## <a name="create-sap-hana-cluster-resources"></a>Tworzenie SAP HANA klastra

Najpierw utwórz topologię platformy HANA. Uruchom następujące polecenia w jednym z węzłów klastra Pacemaker:

<pre><code>sudo crm configure property maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID

sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
  operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10" timeout="600" \
  op start interval="0" timeout="600" \
  op stop interval="0" timeout="300" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"

sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  meta clone-node-max="1" target-role="Started" interleave="true"
</code></pre>

Następnie utwórz zasoby platformy HANA:

> [!IMPORTANT]
> Ostatnie testy wykazały sytuacje, w których netcat przestaje odpowiadać na żądania z powodu listy prac i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań usługi Azure Load Balancer, a zmienny adres IP staje się niedostępny.  
> W przypadku istniejących klastrów Pacemaker zalecamy, aby w przeszłości zastąpić netcat socat. Obecnie zalecamy używanie agenta zasobów azure-lb, który jest częścią pakietu resource-agents, z następującymi wymaganiami wersji pakietu:
> - W przypadku wersji SLES 12 SP4/SP5 wersja musi być co najmniej wersją resource-agents-4.3.018.a7fb5035-3.30.1.  
> - W przypadku wersji SLES 15/15 SP1 wersja musi być co najmniej wersją resource-agents-4.3.0184.6ee15eb2-4.13.1.  
>
> Należy pamiętać, że zmiana będzie wymagać krótkiego przestoju.  
> Jeśli w przypadku istniejących klastrów Pacemaker konfiguracja została już zmieniona w celu używania programu socat zgodnie z opisem w tesłudze [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128)(Wzmacnianie zabezpieczeń wykrywania wykrywania usługi Azure Load-Balancer), nie jest wymagane natychmiastowe przełączenie się do agenta zasobów azure-lb.


> [!NOTE]
> Ten artykuł zawiera odwołania do terminów *master i* *podrzędny*, terminów, których firma Microsoft już nie używa. Gdy te warunki zostaną usunięte z oprogramowania, usuniemy je z tego artykułu.

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer. 

sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
  operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
  op start interval="0" timeout="3600" \
  op stop interval="0" timeout="3600" \
  op promote interval="0" timeout="3600" \
  op monitor interval="60" role="Master" timeout="700" \
  op monitor interval="61" role="Slave" timeout="700" \
  params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
  DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
  meta notify="true" clone-max="2" clone-node-max="1" \
  target-role="Started" interleave="true"

sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
  meta target-role="Started" \
  operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
  op monitor interval="10s" timeout="20s" \
  params ip="<b>10.0.0.13</b>"

sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> azure-lb port=625<b>03</b> \
  meta resource-stickiness=0

sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>

sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 4000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  

sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> Optional: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
  msl_SAPHana_<b>HN1</b>_HDB<b>03</b>

# Clean up the HANA resources. The HANA resources might have failed because of a known issue.
sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

sudo crm configure property maintenance-mode=false
sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby zostały uruchomione. Nie jest ważne, w którym węźle są uruchomione zasoby.

<pre><code>sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
</code></pre>

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Konfigurowanie replikacji systemu z włączoną obsługą aktywnej/odczytu platformy HANA w klastrze Pacemaker

Począwszy od programu SAP HANA 2.0 SPS 01 SAP, system umożliwia konfigurację z włączoną obsługą aktywny/odczyt dla replikacji systemu SAP HANA, w której systemy pomocnicze systemu replikacji systemu SAP HANA mogą być aktywnie używane w przypadku obciążeń intensywnie wymagających odczytu. Do obsługi takiej konfiguracji w klastrze wymagany jest drugi wirtualny adres IP, który umożliwia klientom dostęp do pomocniczej bazy danych z włączoną obsługą odczytu SAP HANA danych. Aby zapewnić, że dostęp do dodatkowej lokacji replikacji będzie nadal możliwy po zakończeniu przejęcia, klaster musi przenieść wirtualny adres IP z pomocniczym zasobem SAPHana.

W tej sekcji opisano dodatkowe kroki, które są wymagane do zarządzania replikacją systemu z włączoną obsługą hana HANA Active/Read w klastrze wysokiej dostępności SUSE z drugim wirtualnym adresem IP.    
Przed dalszymi krokami upewnij się, że masz w pełni skonfigurowany klaster wysokiej dostępności SUSE zarządzający SAP HANA bazą danych, zgodnie z opisem w powyższych segmentach dokumentacji.  

![SAP HANA wysokiej dostępności z pomocniczą obsługą odczytu](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Dodatkowa konfiguracja w usłudze Azure Load Balancer w celu instalacji aktywnej/z włączoną obsługą odczytu

Aby kontynuować dodatkowe kroki aprowiowania drugiego wirtualnego adresu IP, upewnij się, że skonfigurowano Azure Load Balancer zgodnie z opisem w sekcji [Wdrażanie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#manual-deployment) ręczne.

1. W **przypadku usługi** load balancer w standardowych wersjach wykonaj dodatkowe kroki opisane poniżej dotyczące tego samego usługi równoważenia obciążenia, która została utworzona we wcześniejszej sekcji.

   a. Utwórz drugą pulę adresów IP frontonia: 

   - Otwórz równoważenie obciążenia, wybierz **pulę adresów IP frontonia** i wybierz pozycję **Dodaj.**
   - Wprowadź nazwę drugiej puli adresów IP frontonia (na przykład **hana-secondaryIP).**
   - Ustaw **przypisanie na** **statyczny** i wprowadź adres IP (na przykład **10.0.0.14).**
   - Wybierz przycisk **OK**.
   - Po utworzeniu nowej puli adresów IP frontendu zanotuj adres IP frontonia.

   b. Następnie utwórz sondę kondycji:

   - Otwórz usługę równoważenia obciążenia, wybierz **sondy kondycji** i wybierz pozycję **Dodaj.**
   - Wprowadź nazwę nowej sondy kondycji (na przykład **hana-secondaryhp).**
   - Wybierz **TCP** jako protokół i port **62603.** Zachowaj wartość **Interwał** ustawioną na 5, a **wartość** progowa W złej kondycji na 2.
   - Wybierz przycisk **OK**.

   c. Następnie utwórz reguły równoważenia obciążenia:

   - Otwórz równoważenie obciążenia, wybierz **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
   - Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **hana-secondarylb).**
   - Wybierz adres IP frontonia, pulę zaplecza i utworzoną wcześniej sondę kondycji (na przykład **hana-secondaryIP,** **hana-backend** i **hana-secondaryhp).**
   - Wybierz pozycję **Porty HA.**
   - Zwiększ limit **czasu bezczynności do** 30 minut.
   - Upewnij się, że **włączyć zmienny adres IP**.
   - Wybierz przycisk **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>Konfigurowanie replikacji systemu z włączoną obsługą aktywnej/odczytu platformy HANA

Kroki konfigurowania replikacji systemu HANA opisano w sekcji [Konfigurowanie replikacji systemu SAP HANA 2.0.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#configure-sap-hana-20-system-replication) Jeśli wdrażasz pomocniczy scenariusz z włączoną obsługą odczytu podczas konfigurowania replikacji systemu w drugim węźle, wykonaj następujące polecenie jako **hanasid** adm:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Dodawanie pomocniczego zasobu wirtualnego adresu IP dla konfiguracji aktywnej/z włączoną obsługą odczytu

Drugi wirtualny adres IP i odpowiednie ograniczenie kolokacji można skonfigurować za pomocą następujących poleceń:

```
crm configure property maintenance-mode=true

crm configure primitive rsc_secip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
 meta target-role="Started" \
 operations \$id="rsc_secip_HN1_HDB03-operations" \
 op monitor interval="10s" timeout="20s" \
 params ip="10.0.0.14"

crm configure primitive rsc_secnc_HN1_HDB03 azure-lb port=62603 \
 meta resource-stickiness=0

crm configure group g_secip_HN1_HDB03 rsc_secip_HN1_HDB03 rsc_secnc_HN1_HDB03

crm configure colocation col_saphana_secip_HN1_HDB03 4000: g_secip_HN1_HDB03:Started \
 msl_SAPHana_HN1_HDB03:Slave 

crm configure property maintenance-mode=false
```
Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby zostały uruchomione. Drugi wirtualny adres IP zostanie uruchomiony w lokacji dodatkowej wraz z zasobem pomocniczym SAPHana.

```
sudo crm_mon -r

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# stonith-sbd     (stonith:external/sbd): Started hn1-db-0
# Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
#     Started: [ hn1-db-0 hn1-db-1 ]
# Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
# Resource Group: g_ip_HN1_HDB03
#     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
# Resource Group: g_secip_HN1_HDB03:
#     rsc_secip_HN1_HDB03       (ocf::heartbeat:IPaddr2):        Started hn1-db-1
#     rsc_secnc_HN1_HDB03       (ocf::heartbeat:azure-lb):       Started hn1-db-1

```

W następnej sekcji znajdziesz typowy zestaw testów trybu failover do wykonania.

Należy pamiętać o drugim zachowaniu wirtualnego adresu IP podczas testowania klastra HANA skonfigurowanego z pomocniczym włączonym odczytem:

1. Podczas migracji **SAPHana_HN1_HDB03** klastra do **hn1-db-1** drugi wirtualny adres IP zostanie przeniesiona na inny serwer **hn1-db-0.** Jeśli skonfigurowano wartość AUTOMATED_REGISTER="false", a replikacja systemu HANA nie jest rejestrowana automatycznie, drugi wirtualny adres IP zostanie uruchomiony na serwerze **hn1-db-0,** ponieważ serwer jest dostępny, a usługi klastra są w trybie online.  

2. Podczas testowania awarii serwera drugi wirtualny adres IP **(rsc_secip_HN1_HDB03)** i zasób portu usługi Azure Load Balancer **(rsc_secnc_HN1_HDB03)** będą uruchamiane na serwerze podstawowym wraz z podstawowymi zasobami wirtualnego adresu IP. Gdy serwer pomocniczy nie jest już włączony, aplikacje połączone z bazą danych HANA z włączoną obsługą odczytu będą łączyć się z podstawową bazą danych HANA. Zachowanie jest oczekiwane, ponieważ nie chcesz, aby aplikacje połączone z bazą danych HANA z włączoną obsługą odczytu były niedostępne, gdy serwer pomocniczy jest niedostępny.
  
3. Gdy serwer pomocniczy jest dostępny, a usługi klastra są w trybie online, drugi wirtualny adres IP i zasoby portów zostaną automatycznie przenieść na serwer pomocniczy, mimo że replikacja systemu HANA może nie zostać zarejestrowana jako pomocnicza. Przed uruchomieniem usług klastra na tym serwerze należy upewnić się, że pomocnicza baza danych HANA jest włączona jako włączona. Zasób klastra wystąpień platformy HANA można skonfigurować do automatycznego rejestrowania pomocniczego, ustawiając parametr AUTOMATED_REGISTER=true.       

4. Podczas pracy w przypadku trybu failover i powrotu po awarii istniejące połączenia dla aplikacji używające drugiego wirtualnego adresu IP do nawiązywania połączenia z bazą danych HANA mogą zostać przerwane.  

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano sposób testowania konfiguracji. Każdy test zakłada, że użytkownik jest użytkownikiem głównym, a SAP HANA główny jest uruchomiony na maszynie wirtualnej **hn1-db-0.**

### <a name="test-the-migration"></a>Testowanie migracji

Przed rozpoczęciem testu upewnij się, że program Pacemaker nie ma żadnych akcji nieudanych (za pośrednictwem crm_mon -r), nie ma żadnych nieoczekiwanych ograniczeń lokalizacji (na przykład pozostawień testu migracji) i że hana jest stanem synchronizacji, na przykład z sapHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr
Sites    srHook
----------------
SITE2    SOK

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Możesz przeprowadzić migrację SAP HANA głównego, wykonując następujące polecenie:

<pre><code>crm resource move msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b> force
</code></pre>

Jeśli ustawisz wartość , ta sekwencja poleceń powinna migrować węzeł główny SAP HANA grupę zawierającą wirtualny adres IP do `AUTOMATED_REGISTER="false"` węzła hn1-db-1.

Po zakończeniu migracji dane wyjściowe crm_mon -r wyglądają następująco

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Stopped: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1

Failed Actions:
* rsc_SAPHana_HN1_HDB03_start_0 on hn1-db-0 'not running' (7): call=84, status=complete, exitreason='none',
    last-rc-change='Mon Aug 13 11:31:37 2018', queued=0ms, exec=2095ms
</code></pre>

Zasób SAP HANA hn1-db-0 nie może uruchomić się jako pomocniczy. W takim przypadku skonfiguruj wystąpienie platformy HANA jako pomocnicze, wykonując to polecenie:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Migracja tworzy ograniczenia lokalizacji, które należy usunąć ponownie:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource clear msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Należy również wyczyścić stan zasobu węzła pomocniczego:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitoruj stan zasobu PLATFORMY HANA przy użyciu crm_mon -r. Po zakończeniu pracy z programem HANA na hn1-db-0 dane wyjściowe powinny wyglądać podobnie do tych

<pre><code>Online: [ hn1-db-0 hn1-db-1 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started hn1-db-1
 Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hn1-db-0 hn1-db-1 ]
 Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
 Resource Group: g_ip_HN1_HDB03
     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testowanie agenta odszukania platformy Azure (nie SBD)

Możesz przetestować konfigurację agenta ogrodzenia platformy Azure, wyłączając interfejs sieciowy w węźle hn1-db-0:

<pre><code>sudo ifdown eth0
</code></pre>

Maszyna wirtualna powinna zostać ponownie uruchomiona lub zatrzymana w zależności od konfiguracji klastra.
Jeśli to ustawienie zostanie wyłączone, maszyna wirtualna zostanie zatrzymana, a `stonith-action` zasoby zostaną zmigrowane do uruchomionej maszyny wirtualnej.

Po ponownie uruchomieniu maszyny wirtualnej nie można uruchomić SAP HANA pomocniczego, jeśli ustawisz wartość `AUTOMATED_REGISTER="false"` . W takim przypadku skonfiguruj wystąpienie platformy HANA jako pomocnicze, wykonując to polecenie:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Testowanie ogrodzenia SBD

Konfigurację SBD można przetestować, zabicia procesu inquisitor.

<pre><code>hn1-db-0:~ # ps aux | grep sbd
root       1912  0.0  0.0  85420 11740 ?        SL   12:25   0:00 sbd: inquisitor
root       1929  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014056f268462316e4681b704a9f73 - slot: 0 - uuid: 7b862dba-e7f7-4800-92ed-f76a4e3978c8
root       1930  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-360014059bc9ea4e4bac4b18808299aaf - slot: 0 - uuid: 5813ee04-b75c-482e-805e-3b1e22ba16cd
root       1931  0.0  0.0  85456 11776 ?        SL   12:25   0:00 sbd: watcher: /dev/disk/by-id/scsi-36001405b8dddd44eb3647908def6621c - slot: 0 - uuid: 986ed8f8-947d-4396-8aec-b933b75e904c
root       1932  0.0  0.0  90524 16656 ?        SL   12:25   0:00 sbd: watcher: Pacemaker
root       1933  0.0  0.0 102708 28260 ?        SL   12:25   0:00 sbd: watcher: Cluster
root      13877  0.0  0.0   9292  1572 pts/0    S+   12:27   0:00 grep sbd

hn1-db-0:~ # kill -9 1912
</code></pre>

Należy ponownie uruchomić węzeł klastra hn1-db-0. Usługa Pacemaker może nie zacząć później. Pamiętaj, aby uruchomić go ponownie.

### <a name="test-a-manual-failover"></a>Testowanie ręcznego trybu failover

Ręcznego trybu failover można przetestować, zatrzymując `pacemaker` usługę w węźle hn1-db-0:

<pre><code>service pacemaker stop
</code></pre>

Po zakończeniu pracy w trybu failover możesz ponownie uruchomić usługę. Jeśli `AUTOMATED_REGISTER="false"` ustawisz wartość , SAP HANA w węźle hn1-db-0 nie będzie można uruchomić jako pomocniczy. W takim przypadku skonfiguruj wystąpienie platformy HANA jako pomocnicze, wykonując to polecenie:

<pre><code>service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="suse-tests"></a>Testy SUSE

> [!IMPORTANT]
> Upewnij się, że wybierany system operacyjny ma certyfikat SAP SAP HANA dla określonych typów maszyn wirtualnych, których używasz. Listę certyfikowanych SAP HANA maszyn wirtualnych i wydań systemu operacyjnego dla tych typów można znaleźć w te SAP HANA [certyfikowanych platform IaaS.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Pamiętaj, aby kliknąć szczegóły wymienionego typu maszyny wirtualnej, aby uzyskać pełną listę wersji SAP HANA obsługiwanych wersji systemu operacyjnego dla określonego typu maszyny wirtualnej

Uruchom wszystkie przypadki testowe wymienione w scenariuszu optymalizacji wydajności SAP HANA SR lub SAP HANA scenariusz zoptymalizowany pod kątem kosztów, w zależności od przypadku użycia. Przewodniki można znaleźć na stronie [SLES for SAP best practices (Najlepsze rozwiązania dotyczące systemu SLES dla systemu SAP).][sles-for-sap-bp]

Poniższe testy są kopią opisów testów przewodnika SAP HANA SR Performance Optimized Scenario SUSE Linux Enterprise Server for SAP Applications 12 SP1 (Scenariusz zoptymalizowany pod kątem wydajności SUSE Linux Enterprise Server dla aplikacji SAP 12 SP1). Aby uzyskać najnowszą wersję, zawsze przeczytaj sam przewodnik. Przed rozpoczęciem testu upewnij się, że program HANA jest zsynchronizowany, a także upewnij się, że konfiguracja programu Pacemaker jest poprawna.

W poniższych opisach testów założono, że PREFER_SITE_TAKEOVER="true" i AUTOMATED_REGISTER="false".
UWAGA: Następujące testy są przeznaczone do uruchamiania kolejno i zależą od stanu zakończenia poprzednich testów.

1. TEST 1: ZATRZYMYWANIE PODSTAWOWEJ BAZY DANYCH W WĘŹLE 1

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Program Pacemaker powinien wykrywać zatrzymane wystąpienie platformy HANA i przechowywał do trybu failover do drugiego węzła. Po zakończeniu trybu failover wystąpienie platformy HANA w węźle hn1-db-0 zostanie zatrzymane, ponieważ program Pacemaker nie zarejestruje automatycznie węzła jako pomocniczego hana.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-0 jako pomocniczy i wyczyścić zasób, który zakończył się niepowodzeniem.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 2: ZATRZYMYWANIE PODSTAWOWEJ BAZY DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Program Pacemaker powinien wykrywać zatrzymane wystąpienie platformy HANA i przechowywał do trybu failover do drugiego węzła. Po zakończeniu trybu failover wystąpienie platformy HANA w węźle hn1-db-1 zostanie zatrzymane, ponieważ program Pacemaker nie zarejestruje automatycznie węzła jako pomocniczego hana.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-1 jako pomocniczy i wyczyścić zasób, który zakończył się niepowodzeniem.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 3: AWARIA PODSTAWOWEJ BAZY DANYCH W WĘŹLE

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-db-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Program Pacemaker powinien wykryć zabite wystąpienie platformy HANA i przechować go do trybu failover do drugiego węzła. Po zakończeniu trybu failover wystąpienie platformy HANA w węźle hn1-db-0 zostanie zatrzymane, ponieważ program Pacemaker nie zarejestruje automatycznie węzła jako pomocniczego hana.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-0 jako pomocniczy i wyczyścić zasób, który zakończył się niepowodzeniem.

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 4: AWARIA PODSTAWOWEJ BAZY DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Program Pacemaker powinien wykryć zabite wystąpienie platformy HANA i przechować go do trybu failover do drugiego węzła. Po zakończeniu trybu failover wystąpienie platformy HANA w węźle hn1-db-1 zostanie zatrzymane, ponieważ program Pacemaker nie zarejestruje automatycznie węzła jako pomocniczego hana.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-db-1 jako pomocniczy i wyczyścić zasób, który zakończył się niepowodzeniem.

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 5: AWARIA WĘZŁA LOKACJI GŁÓWNEJ (WĘZEŁ 1)

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako główny w węźle hn1-db-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Program Pacemaker powinien wykryć zabity węzeł klastra i ogrodzyć węzeł. Po ogrodzeniu węzła program Pacemaker wyzwoli przejęcie wystąpienia platformy HANA. Po ponownym uruchomieniu ogrodzonego węzła program Pacemaker nie zostanie uruchomiony automatycznie.

   Uruchom następujące polecenia, aby uruchomić program Pacemaker, wyczyścić komunikaty SBD dla węzła hn1-db-0, zarejestrować węzeł hn1-db-0 jako pomocniczy i wyczyścić zasób, który zakończył się niepowodzeniem.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-0 clear
   
   hn1-db-0:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMode=sync --name=SITE1
   
   # run as root
   hn1-db-0:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-0
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

1. TEST 6: AWARIA WĘZŁA LOKACJI DODATKOWEJ (WĘZEŁ 2)

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-1 ]
      Slaves: [ hn1-db-0 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-1
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-1
   </code></pre>

   Uruchom następujące polecenia jako główny w węźle hn1-db-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Program Pacemaker powinien wykryć zagrozony węzeł klastra i ogrodzyć węzeł. Po ogrodzeniu węzła program Pacemaker wyzwoli przejęcie wystąpienia platformy HANA. Po ponownym uruchomieniu ogrodzonego węzła program Pacemaker nie zostanie uruchomiony automatycznie.

   Uruchom następujące polecenia, aby uruchomić program Pacemaker, wyczyścić komunikaty SBD dla węzła hn1-db-1, zarejestrować węzeł hn1-db-1 jako pomocniczy i wyczyścić zasób, który zakończył się niepowodzeniem.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker
   
   # run as &lt;hanasid&gt;adm
   hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2
   
   # run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 7: ZATRZYMYWANIE POMOCNICZEJ BAZY DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Program Pacemaker wykryje zatrzymane wystąpienie platformy HANA i oznaczy zasób jako nieudany w węźle hn1-db-1. Program Pacemaker powinien automatycznie ponownie uruchomić wystąpienie platformy HANA. Uruchom następujące polecenie, aby wyczyścić stan niepowodzenie.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 8: AWARIA POMOCNICZEJ BAZY DANYCH W WĘŹLE 2

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-db-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Program Pacemaker wykryje zabłąkane wystąpienie platformy HANA i oznaczy zasób jako nieudany w węźle hn1-db-1. Uruchom następujące polecenie, aby wyczyścić stan niepowodzenie. Następnie program Pacemaker powinien automatycznie ponownie uruchomić wystąpienie platformy HANA.

   <pre><code># run as root
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

1. TEST 9: AWARIA WĘZŁA LOKACJI DODATKOWEJ (WĘZEŁ 2) Z URUCHAMIANĄ POMOCNICZĄ BAZĄ DANYCH HANA

   Stan zasobu przed rozpoczęciem testu:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

   Uruchom następujące polecenia jako główny w węźle hn1-db-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Program Pacemaker powinien wykryć zabity węzeł klastra i ogrodzyć węzeł. Po ponownym uruchomieniu ogrodzonego węzła program Pacemaker nie zostanie uruchomiony automatycznie.

   Uruchom następujące polecenia, aby uruchomić program Pacemaker, wyczyścić komunikaty SBD dla węzła hn1-db-1 i wyczyścić zasób, który zakończył się niepowodzeniem.

   <pre><code># run as root
   # list the SBD device(s)
   hn1-db-1:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   hn1-db-1:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message hn1-db-1 clear
   
   hn1-db-1:~ # systemctl start pacemaker  
   
   hn1-db-1:~ # crm resource cleanup msl_SAPHana_HN1_HDB03 hn1-db-1
   </code></pre>

   Stan zasobu po teście:

   <pre><code>Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
      Started: [ hn1-db-0 hn1-db-1 ]
   Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
      Masters: [ hn1-db-0 ]
      Slaves: [ hn1-db-1 ]
   Resource Group: g_ip_HN1_HDB03
      rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
      rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hn1-db-0
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [Planowanie Virtual Machines wdrażania oprogramowania SAP na platformie Azure][planning-guide]
* [Wdrażanie usługi Azure Virtual Machines sap][deployment-guide]
* [Wdrażanie usługi Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
