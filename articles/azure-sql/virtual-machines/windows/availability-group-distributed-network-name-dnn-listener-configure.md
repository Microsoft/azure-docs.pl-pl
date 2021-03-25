---
title: Konfigurowanie odbiornika DNN dla grupy dostępności
description: Dowiedz się, jak skonfigurować odbiornik nazwy sieci rozproszonej (DNN) w celu zamiany odbiornika nazwy sieci wirtualnej (VNN) i kierowania ruchu do grupy dostępności zawsze włączone na SQL Server na maszynie wirtualnej platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bf5c4c39ea8f5705cc9788fdcf2cddd01dcb4087
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105034711"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Konfigurowanie odbiornika DNN dla grupy dostępności
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W przypadku SQL Server na maszynach wirtualnych platformy Azure nazwa sieci rozproszonej (DNN) kieruje ruch do odpowiedniego zasobu klastra. Zapewnia łatwiejszy sposób łączenia się z zawsze włączonymi grupami dostępności (AG) niż odbiornik nazw sieci wirtualnej (VNN), bez konieczności Azure Load Balancer.

W tym artykule przedstawiono sposób konfigurowania odbiornika DNN w celu zastąpienia odbiornika VNN i kierowania ruchu do grupy dostępności przy użyciu SQL Server na maszynach wirtualnych platformy Azure w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii (HADR cluster).

Funkcja odbiornika DNN jest obecnie dostępna tylko w SQL Server 2019 CU8 w systemie Windows Server 2016 lub nowszym.

W przypadku alternatywnej opcji łączności Rozważ użycie [odbiornika VNN i Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) zamiast tego.

## <a name="overview"></a>Omówienie

Odbiornik nazwy sieci rozproszonej (DNN) zamienia odbiornik grupy dostępności tradycyjnej nazwy sieci wirtualnej (VNN), gdy jest używany z [zawsze włączonymi grupami dostępności na maszynach wirtualnych SQL Server](availability-group-overview.md). Wyklucza to konieczność przesyłania ruchu przez Azure Load Balancer, upraszczając wdrażanie, konserwację i ulepszanie trybu failover.

Użyj odbiornika DNN, aby zastąpić istniejący odbiornik VNN lub użyć go w połączeniu z istniejącym odbiornikiem VNN, dzięki czemu Grupa dostępności ma dwa odrębne punkty połączenia — jeden przy użyciu nazwy odbiornika VNN (i portu, jeśli nie jest to ustawienie domyślne), a drugi przy użyciu nazwy i portu odbiornika DNN.

> [!CAUTION]
> Zachowanie routingu w przypadku korzystania z DNN różni się w przypadku korzystania z VNN. Nie używaj portu 1433. Aby dowiedzieć się więcej, zobacz sekcję dotyczącą [zagadnień dotyczących portów](#port-considerations) w dalszej części tego artykułu.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym artykule należy dysponować:

- SQL Server 2019 w CU8 lub nowszym w systemie Windows Server 2016 lub nowszym
- Zdecyduj, że nazwa sieci rozproszonej jest odpowiednią [opcją łączności dla rozwiązania HADR Cluster](hadr-cluster-best-practices.md#connectivity).
- Skonfigurowano [zawsze włączone grupy dostępności](availability-group-overview.md). 
- Zainstalowano najnowszą wersję programu [PowerShell](/powershell/azure/install-az-ps). 
- Zidentyfikowano unikatowy port, który będzie używany dla odbiornika DNN. Port używany dla odbiornika DNN musi być unikatowy w ramach wszystkich replik grupy dostępności lub klastra trybu failover.  Żadne inne połączenie nie może współużytkować tego samego portu.



## <a name="create-script"></a>Utwórz skrypt

Utwórz zasób Nazwa sieci rozproszonej (DNN) i skojarz go z grupą dostępności przy użyciu programu PowerShell.

W tym celu wykonaj następujące czynności:

1. Otwórz edytor tekstu, na przykład Notatnik.
1. Skopiuj i wklej następujący skrypt:

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Zapisz skrypt jako `.ps1` plik, taki jak `add_dnn_listener.ps1` .

## <a name="execute-script"></a>Wykonaj skrypt

Aby utworzyć odbiornik DNN, wykonaj skrypt przekazywania parametrów dla nazwy grupy dostępności, nazwy odbiornika i portu.

Na przykład przy założeniu, że nazwa grupy dostępności `ag1` , nazwa odbiornika `dnnlsnr` i port odbiornika jako `6789` , wykonaj następujące czynności:

1. Otwórz narzędzie interfejsu wiersza polecenia, takie jak wiersz polecenia lub program PowerShell.
1. Przejdź do lokalizacji, w której zapisano `.ps1` skrypt, taki jak c:\Documents.
1. Wykonaj skrypt: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>``` . Na przykład:

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Weryfikuj odbiornik

Użyj SQL Server Management Studio lub Transact-SQL, aby potwierdzić, że odbiornik DNN został pomyślnie utworzony.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Rozwiń węzeł **odbiorniki grupy dostępności** w [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) , aby wyświetlić odbiornik DNN:

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Wyświetlanie odbiornika DNN w obszarze odbiorniki grupy dostępności w SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Aby wyświetlić stan odbiornika DNN, Użyj języka Transact-SQL:

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

Wartość `1` parametru dla `is_distributed_network_name` wskazuje, że odbiornik jest odbiornikiem nazwy sieci rozproszonej (DNN):

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Użyj sys.availability_group_listeners, aby zidentyfikować detektory DNN mające wartość 1 w is_distributed_network_name":::

## <a name="update-connection-string"></a>Zaktualizuj parametry połączenia

Zaktualizuj parametry połączenia dla aplikacji tak, aby łączyły się z odbiornikiem DNN. Parametry połączenia dla odbiorników DNN muszą podawać numer portu DNN. Aby zapewnić szybką łączność po przejściu w tryb failover, należy dodać `MultiSubnetFailover=True` do parametrów połączenia, jeśli jest on obsługiwany przez klienta SQL.

## <a name="test-failover"></a>Testowanie pracy w trybie failover

Przetestuj tryb failover grupy dostępności, aby zapewnić funkcjonalność.

Aby przetestować tryb failover, wykonaj następujące kroki:

1. Połącz się z odbiornikiem DNN lub jedną z replik za pomocą [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
1. Rozwiń opcję **zawsze włączone grupy dostępności** w **Eksplorator obiektów**.
1. Kliknij prawym przyciskiem myszy grupę dostępności, a następnie wybierz pozycję **tryb failover** , aby otworzyć **Kreatora trybu failover**.
1. Postępuj zgodnie z monitami, aby wybrać miejsce docelowe trybu failover, a grupa dostępności nie została przeniesiona na replikę pomocniczą.
1. Upewnij się, że baza danych jest w stanie zsynchronizowanym w nowej replice podstawowej.
1. Obowiązkowe Powrót po awarii do oryginalnej podstawowej lub innej repliki pomocniczej.

## <a name="test-connectivity"></a>Testowanie łączności

Przetestuj łączność z odbiornikiem DNN, wykonując następujące czynności:

1. Otwórz [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Nawiąż połączenie z odbiornikiem DNN.
1. Otwórz nowe okno zapytania i sprawdź, z którą repliką jest nawiązane połączenie `SELECT @@SERVERNAME` .
1. Nie można przełączyć grupy dostępności do innej repliki.
1. Po upływie rozsądnego czasu Uruchom polecenie, `SELECT @@SERVERNAME` Aby potwierdzić, że grupa dostępności jest teraz hostowana w innej replice.

## <a name="limitations"></a>Ograniczenia

- Obecnie odbiornik DNN dla grupy dostępności jest obsługiwany tylko w przypadku SQL Server 2019 CU8 i nowszych w systemie Windows Server 2016 lub nowszym. 
- Odbiorniki DNN **muszą** być skonfigurowane przy użyciu unikatowego portu.  Port nie może być współużytkowany z żadnym innym połączeniem w żadnej replice.
- Podczas pracy z innymi funkcjami SQL Server i grupą dostępności z DNN może wystąpić dodatkowe zagadnienia. Aby uzyskać więcej informacji, zobacz [AG ze współdziałaniem DNN](availability-group-dnn-interoperability.md). 

## <a name="port-considerations"></a>Zagadnienia dotyczące portów

Odbiorniki DNN są zaprojektowane do nasłuchiwania na wszystkich adresach IP, ale na określonym, unikatowym porcie. Wpis DNS dla nazwy odbiornika powinien zostać rozpoznany jako adresy wszystkich replik w grupie dostępności. Jest to wykonywane automatycznie przy użyciu skryptu programu PowerShell podanego w sekcji [Tworzenie skryptu](#create-script) . Ponieważ odbiorniki DNN akceptują połączenia ze wszystkimi adresami IP, należy pamiętać, że port odbiornika jest unikatowy i nie jest używany przez żadną inną replikę w grupie dostępności. Ponieważ SQL Server zawsze nasłuchuje na porcie 1433, bezpośrednio lub za pośrednictwem usługi SQL Browser, nie można użyć portu 1433 dla żadnego odbiornika DNN.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat SQL Server funkcji HADR Cluster na platformie Azure, zobacz [grupy dostępności](availability-group-overview.md) i [wystąpienie klastra trybu failover](failover-cluster-instance-overview.md). Możesz również poznać [najlepsze rozwiązania](hadr-cluster-best-practices.md) dotyczące konfigurowania środowiska pod kątem wysokiej dostępności i odzyskiwania po awarii. 
