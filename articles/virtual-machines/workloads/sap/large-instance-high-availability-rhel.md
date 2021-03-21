---
title: Duże dostępność dla oprogramowania SAP w systemie Azure na RHEL
description: Dowiedz się, jak zautomatyzować SAP HANA przełączenia w tryb failover bazy danych przy użyciu klastra Pacemaker w programie Red Hat Enterprise Linux.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 99e9994d01e4579bf6ef2e369e0fe85c48af52ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182438"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Duże dostępność dla oprogramowania SAP w systemie Azure na RHEL

W tym artykule dowiesz się, jak skonfigurować klaster Pacemaker w RHEL 7,6 w celu zautomatyzowania pracy w trybie failover bazy danych SAP HANA. Aby wykonać kroki opisane w tym przewodniku, musisz mieć dobre zrozumienie systemów Linux, SAP HANA i Pacemaker.

Poniższa tabela zawiera nazwy hostów, które są używane w tym artykule. Bloki kodu w artykule zawierają polecenia, które należy uruchomić, a także dane wyjściowe tych poleceń. Zwróć szczególną uwagę na to, do którego węzła odwołuje się każde polecenie.

| Typ | Nazwa hosta | Węzeł|
|-------|-------------|------|
|Host podstawowy|`sollabdsm35`|węzeł 1|
|Dodatkowy Host|`sollabdsm36`|węzeł 2|

## <a name="configure-your-pacemaker-cluster"></a>Konfigurowanie klastra Pacemaker


Przed rozpoczęciem konfigurowania klastra należy skonfigurować wymianę kluczy SSH, aby ustanowić relację zaufania między węzłami.

1. Użyj następujących poleceń, aby utworzyć identyczne `/etc/hosts` na obu węzłach.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    0.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    0.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    0.20.251.150 sollabdsm36-st

    10.20.251.151 sollabdsm35-st

    

    10.20.252.151 sollabdsm36-back

    10.20.252.150 sollabdsm35-back

    

    10.20.253.151 sollabdsm36-node

    10.20.253.150 sollabdsm35-node

    ```

2.  Utwórz i zaexchange klucze SSH.
    1. Generuj klucze SSH.

       ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
       ```
    2. Skopiuj klucze na inne hosty dla protokołu SSH bezhasłem.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Wyłącz SELinux na obu węzłach.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Uruchom ponownie serwery, a następnie użyj następującego polecenia, aby zweryfikować stan SELinux.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. Konfigurowanie protokołu NTP (protokół czasu sieciowego). Strefy czasowe i czasowe obu węzłów klastra muszą być zgodne. Aby otworzyć `chrony.conf` i sprawdzić zawartość pliku, użyj następującego polecenia.
    1. Do pliku konfiguracji należy dodać następującą zawartość. Zmień wartości rzeczywiste na odpowiednie dla danego środowiska.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Włącz usługę chrony. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. Aktualizowanie systemu
    1. Najpierw zainstaluj najnowsze aktualizacje w systemie przed rozpoczęciem instalacji urządzenia SBD.
    1. Jeśli nie chcesz, aby aktualizacja systemu była kompletna, nawet jeśli jest zalecana, zaktualizuj następujące pakiety co najmniej.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```
 

7. Zainstaluj repozytoria SAP HANA i RHEL-HA.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Zainstaluj narzędzia Pacemaker, SBD, OpenIPMI, ipmitools i fencing_sbd na wszystkich węzłach.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitools
    ```

  ## <a name="configure-watchdog"></a>Konfigurowanie licznika alarmowego

W tej sekcji dowiesz się, jak skonfigurować licznik alarm. Ta sekcja zawiera te same dwa hosty `sollabdsm35` i `sollabdsm36` , do których odwołuje się na początku tego artykułu.

1. Upewnij się, że demon licznika alarm nie jest uruchomiony w żadnym systemie.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. Domyślne licznik alarmowy systemu Linux, który zostanie zainstalowany podczas instalacji, to iTCO licznik, który nie jest obsługiwany przez systemy UCS i HPE SDFlex. W związku z tym ten licznik alarmowy musi być wyłączony.
    1. W systemie jest zainstalowany i załadowany nieprawidłowy licznik:
       ```
   
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. Zwolnij niewłaściwy sterownik ze środowiska:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Aby upewnić się, że sterownik nie jest ładowany podczas następnego rozruchu systemu, Sterownik musi być blocklisted. Aby listy blokowania moduły iTCO, Dodaj następujący na końcu `50-blacklist.conf` pliku:
       ```
   
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. Skopiuj plik na host pomocniczy.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. Sprawdź, czy usługa IPMI została uruchomiona. Należy pamiętać, że czasomierz IPMI nie jest uruchomiony. Zarządzanie czasomierzem zostanie wykonane z usługi SBD Pacemaker.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. Domyślnie wymagane urządzenie to/dev/Watchdog nie zostanie utworzone.

    ```
    No watchdog device was created

    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. Skonfiguruj licznik licznika IPMI.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. Skopiuj plik konfiguracji licznika alarmowego do pomocniczego.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Włącz i uruchom usługę IPMI.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     Teraz usługa IPMI jest uruchomiona i/dev/Watchdog urządzenia, a czasomierz jest nadal zatrzymany. Później SBD będzie zarządzać resetowaniem i włącza czasomierz IPMI.
7.  Sprawdź, czy/dev/Watchdog istnieje, ale nie jest w użyciu.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>Konfiguracja SBD
W tej sekcji dowiesz się, jak skonfigurować SBD. Ta sekcja zawiera te same dwa hosty `sollabdsm35` i `sollabdsm36` , do których odwołuje się na początku tego artykułu.

1.  Upewnij się, że dysk iSCSI lub FC jest widoczny w obu węzłach. W tym przykładzie jest stosowane urządzenie SBD oparte na FC. Aby uzyskać więcej informacji na temat ogrodzenia SBD, zobacz [dokumentację referencyjną](http://www.linux-ha.org/wiki/SBD_Fencing).
2.  Identyfikator LUN musi być identyczny we wszystkich węzłach.
  
3.  Sprawdź stan wielościeżkowego urządzenia SBD.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  Tworzenie dysków SBD i Konfigurowanie ogrodzenia podstawowego klastra. Ten krok należy wykonać na pierwszym węźle.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  Skopiuj konfigurację SBD do Węzeł2.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  Sprawdź, czy dysk SBD jest widoczny w obu węzłach.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  Dodaj urządzenie SBD w pliku konfiguracyjnym SBD.

    ```
    \# SBD_DEVICE specifies the devices to use for exchanging sbd messages

    \# and to monitor. If specifying more than one path, use ";" as

    \# separator.

    \#

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    \## Type: yesno
     Default: yes
     \# Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Inicjowanie klastra
W tej sekcji zainicjujesz klaster. Ta sekcja zawiera te same dwa hosty `sollabdsm35` i `sollabdsm36` , do których odwołuje się na początku tego artykułu.

1.  Skonfiguruj hasło użytkownika klastra (wszystkie węzły).
    ```
    passwd hacluster
    ```
2.  Uruchom komputery we wszystkich systemach.
    ```
    systemctl enable pcsd
    ```
  

3.  Zatrzymaj zaporę i wyłącz ją na (wszystkie węzły).
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Uruchom usługę pcsd.
    ```
    systemctl start pcsd
    ```
  
  

5.  Uruchom uwierzytelnianie klastra tylko z Węzeł1.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36



        Username: hacluster

            Password:

            sollabdsm35.localdomain: Authorized

            sollabdsm36.localdomain: Authorized

     ``` 

6.  Tworzenie klastra.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  Sprawdź stan klastra.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. Jeśli jeden węzeł nie jest przyłączany do klastra, sprawdź, czy Zapora jest nadal uruchomiona.

  

9. Tworzenie i Włączanie urządzenia SBD
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  

10. Zatrzymaj klaster ponownie uruchamia usługi klastra (we wszystkich węzłach).

    ```
    pcs cluster stop --all
    ```


11. Uruchom ponownie usługi klastra (we wszystkich węzłach).

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Corosync musi uruchomić usługę SBD.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Uruchom ponownie klaster (jeśli nie został automatycznie uruchomiony z pcsd).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Włącz ustawienia Stonith.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Sprawdź nowy stan klastra za pomocą teraz jednego zasobu.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. Teraz należy uruchomić czasomierz IPMI, a urządzenie/dev/Watchdog musi być otwarte przez SBD.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm351 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. Sprawdź stan SBD.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Przetestuj ogrodzenie SBD przez awarię jądra.

    * Wyzwól awarię jądra.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * Drugim testem do uruchomienia jest ogrodzenie węzła przy użyciu poleceń komputerów.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. W pozostałej części klastrowania SAP HANA można wyłączyć STONITH przez ustawienie:

   * zestaw właściwości komputerów `stonith-enabled=false`
   * Ten parametr musi być ustawiony na wartość true, aby można było użyć wydajności. Jeśli ten parametr nie ma wartości true, klaster nie będzie obsługiwany.
   * zestaw właściwości komputerów `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>Integracja platformy HANA z klastrem

W tej sekcji integrujesz platformę HANA z klastrem. Ta sekcja zawiera te same dwa hosty `sollabdsm35` i `sollabdsm36` , do których odwołuje się na początku tego artykułu.

Dostępne są dwie opcje integracji platformy HANA. Pierwsza opcja to rozwiązanie zoptymalizowane pod kątem kosztów, za pomocą którego można uruchomić system QAS. Ta metoda nie jest zalecana, ponieważ nie pozostawia systemu do testowania aktualizacji oprogramowania klastra, systemu operacyjnego lub platformy HANA, a aktualizacje konfiguracji mogą prowadzić do nieplanowanych przestojów systemu PRD. Ponadto, jeśli system PRD musi zostać aktywowany w systemie pomocniczym, należy wyłączyć QAS w węźle pomocniczym. Drugą opcją jest zainstalowanie systemu QAS w jednym klastrze i użycie drugiego klastra dla PRD. Ta opcja umożliwia również przetestowanie wszystkich składników przed ich wprowadzeniem do środowiska produkcyjnego. W tym artykule opisano sposób konfigurowania drugiej opcji.


* Ten proces jest kompilacją opisu RHEL na stronie:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>Kroki, które należy wykonać, aby skonfigurować HSR

1.  Są to akcje do wykonania w systemie Węzeł1 (podstawowe).
    1. Upewnij się, że tryb dziennika bazy danych jest ustawiony na wartość normalny.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p SAPhana10 -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. Replikacja systemu SAP HANA będzie działała dopiero po wykonaniu początkowej kopii zapasowej. Następujące polecenie tworzy początkową kopię zapasową w `/tmp/` katalogu. Wybierz odpowiedni system plików kopii zapasowej bazy danych. 
       ```
       * hdbsql -i 00 -u system -p SAPhana10 "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```
    

    3. Utwórz kopię zapasową wszystkich kontenerów bazy danych tej bazy danych.
       ```
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"
   
       
   
       * hdbsql -i 00 -u system -p SAPhana10 -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Włącz proces HSR w systemie źródłowym.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Sprawdź stan systemu podstawowego.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. Są to akcje do wykonania w programie Węzeł2 (pomocniczy).
     1. Zatrzymaj bazę danych.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Tylko w przypadku SAP HANA 2.0 Skopiuj system SAP HANA `PKI SSFS_HR2.KEY` i `SSFS_HR2.DAT` pliki z węzła podstawowego do węzła pomocniczego.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. Włącz pomocniczy jako lokację replikacji.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. Uruchom bazę danych.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. Sprawdź stan bazy danych.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. Można również uzyskać więcej informacji na temat stanu replikacji:
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>Opis trybu replikacji dziennika

Aby uzyskać więcej informacji na temat trybu replikacji dzienników, zapoznaj się z [oficjalną dokumentacją SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/c039a1a5b8824ecfa754b55e0caffc01.html).
  

#### <a name="network-setup-for-hana-system-replication"></a>Konfiguracja sieci dla replikacji systemu HANA


Aby zapewnić, że ruch związany z replikacją korzysta z odpowiedniej sieci VLAN do replikacji, musi być prawidłowo skonfigurowany w `global.ini` . W przypadku pominięcia tego kroku platformy HANA będą używać sieci VLAN dostępu do replikacji, co może być niepożądane.


W poniższych przykładach przedstawiono konfigurację rozpoznawania nazw hostów na potrzeby replikacji systemu do lokacji dodatkowej. Można zidentyfikować trzy różne sieci:

* Sieć publiczna z adresami w zakresie 10.0.1. *

* Sieć do komunikacji wewnętrznej SAP HANA między hostami w każdej lokacji: 192.168.1. *

* Sieć dedykowana na potrzeby replikacji systemu: 10.5.1. *

W pierwszym przykładzie `[system_replication_communication]listeninterface` parametr został ustawiony na `.global` i określono tylko hosty sąsiedniej lokacji replikacji.

W poniższym przykładzie `[system_replication_communication]listeninterface` parametr został ustawiony na, `.internal` a wszystkie hosty obu lokacji są określone.

  

### <a name="source-sap-ag-sap-hana-hrs-networking"></a>Źródłowa sieć SAP AG SAP HANA godz.

  

W przypadku replikacji systemu nie trzeba edytować `/etc/hosts` pliku, wewnętrzne ("wirtualne") nazwy hostów muszą być mapowane na adresy IP w `global.ini` pliku, aby utworzyć dedykowaną sieć do replikacji systemu. Składnia jest następująca:

global.ini

[system_replication_hostname_resolution]

<IP-address_site>=<wewnętrzny-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Konfigurowanie SAP HANA w klastrze Pacemaker
W tej sekcji dowiesz się, jak skonfigurować SAP HANA w klastrze Pacemaker. Ta sekcja zawiera te same dwa hosty `sollabdsm35` i `sollabdsm36` , do których odwołuje się na początku tego artykułu.

Upewnij się, że spełniono następujące wymagania wstępne:  

* Klaster Pacemaker jest konfigurowany zgodnie z dokumentacją i ma odpowiednie i działające ogrodzenie

* SAP HANA uruchamianie przy rozruchu jest wyłączone na wszystkich węzłach klastra, gdy uruchomienie i zatrzymanie będzie zarządzane przez klaster

* SAP HANA replikacji i przejęcia systemu przy użyciu narzędzi z oprogramowania SAP działają prawidłowo między węzłami klastra

* SAP HANA zawiera konto monitorowania, które może być używane przez klaster z obu węzłów klastra

* Oba węzły są subskrybowane do kanałów "High-Availability" i "RHEL for SAP HANA" (RHEL 6, RHEL 7)

  

* Ogólnie rzecz biorąc, należy wykonać wszystkie polecenia wszystkie komputery z węzła w węźle, ponieważ CIB zostanie automatycznie zaktualizowana z poziomu powłoki komputery.

* [Więcej informacji na temat zasad kworum](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>Kroki konfigurowania 
1. Skonfiguruj komputery.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Skonfiguruj Corosync.
    ```
    https://access.redhat.com/solutions/1293523 --> quorum information RHEL7

    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

1.  Utwórz sklonowany zasób SAPHanaTopology.
    ```
    pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1 interleave=true
    SAPHanaTopology resource is gathering status and configuration of SAP
    HANA System Replication on each node. SAPHanaTopology requires
    following attributes to be configured.



        Attribute Name Description

        SID SAP System Identifier (SID) of SAP HANA installation. Must be
    same for all nodes.

    InstanceNumber 2-digit SAP Instance identifier.
    pcs resource show SAPHanaTopology_HR2_00-clone

    Clone: SAPHanaTopology_HR2_00-clone

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true

        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat
    type=SAPHanaTopology)

        Attributes: InstanceNumber=00 SID=HR2

        Operations: monitor interval=60 timeout=60
    (SAPHanaTopology_HR2_00-monitor-interval-60)

        start interval=0s timeout=180
    (SAPHanaTopology_HR2_00-start-interval-0s)

        stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)

    ```

3.  Utwórz zasób podstawowy/pomocniczy SAPHana.

    ```
    SAPHana resource is responsible for starting, stopping and relocating the SAP HANA database. This resource must be run as a Primary/    Secondary cluster resource. The resource has the following attributes.

    

    Attribute Name Required? Default value Description

    SID Yes None SAP System Identifier (SID) of SAP HANA installation. Must be same for all nodes.

    InstanceNumber Yes none 2-digit SAP Instance identifier.

    PREFER_SITE_TAKEOVER

    no yes Should cluster prefer to switchover to secondary instance instead of restarting primary locally? ("no": Do prefer restart locally;   "yes": Do prefer takeover to remote site)

    AUTOMATED_REGISTER no false Should the former SAP HANA primary be registered as secondary after takeover and DUPLICATE_PRIMARY_TIMEOUT?     ("false": no, manual intervention will be needed; "true": yes, the former primary will be registered by resource agent as secondary)

    DUPLICATE_PRIMARY_TIMEOUT no 7200 Time difference (in seconds) needed between primary time stamps, if a dual-primary situation occurs. If   the time difference is less than the time gap, then the cluster holds one or both instances in a "WAITING" status. This is to give an   admin a chance to react on a failover. A failed former primary will be registered after the time difference is passed. After this   registration to the new primary all data will be overwritten by the system replication.
    ```
  

5.  Utwórz zasób HANA.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200   AUTOMATED_REGISTER=true primary notify=true clone-max=2 clone-node-max=1 interleave=true

    pcs resource show SAPHana_HR2_00-primary



    Primary: SAPHana_HR2_00-primary

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true

        Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)

        Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200
    InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2

        Operations: demote interval=0s timeout=320
    (SAPHana_HR2_00-demote-interval-0s)

        monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)

        monitor interval=121 role=Secondary timeout=60
    (SAPHana_HR2_00-monitor-

        interval-121)

        monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-

        interval-119)

        promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)

        start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)

        stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  Utwórz zasób wirtualnego adresu IP.

    ```
    Cluster will contain Virtual IP address in order to reach the Primary instance of SAP HANA. Below is example command to create IPaddr2  resource with IP 10.7.0.84/24

    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Utwórz ograniczenia.

    ```
    For correct operation we need to ensure that SAPHanaTopology resources are started before starting the SAPHana resources and also that  the virtual IP address is present on the node where the Primary resource of SAPHana is running. To achieve this, the following 2    constraints need to be created.

    pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
    pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
    ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>Testowanie ręcznego przenoszenia zasobu SAPHana do innego węzła

#### <a name="sap-hana-takeover-by-cluster"></a>(Przejęcie SAP HANA przez klaster)


Aby przetestować przenoszenie zasobu SAPHana z jednego węzła do drugiego, użyj poniższego polecenia. Należy zauważyć, że opcja `--primary` nie powinna być używana podczas uruchamiania następującego polecenia ze względu na sposób, w jaki zasób SAPHana działa wewnętrznie.
```pcs resource move SAPHana_HR2_00-primary```

Po wywołaniu polecenia przenoszenia przez poszczególne komputery komputerów klaster tworzy ograniczenia lokalizacji, aby osiągnąć przeniesienie zasobu. Te ograniczenia muszą zostać usunięte, aby umożliwić automatyczne przejście w tryb failover w przyszłości.
Aby je usunąć, możesz użyć poniższego polecenia.
```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* Zaloguj się do platformy HANA jako weryfikacja.

  * obniżony Host:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Promowany Host:

    ```
    hdbsql -i 00 -u system -p SAPhana10 -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

Po wybraniu opcji `AUTOMATED_REGISTER=false` nie można przełączyć z powrotem.

Jeśli ta opcja jest ustawiona na false, należy ponownie zarejestrować węzeł:

  
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```
  

Teraz Węzeł2, który był podstawowym, działa jako host pomocniczy.

Rozważ ustawienie tej opcji na wartość true, aby zautomatyzować rejestrację obniżonego hosta.

  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true

pcs cluster node clear node1
```
