---
title: Wysoka dostępność dla systemu plików NFS na maszynach wirtualnych platformy Azure w systemie SLES | Microsoft Docs
description: Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server
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
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 6b0504f5e4199ee3cd8e86660b866fddf2568485
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608575"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych, instalowania platformy klastra i instalowania serwera NFS o wysokiej dostępności, który może służyć do przechowywania danych udostępnionych systemu SAP o wysokiej dostępności.
W tym przewodniku opisano sposób konfigurowania serwera NFS o wysokiej dostępności, który jest używany przez dwa systemy SAP, NW1 i NW2. Nazwy zasobów (na przykład Virtual Machines, Virtual Networks) w przykładzie założono, że użyto [szablonu serwera plików SAP][template-file-server] **z prefiksem** zasobu.


> [!NOTE]
> Ten artykuł zawiera odwołania do warunków podrzędnych *i* *głównych* warunków, które nie są już używane przez firmę Microsoft. Gdy warunki zostaną usunięte z oprogramowania, zostaną usunięte z tego artykułu.

Najpierw przeczytaj następujące informacje i dokumenty SAP

* Nota SAP [1928533], która ma:
  * Lista rozmiarów maszyn wirtualnych platformy Azure, które są obsługiwane w przypadku wdrażania oprogramowania SAP
  * Ważne informacje o pojemności dla rozmiarów maszyn wirtualnych platformy Azure
  * Obsługiwane oprogramowanie SAP oraz kombinacje systemu operacyjnego i bazy danych
  * Wymagana wersja jądra SAP dla systemu Windows i Linux w systemie Microsoft Azure

* Uwaga dotycząca protokołu SAP [2015553] zawiera listę wymagań wstępnych dotyczących wdrożeń oprogramowania SAP obsługiwanych przez oprogramowanie SAP na platformie Azure.
* W przypadku programu SAP Uwaga [2205917] zalecane ustawienia systemu operacyjnego dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga dotycząca oprogramowania SAP [1944799] SAP HANA wytycznych dla SUSE Linux Enterprise Server aplikacji SAP
* Uwaga dotycząca oprogramowania SAP [2178632] zawiera szczegółowe informacje o wszystkich metrykach monitorowania raportowanych dla oprogramowania SAP na platformie Azure.
* Uwaga dotycząca programu SAP [2191498] ma wymaganą wersję agenta hosta SAP dla systemu Linux na platformie Azure.
* Uwaga dotycząca oprogramowania SAP [2243692] zawiera informacje na temat licencjonowania SAP w systemie Linux na platformie Azure.
* Uwaga dotycząca protokołu SAP [1984787] zawiera ogólne informacje o SUSE Linux Enterprise Server 12.
* Uwaga dotycząca programu SAP [1999351] zawiera dodatkowe informacje dotyczące rozwiązywania problemów z rozszerzoną funkcją monitorowania platformy Azure dla oprogramowania SAP.
* [Społeczność systemu SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) na stronie wiki ma wszystkie wymagane uwagi SAP dla systemu Linux.
* [Planowanie i wdrażanie usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux (ten artykuł)][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Wskazówki dotyczące najlepszych rozwiązań dla systemu SUSE Linux Enterprise o wysokiej dostępności][sles-hae-guides]
  * Magazyn systemu plików NFS o wysokiej dostępności z DRBD i Pacemaker
* [SUSE Linux Enterprise Server for SAP Applications 12 SP3 — wskazówki dotyczące najlepszych rozwiązań][sles-for-sap-bp]
* [Informacje o wersji w programie SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Omówienie

Aby zapewnić wysoką dostępność, rozwiązanie SAP NetWeaver wymaga serwera NFS. Serwer NFS jest skonfigurowany w osobnym klastrze i może być używany przez wiele systemów SAP.

![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Serwer NFS używa dedykowanej wirtualnej nazwy hosta i wirtualnych adresów IP dla każdego systemu SAP, który używa tego serwera NFS. Na platformie Azure moduł równoważenia obciążenia jest wymagany do używania wirtualnego adresu IP. Na poniższej liście przedstawiono konfigurację modułu równoważenia obciążenia.        

* Konfiguracja frontonu
  * Adres IP 10.0.0.4 dla NW1
  * Adres IP 10.0.0.5 dla NW2
* Konfiguracja zaplecza
  * Połączono z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra NFS
* Port sondy
  * Port 61000 dla NW1
  * Port 61001 dla NW2
* Reguły równoważenia obciążenia (w przypadku korzystania z podstawowego modułu równoważenia obciążenia)
  * 2049 TCP dla NW1
  * 2049 UDP dla NW1
  * 2049 TCP dla NW2
  * 2049 UDP dla NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Konfigurowanie serwera NFS o wysokiej dostępności

Możesz użyć szablonu platformy Azure z usługi GitHub do wdrożenia wszystkich wymaganych zasobów platformy Azure, w tym maszyn wirtualnych, zestawu dostępności i modułu równoważenia obciążenia, lub ręcznie wdrożyć zasoby.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux za pomocą szablonu platformy Azure

Portal Azure Marketplace zawiera obraz dla SUSE Linux Enterprise Server dla aplikacji SAP 12, których można użyć do wdrażania nowych maszyn wirtualnych.
Możesz użyć jednego z szablonów szybkiego startu w usłudze GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, moduł równoważenia obciążenia, zestaw dostępności itd. Wykonaj następujące kroki, aby wdrożyć szablon:

1. Otwórz [Szablon serwera plików SAP][template-file-server] w Azure Portal   
1. Wprowadź następujące parametry
   1. Prefiks zasobu  
      Wprowadź prefiks, którego chcesz użyć. Wartość jest używana jako prefiks dla wdrożonych zasobów.
   2. Liczba systemów SAP  
      Wprowadź liczbę systemów SAP, które będą korzystać z tego serwera plików. Spowoduje to wdrożenie wymaganej liczby konfiguracji frontonu, zasad równoważenia obciążenia, portów sondowania, dysków itp.
   3. Typ systemu operacyjnego  
      Wybierz jedną z dystrybucji systemu Linux. Na potrzeby tego przykładu wybierz pozycję SLES 12
   4. Nazwa użytkownika administratora i hasło administratora  
      Zostanie utworzony nowy użytkownik, którego można użyć do zalogowania się na komputerze.
   5. Identyfikator podsieci  
      Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Identyfikator zazwyczaj wygląda tak, jak **&lt; Identyfikator &gt; subskrypcji**/subscriptions//ResourceGroups/nazwa **&lt; grupy &gt; zasobów**/Providers/Microsoft.Network/virtualNetworks/nazwa **&lt; sieci &gt; wirtualnej**/Subnets/**&lt; nazwa &gt; podsieci**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pośrednictwem Azure Portal

Najpierw należy utworzyć maszyny wirtualne dla tego klastra systemu plików NFS. Następnie należy utworzyć moduł równoważenia obciążenia i użyć maszyn wirtualnych w pulach zaplecza.

1. Tworzenie grupy zasobów
1. Tworzenie sieci wirtualnej
1. Tworzenie zestawu dostępności  
   Ustaw maksymalną domenę aktualizacji
1. Utwórz maszynę wirtualną 1 Użyj co najmniej SLES4SAP 12 SP3. w tym przykładzie jest używany SLES4SAP 12 SP3 BYOS Image SLES for SAP Applications 12 SP3 (BYOS)  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Utwórz maszynę wirtualną 2 Użyj co najmniej SLES4SAP 12 SP3, w tym przykładzie obraz SLES4SAP 12 SP3 BYOS  
   Używane są SLES for SAP Applications 12 SP3 (BYOS)  
   Wybierz utworzony wcześniej zestaw dostępności  
1. Dodaj jeden dysk danych dla każdego systemu SAP do obu maszyn wirtualnych.
1. Utwórz Load Balancer (wewnętrzny). Zalecamy użycie [standardowej usługi równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md).  
   1. Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć standardowy moduł równoważenia obciążenia:
      1. Utwórz adresy IP frontonu
         1. Adres IP 10.0.0.4 dla NW1
            1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pula adresów IP frontonu, a następnie kliknij przycisk Dodaj.
            1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **NW1-fronton**)
            1. Ustaw przypisanie na static i wprowadź adres IP (na przykład **10.0.0.4**)
            1. Kliknij przycisk OK.
         1. Adres IP 10.0.0.5 dla NW2
            * Powtórz powyższe kroki dla NW2
      1. Tworzenie pul zaplecza
         1. Połączono z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra NFS
            1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pule zaplecza, a następnie kliknij przycisk Dodaj.
            1. Wprowadź nazwę nowej puli zaplecza (na przykład NW przypadku **zaplecza**)
            1. Wybierz Virtual Network
            1. Kliknij pozycję Dodaj maszynę wirtualną
            1. Wybierz Maszyny wirtualne klastra NFS i ich adresy IP.
            1. Kliknij pozycję Add (Dodaj).
      1. Tworzenie sond kondycji
         1. Port 61000 dla NW1
            1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji, a następnie kliknij przycisk Dodaj.
            1. Wprowadź nazwę nowej sondy kondycji (na przykład **NW1-HP**)
            1. Wybierz pozycję TCP jako protokół, port 610 **00**, Zachowaj interwał 5 i próg złej kondycji 2
            1. Kliknij przycisk OK.
         1. Port 61001 dla NW2
            * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla NW2
      1. Reguły równoważenia obciążenia
         1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj.
         1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **NW1-lb**)
         1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **NW1-frontonu**. **NW zapleczu** i **NW1 — HP**)
         1. Wybierz pozycję **porty ha**.
         1. Zwiększ limit czasu bezczynności do 30 minut
         1. **Upewnij się, że włączono zmiennoprzecinkowy adres IP**
         1. Kliknij przycisk OK.
         * Powtórz powyższe kroki, aby utworzyć regułę równoważenia obciążenia dla NW2
   1. Alternatywnie, jeśli scenariusz wymaga podstawowego modułu równoważenia obciążenia, wykonaj następujące instrukcje:
      1. Utwórz adresy IP frontonu
         1. Adres IP 10.0.0.4 dla NW1
            1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pula adresów IP frontonu, a następnie kliknij przycisk Dodaj.
            1. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **NW1-fronton**)
            1. Ustaw przypisanie na static i wprowadź adres IP (na przykład **10.0.0.4**)
            1. Kliknij przycisk OK.
         1. Adres IP 10.0.0.5 dla NW2
            * Powtórz powyższe kroki dla NW2
      1. Tworzenie pul zaplecza
         1. Połączono z podstawowymi interfejsami sieciowymi wszystkich maszyn wirtualnych, które powinny być częścią klastra NFS
            1. Otwórz moduł równoważenia obciążenia, wybierz pozycję Pule zaplecza, a następnie kliknij przycisk Dodaj.
            1. Wprowadź nazwę nowej puli zaplecza (na przykład NW przypadku **zaplecza**)
            1. Kliknij pozycję Dodaj maszynę wirtualną
            1. Wybierz utworzony wcześniej zestaw dostępności
            1. Wybierz Maszyny wirtualne klastra NFS
            1. Kliknij przycisk OK.
      1. Tworzenie sond kondycji
         1. Port 61000 dla NW1
            1. Otwórz moduł równoważenia obciążenia, wybierz pozycję sondy kondycji, a następnie kliknij przycisk Dodaj.
            1. Wprowadź nazwę nowej sondy kondycji (na przykład **NW1-HP**)
            1. Wybierz pozycję TCP jako protokół, port 610 **00**, Zachowaj interwał 5 i próg złej kondycji 2
            1. Kliknij przycisk OK.
         1. Port 61001 dla NW2
            * Powtórz powyższe kroki, aby utworzyć sondę kondycji dla NW2
      1. Reguły równoważenia obciążenia
         1. 2049 TCP dla NW1
            1. Otwórz moduł równoważenia obciążenia, wybierz pozycję reguły równoważenia obciążenia i kliknij przycisk Dodaj.
            1. Wprowadź nazwę nowej reguły modułu równoważenia obciążenia (na przykład **NW1-lb-2049**)
            1. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **NW1-fronton**)
            1. Utrzymywanie protokołu **TCP**, wprowadź port **2049**
            1. Zwiększ limit czasu bezczynności do 30 minut
            1. **Upewnij się, że włączono zmiennoprzecinkowy adres IP**
            1. Kliknij przycisk OK.
         1. 2049 UDP dla NW1
            * Powtórz powyższe kroki dla portów 2049 i UDP dla NW1
         1. 2049 TCP dla NW2
            * Powtórz powyższe kroki dla portów 2049 i TCP dla NW2
         1. 2049 UDP dla NW2
            * Powtórz powyższe kroki dla portów 2049 i UDP dla NW2

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.  

> [!Note]
> Gdy maszyny wirtualne bez publicznych adresów IP są umieszczane w puli zaplecza wewnętrznego (bez publicznego adresu IP) standardowego modułu równoważenia obciążenia platformy Azure, nie będzie wychodzące połączenie z Internetem, chyba że zostanie przeprowadzona dodatkowa konfiguracja zezwalająca na kierowanie do publicznych punktów końcowych. Aby uzyskać szczegółowe informacje na temat sposobu osiągnięcia łączności wychodzącej, zobacz [publiczna łączność z punktem końcowym dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

> [!IMPORTANT]
> Nie należy włączać sygnatur czasowych protokołu TCP na maszynach wirtualnych platformy Azure umieszczonych za Azure Load Balancer. Włączenie sygnatur czasowych protokołu TCP spowoduje niepowodzenie sond kondycji. Ustaw parametr **net.IPv4.tcp_timestamps** na **0**. Aby uzyskać szczegółowe informacje, zobacz [sondy kondycji Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Tworzenie klastra Pacemaker

Wykonaj kroki opisane w temacie [Konfigurowanie Pacemaker SUSE Linux Enterprise Server na platformie Azure](high-availability-guide-suse-pacemaker.md) , aby utworzyć podstawowy klaster Pacemaker dla tego serwera systemu plików NFS.

### <a name="configure-nfs-server"></a>Konfigurowanie serwera NFS

Następujące elementy są poprzedzone **[A]** -dotyczy wszystkie węzły, **[1]** — dotyczy tylko węzła 1 lub **[2]** — dotyczy tylko węzła 2.

1. **[A]** rozpoznawanie nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** Włącz serwer NFS

   Tworzenie wpisu eksportu głównego systemu plików NFS

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Zainstaluj składniki DRBD

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Utwórz partycję dla urządzeń DRBD

   Wyświetl wszystkie dostępne dyski danych

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Przykładowe dane wyjściowe
   
   ```
   lun0  lun1
   ```

   Utwórz partycje dla każdego dysku z danymi

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** Utwórz konfiguracje LVM

   Wyświetl wszystkie dostępne partycje

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Przykładowe dane wyjściowe
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Utwórz woluminy LVM dla każdej partycji

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** Skonfiguruj DRBD

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Upewnij się, że plik DRBD. conf zawiera dwa następujące wiersze

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Zmień globalną konfigurację DRBD

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Dodaj następujące wpisy do sekcji obsługi i sieci.

   <pre><code>global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]** tworzenie urządzeń DRBD NFS

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Wstaw konfigurację dla nowego urządzenia DRBD i Zakończ

   <pre><code>resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Wstaw konfigurację dla nowego urządzenia DRBD i Zakończ

   <pre><code>resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Utwórz urządzenie DRBD i uruchom je

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Pomiń synchronizację początkową

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Ustaw węzeł podstawowy

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** poczekaj na zsynchronizowanie nowych urządzeń DRBD

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** tworzenie systemów plików na urządzeniach DRBD

   <pre><code>sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]** konfiguracja DRBD — wykrywanie rozdzielenia mózgów

   W przypadku korzystania z programu DRBD do synchronizowania danych z jednego hosta z innym, może wystąpić polecenie Split mózg. Podzielona mózg jest scenariuszem, w którym oba węzły klastra podwyższenie poziomu urządzenia DRBD jako podstawowe i wystąpiły z synchronizacją. Może to być rzadki przypadek, ale nadal chcesz obsługiwać i rozwiązywać podzielone mózgi tak szybko, jak to możliwe. W związku z tym ważne jest, aby otrzymywać powiadomienia, gdy wystąpił podzielony plik mózgów.

   Zapoznaj [się z oficjalną dokumentacją DRBD](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) na temat konfigurowania powiadomienia o rozdzieleniu mózgów.

   Możliwe jest również automatyczne odzyskanie z podzielonego scenariusza mózgów. Aby uzyskać więcej informacji, przeczytaj artykuł [Automatyczne dzielenie zasad odzyskiwania mózgów](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurowanie struktury klastra

1. **[1]** Dodaj urządzenia DRBD NFS dla oprogramowania SAP system NW1 do konfiguracji klastra

   > [!IMPORTANT]
   > Ostatnie testy ujawniły sytuacje, w których netcat przestaje odpowiadać na żądania z powodu zaległości i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań modułu równoważenia obciążenia platformy Azure, a przestawny adres IP stał się niedostępny.  
   > W przypadku istniejących klastrów Pacemaker zalecamy w przeszłości zastępowanie netcat socat. Obecnie zalecamy korzystanie z agenta zasobów Azure-lb, który jest częścią agentów zasobów pakietu, z następującymi wymaganiami dotyczącymi wersji pakietu:
   > - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej równa "Resource-Agents-4.3.018. a7fb5035-3.30.1.  
   > - W przypadku programu SLES 15/15 z dodatkiem SP1 wersja musi być co najmniej równa "Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  
   >
   > Należy pamiętać, że zmiana będzie wymagała krótkiego przestoju.  
   > W przypadku istniejących klastrów Pacemaker, jeśli konfiguracja została już zmieniona tak, aby korzystała z socat zgodnie z opisem w obszarze [Ograniczanie poziomu wykrywania w usłudze azure Load-Balancer](https://www.suse.com/support/kb/doc/?id=7024128), nie ma potrzeby natychmiastowego przełączenia do agenta zasobów platformy Azure.

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** Dodaj urządzenia DRBD NFS dla oprogramowania SAP system NW2 do konfiguracji klastra

   <pre><code># Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   `crossmnt`Opcja w `exportfs` zasobach klastra znajduje się w naszej dokumentacji w celu zapewnienia zgodności z poprzednimi wersjami przy użyciu starszych wersji programu SLES.  

1. **[1]** Wyłącz tryb konserwacji
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [Instalowanie oprogramowania SAP ASCS i bazy danych](high-availability-guide-suse.md)
* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
