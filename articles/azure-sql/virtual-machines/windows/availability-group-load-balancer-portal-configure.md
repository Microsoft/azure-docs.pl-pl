---
title: Konfigurowanie odbiorników grup dostępności & module równoważenia obciążenia (Azure Portal)
description: Instrukcje krok po kroku dotyczące tworzenia odbiornika dla zawsze włączonych grup dostępności dla SQL Server na maszynach wirtualnych platformy Azure
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: b3f2e8b56af41d1729b9786adda3abdcc4eb0b02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325032"
---
# <a name="configure-a-load-balancer-for-a-sql-server-always-on-availability-group-in-azure-virtual-machines"></a>Konfigurowanie modułu równoważenia obciążenia dla SQL Server zawsze włączone grupy dostępności na platformie Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


W tym artykule wyjaśniono, jak utworzyć moduł równoważenia obciążenia dla SQL Server zawsze włączona Grupa dostępności na platformie Azure Virtual Machines z programem Azure Resource Manager. Grupa dostępności wymaga modułu równoważenia obciążenia, gdy wystąpienia SQL Server znajdują się na platformie Azure Virtual Machines. Moduł równoważenia obciążenia przechowuje adres IP dla odbiornika grupy dostępności. Jeśli grupa dostępności obejmuje wiele regionów, każdy region wymaga modułu równoważenia obciążenia.

Aby wykonać to zadanie, należy mieć zainstalowaną zawsze SQL Server grupę dostępności na maszynach wirtualnych platformy Azure z systemem Menedżer zasobów. Obie SQL Server maszyny wirtualne muszą należeć do tego samego zestawu dostępności. Możesz użyć [szablonu Microsoft](availability-group-azure-marketplace-template-configure.md) , aby automatycznie utworzyć grupę dostępności w Menedżer zasobów. Ten szablon automatycznie tworzy wewnętrzny moduł równoważenia obciążenia. 

Jeśli wolisz, możesz [ręcznie skonfigurować grupę dostępności](availability-group-manually-configure-tutorial.md).

Ten artykuł wymaga, aby grupy dostępności zostały już skonfigurowane.  

Wyświetl powiązane artykuły:

* [Konfigurowanie zawsze włączonych grup dostępności na maszynie wirtualnej platformy Azure (graficzny interfejs użytkownika)](availability-group-manually-configure-tutorial.md)   
* [Konfigurowanie połączenia sieci wirtualnych przy użyciu usługi Azure Resource Manager i programu PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Instruktaż tego artykułu, jak utworzyć i skonfigurować moduł równoważenia obciążenia w Azure Portal. Po zakończeniu procesu skonfiguruj klaster tak, aby używał adresu IP z modułu równoważenia obciążenia dla odbiornika grupy dostępności.

## <a name="create--configure-load-balancer"></a>Tworzenie & konfigurowania modułu równoważenia obciążenia 

W tej części zadania wykonaj następujące czynności:

1. W Azure Portal Utwórz moduł równoważenia obciążenia i skonfiguruj adres IP.
2. Skonfiguruj pulę zaplecza.
3. Utwórz sondę. 
4. Ustaw reguły równoważenia obciążenia.

> [!NOTE]
> Jeśli wystąpienia SQL Server znajdują się w wielu grupach zasobów i regionach, wykonaj każdy krok dwa razy, raz w każdej grupie zasobów.
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Krok 1. utworzenie modułu równoważenia obciążenia i skonfigurowanie adresu IP

Najpierw Utwórz moduł równoważenia obciążenia. 

1. W Azure Portal Otwórz grupę zasobów zawierającą SQL Server maszyn wirtualnych. 

2. W grupie zasobów wybierz pozycję **Dodaj**.

3. Wyszukaj **moduł równoważenia obciążenia**. Wybierz **Load Balancer** (opublikowane przez **firmę Microsoft**) w wynikach wyszukiwania.

4. W bloku **Load Balancer** wybierz pozycję **Utwórz**.

5. W oknie dialogowym **Tworzenie usługi równoważenia obciążenia** Skonfiguruj moduł równoważenia obciążenia w następujący sposób:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekstowa reprezentująca moduł równoważenia obciążenia. Na przykład **sqlLB**. |
   | **Typ** |**Wewnętrzne**: Większość implementacji korzysta z wewnętrznego modułu równoważenia obciążenia, który umożliwia aplikacjom w ramach tej samej sieci wirtualnej łączenie się z grupą dostępności.  </br> **Zewnętrzny**: umożliwia aplikacjom łączenie się z grupą dostępności za pomocą publicznego połączenia internetowego. |
   | **SKU** |**Podstawowa**: opcja domyślna. Prawidłowe tylko wtedy, gdy SQL Server wystąpienia znajdują się w tym samym zestawie dostępności. </br> **Standardowa**: preferowane. Prawidłowy, jeśli wystąpienia SQL Server znajdują się w tym samym zestawie dostępności. Wymagane, jeśli wystąpienia SQL Server znajdują się w różnych strefach dostępności. |
   | **Sieć wirtualna** |Wybierz sieć wirtualną, w której znajdują się wystąpienia SQL Server. |
   | **Podsieć** |Wybierz podsieć, w której znajdują się wystąpienia SQL Server. |
   | **Przypisanie adresu IP** |**Statyczny** |
   | **Prywatny adres IP** |Określ dostępny adres IP z podsieci. Użyj tego adresu IP podczas tworzenia odbiornika w klastrze. W skrypcie programu PowerShell w dalszej części tego artykułu Użyj tego adresu dla `$ILBIP` zmiennej. |
   | **Subskrypcja** |Jeśli masz wiele subskrypcji, to pole może się pojawić. Wybierz subskrypcję, którą chcesz skojarzyć z tym zasobem. Zwykle jest to taka sama subskrypcja jak wszystkie zasoby dla grupy dostępności. |
   | **Grupa zasobów** |Wybierz grupę zasobów, w której znajdują się wystąpienia SQL Server. |
   | **Lokalizacja** |Wybierz lokalizację platformy Azure, w której znajdują się wystąpienia SQL Server. |

6. Wybierz przycisk **Utwórz**. 

Platforma Azure tworzy moduł równoważenia obciążenia. Moduł równoważenia obciążenia należy do określonej sieci, podsieci, grupy zasobów i lokalizacji. Po zakończeniu zadania przez platformę Azure Sprawdź ustawienia modułu równoważenia obciążenia na platformie Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Krok 2. Konfigurowanie puli zaplecza

Platforma Azure wywołuje *pulę zaplecza*puli adresów zaplecza. W takim przypadku Pula zaplecza to adresy dwóch SQL Server wystąpień w grupie dostępności. 

1. W grupie zasobów wybierz utworzony moduł równoważenia obciążenia. 

2. W obszarze **Ustawienia**wybierz pozycję **Pule zaplecza**.

3. W obszarze **Pule zaplecza**wybierz pozycję **Dodaj** , aby utworzyć pulę adresów zaplecza. 

4. W obszarze **Dodaj pulę zaplecza**w polu **Nazwa**wpisz nazwę puli zaplecza.

5. W obszarze **maszyny wirtualne**wybierz pozycję **Dodaj maszynę wirtualną**. 

6. W obszarze **Wybieranie maszyn wirtualnych**wybierz pozycję **Wybierz zestaw dostępności**, a następnie określ zestaw dostępności, do którego należą maszyny wirtualne SQL Server.

7. Po wybraniu zestawu dostępności wybierz pozycję **Wybierz Maszyny wirtualne**, wybierz dwie maszyny wirtualne obsługujące wystąpienia SQL Server w grupie dostępności, a następnie wybierz **pozycję Wybierz**. 

8. Wybierz **przycisk OK** , aby zamknąć bloki dla **wybranych maszyn wirtualnych**i **dodać pulę zaplecza**. 

Platforma Azure aktualizuje ustawienia dla puli adresów zaplecza. Teraz zestaw dostępności ma pulę dwóch SQL Server wystąpień.

### <a name="step-3-create-a-probe"></a>Krok 3. Tworzenie sondy

Sonda definiuje, w jaki sposób platforma Azure weryfikuje, które wystąpienia SQL Server są obecnie własnością odbiornika grupy dostępności. Platforma Azure sonduje usługę na podstawie adresu IP na porcie zdefiniowanym podczas tworzenia sondy.

1. W bloku **Ustawienia** usługi równoważenia obciążenia wybierz pozycję **sondy kondycji**. 

2. W bloku **sondy kondycji** wybierz pozycję **Dodaj**.

3. Skonfiguruj sondę w bloku **Dodawanie sondy** . Użyj następujących wartości, aby skonfigurować sondę:

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekstowa reprezentująca sondę. Na przykład **SQLAlwaysOnEndPointProbe**. |
   | **Protokół** |**TCP** |
   | **Port** |Możesz użyć dowolnego dostępnego portu. Na przykład *59999*. |
   | **Interwał** |*5* |
   | **Próg złej kondycji** |*2* |

4.  Wybierz przycisk **OK**. 

> [!NOTE]
> Upewnij się, że określony port jest otwarty na zaporze obu wystąpień SQL Server. Oba wystąpienia wymagają reguły ruchu przychodzącego dla używanego portu TCP. Aby uzyskać więcej informacji, zobacz [Dodawanie lub Edytowanie reguły zapory](https://technet.microsoft.com/library/cc753558.aspx). 
> 

Platforma Azure tworzy sondę, a następnie używa jej do testowania, które wystąpienie SQL Server ma odbiornik dla grupy dostępności.

### <a name="step-4-set-the-load-balancing-rules"></a>Krok 4. Ustawianie zasad równoważenia obciążenia

Zasady równoważenia obciążenia umożliwiają skonfigurowanie sposobu, w jaki moduł równoważenia obciążenia kieruje ruch do wystąpień SQL Server. W przypadku tego modułu równoważenia obciążenia można włączyć bezpośredni zwrot serwera, ponieważ w danym momencie tylko jeden z dwóch wystąpień SQL Server jest właścicielem zasobu odbiornika grupy dostępności.

1. W bloku **Ustawienia** usługi równoważenia obciążenia wybierz pozycję **reguły równoważenia obciążenia**. 

2. W bloku **reguły równoważenia obciążenia** wybierz pozycję **Dodaj**.

3. W bloku **Dodaj reguły równoważenia obciążenia** Skonfiguruj regułę równoważenia obciążenia. Użyj następujących ustawień: 

   | Ustawienie | Wartość |
   | --- | --- |
   | **Nazwa** |Nazwa tekstowa reprezentująca reguły równoważenia obciążenia. Na przykład **SQLAlwaysOnEndPointListener**. |
   | **Protokół** |**TCP** |
   | **Port** |*1433* |
   | **Port zaplecza** |*1433*. Ta wartość jest ignorowana, ponieważ ta reguła używa **zmiennoprzecinkowego adresu IP (bezpośredni zwrot serwera)**. |
   | **Badane** |Użyj nazwy sondy utworzonej dla tego modułu równoważenia obciążenia. |
   | **Trwałość sesji** |**Brak** |
   | **Limit czasu bezczynności (minuty)** |*4* |
   | **Zmienny adres IP (bezpośredni zwrot serwera)** |**Włączone** |

   > [!NOTE]
   > Może być konieczne przewinięcie w dół bloku, aby wyświetlić wszystkie ustawienia.
   > 

4. Wybierz przycisk **OK**. 

5. Platforma Azure konfiguruje regułę równoważenia obciążenia. Teraz moduł równoważenia obciążenia jest skonfigurowany do kierowania ruchu do wystąpienia SQL Server, które hostuje odbiornik dla grupy dostępności. 

W tym momencie Grupa zasobów ma moduł równoważenia obciążenia łączący się z obydwoma maszynami SQL Server. Moduł równoważenia obciążenia zawiera również adres IP dla SQL Server zawsze włączony odbiornik grupy dostępności, dzięki czemu każdy komputer może odpowiedzieć na żądania dla grup dostępności.

> [!NOTE]
> Jeśli wystąpienia SQL Server znajdują się w dwóch oddzielnych regionach, powtórz kroki w drugim regionie. Każdy region wymaga modułu równoważenia obciążenia. 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Skonfiguruj klaster tak, aby używał adresu IP usługi równoważenia obciążenia

Następnym krokiem jest skonfigurowanie odbiornika w klastrze i przełączenie go w tryb online. Wykonaj następujące czynności: 

1. Utwórz odbiornik grupy dostępności w klastrze trybu failover. 

2. Przełączenie odbiornika w tryb online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Krok 5. Tworzenie odbiornika grupy dostępności w klastrze trybu failover

W tym kroku ręcznie utworzysz odbiornik grupy dostępności w Menedżer klastra trybu failover i SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Weryfikowanie konfiguracji odbiornika

Jeśli zasoby klastra i zależności są prawidłowo skonfigurowane, powinno być możliwe wyświetlenie odbiornika w SQL Server Management Studio. Aby ustawić port odbiornika, wykonaj następujące czynności:

1. Rozpocznij SQL Server Management Studio, a następnie połącz się z repliką podstawową.

2. Przejdź do obszaru odbiorniki grup dostępności funkcji **AlwaysOn o wysokiej dostępności**  >  **Availability Groups**  >  **Availability Group Listeners**.  

    Powinna zostać wyświetlona nazwa odbiornika utworzona w Menedżer klastra trybu failover. 

3. Kliknij prawym przyciskiem myszy nazwę odbiornika, a następnie wybierz polecenie **Właściwości**.

4. W polu **port** Określ numer portu dla odbiornika grupy dostępności przy użyciu użytej wcześniej $EndpointPort (1433 była domyślna), a następnie wybierz **przycisk OK**.

Teraz masz grupę dostępności w usłudze Azure Virtual Machines działającą w trybie Menedżer zasobów. 

## <a name="test-the-connection-to-the-listener"></a>Testowanie połączenia z odbiornikiem

Przetestuj połączenie, wykonując następujące czynności:

1. Użyj protokołu RDP (Remote Desktop Protocol), aby nawiązać połączenie z wystąpieniem SQL Server, które znajduje się w tej samej sieci wirtualnej, ale nie jest to replika. Ten serwer może być innym wystąpieniem SQL Server w klastrze.

2. Przetestuj połączenie przy użyciu narzędzia **sqlcmd** . Na przykład poniższy skrypt ustanawia połączenie **sqlcmd** z repliką podstawową za pomocą odbiornika z uwierzytelnianiem systemu Windows:

    ```console
    sqlcmd -S <listenerName> -E
    ```

Połączenie SQLCMD automatycznie łączy się z wystąpieniem SQL Server, które obsługuje replikę podstawową. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Tworzenie adresu IP dla dodatkowej grupy dostępności

Każda grupa dostępności używa osobnego odbiornika. Każdy odbiornik ma własny adres IP. Użyj tego samego modułu równoważenia obciążenia, aby pomieścić adres IP dla dodatkowych odbiorników. Po utworzeniu grupy dostępności Dodaj adres IP do modułu równoważenia obciążenia, a następnie skonfiguruj odbiornik.

Aby dodać adres IP do modułu równoważenia obciążenia z Azure Portal, wykonaj następujące czynności:

1. W Azure Portal Otwórz grupę zasobów zawierającą usługę równoważenia obciążenia, a następnie wybierz moduł równoważenia obciążenia. 

2. W obszarze **Ustawienia**wybierz pozycję **Pula adresów IP frontonu**, a następnie wybierz pozycję **Dodaj**. 

3. W obszarze **Dodaj adres IP frontonu**Przypisz nazwę frontonu. 

4. Sprawdź, czy **Sieć wirtualna** i **podsieć** są takie same jak wystąpienia SQL Server.

5. Ustaw adres IP dla odbiornika. 
   
   >[!TIP]
   >Można ustawić adres IP na statyczny i wpisać adres, który nie jest aktualnie używany w podsieci. Alternatywnie można ustawić adres IP na dynamiczny i zapisać nową pulę adresów IP frontonu. Gdy to zrobisz, Azure Portal automatycznie przypisze do puli dostępny adres IP. Następnie można ponownie otworzyć pulę adresów IP frontonu i zmienić przypisanie na static. 

6. Zapisz adres IP dla odbiornika. 

7. Dodaj sondę kondycji, używając następujących ustawień:

   |Ustawienie |Wartość
   |:-----|:----
   |**Nazwa** |Nazwa identyfikująca sondę.
   |**Protokół** |TCP
   |**Port** |Nieużywany port TCP, który musi być dostępny na wszystkich maszynach wirtualnych. Nie może być używany do żadnego innego celu. Dwa odbiorniki nie mogą korzystać z tego samego portu sondowania. 
   |**Interwał** |Czas między próbami sondy. Użyj domyślnego (5).
   |**Próg złej kondycji** |Liczba kolejnych progów, które powinny zakończyć się niepowodzeniem, zanim maszyna wirtualna zostanie uznana za złą.

8. Wybierz **przycisk OK** , aby zapisać sondę. 

9. Utwórz regułę równoważenia obciążenia. Wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.

10. Skonfiguruj nową regułę równoważenia obciążenia, używając następujących ustawień:

    |Ustawienie |Wartość
    |:-----|:----
    |**Nazwa** |Nazwa identyfikująca regułę równoważenia obciążenia. 
    |**Adres IP frontonu** |Wybierz utworzony adres IP. 
    |**Protokół** |TCP
    |**Port** |Użyj portu używanego przez wystąpienia SQL Server. Wystąpienie domyślne używa portu 1433, chyba że został zmieniony. 
    |**Port zaplecza** |Użyj tej samej wartości co **port**.
    |**Pula zaplecza** |Pula zawierająca maszyny wirtualne z wystąpieniami SQL Server. 
    |**Sonda kondycji** |Wybierz utworzoną sondę.
    |**Trwałość sesji** |Brak
    |**Limit czasu bezczynności (minuty)** |Wartość domyślna (4)
    |**Zmienny adres IP (bezpośredni zwrot serwera)** | Enabled (Włączony)

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Skonfiguruj grupę dostępności tak, aby korzystała z nowego adresu IP

Aby zakończyć konfigurowanie klastra, powtórz kroki, które zostały wykonane podczas pierwszej grupy dostępności. Oznacza to, że należy skonfigurować klaster tak, [aby korzystał z nowego adresu IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Po dodaniu adresu IP dla odbiornika Skonfiguruj dodatkową grupę dostępności, wykonując następujące czynności: 

1. Sprawdź, czy port sondy dla nowego adresu IP jest otwarty na obu SQL Server maszynach wirtualnych. 

2. [W Menedżerze klastra Dodaj punkt dostępu klienta](#addcap).

3. [Skonfiguruj zasób IP dla grupy dostępności](#congroup).

   >[!IMPORTANT]
   >Podczas tworzenia adresu IP należy użyć adresu IP dodanego do modułu równoważenia obciążenia.  

4. Włącz [zasób grupy dostępności SQL Server zależny od punktu dostępu klienta](#dependencyGroup).

5. [Udostępnij zasób punktu dostępu klienta zależnie od adresu IP](#listname).
 
6. [Ustaw parametry klastra w programie PowerShell](#setparam).

Po skonfigurowaniu grupy dostępności do korzystania z nowego adresu IP skonfiguruj połączenie z odbiornikiem. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Dodaj regułę równoważenia obciążenia dla rozproszonej grupy dostępności

Jeśli grupa dostępności uczestniczy w rozproszonej grupie dostępności, moduł równoważenia obciążenia musi mieć dodatkową regułę. Ta reguła przechowuje port używany przez odbiornik rozproszonej grupy dostępności.

>[!IMPORTANT]
>Ten krok ma zastosowanie tylko wtedy, gdy grupa dostępności uczestniczy w [rozproszonej grupie dostępności](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Na każdym serwerze, który uczestniczy w rozproszonej grupie dostępności, Utwórz regułę ruchu przychodzącego na porcie TCP odbiornika rozproszonej grupy dostępności. W wielu przykładach dokumentacja korzysta z 5022. 

1. W Azure Portal wybierz moduł równoważenia obciążenia i wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **+ Dodaj**. 

1. Utwórz regułę równoważenia obciążenia z następującymi ustawieniami:

   |Ustawienie |Wartość
   |:-----|:----
   |**Nazwa** |Nazwa identyfikująca regułę równoważenia obciążenia dla grupy dostępności rozproszonej. 
   |**Adres IP frontonu** |Użyj tego samego adresu IP frontonu co grupa dostępności.
   |**Protokół** |TCP
   |**Port** |5022 — port [odbiornika punktu końcowego grupy dostępności rozproszonej](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Może to być dowolny dostępny port.  
   |**Port zaplecza** | 5022 — Użyj tej samej wartości co **port**.
   |**Pula zaplecza** |Pula zawierająca maszyny wirtualne z wystąpieniami SQL Server. 
   |**Sonda kondycji** |Wybierz utworzoną sondę.
   |**Trwałość sesji** |Brak
   |**Limit czasu bezczynności (minuty)** |Wartość domyślna (4)
   |**Zmienny adres IP (bezpośredni zwrot serwera)** | Enabled (Włączony)

Powtórz te kroki dla modułu równoważenia obciążenia dla innych grup dostępności, które uczestniczą w grupach dostępności rozproszonej.

Jeśli masz grupę zabezpieczeń sieci platformy Azure, aby ograniczyć dostęp, upewnij się, że reguły zezwalania obejmują:
- Zaplecze SQL Server adresy IP maszyny wirtualnej
- Przestawne adresy IP modułu równoważenia obciążenia dla odbiornika AG
- Podstawowy adres IP klastra, jeśli ma zastosowanie.

## <a name="next-steps"></a>Następne kroki

- [SQL Server Skonfiguruj zawsze dostępną grupę dostępności na maszynach wirtualnych platformy Azure w różnych regionach](availability-group-manually-configure-multiple-regions.md)
