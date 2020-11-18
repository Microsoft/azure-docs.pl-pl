---
title: Konfiguracja modułu równoważenia obciążenia tylko dla ruchu wychodzącego
titleSuffix: Azure Load Balancer
description: W tym artykule dowiesz się, jak utworzyć wewnętrzny moduł równoważenia obciążenia przy użyciu protokołu NAT dla ruchu wychodzącego
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: ee264a22de5ce094e8a4c1335ace77cbbba49270
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694997"
---
# <a name="outbound-only-load-balancer-configuration"></a>Konfiguracja modułu równoważenia obciążenia tylko dla ruchu wychodzącego

Użyj kombinacji wewnętrznych i zewnętrznych usług równoważenia obciążenia, aby utworzyć łączność wychodzącą dla maszyn wirtualnych za wewnętrznym modułem równoważenia obciążenia. 

Ta konfiguracja zapewnia wychodzące NAT dla scenariusza wewnętrznego modułu równoważenia obciążenia, co generuje konfigurację "tylko wyjście danych" dla puli zaplecza.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="Virtual Network translator adresów sieciowych">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*Ilustracja: Konfiguracja usługi równoważenia obciążenia tylko dla ruchu wychodzącego*

Wymagane są następujące kroki:

1. Utwórz sieć wirtualną z hostem bastionu.
2. Utwórz maszynę wirtualną z tylko prywatnym adresem IP.
3. Utwórz wewnętrzne i publiczne usługi równoważenia obciążenia w warstwie Standardowa.
4. Dodaj pule zaplecza do obu modułów równoważenia obciążenia i umieść maszynę wirtualną w każdej puli.
5. Połącz się z maszyną wirtualną za pomocą hosta bastionu i:
    1. Testowanie łączności wychodzącej, 
    2. Skonfiguruj regułę ruchu wychodzącego w publicznym module równoważenia obciążenia.
    3. Przetestowanie łączności wychodzącej.

## <a name="create-virtual-network-and-virtual-machine"></a>Tworzenie sieci wirtualnej i maszyny wirtualnej

Utwórz sieć wirtualną z dwiema podsieciami:

* Podsieć podstawowa
* Podsieć bastionu

Utwórz maszynę wirtualną w nowej sieci wirtualnej.

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **Utwórz nowy**. </br> Wprowadź **myResourceGroupLB**. </br> Wybierz przycisk **OK**. |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myVNet**                                    |
    | Region (Region)           | Wybierz **Wschodnie stany USA 2** |

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

9. Wybierz pozycję **Utwórz**.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz **myResourceGroupLB** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM** |
    | Region (Region) | Wybierz **Wschodnie stany USA 2** |
    | Opcje dostępności | Nie wybieraj **nadmiarowości infrastruktury** |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |
    | **Reguły portów ruchu przychodzącego** |  |
    | Publiczne porty wejściowe | Wybierz opcję **Zezwalaj na wybrane porty** |
    | Wybierz porty wejściowe | Wybierz **protokół RDP (3389)** |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-----|------------|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | **myVNet** |
    | Podsieć | **myBackendSubnet** |
    | Publiczny adres IP | Wybierz pozycję **Brak**. |
    | Grupa zabezpieczeń sieci karty sieciowej | Nie zaznaczaj **niczego**|
    | Umieścić tę maszynę wirtualną za istniejącym rozwiązaniem równoważenia obciążenia? | Wybierz pozycję **nie** |
   
5. Wybierz kartę **Zarządzanie** lub wybierz pozycję **Dalej** > **Zarządzanie**.

6. Na karcie **Zarządzanie** wybierz lub wprowadź:
    
    | Ustawienie | Wartość |
    |-|-|
    | **Monitorowanie** |  |
    | Diagnostyka rozruchu | Wybierz pozycję **wyłączone** |
   
7. Wybierz pozycję **Przejrzyj i utwórz**. 
  
8. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="create-load-balancers-and-test-connectivity"></a>Tworzenie modułów równoważenia obciążenia i Testowanie łączności

Użyj Azure Portal, aby utworzyć:

* Wewnętrzny moduł równoważenia obciążenia
* Publiczny moduł równoważenia obciążenia
 
Dodaj utworzoną maszynę wirtualną do puli zaplecza każdego z nich.  Następnie skonfigurujesz konfigurację tak, aby zezwalała na połączenia wychodzące z maszyny wirtualnej, testowanie przed i po.

### <a name="create-internal-load-balancer"></a>Tworzenie wewnętrznego modułu równoważenia obciążenia

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób zasobów**  >  **Networking**  >  **Load Balancer**.

2. Na karcie **podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz **myResourceGroupLB** utworzone w poprzednim kroku.|
    | Nazwa                   | Wprowadź **myInternalLoadBalancer**                                   |
    | Region (Region)         | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).                                        |
    | Typ          | wybierz pozycję **Wewnętrzny**.                                        |
    | SKU           | Wybierz pozycję **standardowa** |
    | Sieć wirtualna | Wybierz **myVNet** utworzone w poprzednim kroku. |
    | Podsieć  | Wybierz **myBackendSubnet** utworzone w poprzednim kroku. |
    | Przypisanie adresu IP | wybierz pozycję **Dynamiczne**. |

3. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**.

4. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.   

### <a name="create-public-load-balancer"></a>Tworzenie publicznego modułu równoważenia obciążenia

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób zasobów**  >  **Networking**  >  **Load Balancer**.

2. Na karcie **podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **Utwórz nowy** i wprowadź **myResourceGroupLB** w polu tekstowym.|
    | Nazwa                   | Wprowadź **myPublicLoadBalancer**                                   |
    | Region (Region)         | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).                                        |
    | Typ          | Wybierz pozycję **Publiczna**.                                        |
    | SKU           | Wybierz pozycję **standardowa** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. |
    | Nazwa publicznego adresu IP | Wprowadź **myFrontendIP** w polu tekstowym.|
    | Strefa dostępności | Wybierz **strefę nadmiarową** |
    | Dodaj publiczny adres IPv6 | Wybierz pozycję **Nie**. |

3. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**.

4. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.   
   
### <a name="create-internal-backend-address-pool"></a>Tworzenie wewnętrznej puli adresów wewnętrznej bazy danych

Utwórz pulę adresów zaplecza **myInternalBackendPool**:

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myInternalLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

3. Na stronie **Dodawanie puli zaplecza** wpisz nazwę **myInternalBackendPool** jako nazwę puli zaplecza.
 
4. W obszarze **Sieć wirtualna** wybierz pozycję **myVNet**.

5. W obszarze **maszyny wirtualne** wybierz pozycję **Dodaj** i wybierz pozycję **myVM**.
 
6. Wybierz pozycję **Dodaj**.

### <a name="create-public-backend-address-pool"></a>Utwórz pulę adresów publicznej bazy danych

Utwórz pulę adresów zaplecza **myPublicBackendPool**:

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myPublicLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

3. Na stronie **Dodawanie puli zaplecza** wpisz nazwę **myPublicBackendPool** jako nazwę puli zaplecza.

4. W obszarze **Sieć wirtualna** wybierz pozycję **myVNet**.
 
5. W obszarze **maszyny wirtualne** wybierz pozycję **Dodaj** i wybierz pozycję **myVM**.
 
6. Wybierz pozycję **Dodaj**.

### <a name="test-connectivity-before-outbound-rule"></a>Testuj łączność przed regułą ruchu wychodzącego

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myVM** , która znajduje się w grupie zasobów **myResourceGroupLB** .

2. Na stronie **Przegląd** wybierz opcję **Połącz**, a następnie **bastionu**.

4. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

5. Wybierz pozycję **Połącz**.

6. Otwórz program Internet Explorer.

7. Wprowadź **https://whatsmyip.org** na pasku adresu.

8. Połączenie powinno zakończyć się niepowodzeniem. Domyślnie, publiczny moduł równoważenia obciążenia [nie zezwala na ruch wychodzący bez zdefiniowanej reguły wychodzącej](load-balancer-overview.md#securebydefault).
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>Tworzenie reguły ruchu wychodzącego publicznego modułu równoważenia obciążenia

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myPublicLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **reguły ruchu wychodzącego**, a następnie wybierz pozycję **Dodaj**.

3. Użyj tych wartości, aby skonfigurować reguły ruchu wychodzącego:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myOutboundRule**. |
    | Adres IP frontonu | Wybierz pozycję **LoadBalancerFrontEnd**.|
    | Limit czasu bezczynności (minuty) | Przesuń suwak do **15 minut**.|
    | Resetowanie protokołu TCP | Wybierz pozycję **Włączone**.|
    | Pula zaplecza | Wybierz pozycję **myPublicBackendPool**.| |
    | Alokacja portu — alokacja portu > | Wybierz opcję **Użyj domyślnej liczby portów wychodzących** |

4. Wybierz pozycję **Dodaj**.

### <a name="test-connectivity-after-outbound-rule"></a>Testuj łączność po wyjściu z reguły

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myVM** , która znajduje się w grupie zasobów **myResourceGroupLB** .

2. Na stronie **Przegląd** wybierz opcję **Połącz**, a następnie **bastionu**.

4. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

5. Wybierz pozycję **Połącz**.

6. Otwórz program Internet Explorer.

7. Wprowadź **https://whatsmyip.org** na pasku adresu.

8. Połączenie powinno zakończyć się pomyślnie.

9. Wyświetlany adres IP powinien być adresem IP frontonu **myPublicLoadBalancer**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, moduły równoważenia obciążenia, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. 

Aby to zrobić, wybierz grupę zasobów **myResourceGroupLB** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono konfigurację "tylko ruch wychodzący" z połączeniem usług równoważenia obciążenia publicznego i wewnętrznego.  

Ta konfiguracja umożliwia Równoważenie obciążenia przychodzącego ruchu wewnętrznego do puli zaplecza, przy jednoczesnym zachowaniu wszelkich publicznych połączeń przychodzących.

- Dowiedz się więcej na temat [Azure Load Balancer](load-balancer-overview.md).
- Dowiedz się więcej o [połączeniach wychodzących na platformie Azure](load-balancer-outbound-connections.md).
- [Często zadawane pytania](load-balancer-faqs.md)dotyczące usługi równoważenia obciążenia.
- Dowiedz się więcej o [usłudze Azure bastionu](../bastion/bastion-overview.md)