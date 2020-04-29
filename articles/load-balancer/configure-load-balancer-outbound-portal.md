---
title: Skonfiguruj Równoważenie obciążenia i reguły ruchu wychodzącego za pomocą Azure Portal
titleSuffix: Azure Load Balancer
description: W tym artykule pokazano, jak skonfigurować równoważenie obciążenia i reguły ruchu wychodzącego w usługa Load Balancer w warstwie Standardowa przy użyciu Azure Portal.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77590015"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Konfigurowanie równoważenia obciążenia i reguł ruchu wychodzącego w usługa Load Balancer w warstwie Standardowa przy użyciu Azure Portal

W tym artykule pokazano, jak skonfigurować reguły ruchu wychodzącego w usługa Load Balancer w warstwie Standardowa przy użyciu Azure Portal.  

Zasób modułu równoważenia obciążenia zawiera dwa Frontony i skojarzone z nimi reguły. Masz jeden fronton dla ruchu przychodzącego i innego frontonu dla ruchu wychodzącego.  

Każdy fronton odwołuje się do publicznego adresu IP. W tym scenariuszu publiczny adres IP dla ruchu przychodzącego różni się od adresu ruchu wychodzącego.   Reguła równoważenia obciążenia umożliwia tylko Równoważenie obciążenia przychodzącego. Reguła ruchu wychodzącego steruje wychodzącym translatorem adresów sieciowych (NAT) dla maszyny wirtualnej.  

W scenariuszu są stosowane dwie pule zaplecza: jeden dla ruchu przychodzącego i jeden dla ruchu wychodzącego. Te pule ilustrują możliwości i zapewniają elastyczność dla scenariusza.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji utworzysz moduł równoważenia obciążenia, który będzie równoważenia obciążenia maszyn wirtualnych. Można utworzyć publiczny moduł równoważenia obciążenia lub wewnętrzny moduł równoważenia obciążenia. Podczas tworzenia publicznego modułu równoważenia obciążenia tworzony jest nowy publiczny adres IP skonfigurowany jako fronton dla modułu równoważenia obciążenia. Domyślnie zostanie nadana nazwa **LoadBalancerFrontEnd** .

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób zasobów** > **Networking** > **Load Balancer**.
2. Na karcie **podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **Utwórz nowy** i wpisz **myResourceGroupSLB** w polu tekstowym.|
    | Nazwa                   | **myLoadBalancer**                                   |
    | Region         | Wybierz pozycję **Europa Zachodnia**.                                        |
    | Typ          | wybierz pozycję **Publiczny**.                                        |
    | SKU           | Wybierz opcję **standardowa** lub **podstawowa**. Firma Microsoft zaleca standardy dotyczące obciążeń produkcyjnych. |
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. Jeśli masz istniejący publiczny adres IP, którego chcesz użyć, wybierz pozycję **Użyj istniejącej**.  Istniejący publiczny adres IP musi być **standardową** jednostką SKU.  Podstawowe publiczne adresy IP nie są zgodne ze **standardowym** modułem równoważenia obciążenia jednostki SKU.  |
    | Nazwa publicznego adresu IP              | Wpisz **myPublicIP** w polu tekstowym.|
    | Strefa dostępności | Wybierz pozycję **strefa nadmiarowa** , aby utworzyć odporną Load Balancer. Aby utworzyć strefę Load Balancer, wybierz określoną strefę z 1, 2 lub 3 |

3. Zaakceptuj wartości domyślne dla pozostałej części konfiguracji.
4. Wybieranie opcji **Recenzja + tworzenie**

    > [!IMPORTANT]
    > W pozostałej części tego przewodnika Szybki Start przyjęto założenie, że w ramach procesu wyboru jednostki SKU zostanie wybrana **standardowa** jednostka SKU.

5. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.   

    ![Tworzenie usługi Load Balancer w warstwie Standardowa](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Tworzenie zasobów modułu równoważenia obciążenia

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji oraz określisz regułę modułu równoważenia obciążenia.

### <a name="create-a-backend-pool"></a>Tworzenie puli zaplecza

Pula adresów zaplecza zawiera adresy IP wirtualnych kart sieciowych w puli zaplecza. Utwórz pulę adresów zaplecza **myBackendPool** , aby uwzględnić maszyny wirtualne na potrzeby ruchu internetowego związanego z równoważeniem obciążenia.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.
2. W obszarze **Ustawienia**wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
3. Na stronie **Dodawanie puli zaplecza** wpisz nazwę **myBackendPool**, jako nazwę puli zaplecza, a następnie wybierz pozycję **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Sonda kondycji służy do monitorowania stanu aplikacji. Sonda kondycji dodaje lub usuwa maszyny wirtualne z modułu równoważenia obciążenia na podstawie odpowiedzi na testy kondycji. Utwórz sondę kondycji **myHealthProbe**, aby monitorować kondycję maszyn wirtualnych.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.
2. W obszarze **Ustawienia**wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **Dodaj**.
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHealthProbe**. |
    | Protocol (Protokół) | Wybierz pozycję **http**. |
    | Port | Wprowadź **80**.|
    | Interval | Wprowadź **15** dla liczby **interwałów** (w sekundach) między kolejnymi próbami sondowania. |
    | Próg złej kondycji | Wybierz **2** dla liczby **progów złej kondycji** lub kolejnych niepowodzeń sondy, które muszą wystąpić, zanim maszyna wirtualna zostanie uznana za złą.|
    | | |
4. Wybierz przycisk **OK**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia
Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. 

Zdefiniuj:
 - Konfiguracja adresu IP frontonu dla ruchu przychodzącego.
 - Pula adresów IP zaplecza do odbierania ruchu sieciowego.
 - Wymagany port źródłowy i docelowy. 

W poniższej sekcji utworzysz:
 - Reguła modułu równoważenia obciążenia **myHTTPRule** do nasłuchiwania na porcie 80.
 - **LoadBalancerFrontEnd**frontonu.
 - Pula adresów zaplecza **myBackEndPool** również przy użyciu portu 80. 

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.
2. W obszarze **Ustawienia**wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.
3. Użyj tych wartości, aby skonfigurować regułę równoważenia obciążenia:
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHTTPRule**. |
    | Protocol (Protokół) | wybierz pozycję **TCP**. |
    | Port | Wprowadź **80**.|
    | Port zaplecza | Wprowadź **80**. |
    | Pula zaplecza | Wybierz pozycję **myBackendPool**.|
    | Sonda kondycji | Wybierz pozycję **myHealthProbe**. |
    | Utwórz niejawne reguły wychodzące | Wybierz **nr**. Utworzymy reguły ruchu wychodzącego w dalszej części przy użyciu dedykowanego publicznego adresu IP. |
4. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **OK**.

## <a name="create-outbound-rule-configuration"></a>Utwórz konfigurację reguły ruchu wychodzącego
Reguły ruchu wychodzącego modułu równoważenia obciążenia Skonfiguruj wychodzące pliki zasad sieciowych dla maszyn wirtualnych w puli zaplecza. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Tworzenie wychodzącego publicznego adresu IP i frontonu

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia**wybierz pozycję **Konfiguracja adresu IP frontonu**, a następnie wybierz pozycję **Dodaj**.

3. Użyj tych wartości, aby skonfigurować konfigurację adresu IP frontonu dla ruchu wychodzącego:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **LoadBalancerFrontEndOutbound**. |
    | Wersja protokołu IP | Wybierz pozycję **IPv4**. |
    | Typ adresu IP | Wybierz pozycję **adres IP**.|
    | Publiczny adres IP | Wybierz pozycję**Utwórz nowy**. W polu **Dodaj publiczny adres IP**wprowadź **myPublicIPOutbound**.  Wybierz przycisk **OK**. |

4. Wybierz pozycję **Dodaj**.

### <a name="create-an-outbound-backend-pool"></a>Tworzenie puli zaplecza wychodzącego

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia**wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

3. Na stronie **Dodawanie puli zaplecza** wpisz nazwę **myBackendPoolOutbound**, jako nazwę puli zaplecza, a następnie wybierz pozycję **Dodaj**.

### <a name="create-outbound-rule"></a>Utwórz regułę ruchu wychodzącego

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia**wybierz pozycję **reguły ruchu wychodzącego**, a następnie wybierz pozycję **Dodaj**.

3. Użyj tych wartości, aby skonfigurować reguły ruchu wychodzącego:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myOutboundRule**. |
    | Adres IP frontonu | Wybierz pozycję **LoadBalancerFrontEndOutbound**. |
    | Limit czasu bezczynności (minuty) | Przesuń suwak do * * 15 minut.|
    | Resetowanie protokołu TCP | Wybierz pozycję **Włączone**.|
    | Pula zaplecza | Wybierz **myBackendPoolOutbound** |
    | Alokacja portu — alokacja portu > | Wybierz pozycję **ręcznie wybierz liczbę portów wychodzących** |
    | Porty wychodzące — > wybór | Wybierz **porty na wystąpienie** |
    | Porty wychodzące — porty > na wystąpienie | Wprowadź **10 000**. |

4. Wybierz pozycję **Dodaj**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Wybierz grupę zasobów **myResourceGroupSLB** , która zawiera moduł równoważenia obciążenia, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym artykule:
 - Utworzono standardowy moduł równoważenia obciążenia.
 - Skonfigurowano reguły ruchu przychodzącego i wychodzącego równoważenia obciążenia.
 - Skonfigurowano sondę kondycji dla maszyn wirtualnych w puli zaplecza. 

Aby dowiedzieć się więcej, przejdź do [samouczków dotyczących Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
