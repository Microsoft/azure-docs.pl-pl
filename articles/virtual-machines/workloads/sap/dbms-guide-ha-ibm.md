---
title: Konfigurowanie programu IBM DB2 HADR Cluster na maszynach wirtualnych platformy Azure | Microsoft Docs
description: Zapewnienie wysokiej dostępności programu IBM DB2 LUW na maszynach wirtualnych platformy Azure.
author: msjuergent
ms.service: virtual-machines
ms.topic: article
ms.date: 10/16/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: 88a84cd90efb42ea096cad647d75f1c3736426f4
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146436"
---
# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Wysoka dostępność programu IBM DB2 LUW na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Pacemaker

Program IBM DB2 dla systemów Linux, UNIX i Windows (LUW) w [konfiguracji wysokiej dostępności i odzyskiwania po awarii (HADR cluster)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) składa się z jednego węzła, który uruchamia wystąpienie podstawowej bazy danych i co najmniej jeden węzeł, który uruchamia pomocnicze wystąpienie bazy danych. Zmiany w wystąpieniu podstawowej bazy danych są replikowane do wystąpienia pomocniczej bazy danych synchronicznie lub asynchronicznie, w zależności od konfiguracji. 

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
| [1984787] | SUSE LINUX Enterprise Server 12: uwagi dotyczące instalacji |
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
| [SUSE Linux Enterprise Server for SAP Applications 12 SP4 Best Practices][sles-for-sap-bp] |
| [Rozszerzenie systemu SUSE Linux Enterprise High Availability 12 SP4][sles-ha-guide] |
| [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM Db2 dla obciążenia SAP][dbms-db2] |
| [IBM DB2 HADR Cluster 11,1][db2-hadr-11.1] |
| [IBM DB2 HADR Cluster R 10,5][db2-hadr-10.5] |

## <a name="overview"></a>Omówienie
Aby zapewnić wysoką dostępność, program IBM DB2 LUW z HADR Cluster jest instalowany na co najmniej dwóch maszynach wirtualnych platformy Azure, które są wdrażane w [zestawie dostępności platformy Azure](../../windows/tutorial-availability-sets.md) lub w różnych [strefy dostępności platformy Azureach](./sap-ha-availability-zones.md). 

Poniższa Grafika przedstawia konfigurację dwóch maszyn wirtualnych platformy Azure na serwerze bazy danych. Zarówno maszyny wirtualne platformy Azure z serwerem bazy danych mają podłączony własny magazyn, jak i są uruchomione. W HADR Cluster, jedno wystąpienie bazy danych na jednej z maszyn wirtualnych platformy Azure ma rolę wystąpienia podstawowego. Wszyscy klienci są połączeni z tym wystąpieniem podstawowym. Wszystkie zmiany transakcji bazy danych są utrwalane lokalnie w dzienniku transakcji programu DB2. Ponieważ rekordy dziennika transakcji są utrwalane lokalnie, rekordy są transferowane za pośrednictwem protokołu TCP/IP do wystąpienia bazy danych na drugim serwerze bazy danych, serwerze rezerwy lub wystąpieniu gotowości. Wystąpienie gotowości aktualizuje lokalną bazę danych, przechodząc do przodu przesłanych rekordów dziennika transakcji. W ten sposób serwer rezerwy jest zsynchronizowany z serwerem podstawowym.

HADR Cluster jest tylko funkcją replikacji. Nie ma możliwości wykrywania błędów i automatycznego przejmowania lub pracy awaryjnej. Przejęcie lub przeniesienie do serwera rezerwy musi być inicjowane ręcznie przez administratora bazy danych. Aby osiągnąć Automatyczne przejęcie i wykrywanie niepowodzeń, można użyć funkcji klastrowania Pacemaker systemu Linux. Pacemaker monitoruje dwa wystąpienia serwera bazy danych. Gdy wystąpienie serwera podstawowej bazy danych ulegnie awarii, Pacemaker inicjuje *Automatyczne* przejęcie HADR Cluster przez serwer rezerwy. Pacemaker zapewnia również, że wirtualny adres IP jest przypisany do nowego serwera podstawowego.

![Przegląd wysokiej dostępności dla programu IBM DB2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Aby serwery aplikacji SAP łączyły się z podstawową bazą danych, potrzebna jest nazwa hosta wirtualnego i wirtualny adres IP. W przypadku przejścia w tryb failover serwery aplikacji SAP będą łączyć się z nowym podstawowym wystąpieniem bazy danych. W środowisku platformy Azure [moduł równoważenia obciążenia platformy Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) jest wymagany do używania wirtualnego adresu IP w taki sposób, w jaki jest wymagany do HADR Cluster programu IBM DB2. 

Aby ułatwić zrozumienie, jak IBM DB2 LUW z HADR Cluster i Pacemaker, mieści się w Instalatorze systemu SAP o wysokiej dostępności, na poniższej ilustracji przedstawiono omówienie instalacji systemu SAP o wysokiej dostępności na podstawie bazy danych IBM DB2. Ten artykuł dotyczy tylko programu IBM DB2, ale zawiera odwołania do innych artykułów dotyczących sposobu konfigurowania innych składników systemu SAP.

![Przegląd środowiska wysokiej dostępności dla programu IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Ogólne omówienie wymaganych kroków
Aby wdrożyć konfigurację programu IBM DB2, należy wykonać następujące czynności:

  + Zaplanuj środowisko.
  + Wdróż maszyny wirtualne.
  + Zaktualizuj system SUSE Linux i skonfiguruj systemy plików.
  + Zainstaluj i skonfiguruj Pacemaker.
  + Zainstaluj system [plików NFS o wysokiej][nfs-ha]dostępności.
  + Zainstaluj [ASCS/wykres wywołujących w oddzielnym klastrze][ascs-ha].
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
| Ogrodzenie platformy Azure | Ogrodzenie platformy Azure lub SBD (zdecydowanie zalecane). Metoda zapobiegania rozdzielonym sytuacjom mózgów. |
| MASZYNA WIRTUALNA W SBD | SBD rozmiar maszyny wirtualnej, magazyn i sieć. |
| Azure Load Balancer | Użycie Basic lub Standard (zalecane), port sondy dla bazy danych DB2 (nasze zalecenie 62500) **sonda-port**. |
| Rozpoznawanie nazw| Jak rozpoznawanie nazw działa w środowisku. Usługa DNS jest zdecydowanie zalecana. Można użyć lokalnego pliku Hosts. |
    
Aby uzyskać więcej informacji o systemie Linux Pacemaker na platformie Azure, zobacz [Konfigurowanie Pacemaker SUSE Linux Enterprise Server na platformie Azure](./high-availability-guide-suse-pacemaker.md).

## <a name="deployment-on-suse-linux"></a>Wdrażanie w systemie SUSE Linux

Agent zasobów dla programu IBM DB2 LUW jest zawarty w SUSE Linux Enterprise Server dla aplikacji SAP. Aby zapoznać się z konfiguracją opisaną w tym dokumencie, należy użyć serwera SUSE Linux dla aplikacji SAP. Portal Azure Marketplace zawiera obraz dla programu SUSE Enterprise Server for SAP Applications 12, który służy do wdrażania nowych maszyn wirtualnych platformy Azure. Zapoznaj się z różnymi modelami pomocy technicznej lub usług oferowanymi przez SUSE w portalu Azure Marketplace w przypadku wybrania obrazu maszyny wirtualnej na rynku maszyn wirtualnych platformy Azure. 

### <a name="hosts-dns-updates"></a>Hosty: aktualizacje DNS
Utwórz listę wszystkich nazw hostów, w tym nazw hostów wirtualnych, i zaktualizuj serwery DNS, aby umożliwić prawidłowemu adresowi IP rozpoznawanie nazw hostów. Jeśli serwer DNS nie istnieje lub nie można zaktualizować i utworzyć wpisów DNS, należy użyć lokalnych plików hosta poszczególnych maszyn wirtualnych, które uczestniczą w tym scenariuszu. Jeśli używasz wpisów plików hosta, upewnij się, że wpisy są stosowane do wszystkich maszyn wirtualnych w środowisku systemowym SAP. Zalecamy jednak korzystanie z systemu DNS, który najlepiej rozszerza się na platformę Azure


### <a name="manual-deployment"></a>Wdrożenie ręczne

Upewnij się, że wybrany system operacyjny jest obsługiwany przez firmę IBM/SAP dla programu IBM DB2 LUW. Lista obsługiwanych wersji systemu operacyjnego dla maszyn wirtualnych platformy Azure i wydań programu DB2 jest dostępna w programie SAP Note [1928533]. Lista wersji systemu operacyjnego przez poszczególne wersje bazy danych DB2 jest dostępna w macierzy dostępności produktu SAP. Zdecydowanie zalecamy co najmniej SLES 12 SP4 z powodu ulepszeń wydajności związanych z platformą Azure w tej lub nowszej wersji systemu SUSE Linux.

1. Utwórz lub wybierz grupę zasobów.
1. Utwórz lub wybierz sieć wirtualną i podsieć.
1. Utwórz zestaw dostępności platformy Azure lub Wdróż strefę dostępności.
    + W polu zestaw dostępności Ustaw maksymalną liczbę domen aktualizacji na 2.
1. Utwórz maszynę wirtualną 1.
    + Użyj obrazu SLES for SAP w portalu Azure Marketplace.
    + Wybierz zestaw dostępności platformy Azure utworzony w kroku 3 lub wybierz pozycję strefa dostępności.
1.  Utwórz maszynę wirtualną 2.
    + Użyj obrazu SLES for SAP w portalu Azure Marketplace.
    + Wybierz zestaw dostępności platformy Azure, który został utworzony w kroku 3, lub wybierz opcję Strefa dostępności (nie ta sama strefa, co w kroku 3).
1. Dodaj dyski danych do maszyn wirtualnych, a następnie zapoznaj się z zaleceniem Instalatora systemu plików w artykule [IBM DB2 Azure Virtual Machines DBMS wdrożenie oprogramowania SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Tworzenie klastra Pacemaker
    
Aby utworzyć podstawowy klaster Pacemaker dla tego serwera programu IBM DB2, zobacz [Konfigurowanie Pacemaker na SUSE Linux Enterprise Server na platformie Azure][sles-pacemaker]. 

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

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Wskazówki dotyczące instalacji dotyczące konfigurowania programu IBM DB2 LUW z HADR Cluster

Aby skonfigurować podstawowe wystąpienie bazy danych programu IBM DB2 LUW:

- Użyj opcji wysoka dostępność lub dystrybuowanie.
- Zainstaluj wystąpienie SAP ASCS/wykres WYWOŁUJĄCYCH i Database.
- Utwórz kopię zapasową nowo zainstalowanej bazy danych.


> [!IMPORTANT] 
> Zanotuj "port komunikacji bazy danych", który jest ustawiany podczas instalacji. Musi to być ten sam numer portu dla obu wystąpień bazy danych

Aby skonfigurować serwer bazy danych w stanie gotowości przy użyciu procedury kopiowania jednorodnego systemu SAP, wykonaj następujące kroki:

1. Wybierz opcję **kopiowania systemu** > wystąpienia **Target systems**  >  **rozproszonej**  >  **bazy danych**w systemie docelowym.
1. Jako metodę kopiowania wybierz **jednorodny system** , aby można było użyć kopii zapasowej do przywrócenia kopii zapasowej w wystąpieniu serwera w stanie gotowości.
1. Po dojściu do kroku zakończenia, aby przywrócić bazę danych jednorodnej kopii systemu, zamknij Instalatora. Przywróć bazę danych z kopii zapasowej hosta podstawowego. Wszystkie kolejne etapy instalacji zostały już wykonane na podstawowym serwerze bazy danych.
1. Skonfiguruj HADR Cluster dla programu IBM DB2.

   > [!NOTE]
   > Aby przeprowadzić instalację i konfigurację charakterystyczną dla platformy Azure i Pacemaker: podczas procedury instalacji za pomocą Menedżera aprowizacji oprogramowania SAP, istnieje jawne pytanie o wysoką dostępność dla programu IBM DB2 LUW:
   >+ Nie wybieraj programu **IBM DB2 pureScale**.
   >+ Nie wybieraj opcji **Zainstaluj program IBM Tivoli system Automation dla wielu platform**.
   >+ Nie wybieraj opcji **Generuj pliki konfiguracji klastra**.

   W przypadku korzystania z urządzenia SBD dla systemu Linux Pacemaker ustaw następujące parametry programu DB2 HADR cluster:
   + Czas trwania okna równorzędnego HADR Cluster (w sekundach) (HADR_PEER_WINDOW) = 300  
   + Wartość limitu czasu HADR Cluster (HADR_TIMEOUT) = 60

   W przypadku korzystania z Agenta ogrodzenia Pacemaker platformy Azure ustaw następujące parametry:
   + Czas trwania okna równorzędnego HADR Cluster (w sekundach) (HADR_PEER_WINDOW) = 900  
   + Wartość limitu czasu HADR Cluster (HADR_TIMEOUT) = 60

Zalecamy stosowanie powyższych parametrów na podstawie wstępnego testowania trybu failover/przejęcia. Wymagane jest przetestowanie pod kątem poprawnej funkcjonalności trybu failover i przejęcia z tymi ustawieniami parametrów. Ponieważ poszczególne konfiguracje mogą się różnić, parametry mogą wymagać korekty. 

> [!IMPORTANT]
> Specyficzne dla programu IBM DB2 z konfiguracją HADR Cluster z normalnym uruchamianiem: wystąpienie bazy danych pomocniczej lub zapasowej musi być uruchomione, aby można było uruchomić podstawowe wystąpienie bazy danych.

W celach demonstracyjnych i procedurach opisanych w tym artykule identyfikator SID bazy danych to **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Sprawdzenie HADR Cluster IBM DB2
Po skonfigurowaniu HADR Cluster, gdy stan jest RÓWNORZĘDNy i połączony w węzłach głównych i w stanie wstrzymania, wykonaj następujące sprawdzenie:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Konfiguracja programu DB2 Pacemaker

W przypadku korzystania z Pacemaker w celu automatycznego przełączania do trybu failover w przypadku awarii węzła należy odpowiednio skonfigurować wystąpienia bazy danych DB2 i Pacemaker. W tej sekcji opisano ten typ konfiguracji.

Następujące elementy są poprzedzone prefiksem:

- **[A]**: dotyczy wszystkich węzłów
- **[1]**: dotyczy tylko węzła 1 
- **[2]**: dotyczy tylko węzła 2

**[A]** wymagania wstępne dotyczące konfiguracji Pacemaker:
1. Zamknij oba serwery baz danych z użytkownikiem DB2 \<sid> z db2stop.
1. Zmień środowisko powłoki dla użytkownika programu DB2 na \<sid> */bin/ksh*. Zalecamy korzystanie z narzędzia YaST. 


### <a name="pacemaker-configuration"></a>Konfiguracja Pacemaker

> [!IMPORTANT]
> Ostatnie testy ujawniły sytuacje, w których netcat przestaje odpowiadać na żądania z powodu zaległości i ograniczenia obsługi tylko jednego połączenia. Zasób netcat przestaje nasłuchiwać żądań modułu równoważenia obciążenia platformy Azure, a przestawny adres IP stał się niedostępny.  
> W przypadku istniejących klastrów Pacemaker zalecamy w przeszłości zastępowanie netcat socat. Obecnie zalecamy korzystanie z agenta zasobów Azure-lb, który jest częścią agentów zasobów pakietu, z następującymi wymaganiami dotyczącymi wersji pakietu:
> - W przypadku SLES 12 SP4/SP5 wersja musi być co najmniej równa "Resource-Agents-4.3.018. a7fb5035-3.30.1.  
> - W przypadku programu SLES 15/15 z dodatkiem SP1 wersja musi być co najmniej równa "Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  
>
> Należy pamiętać, że zmiana będzie wymagała krótkiego przestoju.  
> W przypadku istniejących klastrów Pacemaker, jeśli konfiguracja została już zmieniona tak, aby korzystała z socat zgodnie z opisem w obszarze [Ograniczanie poziomu wykrywania w usłudze azure Load-Balancer](https://www.suse.com/support/kb/doc/?id=7024128), nie ma potrzeby natychmiastowego przełączenia do agenta zasobów platformy Azure.

**[1]** konfiguracja Pacemaker programu IBM DB2 HADR cluster:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** Utwórz zasoby programu IBM DB2:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** uruchamianie zasobów programu IBM DB2:
* Pacemaker z trybu konserwacji.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** upewnij się, że klaster ma stan OK i że wszystkie zasoby są uruchomione. Węzeł, na którym są uruchomione zasoby, nie jest ważny.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>skonfigurowano 2 węzły
# <a name="5-resources-configured"></a>5 skonfigurowanych zasobów

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Pełna lista zasobów:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-SBD (stonith: External/SBD): uruchomiono azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Grupa zasobów: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (OCF:: puls: IPaddr2): uruchomiono azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (OCF:: puls: Azure-lb): uruchomiono azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Zestaw główny/podrzędny: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Wzorce: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Elementy podrzędne: [azibmdb01]
</pre>

> [!IMPORTANT]
> Aby zarządzać klastrowanym wystąpieniem bazy danych Pacemaker, należy użyć narzędzi Pacemaker. W przypadku korzystania z poleceń programu DB2, takich jak db2stop, Pacemaker wykrywa akcję jako niepowodzenie zasobów. W przypadku przeprowadzania konserwacji można umieścić węzły lub zasoby w trybie konserwacji. Pacemaker wstrzymuje zasoby monitorowania i można używać zwykłych poleceń administracyjnych programu DB2.


### <a name="configure-azure-load-balancer"></a>Konfigurowanie modułu Azure Load Balancer
Aby skonfigurować Azure Load Balancer, zalecamy użycie [jednostki SKU usługa Load Balancer w warstwie Standardowa platformy Azure](../../../load-balancer/load-balancer-overview.md) , a następnie wykonanie następujących czynności:

> [!NOTE]
> Jednostka SKU usługa Load Balancer w warstwie Standardowa ma ograniczenia dostępu do publicznych adresów IP z węzłów znajdujących się pod Load Balancer. [Publiczna łączność z punktem końcowym firmy Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) zawiera opis sposobów włączania tych węzłów w celu uzyskania dostępu do publicznych adresów IP

> [!IMPORTANT]
> Zmienny adres IP nie jest obsługiwany w konfiguracji pomocniczego adresu IP karty sieciowej w scenariuszach równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [ograniczenia modułu równoważenia obciążenia platformy Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Jeśli potrzebujesz dodatkowego adresu IP dla maszyny wirtualnej, wdróż drugą kartę sieciową.  

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

   c. Wybierz pozycję **TCP** jako protokół i port **62500**. Pozostaw wartość **interwału** ustawioną na **5**i pozostaw wartość **progową złej kondycji** ustawioną na **2**.

   d. Wybierz przycisk **OK**.

1. Utwórz reguły równoważenia obciążenia:

   a. W Azure Portal Otwórz Azure Load Balancer, wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.

   b. Wprowadź nazwę nowej reguły Load Balancer (na przykład **DB2-SID**).

   c. Wybierz adres IP frontonu, pulę zaplecza i sondę kondycji utworzoną wcześniej (na przykład **DB2-fronton**).

   d. Pozostaw **Protokół** ustawiony na **TCP**i wprowadź port *komunikacyjny bazy danych*portu.

   e. Zwiększ **limit czasu bezczynności** do 30 minut.

   f. Upewnij się, że **włączono zmiennoprzecinkowy adres IP**.

   przykład Wybierz przycisk **OK**.


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
 
1. Zaloguj się do podstawowego serwera aplikacji wystąpienia J2EE i wykonaj następujące operacje:   `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. W lewej ramce wybierz pozycję **Magazyn zabezpieczeń**.
1. W prawej ramce wybierz pozycję Key JDBC/Pool/ \<SAPSID> /URL.
1. Zmień nazwę hosta w adresie URL JDBC na nazwę hosta wirtualnego.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Wybierz pozycję **Dodaj**.
1. Aby zapisać zmiany, wybierz ikonę dysku w lewym górnym rogu.
1. Zamknij narzędzie konfiguracji.
1. Uruchom ponownie wystąpienie środowiska Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Konfigurowanie archiwizacji dzienników dla Instalatora HADR Cluster
Aby skonfigurować funkcję archiwizowania dziennika bazy danych DB2 dla Instalatora HADR Cluster, zaleca się skonfigurowanie zarówno podstawowej, jak i w stanie gotowości do automatycznego pobierania dzienników ze wszystkich lokalizacji archiwum dzienników. Zarówno podstawowa, jak i zapasowa baza danych muszą mieć możliwość pobierania plików archiwum dzienników ze wszystkich lokalizacji archiwum dzienników, do których jeden z wystąpień bazy danych może archiwizować pliki dziennika. 

Archiwizowanie dzienników jest wykonywane tylko przez podstawową bazę danych. Jeśli zmienisz role HADR Cluster serwerów baz danych lub wystąpi awaria, Nowa podstawowa baza danych odpowiada za archiwizowanie dzienników. Jeśli skonfigurowano wiele lokalizacji archiwum dzienników, dzienniki mogą być archiwizowane dwukrotnie. W przypadku lokalnego lub zdalnego przechwycenia może być również konieczne ręczne skopiowanie zarchiwizowanych dzienników ze starego serwera podstawowego do aktywnej lokalizacji dziennika na nowym serwerze podstawowym.

Zalecamy skonfigurowanie wspólnego udziału NFS, w którym dzienniki są zapisywane z obu węzłów. Udział NFS musi być w dużej dostępności. 

Możesz użyć istniejących udziałów NFS o wysokiej dostępności do transportowania lub katalogu profilów. Aby uzyskać więcej informacji, zobacz:

- [Wysoka dostępność systemu plików NFS na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server][nfs-ha] 
- [Wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z Azure NetApp Files dla aplikacji SAP](./high-availability-guide-suse-netapp-files.md)
- [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) (aby utworzyć udziały NFS)


## <a name="test-the-cluster-setup"></a>Testowanie konfiguracji klastra

W tej sekcji opisano, jak można testować konfigurację programu DB2 HADR Cluster. *Każdy test zakłada, że użytkownik jest zalogowany jako katalog główny użytkownika* , a na maszynie wirtualnej *azibmdb01* jest uruchomiony program IBM DB2 Primary.

Początkowy stan wszystkich przypadków testowych został wyjaśniony tutaj: (crm_mon-r lub CRM status)

- **stan programu CRM** to migawka stanu Pacemaker w czasie wykonywania 
- **crm_mon-r** jest ciągłym wyjściem stanu Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Oryginalny stan w systemie SAP jest opisany w temacie Transaction DBACOCKPIT > Configuration > Overview, jak pokazano na poniższej ilustracji:

![DBACockpit — wstępna migracja](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Testowanie przejęcia programu IBM DB2


> [!IMPORTANT] 
> Przed rozpoczęciem testu upewnij się, że:
> * Pacemaker nie ma żadnych akcji zakończonych niepowodzeniem (stan programu CRM).
> * Brak ograniczeń lokalizacji (pozostałości testu migracji)
> * Synchronizacja programu IBM DB2 HADR Cluster działa. Skontaktuj się z użytkownikiem DB2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Przeprowadź migrację węzła, w którym działa podstawowa baza danych DB2, wykonując następujące polecenie:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Po zakończeniu migracji dane wyjściowe stanu programu CRM wyglądają następująco:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Oryginalny stan w systemie SAP jest opisany w temacie Transaction DBACOCKPIT > Configuration > Overview, jak pokazano na poniższej ilustracji:

![DBACockpit — po migracji](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Migracja zasobów z "migrowaniem zasobów programu CRM" tworzy ograniczenia lokalizacji. Ograniczenia lokalizacji należy usunąć. Jeśli ograniczenia lokalizacji nie zostaną usunięte, zasób nie może powrócić po awarii lub można napotkać niepożądane przejęcia. 

Przeprowadź migrację zasobu z powrotem do *azibmdb01* i wyczyść ograniczenia lokalizacji
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **Migrowanie zasobów programu CRM \<res_name> \<host> :** tworzy ograniczenia lokalizacji i może powodować problemy z przejęciem
- **czyszczenie \<res_name> zasobu CRM **: czyści ograniczenia lokalizacji
- **oczyszczanie \<res_name> zasobów programu CRM **: czyści wszystkie błędy zasobu

### <a name="test-the-fencing-agent"></a>Testowanie czynnika ogrodzenia

W takim przypadku firma Microsoft testuje ogrodzenie SBD, które zalecamy w przypadku korzystania z systemu SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Węzeł klastra *azibmdb01* powinien zostać ponownie uruchomiony. Rolę HADR Cluster podstawowego programu IBM DB2 można przenieść do *azibmdb02*. Gdy *azibmdb01* jest z powrotem w tryb online, wystąpienie bazy danych programu DB2 będzie przenoszone w roli pomocniczego wystąpienia. 

Jeśli usługa Pacemaker nie zostanie uruchomiona automatycznie przy ponownym uruchomieniu poprzedniego serwera podstawowego, upewnij się, że została ona uruchomiona ręcznie przy użyciu:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testowanie ręcznego przejęcia

Ręczne przejęcie można przetestować, zatrzymując usługę Pacemaker w węźle *azibmdb01* :
<pre><code>service pacemaker stop</code></pre>

stan na *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Po przejściu do trybu failover można uruchomić usługę ponownie w witrynie *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Kasuj proces programu DB2 w węźle, który uruchamia podstawową bazę danych HADR Cluster

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Wystąpienie bazy danych DB2 kończy się niepowodzeniem, a Pacemaker będzie zgłaszać następujący stan:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker uruchomi ponownie wystąpienie podstawowej bazy danych DB2 w tym samym węźle lub przejdzie do trybu failover w węźle, w którym jest uruchomione pomocnicze wystąpienie bazy danych, a zostanie zgłoszony błąd.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Kasuj proces programu DB2 w węźle, w którym działa wystąpienie pomocniczej bazy danych

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Określony węzeł nie powiódł się i zgłoszono błąd
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Wystąpienie bazy danych DB2 zostanie uruchomione ponownie w roli pomocniczej, która została wcześniej przypisana.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Zatrzymaj bazę danych za pośrednictwem db2stop Force w węźle, w którym działa wystąpienie podstawowej bazy danych HADR Cluster

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Jako użytkownik programu DB2 \<sid> wykona db2stop polecenia:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Wykryto błąd
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Wystąpienie pomocniczej bazy danych DB2 HADR Cluster zostało podwyższone do roli podstawowej
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Awaria maszyny wirtualnej z ponownym uruchomieniem w węźle, na którym działa wystąpienie podstawowej bazy danych HADR Cluster

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker Podnieś do roli wystąpienia podstawowego wystąpienie pomocnicze. Stare wystąpienie podstawowe przejdzie do roli pomocniczej po ponownym uruchomieniu maszyny wirtualnej i wszystkich usług.

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Awaria maszyny wirtualnej z uruchomionym wystąpieniem podstawowej bazy danych HADR Cluster z "zatrzymaniem"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

W takim przypadku Pacemaker wykryje, że węzeł, na którym działa podstawowe wystąpienie bazy danych, nie odpowiada.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Następnym krokiem jest sprawdzenie, czy występuje *podzielona sytuacja mózgów* . Po zakończeniu działania węzła, który wykrył, że węzeł, który ostatnio uruchomił podstawowe wystąpienie bazy danych, nie działa w trybie failover zasobów.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


W przypadku "zatrzymania" węzła uszkodzony węzeł należy ponownie uruchomić za pomocą narzędzi do zarządzania platformy Azure (w Azure Portal, PowerShell lub interfejsie wiersza polecenia platformy Azure). Gdy węzeł, który uległ awarii, zostanie przywrócony do trybu online, uruchamia wystąpienie bazy danych DB2 w roli pomocniczej.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Następne kroki
- [Architektura i scenariusze wysokiej dostępności dla oprogramowania SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
- [Konfigurowanie Pacemaker SUSE Linux Enterprise Server na platformie Azure](./high-availability-guide-suse-pacemaker.md)

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist
