---
title: Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure w systemie SLES | Microsoft Docs
description: Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: radeltch
ms.openlocfilehash: 42a4c4a41f6c8bdf9d4a8e78f634893722c8f389
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576405"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-suse-linux-enterprise-server"></a>Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server

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

W przypadku programowania lokalnego można użyć replikacji systemu HANA lub użyć magazynu udostępnionego do ustanowienia wysokiej dostępności dla SAP HANA.
Na platformie Azure Virtual Machines funkcja replikacji systemu HANA na platformę Azure jest obecnie jedyną obsługiwaną funkcją wysokiej dostępności. SAP HANA replikacja składa się z jednego węzła podstawowego i co najmniej jednego węzła pomocniczego. Zmiany danych w węźle podstawowym są replikowane do węzła pomocniczego synchronicznie lub asynchronicznie.

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych, instalowania platformy klastra oraz instalowania i konfigurowania replikacji systemu SAP HANA.
W przykładowych konfiguracjach, poleceniach instalacji, numer wystąpienia nr **03** i identyfikator systemu Hana **HN1** są używane.

Przeczytaj najpierw następujące informacje i dokumenty SAP:

* Nota SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane programy SAP i system operacyjny oraz kombinacje baz danych.
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure.
* W przypadku oprogramowania SAP Uwaga [2015553] przedstawiono wymagania wstępne dotyczące wdrożeń programu SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2205917] zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server aplikacji SAP.
* Uwaga dotycząca oprogramowania SAP [1944799] SAP HANA wytycznych dotyczących SUSE Linux Enterprise Server dla aplikacji SAP.
* Uwaga dotycząca oprogramowania SAP [2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca protokołu SAP [1984787] zawiera ogólne informacje o SUSE Linux Enterprise Server 12.
* Uwaga dotycząca programu SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* Uwaga dotycząca protokołu SAP [401162] zawiera informacje o tym, jak unikać "adresu już używanego" podczas konfigurowania replikacji systemu Hana.
* System [SAP wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [SAP HANA certyfikowane platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
* [Azure Virtual Machines planowanie i wdrażanie oprogramowania SAP w systemie Linux][planning-guide] — przewodnik.
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide] (ten artykuł).
* Przewodnik [wdrażania systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide] .
* [SUSE Linux Enterprise Server for SAP Applications 12 SP3 — wskazówki dotyczące najlepszych rozwiązań][sles-for-sap-bp]
  * Konfigurowanie SAP HANAej infrastruktury zoptymalizowanej pod kątem wydajności SR (SLES for SAP Applications 12 SP1). Przewodnik zawiera wszystkie informacje wymagane do skonfigurowania replikacji systemu SAP HANA na potrzeby programowania lokalnego. Użyj tego przewodnika jako linii bazowej.
  * Konfigurowanie SAP HANAej infrastruktury zoptymalizowanej pod kątem kosztu SR (SLES for SAP, 12 SP1)

## <a name="overview"></a>Omówienie

Aby zapewnić wysoką dostępność, SAP HANA jest instalowany na dwóch maszynach wirtualnych. Dane są replikowane przy użyciu replikacji systemu HANA.

![Przegląd wysokiej dostępności SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

Konfiguracja replikacji systemu SAP HANA używa dedykowanej wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia:

* Konfiguracja frontonu: adres IP 10.0.0.13 dla hn1-DB
* Konfiguracja zaplecza: połączono z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią replikacji systemu HANA
* Port sondy: Port 62503
* Reguły równoważenia obciążenia: 30313 TCP, 30315 TCP, 30317 TCP

## <a name="deploy-for-linux"></a>Wdrażanie dla systemu Linux

Agent zasobów dla SAP HANA jest uwzględniony w SUSE Linux Enterprise Server dla aplikacji SAP.
Portal Azure Marketplace zawiera obraz dla SUSE Linux Enterprise Server dla aplikacji SAP 12, których można użyć do wdrażania nowych maszyn wirtualnych.

### <a name="deploy-with-a-template"></a>Wdrażanie za pomocą szablonu

Możesz użyć jednego z szablonów szybkiego startu, które znajdują się w serwisie GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, moduł równoważenia obciążenia, zestaw dostępności i tak dalej.
Aby wdrożyć szablon, wykonaj następujące kroki:

1. Otwórz [szablon bazy danych][template-multisid-db] lub [zbieżny szablon][template-converged] na Azure Portal. 
    Szablon bazy danych tworzy reguły równoważenia obciążenia tylko dla bazy danych. Zbieżny szablon tworzy również reguły równoważenia obciążenia dla wystąpienia ASCS/SCS i wykres WYWOŁUJĄCYCH (tylko system Linux). Jeśli planujesz zainstalować system oparty na oprogramowaniu SAP NetWeaver i chcesz zainstalować wystąpienie ASCS/SCS na tych samych komputerach, użyj [zbieżnego szablonu][template-converged].

1. Wprowadź następujące parametry:
    - **Identyfikator systemu SAP**: Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla wdrożonych zasobów.
    - **Typ stosu**: (ten parametr ma zastosowanie tylko w przypadku użycia zbieżnego szablonu). Wybierz typ stosu SAP NetWeaver.
    - **Typ systemu operacyjnego**: Wybierz jedną z dystrybucji Linux. Na potrzeby tego przykładu wybierz pozycję **SLES 12**.
    - **Typ bazy danych**: wybierz pozycję **Hana**.
    - **Rozmiar systemu SAP**: wprowadź liczbę punktów SAP, które ma dostarczyć nowy system. Jeśli nie masz pewności, ile punktów SAP wymaga system, skontaktuj się z partnerem technologii SAP lub integratorem systemu.
    - **Dostępność systemu**: Wybierz **ha**.
    - **Nazwa użytkownika administratora i hasło administratora**: tworzony jest nowy użytkownik, którego można użyć do zalogowania się na komputerze.
    - **Nowa lub istniejąca podsieć**: określa, czy należy utworzyć nową sieć wirtualną i podsieć, czy też użyto istniejącej podsieci. Jeśli masz już sieć wirtualną, która jest połączona z siecią lokalną, wybierz pozycję **istniejące**.
    - **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Identyfikator zazwyczaj wygląda podobnie do **/subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /Subnets/ \<subnet name>**.

### <a name="manual-deployment"></a>Wdrożenie ręczne

> [!IMPORTANT]
> Upewnij się, że wybrany system operacyjny to SAP Certified dla SAP HANA na określonych typach maszyn wirtualnych, których używasz. Listę SAP HANA certyfikowanych typów maszyn wirtualnych i wydań systemu operacyjnego dla tych systemów można wyszukać w [SAP HANA certyfikowanych platformach IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Pamiętaj, aby kliknąć na liście szczegóły typu maszyny wirtualnej, aby uzyskać pełną listę SAP HANA obsługiwanych wersji systemu operacyjnego dla określonego typu maszyny wirtualnej
>  

1. Utwórz grupę zasobów.
1. Utwórz sieć wirtualną.
1. Utwórz zestaw dostępności.
   - Ustaw maksymalną domenę aktualizacji.
1. Utwórz moduł równoważenia obciążenia (wewnętrzny). Zalecamy użycie [standardowej usługi równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md).
   - Wybierz sieć wirtualną utworzoną w kroku 2.
1. Utwórz maszynę wirtualną 1.
   - Użyj obrazu SLES4SAP w galerii platformy Azure, który jest obsługiwany dla SAP HANA w wybranym typie maszyny wirtualnej.
   - Wybierz zestaw dostępności utworzony w kroku 3.
1. Utwórz maszynę wirtualną 2.
   - Użyj obrazu SLES4SAP w galerii platformy Azure, który jest obsługiwany dla SAP HANA w wybranym typie maszyny wirtualnej.
   - Wybierz zestaw dostępności utworzony w kroku 3. 
1. Dodaj dyski danych.

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.   

> [!Note]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

1. W przypadku korzystania z usługi równoważenia obciążenia w warstwie Standardowa wykonaj następujące czynności konfiguracyjne:
   1. Najpierw Utwórz pulę adresów IP frontonu:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pula adresów IP frontonu**, a następnie wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **Hana-fronton**).
      1. Ustaw **przypisanie** na **static** i wprowadź adres IP (na przykład **10.0.0.13**).
      1. Wybierz przycisk **OK**.
      1. Po utworzeniu nowej puli adresów IP frontonu Zanotuj adres IP puli.
   
   1. Następnie Utwórz pulę zaplecza:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **Hana-zaplecze**).
      1. Wybierz **Virtual Network**.
      1. Wybierz pozycję **Dodaj maszynę wirtualną**.
      1. Wybierz pozycję * * maszyna wirtualna * *.
      1. Wybierz Maszyny wirtualne klastra SAP HANA i ich adresy IP.
      1. Wybierz pozycję **Dodaj**.
   
   1. Następnie utwórz sondę kondycji:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej sondy kondycji (na przykład **Hana-HP**).
      1. Wybierz pozycję **TCP** jako protokół i port 625 **03**. Pozostaw wartość **interwału** ustawioną na 5, a wartość **progowa złej kondycji** równa 2.
      1. Wybierz przycisk **OK**.
   
   1. Następnie utwórz reguły równoważenia obciążenia:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **Hana-lb**).
      1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-fronton**, **Hana — zaplecze** i **Hana-HP**).
      1. Wybierz pozycję **porty ha**.
      1. Zwiększ **limit czasu bezczynności** do 30 minut.
      1. Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
      1. Wybierz przycisk **OK**.

1. Alternatywnie, jeśli scenariusz podyktuje przy użyciu podstawowego modułu równoważenia obciążenia, wykonaj następujące czynności konfiguracyjne:
   1. Najpierw Utwórz pulę adresów IP frontonu:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pula adresów IP frontonu**, a następnie wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **Hana-fronton**).
      1. Ustaw **przypisanie** na **static** i wprowadź adres IP (na przykład **10.0.0.13**).
      1. Wybierz przycisk **OK**.
      1. Po utworzeniu nowej puli adresów IP frontonu Zanotuj adres IP puli.
   
   1. Następnie Utwórz pulę zaplecza:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej puli zaplecza (na przykład **Hana-zaplecze**).
      1. Wybierz pozycję **Dodaj maszynę wirtualną**.
      1. Wybierz zestaw dostępności utworzony w kroku 3.
      1. Wybierz Maszyny wirtualne klastra SAP HANA.
      1. Wybierz przycisk **OK**.
   
   1. Następnie utwórz sondę kondycji:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej sondy kondycji (na przykład **Hana-HP**).
      1. Wybierz pozycję **TCP** jako protokół i port 625 **03**. Pozostaw wartość **interwału** ustawioną na 5, a wartość **progowa złej kondycji** równa 2.
      1. Wybierz przycisk **OK**.
   
   1. W przypadku SAP HANA 1,0 Utwórz reguły równoważenia obciążenia:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład Hana-lb-3 **03** 15).
      1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-fronton**).
      1. Pozostaw **Protokół** ustawiony na **TCP** i wprowadź port 3 **03** 15.
      1. Zwiększ **limit czasu bezczynności** do 30 minut.
      1. Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
      1. Wybierz przycisk **OK**.
      1. Powtórz te kroki dla portu 3 **03** 17.
   
   1. W przypadku SAP HANA 2,0 Utwórz reguły równoważenia obciążenia dla systemowej bazy danych:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład Hana-lb-3 **03** 13).
      1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-fronton**).
      1. Pozostaw **Protokół** ustawiony na **TCP** i wprowadź port 3 **03** 13.
      1. Zwiększ **limit czasu bezczynności** do 30 minut.
      1. Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
      1. Wybierz przycisk **OK**.
      1. Powtórz te kroki dla portu 3 **03** 14.
   
   1. W przypadku SAP HANA 2,0 najpierw utwórz reguły równoważenia obciążenia dla bazy danych dzierżawcy:
   
      1. Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj**.
      1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład Hana-lb-3 **03** 40).
      1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-fronton**).
      1. Pozostaw **Protokół** ustawiony na **TCP** i wprowadź port 3 **03** 40.
      1. Zwiększ **limit czasu bezczynności** do 30 minut.
      1. Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
      1. Wybierz przycisk **OK**.
      1. Powtórz te kroki dla portów 3 **03** 41 i 3 **03** 42.

   Aby uzyskać więcej informacji na temat wymaganych portów dla SAP HANA, zapoznaj się z rozdziałem [połączenia z bazami danych dzierżawy](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) w Przewodniku obsługi [bazy danych dzierżaw SAP HANA](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) lub [Uwaga 2388694][2388694].

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych protokołu TCP na maszynach wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie sygnatur czasowych protokołu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.IPv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).
> Zobacz również artykuł SAP Uwaga [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w temacie [Konfigurowanie Pacemaker SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) , aby utworzyć podstawowy klaster Pacemaker dla tego serwera platformy Hana. Tego samego klastra Pacemaker można użyć dla SAP HANA i SAP NetWeaver (A) SCS.

## <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

W procedurach przedstawionych w tej sekcji są używane następujące prefiksy:
- **[A]**: krok ma zastosowanie do wszystkich węzłów.
- **[1]**: krok ma zastosowanie tylko do węzła 1.
- **[2]**: krok ma zastosowanie tylko do węzła 2 klastra Pacemaker.

1. **[A]** Skonfiguruj układ dysku: **Menedżer woluminów logicznych (LVM)**.

   Zalecamy używanie LVM dla woluminów, które przechowują pliki danych i dziennika. W poniższym przykładzie założono, że maszyny wirtualne mają dołączone cztery dyski z danymi, które są używane do tworzenia dwóch woluminów.

   Wyświetl listę wszystkich dostępnych dysków:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Przykładowe dane wyjściowe:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Utwórz woluminy fizyczne dla wszystkich dysków, które mają być używane:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Utwórz grupę woluminów dla plików danych. Użyj jednej grupy woluminów dla plików dziennika i jednego dla katalogu udostępnionego SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Utwórz woluminy logiczne. Wolumin liniowy jest tworzony, gdy jest używany `lvcreate` bez `-i` przełącznika. Zalecamy utworzenie woluminu rozłożonego w celu uzyskania lepszej wydajności operacji we/wy i dostosowanie rozmiarów rozłożonych do wartości przedstawionych w [SAP HANA konfiguracjach magazynu maszyny wirtualnej](./hana-vm-operations-storage.md). `-i`Argument powinien być liczbą podstawowych woluminów fizycznych, a `-I` argument jest rozmiarem paska. W tym dokumencie dwa woluminy fizyczne są używane dla woluminu danych, więc `-i` argument Switch ma wartość **2**. Rozmiar rozłożonego woluminu danych to **256KiB**. Jeden wolumin fizyczny jest używany w woluminie dziennika, więc żadne `-i` `-I` przełączniki nie są jawnie używane dla poleceń woluminu dziennika.  

   > [!IMPORTANT]
   > Użyj `-i` przełącznika i ustaw go na numer bazowego woluminu fizycznego, jeśli używasz więcej niż jednego woluminu fizycznego dla każdego danych, dziennika lub udostępnionych woluminów. Użyj `-I` przełącznika, aby określić rozmiar paska podczas tworzenia woluminu rozłożonego.  
   > Zapoznaj się z [SAP HANA konfiguracjami magazynu maszyn wirtualnych](./hana-vm-operations-storage.md) , aby uzyskać zalecane konfiguracje magazynu, w tym rozmiary i liczbę dysków.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>
  
   Utwórz katalogi instalacji i skopiuj identyfikator UUID wszystkich woluminów logicznych:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   Utwórz `fstab` wpisy dla trzech woluminów logicznych:       

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Wstaw następujący wiersz do `/etc/fstab` pliku:      

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   Zainstaluj nowe woluminy:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Skonfiguruj układ dysku: **zwykłe dyski**.

   W przypadku systemów demonstracyjnych można umieścić pliki danych i dziennika platformy HANA na jednym dysku. Utwórz partycję na/dev/Disk/Azure/scsi1/lun0 i sformatuj ją z XFS:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Wstaw ten wiersz w pliku/etc/fstab:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Utwórz katalog docelowy i zainstaluj dysk:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A]** Skonfiguruj rozpoznawanie nazw hostów dla wszystkich hostów.

   Możesz użyć serwera DNS lub zmodyfikować plik/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze w pliku/etc/hosts. Zmień adres IP i nazwę hosta w taki sposób, aby odpowiadały Twojemu środowisku:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Zainstaluj SAP HANA pakiety o wysokiej dostępności:

   <pre><code>sudo zypper install SAPHanaSR
   </code></pre>

Aby zainstalować replikację systemu SAP HANA, postępuj zgodnie z rozdziałem 4 [przewodnika dotyczącego scenariusza SAP HANAa Optymalizacja wydajności SR](https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/).

1. **[A]** Uruchom program **hdblcm** z dysku DVD platformy Hana. W wierszu polecenia wprowadź następujące wartości:
   * Wybierz pozycję Instalacja: wprowadź **1**.
   * Wybierz dodatkowe składniki do instalacji: wprowadź **1**.
   * Wprowadź ścieżkę instalacji [/Hana/Shared]: Wybierz ENTER.
   * Wprowadź nazwę hosta lokalnego [..]: wybierz klawisz ENTER.
   * Czy chcesz dodać do systemu dodatkowe hosty? (t/n) [n]: wybierz klawisz ENTER.
   * Wprowadź identyfikator systemu SAP HANA: Wprowadź identyfikator SID platformy HANA, na przykład: **HN1**.
   * Wprowadź numer wystąpienia [00]: Wprowadź numer wystąpienia HANA. Wprowadź wartość **03** , jeśli użyto szablonu platformy Azure lub zastosowano sekcję wdrażanie ręczne w tym artykule.
   * Wybierz tryb bazy danych/wprowadź indeks [1]: wybierz klawisz ENTER.
   * Wybieranie opcji użycie systemu/wprowadzanie indeksu [4]: wybierz wartość użycie systemu.
   * Wprowadź lokalizację woluminów danych [/hana/data/HN1]: Wybierz ENTER.
   * Wprowadź lokalizację woluminów dziennika [/hana/log/HN1]: Wybierz ENTER.
   * Ograniczyć maksymalną alokację pamięci? [n]: wybierz klawisz ENTER.
   * Wprowadź nazwę hosta certyfikatu dla hosta "..." [...]: Wybierz klawisz ENTER.
   * Wprowadź hasło użytkownika agenta hosta SAP (sapadm): wprowadź hasło użytkownika agenta hosta.
   * Potwierdź hasło użytkownika agenta hosta SAP (sapadm): Wprowadź ponownie hasło użytkownika agenta hosta, aby je potwierdzić.
   * Wprowadź hasło administratora systemu (hdbadm): wprowadź hasło administratora systemu.
   * Potwierdź hasło administratora systemu (hdbadm): Wprowadź ponownie hasło administratora systemu, aby potwierdzić.
   * Wprowadź katalog macierzysty administratora systemu [/usr/sap/HN1/home]: wybierz klawisz ENTER.
   * Wprowadź powłokę logowania administratora systemu [/bin/sh]: wybierz klawisz ENTER.
   * Wprowadź identyfikator użytkownika administratora systemu [1001]: wybierz klawisz ENTER.
   * Wprowadź identyfikator grupy użytkowników (sapsys) [79]: wybierz klawisz ENTER.
   * Wprowadź hasło użytkownika bazy danych (SYSTEM): wprowadź hasło użytkownika bazy danych.
   * Potwierdź hasło użytkownika bazy danych (SYSTEM): Wprowadź ponownie hasło użytkownika bazy danych, aby je potwierdzić.
   * Czy uruchomić ponownie system po ponownym uruchomieniu komputera? [n]: wybierz klawisz ENTER.
   * Czy chcesz kontynuować? (t/n): Sprawdź poprawność podsumowania. Wprowadź **y** , aby kontynuować.

1. **[A]** Uaktualnij agenta hosta SAP.

   Pobierz najnowszą archiwum agenta hosta SAP z [centrum oprogramowania SAP][sap-swcenter] i uruchom następujące polecenie, aby uaktualnić agenta. Zastąp ścieżkę do archiwum, aby wskazywało pobrany plik:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurowanie replikacji systemu SAP HANA 2,0

W procedurach przedstawionych w tej sekcji są używane następujące prefiksy:

* **[A]**: krok ma zastosowanie do wszystkich węzłów.
* **[1]**: krok ma zastosowanie tylko do węzła 1.
* **[2]**: krok ma zastosowanie tylko do węzła 2 klastra Pacemaker.

1. **[1]** Utwórz bazę danych dzierżawy.

   Jeśli używasz SAP HANA 2,0 lub MDC, Utwórz bazę danych dzierżawy dla systemu SAP NetWeaver. Zastąp **NW1** identyfikatorem SID systemu SAP.

   Wykonaj następujące polecenie jako <hanasid \> adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Skonfiguruj replikację systemu w pierwszym węźle:

   Wykonaj kopię zapasową baz danych jako <hanasid \> adm:

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

1. **[2]** Skonfiguruj replikację systemu w drugim węźle:
    
   Zarejestruj drugi węzeł, aby rozpocząć replikację systemu. Uruchom następujące polecenie jako <hanasid \> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurowanie replikacji systemu SAP HANA 1,0

W procedurach przedstawionych w tej sekcji są używane następujące prefiksy:

* **[A]**: krok ma zastosowanie do wszystkich węzłów.
* **[1]**: krok ma zastosowanie tylko do węzła 1.
* **[2]**: krok ma zastosowanie tylko do węzła 2 klastra Pacemaker.

1. **[1]** Utwórz wymaganych użytkowników.

   Uruchom następujące polecenie jako element główny. Pamiętaj, aby zastąpić ciągi pogrubione (identyfikator systemu HANA **HN1** i wystąpienie numer **03**) wartościami instalacji SAP HANA:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** Utwórz wpis magazynu kluczy.

   Uruchom następujące polecenie jako element główny, aby utworzyć nowy wpis magazynu kluczy:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** wykonaj kopię zapasową bazy danych.

   Wykonaj kopię zapasową baz danych jako rdzeń:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Jeśli używasz instalacji z wieloma dzierżawcami, Utwórz kopię zapasową bazy danych dzierżawcy:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Skonfiguruj replikację systemu na pierwszym węźle.

   Utwórz lokację główną jako <hanasid \> adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Skonfiguruj replikację systemu w węźle pomocniczym.

   Zarejestruj lokację dodatkową jako <hanasid \> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
   </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Tworzenie zasobów klastra SAP HANA

Najpierw utwórz topologię HANA. Uruchom następujące polecenia na jednym z węzłów klastra Pacemaker:

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
> Ostatnie testy ujawniły sytuacje, w których netcat przestaje odpowiadać na żądania z powodu zaległości i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań modułu równoważenia obciążenia platformy Azure, a przestawny adres IP stał się niedostępny.  
> W przypadku istniejących klastrów Pacemaker zalecamy w przeszłości zastępowanie netcat socat. Obecnie zalecamy korzystanie z agenta zasobów Azure-lb, który jest częścią agentów zasobów pakietu, z następującymi wymaganiami dotyczącymi wersji pakietu:
> - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej równa "Resource-Agents-4.3.018. a7fb5035-3.30.1.  
> - W przypadku programu SLES 15/15 z dodatkiem SP1 wersja musi być co najmniej równa "Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  
>
> Należy pamiętać, że zmiana będzie wymagała krótkiego przestoju.  
> W przypadku istniejących klastrów Pacemaker, jeśli konfiguracja została już zmieniona tak, aby korzystała z socat zgodnie z opisem w obszarze [Ograniczanie poziomu wykrywania w usłudze azure Load-Balancer](https://www.suse.com/support/kb/doc/?id=7024128), nie ma potrzeby natychmiastowego przełączenia do agenta zasobów platformy Azure.


> [!NOTE]
> Ten artykuł zawiera odwołania do *głównego* terminu i serwera *podrzędnego*, warunki, które nie są już używane przez firmę Microsoft. Po usunięciu tych warunków z oprogramowania usuniemy je z tego artykułu.

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

Upewnij się, że klaster ma stan OK i że wszystkie zasoby są uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

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

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Konfiguruj replikację systemu z włączoną funkcją Active/odczyt w klastrze Pacemaker

Począwszy od SAP HANA 2,0 SPS 01 SAP zezwala na konfigurację typu Active/z obsługą odczytu dla replikacji systemu SAP HANA, w przypadku których systemy pomocnicze replikacji systemu SAP HANA mogą być używane aktywnie w przypadku obciążeń odczytu intensywnie. Aby zapewnić obsługę takich ustawień w klastrze, wymagany jest drugi wirtualny adres IP, który umożliwia klientom dostęp do pomocniczej bazy danych SAP HANA z włączoną obsługą odczytu. Aby upewnić się, że lokacja replikacji pomocniczej nadal będzie dostępna po przejęciu przejęcia, klaster musi przenieść wirtualny adres IP wokół pomocniczego zasobu SAPHana.

W tej sekcji opisano dodatkowe kroki, które są wymagane do zarządzania replikacją systemu w ramach usługi HANA Active/Read, w klastrze o wysokiej dostępności z drugim wirtualnym adresem IP.    
Przed kontynuowaniem upewnij się, że masz w pełni skonfigurowany klaster o wysokiej dostępności SUSE zarządzania SAP HANA bazą danych, zgodnie z opisem w powyższych segmentach dokumentacji.  

![SAP HANA wysoka dostępność z użyciem dodatkowej z możliwością odczytu](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Dodatkowa konfiguracja modułu równoważenia obciążenia platformy Azure na potrzeby instalacji Active/z obsługą odczytu

Aby kontynuować dodatkowe kroki dotyczące aprowizacji drugiego wirtualnego adresu IP, upewnij się, że skonfigurowano Azure Load Balancer zgodnie z opisem w sekcji [wdrażanie ręczne](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#manual-deployment) .

1. W przypadku usługi równoważenia obciążenia w **warstwie Standardowa** wykonaj dodatkowe czynności opisane poniżej w tym samym module równoważenia obciążenia, który został utworzony we wcześniejszej sekcji.

   a. Utwórz drugą pulę adresów IP frontonu: 

   - Otwórz moduł równoważenia obciążenia, wybierz pozycję **Pula adresów IP frontonu**, a następnie wybierz pozycję **Dodaj**.
   - Wprowadź nazwę drugiej puli adresów IP frontonu (na przykład **Hana-secondaryIP**).
   - Ustaw **przypisanie** na **static** i wprowadź adres IP (na przykład **10.0.0.14**).
   - Wybierz przycisk **OK**.
   - Po utworzeniu nowej puli adresów IP frontonu Zanotuj adres IP frontonu.

   b. Następnie utwórz sondę kondycji:

   - Otwórz moduł równoważenia obciążenia, wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
   - Wprowadź nazwę nowej sondy kondycji (na przykład **Hana-secondaryhp**).
   - Wybierz pozycję **TCP** jako protokół i port **62603**. Pozostaw wartość **interwału** ustawioną na 5, a wartość **progowa złej kondycji** równa 2.
   - Wybierz przycisk **OK**.

   c. Następnie utwórz reguły równoważenia obciążenia:

   - Otwórz moduł równoważenia obciążenia, wybierz pozycję **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj**.
   - Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **Hana-secondarylb**).
   - Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **Hana-secondaryIP**, **Hana — zaplecze** i **Hana-secondaryhp**).
   - Wybierz pozycję **porty ha**.
   - Zwiększ **limit czasu bezczynności** do 30 minut.
   - Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.
   - Wybierz przycisk **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>Konfiguruj replikację systemu w ramach usługi HANA Active/Read Enabled

Procedurę konfigurowania replikacji systemu HANA opisano w sekcji [konfigurowanie SAP HANA 2,0 replikacji systemowej](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability#configure-sap-hana-20-system-replication) . Jeśli wdrażasz scenariusz pomocniczy z włączoną obsługą odczytu, podczas konfigurowania replikacji systemu w drugim węźle wykonaj następujące polecenie jako **hanasid** adm:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Dodawanie zasobu dodatkowego wirtualnego adresu IP dla instalacji z obsługą aktywnego/z możliwością odczytu

Drugi wirtualny adres IP i odpowiednie ograniczenie między lokalizacjami można skonfigurować przy użyciu następujących poleceń:

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
Upewnij się, że klaster ma stan OK i że wszystkie zasoby są uruchomione. Drugi wirtualny adres IP zostanie uruchomiony w lokacji dodatkowej wraz z zasobem pomocniczym SAPHana.

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

W następnej sekcji można znaleźć typowy zestaw testów trybu failover do wykonania.

Zapoznaj się z drugim zachowaniem wirtualnego adresu IP, podczas testowania klastra HANA skonfigurowanego przy użyciu dodatkowej z włączoną funkcją odczytu:

1. Podczas migracji **SAPHana_HN1_HDB03** zasobu klastra do **HN1-dB-1** drugi wirtualny adres IP zostanie przeniesiony na inny serwer **HN1-DB-0**. Jeśli skonfigurowano AUTOMATED_REGISTER = "false" i replikacja systemu HANA nie jest automatycznie zarejestrowana, drugi wirtualny adres IP zostanie uruchomiony w **hn1-DB-0,** ponieważ serwer jest dostępny i usługi klastra są w trybie online.  

2. Podczas testowania awarii serwera, drugie wirtualne zasoby IP (**rsc_secip_HN1_HDB03**) i zasób portu usługi równoważenia obciążenia platformy Azure (**rsc_secnc_HN1_HDB03**) będą uruchamiane na serwerze podstawowym wraz z głównymi zasobami wirtualnego adresu IP. Gdy serwer pomocniczy nie działa, aplikacje połączone z bazą danych HANA z obsługą odczytu będą łączyć się z podstawową bazą danych platformy HANA. Zachowanie jest oczekiwane, ponieważ nie ma potrzeby, aby aplikacje połączone z bazą danych HANA z włączoną funkcją odczytu były niedostępne, gdy serwer pomocniczy jest niedostępny.
  
3. Gdy serwer pomocniczy jest dostępny i usługi klastra są w trybie online, drugi wirtualny adres IP i zasoby portów zostaną automatycznie przeniesione na serwer pomocniczy, nawet jeśli replikacja systemu HANA nie zostanie zarejestrowana jako dodatkowa. Przed uruchomieniem usług klastrowania na tym serwerze należy się upewnić, że baza danych dodatkowej platformy HANA została zarejestrowana jako przeczytana. Zasób klastra wystąpienia HANA można skonfigurować tak, aby automatycznie rejestrował pomocniczy przez ustawienie parametru AUTOMATED_REGISTER = true.       

4. Podczas przełączania do trybu failover i powrotu istniejące połączenia dla aplikacji przy użyciu drugiego wirtualnego adresu IP do łączenia się z bazą danych HANA mogą zostać przerwane.  

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano, jak można testować konfigurację. Każdy test zakłada, że jest to katalog główny, a główny SAP HANA jest uruchomiony na maszynie wirtualnej **hn1-DB-0** .

### <a name="test-the-migration"></a>Testowanie migracji

Przed rozpoczęciem testu upewnij się, że Pacemaker nie ma żadnej akcji zakończonej niepowodzeniem (za pośrednictwem crm_mon-r), nie ma żadnych nieoczekiwanych ograniczeń lokalizacji (na przykład pozostałości testu migracji) i że platforma HANA jest stanem synchronizacji, na przykład z SAPHanaSR-showAttr:

<pre><code>hn1-db-0:~ # SAPHanaSR-showAttr

Global cib-time
--------------------------------
global Mon Aug 13 11:26:04 2018

Hosts    clone_state lpa_hn1_lpt node_state op_mode   remoteHost    roles                            score site  srmode sync_state version                vhost
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------
hn1-db-0 PROMOTED    1534159564  online     logreplay nws-hana-vm-1 4:P:master1:master:worker:master 150   SITE1 sync   PRIM       2.00.030.00.1522209842 nws-hana-vm-0
hn1-db-1 DEMOTED     30          online     logreplay nws-hana-vm-0 4:S:master1:master:worker:master 100   SITE2 sync   SOK        2.00.030.00.1522209842 nws-hana-vm-1
</code></pre>

Można zmigrować węzeł główny SAP HANA, wykonując następujące polecenie:

<pre><code>crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

W przypadku ustawienia `AUTOMATED_REGISTER="false"` Ta sekwencja poleceń powinna migrować węzeł główny SAP HANA i grupę zawierającą wirtualny adres IP do hn1-dB-1.

Po zakończeniu migracji crm_mon-r dane wyjściowe wyglądają następująco

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

Nie można uruchomić zasobu SAP HANA w hn1-DB-0 jako pomocniczy. W takim przypadku należy skonfigurować wystąpienie HANA jako pomocnicze, wykonując następujące polecenie:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>
</code></pre>

Migracja tworzy ograniczenia lokalizacji, które muszą zostać usunięte ponownie:

<pre><code># Switch back to root and clean up the failed state
exit
hn1-db-0:~ # crm resource unmigrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
</code></pre>

Konieczne jest również oczyszczenie stanu zasobu węzła pomocniczego:

<pre><code>hn1-db-0:~ # crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

Monitoruj stan zasobu HANA przy użyciu crm_mon-r. Po uruchomieniu platformy HANA w hn1-DB-0 dane wyjściowe powinny wyglądać następująco

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

### <a name="test-the-azure-fencing-agent-not-sbd"></a>Testowanie Agenta ogrodzenia platformy Azure (nie SBD)

Możesz przetestować instalację Agenta ogrodzenia platformy Azure, wyłączając interfejs sieciowy w węźle hn1-DB-0:

<pre><code>sudo ifdown eth0
</code></pre>

Maszyna wirtualna powinna być teraz ponownie uruchamiana lub zatrzymywana w zależności od konfiguracji klastra.
Jeśli `stonith-action` ustawienie zostanie wyłączone, maszyna wirtualna zostanie zatrzymana, a zasoby zostaną zmigrowane do uruchomionej maszyny wirtualnej.

Po ponownym uruchomieniu maszyny wirtualnej nie można uruchomić zasobu SAP HANA jako pomocniczego, jeśli został ustawiony `AUTOMATED_REGISTER="false"` . W takim przypadku należy skonfigurować wystąpienie HANA jako pomocnicze, wykonując następujące polecenie:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

### <a name="test-sbd-fencing"></a>Ogrodzenie SBD testu

Możesz przetestować konfigurację SBD, zabijając proces Inquisitor.

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

Węzeł klastra hn1-DB-0 powinien zostać ponownie uruchomiony. Usługa Pacemaker może nie rozpocząć pracy później. Upewnij się, że została uruchomiona ponownie.

### <a name="test-a-manual-failover"></a>Testowanie ręcznego przełączania do trybu failover

Ręczne przełączanie w tryb failover można przetestować, zatrzymując `pacemaker` usługę w węźle hn1-DB-0:

<pre><code>service pacemaker stop
</code></pre>

Po przejściu w tryb failover możesz ponownie uruchomić usługę. Jeśli ustawisz `AUTOMATED_REGISTER="false"` , zasób SAP HANA w węźle hn1-DB-0 nie zostanie uruchomiony jako pomocniczy. W takim przypadku należy skonfigurować wystąpienie HANA jako pomocnicze, wykonując następujące polecenie:

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
> Upewnij się, że wybrany system operacyjny to SAP Certified dla SAP HANA na określonych typach maszyn wirtualnych, których używasz. Listę SAP HANA certyfikowanych typów maszyn wirtualnych i wydań systemu operacyjnego dla tych systemów można wyszukać w [SAP HANA certyfikowanych platformach IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Pamiętaj, aby kliknąć na liście szczegóły typu maszyny wirtualnej, aby uzyskać pełną listę SAP HANA obsługiwanych wersji systemu operacyjnego dla określonego typu maszyny wirtualnej

Uruchom wszystkie przypadki testowe, które są wymienione w SAP HANA scenariuszu zoptymalizowane pod kątem wydajności SR lub SAP HANA Przewodnik po optymalizacji kosztów SR, w zależności od przypadku użycia. Przewodniki można znaleźć na [stronie SLES for SAP Best Practices][sles-for-sap-bp].

Poniższe testy są kopią opisów testu SAP HANA scenariusza zoptymalizowane pod kątem wydajności SR SUSE Linux Enterprise Server dla programu SAP Applications 12 SP1 Guide. Aby uzyskać aktualną wersję, należy również zapoznać się z przewodnikiem. Zawsze upewnij się, że przed rozpoczęciem testu HANA jest zsynchronizowany, a także upewnij się, że konfiguracja Pacemaker jest poprawna.

W poniższych opisach testów przyjęto, że PREFER_SITE_TAKEOVER = "true" i AUTOMATED_REGISTER = "false".
Uwaga: następujące testy zostały zaprojektowane tak, aby były uruchamiane w sekwencji i zależały od stanu zakończenia poprzedzających testów.

1. TEST 1: ZATRZYMAJ PODSTAWOWĄ BAZĘ DANYCH W WĘŹLE 1

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

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-DB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker powinny wykrywać zatrzymane wystąpienie platformy HANA i przełączać w tryb failover do innego węzła. Po zakończeniu pracy w trybie failover wystąpienie platformy HANA w węźle hn1-DB-0 zostanie zatrzymane, ponieważ usługa Pacemaker nie zarejestruje go automatycznie w ramach dodatkowej platformy HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-DB-0 jako pomocniczy i wyczyść zasób zakończony niepowodzeniem.

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

1. TEST 2: ZATRZYMAJ PODSTAWOWĄ BAZĘ DANYCH W WĘŹLE 2

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

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-dB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker powinny wykrywać zatrzymane wystąpienie platformy HANA i przełączać w tryb failover do innego węzła. Po zakończeniu pracy w trybie failover wystąpienie platformy HANA w węźle hn1-dB-1 zostanie zatrzymane, ponieważ program Pacemaker nie zarejestruje go automatycznie w ramach dodatkowej platformy HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-dB-1 jako pomocniczy i oczyść zasób, którego dotyczy błąd.

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

1. TEST 3: PODSTAWOWA BAZA DANYCH AWARII W WĘŹLE

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

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-DB-0:

   <pre><code>hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>
   
   Pacemaker powinny wykrywać zabite wystąpienie platformy HANA i przełączenie w tryb failover do innego węzła. Po zakończeniu pracy w trybie failover wystąpienie platformy HANA w węźle hn1-DB-0 zostanie zatrzymane, ponieważ usługa Pacemaker nie zarejestruje go automatycznie w ramach dodatkowej platformy HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-DB-0 jako pomocniczy i wyczyść zasób zakończony niepowodzeniem.

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

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-dB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker powinny wykrywać zabite wystąpienie platformy HANA i przełączenie w tryb failover do innego węzła. Po zakończeniu pracy w trybie failover wystąpienie platformy HANA w węźle hn1-dB-1 zostanie zatrzymane, ponieważ program Pacemaker nie zarejestruje go automatycznie w ramach dodatkowej platformy HANA.

   Uruchom następujące polecenia, aby zarejestrować węzeł hn1-dB-1 jako pomocniczy i oczyść zasób, którego dotyczy błąd.

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

1. TEST 5: WĘZEŁ LOKACJI GŁÓWNEJ AWARII (WĘZEŁ 1)

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

   Uruchom następujące polecenia jako główne w węźle hn1-DB-0:

   <pre><code>hn1-db-0:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker powinien wykryć zamknięty węzeł klastra i obogrodzenia węzeł. Gdy węzeł zostanie ogrodzony, Pacemaker wyzwoli przejęcie wystąpienia HANA. Gdy ogrodzony węzeł zostanie ponownie uruchomiony, Pacemaker nie zostanie uruchomiony automatycznie.

   Uruchom następujące polecenia, aby rozpocząć Pacemaker, wyczyść komunikaty SBD dla węzła hn1-DB-0, zarejestruj węzeł hn1-DB-0 jako pomocniczy i wyczyść zasób, który uległ awarii.

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

1. TEST 6: WĘZEŁ LOKACJI DODATKOWEJ AWARII (WĘZEŁ 2)

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

   Uruchom następujące polecenia jako główne w węźle hn1-dB-1:

   <pre><code>hn1-db-1:~ #  echo 'b' > /proc/sysrq-trigger
   </code></pre>

   Pacemaker powinien wykryć zamknięty węzeł klastra i obogrodzenia węzeł. Gdy węzeł zostanie ogrodzony, Pacemaker wyzwoli przejęcie wystąpienia HANA. Gdy ogrodzony węzeł zostanie ponownie uruchomiony, Pacemaker nie zostanie uruchomiony automatycznie.

   Uruchom następujące polecenia, aby rozpocząć Pacemaker, wyczyścić komunikaty SBD dla węzła hn1-dB-1, zarejestrować węzeł hn1-dB-1 jako pomocniczy i oczyścić zasób, który uległ awarii.

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

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-dB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB stop
   </code></pre>

   Pacemaker wykryje zatrzymane wystąpienie HANA i oznaczy zasób jako zakończony niepowodzeniem w węźle hn1-dB-1. Pacemaker powinno automatycznie ponownie uruchomić wystąpienie platformy HANA. Uruchom następujące polecenie, aby oczyścić stan niepowodzenia.

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

   Uruchom następujące polecenia jako <hanasid \> adm w węźle hn1-dB-1:

   <pre><code>hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> HDB kill-9
   </code></pre>

   Pacemaker wykryje przerwane wystąpienie platformy HANA i oznaczy zasób jako niepowodzenie w węźle hn1-dB-1. Uruchom następujące polecenie, aby oczyścić stan niepowodzenia. Pacemaker powinien następnie automatycznie ponownie uruchomić wystąpienie platformy HANA.

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

1. TEST 9: WĘZEŁ LOKACJI DODATKOWEJ AWARII (WĘZEŁ 2) Z URUCHOMIONĄ POMOCNICZĄ BAZĄ DANYCH HANA

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

   Uruchom następujące polecenia jako główne w węźle hn1-dB-1:

   <pre><code>hn1-db-1:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pacemaker powinien wykryć zamknięty węzeł klastra i obogrodzenia węzeł. Gdy ogrodzony węzeł zostanie ponownie uruchomiony, Pacemaker nie zostanie uruchomiony automatycznie.

   Uruchom następujące polecenia, aby rozpocząć Pacemaker, wyczyść komunikaty SBD dla węzła hn1-dB-1 i oczyść zasób zakończony niepowodzeniem.

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

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
