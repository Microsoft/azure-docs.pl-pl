---
title: Azure Monitor dla rozwiązań SAP dostawcy| Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Monitor dla dostawców rozwiązań SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 93e97f1f04aea2a31b62b2014a88a5aaa998ed2d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376090"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Usługa Azure Monitor dla dostawców rozwiązań SAP (wersja zapoznawcza)

## <a name="overview"></a>Omówienie  

W kontekście Azure Monitor dla rozwiązań SAP typ *dostawcy* odwołuje się do określonego *dostawcy*. Na przykład *SAP HANA*, który jest skonfigurowany dla określonego składnika w krajobrazie SAP, takiego jak SAP HANA danych. Dostawca zawiera informacje o połączeniu odpowiedniego składnika i pomaga zbierać dane telemetryczne z tego składnika. Jeden Azure Monitor dla rozwiązań SAP (znany również jako zasób monitora SAP) można skonfigurować przy użyciu wielu dostawców tego samego typu dostawcy lub wielu dostawców wielu typów dostawców.
   
Klienci mogą skonfigurować różne typy dostawców, aby umożliwić zbieranie danych z odpowiedniego składnika w ich krajobrazie SAP. Na przykład klienci mogą skonfigurować jednego dostawcę dla SAP HANA dostawcy, innego dostawcę dla typu dostawcy klastra o wysokiej dostępności i tak dalej.  

Klienci mogą również skonfigurować wielu dostawców określonego typu, aby ponownie używać tego samego zasobu monitora SAP i skojarzonej grupy zarządzanej. Więcej informacji na temat zarządzanej grupy zasobów. W przypadku publicznej wersji zapoznawczej obsługiwane są następujące typy dostawców:   
- SAP HANA
- Klaster o wysokiej dostępności
- Microsoft SQL Server
- SAP NetWeaver

![Azure Monitor dla dostawców rozwiązań SAP](./media/azure-monitor-sap/azure-monitor-providers.png)

Klientom zaleca się skonfigurowanie co najmniej jednego dostawcy z dostępnych typów dostawców w czasie wdrażania zasobu rozwiązania SAP Monitor. Konfigurując dostawcę, klienci inicjują zbieranie danych z odpowiedniego składnika, dla którego dostawca jest skonfigurowany.   

Jeśli klienci nie skonfigurują żadnych dostawców w czasie wdrażania zasobu monitora SAP, mimo że zasób monitora SAP zostanie pomyślnie wdrożony, żadne dane telemetryczne nie będą zbierane. Klienci mają możliwość dodawania dostawców po wdrożeniu za pośrednictwem zasobu monitora SAP w Azure Portal. Klienci mogą w dowolnym momencie dodawać lub usuwać dostawców z zasobu monitora SAP.

> [!Tip]
> Jeśli chcesz, aby firma Microsoft zaimplementowała określonego dostawcę, prosimy o pozostawienie opinii za pośrednictwem linku na końcu tego dokumentu lub s kontaktowanie się z zespołem obsługi klienta.  

## <a name="provider-type-sap-hana"></a>Typ dostawcy SAP HANA

Klienci mogą skonfigurować co najmniej jednego dostawcę typu dostawcy, *SAP HANA* włączyć zbieranie danych z SAP HANA danych. Dostawca SAP HANA łączy się z bazą danych SAP HANA za pośrednictwem portu SQL, ściąga dane telemetryczne z bazy danych i wypycha je do obszaru roboczego usługi Log Analytics w subskrypcji klienta. Dostawca SAP HANA zbiera dane co 1 minutę z SAP HANA danych.  

W publicznej wersji zapoznawczej klienci mogą spodziewać się następujących danych u dostawcy usługi SAP HANA: Bazowe wykorzystanie infrastruktury, stan hosta usługi SAP HANA, replikacja systemu SAP HANA i dane telemetryczne usługi SAP HANA Backup. Aby skonfigurować SAP HANA, wymagany jest adres IP hosta, numer portu SQL platformy HANA oraz nazwa użytkownika i hasło bazy danych SYSTEMDB. Klientom zaleca się skonfigurowanie dostawcy SAP HANA względem bazy danych SYSTEMDB, jednak w przypadku innych dzierżaw baz danych można skonfigurować większej liczby dostawców.

![Azure Monitor dla dostawców rozwiązań SAP — SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Typ dostawcy Klaster wysokiej dostępności
Klienci mogą skonfigurować co najmniej  jednego dostawcę typu klaster o wysokiej dostępności, aby umożliwić zbieranie danych z klastra Pacemaker w krajobrazie SAP. Dostawca klastra o wysokiej dostępności łączy się z programem Pacemaker przy użyciu punktu końcowego usługi [ha_cluster_exporter,](https://github.com/ClusterLabs/ha_cluster_exporter) ściąga dane telemetryczne z bazy danych i wypycha je do obszaru roboczego usługi Log Analytics w subskrypcji klienta. Dostawca klastra o wysokiej dostępności zbiera dane z usługi Pacemaker co 60 sekund.  

W publicznej wersji zapoznawczej klienci mogą oczekiwać następujących danych u dostawcy klastra o wysokiej dostępności:   
 - Stan klastra reprezentowany jako zbiorczy stan węzła i zasobu 
 - [Innych](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor dla dostawców rozwiązań SAP — klaster o wysokiej dostępności](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Aby skonfigurować dostawcę klastra o wysokiej dostępności, należy wykonać dwa podstawowe kroki:

1. Zainstaluj [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) w *każdym węźle* klastra Pacemaker.

   Dostępne są dwie opcje instalowania ha_cluster_exporter:
   
   - Użyj Azure Automation skryptów do wdrożenia klastra o wysokiej dostępności. Skrypty [instalują ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) każdym węźle klastra.  
   - Wykonaj instalację [ręczną.](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build) 

2. Skonfiguruj dostawcę klastra o wysokiej dostępności dla *każdego węzła* w klastrze Pacemaker.

   Aby skonfigurować dostawcę klastra o wysokiej dostępności, wymagane są następujące informacje:
   
   - **Nazwa**. Nazwa tego dostawcy. Powinna być unikatowa dla tego Azure Monitor dla wystąpienia rozwiązań SAP.
   - **Prometheus Endpoint**. \: // \<servername or ip address\> http:9664/metrics.
   - **Identyfikator SID**. W przypadku systemów SAP użyj identyfikatora SID SAP. W przypadku innych systemów (na przykład klastrów NFS) należy użyć trzy znakowej nazwy klastra. Identyfikator SID musi być inny niż inne monitorowane klastry.   
   - **Nazwa klastra**. Nazwa klastra używana podczas tworzenia klastra. Nazwę klastra można znaleźć we właściwości klastra `cluster-name` .
   - **Nazwa hosta**. Nazwa hosta maszyny wirtualnej z systemem Linux.  


## <a name="provider-type-os-linux"></a>System operacyjny typu dostawcy (Linux)
Klienci mogą skonfigurować jednego lub wielu dostawców typu dostawcy systemu operacyjnego (Linux), aby umożliwić zbieranie danych z programu BareMetal lub węzła maszyny wirtualnej. Dostawca systemu operacyjnego (Linux) łączy się z węzłami BareMetal lub VM Nodes przy użyciu punktu końcowego programu [Node_Exporter,](https://github.com/prometheus/node_exporter)ściąga dane telemetryczne z węzłów i wypycha je do obszaru roboczego usługi Log Analytics w subskrypcji   klienta. Dostawca systemu operacyjnego (Linux) zbiera dane co 60 sekund dla większości metryk z węzłów. 

W publicznej wersji zapoznawczej klienci mogą oczekiwać następujących danych od dostawcy systemu operacyjnego (Linux): 
   - Użycie procesora CPU, użycie procesora CPU według procesu 
   - Wykorzystanie dysku, odczyt we/wy & zapisu 
   - Dystrybucja pamięci, użycie pamięci, użycie pamięci wymiany 
   - Użycie sieci, Ruch przychodzący sieciowy & szczegóły ruchu wychodzącego. 

Aby skonfigurować dostawcę systemu operacyjnego (Linux), należy wykonać dwa podstawowe kroki:
1. Zainstaluj [Node_Exporter](https://github.com/prometheus/node_exporter)   w każdym węzłu baremetalu lub maszyny wirtualnej.
   Dostępne są dwie opcje instalowania [Node_exporter:](https://github.com/prometheus/node_exporter) 
      - W przypadku instalacji automatyzacji za pomocą rozwiązania Ansible użyj [Node_Exporter](https://github.com/prometheus/node_exporter) na każdym węzłom baremetalu lub maszyny wirtualnej, aby zainstalować dostawcę systemu operacyjnego (Linux).  
      - Wykonaj instalację [ręczną.](https://prometheus.io/docs/guides/node-exporter/)

2. Skonfiguruj dostawcę systemu operacyjnego (Linux) dla każdego wystąpienia węzła baremetalu lub maszyny wirtualnej w środowisku. 
   Aby skonfigurować dostawcę systemu operacyjnego (Linux), wymagane są następujące informacje: 
      - Nazwa. Nazwa tego dostawcy. Powinna być unikatowa dla tego Azure Monitor dla wystąpienia rozwiązań SAP. 
      - Punkt końcowy eksportera węzłów. Zwykle <servername or ip address> http://:9100/metrics 

> [!NOTE]
> Port 9100 jest ujawniony dla Node_Exporter końcowego.

> [!Warning]
> Upewnij się, że eksporter węzłów będzie nadal działać po ponownym uruchomieniu węzła. 


## <a name="provider-type-microsoft-sql-server"></a>Typ dostawcy Microsoft SQL Server

Klienci mogą skonfigurować co najmniej jednego dostawcę typu *dostawcy, Microsoft SQL Server* włączyć zbieranie danych z [Program SQL Server w usłudze Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). SQL Server łączy się z usługą Microsoft SQL Server przez port SQL, ściąga dane telemetryczne z bazy danych i wypycha je do obszaru roboczego usługi Log Analytics w subskrypcji klienta. Serwer SQL Server musi być skonfigurowany do uwierzytelniania SQL i należy utworzyć identyfikator logowania usługi SQL Server z bazą danych SAP DB jako domyślną bazą danych dostawcy. SQL Server zbiera dane z programu SQL Server co 60 sekund do każdej godziny.  

W publicznej wersji zapoznawczej klienci mogą spodziewać się następujących danych u dostawcy usługi SQL Server: wykorzystanie podstawowej infrastruktury, najważniejsze instrukcje SQL, największa tabela, problemy zarejestrowane w dziennikach błędów usługi SQL Server, procesy blokowania i inne.  

Do skonfigurowania Microsoft SQL Server wymagany jest identyfikator systemu SAP, adres IP hosta, numer portu SQL Server oraz SQL Server logowania i hasło.

![Azure Monitor dla dostawców rozwiązań SAP — SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-sap-netweaver"></a>Typ dostawcy SAP NetWeaver

Klienci mogą skonfigurować co najmniej jednego dostawcę typu SAP NetWeaver, aby umożliwić zbieranie danych z warstwy SAP NetWeaver. Dostawca AMS NetWeaver korzysta z istniejącego interfejsu usługi internetowej [SAPControl](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) w celu pobrania odpowiednich informacji telemetrycznych.

W bieżącej wersji poniżej przedstawiono standardowe metody internetowe protokołu SOAP wywoływane przez usługę AMS.
|Metoda internetowa|    Abap|   Java|   Metryki|
|--|--|--|--|
|GetSystemInstanceList| X|  X|  Dostępność wystąpienia, serwer komunikatów, brama, ICM, dostępność ABAP|
|GetProcessList|    X|  X|  Jeśli lista wystąpień ma kolor CZERWONY, możemy określić, który proces powoduje, że ten serwer jest czerwony|
|GetQueueStatistic| X|  X|  Statystyki kolejki (DIA/BATCH/UPD)|
|TABELA ABAPGetWPTable|    X|   -| Wykorzystanie procesów pracy|
|EnqGetStatistic|   X   |X  |Blokady|

W publicznej wersji zapoznawczej klienci mogą spodziewać się następujących danych u dostawcy SAP NetWeaver: 
- Dostępność systemu i wystąpienia
- Wykorzystanie procesów pracy
- Wykorzystanie kolejki
- Statystyka blokady kolejkowania.

![image (obraz)](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="next-steps"></a>Następne kroki

- Utwórz swój pierwszy Azure Monitor zasobów rozwiązań SAP.
- Czy masz pytania dotyczące Azure Monitor dla rozwiązań SAP? Zapoznaj się z [sekcją często zadawanych](./azure-monitor-faq.md) pytań
