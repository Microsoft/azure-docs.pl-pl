---
title: Współdziałanie z funkcją SQL Server FCI & DNN
description: 'Zapoznaj się z dodatkowymi zagadnieniami dotyczącymi pracy z pewnymi SQL Server funkcjami i zasobem rozproszonej sieci nazw (DNN) z wystąpieniem klastra trybu failover na SQL Server na maszynach wirtualnych platformy Azure. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358218"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>Współdziałanie z funkcją SQL Server FCI & DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Istnieją pewne SQL Server funkcje, które opierają się na zakodowanej nazwie sieci wirtualnej (VNN). W związku z tym w przypadku używania zasobu nazwy sieci rozproszonej (DNN) z wystąpieniem klastra trybu failover i SQL Server na maszynach wirtualnych platformy Azure należy wziąć pod uwagę pewne dodatkowe zagadnienia. 

W tym artykule dowiesz się, jak skonfigurować alias sieci przy użyciu zasobu DNN oraz które funkcje SQL Server wymagają dodatkowej uwagi.

## <a name="create-network-alias-fci"></a>Utwórz alias sieci (FCI)

Niektóre składniki po stronie serwera korzystają z zakodowanej wartości VNN i wymagają aliasu sieciowego, który mapuje VNN na nazwę DNS DNN w celu poprawnego działania. Postępuj zgodnie z instrukcjami w temacie [Tworzenie aliasu serwera](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) , aby utworzyć alias, który mapuje VNN na nazwę DNS DNN. 

Dla wystąpienia domyślnego można mapować VNN na nazwę DNS DNN bezpośrednio, na przykład VNN = DNN DNS.
Na przykład, jeśli nazwa VNN to, `FCI1` Nazwa wystąpienia to `MSSQLSERVER` , a DNN to `FCI1DNN` (klienci wcześniej połączeni z `FCI` i teraz łączą się z), `FCI1DNN` Mapuj VNN `FCI1` na DNN `FCI1DNN` . 

Dla nazwanego wystąpienia mapowanie aliasu sieci należy wykonać dla pełnego wystąpienia, na przykład `VNN\Instance`  =  `DNN\Instance` . Na przykład, jeśli nazwa VNN to, `FCI1` Nazwa wystąpienia to `instA` , a DNN to `FCI1DNN` (klienci wcześniej połączeni z `FCI1\instA` i teraz łączą się z), `FCI1DNN\instaA` Mapuj VNN `FCI1\instaA` na DNN `FCI1DNN\instaA` . 



## <a name="client-drivers"></a>Sterowniki klienta

W przypadku sterowników ODBC, OLEDB, ADO.NET, JDBC, PHP i Node.js użytkownicy muszą jawnie określić nazwę DNS DNN jako nazwę serwera w parametrach połączenia. Aby zapewnić szybką łączność po przejściu w tryb failover, należy dodać `MultiSubnetFailover=True` do parametrów połączenia, jeśli jest on obsługiwany przez klienta SQL. 

## <a name="tools"></a>Narzędzia

Użytkownicy narzędzi [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)i [SQL Server danych](/sql/ssdt/sql-server-data-tools) muszą jawnie określić nazwę DNS DNN jako nazwę serwera w parametrach połączenia. 

## <a name="availability-groups-and-fci"></a>Grupy dostępności i FCI

Zawsze włączona Grupa dostępności można skonfigurować przy użyciu wystąpienia klastra trybu failover jako jednej z replik. W tej konfiguracji adres URL punktu końcowego dublowania dla repliki FCI musi używać FCI DNN. Podobnie, jeśli FCI jest używany jako replika tylko do odczytu, routing tylko do odczytu do repliki FCI musi używać DNN FCI. 

Format punktu końcowego dublowania to: `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` . 

Na przykład jeśli nazwa DNS DNN to `dnnlsnr` i `5022` Port punktu końcowego dublowania FCI, fragment kodu języka Transact-SQL (T-SQL), aby utworzyć adres URL punktu końcowego, wygląda następująco: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Analogicznie, format adresu URL routingu tylko do odczytu to: `TCP://<DNN DNS name>:<SQL Server instance port>` . 

Na przykład jeśli nazwa DNS DNN to `dnnlsnr` i `1444` port używany przez element docelowy tylko do odczytu SQL Server FCI, fragment kodu T-SQL, aby utworzyć adres URL routingu tylko do odczytu, wygląda następująco: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Możesz pominąć port w adresie URL, jeśli jest to domyślny port 1433. Dla nazwanego wystąpienia Skonfiguruj statyczny port dla nazwanego wystąpienia i określ go w adresie URL routingu tylko do odczytu.  

## <a name="replication"></a>Replikacja

Replikacja ma trzy składniki: Wydawca, dystrybutor, subskrybent. Każdy z tych składników może być wystąpieniem klastra trybu failover. Ponieważ FCI VNN jest silnie używana w konfiguracji replikacji, zarówno jawnie, jak i niejawnie, alias sieci, który mapuje VNN do DNN, może być niezbędny do działania replikacji. 

Należy nadal używać nazwy VNN jako nazwy FCI w ramach replikacji, ale *przed skonfigurowaniem replikacji* należy utworzyć alias sieciowy w następujących sytuacjach zdalnych:

| **Składnik replikacji (FCI z DNN)** | **Składnik zdalny** | **Mapa aliasów sieciowych** | **Serwer z mapą sieci**| 
|---------|---------|---------|-------- | 
|Publisher | Dystrybutor | Wydawca VNN do wydawcy DNN| Dystrybutor| 
|Dystrybutor|Subscriber |Dystrybutor VNN do dystrybutora DNN| Subscriber | 
|Dystrybutor|Publisher | Dystrybutor VNN do dystrybutora DNN | Publisher| 
|Subscriber| Dystrybutor| Subskrybent VNN DNN | Dystrybutor| 

Załóżmy na przykład, że masz wydawcę, który jest skonfigurowany jako FCI przy użyciu DNN w topologii replikacji, a dystrybutor jest zdalny. W takim przypadku utwórz alias sieciowy na serwerze dystrybutora, aby zamapować VNN wydawcy na DNN wydawcy: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="Skonfiguruj nazwę DNS DNN jako alias sieci przy użyciu SQL Server Configuration Manager." :::

Użyj pełnej nazwy wystąpienia dla nazwanego wystąpienia, takiego jak Poniższy przykład obrazu: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="Użyj pełnej nazwy wystąpienia podczas konfigurowania aliasu sieciowego dla nazwanego wystąpienia." :::

## <a name="database-mirroring"></a>Dublowanie bazy danych

Dublowanie bazy danych można skonfigurować za pomocą FCI jako partnera dublowania baz danych. Skonfiguruj go przy użyciu [języka Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) zamiast graficznego interfejsu użytkownika SQL Server Management Studio. Przy użyciu języka T-SQL, upewnij się, że punkt końcowy dublowania bazy danych jest tworzony przy użyciu DNN zamiast VNN. 

Na przykład jeśli nazwa DNS DNN ma wartość `dnnlsnr` , a punkt końcowy dublowania bazy danych to 7022, następujący fragment kodu T-SQL konfiguruje partnera dublowania baz danych: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

W przypadku dostępu klienta Właściwość **partner trybu failover** może obsłużyć tryb failover dublowania baz danych, ale nie FCI trybu failover. 

## <a name="msdtc"></a>ZNAJDUJĄC

FCI może uczestniczyć w transakcjach rozproszonych koordynowanych przez firmę Microsoft Distributed Transaction Coordinator (MSDTC). Mimo że obsługiwane są zarówno klastrowane usługi MSDTC, jak i lokalne usługi MSDTC, w przypadku używania usługi FCI DNN, moduł równoważenia obciążenia jest nadal potrzebny dla klastrowanej usługi MSDTC. DNN zdefiniowane w FCI nie zastępuje wymagania Azure Load Balancer dla klastrowanej usługi MSDTC na platformie Azure. 

## <a name="filestream"></a>Strumieni

Chociaż element FileStream jest obsługiwany dla bazy danych w FCI, uzyskanie dostępu do FileStream lub FileTable przy użyciu interfejsów API systemu plików z DNN nie jest obsługiwane. 

## <a name="linked-servers"></a>Serwery połączone

Obsługiwane jest używanie połączonego serwera z FCI DNN. Użyj DNN bezpośrednio, aby skonfigurować połączony serwer, lub użyj aliasu sieciowego do mapowania VNN na DNN. 


Na przykład aby utworzyć połączony serwer z nazwą DNS DNN `dnnlsnr` dla nazwanego wystąpienia `insta1` , użyj następującego polecenia języka Transact-SQL (T-SQL):

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

Zamiast tego można także utworzyć połączony serwer przy użyciu nazwy sieci wirtualnej (VNN), ale należy zdefiniować alias sieciowy do mapowania VNN na DNN. 

Na przykład, w przypadku nazwy wystąpienia `insta1` , nazwy VNN `vnnname` i nazwy DNN `dnnlsnr` , użyj następującego polecenia języka Transact-SQL (T-SQL), aby utworzyć połączony serwer przy użyciu VNN:

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

Następnie utwórz alias sieciowy do zamapowania `vnnname\insta1` `dnnlsnr\insta1` . 



## <a name="frequently-asked-questions"></a>Często zadawane pytania


- Która wersja SQL Server zapewnia pomoc techniczną DNN? 

   SQL Server 2019 ZASTOSUJESZ pakietu CU2 i nowszych.

- Jaki jest oczekiwany czas pracy w trybie failover, gdy jest używany DNN?

   W przypadku DNN czas pracy w trybie failover będzie przekroczyć czas pracy w trybie failover FCI, bez dowolnych godzin (na przykład czasu sondy podczas korzystania z Azure Load Balancer).

- Czy istnieje wymagana wersja programu SQL Server do obsługi DNN z użyciem OLEDB i ODBC?

   Zalecamy `MultiSubnetFailover=True` obsługę parametrów połączenia dla DNN. Jest on dostępny od SQL Server 2012 (11. x).

- Czy jakiekolwiek zmiany konfiguracji SQL Server są wymagane do korzystania z DNN? 

   SQL Server nie wymaga żadnej zmiany konfiguracji do korzystania z DNN, ale niektóre funkcje SQL Server mogą wymagać większej uwagi. 

- Czy DNN obsługuje klastry z wieloma podsieciami?

   Tak. Klaster wiąże DNN w systemie DNS z fizycznymi adresami IP wszystkich węzłów w klastrze niezależnie od podsieci. Klient SQL próbuje wszystkie adresy IP nazwy DNS niezależnie od podsieci. 



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz: 

- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server wystąpienia klastra trybu failover](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

