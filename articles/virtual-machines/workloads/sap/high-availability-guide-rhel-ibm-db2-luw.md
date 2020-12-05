---
title: Konfigurowanie programu IBM DB2 HADR Cluster na maszynach wirtualnych platformy Azure w usłudze RHEL | Microsoft Docs
description: Zapewnienie wysokiej dostępności programu IBM DB2 LUW na maszynach wirtualnych platformy Azure RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: juergent
ms.openlocfilehash: 85f268990ac9e0c04cba1b9c409a232a24ce0d61
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608638"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Wysoka dostępność programu IBM Db2 LUW na maszynach wirtualnych platformy Azure w systemie Red Hat Enterprise Linux Server

Program IBM DB2 dla systemów Linux, UNIX i Windows (LUW) w [konfiguracji wysokiej dostępności i odzyskiwania po awarii (HADR cluster)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) składa się z jednego węzła, który uruchamia wystąpienie podstawowej bazy danych i co najmniej jeden węzeł, który uruchamia pomocnicze wystąpienie bazy danych. Zmiany w wystąpieniu podstawowej bazy danych są replikowane do wystąpienia pomocniczej bazy danych synchronicznie lub asynchronicznie, w zależności od konfiguracji. 

> [!NOTE]
> Ten artykuł zawiera odwołania do *głównego* terminu i serwera *podrzędnego*, warunki, które nie są już używane przez firmę Microsoft. Po usunięciu tych warunków z oprogramowania usuniemy je z tego artykułu.

W tym artykule opisano sposób wdrażania i konfigurowania maszyn wirtualnych platformy Azure, instalowania platformy klastra oraz instalowania programu IBM DB2 LUW z konfiguracją HADR Cluster. 

Artykuł nie obejmuje sposobu instalowania i konfigurowania programu IBM DB2 LUW z instalacją oprogramowania HADR Cluster lub SAP. Aby ułatwić Ci wykonywanie tych zadań, udostępniamy odwołania do podręczników instalacji oprogramowania SAP i IBM. Ten artykuł koncentruje się na częściach, które są specyficzne dla środowiska platformy Azure. 

Obsługiwane wersje programu IBM DB2 to 10,5 i nowsze, zgodnie z opisem w temacie SAP Uwaga [1928533].

Przed rozpoczęciem instalacji zapoznaj się z następującymi informacjami i dokumentacją SAP:

| Uwaga dotycząca oprogramowania SAP | Opis |
| --- | --- |
| [1928533] | Aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] | SAP na platformie Azure: wymagania wstępne dotyczące pomocy technicznej |
| [2178632] | Metryki monitorowania kluczy dla oprogramowania SAP na platformie Azure |
| [2191498] | SAP w systemie Linux z platformą Azure: ulepszone monitorowanie |
| [2243692] | Maszyna wirtualna z systemem Linux na platformie Azure (IaaS): problemy z licencją SAP |
| [2002167] | Red Hat Enterprise Linux 7. x: Instalowanie i uaktualnianie |
| [2694118] | Red Hat Enterprise Linux HA Add-On na platformie Azure |
| [1999351] | Rozwiązywanie problemów z ulepszonym monitorowaniem platformy Azure dla oprogramowania SAP |
| [2233094] | DB6: Aplikacje SAP na platformie Azure korzystające z programu IBM DB2 dla systemów Linux, UNIX i Windows — dodatkowe informacje |
| [1612105] | DB6: często zadawane pytania dotyczące bazy danych DB2 z HADR Cluster |


| Dokumentacja | 
| --- |
| [Strona typu wiki społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): zawiera wszystkie wymagane uwagi SAP dla systemu Linux |
| Przewodnik po [planowaniu i implementacji usługi Azure Virtual Machines dla oprogramowania SAP w systemie Linux][planning-guide] |
| [Wdrożenie Virtual Machines platformy Azure dla oprogramowania SAP w systemie Linux][deployment-guide] (ten artykuł) |
| [Wdrażanie systemu Azure Virtual Machines Database Management System (DBMS) dla oprogramowania SAP w systemie Linux][dbms-guide] — Przewodnik |
| [Obciążenie SAP na liście kontrolnej planowania i wdrażania platformy Azure][azr-sap-plancheck] |
| [Przegląd Add-On wysokiej dostępności dla Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Administracja Add-On wysokiej dostępności][rhel-ha-admin] |
| [Informacje o wysokiej dostępności Add-On][rhel-ha-ref] |
| [Zasady obsługi klastrów RHEL o wysokiej dostępności — Microsoft Azure Virtual Machines jako elementy członkowskie klastra][rhel-azr-supp]
| [Instalowanie i Konfigurowanie Red Hat Enterprise Linux 7,4 (i nowszych) High-Availability klastra na Microsoft Azure][rhel-azr-inst]
| [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM Db2 dla obciążenia SAP][dbms-db2] |
| [IBM DB2 HADR Cluster 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR Cluster 10,5][db2-hadr-10.5] |
| [Zasady obsługi klastrów RHEL o wysokiej dostępności — zarządzanie IBM DB2 dla systemów Linux, UNIX i Windows w klastrze][rhel-db2-supp]



## <a name="overview"></a>Omówienie
Aby zapewnić wysoką dostępność, program IBM DB2 LUW z HADR Cluster jest instalowany na co najmniej dwóch maszynach wirtualnych platformy Azure, które są wdrażane w [zestawie dostępności platformy Azure](../../windows/tutorial-availability-sets.md) lub w różnych [strefy dostępności platformy Azureach](./sap-ha-availability-zones.md). 

Poniższa Grafika przedstawia konfigurację dwóch maszyn wirtualnych platformy Azure na serwerze bazy danych. Zarówno maszyny wirtualne platformy Azure z serwerem bazy danych mają podłączony własny magazyn, jak i są uruchomione. W HADR Cluster, jedno wystąpienie bazy danych na jednej z maszyn wirtualnych platformy Azure ma rolę wystąpienia podstawowego. Wszyscy klienci są połączeni z wystąpieniem podstawowym. Wszystkie zmiany transakcji bazy danych są utrwalane lokalnie w dzienniku transakcji programu DB2. Ponieważ rekordy dziennika transakcji są utrwalane lokalnie, rekordy są transferowane za pośrednictwem protokołu TCP/IP do wystąpienia bazy danych na drugim serwerze bazy danych, serwerze rezerwy lub wystąpieniu gotowości. Wystąpienie gotowości aktualizuje lokalną bazę danych, przechodząc do przodu przesłanych rekordów dziennika transakcji. W ten sposób serwer rezerwy jest zsynchronizowany z serwerem podstawowym.

HADR Cluster jest tylko funkcją replikacji. Nie ma możliwości wykrywania błędów i automatycznego przejmowania lub pracy awaryjnej. Przejęcie lub przeniesienie do serwera rezerwy musi być inicjowane ręcznie przez administratora bazy danych. Aby osiągnąć Automatyczne przejęcie i wykrywanie niepowodzeń, można użyć funkcji klastrowania Pacemaker systemu Linux. Pacemaker monitoruje dwa wystąpienia serwera bazy danych. Gdy wystąpienie serwera podstawowej bazy danych ulegnie awarii, Pacemaker inicjuje *Automatyczne* przejęcie HADR Cluster przez serwer rezerwy. Pacemaker zapewnia również, że wirtualny adres IP jest przypisany do nowego serwera podstawowego.

![Przegląd wysokiej dostępności dla programu IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Aby serwery aplikacji SAP łączyły się z podstawową bazą danych, potrzebna jest nazwa hosta wirtualnego i wirtualny adres IP. W przypadku przejścia w tryb failover serwery aplikacji SAP będą łączyć się z nowym podstawowym wystąpieniem bazy danych. W środowisku platformy Azure [moduł równoważenia obciążenia platformy Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) jest wymagany do używania wirtualnego adresu IP w taki sposób, w jaki jest wymagany do HADR Cluster programu IBM DB2. 

Aby ułatwić zrozumienie, jak IBM DB2 LUW z HADR Cluster i Pacemaker, mieści się w Instalatorze systemu SAP o wysokiej dostępności, na poniższej ilustracji przedstawiono omówienie instalacji systemu SAP o wysokiej dostępności na podstawie bazy danych IBM DB2. Ten artykuł dotyczy tylko programu IBM DB2, ale zawiera odwołania do innych artykułów dotyczących sposobu konfigurowania innych składników systemu SAP.

![Przegląd środowiska wysokiej dostępności dla programu IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Ogólne omówienie wymaganych kroków
Aby wdrożyć konfigurację programu IBM DB2, należy wykonać następujące czynności:

  + Zaplanuj środowisko.
  + Wdróż maszyny wirtualne.
  + Aktualizacja RHEL Linux i konfigurowanie systemów plików.
  + Zainstaluj i skonfiguruj Pacemaker.
  + Skonfiguruj [klaster glusterfs][glusterfs] lub [Azure NetApp Files][anf-rhel]
  + Zainstaluj [ASCS/wykres wywołujących w oddzielnym klastrze][ascs-ha-rhel].
  + Zainstaluj bazę danych IBM DB2 z opcją Distributed/wysoka dostępność (SWPM).
  + Zainstaluj i Utwórz pomocniczy węzeł i wystąpienie bazy danych, a następnie skonfiguruj HADR Cluster.
  + Upewnij się, że HADR Cluster działa.
  + Zastosuj konfigurację Pacemaker, aby kontrolować IBM DB2.
  + Skonfiguruj Azure Load Balancer.
  + Zainstaluj serwery aplikacji podstawowych i oknach dialogowych.
  + Sprawdź i Dostosuj konfigurację serwerów aplikacji SAP.
  + Przeprowadź testy trybu failover i przejęcia.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planowanie infrastruktury platformy Azure pod kątem hostingu programu IBM DB2 LUW z HADR Cluster

Ukończ proces planowania przed wykonaniem wdrożenia. Planowanie kompiluje podstawę wdrożenia konfiguracji bazy danych DB2 z HADR Cluster na platformie Azure. Najważniejsze elementy, które muszą być częścią planowania dla IMB DB2 LUW (część bazy danych środowiska SAP), są wymienione w poniższej tabeli:

| Temat | Krótki opis |
| --- | --- |
| Definiowanie grup zasobów platformy Azure | Grupy zasobów, w których wdrażana jest maszyna wirtualna, Sieć wirtualna, Azure Load Balancer i inne zasoby. Może być istniejący lub nowy. |
| Definicja sieci wirtualnej/podsieci | Miejsce, w którym są wdrażane maszyny wirtualne dla programu IBM DB2 i Azure Load Balancer. Może być istniejący lub nowo utworzony. |
| Maszyny wirtualne obsługujące IBM DB2 LUW | Rozmiar maszyny wirtualnej, magazyn, Sieć, adres IP. |
| Nazwa hosta wirtualnego i wirtualny adres IP dla bazy danych programu IBM DB2| Wirtualny adres IP lub nazwa hosta, który jest używany do połączenia serwerów aplikacji SAP. **DB-virt-hostname**, **DB-virt-IP**. |
| Ogrodzenie platformy Azure | Metoda zapobiegania rozdzielonym sytuacje mózgów jest nieblokowana. |
| Azure Load Balancer | Użycie Basic lub Standard (zalecane), port sondy dla bazy danych DB2 (nasze zalecenie 62500) **sonda-port**. |
| Rozpoznawanie nazw| Jak rozpoznawanie nazw działa w środowisku. Usługa DNS jest zdecydowanie zalecana. Można użyć lokalnego pliku Hosts. |
    
Aby uzyskać więcej informacji o systemie Linux Pacemaker na platformie Azure, zobacz [Konfigurowanie Pacemaker na Red Hat Enterprise Linux na platformie Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Wdrożenie na Red Hat Enterprise Linux

Agent zasobów dla programu IBM DB2 LUW jest dołączany do dodatku Red Hat Enterprise Linux Server HA. W przypadku instalacji opisanej w tym dokumencie należy używać Red Hat Enterprise Linux dla oprogramowania SAP. Portal Azure Marketplace zawiera obraz dla Red Hat Enterprise Linux 7,4 dla oprogramowania SAP lub nowszego, który służy do wdrażania nowych maszyn wirtualnych platformy Azure. Zapoznaj się z różnymi modelami pomocy technicznej lub usług, które są oferowane przez Red Hat za pomocą witryny Azure Marketplace w przypadku wybrania obrazu maszyny wirtualnej na rynku maszyn wirtualnych platformy Azure.

### <a name="hosts-dns-updates"></a>Hosty: aktualizacje DNS
Utwórz listę wszystkich nazw hostów, w tym nazw hostów wirtualnych, i zaktualizuj serwery DNS, aby umożliwić prawidłowemu adresowi IP rozpoznawanie nazw hostów. Jeśli serwer DNS nie istnieje lub nie można zaktualizować i utworzyć wpisów DNS, należy użyć lokalnych plików hosta poszczególnych maszyn wirtualnych, które uczestniczą w tym scenariuszu. Jeśli używasz wpisów plików hosta, upewnij się, że wpisy są stosowane do wszystkich maszyn wirtualnych w środowisku systemowym SAP. Zalecamy jednak korzystanie z systemu DNS, który najlepiej rozszerza się na platformę Azure


### <a name="manual-deployment"></a>Wdrożenie ręczne

Upewnij się, że wybrany system operacyjny jest obsługiwany przez firmę IBM/SAP dla programu IBM DB2 LUW. Lista obsługiwanych wersji systemu operacyjnego dla maszyn wirtualnych platformy Azure i wydań programu DB2 jest dostępna w programie SAP Note [1928533]. Lista wersji systemu operacyjnego przez poszczególne wersje bazy danych DB2 jest dostępna w macierzy dostępności produktu SAP. Zdecydowanie zalecamy co najmniej Red Hat Enterprise Linux 7,4 dla oprogramowania SAP z powodu ulepszeń wydajności związanych z platformą Azure w tych lub nowszych wersjach Red Hat Enterprise Linux.

1. Utwórz lub wybierz grupę zasobów.
1. Utwórz lub wybierz sieć wirtualną i podsieć.
1. Utwórz zestaw dostępności platformy Azure lub Wdróż strefę dostępności.
    + W polu zestaw dostępności Ustaw maksymalną liczbę domen aktualizacji na 2.
1. Utwórz maszynę wirtualną 1.
    + Użyj Red Hat Enterprise Linux dla obrazu SAP w portalu Azure Marketplace.
    + Wybierz zestaw dostępności platformy Azure utworzony w kroku 3 lub wybierz pozycję strefa dostępności.
1.  Utwórz maszynę wirtualną 2.
    + Użyj Red Hat Enterprise Linux dla obrazu SAP w portalu Azure Marketplace.
    + Wybierz zestaw dostępności platformy Azure, który został utworzony w kroku 3, lub wybierz opcję Strefa dostępności (nie ta sama strefa, co w kroku 3).
1. Dodaj dyski danych do maszyn wirtualnych, a następnie zapoznaj się z zaleceniem Instalatora systemu plików w artykule [IBM DB2 Azure Virtual Machines DBMS wdrożenie oprogramowania SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Tworzenie klastra Pacemaker
    
Aby utworzyć podstawowy klaster Pacemaker dla tego serwera programu IBM DB2, zobacz [Konfigurowanie Pacemaker na Red Hat Enterprise Linux na platformie Azure][rhel-pcs-azr]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instalowanie środowiska IBM DB2 LUW i SAP

Przed rozpoczęciem instalacji środowiska SAP opartego na programie IBM DB2 LUW zapoznaj się z następującą dokumentacją:

+ Dokumentacja platformy Azure
+ Dokumentacja SAP
+ Dokumentacja IBM

Linki do tej dokumentacji znajdują się w sekcji wprowadzającej w tym artykule.

Sprawdź instrukcje instalacji SAP dotyczące instalowania aplikacji opartych na NetWeaver w programie IBM DB2 LUW.
Przewodniki można znaleźć w portalu pomocy SAP za pomocą narzędzia do [wyszukiwania w przewodniku instalacji SAP][sap-instfind].

Aby zmniejszyć liczbę przewodników wyświetlanych w portalu, można ustawić następujące filtry:
- Chcę: "Zainstaluj nowy system"
- Moja baza danych: "IBM DB2 dla systemów Linux, UNIX i Windows"
- Dodatkowe filtry dla wersji SAP NetWeaver, konfiguracji stosu lub systemu operacyjnego

#### <a name="red-hat-firewall-rules"></a>Reguły zapory Red Hat
Red Hat Enterprise Linux domyślnie włączona Zapora. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Wskazówki dotyczące instalacji dotyczące konfigurowania programu IBM DB2 LUW z HADR Cluster

Aby skonfigurować podstawowe wystąpienie bazy danych programu IBM DB2 LUW:

- Użyj opcji wysoka dostępność lub dystrybuowanie.
- Zainstaluj wystąpienie SAP ASCS/wykres WYWOŁUJĄCYCH i Database.
- Utwórz kopię zapasową nowo zainstalowanej bazy danych.

> [!IMPORTANT] 
> Zanotuj "port komunikacji bazy danych", który jest ustawiany podczas instalacji. Musi to być ten sam numer portu dla obu wystąpień bazy danych.
>![Definicja portu SAP SWPM](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Ustawienia programu IBM DB2 HADR Cluster dla platformy Azure

   W przypadku korzystania z Agenta ogrodzenia Pacemaker platformy Azure ustaw następujące parametry:

   - Czas trwania okna równorzędnego HADR Cluster (w sekundach) (HADR_PEER_WINDOW) = 240  
   - Wartość limitu czasu HADR Cluster (HADR_TIMEOUT) = 45

Zalecamy stosowanie powyższych parametrów na podstawie wstępnego testowania trybu failover/przejęcia. Wymagane jest przetestowanie pod kątem poprawnej funkcjonalności trybu failover i przejęcia z tymi ustawieniami parametrów. Ponieważ poszczególne konfiguracje mogą się różnić, parametry mogą wymagać korekty. 

> [!NOTE]
> Specyficzne dla programu IBM DB2 z konfiguracją HADR Cluster z normalnym uruchamianiem: wystąpienie bazy danych pomocniczej lub zapasowej musi być uruchomione, aby można było uruchomić podstawowe wystąpienie bazy danych.

   
> [!NOTE]
> Aby przeprowadzić instalację i konfigurację charakterystyczną dla platformy Azure i Pacemaker: podczas procedury instalacji za pomocą Menedżera aprowizacji oprogramowania SAP, istnieje jawne pytanie o wysoką dostępność dla programu IBM DB2 LUW:
>+ Nie wybieraj programu **IBM DB2 pureScale**.
>+ Nie wybieraj opcji **Zainstaluj program IBM Tivoli system Automation dla wielu platform**.
>+ Nie wybieraj opcji **Generuj pliki konfiguracji klastra**.
>![SAP SWPM — opcje wysokiej dostępności](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Aby skonfigurować serwer bazy danych w stanie gotowości przy użyciu procedury kopiowania jednorodnego systemu SAP, wykonaj następujące kroki:

1. Wybierz opcję **kopiowania systemu** > wystąpienia **Target systems**  >  **rozproszonej**  >  **bazy danych** w systemie docelowym.
1. Jako metodę kopiowania wybierz **jednorodny system** , aby można było użyć kopii zapasowej do przywrócenia kopii zapasowej w wystąpieniu serwera w stanie gotowości.
1. Po dojściu do kroku zakończenia, aby przywrócić bazę danych jednorodnej kopii systemu, zamknij Instalatora. Przywróć bazę danych z kopii zapasowej hosta podstawowego. Wszystkie kolejne etapy instalacji zostały już wykonane na podstawowym serwerze bazy danych.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Reguły zapory Red Hat dla programu DB2 HADR Cluster
Dodaj reguły zapory, aby zezwalać na działanie ruchu do bazy danych DB2 i między programem DB2 for HADR cluster:
+ Port komunikacji z bazą danych. W przypadku używania partycji należy również dodać te porty.
+ Port HADR Cluster (wartość HADR_LOCAL_SVC parametru DB2)
+ Port sondy platformy Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>Sprawdzenie HADR Cluster IBM DB2
W celach demonstracyjnych i procedurach opisanych w tym artykule identyfikator SID bazy danych to **ID2**.

Po skonfigurowaniu HADR Cluster, gdy stan jest RÓWNORZĘDNy i połączony w węzłach głównych i w stanie wstrzymania, wykonaj następujące sprawdzenie:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Konfiguracja programu DB2 Pacemaker

W przypadku korzystania z Pacemaker w celu automatycznego przełączania do trybu failover w przypadku awarii węzła należy odpowiednio skonfigurować wystąpienia bazy danych DB2 i Pacemaker. W tej sekcji opisano ten typ konfiguracji.

Następujące elementy są poprzedzone prefiksem:

- **[A]**: dotyczy wszystkich węzłów
- **[1]**: dotyczy tylko węzła 1 
- **[2]**: dotyczy tylko węzła 2

**[A]** wymagania wstępne dotyczące konfiguracji Pacemaker:
1. Zamknij oba serwery baz danych z użytkownikiem DB2 \<sid> z db2stop.
1. Zmień środowisko powłoki dla użytkownika programu DB2 \<sid> na */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Konfiguracja Pacemaker

**[1]** konfiguracja Pacemaker programu IBM DB2 HADR cluster:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** Utwórz zasoby programu IBM DB2:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** uruchamianie zasobów programu IBM DB2:
* Pacemaker z trybu konserwacji.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** upewnij się, że klaster ma stan OK i że wszystkie zasoby są uruchomione. Węzeł, na którym są uruchomione zasoby, nie jest ważny.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [AZ-idb01 AZ-idb02]

Pełna lista zasobów:

 rsc_st_azure (stonith: fence_azure_arm): uruchomiono AZ-idb01 Master/Slave Set: Db2_HADR_ID2-Master [Db2_HADR_ID2] Masters: [AZ-idb01] slave: [AZ-idb02] grupa zasobów: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (OCF:: puls: IPaddr2): uruchomiono AZ-idb01 nc_db2id2_ID2 (OCF:: puls: Azure-lb): uruchomiono AZ-idb01

Stan demona: Corosync: Active/disabled Pacemaker: Active/disabled pcsd: Active/Enabled
</pre>

> [!IMPORTANT]
> Aby zarządzać klastrowanym wystąpieniem bazy danych Pacemaker, należy użyć narzędzi Pacemaker. W przypadku korzystania z poleceń programu DB2, takich jak db2stop, Pacemaker wykrywa akcję jako niepowodzenie zasobów. W przypadku przeprowadzania konserwacji można umieścić węzły lub zasoby w trybie konserwacji. Pacemaker wstrzymuje zasoby monitorowania i można używać zwykłych poleceń administracyjnych programu DB2.


### <a name="configure-azure-load-balancer"></a>Konfigurowanie modułu Azure Load Balancer
Aby skonfigurować Azure Load Balancer, zalecamy użycie [jednostki SKU usługa Load Balancer w warstwie Standardowa platformy Azure](../../../load-balancer/load-balancer-overview.md) , a następnie wykonanie następujących czynności:

> [!NOTE]
> Jednostka SKU usługa Load Balancer w warstwie Standardowa ma ograniczenia dostępu do publicznych adresów IP z węzłów znajdujących się pod Load Balancer. [Publiczna łączność z punktem końcowym firmy Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) zawiera opis sposobów włączania tych węzłów w celu uzyskania dostępu do publicznych adresów IP

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.  


1. Utwórz pulę adresów IP frontonu:

   a. W Azure Portal Otwórz Azure Load Balancer, wybierz pozycję **Pula adresów IP frontonu**, a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej puli adresów IP frontonu (na przykład **DB2-Connection**).

   c. Ustaw **przypisanie** na **static**, a następnie wprowadź adres IP **Virtual-IP** zdefiniowany na początku.

   d. Wybierz przycisk **OK**.

   e. Po utworzeniu nowej puli adresów IP frontonu Zanotuj adres IP puli.

1. Utwórz pulę zaplecza:

   a. W Azure Portal Otwórz Azure Load Balancer, wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej puli zaplecza (na przykład **DB2-zaplecze**).

   c. Wybierz pozycję **Dodaj maszynę wirtualną**.

   d. Wybierz zestaw dostępności lub maszyny wirtualne hostujący bazę danych IBM DB2 utworzoną w poprzednim kroku.

   e. Wybierz Maszyny wirtualne w klastrze programu IBM DB2.

   f. Wybierz przycisk **OK**.

1. Utwórz sondę kondycji:

   a. W Azure Portal Otwórz Azure Load Balancer, wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej sondy kondycji (na przykład **DB2-HP**).

   c. Wybierz pozycję **TCP** jako protokół i port **62500**. Pozostaw wartość **interwału** ustawioną na **5** i pozostaw wartość **progową złej kondycji** ustawioną na **2**.

   d. Wybierz przycisk **OK**.

1. Utwórz reguły równoważenia obciążenia:

   a. W Azure Portal Otwórz Azure Load Balancer, wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej reguły Load Balancer (na przykład **DB2-SID**).

   c. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **DB2-fronton**).

   d. Pozostaw **Protokół** ustawiony na **TCP** i wprowadź port *komunikacyjny bazy danych* portu.

   e. Zwiększ **limit czasu bezczynności** do 30 minut.

   f. Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.

   przykład Wybierz przycisk **OK**.

**[A]** Dodaj regułę zapory dla portu sondy:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Wprowadzanie zmian w profilach SAP do używania wirtualnego adresu IP na potrzeby połączenia
Aby nawiązać połączenie z podstawowym wystąpieniem konfiguracji HADR Cluster, warstwa aplikacji SAP musi używać wirtualnego adresu IP, który został zdefiniowany i skonfigurowany dla Azure Load Balancer. Wymagane są następujące zmiany:

/sapmnt/ \<SID> /Profile/default. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/ \<SID> /global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Zainstaluj serwery aplikacji podstawowej i okna dialogowego

W przypadku instalowania serwerów aplikacji podstawowych i okien dialogowych z konfiguracją programu DB2 HADR Cluster należy użyć nazwy hosta wirtualnego pobranej do konfiguracji. 

W przypadku wykonania instalacji przed utworzeniem konfiguracji programu DB2 HADR Cluster należy wprowadzić zmiany zgodnie z opisem w poprzedniej sekcji i w artykule dotyczącym stosów języka SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP + Java lub systemy stosu Java JDBC — sprawdzanie adresów URL

Użyj narzędzia konfiguracji J2EE, aby sprawdzić lub zaktualizować adres URL JDBC. Ponieważ narzędzie konfiguracji J2EE jest narzędziem graficznym, musisz mieć zainstalowany serwer X:
 
1. Zaloguj się do podstawowego serwera aplikacji wystąpienia J2EE i wykonaj następujące operacje:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. W lewej ramce wybierz pozycję **Magazyn zabezpieczeń**.
1. W prawej ramce wybierz klucz `jdbc/pool/\<SAPSID>/url` .
1. Zmień nazwę hosta w adresie URL JDBC na nazwę hosta wirtualnego.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Wybierz pozycję **Dodaj**.
1. Aby zapisać zmiany, wybierz ikonę dysku w lewym górnym rogu.
1. Zamknij narzędzie konfiguracji.
1. Uruchom ponownie wystąpienie środowiska Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurowanie archiwizacji dzienników dla Instalatora HADR Cluster
Aby skonfigurować funkcję archiwizowania dziennika bazy danych DB2 dla Instalatora HADR Cluster, zaleca się skonfigurowanie zarówno podstawowej, jak i w stanie gotowości do automatycznego pobierania dzienników ze wszystkich lokalizacji archiwum dzienników. Zarówno podstawowa, jak i zapasowa baza danych muszą mieć możliwość pobierania plików archiwum dzienników ze wszystkich lokalizacji archiwum dzienników, do których jeden z wystąpień bazy danych może archiwizować pliki dziennika. 

Archiwizowanie dzienników jest wykonywane tylko przez podstawową bazę danych. Jeśli zmienisz role HADR Cluster serwerów baz danych lub wystąpi awaria, Nowa podstawowa baza danych odpowiada za archiwizowanie dzienników. Jeśli skonfigurowano wiele lokalizacji archiwum dzienników, dzienniki mogą być archiwizowane dwukrotnie. W przypadku lokalnego lub zdalnego przechwycenia może być również konieczne ręczne skopiowanie zarchiwizowanych dzienników ze starego serwera podstawowego do aktywnej lokalizacji dziennika na nowym serwerze podstawowym.

Zalecamy skonfigurowanie wspólnego udziału NFS lub GlusterFS, w którym dzienniki są zapisywane z obu węzłów. Udział NFS lub GlusterFS musi mieć wysoką dostępność. 

Możesz użyć istniejących udziałów NFS o wysokiej dostępności lub GlusterFS do transportowania lub katalogu profilów. Aby uzyskać więcej informacji, zobacz:

- [System GlusterFS na maszynach wirtualnych platformy Azure z systemem Red Hat Enterprise Linux dla oprogramowania SAP NetWeaver][glusterfs] 
- [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na Red Hat Enterprise Linux z Azure NetApp Files dla aplikacji SAP][anf-rhel]
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (aby utworzyć udziały NFS)

## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano, jak można testować konfigurację programu DB2 HADR Cluster. Każdy test zakłada, że program IBM DB2 Primary działa na maszynie wirtualnej *AZ-idb01* . Użytkownik mający uprawnienia sudo lub główny (niezalecane) musi być używany.

Początkowy stan wszystkich przypadków testowych został wyjaśniony tutaj: (stan crm_mon-r lub komputery)

- **stan komputerów** jest migawką stanu Pacemaker w czasie wykonywania 
- **crm_mon-r** jest ciągłym wyjściem stanu Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Oryginalny stan w systemie SAP jest opisany w temacie Transaction DBACOCKPIT > Configuration > Overview, jak pokazano na poniższej ilustracji:

![DBACockpit — wstępna migracja](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Testowanie przejęcia programu IBM DB2


> [!IMPORTANT] 
> Przed rozpoczęciem testu upewnij się, że:
> * Pacemaker nie ma żadnych akcji zakończonych niepowodzeniem (stan komputerów).
> * Brak ograniczeń lokalizacji (pozostałości testu migracji)
> * Synchronizacja programu IBM DB2 HADR Cluster działa. Skontaktuj się z użytkownikiem DB2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Przeprowadź migrację węzła, w którym działa podstawowa baza danych DB2, wykonując następujące polecenie:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Po zakończeniu migracji dane wyjściowe stanu programu CRM wyglądają następująco:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Oryginalny stan w systemie SAP jest opisany w temacie Transaction DBACOCKPIT > Configuration > Overview, jak pokazano na poniższej ilustracji:

![DBACockpit — po migracji](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Migracja zasobów przy użyciu pozycji "Przenoszenie zasobów komputerów" tworzy ograniczenia lokalizacji. Ograniczenia lokalizacji w tym przypadku uniemożliwiają uruchomienie wystąpienia programu IBM DB2 w programie AZ-idb01. Jeśli ograniczenia lokalizacji nie zostaną usunięte, zasób nie może powrócić do trybu failover.

Usuń ograniczenie lokalizacji i węzeł gotowości zostanie uruchomiony przy użyciu AZ-idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
I stan klastra zmieni się na:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit — ograniczenie lokalizacji usuniętej](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Przeprowadź migrację zasobu z powrotem do *AZ-idb01* i wyczyść ograniczenia lokalizacji
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **przenoszenie zasobów komputerów \<res_name> <host> :** tworzy ograniczenia lokalizacji i może powodować problemy z przejęciem
- **niezrozumiały \<res_name> zasób komputerów**: czyści ograniczenia lokalizacji
- **oczyszczanie \<res_name> zasobów komputerów**: czyści wszystkie błędy zasobu

### <a name="test-a-manual-takeover"></a>Testowanie ręcznego przejęcia

Ręczne przejęcie można przetestować, zatrzymując usługę Pacemaker w węźle *AZ-idb01* :
<pre><code>systemctl stop pacemaker</code></pre>

stan na *AZ-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Po przejściu w tryb failover możesz ponownie uruchomić usługę przy użyciu *AZ-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Kasuj proces programu DB2 w węźle, który uruchamia podstawową bazę danych HADR Cluster

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Wystąpienie bazy danych DB2 kończy się niepowodzeniem, a Pacemaker przeniesie węzeł główny i zgłosi następujący stan:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker uruchomi ponownie wystąpienie podstawowej bazy danych DB2 w tym samym węźle lub przejdzie do trybu failover w węźle, w którym jest uruchomione pomocnicze wystąpienie bazy danych, a zostanie zgłoszony błąd.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Kasuj proces programu DB2 w węźle, w którym działa wystąpienie pomocniczej bazy danych

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Określony węzeł nie powiódł się i zgłoszono błąd
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Wystąpienie bazy danych DB2 zostanie uruchomione ponownie w roli pomocniczej, która została wcześniej przypisana.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zatrzymaj bazę danych za pośrednictwem db2stop Force w węźle, w którym działa wystąpienie podstawowej bazy danych HADR Cluster

Jako użytkownik programu DB2 \<sid> wykona db2stop polecenia:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Wykryto błąd:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Wystąpienie pomocniczej bazy danych DB2 HADR Cluster zostało podwyższone do roli podstawowej.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Awaria maszyny wirtualnej z uruchomionym wystąpieniem podstawowej bazy danych HADR Cluster z "zatrzymaniem"

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

W takim przypadku Pacemaker wykryje, że węzeł, na którym działa podstawowe wystąpienie bazy danych, nie odpowiada.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

Następnym krokiem jest sprawdzenie, czy występuje *podzielona sytuacja mózgów* . Po zakończeniu działania węzła, który wykrył, że węzeł, który ostatnio uruchomił podstawowe wystąpienie bazy danych, nie działa w trybie failover zasobów.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


W przypadku awaryjnego jądra węzeł, którego nie powiodło się, zostanie poddany niepowodzeniem przez Agenta ogrodzenia. Gdy uszkodzony węzeł nie zostanie przywrócony do trybu online, należy uruchomić klaster Pacemaker przez
<pre><code>sudo pcs cluster start</code></pre> uruchamia wystąpienie bazy danych DB2 w roli pomocniczej.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Następne kroki
- [Architektura i scenariusze wysokiej dostępności dla oprogramowania SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
- [Konfigurowanie Pacemaker Red Hat Enterprise Linux na platformie Azure][rhel-pcs-azr]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
