---
title: 'Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia — Azure Portal'
titleSuffix: Azure Load Balancer
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia wewnętrznego modułu równoważenia obciążenia przy użyciu Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a internal load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: de179ab2fe1c02b3912262ee57fbb41a23d56164
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715466"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu Azure Portal

Rozpocznij pracę z Azure Load Balancer przy użyciu Azure Portal, aby utworzyć wewnętrzny moduł równoważenia obciążenia i trzy maszyny wirtualne.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

---

# <a name="standard-sku"></a>[**Standardowy SKU**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych.  Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)**.

W tej sekcji utworzysz moduł równoważenia obciążenia, który równoważy obciążenie maszyn wirtualnych. 

Podczas tworzenia wewnętrznego modułu równoważenia obciążenia Sieć wirtualna jest konfigurowana jako sieć dla modułu równoważenia obciążenia. 

Na poniższym diagramie przedstawiono zasoby utworzone w ramach tego przewodnika Szybki Start:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Zasoby usługi równoważenia obciążenia w warstwie Standardowa utworzone w ramach szybkiego startu." border="false":::

Prywatny adres IP w sieci wirtualnej jest skonfigurowany jako fronton (domyślnie **LoadBalancerFrontend** ) dla usługi równoważenia obciążenia. 

Adres IP frontonu może być **statyczny** lub **dynamiczny**.

## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **CreateIntLBQS — RG** |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myVNet**                                    |
    | Region (Region)           | SELECT **(Europa) Europa Zachodnia** |

3. Wybierz kartę **adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

4. Na karcie **adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresowa IPv4 | Wprowadź **10.1.0.0/16** |

5. W obszarze **Nazwa podsieci** wybierz pozycję **domyślny** wyraz.

6. W obszarze **Edytuj podsieć** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wprowadź **myBackendSubnet** |
    | Zakres adresów podsieci | Wprowadź **10.1.0.0/24** |

7. Wybierz pozycję **Zapisz**.

8. Wybierz kartę **zabezpieczenia** .

9. W obszarze **BastionHost** wybierz pozycję **enable (Włącz**). Wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa bastionu | Wprowadź **myBastionHost** |
    | Przestrzeń adresowa AzureBastionSubnet | Wprowadź **10.1.1.0/24** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> W obszarze **Nazwa** wprowadź **myBastionIP**. </br> Wybierz przycisk **OK**. |


8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

9. Wybierz przycisk **Utwórz**.

## <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

1. Wybierz pozycję **Utwórz zasób**. 
2. W polu wyszukiwania wprowadź **moduł równoważenia obciążenia**. Wybierz pozycję **moduł równoważenia obciążenia** w wynikach wyszukiwania.
3. Na stronie **moduł równoważenia obciążenia** wybierz pozycję **Utwórz**.
4. Na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **CreateIntLBQS-RG** utworzone w poprzednim kroku.|
    | Nazwa                   | Wprowadź **myLoadBalancer**                                   |
    | Region (Region)         | Wybierz **(Europa) Europa Zachodnia**.                                        |
    | Typ          | wybierz pozycję **Wewnętrzny**.                                        |
    | SKU           | Wybierz pozycję **standardowa** |
    | Sieć wirtualna | Wybierz **myVNet** utworzone w poprzednim kroku. |
    | Podsieć  | Wybierz **myBackendSubnet** utworzone w poprzednim kroku. |
    | Przypisanie adresu IP | wybierz pozycję **Dynamiczne**. |
    | Strefa dostępności | Wybierz **strefę nadmiarową** |

3. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**.

4. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Tworzenie standardowego wewnętrznego modułu równoważenia obciążenia." border="true":::
 
## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji skonfigurujesz:

* Ustawienia usługi równoważenia obciążenia dla puli adresów zaplecza.
* Sonda kondycji.
* Reguła modułu równoważenia obciążenia.

### <a name="create-a-backend-pool"></a>Tworzenie puli zaplecza

Pula adresów zaplecza zawiera adresy IP wirtualnych (kart sieciowych) podłączonych do modułu równoważenia obciążenia. 

Utwórz pulę adresów zaplecza **myBackendPool** , aby uwzględnić maszyny wirtualne na potrzeby ruchu internetowego związanego z równoważeniem obciążenia.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

3. Na stronie **Dodawanie puli zaplecza** wpisz nazwę **myBackendPool**, jako nazwę puli zaplecza, a następnie wybierz pozycję **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Moduł równoważenia obciążenia monitoruje stan aplikacji przy użyciu sondy kondycji. 

Sonda kondycji dodaje lub usuwa maszyny wirtualne z modułu równoważenia obciążenia na podstawie odpowiedzi na testy kondycji. 

Utwórz sondę kondycji o nazwie **myHealthProbe**, aby monitorować kondycję maszyn wirtualnych.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHealthProbe**. |
    | Protokół | Wybierz pozycję **http**. |
    | Port | Wprowadź **80**.|
    | Interwał | Wprowadź **15** dla liczby **interwałów** (w sekundach) między kolejnymi próbami sondowania. |
    | Próg złej kondycji | Wybierz **2** dla liczby **progów złej kondycji** lub kolejnych niepowodzeń sondy, które muszą wystąpić, zanim maszyna wirtualna zostanie uznana za złą.|
    | | |

3. Pozostaw pozostałe wartości domyślne i wybierz **przycisk OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Należy zdefiniować konfigurację adresu IP frontonu dla ruchu przychodzącego i pulę adresów IP zaplecza do odbierania ruchu. Port źródłowy i docelowy są zdefiniowane w regule. 

W tej sekcji utworzysz regułę modułu równoważenia obciążenia:

* O nazwie **myHTTPRule**.
* Na frontonie o nazwie **LoadBalancerFrontEnd**.
* Nasłuchiwanie na **porcie 80**.
* Kieruje ruch o zrównoważonym obciążeniu do zaplecza o nazwie **myBackendPool** na **porcie 80**.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.

3. Użyj tych wartości, aby skonfigurować regułę równoważenia obciążenia:
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHTTPRule**. |
    | Wersja protokołu IP | Wybierz **protokół IPv4** |
    | Adres IP frontonu | Wybierz **LoadBalancerFrontEnd** |
    | Protokół | Wybierz pozycję **TCP**. |
    | Port | Wprowadź **80**.|
    | Port zaplecza | Wprowadź **80**. |
    | Pula zaplecza | Wybierz pozycję **myBackendPool**.|
    | Sonda kondycji | Wybierz pozycję **myHealthProbe**. |
    | Limit czasu bezczynności (minuty) | Przesuń suwak do **15** minut. |
    | Resetowanie protokołu TCP | Wybierz pozycję **Włączone**. |
    
4. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji omówiono następujące zagadnienia:

* Utwórz trzy maszyny wirtualne dla puli zaplecza modułu równoważenia obciążenia.
* Zainstaluj usługi IIS na maszynach wirtualnych w celu przetestowania modułu równoważenia obciążenia.

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tej sekcji utworzysz trzy maszyny wirtualne (**myVM1**, **myVM2** i **myVM3**).

Te maszyny wirtualne są dodawane do puli zaplecza modułu równoważenia obciążenia, który został utworzony wcześniej.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję **CreateIntLBQS — RG** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM1** |
    | Region (Region) | SELECT **(Europa) Europa Zachodnia** |
    | Opcje dostępności | Wybierz **strefy dostępności** |
    | Strefa dostępności | Wybierz **1** |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | **myVNet** |
    | Podsieć | **myBackendSubnet** |
    | Publiczny adres IP | Nie zaznaczaj **niczego** |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **Zaawansowane**|
    | Konfigurowanie sieciowej grupy zabezpieczeń | Wybierz pozycję **Utwórz nowy**. </br> W **grupie Tworzenie zabezpieczeń sieci** wprowadź **MyNSG** w polu **Nazwa**. </br> Wybierz przycisk **OK**. |
    | **Równoważenie obciążenia**  |
    | Umieścić tę maszynę wirtualną za istniejącym rozwiązaniem równoważenia obciążenia? | Wybierz pozycję **Tak** |
    | **Ustawienia równoważenia obciążenia** |
    | Opcje równoważenia obciążenia | Wybieranie **usługi Azure Load Balancing** |
    | Wybierz moduł równoważenia obciążenia | Wybierz **myLoadBalancer**  |
    | Wybierz pulę zaplecza | Wybierz **myBackendPool** |
   
5. Wybierz pozycję **Przejrzyj i utwórz**. 
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

7. Wykonaj kroki od 1 do 8, aby utworzyć dwie więcej maszyn wirtualnych o następujących wartościach, a wszystkie inne ustawienia takie same jak **myVM1**:

    | Ustawienie | MW 2 | MASZYNA WIRTUALNA 3 |
    | ------- | ----- | ---- |
    | Nazwa |  **myVM2** | **myVM3** |
    | Strefa dostępności | **2** | **3** |
    | Sieciowa grupa zabezpieczeń | Wybierz istniejący **myNSG**| Wybierz istniejący **myNSG** |


# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-1-create-internal-load-balancer-basic)

>[!NOTE]
>Moduł równoważenia obciążenia w warstwie Standardowa jest zalecany w przypadku obciążeń produkcyjnych.  Aby uzyskać więcej informacji o jednostkach SKU, zobacz **[Azure Load Balancer SKU](skus.md)**.

W tej sekcji utworzysz moduł równoważenia obciążenia, który równoważy obciążenie maszyn wirtualnych. 

Podczas tworzenia wewnętrznego modułu równoważenia obciążenia Sieć wirtualna jest konfigurowana jako sieć dla modułu równoważenia obciążenia. 

Na poniższym diagramie przedstawiono zasoby utworzone w ramach tego przewodnika Szybki Start:

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Zasoby podstawowego modułu równoważenia obciążenia utworzone w ramach szybkiego startu." border="false":::

Prywatny adres IP w sieci wirtualnej jest skonfigurowany jako fronton (domyślnie **LoadBalancerFrontend** ) dla usługi równoważenia obciążenia. 

Adres IP frontonu może być **statyczny** lub **dynamiczny**.

## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **CreateIntLBQS — RG** |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myVNet**                                    |
    | Region (Region)           | SELECT **(Europa) Europa Zachodnia** |

3. Wybierz kartę **adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

4. Na karcie **adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresowa IPv4 | Wprowadź **10.1.0.0/16** |

5. W obszarze **Nazwa podsieci** wybierz pozycję **domyślny** wyraz.

6. W obszarze **Edytuj podsieć** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wprowadź **myBackendSubnet** |
    | Zakres adresów podsieci | Wprowadź **10.1.0.0/24** |

7. Wybierz pozycję **Zapisz**.

8. Wybierz kartę **zabezpieczenia** .

9. W obszarze **BastionHost** wybierz pozycję **enable (Włącz**). Wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa bastionu | Wprowadź **myBastionHost** |
    | Przestrzeń adresowa AzureBastionSubnet | Wprowadź **10.1.1.0/24** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> W obszarze **Nazwa** wprowadź **myBastionIP**. </br> Wybierz przycisk **OK**. |


8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

9. Wybierz przycisk **Utwórz**.

## <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób zasobów**  >    >  **Load Balancer**.

2. Na karcie **podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **CreateIntLBQS-RG** utworzone w poprzednim kroku.|
    | Nazwa                   | Wprowadź **myLoadBalancer**                                   |
    | Region (Region)         | Wybierz **(Europa) Europa Zachodnia**.                                        |
    | Typ          | wybierz pozycję **Wewnętrzny**.                                        |
    | SKU           | Wybierz pozycję **podstawowa** |
    | Sieć wirtualna | Wybierz **myVNet** utworzone w poprzednim kroku. |
    | Podsieć  | Wybierz **myBackendSubnet** utworzone w poprzednim kroku. |
    | Przypisanie adresu IP | wybierz pozycję **Dynamiczne**. |

3. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**.

4. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Tworzenie podstawowego wewnętrznego modułu równoważenia obciążenia." border="true":::

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji skonfigurujesz:

* Ustawienia usługi równoważenia obciążenia dla puli adresów zaplecza.
* Sonda kondycji.
* Reguła modułu równoważenia obciążenia.

### <a name="create-a-backend-pool"></a>Tworzenie puli zaplecza

Pula adresów zaplecza zawiera adresy IP wirtualnych (kart sieciowych) podłączonych do modułu równoważenia obciążenia. 

Utwórz pulę adresów zaplecza **myBackendPool** , aby uwzględnić maszyny wirtualne na potrzeby ruchu internetowego związanego z równoważeniem obciążenia.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

3. Na stronie **Dodawanie puli zaplecza** wprowadź lub wybierz:
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myBackendPool**. |
    | Sieć wirtualna | Wybierz pozycję **myVNet**. |
    | Skojarzone z | Wybierz **maszyny wirtualne** |

4. Wybierz pozycję **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Moduł równoważenia obciążenia monitoruje stan aplikacji przy użyciu sondy kondycji. 

Sonda kondycji dodaje lub usuwa maszyny wirtualne z modułu równoważenia obciążenia na podstawie odpowiedzi na testy kondycji. 

Utwórz sondę kondycji o nazwie **myHealthProbe**, aby monitorować kondycję maszyn wirtualnych.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHealthProbe**. |
    | Protokół | Wybierz pozycję **http**. |
    | Port | Wprowadź **80**.|
    | Ścieżka | Wejść **/** |
    | Interwał | Wprowadź **15** dla liczby **interwałów** (w sekundach) między kolejnymi próbami sondowania. |
    | Próg złej kondycji | Wybierz **2** dla liczby **progów złej kondycji** lub kolejnych niepowodzeń sondy, które muszą wystąpić, zanim maszyna wirtualna zostanie uznana za złą.|

3. Wybierz przycisk **OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Należy zdefiniować konfigurację adresu IP frontonu dla ruchu przychodzącego i pulę adresów IP zaplecza do odbierania ruchu. Port źródłowy i docelowy są zdefiniowane w regule. 

W tej sekcji utworzysz regułę modułu równoważenia obciążenia:

* O nazwie **myHTTPRule**.
* Na frontonie o nazwie **LoadBalancerFrontEnd**.
* Nasłuchiwanie na **porcie 80**.
* Kieruje ruch o zrównoważonym obciążeniu do zaplecza o nazwie **myBackendPool** na **porcie 80**.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.

3. Użyj tych wartości, aby skonfigurować regułę równoważenia obciążenia:
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHTTPRule**. |
    | Wersja protokołu IP | Wybierz **protokół IPv4** |
    | Adres IP frontonu | Wybierz **LoadBalancerFrontEnd** |
    | Protokół | Wybierz pozycję **TCP**. |
    | Port | Wprowadź **80**.|
    | Port zaplecza | Wprowadź **80**. |
    | Pula zaplecza | Wybierz pozycję **myBackendPool**.|
    | Sonda kondycji | Wybierz pozycję **myHealthProbe**. |
    | Limit czasu bezczynności (minuty) | Przesuń suwak do **15** minut. |
 
4. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **OK**.

## <a name="create-backend-servers"></a>Tworzenie serwerów zaplecza

W tej sekcji omówiono następujące zagadnienia:

* Utwórz trzy maszyny wirtualne dla puli zaplecza modułu równoważenia obciążenia.
* Utwórz zestaw dostępności dla maszyn wirtualnych.
* Zainstaluj usługi IIS na maszynach wirtualnych w celu przetestowania modułu równoważenia obciążenia.

### <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tej sekcji utworzysz trzy maszyny wirtualne (**myVM1**, **myVM2**, **myVM3**).

Dwie maszyny wirtualne zostaną dodane do zestawu dostępności o nazwie **myAvailabilitySet**.

Te maszyny wirtualne są dodawane do puli zaplecza modułu równoważenia obciążenia, który został utworzony wcześniej.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję **CreateIntLBQS — RG** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM1** |
    | Region (Region) | Wybierz **Europa Zachodnia** |
    | Opcje dostępności | Wybierz pozycję **Zestaw dostępności** |
    | Zestaw dostępności | Wybierz pozycję **Utwórz nowy**. </br> Wprowadź **myAvailabilitySet** w polu **Nazwa**. </br> Wybierz przycisk **OK**. |
    | Image (Obraz) | **Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | Wybierz **myVNet** |
    | Podsieć | Wybierz **myBackendSubnet** |
    | Publiczny adres IP | Nie zaznaczaj **niczego** |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **Zaawansowane**|
    | Konfigurowanie sieciowej grupy zabezpieczeń | Wybierz pozycję **Utwórz nowy**. </br> W **grupie Tworzenie zabezpieczeń sieci** wprowadź **MyNSG** w polu **Nazwa**. </br> Wybierz przycisk **OK**. |
    | **Równoważenie obciążenia**  |
    | Umieścić tę maszynę wirtualną za istniejącym rozwiązaniem równoważenia obciążenia? | Wybierz pozycję **nie** |

5. Wybierz pozycję **Przejrzyj i utwórz**. 
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

7. Wykonaj kroki od 1 do 8, aby utworzyć dwie więcej maszyn wirtualnych o następujących wartościach, a wszystkie inne ustawienia takie same jak **myVM1**:

    | Ustawienie | MW 2 | MASZYNA WIRTUALNA 3 |
    | ------- | ----- | ---- |
    | Nazwa |  **myVM2** | **myVM3** |
    | Zestaw dostępności | Wybierz **myAvailabilitySet** | Wybierz **myAvailabilitySet** |
    | Sieciowa grupa zabezpieczeń | Wybierz istniejący **myNSG** | Wybierz istniejący **myNSG** |

### <a name="add-virtual-machines-to-the-backend-pool"></a>Dodawanie maszyn wirtualnych do puli zaplecza

Maszyny wirtualne utworzone w poprzednich krokach należy dodać do puli zaplecza **myLoadBalancer**.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **myBackendPool**.

3. Wybierz **maszyny wirtualne** **powiązane** z.

4. W sekcji **maszyny wirtualne** wybierz pozycję **+ Dodaj**.

5. Zaznacz pola obok pozycji **myVM1**, **myVM2** i **myVM3**.

6. Wybierz pozycję **Dodaj**.

7. Wybierz pozycję **Zapisz**.
---

## <a name="create-test-virtual-machine"></a>Utwórz testową maszynę wirtualną

W tej sekcji utworzysz maszynę wirtualną o nazwie **myTestVM**.  Ta maszyna wirtualna zostanie użyta do przetestowania konfiguracji modułu równoważenia obciążenia.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję **CreateIntLBQS — RG** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myTestVM** |
    | Region (Region) | Wybierz **Europa Zachodnia** |
    | Opcje dostępności | Nie wybieraj **nadmiarowości infrastruktury** |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | **myVNet** |
    | Podsieć | **myBackendSubnet** |
    | Publiczny adres IP | Wybierz pozycję **Brak**. |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **Zaawansowane**|
    | Konfigurowanie sieciowej grupy zabezpieczeń | Wybierz **MyNSG** utworzone w poprzednim kroku.|
       
5. Wybierz pozycję **Przejrzyj i utwórz**. 
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="install-iis"></a>Instalowanie usług IIS

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myVM1** , która znajduje się w grupie zasobów **CreateIntLBQS-RG** .

2. Na stronie **Przegląd** wybierz opcję **Połącz**, a następnie **bastionu**.

4. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

5. Wybierz pozycję **Połącz**.

6. Na pulpicie serwera przejdź do **narzędzi administracyjnych systemu Windows**  >  **programu Windows PowerShell**.

7. W oknie programu PowerShell uruchom następujące polecenia, aby:

    * Zainstaluj serwer IIS
    * Usuń domyślny plik iisstart.htm
    * Dodaj nowy plik iisstart.htm, który wyświetla nazwę maszyny wirtualnej:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Zamknij sesję bastionu z **myVM1**.

9. Powtórz kroki od 1 do 6, aby zainstalować usługi IIS i zaktualizowany plik iisstart.htm na maszynach **myVM2** i **myVM3**.


## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

1. Znajdź prywatny adres IP dla modułu równoważenia obciążenia na ekranie **Przegląd** . Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer**.

2. Zanotuj lub skopiuj adres obok **prywatnego adresu IP** w **omówieniu** **myLoadBalancer**.

3. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myTestVM** , która znajduje się w grupie zasobów **CreateIntLBQS-RG** .

4. Na stronie **Przegląd** wybierz opcję **Połącz**, a następnie **bastionu**.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Otwórz program **Internet Explorer** w systemie **myTestVM**.

8. Wprowadź adres IP z poprzedniego kroku na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Zrzut ekranu przedstawia okno przeglądarki zawierające stronę domyślną zgodnie z oczekiwaniami." border="true":::
   
Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch na obu maszynach wirtualnych, można dostosować domyślną stronę każdej maszyny wirtualnej sieci Web usług IIS, a następnie wymusić odświeżenie przeglądarki sieci Web z komputera klienckiego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. W tym celu wybierz grupę zasobów **CreateIntLBQS-RG** , która zawiera zasoby, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W ramach tego przewodnika Szybki start wykonasz następujące czynności:

* Tworzenie wewnętrznego modułu równoważenia obciążenia platformy Azure w warstwie Standardowa lub podstawowa
* Dołączono 3 maszyny wirtualne do modułu równoważenia obciążenia.
* Skonfigurowano regułę ruchu modułu równoważenia obciążenia, sondę kondycji, a następnie przetestowano moduł równoważenia obciążenia. 

Aby dowiedzieć się więcej na temat Azure Load Balancer, przejdź do:
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Load Balancer?](load-balancer-overview.md)