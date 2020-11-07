---
title: Azure Monitor dla dostawców rozwiązań SAP | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure monitor dla dostawców rozwiązań SAP.
author: rdeltcheva
ms.service: virtual-machines
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: f5df8bccc10ca64ee9a04f195299c5228b7274c1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356454"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Usługa Azure monitor dla dostawców rozwiązań SAP (wersja zapoznawcza)

## <a name="overview"></a>Omówienie  

W kontekście Azure Monitor dla rozwiązań SAP *Typ dostawcy* odnosi się do określonego *dostawcy*. Na przykład *SAP HANA* , który jest skonfigurowany dla określonego składnika w oprogramowaniu SAP, takich jak SAP HANA Database. Dostawca zawiera informacje o połączeniu dla odpowiedniego składnika i pomaga zbierać dane telemetryczne z tego składnika. Jeden Azure Monitor dla zasobu rozwiązań SAP (znany również jako zasób monitora SAP) można skonfigurować przy użyciu wielu dostawców tego samego typu dostawcy lub wielu dostawców z wieloma typami dostawcy.
   
Klienci mogą konfigurować różne typy dostawców, aby umożliwić zbieranie danych z odpowiedniego składnika w systemie SAP. Na przykład klienci mogą skonfigurować jednego dostawcę dla typu dostawcy SAP HANA, innego dostawcę dla typu dostawcy klastrów o wysokiej dostępności i tak dalej.  

Klienci mogą również skonfigurować wielu dostawców określonego typu dostawcy, aby ponownie używać tego samego zasobu monitora SAP i skojarzonej grupy zarządzanej. Więcej informacji na temat zarządzanej grupy zasobów. W publicznej wersji zapoznawczej obsługiwane są następujące typy dostawców:   
- SAP HANA
- Klaster o wysokiej dostępności
- Microsoft SQL Server

![Azure Monitor dla dostawców rozwiązań SAP](./media/azure-monitor-sap/azure-monitor-providers.png)

Klienci są zalecani do skonfigurowania co najmniej jednego dostawcy spośród dostępnych typów dostawców w momencie wdrażania zasobu monitorowania SAP. Konfigurując dostawcę, klienci inicjują zbieranie danych z odpowiedniego składnika, dla którego skonfigurowano dostawcę.   

Jeśli klienci nie skonfigurują żadnych dostawców w czasie wdrażania zasobu monitora SAP, mimo że zasób monitora SAP zostanie pomyślnie wdrożony, nie będą zbierane żadne dane telemetryczne. Klienci mają możliwość dodawania dostawców po wdrożeniu za pomocą zasobu monitorowania SAP w ramach Azure Portal. Klienci mogą dodawać lub usuwać dostawców z zasobu monitorowania SAP w dowolnym momencie.

> [!Tip]
> Jeśli chcesz, aby firma Microsoft zaimplementował określonego dostawcę, wystaw opinię za pomocą linku na końcu tego dokumentu lub skontaktuj się z zespołem ds. klientów.  

## <a name="provider-type-sap-hana"></a>Typ dostawcy SAP HANA

Klienci mogą skonfigurować jednego lub więcej dostawców typu dostawcy *SAP HANA* , aby umożliwić zbieranie danych z bazy danych SAP HANA Database. Dostawca SAP HANA nawiązuje połączenie z bazą danych SAP HANA za pośrednictwem portu SQL, pobiera dane telemetryczne z bazy danych i wypycha je do obszaru roboczego Log Analytics w ramach subskrypcji klienta. Dostawca SAP HANA zbiera dane co 1 minutę z bazy danych SAP HANA.  

W publicznej wersji zapoznawczej klienci mogą oczekiwać, że następujące dane są dostępne dla SAP HANA dostawcy: podstawowego wykorzystania infrastruktury, SAP HANA stanu hosta, SAP HANA replikacji systemu i SAP HANA kopii zapasowych danych telemetrycznych. Aby skonfigurować dostawcę SAP HANA, adres IP hosta, numer portu SQL HANA i SYSTEMDB nazwę użytkownika i hasło są wymagane. Klienci są zalecani do konfigurowania dostawcy SAP HANA w SYSTEMDB, ale można skonfigurować dodatkowych dostawców dla innych dzierżawców bazy danych.

![Azure Monitor dla dostawców rozwiązań SAP — SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Klaster o wysokiej dostępności typu dostawcy
Klienci mogą skonfigurować co najmniej jednego dostawcę typu dostawcy *o wysokiej dostępności* , aby umożliwić zbieranie danych z klastra Pacemaker w poziomie SAP. Dostawca klastrów o wysokiej dostępności nawiązuje połączenie z usługą Pacemaker przy użyciu punktu końcowego [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) , ściąga dane telemetryczne z bazy danych i wypycha je do log Analytics obszaru roboczego w ramach subskrypcji klienta. Dostawca klastrów o wysokiej dostępności zbiera dane co 60 sekund od Pacemaker.  

W publicznej wersji zapoznawczej klienci mogą oczekiwać, że zobaczysz następujące dane z dostawcą klastrów o wysokiej dostępności:   
 - Stan klastra reprezentowany jako pakiet zbiorczy stanu węzła i zasobu 
 - [Notes](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor dla dostawców rozwiązań SAP — klaster o wysokiej dostępności](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Aby skonfigurować dostawcę klastrów o wysokiej dostępności, należy wykonać dwa podstawowe czynności:

1. Zainstaluj [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) w *każdym* węźle w klastrze Pacemaker.

   Dostępne są dwie opcje instalacji ha_cluster_exporter:
   
   - Użyj skryptów Azure Automation do wdrożenia klastra o wysokiej dostępności. Skrypty instalują [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) na każdym węźle klastra.  
   - Wykonaj [instalację ręczną](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build). 

2. Skonfiguruj dostawcę klastrów o wysokiej dostępności dla *każdego* węzła w klastrze Pacemaker.

   Aby skonfigurować dostawcę klastrów o wysokiej dostępności, wymagane są następujące informacje:
   
   - **Nazwa**. Nazwa tego dostawcy. Dla tego Azure Monitor dla wystąpienia rozwiązań SAP powinna być unikatowa.
   - **Prometheus punkt końcowy**. Zwykle http \: // \<servername or ip address\> : 9664/Metrics.
   - **Identyfikator SID**. W przypadku systemów SAP Użyj identyfikatora SID SAP. W przypadku innych systemów (na przykład klastrów NFS) Użyj nazwy klastra o trzech znakach. Identyfikator SID musi być różny od innych monitorowanych klastrów.   
   - **Nazwa klastra**. Nazwa klastra użyta podczas tworzenia klastra. Nazwę klastra można znaleźć we właściwości klastra `cluster-name` .
   - **Nazwa hosta**. Nazwa hosta maszyny wirtualnej z systemem Linux.  

## <a name="provider-type-microsoft-sql-server"></a>Typ dostawcy programu Microsoft SQL Server

Klienci mogą skonfigurować jednego lub więcej dostawców typu dostawcy *Microsoft SQL Server* , aby umożliwić zbieranie danych z [program SQL Server w usłudze Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/). Dostawca SQL Server nawiązuje połączenie z Microsoft SQL Server za pośrednictwem portu SQL, pobiera dane telemetryczne z bazy danych i wypycha je do obszaru roboczego Log Analytics w ramach subskrypcji klienta. Należy utworzyć SQL Server na potrzeby uwierzytelniania SQL i logowania SQL Server z użyciem usługi SAP DB jako domyślnej bazy danych dla dostawcy. Dostawca SQL Server zbiera dane od co 60 sekund do co godzinę od programu SQL Server.  

W publicznej wersji zapoznawczej klienci mogą oczekiwać, że następujące dane są dostępne w ramach dostawcy SQL Server: bazowego wykorzystania infrastruktury, najważniejszych instrukcji SQL, najwyższej największej tabeli, problemów zarejestrowanych w dziennikach błędów SQL Server, blokowania procesów i innych.  

Aby skonfigurować dostawcę Microsoft SQL Server, wymagany jest identyfikator systemu SAP, adres IP hosta, SQL Server numer portu, a także SQL Server nazwę logowania i hasło.

![Azure Monitor dla dostawców rozwiązań SAP — SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Następne kroki

- Utwórz pierwszy Azure Monitor dla zasobu rozwiązań SAP.
- Masz pytania dotyczące Azure Monitor dla rozwiązań SAP? Zapoznaj się z sekcją [często zadawane pytania](./azure-monitor-faq.md)
