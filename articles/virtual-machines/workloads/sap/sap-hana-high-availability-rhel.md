---
title: Wysoka dostępność zasobów SAP HANA maszyn wirtualnych platformy Azure w systemie RHEL | Microsoft Docs
description: Ustanów wysoką dostępność SAP HANA na maszynach wirtualnych platformy Azure.
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
ms.openlocfilehash: 3d1b05560c02f3bf4de199a3d5cad48907ee16fb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365811"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Wysoka dostępność maszyn SAP HANA na platformie Azure na platformie Red Hat Enterprise Linux

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

W przypadku tworzenia aplikacji lokalnych można użyć replikacji systemu HANA lub magazynu udostępnionego w celu ustanowienia wysokiej dostępności dla SAP HANA.
Na maszynach wirtualnych platformy Azure replikacja systemu HANA na platformie Azure jest obecnie jedyną obsługiwaną funkcją wysokiej dostępności.
SAP HANA Replikacja składa się z jednego węzła podstawowego i co najmniej jednego węzła pomocniczego. Zmiany danych w węźle podstawowym są replikowane do węzła pomocniczego synchronicznie lub asynchronicznie.

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych, instalowania struktury klastra oraz instalowania i konfigurowania SAP HANA replikacji systemu.
W przykładowych konfiguracjach są używane polecenia instalacji, numer wystąpienia **03** i identyfikator systemu HANA **HN1.**

Najpierw zapoznaj się z następującymi uwagami i dokumentami sap:

* Uwaga SAP [1928533,]która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP.
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure.
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemów operacyjnych i baz danych.
  * Wymagana wersja jądra SAP dla systemów Windows i Linux na Microsoft Azure.
* Uwaga sap [2015553] zawiera listę wymagań wstępnych dla wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* Uwaga SAP [2002167] ma zalecane ustawienia systemu operacyjnego dla Red Hat Enterprise Linux
* Uwaga sap [2009879] ma SAP HANA wytyczne dotyczące Red Hat Enterprise Linux
* Uwaga [SAP 2178632 zawiera] szczegółowe informacje o wszystkich metrykach monitorowania zgłoszonych dla oprogramowania SAP na platformie Azure.
* Uwaga SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga [2243692] oprogramowania SAP zawiera informacje o licencjonowaniu SAP w systemie Linux na platformie Azure.
* Uwaga [1999351] oprogramowania SAP zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzonym rozszerzeniem monitorowania platformy Azure dla oprogramowania SAP.
* [Strona WIKI społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zawiera wszystkie wymagane uwagi dotyczące systemu SAP dla systemu Linux.
* [Planowanie i Virtual Machines wdrożenia oprogramowania SAP w systemie Linux na platformie Azure][planning-guide]
* [Wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux (w tym artykule)][deployment-guide]
* [Wdrażanie usługi Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [SAP HANA replikacji systemu w klastrze pacemaker](https://access.redhat.com/articles/3004101)
* Ogólna dokumentacja systemu RHEL
  * [Omówienie Add-On dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administrowanie Add-On wysoką dostępnością](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Informacje o wysokiej Add-On dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Dokumentacja systemu RHEL specyficzna dla platformy Azure:
  * [Zasady obsługi klastrów wysokiej dostępności RHEL — Microsoft Azure Virtual Machines jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i konfigurowanie klastra Red Hat Enterprise Linux 7.4 (lub nowszego) High-Availability na Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Instalowanie SAP HANA na Red Hat Enterprise Linux do użytku w programie Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Omówienie

Aby uzyskać wysoką dostępność, SAP HANA na dwóch maszynach wirtualnych. Dane są replikowane przy użyciu replikacji systemu HANA.

![SAP HANA o wysokiej dostępności](./media/sap-hana-high-availability-rhel/ha-hana.png)

SAP HANA konfiguracji replikacji systemu korzysta z dedykowanej wirtualnej nazwy hosta i wirtualnych adresów IP. Na platformie Azure do korzystania z wirtualnego adresu IP wymagany jest usługa równoważenia obciążenia. Na poniższej liście przedstawiono konfigurację usługi równoważenia obciążenia:

* Konfiguracja frontonu: adres IP 10.0.0.13 dla hn1-db
* Konfiguracja serwera końcowego: połączone z podstawowymi interfejsami sieciowym wszystkich maszyn wirtualnych, które powinny być częścią replikacji systemu HANA
* Port sondy: port 62503
* Reguły równoważenia obciążenia: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Wdrażanie dla systemu Linux

Plik Azure Marketplace obraz dla maszyny wirtualnej w Red Hat Enterprise Linux 7.4 SAP HANA do wdrażania nowych maszyn wirtualnych.

### <a name="deploy-with-a-template"></a>Wdrażanie za pomocą szablonu

Aby wdrożyć wszystkie wymagane zasoby, możesz użyć jednego z szablonów szybkiego startu dostępnych w usłudze GitHub. Szablon wdraża maszyny wirtualne, usługę równoważenia obciążenia, zestaw dostępności i tak dalej.
Aby wdrożyć szablon, wykonaj następujące kroki:

1. Otwórz szablon [bazy danych w][template-multisid-db] Azure Portal.
1. Wprowadź następujące parametry:
    * **Identyfikator systemu SAP:** wprowadź identyfikator systemu SAP systemu SAP, który chcesz zainstalować. Identyfikator jest używany jako prefiks dla wdrażanych zasobów.
    * **Typ systemu operacyjnego:** wybierz jedną z dystrybucji systemu Linux. W tym przykładzie wybierz **pozycję RHEL 7.**
    * **Typ bazy danych:** wybierz **pozycję HANA.**
    * **Sap System Size**(Rozmiar systemu SAP): wprowadź liczbę systemów SAP, które ma udostępnić nowy system. Jeśli nie masz pewności, ilu systemów SAP wymaga system, zapytaj partnera technologii SAP lub integrator systemu.
    * **Dostępność systemu:** wybierz pozycję **HA**.
    * **Nazwa użytkownika administratora, hasło administratora lub klucz SSH:** zostanie utworzony nowy użytkownik, który może służyć do logowania się na maszynie.
    * **Identyfikator podsieci:** jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, do której powinna zostać przypisana maszyna wirtualna, nadaj identyfikator tej konkretnej podsieci. Identyfikator zazwyczaj wygląda **następująco: /subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**. Pozostaw puste, jeśli chcesz utworzyć nową sieć wirtualną

### <a name="manual-deployment"></a>Wdrożenie ręczne

1. Utwórz grupę zasobów.
1. Utwórz sieć wirtualną.
1. Utwórz zestaw dostępności.  
   Ustaw maksymalną domenę aktualizacji.
1. Tworzenie równoważenia obciążenia (wewnętrznego). Zalecamy użycie [standardowego równoważenia obciążenia.](../../../load-balancer/load-balancer-overview.md)
   * Wybierz sieć wirtualną utworzoną w kroku 2.
1. Utwórz maszynę wirtualną 1.  
   Użyj co najmniej Red Hat Enterprise Linux 7.4 dla SAP HANA. W tym przykładzie użyto Red Hat Enterprise Linux 7.4 dla SAP HANA Wybierz zestaw dostępności <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> utworzony w kroku 3.
1. Utwórz maszynę wirtualną 2.  
   Użyj co najmniej Red Hat Enterprise Linux 7.4 dla SAP HANA. W tym przykładzie użyto Red Hat Enterprise Linux 7.4 dla SAP HANA Wybierz zestaw dostępności utworzony <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> w kroku 3.
1. Dodawanie dysków danych.

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w dodatkowej konfiguracji adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz Azure Load balancer Limitations (Ograniczenia [usługi Azure Load Balancer).](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wd wdrażaj drugą kartę sieciową.    

> [!Note]
> Jeśli maszyny wirtualne bez publicznych adresów IP zostaną umieszczone w puli zaplecza wewnętrznego (bez publicznego adresu IP) usługi Load Balancer w witrynie Azure Standard, nie będzie żadnych wychodzących połączeń z Internetem, chyba że zostanie wykonana dodatkowa konfiguracja zezwalania na routing do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz Public [endpoint connectivity for Virtual Machines using Azure usługa Load Balancer w warstwie Standardowa in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md)(Łączność z publicznym punktem końcowym dla klientów korzystających z usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności oprogramowania SAP).  

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
      1. Wybierz **pozycję Dodaj maszynę wirtualną.**
      1. Wybierz pozycję **Maszyna wirtualna**.
      1. Wybierz maszyny wirtualne klastra SAP HANA i ich adresy IP.
      1. Wybierz pozycję **Dodaj**.

   1. Następnie utwórz sondę kondycji:

      1. Otwórz usługę równoważenia obciążenia, wybierz **pozycję Sondy kondycji** i wybierz pozycję **Dodaj.**
      1. Wprowadź nazwę nowej sondy kondycji (na przykład **hana-hp).**
      1. Wybierz **TCP** jako protokół i port 625 **03.** Zachowaj wartość **Interwał** ustawioną na 5, a **wartość** progowa W złej kondycji na 2.
      1. Wybierz przycisk **OK**.

   1. Następnie utwórz reguły równoważenia obciążenia:
   
      1. Otwórz równoważenie obciążenia, wybierz **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
      1. Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **hana-lb).**
      1. Wybierz utworzony wcześniej adres IP frontonia, pulę zaplecza i sondę kondycji (na przykład **hana-frontend,** **hana-backend** i **hana-hp).**
      1. Wybierz pozycję **Porty HA.**
      1. Zwiększ limit **czasu bezczynności do** 30 minut.
      1. Upewnij się, że **włączyć zmienny adres IP**.
      1. Wybierz przycisk **OK**.


1. Alternatywnie, jeśli twój scenariusz decyduje o użyciu podstawowego równoważenia obciążenia, wykonaj następujące kroki konfiguracji:
   1. Skonfiguruj usługę równoważenia obciążenia. Najpierw utwórz pulę adresów IP frontonia:

      1. Otwórz równoważenie obciążenia, wybierz **pulę adresów IP frontendu** i wybierz pozycję **Dodaj.**
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
      1. Zachowaj protokół **ustawiony** na **TCP** i wprowadź port 3 **03** 15.
      1. Zwiększ limit **czasu bezczynności do** 30 minut.
      1. Upewnij się, że **włączyć zmienny adres IP**.
      1. Wybierz przycisk **OK**.
      1. Powtórz te kroki dla portu 3 **03** 17.

   1. W SAP HANA 2.0 utwórz reguły równoważenia obciążenia dla systemowej bazy danych:

      1. Otwórz równoważenie obciążenia, wybierz **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
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
> Nie włączaj znaczników czasu TCP na maszyn wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie znaczników czasu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.ipv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe [informacje, Load Balancer sond kondycji.](../../../load-balancer/load-balancer-custom-probe-overview.md)
> Zobacz też uwaga sap [2382421.](https://launchpad.support.sap.com/#/notes/2382421) 

## <a name="install-sap-hana"></a>Instalowanie platformy SAP HANA

W krokach w tej sekcji są następujące prefiksy:

* **[A]**: Krok ma zastosowanie do wszystkich węzłów.
* **[1]**: Krok dotyczy tylko węzła 1.
* **[2]**: Krok dotyczy tylko węzła 2 klastra Pacemaker.

1. **[A]** Skonfiguruj układ dysku: **Menedżer woluminów logicznych (LVM).**

   Zalecamy używanie lvm woluminów, które przechowują dane i pliki dziennika. W poniższym przykładzie przyjęto założenie, że maszyny wirtualne mają dołączone cztery dyski danych, które są używane do tworzenia dwóch woluminów.

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

   Utwórz woluminy logiczne. Wolumin liniowy jest tworzony w przypadku użycia `lvcreate` bez `-i` przełącznika. Zalecamy utworzenie woluminu rozłożonego w celu lepszej wydajności we/wy i dopasowanie rozmiarów rozłożonych do wartości udokumentowanych w te SAP HANA [konfiguracji magazynu maszyny wirtualnej.](./hana-vm-operations-storage.md) Argument powinien być liczbą podstawowych woluminów fizycznych, a `-i` `-I` argumentem jest rozmiar paska. W tym dokumencie dwa woluminy fizyczne są używane dla woluminu danych, więc `-i` argument przełącznika jest ustawiony na **2**. Rozmiar rozłożony woluminu danych to **256KiB.** Jeden wolumin fizyczny jest używany dla woluminu dziennika, więc nie lub przełączniki `-i` `-I` są jawnie używane dla poleceń woluminu dziennika.  

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

   W przypadku systemów demonstracyjnych pliki danych i dzienników platformy HANA można umieścić na jednym dysku. Utwórz partycję na stronie /dev/disk/azure/scsi1/lun0 i sformatuj ją przy użyciu systemu plików xfs:

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

   Można użyć serwera DNS lub zmodyfikować plik /etc/hosts we wszystkich węzłach. W tym przykładzie pokazano, jak używać pliku /etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze w pliku /etc/hosts. Zmień adres IP i nazwę hosta, aby dopasować je do środowiska:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL dla konfiguracji platformy HANA

   Skonfiguruj system RHEL zgodnie z opisem <https://access.redhat.com/solutions/2447641> w poniższych uwagach o systemie SAP:  
   - [2292690 — SAP HANA DB: zalecane ustawienia systemu operacyjnego dla systemu RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 — SAP HANA DB: zalecane ustawienia systemu operacyjnego dla systemu RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 — Linux: Uruchamianie aplikacji SAP skompilowanych za pomocą środowiska GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 — Linux: Uruchamianie aplikacji SAP skompilowanych za pomocą środowiska GCC 7.x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 — Linux: Uruchamianie aplikacji SAP skompilowanych za pomocą środowiska GCC 9.x](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** Zainstaluj SAP HANA

   Aby zainstalować SAP HANA replikacji systemu, wykonaj następujące <https://access.redhat.com/articles/3004101> czynności: .

   * Uruchom program **hdblcm** z dysku DVD HANA. Wprowadź następujące wartości w wierszu polecenia:
   * Wybierz instalację: wprowadź **1**.
   * Wybierz dodatkowe składniki do instalacji: wprowadź **1**.
   * Wprowadź ścieżkę instalacji [/hana/shared]: wybierz klawisz Enter.
   * Wprowadź nazwę hosta lokalnego [..]: wybierz klawisz Enter.
   * Czy chcesz dodać dodatkowe hosty do systemu? (y/n) [n]: wybierz klawisz Enter.
   * Wprowadź SAP HANA systemowy: wprowadź identyfikator SID platformy HANA, na przykład: **HN1**.
   * Wprowadź numer wystąpienia [00]: wprowadź numer wystąpienia platformy HANA. Wprowadź **wartość 03,** jeśli używasz szablonu platformy Azure lub zgodnie z sekcją ręcznego wdrażania tego artykułu.
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
   * Wprowadź katalog macierzysty administratora systemu [/usr/sap/HN1/home]: wybierz klawisz Enter.
   * Wprowadź nazwę Powłoki logowania administratora systemu [/bin/sh]: wybierz klawisz Enter.
   * Wprowadź identyfikator użytkownika administratora systemu [1001]: wybierz klawisz Enter.
   * Wprowadź identyfikator grupy użytkowników (sapsys) [79]: wybierz klawisz Enter.
   * Wprowadź hasło użytkownika bazy danych (SYSTEM): wprowadź hasło użytkownika bazy danych.
   * Potwierdź hasło użytkownika bazy danych (SYSTEM): wprowadź ponownie hasło użytkownika bazy danych, aby potwierdzić.
   * Uruchom ponownie system po ponownym uruchomieniu komputera? [n]: wybierz klawisz Enter.
   * Czy chcesz kontynuować? (y/n): zweryfikuj podsumowanie. Wprowadź **y,** aby kontynuować.

1. **[A] Uaktualnij** agenta hosta SAP.

   Pobierz najnowsze archiwum agenta hosta SAP z centrum [oprogramowania SAP i][sap-swcenter] uruchom następujące polecenie, aby uaktualnić agenta. Zastąp ścieżkę do archiwum, aby wskazać pobrany plik:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A] Konfigurowanie** zapory

   Utwórz regułę zapory dla portu sondy usługi Azure Load Balancer.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurowanie SAP HANA replikacji systemu w programie 2.0

W krokach w tej sekcji są następujące prefiksy:

* **[A]**: Krok ma zastosowanie do wszystkich węzłów.
* **[1]**: Krok dotyczy tylko węzła 1.
* **[2]**: Krok dotyczy tylko węzła 2 klastra Pacemaker.

1. **[A] Konfigurowanie** zapory

   Utwórz reguły zapory, aby zezwolić na ruch klientów i replikację systemu HANA. Wymagane porty są wymienione na liście [Porty TCP/IP wszystkich produktów SAP.](https://help.sap.com/viewer/ports) Poniższe polecenia są tylko przykładem zezwalania na replikację systemu HANA 2.0 i ruch klientów do bazy danych SYSTEMDB, HN1 i NW1.

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

   Jeśli używasz usługi SAP HANA 2.0 lub MDC, utwórz bazę danych dzierżawy dla systemu SAP NetWeaver. Zastąp **NW1** identyfikatorem SID systemu SAP.

   Wykonaj jako <hanasid \> adm następujące polecenie:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1] Skonfiguruj** replikację systemu w pierwszym węźle:

   Tworzenie kopii zapasowej baz danych <hanasid \> adm:

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

1. **[1] Sprawdzanie** stanu replikacji

   Sprawdź stan replikacji i poczekaj, aż wszystkie bazy danych będą zsynchronizowane. Jeśli stan pozostanie NIEZNANY, sprawdź ustawienia zapory.

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

## <a name="configure-sap-hana-10-system-replication"></a>Konfigurowanie SAP HANA replikacji systemu w programie 1.0

W krokach w tej sekcji są następujące prefiksy:

* **[A]**: Krok ma zastosowanie do wszystkich węzłów.
* **[1]**: Krok dotyczy tylko węzła 1.
* **[2]**: Krok dotyczy tylko węzła 2 klastra Pacemaker.

1. **[A] Konfigurowanie** zapory

   Utwórz reguły zapory, aby zezwolić na ruch klientów i replikację systemu HANA. Wymagane porty są wymienione na liście [Porty TCP/IP wszystkich produktów SAP.](https://help.sap.com/viewer/ports) Poniższe polecenia są tylko przykładem zezwalania na replikację systemu HANA 2.0. Dostosuj go do instalacji SAP HANA 1.0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

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

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w [te tematu Setting up Pacemaker on Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) (Konfigurowanie programu Pacemaker na platformie Azure), aby utworzyć podstawowy klaster pacemaker dla tego serwera HANA.

## <a name="implement-the-python-system-replication-hook-saphanasr"></a>Implementowanie wpięć replikacji systemu języka Python SAPHanaSR

Jest to ważny krok w celu zoptymalizowania integracji z klastrem i ulepszenia wykrywania, gdy jest wymagane włączenie klastra w tryb failover. Zdecydowanie zaleca się skonfigurowanie zaczepienia SAPHanaSR w języku Python.    

1. **[A]** Zainstaluj "wpięcie replikacji systemu" platformy HANA. Węzły zaczepienia muszą być zainstalowane w obu węzłach usługi HANA DB.           

   > [!TIP]
   > Wpięcie języka Python można zaimplementować tylko dla platformy HANA 2.0.        

   1. Przygotuj wpięcie jako `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Zatrzymaj hanę na obu węzłach. Wykonaj jako <\> sid adm:  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Dostosuj `global.ini` w każdym węźle klastra.  
 
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

3. **[A]** Uruchom SAP HANA obu węzłach. Wykonaj polecenie jako <\> sid adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1] Sprawdź** instalację wpięć. Wykonaj polecenie <\> sid adm w aktywnej lokacji replikacji systemu HANA.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

Aby uzyskać więcej informacji na temat implementacji SAP HANA replikacji systemu, zobacz Włączanie wpięcie dostawcy [SAP HA/DR.](https://access.redhat.com/articles/3004101#enable-srhook)  
 
## <a name="create-sap-hana-cluster-resources"></a>Tworzenie SAP HANA klastra

Zainstaluj agentów SAP HANA zasobów na **wszystkich węzłach.** Pamiętaj, aby włączyć repozytorium zawierające pakiet . Nie musisz włączać dodatkowych repozytoriów, jeśli używasz obrazu z włączoną wysokiej jakości obsługą systemu RHEL 8.x.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Następnie utwórz topologię platformy HANA. Uruchom następujące polecenia w jednym z węzłów klastra Pacemaker:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Następnie utwórz zasoby platformy HANA.

> [!NOTE]
> Ten artykuł zawiera odwołania do terminu podrzędnego *,* terminu, który nie jest już używany przez firmę Microsoft. Po usunięciu terminu z oprogramowania usuniemy go z tego artykułu.

W przypadku budowania klastra w **programie RHEL 7.x** użyj następujących poleceń:  

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

W przypadku budowania klastra w **programie RHEL 8.x** użyj następujących poleceń:  

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

Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby zostały uruchomione. Nie jest ważne, w którym węźle są uruchomione zasoby.

> [!NOTE]
> Limity czasu w powyższej konfiguracji są tylko przykładami i może być konieczne dostosowanie do określonej konfiguracji platformy HANA. Na przykład może być konieczne zwiększenie limitu czasu rozpoczęcia, jeśli uruchomienie bazy danych trwa dłużej SAP HANA danych.  

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


## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Konfigurowanie replikacji systemu z włączoną obsługą aktywnej/odczytu platformy HANA w klastrze Pacemaker

Począwszy od programu SAP HANA 2.0 SPS 01 SAP umożliwia konfiguracje aktywne/z obsługą odczytu na potrzeby replikacji systemu SAP HANA, w której systemy pomocnicze systemu replikacji systemu SAP HANA mogą być aktywnie używane dla obciążeń intensywnie wymagających odczytu. Do obsługi takiej konfiguracji w klastrze wymagany jest drugi wirtualny adres IP, który umożliwia klientom dostęp do pomocniczej bazy danych z włączoną obsługą odczytu SAP HANA danych. Aby zapewnić, że dostęp do dodatkowej lokacji replikacji będzie nadal możliwy po zakończeniu przejęcia, klaster musi przenieść wirtualny adres IP z pomocniczym zasobem SAPHana.

W tej sekcji opisano dodatkowe kroki, które są wymagane do zarządzania replikacją systemu z włączoną obsługą hana HANA Active/Read w klastrze o wysokiej dostępności z drugim wirtualnym adresem IP.    

Przed dalszymi krokami upewnij się, że masz w pełni skonfigurowany klaster wysokiej dostępności red Hat zarządzający SAP HANA bazą danych, zgodnie z opisem w powyższych segmentach dokumentacji.  

![SAP HANA wysokiej dostępności z pomocniczą obsługą odczytu](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Dodatkowa konfiguracja w usłudze Azure Load Balancer dla konfiguracji aktywne/z włączoną obsługą odczytu

Aby przejść do dodatkowych kroków aprowizowania drugiego wirtualnego adresu IP, upewnij się, że skonfigurowano Azure Load Balancer zgodnie z opisem w sekcji [Wdrażanie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#manual-deployment) ręczne.

1. W **przypadku usługi** load balancer w standardowych wersjach wykonaj poniższe dodatkowe kroki dotyczące tego samego usługi równoważenia obciążenia, która została utworzona we wcześniejszej sekcji.

   a. Utwórz drugą pulę adresów IP frontonia: 

   - Otwórz równoważenie obciążenia, wybierz **pulę adresów IP frontendu** i wybierz pozycję **Dodaj.**
   - Wprowadź nazwę drugiej puli adresów IP frontonia (na przykład **hana-secondaryIP).**
   - Ustaw ustawienie **Przypisanie** **na statyczne** i wprowadź adres IP (na przykład **10.0.0.14).**
   - Wybierz przycisk **OK**.
   - Po utworzeniu nowej puli adresów IP frontonia zanotuj adres IP puli.

   b. Następnie utwórz sondę kondycji:

   - Otwórz usługę równoważenia obciążenia, wybierz **sondy kondycji** i wybierz pozycję **Dodaj.**
   - Wprowadź nazwę nowej sondy kondycji (na przykład **hana-secondaryhp).**
   - Wybierz **TCP** jako protokół i port **62603.** Zachowaj wartość **Interwał** ustawioną na 5, a **wartość** progowa W złej kondycji na 2.
   - Wybierz przycisk **OK**.

   c. Następnie utwórz reguły równoważenia obciążenia:

   - Otwórz równoważenie obciążenia, wybierz **reguły równoważenia obciążenia** i wybierz pozycję **Dodaj.**
   - Wprowadź nazwę nowej reguły równoważenia obciążenia (na przykład **hana-secondarylb).**
   - Wybierz adres IP frontonia, pulę zaplecza i utworzoną wcześniej sondę kondycji (na przykład **hana-secondaryIP,** **hana-backend** i **hana-secondary hp).**
   - Wybierz pozycję **Porty HA.**
   - Upewnij się, że **włączyć zmienny adres IP**.
   - Wybierz przycisk **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>Konfigurowanie replikacji systemu z włączoną obsługą aktywnej/odczytu platformy HANA

Kroki konfigurowania replikacji systemu HANA opisano w sekcji [Konfigurowanie replikacji systemu SAP HANA 2.0.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication) Jeśli wdrażasz scenariusz pomocniczy z włączoną obsługą odczytu podczas konfigurowania replikacji systemu w drugim węźle, wykonaj następujące polecenie jako **hanasid** adm:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Dodawanie pomocniczego zasobu wirtualnego adresu IP dla konfiguracji aktywnej/z włączoną obsługą odczytu

Drugi wirtualny adres IP i odpowiednie ograniczenie kolokacji można skonfigurować za pomocą następujących poleceń:

```
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.40.0.16"

pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603

pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

RHEL 8.x: 
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-clone 4000
RHEL 7.x:
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-master 4000

pcs property set maintenance-mode=false
```
Upewnij się, że stan klastra jest w porządku i że wszystkie zasoby zostały uruchomione. Drugi wirtualny adres IP zostanie uruchomiony w lokacji dodatkowej wraz z zasobem pomocniczym SAPHana.

```
sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full List of Resources:
#   rsc_hdb_azr_agt     (stonith:fence_azure_arm):      Started hn1-db-0
#   Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]:
#     Started: [ hn1-db-0 hn1-db-1 ]
#   Clone Set: SAPHana_HN1_03-clone [SAPHana_HN1_03] (promotable):
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
#   Resource Group: g_ip_HN1_03:
#     nc_HN1_03         (ocf::heartbeat:azure-lb):      Started hn1-db-0
#     vip_HN1_03        (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#   Resource Group: g_secip_HN1_03:
#     secnc_HN1_03      (ocf::heartbeat:azure-lb):      Started hn1-db-1
#     secvip_HN1_03     (ocf::heartbeat:IPaddr2):       Started hn1-db-1
```

W następnej sekcji można znaleźć typowy zestaw testów trybu failover do wykonania.

Należy pamiętać o drugim zachowaniu wirtualnego adresu IP podczas testowania klastra HANA skonfigurowanego z pomocniczym włączonym odczytem:

1. Podczas migracji **SAPHana_HN1_HDB03** klastra do **hn1-db-1,** drugi wirtualny adres IP zostanie przeniesione do innego serwera **hn1-db-0.** Jeśli skonfigurowano wartość AUTOMATED_REGISTER="false", a replikacja systemu HANA nie jest rejestrowana automatycznie, drugi wirtualny adres IP zostanie uruchomiony na serwerze **hn1-db-0,** ponieważ serwer jest dostępny, a usługi klastra są w trybie online.  

2. Podczas testowania awarii serwera drugi wirtualny adres IP **(rsc_secip_HN1_HDB03)** i zasób portu usługi Azure Load Balancer **(rsc_secnc_HN1_HDB03)** będą uruchamiane na serwerze podstawowym wraz z podstawowymi zasobami wirtualnego adresu IP.  Gdy serwer pomocniczy nie istnieje, aplikacje połączone z bazą danych HANA z włączoną obsługą odczytu będą łączyć się z podstawową bazą danych HANA. Zachowanie jest oczekiwane, ponieważ nie chcesz, aby aplikacje połączone z bazą danych HANA z włączoną obsługą odczytu były niedostępne, gdy serwer pomocniczy jest niedostępny.

3. Gdy serwer pomocniczy jest dostępny, a usługi klastra są w trybie online, drugi wirtualny adres IP i zasoby portów zostaną automatycznie przenieść na serwer pomocniczy, mimo że replikacja systemu HANA może nie zostać zarejestrowana jako pomocnicza. Przed uruchomieniem usług klastra na tym serwerze należy upewnić się, że pomocnicza baza danych HANA jest włączona jako włączona. Zasób klastra wystąpień platformy HANA można skonfigurować do automatycznego rejestrowania pomocniczego, ustawiając parametr AUTOMATED_REGISTER=true.
   
4. Podczas pracy w trybu failover i powrotu po awarii istniejące połączenia dla aplikacji używające drugiego wirtualnego adresu IP do nawiązywania połączenia z bazą danych HANA mogą zostać przerwane.  

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano sposób testowania konfiguracji. Przed rozpoczęciem testu upewnij się, że program Pacemaker nie ma żadnej akcji, która zakończyła się niepowodzeniem (za pośrednictwem stanu pcs), nie ma nieoczekiwanych ograniczeń lokalizacji (na przykład pozostawień testu migracji) i że hana jest stanem synchronizacji, na przykład z stanem systemReplicationStatus:

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

Możesz przeprowadzić migrację SAP HANA głównego, wykonując następujące polecenie:

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

Jeśli ustawisz wartość , to polecenie powinno zmigrować węzeł główny SAP HANA grupę zawierającą wirtualny adres IP do `AUTOMATED_REGISTER="false"` węzła hn1-db-1.

Po zakończeniu migracji dane wyjściowe "sudo pcs status" wyglądają następująco

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

Zasób SAP HANA hn1-db-0 jest zatrzymany. W takim przypadku skonfiguruj wystąpienie platformy HANA jako pomocnicze, wykonując to polecenie:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

Migracja tworzy ograniczenia lokalizacji, które należy usunąć ponownie:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Monitoruj stan zasobu PLATFORMY HANA przy użyciu "stanu komputerów". Po zakończeniu pracy z programem HANA na hn1-db-0 dane wyjściowe powinny wyglądać podobnie do tych

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>Testowanie agenta ogrodzenia platformy Azure

> [!NOTE]
> Ten artykuł zawiera odwołania do terminu *podrzędnego*, czyli terminu, który nie jest już używany przez firmę Microsoft. Po usunięciu terminu z oprogramowania usuniemy go z tego artykułu.  

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

Możesz przetestować konfigurację agenta ogrodzenia platformy Azure, wyłączając interfejs sieciowy w węźle, w którym SAP HANA jako główny.
Opis sposobu symulowania awarii sieci można znaleźć w artykule [79523](https://access.redhat.com/solutions/79523) bazy wiedzy systemu Red Hat. W tym przykładzie używamy skryptu net_breaker, aby zablokować cały dostęp do sieci.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Maszyna wirtualna powinna zostać ponownie uruchomiona lub zatrzymana w zależności od konfiguracji klastra.
Jeśli to ustawienie zostanie wyłączone, maszyna wirtualna zostanie zatrzymana, a zasoby `stonith-action` zostaną zmigrowane do uruchomionej maszyny wirtualnej.

Po uruchomieniu maszyny wirtualnej ponownie uruchomienie zasobu SAP HANA nie powiedzie się jako pomocniczy, jeśli ustawisz `AUTOMATED_REGISTER="false"` wartość . W takim przypadku skonfiguruj wystąpienie platformy HANA jako pomocnicze, wykonując to polecenie:

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

### <a name="test-a-manual-failover"></a>Testowanie ręcznego trybu failover

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

Ręcznego trybu failover można przetestować, zatrzymując klaster w węźle hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Po zakończeniu pracy w trybie failover można ponownie uruchomić klaster. Jeśli `AUTOMATED_REGISTER="false"` ustawisz wartość , SAP HANA w węźle hn1-db-0 nie będzie można uruchomić jako pomocniczy. W takim przypadku skonfiguruj wystąpienie platformy HANA jako pomocnicze, wykonując to polecenie:

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

### <a name="test-a-manual-failover"></a>Testowanie ręcznego trybu failover

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

Ręcznego trybu failover można przetestować, zatrzymując klaster w węźle hn1-db-0:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>Następne kroki

* [Planowanie Virtual Machines wdrożenia oprogramowania SAP na platformie Azure][planning-guide]
* [Wdrożenie usługi Azure Virtual Machines sap][deployment-guide]
* [Wdrażanie Virtual Machines DBMS na platformie Azure dla oprogramowania SAP][dbms-guide]
* [SAP HANA magazynu maszyny wirtualnej](./hana-vm-operations-storage.md)