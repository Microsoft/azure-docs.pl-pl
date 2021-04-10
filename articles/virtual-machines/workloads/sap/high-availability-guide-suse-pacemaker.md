---
title: Konfigurowanie Pacemaker na SLES na platformie Azure | Microsoft Docs
description: Konfigurowanie Pacemaker SUSE Linux Enterprise Server na platformie Azure
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
ms.date: 02/03/2020
ms.author: radeltch
ms.openlocfilehash: aa2006ecfad91e21ac13a1e63be23302b2a70399
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551037"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Konfigurowanie Pacemaker SUSE Linux Enterprise Server na platformie Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Dostępne są dwie opcje konfigurowania klastra Pacemaker na platformie Azure. Można użyć Agenta ogrodzenia, który należy zwrócić uwagę na ponowne uruchomienie węzła zakończonego niepowodzeniem za pośrednictwem interfejsów API platformy Azure lub można użyć urządzenia SBD.

Urządzenie SBD wymaga co najmniej jednej dodatkowej maszyny wirtualnej, która działa jako serwer docelowy iSCSI i udostępnia urządzenie SBD. Te serwery obiektów docelowych iSCSI można jednak udostępnić innym Klastrom Pacemaker. Zaletą korzystania z urządzenia SBD jest to, że jeśli już używasz urządzeń SBD w środowisku lokalnym, nie wymaga żadnych zmian w sposobie korzystania z klastra Pacemaker. Do klastra Pacemaker można użyć maksymalnie trzech urządzeń SBD, aby umożliwić niedostępność urządzenia SBD, na przykład podczas stosowania poprawek systemu operacyjnego serwera obiektów docelowych iSCSI. Jeśli chcesz użyć więcej niż jednego urządzenia SBD na Pacemaker, upewnij się, że wdrożono wiele serwerów obiektów docelowych iSCSI i połączenie jednego SBD z każdego serwera obiektów docelowych iSCSI. Zalecamy użycie jednego urządzenia SBD lub trzy. Pacemaker nie będzie mógł automatycznie obsłużyć węzła klastra, jeśli skonfigurowano tylko dwa urządzenia SBD i jedna z nich jest niedostępna. Jeśli chcesz mieć możliwość ogrodzenia, gdy jeden serwer docelowy iSCSI nie działa, musisz użyć trzech urządzeń SBD i w związku z tym trzy serwery obiektów docelowych iSCSI, co jest najbardziej odporną konfiguracją podczas korzystania z SBDs.

Agent usługi Azure ogrodzenia nie wymaga wdrażania dodatkowych maszyn wirtualnych.   

![Pacemaker na SLES — Omówienie](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Podczas planowania i wdrażania klastrowanych węzłów Pacemaker i urządzeń SBD z systemem Linux należy koniecznie ogólnej niezawodności kompletnej konfiguracji klastra, którą Routing między maszynami wirtualnymi i maszyn wirtualnych obsługujących urządzenia SBD nie są przekazywane przez żadne inne urządzenia, takie jak [urządzeń WUS](https://azure.microsoft.com/solutions/network-appliances/). W przeciwnym razie problemy i zdarzenia konserwacji z urządzenie WUS mogą mieć negatywny wpływ na stabilność i niezawodność ogólnej konfiguracji klastra. Aby uniknąć takich przeszkód, nie należy definiować reguł routingu urządzeń WUS lub [reguł routingu zdefiniowanych przez użytkownika](../../../virtual-network/virtual-networks-udr-overview.md) , które kierują ruch między węzłami klastrowanymi i urządzeniami SBD za poorednictwem urządzeń WUS i podobnych urządzeń podczas planowania i wdrażania węzłów klastra Pacemaker systemu Linux i urządzeń SBD. 
>

## <a name="sbd-fencing"></a>SBD ogrodzenia

Wykonaj te kroki, jeśli chcesz użyć urządzenia SBD na potrzeby ogrodzenia.

### <a name="set-up-iscsi-target-servers"></a>Konfigurowanie serwerów obiektów docelowych iSCSI

Najpierw musisz utworzyć maszyny wirtualne obiektów docelowych iSCSI. serwery obiektów docelowych iSCSI mogą być współużytkowane z wieloma klastrami Pacemaker.

1. Wdróż nowe maszyny wirtualne SLES 12 z dodatkiem SP1 lub nowszym, a następnie połącz się z nimi za pośrednictwem protokołu SSH. Maszyny nie muszą być duże. Rozmiar maszyny wirtualnej, taki jak Standard_E2s_v3 lub Standard_D2s_v3, jest wystarczający. Upewnij się, że na dysku systemu operacyjnego jest używany magazyn Premium Storage.

Uruchom następujące polecenia na wszystkich **docelowych maszynach wirtualnych iSCSI**.

1. Aktualizacja SLES

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Po uaktualnieniu lub zaktualizowaniu systemu operacyjnego może być konieczne ponowne uruchomienie systemu operacyjnego. 

1. Usuń pakiety

   Aby uniknąć znanego problemu z targetcli i SLES 12 SP3, Odinstaluj następujące pakiety. Można zignorować błędy dotyczące pakietów, których nie można znaleźć

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Instalowanie pakietów obiektów docelowych iSCSI

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Włączanie usługi obiektów docelowych iSCSI

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Utwórz urządzenie iSCSI na serwerze obiektów docelowych iSCSI

Uruchom następujące polecenia na wszystkich **docelowych maszynach wirtualnych iSCSI** , aby utworzyć dyski iSCSI dla klastrów używanych przez systemy SAP. W poniższym przykładzie są tworzone urządzenia SBD dla wielu klastrów. Pokazuje, w jaki sposób używać jednego serwera docelowego iSCSI dla wielu klastrów. Urządzenia SBD są umieszczane na dysku systemu operacyjnego. Upewnij się, że masz wystarczającą ilość miejsca.

**`nfs`** służy do identyfikowania klastra NFS, **ascsnw1** służy do identyfikowania klastra ASCS **NW1**, **dbnw1** służy do identyfikowania klastra bazy danych **NW1**, **NFS-0** i **NFS-1** są nazwami hostów węzłów klastra NFS, **NW1-Xscs-0** i **NW1-xscs-1** są nazwami hostów **NW1** ASCS węzłów klastra, a **NW1-DB-0** i **NW1-dB-1** są nazwami hostów węzłów klastra bazy danych. Zastąp je nazwami hostów węzłów klastra i identyfikatorem SID systemu SAP.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Możesz sprawdzić, czy wszystko zostało poprawnie skonfigurowane za pomocą

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Konfigurowanie urządzenia SBD

Nawiąż połączenie z urządzeniem iSCSI, które zostało utworzone w ostatnim kroku z klastra.
Uruchom następujące polecenia na węzłach nowego klastra, który chcesz utworzyć.
Następujące elementy są poprzedzone **[A]** -dotyczy wszystkie węzły, **[1]** — dotyczy tylko węzła 1 lub **[2]** — dotyczy tylko węzła 2.

1. **[A]** łączenie z urządzeniami iSCSI

   Najpierw Włącz usługi iSCSI i SBD.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** Zmień nazwę inicjatora w pierwszym węźle

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Zmień zawartość pliku tak, aby odpowiadała listom ACL użytym podczas tworzenia urządzenia iSCSI na serwerze docelowym iSCSI, na przykład dla serwera NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** Zmień nazwę inicjatora w drugim węźle

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Zmień zawartość pliku tak, aby odpowiadała listom ACL użytym podczas tworzenia urządzenia iSCSI na serwerze docelowym iSCSI

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Uruchom ponownie usługę iSCSI

   Teraz uruchom ponownie usługę iSCSI, aby zastosować zmianę.

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Podłącz urządzenia iSCSI. W poniższym przykładzie 10.0.0.17 jest adresem IP serwera docelowego iSCSI, a 3260 jest domyślnym portem. <b>IQN. 2006-04. NFS. local: system plików NFS</b> jest jedną z nazw docelowych, która jest wyświetlana podczas uruchamiania pierwszego polecenia poniżej (Odnajdywanie iscsiadm-m).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Upewnij się, że urządzenia iSCSI są dostępne i zanotuj nazwę urządzenia (w poniższym przykładzie/dev/SDE)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Teraz można pobrać identyfikatory urządzeń iSCSI.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   Polecenie wyświetla listę trzech identyfikatorów urządzeń dla każdego urządzenia SBD. Zalecamy użycie identyfikatora rozpoczynającego się od interfejsu SCSI-3, w powyższym przykładzie.

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Utwórz urządzenie SBD

   Użyj identyfikatora urządzenia dla urządzeń iSCSI, aby utworzyć nowe urządzenia SBD w pierwszym węźle klastra.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** dostosowanie konfiguracji SBD

   Otwórz plik konfiguracji SBD

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Zmień właściwość urządzenia SBD, Włącz integrację Pacemaker i zmień tryb uruchamiania elementu SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   Utwórz `softdog` plik konfiguracji

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Teraz Załaduj moduł

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalacja klastra

Następujące elementy są poprzedzone **[A]** -dotyczy wszystkie węzły, **[1]** — dotyczy tylko węzła 1 lub **[2]** — dotyczy tylko węzła 2.

1. **[A]** aktualizacja SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** składnik instalacji wymagany dla zasobów klastra

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** Zainstaluj składnik Azure-lb, który jest wymagany dla zasobów klastra

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Sprawdź wersję agentów zasobów pakietu i upewnij się, że spełnione są wymagania dotyczące minimalnej wersji:  
   > - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej równa "Resource-Agents-4.3.018. a7fb5035-3.30.1.  
   > - W przypadku programu SLES 15/15 z dodatkiem SP1 wersja musi być co najmniej równa "Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  

1. **[A]** Skonfiguruj system operacyjny

   W niektórych przypadkach Pacemaker tworzy wiele procesów, a tym samym wyczerpuje dozwoloną liczbę procesów. W takim przypadku puls między węzłami klastra może zakończyć się niepowodzeniem i prowadzić do przejścia w tryb failover zasobów. Zalecamy zwiększenie maksymalnego dozwolonego procesu przez ustawienie poniższego parametru.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Zmniejsz rozmiar zanieczyszczonej pamięci podręcznej. Aby uzyskać więcej informacji, zobacz [niska wydajność zapisu na serwerach z systemem SLES 11/12 i dużą ilością pamięci RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Konfigurowanie klastra usługi Cloud-config — Azure for ha

   >[!NOTE]
   > Sprawdź zainstalowaną wersję pakietu **Cloud config — Azure** , uruchamiając **użyciu narzędzia zypper info Cloud-config-Azure**. Jeśli wersja środowiska ma wartość 1,3 lub wyższą, nie jest już konieczne pomijanie zarządzania interfejsami sieciowymi przez wtyczkę sieci w chmurze. Jeśli wersja jest niższa niż 1,3, Zalecamy zaktualizowanie pakietu **Cloud-config-Azure** do najnowszej dostępnej wersji.  

   Zmień plik konfiguracji dla interfejsu sieciowego, jak pokazano poniżej, aby zapobiec usunięciu przez wtyczkę sieci wirtualnej wirtualnego adresu IP (Pacemaker musi kontrolować przypisanie adresu VIP). Aby uzyskać więcej informacji, zobacz [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** Włącz dostęp SSH

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** Włącz dostęp SSH

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** Włącz dostęp SSH

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Zainstaluj pakiet agentów ogrodzenia, jeśli używasz urządzenia STONITH na podstawie agenta usługi Azure ogrodzenia.  
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Zainstalowana wersja **ogranicznika** pakietu musi być co najmniej **4.4.0**  , aby można było korzystać z szybszych czasów pracy w trybie failover przy użyciu agenta usługi Azure ogrodzenia, jeśli węzły klastra muszą być ogrodzone. Zalecamy zaktualizowanie pakietu, jeśli jest uruchomiona Starsza wersja.  


1. **[A]** Instalacja zestawu Azure Python SDK 
   - W systemie SLES 12 SP4 lub SLES 12 SP5
   <pre><code>
    # You may need to activate the Public cloud extention first
    SUSEConnect -p sle-module-public-cloud/12/x86_64
    sudo zypper install python-azure-mgmt-compute
   </code></pre> 

   - Na SLES 15 i nowszych 
   <pre><code>
    # You may need to activate the Public cloud extention first. In this example the SUSEConnect command is for SLES 15 SP1
    SUSEConnect -p sle-module-public-cloud/15.1/x86_64
    sudo zypper install python3-azure-mgmt-compute
   </code></pre> 
 
   >[!IMPORTANT]
   >W zależności od wersji i typu obrazu może być konieczne aktywowanie rozszerzenia chmury publicznej dla wydania systemu operacyjnego, aby można było zainstalować zestaw Azure Python SDK.
   >Rozszerzenie można sprawdzić, uruchamiając SUSEConnect---list-Extensions.  
   >Aby osiągnąć krótszy czas pracy w trybie failover przy użyciu agenta usługi Azure ogrodzenia:
   > - w systemie SLES 12 SP4 lub SLES 12 SP5 Zainstaluj wersję **4.6.2** lub nowszą pakietu Python — usługa Azure-zarządzanie-obliczenia  
   > - w systemie SLES 15 Zainstaluj wersję **4.6.2** lub nowszą pakietu Python **3**— usługa Azure-zarządzania — obliczenia 

1. **[A]** rozpoznawanie nazw hostów

   Możesz użyć serwera DNS lub zmodyfikować/etc/hosts na wszystkich węzłach. Ten przykład pokazuje, jak używać pliku/etc/hosts.
   Zastąp adres IP i nazwę hosta w następujących poleceniach.

   >[!IMPORTANT]
   > W przypadku używania nazw hostów w konfiguracji klastra niezbędne jest niezawodne rozpoznawanie nazw hostów. Komunikacja klastra zakończy się niepowodzeniem, jeśli nazwy nie są dostępne i mogą prowadzić do opóźnień w przypadku awarii klastra.
   > Zaletą korzystania z/etc/hosts jest to, że klaster będzie niezależny od systemu DNS, co może być tylko pojedynczym punktem awarii.  
     
   <pre><code>sudo vi /etc/hosts

   </code></pre>

   Wstaw następujące wiersze do/etc/hosts. Zmień adres IP i nazwę hosta, aby odpowiadały Twojemu środowisku   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Zainstaluj klaster
- w przypadku używania urządzeń SBD do ogrodzenia
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

- Jeśli *nie używasz* urządzeń SBD dla ogrodzenia
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # Do you wish to use SBD (y/n)? <b>n</b>
   #WARNING: Not configuring SBD - STONITH will be disabled.
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Dodaj węzeł do klastra

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Zmień hasło hacluster na to samo hasło

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Dostosuj ustawienia Corosync.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Dodaj poniższą zawartość do pliku, jeśli wartości nie są dostępne lub różnią się. Upewnij się, że zmieniono token na 30000, aby umożliwić zachowanie konserwacji pamięci. Aby uzyskać więcej informacji, zobacz [ten artykuł dla systemu Linux][virtual-machines-linux-maintenance] lub [Windows][virtual-machines-windows-maintenance].

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Następnie uruchom ponownie usługę Corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Domyślna konfiguracja Pacemaker dla SBD

Konfiguracja w tej sekcji ma zastosowanie tylko w przypadku korzystania z SBD STONITH.  

1. **[1]** Włącz korzystanie z urządzenia STONITH i ustaw opóźnienie ogranicznika

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Utwórz urządzenie STONITH agenta usługi Azure ogrodzenia

Ta sekcja dokumentacji ma zastosowanie tylko w przypadku korzystania z programu STONITH na podstawie agenta usługi Azure Ogrodzeni.
Urządzenie STONITH używa nazwy głównej usługi do autoryzacji przed Microsoft Azure. Wykonaj następujące kroki, aby utworzyć nazwę główną usługi.

1. Przejdź do strony <https://portal.azure.com>
1. Otwórz blok Azure Active Directory  
   Przejdź do pozycji właściwości i Zapisz identyfikator katalogu. To jest **Identyfikator dzierżawy**.
1. Kliknij Rejestracje aplikacji
1. Kliknij pozycję Nowa rejestracja
1. Wprowadź nazwę, wybierz pozycję "konta tylko w tym katalogu organizacji". 
2. Wybierz pozycję typ aplikacji "sieć Web", wprowadź adres URL logowania (na przykład http: \/ /localhost), a następnie kliknij przycisk Dodaj.  
   Adres URL logowania nie jest używany i może być dowolnym prawidłowym adresem URL
1. Wybierz pozycję Certyfikaty i wpisy tajne, a następnie kliknij pozycję Nowy wpis tajny klienta.
1. Wprowadź opis nowego klucza, wybierz pozycję "nigdy nie wygasa" i kliknij przycisk Dodaj.
1. Zapisz wartość. Służy jako **hasło** dla nazwy głównej usługi
1. Wybierz pozycję Omówienie. Zapisz identyfikator aplikacji. Jest ona używana jako nazwa główna usługi

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Utwórz rolę niestandardową dla agenta ogranicznika

Nazwa główna usługi nie ma uprawnień dostępu do zasobów platformy Azure domyślnie. Należy nadać uprawnienia główne usługi do uruchamiania i zatrzymywania (cofania alokacji) wszystkich maszyn wirtualnych klastra. Jeśli rola niestandardowa nie została jeszcze utworzona, możesz ją utworzyć przy użyciu [programu PowerShell](../../../role-based-access-control/custom-roles-powershell.md#create-a-custom-role) lub [interfejsu wiersza polecenia platformy Azure](../../../role-based-access-control/custom-roles-cli.md)

Użyj następującej zawartości dla pliku wejściowego. Musisz dostosować zawartość do swoich subskrypcji, zastępując c276fc76-9cd4-44c9-99a7-4fd71546436e i e91d47c4-76f3-4271-a796-21b4ecfe3624 identyfikatorami subskrypcji. Jeśli masz tylko jedną subskrypcję, Usuń drugą pozycję w AssignableScopes.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Przypisz rolę niestandardową do jednostki usługi

Przypisz rolę niestandardową "rola agenta ogranicznika systemu Linux" utworzoną w ostatnim rozdziale do jednostki usługi. Nie używaj już roli właściciela!

1. Przejdź na stronę [https://portal.azure.com](https://portal.azure.com)
1. Otwórz blok wszystkie zasoby
1. Wybierz maszynę wirtualną pierwszego węzła klastra
1. Kliknij pozycję Kontrola dostępu (IAM)
1. Kliknij pozycję Dodaj przypisanie roli
1. Wybierz rolę "rola agenta ogranicznika systemu Linux"
1. Wprowadź nazwę utworzonej aplikacji
1. Klikanie pozycji Zapisz.

Powtórz powyższe kroki dla drugiego węzła klastra.

### <a name="1-create-the-stonith-devices"></a>**[1]** tworzenie urządzeń STONITH

Po edytowaniu uprawnień dla maszyn wirtualnych można skonfigurować urządzenia STONITH w klastrze.

> [!NOTE]
> Opcja "pcmk_host_map" jest wymagana tylko w poleceniu, jeśli nazwy hostów i maszyn wirtualnych platformy Azure nie są identyczne. Określ mapowanie w formacie **hostname: VM-Name**.
> Zapoznaj się z sekcją pogrubienie w poleceniu.

<pre><code>sudo crm configure property stonith-enabled=true
crm configure property concurrent-fencing=true
# replace the bold string with your subscription ID, resource group of the VM, tenant ID, service principal application ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
  params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>application ID</b>" passwd="<b>password</b>" \
  pcmk_monitor_retries=4 pcmk_action_limit=3 power_timeout=240 pcmk_reboot_timeout=900 <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
  op monitor interval=3600 timeout=120

sudo crm configure property stonith-timeout=900

</code></pre>

> [!IMPORTANT]
> Operacje monitorowania i ogrodzenia są deserializowane. W związku z tym, jeśli istnieje już uruchomiona operacja monitorowania i jednoczesne zdarzenie ogrodzenia, nie istnieje opóźnienie dla klastra w trybie failover z powodu już uruchomionej operacji monitorowania.

> [!TIP]
>Agent usługi Azure ogrodzenia wymaga łączności wychodzącej z publicznymi punktami końcowymi zgodnie z opisem, a także z możliwymi rozwiązaniami w [publicznej łączności punktu końcowego dla maszyn wirtualnych używających standardowej ILB](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Konfiguracja Pacemaker dla zaplanowanych zdarzeń platformy Azure

Platforma Azure oferuje [zaplanowane zdarzenia](../../linux/scheduled-events.md). Zaplanowane zdarzenia są udostępniane za pośrednictwem usługi meta-danych i umożliwiają czas przygotowania aplikacji do zdarzeń takich jak zamknięcie maszyny wirtualnej, ponowne wdrożenie maszyny wirtualnej itp. Agent zasobów **[Azure — monitorowanie zdarzeń](https://github.com/ClusterLabs/resource-agents/pull/1161)** dla zaplanowanych zdarzeń platformy Azure. Jeśli zostaną wykryte zdarzenia, Agent podejmie próbę zatrzymania wszystkich zasobów na maszynie wirtualnej, której to dotyczy, i przenieść je do innego węzła w klastrze. Aby uzyskać więcej zasobów Pacemaker, należy skonfigurować. 

1. **[A]** upewnij się, że pakiet agenta **usługi Azure-Events** jest już zainstalowany i aktualny. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Skonfiguruj zasoby w Pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Po skonfigurowaniu zasobów Pacemaker dla agenta zdarzeń platformy Azure, gdy klaster zostanie umieszczony w trybie konserwacji lub z niego, można uzyskać komunikaty ostrzegawcze, takie jak:  
     Ostrzeżenie: CIB-Bootstrap-Options: nieznany atrybut "hostName_ <strong> hostname</strong>"  
     Ostrzeżenie: CIB-Bootstrap-Options: nieznany atrybut "Azure-events_globalPullState"  
     Ostrzeżenie: CIB-Bootstrap-Options: nieznany atrybut "hostName_ <strong>hostname</strong>"  
   > Te komunikaty ostrzegawcze mogą być ignorowane.

## <a name="next-steps"></a>Następne kroki

* [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP][planning-guide]
* [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP][deployment-guide]
* [Wdrożenie systemu Azure Virtual Machines DBMS dla oprogramowania SAP][dbms-guide]
* [Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server][sles-nfs-guide]
* [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server dla aplikacji SAP][sles-guide]
* Aby dowiedzieć się, jak zapewnić wysoką dostępność i zaplanować odzyskiwanie po awarii SAP HANA na maszynach wirtualnych platformy Azure, zobacz [wysoka dostępność SAP HANA na platformie azure Virtual Machines (maszyny wirtualne)][sap-hana-ha]
