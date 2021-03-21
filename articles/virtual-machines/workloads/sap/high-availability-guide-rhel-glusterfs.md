---
title: GlusterFS na maszynach wirtualnych platformy Azure w usłudze RHEL for SAP NetWeaver | Microsoft Docs
description: System GlusterFS na maszynach wirtualnych platformy Azure z systemem Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: radeltch
ms.openlocfilehash: 3ebc125fe6802ffbe4192c0250ec9adc2ceceb0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668728"
---
# <a name="glusterfs-on-azure-vms-on-red-hat-enterprise-linux-for-sap-netweaver"></a>System GlusterFS na maszynach wirtualnych platformy Azure z systemem Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md

W tym artykule opisano sposób wdrażania maszyn wirtualnych, konfigurowania maszyn wirtualnych i instalowania klastra GlusterFS, który może służyć do przechowywania danych udostępnionych systemu SAP o wysokiej dostępności.
W tym przewodniku opisano sposób konfigurowania GlusterFS, który jest używany przez dwa systemy SAP, NW1 i NW2. Nazwy zasobów (na przykład Virtual Machines, Virtual Networks) w przykładzie założono, że użyto [szablonu serwera plików SAP][template-file-server] z prefiksem zasobu **glust**.

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
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux (ten artykuł)][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP w systemie Linux][dbms-guide]
* [Dokumentacja produktu dla magazynu Red Hat Gluster](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* Ogólna dokumentacja RHEL
  * [Omówienie Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administracja Add-On wysokiej dostępności](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Informacje o wysokiej dostępności Add-On](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Dokumentacja usługi RHEL specyficznej dla platformy Azure:
  * [Zasady obsługi klastrów RHEL o wysokiej dostępności — Microsoft Azure Virtual Machines jako elementy członkowskie klastra](https://access.redhat.com/articles/3131341)
  * [Instalowanie i Konfigurowanie Red Hat Enterprise Linux 7,4 (i nowszych) High-Availability klastra na Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Omówienie

Aby zapewnić wysoką dostępność, rozwiązanie SAP NetWeaver wymaga magazynu udostępnionego. GlusterFS jest skonfigurowany w oddzielnym klastrze i może być używany przez wiele systemów SAP.

![Omówienie wysokiej dostępności SAP NetWeaver](./media/high-availability-guide-rhel-glusterfs/rhel-glusterfs.png)

## <a name="set-up-glusterfs"></a>Konfigurowanie GlusterFS

Możesz użyć szablonu platformy Azure z usługi GitHub do wdrożenia wszystkich wymaganych zasobów platformy Azure, w tym maszyn wirtualnych, zestawu dostępności i interfejsów sieciowych, lub ręcznie wdrożyć zasoby.

### <a name="deploy-linux-via-azure-template"></a>Wdrażanie systemu Linux za pomocą szablonu platformy Azure

Portal Azure Marketplace zawiera obraz Red Hat Enterprise Linux, który służy do wdrażania nowych maszyn wirtualnych.
Możesz użyć jednego z szablonów szybkiego startu w usłudze GitHub, aby wdrożyć wszystkie wymagane zasoby. Szablon wdraża maszyny wirtualne, zestaw dostępności itd. Wykonaj następujące kroki, aby wdrożyć szablon:

1. Otwórz [Szablon serwera plików SAP][template-file-server] w Azure Portal
1. Wprowadź następujące parametry
   1. Prefiks zasobu  
      Wprowadź prefiks, którego chcesz użyć. Wartość jest używana jako prefiks dla wdrożonych zasobów.
   2. Liczba systemowa SAP wprowadź liczbę systemów SAP, które będą korzystać z tego serwera plików. Spowoduje to wdrożenie wymaganej liczby dysków itd.
   3. Typ systemu operacyjnego  
      Wybierz jedną z dystrybucji systemu Linux. Na potrzeby tego przykładu wybierz pozycję RHEL 7
   4. Nazwa użytkownika administratora, hasło administratora lub klucz SSH  
      Zostanie utworzony nowy użytkownik, którego można użyć do zalogowania się na komputerze.
   5. Identyfikator podsieci  
      Jeśli chcesz wdrożyć maszynę wirtualną w istniejącej sieci wirtualnej, w której zdefiniowano podsieć, należy przypisać do niej identyfikator tej konkretnej podsieci. Identyfikator zazwyczaj wygląda tak, jak **&lt; Identyfikator &gt; subskrypcji**/subscriptions//ResourceGroups/nazwa **&lt; grupy &gt; zasobów**/Providers/Microsoft.Network/virtualNetworks/nazwa **&lt; sieci &gt; wirtualnej**/Subnets/**&lt; nazwa &gt; podsieci**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ręczne wdrażanie systemu Linux za pośrednictwem Azure Portal

Najpierw musisz utworzyć maszyny wirtualne dla tego klastra. Następnie należy utworzyć moduł równoważenia obciążenia i użyć maszyn wirtualnych w pulach zaplecza. Zalecamy użycie [standardowej usługi równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md).  

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
1. Dodaj jeden dysk danych dla każdego systemu SAP do obu maszyn wirtualnych.

### <a name="configure-glusterfs"></a>Konfigurowanie GlusterFS

Następujące elementy są poprzedzone znakiem **[A]** -odpowiednim dla wszystkich węzłów, **[1]** — dotyczy tylko węzła 1, **[2]** — dotyczy tylko węzła 2, **[3]** — dotyczy tylko węzła 3.

1. **[A]** rozpoznawanie nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku

   <pre><code># IP addresses of the Gluster nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   </code></pre>

1. Rejestr **[A]**

   Rejestrowanie maszyn wirtualnych i dołączanie ich do puli zawierającej repozytoria dla RHEL 7 i GlusterFS

   <pre><code>sudo subscription-manager register
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]** Włącz repozytoria GlusterFS

   Aby zainstalować wymagane pakiety, należy włączyć następujące repozytoria.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rh-gluster-3-for-rhel-7-server-rpms
   </code></pre>
  
1. **[A]** Zainstaluj pakiety GlusterFS

   Zainstaluj te pakiety we wszystkich węzłach GlusterFS

   <pre><code>sudo yum -y install redhat-storage-server
   </code></pre>

   Po zakończeniu instalacji uruchom ponownie węzły.

1. **[A]** modyfikowanie zapory

   Dodaj reguły zapory, aby zezwolić na ruch klienta do węzłów GlusterFS.

   <pre><code># list the available zones
   firewall-cmd --get-active-zones
   
   sudo firewall-cmd --zone=public --add-service=glusterfs --permanent
   sudo firewall-cmd --zone=public --add-service=glusterfs
   </code></pre>

1. **[A]** Włączanie i uruchamianie usługi GlusterFS

   Uruchom usługę GlusterFS na wszystkich węzłach.

   <pre><code>sudo systemctl start glusterd
   sudo systemctl enable glusterd
   </code></pre>

1. **[1]** Utwórz GluserFS

   Uruchom następujące polecenia, aby utworzyć klaster GlusterFS

   <pre><code>sudo gluster peer probe glust-1
   sudo gluster peer probe glust-2
   
   # Check gluster peer status
   sudo gluster peer status
   
   # Number of Peers: 2
   # 
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Accepted peer request (Connected)
   # 
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Accepted peer request (Connected)
   </code></pre>

1. **[2]** test stanu elementu równorzędnego

   Testowanie stanu elementu równorzędnego w drugim węźle

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[3]** testowanie stanu elementu równorzędnego

   Testowanie stanu elementu równorzędnego w trzecim węźle

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[A]** Utwórz LVM

   W tym przykładzie GlusterFS jest używany dla dwóch systemów SAP, NW1 i NW2. Użyj następujących poleceń, aby utworzyć konfiguracje LVM dla tych systemów SAP.

   Użyj tych poleceń dla NW1

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun0
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW1</b> /dev/disk/azure/scsi1/lun0
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW1</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW1</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW1</b>/aers
   sudo lvscan
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/aers
   
   sudo mkdir -p /rhs/<b>NW1</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW1</b>/trans
   sudo mkdir -p /rhs/<b>NW1</b>/sys
   sudo mkdir -p /rhs/<b>NW1</b>/ascs
   sudo mkdir -p /rhs/<b>NW1</b>/aers
   
   sudo chattr +i /rhs/<b>NW1</b>/sapmnt
   sudo chattr +i /rhs/<b>NW1</b>/trans
   sudo chattr +i /rhs/<b>NW1</b>/sys
   sudo chattr +i /rhs/<b>NW1</b>/ascs
   sudo chattr +i /rhs/<b>NW1</b>/aers

   echo -e "/dev/rhgs-<b>NW1</b>/sapmnt\t/rhs/<b>NW1</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/trans\t/rhs/<b>NW1</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/sys\t/rhs/<b>NW1</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/ascs\t/rhs/<b>NW1</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/aers\t/rhs/<b>NW1</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

   Użyj tych poleceń dla NW2

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun1
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW2</b> /dev/disk/azure/scsi1/lun1
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW2</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW2</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW2</b>/aers
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/aers
   
   sudo mkdir -p /rhs/<b>NW2</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW2</b>/trans
   sudo mkdir -p /rhs/<b>NW2</b>/sys
   sudo mkdir -p /rhs/<b>NW2</b>/ascs
   sudo mkdir -p /rhs/<b>NW2</b>/aers
   
   sudo chattr +i /rhs/<b>NW2</b>/sapmnt
   sudo chattr +i /rhs/<b>NW2</b>/trans
   sudo chattr +i /rhs/<b>NW2</b>/sys
   sudo chattr +i /rhs/<b>NW2</b>/ascs
   sudo chattr +i /rhs/<b>NW2</b>/aers
   sudo lvscan
   
   echo -e "/dev/rhgs-<b>NW2</b>/sapmnt\t/rhs/<b>NW2</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/trans\t/rhs/<b>NW2</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/sys\t/rhs/<b>NW2</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/ascs\t/rhs/<b>NW2</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/aers\t/rhs/<b>NW2</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

1. **[1]** Tworzenie woluminu rozproszonego

   Użyj następujących poleceń, aby utworzyć wolumin GlusterFS dla NW1 i uruchomić go.

   <pre><code>sudo gluster vol create <b>NW1</b>-sapmnt replica 3 glust-0:/rhs/<b>NW1</b>/sapmnt glust-1:/rhs/<b>NW1</b>/sapmnt glust-2:/rhs/<b>NW1</b>/sapmnt force
   sudo gluster vol create <b>NW1</b>-trans replica 3 glust-0:/rhs/<b>NW1</b>/trans glust-1:/rhs/<b>NW1</b>/trans glust-2:/rhs/<b>NW1</b>/trans force
   sudo gluster vol create <b>NW1</b>-sys replica 3 glust-0:/rhs/<b>NW1</b>/sys glust-1:/rhs/<b>NW1</b>/sys glust-2:/rhs/<b>NW1</b>/sys force
   sudo gluster vol create <b>NW1</b>-ascs replica 3 glust-0:/rhs/<b>NW1</b>/ascs glust-1:/rhs/<b>NW1</b>/ascs glust-2:/rhs/<b>NW1</b>/ascs force
   sudo gluster vol create <b>NW1</b>-aers replica 3 glust-0:/rhs/<b>NW1</b>/aers glust-1:/rhs/<b>NW1</b>/aers glust-2:/rhs/<b>NW1</b>/aers force
   
   sudo gluster volume start <b>NW1</b>-sapmnt
   sudo gluster volume start <b>NW1</b>-trans
   sudo gluster volume start <b>NW1</b>-sys
   sudo gluster volume start <b>NW1</b>-ascs
   sudo gluster volume start <b>NW1</b>-aers
   </code></pre>

   Użyj następujących poleceń, aby utworzyć wolumin GlusterFS dla NW2 i uruchomić go.

   <pre><code>sudo gluster vol create <b>NW2</b>-sapmnt replica 3 glust-0:/rhs/<b>NW2</b>/sapmnt glust-1:/rhs/<b>NW2</b>/sapmnt glust-2:/rhs/<b>NW2</b>/sapmnt force
   sudo gluster vol create <b>NW2</b>-trans replica 3 glust-0:/rhs/<b>NW2</b>/trans glust-1:/rhs/<b>NW2</b>/trans glust-2:/rhs/<b>NW2</b>/trans force
   sudo gluster vol create <b>NW2</b>-sys replica 3 glust-0:/rhs/<b>NW2</b>/sys glust-1:/rhs/<b>NW2</b>/sys glust-2:/rhs/<b>NW2</b>/sys force
   sudo gluster vol create <b>NW2</b>-ascs replica 3 glust-0:/rhs/<b>NW2</b>/ascs glust-1:/rhs/<b>NW2</b>/ascs glust-2:/rhs/<b>NW2</b>/ascs force
   sudo gluster vol create <b>NW2</b>-aers replica 3 glust-0:/rhs/<b>NW2</b>/aers glust-1:/rhs/<b>NW2</b>/aers glust-2:/rhs/<b>NW2</b>/aers force
   
   sudo gluster volume start <b>NW2</b>-sapmnt
   sudo gluster volume start <b>NW2</b>-trans
   sudo gluster volume start <b>NW2</b>-sys
   sudo gluster volume start <b>NW2</b>-ascs
   sudo gluster volume start <b>NW2</b>-aers
   </code></pre>

## <a name="next-steps"></a>Następne kroki

* [Instalowanie oprogramowania SAP ASCS i bazy danych](high-availability-guide-rhel.md)
* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* Aby dowiedzieć się, jak stworzyć wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na platformie Azure (duże wystąpienia), zobacz [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md).
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
