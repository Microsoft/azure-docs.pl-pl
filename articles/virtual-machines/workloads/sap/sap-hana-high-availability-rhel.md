---
title: Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure w systemie RHEL | Microsoft Docs
description: Zapewnienie wysokiej dostępności SAP HANA na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 277ed8ad5f9888daa911cb3b5c7dcf00fd285bf4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489160"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Wysoka dostępność SAP HANA na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

W przypadku programowania lokalnego można użyć replikacji systemu HANA lub użyć magazynu udostępnionego do ustanowienia wysokiej dostępności dla SAP HANA.
Na platformie Azure Virtual Machines funkcja replikacji systemu HANA na platformę Azure jest obecnie jedyną obsługiwaną funkcją wysokiej dostępności.
SAP HANA replikacja składa się z jednego węzła podstawowego i co najmniej jednego węzła pomocniczego. Zmiany danych w węźle podstawowym są replikowane do węzła pomocniczego synchronicznie lub asynchronicznie.

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych, instalowania platformy klastra oraz instalowania i konfigurowania replikacji systemu SAP HANA.
W przykładowych konfiguracjach, poleceniach instalacji, numer wystąpienia nr **03** i identyfikator systemu Hana **HN1** są używane.

Przeczytaj najpierw następujące informacje i dokumenty SAP:

* Nota SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane programy SAP i system operacyjny oraz kombinacje baz danych.
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure.
* Uwaga dotycząca protokołu SAP [2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2002167] zalecane ustawienia systemu operacyjnego dla Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2009879] SAP HANA wytycznych dotyczących Red Hat Enterprise Linux
* Uwaga dotycząca oprogramowania SAP [2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca programu SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux (ten artykuł)][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Replikacja systemu SAP HANA w klastrze Pacemaker](https://access.redhat.com/articles/3004101)
* Ogólna dokumentacja RHEL
  * [Omówienie Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Informacje o wysokiej dostępności Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Dokumentacja usługi RHEL specyficznej dla platformy Azure:
  * [Zasady obsługi klastrów RHEL o wysokiej dostępności — Microsoft Azure Virtual Machines jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i Konfigurowanie Red Hat Enterprise Linux 7,4 (i nowszych) High-Availability klastra na Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Zainstaluj SAP HANA na Red Hat Enterprise Linux do użycia w Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Omówienie

Aby zapewnić wysoką dostępność, SAP HANA jest instalowany na dwóch maszynach wirtualnych. Dane są replikowane przy użyciu replikacji systemu HANA.

![Przegląd wysokiej dostępności SAP HANA](./media/sap-hana-high-availability-rhel/ha-hana.png)

Konfiguracja replikacji systemu SAP HANA używa dedykowanej wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia:

* Konfiguracja frontonu: adres IP 10.0.0.13 dla hn1-DB
* Konfiguracja zaplecza: połączono z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią replikacji systemu HANA
* Port sondy: Port 62503
* Reguły równoważenia obciążenia: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Wdrażanie dla systemu Linux

Portal Azure Marketplace zawiera obraz dla Red Hat Enterprise Linux 7,4 dla SAP HANA, który służy do wdrażania nowych maszyn wirtualnych.

### <a name="deploy-with-a-template"></a>Wdrażanie za pomocą szablonu

Możesz użyć jednego z szablonów szybkiego startu, które znajdują się w serwisie GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, moduł równoważenia obciążenia, zestaw dostępności i tak dalej.
Aby wdrożyć szablon, wykonaj następujące kroki:

1. Otwórz [szablon bazy danych][template-multisid-db] na Azure Portal.
1. Wprowadź następujące parametry:
    * **Identyfikator systemu SAP**: Wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla wdrożonych zasobów.
    * **Typ systemu operacyjnego**: Wybierz jedną z dystrybucji Linux. Na potrzeby tego przykładu wybierz pozycję **RHEL 7**.
    * **Typ bazy danych**: wybierz pozycję **Hana**.
    * **Rozmiar systemu SAP**: wprowadź liczbę punktów SAP, które ma dostarczyć nowy system. Jeśli nie masz pewności, ile punktów SAP wymaga system, skontaktuj się z partnerem technologii SAP lub integratorem systemu.
    * **Dostępność systemu**: Wybierz **ha**.
    * **Nazwa użytkownika administratora, hasło administratora lub klucz SSH**: tworzony jest nowy użytkownik, którego można użyć do zalogowania się na komputerze.
    * **Identyfikator podsieci**: Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Identyfikator zazwyczaj wygląda podobnie do **/subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /Subnets/ \<subnet name>**. Pozostaw puste, jeśli chcesz utworzyć nową sieć wirtualną

### <a name="manual-deployment"></a>Wdrożenie ręczne

1. Utwórz grupę zasobów.
1. Utwórz sieć wirtualną.
1. Utwórz zestaw dostępności.  
   Ustaw maksymalną domenę aktualizacji.
1. Utwórz moduł równoważenia obciążenia (wewnętrzny). Zalecamy użycie [standardowej usługi równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md).
   * Wybierz sieć wirtualną utworzoną w kroku 2.
1. Utwórz maszynę wirtualną 1.  
   Użyj co najmniej Red Hat Enterprise Linux 7,4 dla SAP HANA. W tym przykładzie zastosowano Red Hat Enterprise Linux 7,4 dla SAP HANA obrazu <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Wybierz zestaw dostępności utworzony w kroku 3.
1. Utwórz maszynę wirtualną 2.  
   Użyj co najmniej Red Hat Enterprise Linux 7,4 dla SAP HANA. W tym przykładzie zastosowano Red Hat Enterprise Linux 7,4 dla SAP HANA obrazu <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> Wybierz zestaw dostępności utworzony w kroku 3.
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
   1. Skonfiguruj moduł równoważenia obciążenia. Najpierw Utwórz pulę adresów IP frontonu:

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

## <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

W procedurach przedstawionych w tej sekcji są używane następujące prefiksy:

* **[A]**: krok ma zastosowanie do wszystkich węzłów.
* **[1]**: krok ma zastosowanie tylko do węzła 1.
* **[2]**: krok ma zastosowanie tylko do węzła 2 klastra Pacemaker.

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

1. **[A]** RHEL for Hana — konfiguracja

   Skonfiguruj RHEL zgodnie z opisem w temacie <https://access.redhat.com/solutions/2447641> i w następujących uwagach dotyczących oprogramowania SAP:  
   - [2292690 — SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 — SAP HANA DB: zalecane ustawienia systemu operacyjnego dla RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 — Linux: uruchamianie aplikacji SAP skompilowanych z usługą w zatoce 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 — Linux: uruchamianie aplikacji SAP skompilowanych z usługą w zatoce 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 — Linux: uruchamianie aplikacji SAP skompilowanych z usługą w zatoce 9. x](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** Zainstaluj SAP HANA

   Aby zainstalować replikację systemu SAP HANA, wykonaj następujące czynności <https://access.redhat.com/articles/3004101> .

   * Uruchom program **hdblcm** z dysku DVD platformy Hana. W wierszu polecenia wprowadź następujące wartości:
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

1. **[A]** Konfigurowanie zapory

   Utwórz regułę zapory dla portu sondowania modułu równoważenia obciążenia platformy Azure.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurowanie replikacji systemu SAP HANA 2,0

W procedurach przedstawionych w tej sekcji są używane następujące prefiksy:

* **[A]**: krok ma zastosowanie do wszystkich węzłów.
* **[1]**: krok ma zastosowanie tylko do węzła 1.
* **[2]**: krok ma zastosowanie tylko do węzła 2 klastra Pacemaker.

1. **[A]** Konfigurowanie zapory

   Utwórz reguły zapory, aby umożliwić replikację systemu HANA i ruch klientów. Wymagane porty są wymienione w [portach TCP/IP wszystkich produktów SAP](https://help.sap.com/viewer/ports). Następujące polecenia są tylko przykładem, aby zezwolić na replikację systemu HANA 2,0 i ruch klienta do bazy danych SYSTEMDB, HN1 i NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** Utwórz bazę danych dzierżawy.

   Jeśli używasz SAP HANA 2,0 lub MDC, Utwórz bazę danych dzierżawy dla systemu SAP NetWeaver. Zastąp **NW1** identyfikatorem SID systemu SAP.

   Uruchom jako <hanasid \> adm następujące polecenie:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** Skonfiguruj replikację systemu w pierwszym węźle:

   Utwórz kopię zapasową baz danych jako <hanasid \> adm:

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

1. **[1]** Sprawdź stan replikacji

   Sprawdź stan replikacji i poczekaj na zsynchronizowanie wszystkich baz danych. Jeśli stan jest nieznany, sprawdź ustawienia zapory.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurowanie replikacji systemu SAP HANA 1,0

W procedurach przedstawionych w tej sekcji są używane następujące prefiksy:

* **[A]**: krok ma zastosowanie do wszystkich węzłów.
* **[1]**: krok ma zastosowanie tylko do węzła 1.
* **[2]**: krok ma zastosowanie tylko do węzła 2 klastra Pacemaker.

1. **[A]** Konfigurowanie zapory

   Utwórz reguły zapory, aby umożliwić replikację systemu HANA i ruch klientów. Wymagane porty są wymienione w [portach TCP/IP wszystkich produktów SAP](https://help.sap.com/viewer/ports). Poniższe polecenia są tylko przykładem umożliwiającym replikację systemu HANA 2,0. Dostosuj je do instalacji SAP HANA 1,0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w temacie [Konfigurowanie Pacemaker Red Hat Enterprise Linux na platformie Azure](high-availability-guide-rhel-pacemaker.md) , aby utworzyć podstawowy klaster Pacemaker dla tego serwera platformy Hana.

## <a name="create-sap-hana-cluster-resources"></a>Tworzenie zasobów klastra SAP HANA

Zainstaluj agentów zasobów SAP HANA na **wszystkich węzłach**. Upewnij się, że włączono repozytorium zawierające pakiet. Nie musisz włączać dodatkowych repozytoriów, jeśli używany jest obraz RHEL 8. x z obsługą wysokiej dostępności.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Następnie utwórz topologię platformy HANA. Uruchom następujące polecenia na jednym z węzłów klastra Pacemaker:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Następnie utwórz zasoby platformy HANA.

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku *podrzędnego*, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

Jeśli tworzysz klaster na **RHEL 7. x**, użyj następujących poleceń:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Jeśli tworzysz klaster na **RHEL 8. x**, użyj następujących poleceń:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
promotable meta notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-clone symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-clone 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Upewnij się, że klaster ma stan OK i że wszystkie zasoby są uruchomione. Nie ma znaczenia, w którym węźle są uruchomione zasoby.

> [!NOTE]
> Przekroczenia limitów czasu w powyższej konfiguracji są tylko przykłady i konieczne może być dostosowanie do określonej konfiguracji platformy HANA. Na przykład może być konieczne zwiększenie limitu czasu uruchamiania, Jeśli uruchomienie SAP HANA bazy danych trwa dłużej.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano, jak można testować konfigurację. Przed rozpoczęciem testu upewnij się, że Pacemaker nie ma żadnej akcji zakończonej niepowodzeniem (za pośrednictwem stanu komputerów), nie ma żadnych nieoczekiwanych ograniczeń lokalizacji (na przykład pozostałości testu migracji) i że HANA jest stan synchronizacji, na przykład z systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>Testowanie migracji

Stan zasobu przed rozpoczęciem testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Można zmigrować węzeł główny SAP HANA, wykonując następujące polecenie:

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

W przypadku ustawienia `AUTOMATED_REGISTER="false"` tego polecenia należy zmigrować węzeł główny SAP HANA i grupę zawierającą wirtualny adres IP do hn1-dB-1.

Po zakończeniu migracji wynik "sudo PCs status" będzie wyglądać następująco

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Zasób SAP HANA w hn1-DB-0 jest zatrzymany. W takim przypadku należy skonfigurować wystąpienie HANA jako pomocnicze, wykonując następujące polecenie:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Migracja tworzy ograniczenia lokalizacji, które muszą zostać usunięte ponownie:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitoruj stan zasobu HANA przy użyciu "stan komputerów". Po uruchomieniu platformy HANA w hn1-DB-0 dane wyjściowe powinny wyglądać następująco

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testowanie usługi Azure ogrodzeniing Agent

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku *podrzędnego*, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.  

Stan zasobu przed rozpoczęciem testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Możesz przetestować instalację Agenta ogrodzenia platformy Azure, wyłączając interfejs sieciowy w węźle, w którym SAP HANA jest uruchomiony jako główny.
Zapoznaj się z [artykułem 79523 w bazie wiedzy Red Hat](https://access.redhat.com/solutions/79523) , aby uzyskać opis sposobu symulowania awarii sieci. W tym przykładzie używamy skryptu net_breaker, aby zablokować dostęp do sieci.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Maszyna wirtualna powinna być teraz ponownie uruchamiana lub zatrzymywana w zależności od konfiguracji klastra.
Jeśli `stonith-action` ustawienie zostanie wyłączone, maszyna wirtualna zostanie zatrzymana, a zasoby zostaną zmigrowane do uruchomionej maszyny wirtualnej.

Po ponownym uruchomieniu maszyny wirtualnej nie można uruchomić zasobu SAP HANA jako pomocniczego, jeśli został ustawiony `AUTOMATED_REGISTER="false"` . W takim przypadku należy skonfigurować wystąpienie HANA jako pomocnicze, wykonując następujące polecenie:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Stan zasobu po teście:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Testowanie ręcznego przełączania do trybu failover

Stan zasobu przed rozpoczęciem testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Ręczne przełączanie w tryb failover można przetestować, zatrzymując klaster w węźle hn1-DB-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Po przejściu do trybu failover można ponownie uruchomić klaster. Jeśli ustawisz `AUTOMATED_REGISTER="false"` , zasób SAP HANA w węźle hn1-DB-0 nie zostanie uruchomiony jako pomocniczy. W takim przypadku należy skonfigurować wystąpienie HANA jako pomocnicze, wykonując następujące polecenie:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Stan zasobu po teście:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>Testowanie ręcznego przełączania do trybu failover

Stan zasobu przed rozpoczęciem testu:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

Ręczne przełączanie w tryb failover można przetestować, zatrzymując klaster w węźle hn1-DB-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* [SAP HANA konfiguracjami magazynu maszyny wirtualnej](./hana-vm-operations-storage.md)