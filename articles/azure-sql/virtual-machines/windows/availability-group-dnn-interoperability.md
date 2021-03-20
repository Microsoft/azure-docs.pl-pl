---
title: Współdziałanie funkcji z grupami dostępności i odbiornikiem DNN
description: 'Zapoznaj się z dodatkowymi zagadnieniami dotyczącymi pracy z pewnymi SQL Server funkcjami i odbiornikiem nazwy sieci rozproszonej (DNN) z zawsze włączonymi grupami dostępności na SQL Server na maszynach wirtualnych platformy Azure. '
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
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 19b4b7407468b19419e2f85193b1f8fb6ace39c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359408"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>Współdziałanie funkcji z odbiornikiem AG i DNN 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Istnieją pewne SQL Server funkcje, które opierają się na zakodowanej nazwie sieci wirtualnej (VNN). W związku z tym, gdy korzystasz z odbiornika nazwy sieci rozproszonej (DNN) z zawsze włączonymi grupami dostępności i SQL Server na maszynach wirtualnych platformy Azure, mogą wystąpić pewne dodatkowe zagadnienia. 

W tym artykule szczegółowo opisano SQL Server funkcje i współdziałanie z odbiornikiem DNN grupy dostępności. 


## <a name="client-drivers"></a>Sterowniki klienta

W przypadku sterowników ODBC, OLEDB, ADO.NET, JDBC, PHP i Node.js użytkownicy muszą jawnie określić nazwę i port odbiornika DNN jako nazwę serwera w parametrach połączenia. Aby zapewnić szybką łączność po przejściu w tryb failover, należy dodać `MultiSubnetFailover=True` do parametrów połączenia, jeśli jest on obsługiwany przez klienta SQL. 

## <a name="tools"></a>Narzędzia

Użytkownicy narzędzi [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)i [SQL Server danych](/sql/ssdt/sql-server-data-tools) muszą jawnie określić nazwę i port odbiornika DNN jako nazwę serwera w parametrach połączenia, aby połączyć się z odbiornikiem. 

Tworzenie odbiornika DNN za pomocą interfejsu GUI SQL Server Management Studio (SSMS) nie jest obecnie obsługiwane. 


## <a name="availability-groups-and-fci"></a>Grupy dostępności i FCI

Zawsze włączona Grupa dostępności można skonfigurować przy użyciu wystąpienia klastra trybu failover (FCI) jako jednej z replik. Aby ta konfiguracja działała z odbiornikiem DNN, [wystąpienie klastra trybu failover musi również używać DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md) , ponieważ nie ma możliwości umieszczenia wirtualnego adresu IP FCI na liście adresów IP w sieci AG DNN. 

W tej konfiguracji adres URL punktu końcowego dublowania dla repliki FCI musi używać FCI DNN. Podobnie, jeśli FCI jest używany jako replika tylko do odczytu, routing tylko do odczytu do repliki FCI musi używać DNN FCI. 

Format punktu końcowego dublowania to: `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` . 

Na przykład jeśli nazwa DNS FCI DNN to `dnnlsnr` i `5022` Port punktu końcowego dublowania FCI, fragment kodu języka Transact-SQL (T-SQL), aby utworzyć adres URL punktu końcowego wygląda następująco: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Analogicznie, format adresu URL routingu tylko do odczytu to: `TCP://<FCI DNN DNS name>:<SQL Server instance port>` . 

Na przykład jeśli nazwa DNS DNN to `dnnlsnr` i `1444` port używany przez element docelowy tylko do odczytu SQL Server FCI, fragment kodu T-SQL, aby utworzyć adres URL routingu tylko do odczytu, wygląda następująco: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Możesz pominąć port w adresie URL, jeśli jest to domyślny port 1433. Dla nazwanego wystąpienia Skonfiguruj statyczny port dla nazwanego wystąpienia i określ go w adresie URL routingu tylko do odczytu.  

## <a name="distributed-availability-group"></a>Rozproszona Grupa dostępności

Rozproszone grupy dostępności nie są obecnie obsługiwane przez odbiornik DNN. 

## <a name="replication"></a>Replikacja

Replikacja transakcyjna, scalająca i migawek wszystkie obsługują wymianę odbiornika VNN z odbiornikiem DNN i portem w obiektach replikacji łączących się z odbiornikiem. 

Aby uzyskać więcej informacji na temat korzystania z replikacji z grupami dostępności, zobacz [Publisher, AG](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server), [Subscriber i AG](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server)oraz [dystrybutor i AG](/sql/relational-databases/replication/configure-distribution-availability-group).

## <a name="msdtc"></a>ZNAJDUJĄC

Obsługiwane są zarówno lokalne, jak i klastrowane usługi MSDTC, ale usługa MSDTC korzysta z portu dynamicznego, który wymaga standardowej Azure Load Balancer konfigurowania portu HA. W związku z tym maszyna wirtualna musi używać standardowej rezerwacji adresu IP lub nie może być ujawniona w Internecie. 

Zdefiniuj dwie reguły, jeden dla portu mapowania punktów końcowych wywołań RPC 135 i jeden dla rzeczywistego portu usługi MSDTC. Po przejściu w tryb failover należy zmodyfikować regułę LB na nowy port usługi MSDTC po zmianie w nowym węźle. 

Jeśli usługa MSDTC jest lokalna, pamiętaj, aby zezwolić na komunikację wychodzącą. 

## <a name="distributed-query"></a>Zapytanie rozproszone 

Zapytanie rozproszone bazuje na połączonym serwerze, który można skonfigurować za pomocą odbiornika i portu usługi AG DNN. Jeśli port nie jest 1433, wybierz opcję **Użyj innego źródła danych** w SQL Server Management Studio (SSMS) podczas konfigurowania połączonego serwera. 

## <a name="filestream"></a>Strumieni

Element FILESTREAM jest obsługiwany, ale nie w scenariuszach, w których użytkownicy uzyskują dostęp do udziału plików w zakresie przy użyciu interfejsu API plików systemu Windows. 

## <a name="filetable"></a>Tabela plików

Obiekt FileTable jest obsługiwany, ale nie dla scenariuszy, w których użytkownicy uzyskują dostęp do udziału plików w zakresie przy użyciu interfejsu API plików systemu Windows. 

## <a name="linked-servers"></a>Serwery połączone

Skonfiguruj połączony serwer przy użyciu nazwy i portu odbiornika AG DNN. Jeśli port nie jest 1433, wybierz opcję **Użyj innego źródła danych** w SQL Server Management Studio (SSMS) podczas konfigurowania połączonego serwera. 


## <a name="frequently-asked-questions"></a>Często zadawane pytania


- Która wersja SQL Server zapewnia obsługę odbiornika AG DNN? 

   SQL Server 2019 CU8 i nowszych.

- Jaki jest oczekiwany czas pracy w trybie failover w przypadku użycia odbiornika DNN?

   W przypadku odbiornika DNN czas pracy w trybie failover będzie wynosił zaledwie czas pracy w trybie failover, bez dodatkowego czasu (np. czasu sondy podczas korzystania z Azure Load Balancer).

- Czy istnieje wymagana wersja programu SQL Server do obsługi DNN z użyciem OLEDB i ODBC?

   Zalecamy `MultiSubnetFailover=True` obsługę parametrów połączenia dla odbiornika DNN. Jest on dostępny od SQL Server 2012 (11. x).

- Czy jakiekolwiek zmiany konfiguracji SQL Server są wymagane do korzystania z odbiornika DNN? 

   SQL Server nie wymaga żadnej zmiany konfiguracji do korzystania z DNN, ale niektóre funkcje SQL Server mogą wymagać większej uwagi. 

- Czy DNN obsługuje klastry z wieloma podsieciami?

   Tak. Klaster wiąże DNN w systemie DNS z fizycznymi adresami IP wszystkich replik w dostępności niezależnie od podsieci. Klient SQL próbuje wszystkie adresy IP nazwy DNS niezależnie od podsieci. 



## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz: 

- [Technologie klastrów systemu Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Zawsze włączona Grupa dostępności](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

