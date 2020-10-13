---
title: Konfigurowanie VNN z Azure Load Balancer dla HADR Cluster
description: Dowiedz się, jak skonfigurować moduł równoważenia obciążenia platformy Azure, aby kierować ruch do nazwy sieci wirtualnej (VNN) dla grupy dostępności lub wystąpienia klastra trybu failover (FCI) z SQL Server na maszynach wirtualnych platformy Azure w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii (HADR cluster).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2f36e568603ded5a89f88cf11627a09a5a240fac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316991"
---
# <a name="configure-vnn-with-azure-load-balancer-sql-server-on-azure-vms"></a>Konfigurowanie VNN z Azure Load Balancer (SQL Server na maszynach wirtualnych platformy Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W przypadku usługi Azure Virtual Machines klastry używają modułu równoważenia obciążenia do przechowywania adresu IP, który musi znajdować się w jednym węźle klastra naraz. W tym rozwiązaniu moduł równoważenia obciążenia przechowuje adres IP dla nazwy sieci wirtualnej (VNN) używanej przez zasób klastrowany na platformie Azure. 

W tym artykule przedstawiono sposób konfigurowania modułu równoważenia obciążenia za pomocą usługi Azure Load Balancer. Moduł równoważenia obciążenia kieruje ruch do usługi Azure [GROUPING (AG)](availability-group-overview.md) lub [wystąpień klastra pracy awaryjnej (FCIs)](failover-cluster-instance-overview.md) z SQL Server na maszynach wirtualnych z systemem na potrzeby wysokiej dostępności i odzyskiwania po awarii (HADR cluster). 



## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym artykule należy dysponować:

- Zdecydowano, że Azure Load Balancer jest odpowiednią [opcją łączności dla rozwiązania HADR Cluster](hadr-cluster-best-practices.md#connectivity).
- Skonfigurowano [odbiornik grupy dostępności](availability-group-overview.md) lub [wystąpienia klastra trybu failover](failover-cluster-instance-overview.md). 
- Zainstalowano najnowszą wersję programu [PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0). 


## <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Użyj [Azure Portal](https://portal.azure.com) , aby utworzyć moduł równoważenia obciążenia:

1. W Azure Portal przejdź do grupy zasobów zawierającej maszyny wirtualne.

1. Wybierz pozycję **Dodaj**. Wyszukaj **Load Balancer**w witrynie Azure Marketplace. Wybierz **Load Balancer**.

1. Wybierz przycisk **Utwórz**.

1. Skonfiguruj moduł równoważenia obciążenia przy użyciu następujących wartości:

   - **Subskrypcja**: Twoja subskrypcja platformy Azure.
   - **Grupa zasobów**: Grupa zasobów zawierająca maszyny wirtualne.
   - **Nazwa**: Nazwa identyfikująca moduł równoważenia obciążenia.
   - **Region**: Lokalizacja platformy Azure, w której znajdują się maszyny wirtualne.
   - **Typ**: publiczny lub prywatny. Dostęp do prywatnego modułu równoważenia obciążenia można uzyskać z poziomu sieci wirtualnej. Większość aplikacji platformy Azure może korzystać z prywatnego modułu równoważenia obciążenia. Jeśli aplikacja wymaga dostępu do SQL Server bezpośrednio za pośrednictwem Internetu, użyj publicznego modułu równoważenia obciążenia.
   - **Jednostka SKU**: Standard.
   - **Sieć wirtualna**: sieć, w której znajduje się maszyna wirtualna.
   - **Przypisanie adresu IP**: statyczne. 
   - **Prywatny adres IP**: adres IP przypisany do klastrowanego zasobu sieciowego.

   Na poniższej ilustracji przedstawiono interfejs użytkownika **tworzenia modułu równoważenia obciążenia** :

   ![Konfigurowanie modułu równoważenia obciążenia](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

## <a name="configure-backend-pool"></a>Konfigurowanie puli zaplecza

1. Wróć do grupy zasobów platformy Azure zawierającej maszyny wirtualne i Znajdź nowy moduł równoważenia obciążenia. Może być konieczne odświeżenie widoku w grupie zasobów. Wybierz moduł równoważenia obciążenia.

1. Wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

1. Skojarz pulę zaplecza z zestawem dostępności zawierającym maszyny wirtualne.

1. W obszarze **Konfiguracja IP sieci docelowej**wybierz pozycję **maszyna wirtualna** i wybierz maszyny wirtualne, które będą uczestniczyć w węzłach klastra. Pamiętaj, aby uwzględnić wszystkie maszyny wirtualne, które będą hostować FCIą lub grupę dostępności.

1. Wybierz **przycisk OK** , aby utworzyć pulę zaplecza.

## <a name="configure-health-probe"></a>Konfigurowanie sondy kondycji

1. W okienku moduł równoważenia obciążenia wybierz pozycję **sondy kondycji**.

1. Wybierz pozycję **Dodaj**.

1. W okienku **Dodawanie sondy kondycji** <span id="probe"> </span> ustaw następujące parametry sondy kondycji:

   - **Name**: Nazwa sondy kondycji.
   - **Protokół**: TCP.
   - **Port**: Port utworzony w zaporze dla sondy kondycji [podczas przygotowywania maszyny wirtualnej](failover-cluster-instance-prepare-vm.md#uninstall-sql-server-1). W tym artykule w przykładzie zastosowano port TCP `59999` .
   - **Interwał**: 5 sekund.
   - **Próg złej kondycji**: 2 kolejne błędy.

1. Wybierz przycisk **OK**.

## <a name="set-load-balancing-rules"></a>Ustawianie reguł równoważenia obciążenia

1. W okienku moduł równoważenia obciążenia wybierz pozycję **reguły równoważenia obciążenia**.

1. Wybierz pozycję **Dodaj**.

1. Ustaw parametry reguły równoważenia obciążenia:

   - **Name**: nazwa dla reguł równoważenia obciążenia.
   - **Adres IP frontonu**: adres IP dla klastrowanego zasobu sieci FCI lub odbiornika usługi SQL Server.
   - **Port**: Port SQL Server TCP. Domyślnym portem wystąpienia jest 1433.
   - **Port zaplecza**: ten sam port co wartość **portu** w przypadku włączenia **swobodnego adresu IP (bezpośredni zwrot serwera)**.
   - **Pula zaplecza**: Nazwa puli zaplecza, która została wcześniej skonfigurowana.
   - **Sonda kondycji**: Sonda kondycji, która została wcześniej skonfigurowana.
   - **Trwałość sesji**: Brak.
   - **Limit czasu bezczynności (w minutach)**: 4.
   - **Zmienny adres IP (bezpośredni zwrot serwera)**: włączony.

1. Wybierz przycisk **OK**.

## <a name="configure-cluster-probe"></a>Konfigurowanie sondy klastra

Ustaw parametr portu sondowania klastra w programie PowerShell.

Aby ustawić parametr port sondowania klastra, należy zaktualizować zmienne w następującym skrypcie przy użyciu wartości z danego środowiska. Usuń nawiasy kątowe ( `<` i `>` ) ze skryptu.

```powershell
$ClusterNetworkName = "<Cluster Network Name>"
$IPResourceName = "<SQL Server FCI / AG Listener IP Address Resource Name>" 
$ILBIP = "<n.n.n.n>" 
[int]$ProbePort = <nnnnn>

Import-Module FailoverClusters

Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
```

W poniższej tabeli opisano wartości, które należy zaktualizować:


|**Wartość**|**Opis**|
|---------|---------|
|`Cluster Network Name`| Nazwa klastra trybu failover systemu Windows Server dla sieci. W **Menedżer klastra trybu failover**  >  **sieci**, kliknij prawym przyciskiem myszy sieć i wybierz pozycję **Właściwości**. Poprawna wartość jest pod **nazwą** na karcie **Ogólne** .|
|`SQL Server FCI/AG listener IP Address Resource Name`|Nazwa zasobu dla adresu IP odbiornika SQL Server FCI lub AG. W **Menedżer klastra trybu failover**  >  **role**w obszarze SQL Server rolę FCI w obszarze **Nazwa serwera**kliknij prawym przyciskiem myszy zasób adres IP i wybierz pozycję **Właściwości**. Poprawna wartość jest pod **nazwą** na karcie **Ogólne** .|
|`ILBIP`|Adres IP wewnętrznego modułu równoważenia obciążenia (ILB). Ten adres jest skonfigurowany w Azure Portal jako adres frontonu ILB. Jest to również adres IP SQL Server FCI. Można go znaleźć w **Menedżer klastra trybu failover** na tej samej stronie właściwości, na której został umieszczony `<SQL Server FCI/AG listener IP Address Resource Name>` .|
|`nnnnn`|Port sondy, który został skonfigurowany w sondie kondycji modułu równoważenia obciążenia. Dowolny nieużywany port TCP jest prawidłowy.|
|"Maska_podsieci"| Maska podsieci parametru Cluster. Musi to być adres emisji IP protokołu TCP: `255.255.255.255` .| 


Po ustawieniu sondy klastra można zobaczyć wszystkie parametry klastra w programie PowerShell. Uruchom ten skrypt:

```powershell
Get-ClusterResource $IPResourceName | Get-ClusterParameter
```


## <a name="test-failover"></a>Testowanie pracy w trybie failover


Przetestuj tryb failover zasobu klastra, aby sprawdzić poprawność funkcjonalności klastra. 

# <a name="failover-cluster-instance"></a>[Wystąpienie klastra trybu failover](#tab/fci)

Wykonaj następujące czynności:

1. Połącz się z jednym z SQL Server węzłów klastra przy użyciu protokołu RDP.
1. Otwórz **Menedżer klastra trybu failover**. Wybierz pozycję **role**. Zwróć uwagę na to, który węzeł jest właścicielem SQL Server roli FCI.
1. Kliknij prawym przyciskiem myszy SQL Server rolę FCI. 
1. Wybierz pozycję **Przenieś**, a następnie wybierz pozycję **najlepszy możliwy węzeł**.

**Menedżer klastra trybu failover** pokazuje rolę i jej zasoby przechodzą do trybu offline. Zasoby zostaną następnie przeniesione i przywrócone do trybu online w innym węźle.



# <a name="ag-listener"></a>[Odbiornik AG](#tab/ag)

Wykonaj następujące czynności:

1. Otwórz [SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) i Połącz się z odbiornikiem grupy dostępności. 

1. Rozwiń opcję **zawsze włączone grupy dostępności** w **Eksplorator obiektów**. 
1. Kliknij prawym przyciskiem myszy grupę dostępności i wybierz pozycję **tryb failover**. 
1. Postępuj zgodnie z instrukcjami kreatora, aby przełączyć grupę dostępności do trybu failover w replice pomocniczej. 

Tryb failover kończy się powodzeniem, gdy repliki przełączają role i są zsynchronizowane. 

---

## <a name="test-connectivity"></a>Testowanie łączności

Aby przetestować łączność, zaloguj się do innej maszyny wirtualnej w tej samej sieci wirtualnej. Otwórz **SQL Server Management Studio** i nawiąż połączenie z SQL Server FCI lub z odbiornikiem grupy dostępności.

>[!NOTE]
>Jeśli zachodzi taka potrzeba, można [pobrać SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).



## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat SQL Server funkcji HADR Cluster na platformie Azure, zobacz [grupy dostępności](availability-group-overview.md) i [wystąpienie klastra trybu failover](failover-cluster-instance-overview.md). Możesz również poznać [najlepsze rozwiązania](hadr-cluster-best-practices.md) dotyczące konfigurowania środowiska pod kątem wysokiej dostępności i odzyskiwania po awarii. 



