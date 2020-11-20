---
title: Obsługiwane scenariusze dla SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Obsługiwane scenariusze i szczegółowe informacje o architekturze dla SAP HANA na platformie Azure (duże wystąpienia)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f33ef81282dd8e60eba64cc77cbd95ab8dbc557
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967571"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Obsługiwane scenariusze dla dużych wystąpień HANA
W tym artykule opisano obsługiwane scenariusze i szczegóły architektury dla dużych wystąpień platformy HANA (HLI).

>[!NOTE]
>Jeśli w tym artykule nie określono wymaganego scenariusza, skontaktuj się z zespołem zarządzania usługami firmy Microsoft, aby ocenić wymagania.
Przed skonfigurowaniem jednostki protokołu HLI Sprawdź, czy projekt jest używany przez oprogramowanie SAP lub partnera implementacji usługi.

## <a name="terms-and-definitions"></a>Warunki i definicje
Zapoznaj się z postanowieniami i definicjami, które są używane w tym artykule:

- **SID**: identyfikator systemu dla systemu Hana
- **HLI**: Duże wystąpienia Hana
- **DR** Odzyskiwanie po awarii
- **Normalny program Dr**: Konfiguracja systemu z dedykowanym zasobem tylko do celów odzyskiwania po awarii
- **MULTIPURPOSE Dr**: system lokacji Dr, który jest skonfigurowany do korzystania ze środowiska nieprodukcyjnego obok wystąpienia produkcyjnego skonfigurowanego dla zdarzenia odzyskiwania po awarii 
- **Single-SID**: system z zainstalowanym jednym wystąpieniem
- **Wiele identyfikatorów SID**: system z wieloma skonfigurowanymi wystąpieniami; nazywana również środowiskiem MCOS
- **HSR**: replikacja systemu SAP HANA

## <a name="overview"></a>Omówienie
Duże wystąpienia HANA obsługują różne architektury, które ułatwiają spełnienie wymagań firmy. W poniższych sekcjach omówiono scenariusze architektury i ich szczegóły konfiguracji. 

Projekt architektury pochodnej jest całkowicie z perspektywy infrastruktury i należy skontaktować się z producentem SAP lub partnerami implementacji w celu wdrożenia platformy HANA. Jeśli Twoje scenariusze nie są wymienione w tym artykule, skontaktuj się z zespołem konto Microsoft, aby zapoznać się z architekturą i utworzyć rozwiązanie.

> [!NOTE]
> Te architektury są w pełni zgodne z projektem integracji danych (TDI) i obsługiwanym przez system SAP.

W tym artykule opisano szczegółowe informacje o dwóch składnikach w poszczególnych obsługiwanych architekturach:

- Sieć Ethernet
- Magazyn

### <a name="ethernet"></a>Sieć Ethernet

Każdy serwer aprowizacji jest wstępnie skonfigurowany z zestawami interfejsów sieci Ethernet. Interfejsy Ethernet skonfigurowane w poszczególnych jednostkach programu HLI są podzielone na cztery typy:

- Odp **.: używany przez lub** przez dostęp klienta.
- **B**: służy do komunikacji między węzłami. Ten interfejs jest skonfigurowany na wszystkich serwerach (niezależnie od żądanej topologii), ale używany tylko w scenariuszach skalowania w poziomie.
- **C**: służy do łączności między węzłami.
- **D**: służy do nawiązywania połączenia z urządzeniem między węzłami i iSCSI dla Instalatora STONITH. Ten interfejs jest konfigurowany tylko wtedy, gdy zażądano Instalatora HSR.  

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Węzeł-węzeł|
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | STONITH |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Węzeł-węzeł|
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | STONITH |

Interfejs jest wybierany na podstawie topologii skonfigurowanej w jednostce. Na przykład interfejs "B" jest skonfigurowany do komunikacji między węzłami, co jest przydatne w przypadku skonfigurowania topologii skalowalnej w poziomie. Ten interfejs nie jest używany w przypadku pojedynczych węzłów i skalowalnych konfiguracji. Aby uzyskać więcej informacji na temat użycia interfejsu, przejrzyj wymagane scenariusze (w dalszej części tego artykułu). 

W razie potrzeby możesz samodzielnie zdefiniować dodatkowe karty sieciowe. *Nie* można jednak zmienić konfiguracji istniejących kart interfejsu sieciowego.

>[!NOTE]
>Mogą znajdować się dodatkowe interfejsy, które są interfejsami fizycznymi lub wiązania. Należy wziąć pod uwagę tylko wyżej wymienione interfejsy dla przypadku użycia. Wszystkie inne mogą być ignorowane.

Dystrybucja jednostek z dwoma przypisanymi adresami IP powinna wyglądać następująco:

- Sieć Ethernet "A" powinna mieć przypisany adres IP należący do zakresu adresów puli IP serwera, który został przesłany do firmy Microsoft. Ten adres IP powinien być przechowywany w katalogu */etc/hosts* systemu operacyjnego.

- Sieć Ethernet "C" powinna mieć przypisany adres IP używany do komunikacji z systemem plików NFS. Ten adres nie *musi być obsługiwany w* katalogu *etc/hosts* , aby umożliwić ruch między wystąpieniami w ramach dzierżawy.

W przypadku replikacji systemu HANA lub wdrożenia w poziomie platformy Hana konfiguracja bloku z dwoma przypisanymi adresami IP nie jest odpowiednia. Jeśli masz tylko dwa przypisane adresy IP i chcesz wdrożyć taką konfigurację, skontaktuj się z SAP HANA w usłudze Azure Service Management. Mogą oni przypisywać trzeci adres IP w trzeciej sieci VLAN. W przypadku dużych wystąpień usługi HANA jednostki z trzema przypisanymi adresami IP na trzech portach kart sieciowych mają zastosowanie następujące reguły użycia:

- Sieć Ethernet "A" powinna mieć przypisany adres IP, który znajduje się poza zakresem adresów puli adresów IP przesłanym do firmy Microsoft. Ten adres IP nie powinien być utrzymywany w katalogu " *etc/hosts* " systemu operacyjnego.

- Sieć Ethernet "B" powinna być obsługiwana wyłącznie w katalogu " *etc/hosts* " na potrzeby komunikacji między różnymi wystąpieniami. Adresy IP, które mają być utrzymywane w konfiguracjach platformy HANA w poziomie, jako adresy IP używane przez platformę w konfiguracji między węzłami.

- Sieć Ethernet "C" powinna mieć przypisany adres IP używany do komunikacji z magazynem systemu plików NFS. Tego typu adresu nie należy utrzymywać w katalogu *etc/hosts* .

- Sieć Ethernet "D" powinna być używana wyłącznie na potrzeby dostępu do urządzeń STONITH w Pacemaker. Ten interfejs jest wymagany w przypadku skonfigurowania replikacji systemu HANA i zapewnienia automatycznie przełączenia w tryb failover systemu operacyjnego przy użyciu urządzenia z systemem SBD.


### <a name="storage"></a>Magazyn
Magazyn jest wstępnie skonfigurowany na podstawie wybranej topologii. Rozmiary woluminów i punkty instalacji różnią się w zależności od liczby serwerów, liczby jednostek SKU i skonfigurowanej topologii. Aby uzyskać więcej informacji, przejrzyj wymagane scenariusze (w dalszej części tego artykułu). Jeśli potrzebujesz więcej miejsca w magazynie, możesz kupić je w przyrostach o pojemności 1 TB.

>[!NOTE]
>/Usr/SAP/punktu instalacji \<SID> jest symbolicznym łączem do punktu instalacji/Hana/Shared.


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Diagramy architektury w następnych sekcjach używają następujących notacji:

[![Tabela diagramów architektury](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Oto obsługiwane scenariusze:

* Pojedynczy węzeł z jednym identyfikatorem SID
* MCOS jednego węzła
* Pojedynczy węzeł z odzyskiwaniem po awarii (normalne)
* Pojedynczy węzeł z odzyskiwaniem po awarii (Multipurpose)
* HSR z STONITH
* HSR z DR (Normal/Multipurpose) 
* Przełączenie w tryb failover hosta (1 + 1) 
* Skalowanie w poziomie przy użyciu rezerwy
* Skalowanie w poziomie bez rezerwy
* Skalowanie w poziomie za pomocą programu DR

## <a name="single-node-with-one-sid"></a>Pojedynczy węzeł z jednym identyfikatorem SID

Ta topologia obsługuje jeden węzeł w konfiguracji skalowania z jednym identyfikatorem SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczy węzeł z jednym identyfikatorem SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|/hana/shared/SID | Instalacja platformy HANA | 
|/hana/data/SID/mnt00001 | Instalacja plików danych | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.

## <a name="single-node-mcos"></a>MCOS jednego węzła

Ta topologia obsługuje jeden węzeł w konfiguracji skalowania z wieloma identyfikatorami SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![MCOS jednego węzła](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|/hana/shared/SID1 | Instalacja platformy HANA dla SID1 | 
|/hana/data/SID1/mnt00001 | Instalacja plików danych dla SID1 | 
|/hana/log/SID1/mnt00001 | Instalacja plików dziennika dla SID1 | 
|/hana/logbackups/SID1 | Wykonaj ponownie dzienniki dla SID1 |
|/hana/shared/SID2 | Instalacja platformy HANA dla SID2 | 
|/hana/data/SID2/mnt00001 | Instalacja plików danych dla SID2 | 
|/hana/log/SID2/mnt00001 | Instalacja plików dziennika dla SID2 | 
|/hana/logbackups/SID2 | Wykonaj ponownie dzienniki dla SID2 |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- Rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku z obsługą wiele identyfikatorów SID, zobacz [Omówienie i architektura](./hana-overview-architecture.md).

## <a name="single-node-with-dr-using-storage-replication"></a>Pojedynczy węzeł z odzyskiwaniem po awarii przy użyciu replikacji magazynu
 
Ta topologia obsługuje jeden węzeł w konfiguracji skalowania z co najmniej jednym identyfikatorem SID z replikacją opartą na magazynie do lokacji DR dla podstawowego identyfikatora SID. Na diagramie w lokacji głównej jest przedstawiony tylko system o pojedynczym identyfikatorze SID, ale systemy MCOS również są obsługiwane.

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczy węzeł z odzyskiwaniem po awarii przy użyciu replikacji magazynu](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|/hana/shared/SID | Instalacja platformy HANA dla identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W przypadku MCOS: rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku z obsługą wiele identyfikatorów SID, zobacz [Omówienie i architektura](./hana-overview-architecture.md).
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "wymagane do instalacji platformy HANA") dla instalacji wystąpienia platformy HANA w jednostce. 
- W witrynie DR: dane, kopie zapasowe dzienników i udostępnione woluminy (oznaczone jako "replikacja magazynu") są replikowane za pośrednictwem migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko podczas pracy w trybie failover. Aby uzyskać więcej informacji, zobacz [procedura trybu failover odzyskiwania po awarii](./hana-overview-high-availability-disaster-recovery.md).
- Wolumin rozruchowy dla *klasy typu SKU I* jest replikowany do węzła Dr.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Pojedynczy węzeł z DR (Multipurpose) przy użyciu replikacji magazynu
 
Ta topologia obsługuje jeden węzeł w konfiguracji skalowania z co najmniej jednym identyfikatorem SID z replikacją opartą na magazynie do lokacji DR dla podstawowego identyfikatora SID. Na diagramie w lokacji głównej jest przedstawiony tylko system o pojedynczym identyfikatorze SID, ale obsługiwane są również systemy wieloidentyfikatorowe (MCOS). W lokacji odzyskiwania po awarii jednostka HLI jest używana na potrzeby wystąpienia usługi pytań i odpowiedzi podczas wykonywania operacji produkcyjnych z lokacji głównej. Podczas pracy w trybie failover odzyskiwania po awarii (test pracy w trybie failover) wystąpienie funkcji pytań i odpowiedzi w witrynie DR zostanie wyłączone.

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczy węzeł z DR (Multipurpose) przy użyciu replikacji magazynu](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W witrynie DR**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/shared/QA-SID | Instalacja platformy HANA dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/data/QA-SID/mnt00001 | Instalacja plików danych dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/log/QA-SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID usługi pytania i odpowiedzi |
|/hana/logbackups/QA-SID | Dzienniki ponownego wykonywania dla identyfikatora SID usługi pytania i odpowiedzi |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W przypadku MCOS: rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku z obsługą wiele identyfikatorów SID, zobacz [Omówienie i architektura](./hana-overview-architecture.md).
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "wymagane do instalacji platformy HANA") dla instalacji wystąpienia platformy HANA w jednostce. 
- W witrynie DR: dane, kopie zapasowe dzienników i udostępnione woluminy (oznaczone jako "replikacja magazynu") są replikowane za pośrednictwem migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko podczas pracy w trybie failover. Aby uzyskać więcej informacji, zobacz [procedura trybu failover odzyskiwania po awarii](./hana-overview-high-availability-disaster-recovery.md). 
- W witrynie DR: dane, kopie zapasowe dzienników, dzienniki i udostępnione woluminy dla pytań i odpowiedzi (oznaczone jako "Instalacja wystąpienia usługi pytania") są konfigurowane na potrzeby instalacji wystąpienia usługi pytania i odpowiedzi.
- Wolumin rozruchowy dla *klasy typu SKU I* jest replikowany do węzła Dr.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR z STONITHem w celu zapewnienia wysokiej dostępności
 
Ta topologia obsługuje dwa węzły w konfiguracji replikacji systemu HANA. Ta konfiguracja jest obsługiwana tylko dla pojedynczych wystąpień platformy HANA w węźle. Oznacza to, że scenariusze MCOS *nie* są obsługiwane.

> [!NOTE]
> Od grudnia 2019 ta architektura jest obsługiwana tylko dla systemu operacyjnego SUSE.


### <a name="architecture-diagram"></a>Diagram architektury  

![HSR z STONITHem w celu zapewnienia wysokiej dostępności](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Używane na potrzeby STONITH |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Używane na potrzeby STONITH |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W węźle pomocniczym**|
|/hana/shared/SID | Instalacja platformy HANA dla pomocniczego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla pomocniczego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla pomocniczego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla pomocniczego identyfikatora SID |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W przypadku MCOS: rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku z obsługą wiele identyfikatorów SID, zobacz [Omówienie i architektura](./hana-overview-architecture.md).
- STONITH: SBD jest skonfigurowana dla Instalatora STONITH. Jednak użycie STONITH jest opcjonalne.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Wysoka dostępność dzięki replikacji HSR i DR z replikacją magazynu
 
Ta topologia obsługuje dwa węzły w konfiguracji replikacji systemu HANA. Obsługiwane są zarówno normalne, jak i uniwersalne DRs. Te konfiguracje są obsługiwane tylko dla pojedynczych wystąpień platformy HANA w węźle. Oznacza to, że scenariusze MCOS *nie* są obsługiwane w przypadku tych konfiguracji.

W diagramie wielomiarowy scenariusz jest przedstawiany w witrynie odzyskiwania po awarii, gdzie jednostka HLI jest używana w przypadku wystąpienia usługi pytań i odpowiedzi podczas wykonywania operacji produkcyjnych z lokacji głównej. Podczas pracy w trybie failover odzyskiwania po awarii (test pracy w trybie failover) wystąpienie funkcji pytań i odpowiedzi w witrynie DR zostanie wyłączone. 

### <a name="architecture-diagram"></a>Diagram architektury  

![Wysoka dostępność dzięki replikacji HSR i DR z replikacją magazynu](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Używane na potrzeby STONITH |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Używane na potrzeby STONITH |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym w lokacji głównej**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W węźle pomocniczym w lokacji głównej**|
|/hana/shared/SID | Instalacja platformy HANA dla pomocniczego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla pomocniczego identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla pomocniczego identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla pomocniczego identyfikatora SID |
|**W witrynie DR**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/shared/QA-SID | Instalacja platformy HANA dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/data/QA-SID/mnt00001 | Instalacja plików danych dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/log/QA-SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID usługi pytania i odpowiedzi |
|/hana/logbackups/QA-SID | Dzienniki ponownego wykonywania dla identyfikatora SID usługi pytania i odpowiedzi |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W przypadku MCOS: rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku z obsługą wiele identyfikatorów SID, zobacz [Omówienie i architektura](./hana-overview-architecture.md).
- STONITH: SBD jest skonfigurowana dla Instalatora STONITH. Jednak użycie STONITH jest opcjonalne.
- W witrynie DR: *wymagane są dwa zestawy woluminów magazynu* dla replikacji węzłów podstawowych i pomocniczych.
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "wymagane do instalacji platformy HANA") dla instalacji wystąpienia platformy HANA w jednostce. 
- W witrynie DR: dane, kopie zapasowe dzienników i udostępnione woluminy (oznaczone jako "replikacja magazynu") są replikowane za pośrednictwem migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko podczas pracy w trybie failover. Aby uzyskać więcej informacji, zobacz [procedura trybu failover odzyskiwania po awarii](./hana-overview-high-availability-disaster-recovery.md). 
- W witrynie DR: dane, kopie zapasowe dzienników, dzienniki i udostępnione woluminy dla pytań i odpowiedzi (oznaczone jako "Instalacja wystąpienia usługi pytania") są konfigurowane na potrzeby instalacji wystąpienia usługi pytania i odpowiedzi.
- Wolumin rozruchowy dla *klasy typu SKU I* jest replikowany do węzła Dr.


## <a name="host-auto-failover-11"></a>Przełączenie w tryb failover hosta (1 + 1)
 
Ta topologia obsługuje dwa węzły w konfiguracji automatycznej pracy awaryjnej hosta. Istnieje jeden węzeł z rolą Master/Worker i inną jako stan gotowości. *System SAP obsługuje ten scenariusz tylko dla S/4 HANA.* Aby uzyskać więcej informacji, zobacz temat [OSS uwaga 2408419 — SAP S/4HANA — obsługa wielu węzłów](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Diagram architektury  

![Przełączenie w tryb failover hosta (1 + 1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Komunikacja między węzłami |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Komunikacja między węzłami |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**Na węzłach głównych i rezerwowych**|
|/hana/shared | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |



### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W stanie wstrzymania: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "wymagane na potrzeby instalacji platformy HANA") dla instalacji wystąpienia HANA w jednostce rezerwy.
 

## <a name="scale-out-with-standby"></a>Skalowanie w poziomie przy użyciu rezerwy
 
Ta topologia obsługuje wiele węzłów w konfiguracji skalowania w poziomie. Istnieje jeden węzeł z rolą nadrzędną, co najmniej jednym węzłem z rolą proces roboczy oraz co najmniej jeden węzeł jako stan gotowości. Jednak w danym momencie może istnieć tylko jeden węzeł główny.


### <a name="architecture-diagram"></a>Diagram architektury  

![Skalowanie w poziomie przy użyciu rezerwy](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Komunikacja między węzłami |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Komunikacja między węzłami |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węzłach Master, Worker i Standby**|
|/hana/shared | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |


## <a name="scale-out-without-standby"></a>Skalowanie w poziomie bez rezerwy
 
Ta topologia obsługuje wiele węzłów w konfiguracji skalowania w poziomie. Istnieje jeden węzeł z rolą główną i co najmniej jednym węzłem z rolą proces roboczy. Jednak w danym momencie może istnieć tylko jeden węzeł główny.


### <a name="architecture-diagram"></a>Diagram architektury  

![Skalowanie w poziomie bez rezerwy](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Komunikacja między węzłami |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Komunikacja między węzłami |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**Na węzłach głównych i procesów roboczych**|
|/hana/shared | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Skalowanie za pomocą odzyskiwania po awarii przy użyciu replikacji magazynu
 
Ta topologia obsługuje wiele węzłów w skalowaniu w poziomie przy użyciu funkcji odzyskiwania po awarii. Obsługiwane są zarówno normalne, jak i uniwersalne DRs. Na diagramie przedstawiony jest tylko pojedynczy cel DR. Możesz zażądać tej topologii z węzłem w stanie wstrzymania lub bez niego.


### <a name="architecture-diagram"></a>Diagram architektury  

![Skalowanie za pomocą odzyskiwania po awarii przy użyciu replikacji magazynu](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient--HLI |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Komunikacja między węzłami |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient--HLI |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Komunikacja między węzłami |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/hana/shared | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W węźle DR**|
|/hana/shared | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
-  W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "wymagane do instalacji platformy HANA") dla instalacji wystąpienia platformy HANA w jednostce. 
- W witrynie DR: dane, kopie zapasowe dzienników i udostępnione woluminy (oznaczone jako "replikacja magazynu") są replikowane za pośrednictwem migawki z lokacji produkcyjnej. Te woluminy są instalowane tylko podczas pracy w trybie failover. Aby uzyskać więcej informacji, zobacz [procedura trybu failover odzyskiwania po awarii](./hana-overview-high-availability-disaster-recovery.md). 
- Wolumin rozruchowy dla *klasy typu SKU I* jest replikowany do węzła Dr.


## <a name="single-node-with-dr-using-hsr"></a>Pojedynczy węzeł z odzyskiwaniem po awarii przy użyciu HSR
 
Ta topologia obsługuje jeden węzeł w konfiguracji skalowania z jednym identyfikatorem SID z replikacją systemu HANA do lokacji DR dla podstawowego identyfikatora SID. Na diagramie w lokacji głównej jest przedstawiony tylko system o pojedynczym identyfikatorze SID, ale obsługiwane są również systemy wieloidentyfikatorowe (MCOS).

### <a name="architecture-diagram"></a>Diagram architektury  

![Pojedynczy węzeł z odzyskiwaniem po awarii przy użyciu HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient-do-HLI/HSR |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient-do-HLI/HSR |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane na jednostkach HLI (podstawowa i DR):

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|/hana/shared/SID | Instalacja platformy HANA dla identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W przypadku MCOS: rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku z obsługą wiele identyfikatorów SID, zobacz [Omówienie i architektura](./hana-overview-architecture.md).
- Węzeł podstawowy jest synchronizowany z węzłem DR przy użyciu replikacji systemu HANA. 
- [Global REACH](../../../expressroute/expressroute-global-reach.md) służy do łączenia obwodów usługi ExpressRoute w celu nałożenia sieci prywatnej między sieciami regionalnymi.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>HSR z jednym węzłem do odzyskiwania po awarii (koszt zoptymalizowany) 
 
 Ta topologia obsługuje jeden węzeł w konfiguracji skalowania z jednym identyfikatorem SID z replikacją systemu HANA do lokacji DR dla podstawowego identyfikatora SID. Na diagramie w lokacji głównej jest przedstawiony tylko system o pojedynczym identyfikatorze SID, ale obsługiwane są również systemy wieloidentyfikatorowe (MCOS). W odniesieniu do wystąpienia usługi pytania i odpowiedzi podczas wykonywania operacji produkcyjnych z lokacji głównej jest używana jednostka HLI. Podczas pracy w trybie failover odzyskiwania po awarii (test pracy w trybie failover) wystąpienie funkcji pytań i odpowiedzi w witrynie DR zostanie wyłączone.

### <a name="architecture-diagram"></a>Diagram architektury  

![HSR z jednym węzłem do odzyskiwania po awarii (koszt zoptymalizowany)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient-do-HLI/HSR |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient-do-HLI/HSR |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W witrynie DR**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|/hana/shared/QA-SID | Instalacja platformy HANA dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/data/QA-SID/mnt00001 | Instalacja plików danych dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/log/QA-SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID usługi pytania i odpowiedzi |
|/hana/logbackups/QA-SID | Dzienniki ponownego wykonywania dla identyfikatora SID usługi pytania i odpowiedzi |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W przypadku MCOS: rozkład rozmiaru woluminu zależy od rozmiaru bazy danych w pamięci. Aby dowiedzieć się, jakie rozmiary bazy danych w pamięci są obsługiwane w środowisku z obsługą wiele identyfikatorów SID, zobacz [Omówienie i architektura](./hana-overview-architecture.md).
- W lokacji odzyskiwania po awarii: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "wystąpienie produkcyjne w lokacji DR") dla instalacji wystąpienia platformy HANA w jednostce. 
- W witrynie DR: dane, kopie zapasowe dzienników, dzienniki i udostępnione woluminy dla pytań i odpowiedzi (oznaczone jako "Instalacja wystąpienia usługi pytania") są konfigurowane na potrzeby instalacji wystąpienia usługi pytania i odpowiedzi.
- Węzeł podstawowy jest synchronizowany z węzłem DR przy użyciu replikacji systemu HANA. 
- [Global REACH](../../../expressroute/expressroute-global-reach.md) służy do łączenia obwodów usługi ExpressRoute w celu nałożenia sieci prywatnej między sieciami regionalnymi.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Wysoka dostępność i odzyskiwanie po awarii za pomocą HSR 
 
 Ta topologia obsługuje dwa węzły dla konfiguracji replikacji systemu HANA na potrzeby wysokiej dostępności regionów lokalnych. W przypadku odzyskiwania po awarii trzeci węzeł w regionie DR synchronizuje się z lokacją główną przy użyciu HSR (tryb asynchroniczny). 

### <a name="architecture-diagram"></a>Diagram architektury  

![Wysoka dostępność i odzyskiwanie po awarii za pomocą HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient-do-HLI/HSR |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient-do-HLI/HSR |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W witrynie DR**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W witrynie DR: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "produkcyjne DR instance") dla instalacji wystąpienia platformy HANA w jednostce. 
- Węzeł lokacji głównej synchronizuje się z węzłem DR przy użyciu replikacji systemu HANA. 
- [Global REACH](../../../expressroute/expressroute-global-reach.md) służy do łączenia obwodów usługi ExpressRoute w celu nałożenia sieci prywatnej między sieciami regionalnymi.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Wysoka dostępność i odzyskiwanie po awarii za pomocą HSR (zoptymalizowane pod kątem kosztów)
 
 Ta topologia obsługuje dwa węzły dla konfiguracji replikacji systemu HANA na potrzeby wysokiej dostępności regionów lokalnych. W przypadku odzyskiwania po awarii trzeci węzeł w regionie DR synchronizuje się z lokacją główną przy użyciu HSR (tryb Async), podczas gdy inne wystąpienie (na przykład funkcja pytań i odpowiedzi) jest już uruchomione z węzła DR. 

### <a name="architecture-diagram"></a>Diagram architektury  

![Wysoka dostępność i odzyskiwanie po awarii za pomocą HSR (zoptymalizowane pod kątem kosztów)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient-do-HLI/HSR |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient-do-HLI/HSR |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Skonfigurowane, ale nie w użyciu |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W lokacji głównej**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W witrynie DR**|
|/hana/shared/SID | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|/hana/shared/QA-SID | Instalacja platformy HANA dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/data/QA-SID/mnt00001 | Instalacja plików danych dla identyfikatora SID usługi pytania i odpowiedzi | 
|/hana/log/QA-SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID usługi pytania i odpowiedzi |
|/hana/logbackups/QA-SID | Dzienniki ponownego wykonywania dla identyfikatora SID usługi pytania i odpowiedzi |

### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W witrynie DR: woluminy i punkty instalacji są skonfigurowane (oznaczone jako "produkcyjne DR instance") dla instalacji wystąpienia platformy HANA w jednostce. 
- W witrynie DR: dane, kopie zapasowe dzienników, dzienniki i udostępnione woluminy dla pytań i odpowiedzi (oznaczone jako "Instalacja wystąpienia usługi pytania") są konfigurowane na potrzeby instalacji wystąpienia usługi pytania i odpowiedzi.
- Węzeł lokacji głównej synchronizuje się z węzłem DR przy użyciu replikacji systemu HANA. 
- [Global REACH](../../../expressroute/expressroute-global-reach.md) służy do łączenia obwodów usługi ExpressRoute w celu nałożenia sieci prywatnej między sieciami regionalnymi.

## <a name="scale-out-with-dr-using-hsr"></a>Skalowanie w poziomie za pomocą narzędzia DR using HSR
 
Ta topologia obsługuje wiele węzłów w skalowaniu w poziomie przy użyciu funkcji odzyskiwania po awarii. Możesz zażądać tej topologii z węzłem w stanie wstrzymania lub bez niego. Węzeł lokacji głównej jest synchronizowany z węzłem lokacji DR przy użyciu replikacji systemu HANA (tryb Async).


### <a name="architecture-diagram"></a>Diagram architektury  

[![Skalowanie w poziomie za pomocą narzędzia Dr using HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Sieć Ethernet
Następujące interfejsy sieciowe są wstępnie skonfigurowane:

| Interfejs logiczny karty sieciowej | Typ jednostki SKU | Nazwa przy użyciu systemu SUSE OS | Nazwa z systemem operacyjnym RHEL | Przypadek użycia|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. Dzierżawca | eno1. Dzierżawca | Klient-do-HLI/HSR |
| B | TYP I | eth2. Dzierżawca | eno3. Dzierżawca | Komunikacja między węzłami |
| C | TYP I | eth1. Dzierżawca | eno2. Dzierżawca | Węzeł-Magazyn |
| D | TYP I | eth4. Dzierżawca | eno4. Dzierżawca | Skonfigurowane, ale nie w użyciu |
| A | TYP II | określone\<tenantNo> | team0. Dzierżawca | Klient-do-HLI/HSR |
| B | TYP II | określone\<tenantNo+2> | team0. Dzierżawca + 2 | Komunikacja między węzłami |
| C | TYP II | określone\<tenantNo+1> | team0. Dzierżawca + 1 | Węzeł-Magazyn |
| D | TYP II | określone\<tenantNo+3> | team0. Dzierżawca + 3 | Skonfigurowane, ale nie w użyciu |

### <a name="storage"></a>Magazyn
Następujące punkty instalacji są wstępnie skonfigurowane:

| Punkt instalacji | Przypadek użycia | 
| --- | --- |
|**W węźle podstawowym**|
|/hana/shared | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |
|**W węźle DR**|
|/hana/shared | Instalacja platformy HANA dla produkcyjnego identyfikatora SID | 
|/hana/data/SID/mnt00001 | Instalacja plików danych dla identyfikatora SID produkcji | 
|/hana/log/SID/mnt00001 | Instalacja plików dziennika dla identyfikatora SID produkcji | 
|/hana/logbackups/SID | Wykonaj ponownie dzienniki dla produkcyjnego identyfikatora SID |


### <a name="key-considerations"></a>Najważniejsze zagadnienia
- /usr/sap/SID jest linkiem symbolicznym do/hana/shared/SID.
- W witrynie DR: woluminy i punkty instalacji są skonfigurowane na potrzeby instalacji wystąpienia platformy HANA w jednostce. 
- Węzeł lokacji głównej synchronizuje się z węzłem DR przy użyciu replikacji systemu HANA. 
- [Global REACH](../../../expressroute/expressroute-global-reach.md) służy do łączenia obwodów usługi ExpressRoute w celu nałożenia sieci prywatnej między sieciami regionalnymi.


## <a name="next-steps"></a>Następne kroki
- [Infrastruktura i łączność z](./hana-overview-infrastructure-connectivity.md) dużymi WYSTĄPIENIAmi Hana
- [Wysoka dostępność i odzyskiwanie po awarii](./hana-overview-high-availability-disaster-recovery.md) dla dużych wystąpień platformy Hana
