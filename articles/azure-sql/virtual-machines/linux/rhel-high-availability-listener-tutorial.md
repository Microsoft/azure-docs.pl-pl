---
title: Konfigurowanie odbiornika grupy dostępności na potrzeby SQL Server na maszynach wirtualnych RHEL na maszynach wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: Dowiedz się więcej na temat konfigurowania odbiornika grupy dostępności w SQL Server na maszynach wirtualnych RHEL na platformie Azure
ms.service: virtual-machines-sql
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 5f0b300be2f1cec4ee456065455832a2dc3598be
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449509"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Samouczek: Konfigurowanie odbiornika grupy dostępności dla SQL Server na maszynach wirtualnych RHEL na platformie Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> Przedstawiony samouczek jest w **publicznej wersji zapoznawczej**. 
>
> W tym samouczku używamy SQL Server 2017 z RHEL 7,6, ale można użyć SQL Server 2019 w RHEL 7 lub RHEL 8, aby skonfigurować wysoką dostępność. Polecenia służące do konfigurowania zasobów grupy dostępności zostały zmienione w RHEL 8 i chcesz zapoznać się z artykułem [Tworzenie zasobu grupy dostępności](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) i zasobów RHEL 8, aby uzyskać więcej informacji na temat prawidłowych poleceń.

Ten samouczek zawiera instrukcje dotyczące tworzenia odbiornika grup dostępności dla serwerów SQL na maszynach wirtualnych RHEL na platformie Azure. Poznasz następujące czynności:

> [!div class="checklist"]
> - Tworzenie modułu równoważenia obciążenia w Azure Portal
> - Konfigurowanie puli zaplecza dla modułu równoważenia obciążenia
> - Tworzenie sondy dla modułu równoważenia obciążenia
> - Ustawianie reguł równoważenia obciążenia
> - Tworzenie zasobu modułu równoważenia obciążenia w klastrze
> - Tworzenie odbiornika grupy dostępności
> - Testuj połączenie z odbiornikiem
> - Testowanie pracy w trybie failover

## <a name="prerequisite"></a>Wymaganie wstępne

Ukończony [Samouczek: Konfigurowanie grup dostępności dla SQL Server na maszynach wirtualnych RHEL na platformie Azure](rhel-high-availability-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Utwórz moduł równoważenia obciążenia w Azure Portal

Poniższe instrukcje przeprowadzi Cię przez kroki od 1 do 4 z tematu [Tworzenie i Konfigurowanie modułu równoważenia obciążenia w sekcji Azure Portal](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer) w artykule [Azure Portal modułu równoważenia obciążenia](../windows/availability-group-load-balancer-portal-configure.md) .

### <a name="create-the-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

1. W Azure Portal Otwórz grupę zasobów zawierającą SQL Server maszyn wirtualnych. 

2. W grupie zasobów kliknij przycisk **Dodaj**.

3. Wyszukaj **moduł równoważenia obciążenia** , a następnie w wynikach wyszukiwania wybierz pozycję **Load Balancer**, która jest publikowana przez **firmę Microsoft**.

4. W bloku **Load Balancer** kliknij pozycję **Utwórz**.

5. W oknie dialogowym **Tworzenie usługi równoważenia obciążenia** Skonfiguruj moduł równoważenia obciążenia w następujący sposób:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekstowa reprezentująca moduł równoważenia obciążenia. Na przykład **sqlLB**. |
   | **Typ** |**Wewnętrz** |
   | **Sieć wirtualna** |Domyślna Sieć wirtualna, która została utworzona, powinna mieć nazwę **VM1VNET**. |
   | **Podsieć** |Wybierz podsieć, w której znajdują się wystąpienia SQL Server. Wartość domyślna to **VM1Subnet**.|
   | **Przypisanie adresu IP** |**Statyczny** |
   | **Prywatny adres IP** |Użyj `virtualip` adresu IP, który został utworzony w klastrze. |
   | **Subskrypcja** |Użyj subskrypcji, która została użyta dla grupy zasobów. |
   | **Grupa zasobów** |Wybierz grupę zasobów, w której znajdują się wystąpienia SQL Server. |
   | **Lokalizacja** |Wybierz lokalizację platformy Azure, w której znajdują się wystąpienia SQL Server. |

### <a name="configure-the-back-end-pool"></a>Konfigurowanie puli zaplecza
Platforma Azure wywołuje *pulę zaplecza* puli adresów zaplecza. W takim przypadku Pula zaplecza jest adresami trzech SQL Server wystąpieniami w grupie dostępności. 

1. W grupie zasobów kliknij utworzony moduł równoważenia obciążenia. 

2. W obszarze **Ustawienia** kliknij pozycję **Pule zaplecza**.

3. W obszarze **Pule zaplecza** kliknij przycisk **Dodaj** , aby utworzyć pulę adresów zaplecza. 

4. W obszarze **Dodaj pulę zaplecza** w polu **Nazwa** wpisz nazwę puli zaplecza.

5. W obszarze **skojarzone z** wybierz pozycję **maszyna wirtualna**. 

6. Zaznacz każdą maszynę wirtualną w środowisku i Skojarz odpowiedni adres IP z każdym wyborem.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="Dodawanie puli zaplecza":::

7. Kliknij pozycję **Dodaj**. 

### <a name="create-a-probe"></a>Tworzenie sondy

Sonda definiuje, w jaki sposób platforma Azure weryfikuje, które wystąpienia SQL Server są obecnie własnością odbiornika grupy dostępności. Platforma Azure sonduje usługę na podstawie adresu IP na porcie zdefiniowanym podczas tworzenia sondy.

1. W bloku **Ustawienia** usługi równoważenia obciążenia kliknij pozycję **sondy kondycji**. 

2. W bloku **sondy kondycji** kliknij pozycję **Dodaj**.

3. Skonfiguruj sondę w bloku **Dodawanie sondy** . Użyj następujących wartości, aby skonfigurować sondę:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekstowa reprezentująca sondę. Na przykład **SQLAlwaysOnEndPointProbe**. |
   | **Protokół** |**TCP** |
   | **Port** |Możesz użyć dowolnego dostępnego portu. Na przykład *59999*. |
   | **Interwał** |*5* |
   | **Próg złej kondycji** |*2* |

4.  Kliknij przycisk **OK**. 

5. Zaloguj się do wszystkich maszyn wirtualnych i Otwórz port sondowania przy użyciu następujących poleceń:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Platforma Azure tworzy sondę, a następnie używa jej do testowania, które wystąpienie SQL Server ma odbiornik dla grupy dostępności.

### <a name="set-the-load-balancing-rules"></a>Ustawianie reguł równoważenia obciążenia

Zasady równoważenia obciążenia umożliwiają skonfigurowanie sposobu, w jaki moduł równoważenia obciążenia kieruje ruch do wystąpień SQL Server. W przypadku tego modułu równoważenia obciążenia można włączyć bezpośredni zwrot serwera, ponieważ w danym momencie tylko jeden z trzech wystąpień SQL Server jest właścicielem zasobu odbiornika grupy dostępności.

1. W bloku **Ustawienia** usługi równoważenia obciążenia kliknij pozycję **reguły równoważenia obciążenia**. 

2. W bloku **reguły równoważenia obciążenia** kliknij pozycję **Dodaj**.

3. W bloku **Dodaj reguły równoważenia obciążenia** Skonfiguruj regułę równoważenia obciążenia. Użyj następujących ustawień: 

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekstowa reprezentująca reguły równoważenia obciążenia. Na przykład **SQLAlwaysOnEndPointListener**. |
   | **Protokół** |**TCP** |
   | **Port** |*1433* |
   | **Port zaplecza** |*1433*. Ta wartość jest ignorowana, ponieważ ta reguła używa **zmiennoprzecinkowego adresu IP (bezpośredni zwrot serwera)**. |
   | **Sonda** |Użyj nazwy sondy utworzonej dla tego modułu równoważenia obciążenia. |
   | **Trwałość sesji** |**Brak** |
   | **Limit czasu bezczynności (minuty)** |*4* |
   | **Zmienny adres IP (bezpośredni zwrot serwera)** |**Włączono** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="Dodaj regułę równoważenia obciążenia":::

4. Kliknij przycisk **OK**. 
5. Platforma Azure konfiguruje regułę równoważenia obciążenia. Teraz moduł równoważenia obciążenia jest skonfigurowany do kierowania ruchu do wystąpienia SQL Server, które hostuje odbiornik dla grupy dostępności. 

W tym momencie Grupa zasobów ma moduł równoważenia obciążenia, który łączy się ze wszystkimi maszynami SQL Server. Moduł równoważenia obciążenia zawiera również adres IP dla SQL Server zawsze włączony odbiornik grupy dostępności, dzięki czemu każda maszyna może odpowiadać na żądania dotyczące grup dostępności.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Tworzenie zasobu modułu równoważenia obciążenia w klastrze

1. Zaloguj się do podstawowej maszyny wirtualnej. Musimy utworzyć zasób, aby włączyć port sondy modułu równoważenia obciążenia platformy Azure (59999 w naszym przykładzie). Uruchom następujące polecenie:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Utwórz grupę zawierającą `virtualip` `azure_load_balancer` zasób i:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Dodaj ograniczenia

1. Należy skonfigurować ograniczenie wspólnej lokalizacji, aby zapewnić, że adres IP modułu równoważenia obciążenia platformy Azure i zasób AG są uruchomione w tym samym węźle. Uruchom następujące polecenie:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Utwórz ograniczenie porządkowania, aby upewnić się, że w ramach usługi Azure Load jest uruchomione i działające przed adresem IP modułu równoważenia obciążenia. Chociaż ograniczenie wspólnej lokalizacji implikuje ograniczenie kolejności, wymusza je.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Aby sprawdzić ograniczenia, uruchom następujące polecenie:

    ```bash
    sudo pcs constraint list --full
    ```

    Powinny zostać wyświetlone następujące dane wyjściowe:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>Tworzenie odbiornika grupy dostępności

1. W węźle podstawowym Uruchom następujące polecenie w SQLCMD lub SSMS:

    - Zastąp adres IP używany poniżej z `virtualip` adresem IP.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Zaloguj się do każdego węzła maszyny wirtualnej. Użyj poniższego polecenia, aby otworzyć plik Hosts i skonfigurować rozpoznawanie nazw hostów dla `ag1-listener` każdego komputera.

    ```
    sudo vi /etc/hosts
    ```

    W edytorze **VI** wpisz, `i` Aby wstawić tekst, a w pustym wierszu Dodaj adres IP `ag1-listener` . Następnie Dodaj `ag1-listener` po odstępie obok adresu IP.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Aby wyjść z edytora **VI** , najpierw naciśnij klawisz **ESC** , a następnie wprowadź polecenie, `:wq` Aby napisać plik i zakończyć pracę. Zrób to w każdym węźle.

## <a name="test-the-listener-and-a-failover"></a>Testowanie odbiornika i trybu failover

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>Testowanie logowania do SQL Server przy użyciu odbiornika grupy dostępności

1. Użyj polecenia SQLCMD, aby zalogować się do węzła podstawowego SQL Server przy użyciu nazwy odbiornika grupy dostępności:

    - Użyj nazwy logowania, która została wcześniej utworzona i Zastąp ciąg `<YourPassword>` prawidłowym hasłem. W poniższym przykładzie użyto `sa` nazwy logowania, która została utworzona przy użyciu SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Sprawdź nazwę serwera, z którym nawiązano połączenie. Uruchom następujące polecenie w SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Dane wyjściowe powinny wyświetlać bieżący węzeł podstawowy. Należy to zrobić `VM1` , jeśli nigdy nie przetestowano trybu failover.

    Zakończ sesję SQL Server, wpisując `exit` polecenie.

### <a name="test-a-failover"></a>Testowanie trybu failover

1. Uruchom następujące polecenie, aby ręcznie przełączyć replikę podstawową na `<VM2>` lub inną replikę. Zamień `<VM2>` na wartość nazwy serwera.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Jeśli sprawdzisz ograniczenia, zobaczysz, że zostało dodane inne ograniczenie ze względu na ręczną pracę awaryjną:

    ```bash
    sudo pcs constraint list --full
    ```

    Zobaczysz, że zostało dodane ograniczenie o IDENTYFIKATORze `cli-prefer-ag_cluster-master` .

1. Usuń ograniczenie o IDENTYFIKATORze `cli-prefer-ag_cluster-master` przy użyciu następującego polecenia:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Sprawdź zasoby klastra przy użyciu polecenia `sudo pcs resource` , aby zobaczyć, że wystąpienie podstawowe jest teraz `<VM2>` .

    > [!NOTE]
    > Ten artykuł zawiera odwołania do warunku podrzędnego, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.


    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Użyj polecenia SQLCMD, aby zalogować się do repliki podstawowej przy użyciu nazwy odbiornika:

    - Użyj nazwy logowania, która została wcześniej utworzona i Zastąp ciąg `<YourPassword>` prawidłowym hasłem. W poniższym przykładzie użyto `sa` nazwy logowania, która została utworzona przy użyciu SQL Server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. Sprawdź serwer, z którym nawiązano połączenie. Uruchom następujące polecenie w SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Powinieneś zobaczyć, że masz połączenie z maszyną wirtualną, która została przełączona w tryb failover.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułów równoważenia obciążenia na platformie Azure, zobacz:

> [!div class="nextstepaction"]
> [Konfigurowanie równoważenia obciążenia dla grupy dostępności na SQL Server na maszynach wirtualnych platformy Azure](../windows/availability-group-load-balancer-portal-configure.md)
