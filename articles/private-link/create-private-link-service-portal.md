---
title: Szybki Start — Tworzenie usługi linku prywatnego przy użyciu Azure Portal
titlesuffix: Azure Private Link
description: Dowiedz się, jak utworzyć usługę link prywatny przy użyciu Azure Portal w tym przewodniku Szybki Start
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: allensu
ms.openlocfilehash: d394a475c5121607f70c03437382e104a5d0cbee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746411"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Szybki Start: Tworzenie usługi linku prywatnego przy użyciu Azure Portal

Rozpocznij tworzenie usługi linku prywatnego, która odwołuje się do Twojej usługi.  Udziel prywatnego linku dostępu do usługi lub zasobu wdrożonego za usługa Load Balancer w warstwie Standardowa platformy Azure.  Użytkownicy usługi mają prywatny dostęp z sieci wirtualnej.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

W tej sekcji utworzysz sieć wirtualną i wewnętrzną Azure Load Balancer.

### <a name="virtual-network"></a>Sieć wirtualna

W tej sekcji utworzysz sieć wirtualną i podsieć służącą do hostowania modułu równoważenia obciążenia, który uzyskuje dostęp do usługi linków prywatnych.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **CreatePrivLinkService — RG** |
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
    | Nazwa podsieci | Wprowadź **maskę** |
    | Zakres adresów podsieci | Wprowadź **10.1.0.0/24** |

7. Wybierz pozycję **Zapisz**.

8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

9. Wybierz przycisk **Utwórz**.

### <a name="create-a-standard-load-balancer"></a>Tworzenie modułu równoważenia obciążenia w warstwie Standardowa

Użyj portalu do utworzenia standardowego wewnętrznego modułu równoważenia obciążenia. 

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób zasobów**  >    >  **Load Balancer**.

2. Na karcie **podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **CreatePrivLinkService-RG** utworzone w poprzednim kroku.|
    | Nazwa                   | Wprowadź **myLoadBalancer**                                   |
    | Region (Region)         | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).                                        |
    | Typ          | wybierz pozycję **Wewnętrzny**.                                        |
    | SKU           | Wybierz pozycję **standardowa** |
    | Sieć wirtualna | Wybierz **myVNet** utworzone w poprzednim kroku. |
    | Podsieć  | Wybierz pozycję Moja **podsieć** utworzona w poprzednim kroku. |
    | Przypisanie adresu IP | wybierz pozycję **Dynamiczne**. |
    | Strefa dostępności | Wybierz **strefę nadmiarową** |

3. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**.

4. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.   

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
    | Protokół | Wybierz pozycję **TCP**. |
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

## <a name="create-a-private-link-service"></a>Tworzenie usługi linku prywatnego

W tej sekcji utworzysz usługę linku prywatnego za pomocą usługi równoważenia obciążenia w warstwie Standardowa.

1. W lewym górnym rogu strony w Azure Portal wybierz pozycję **Utwórz zasób**.

2. Wyszukaj **link prywatny** w polu **Wyszukaj w portalu Marketplace** .

3. Wybierz przycisk **Utwórz**.

4. W obszarze **Omówienie** w obszarze **prywatne centrum linków** wybierz przycisk Blue **Create Private Link Service** .

5. Na karcie **podstawowe** w obszarze **Utwórz usługę link prywatny** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **CreatePrivLinkService-RG**. |
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź **myPrivateLinkService**. |
    | Region (Region) | Wybierz pozycję **East US 2** (Wschodnie stany USA 2). |

6. Wybierz kartę **Ustawienia wychodzące** lub wybierz pozycję **Dalej: ustawienia wychodzące** w dolnej części strony.

7. Na karcie **Ustawienia wychodzące** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Moduł równoważenia obciążenia | Wybierz pozycję **myLoadBalancer**. |
    | Adres IP frontonu modułu równoważenia obciążenia | Wybierz pozycję **LoadBalancerFrontEnd (10.1.0.4)**. |
    | Źródłowa podsieć NAT | Wybierz pozycję Moja **podsieć (10.1.0.0/24)**. |
    | Włącz serwer proxy TCP v2 | Pozostaw wartość domyślną **nie**. </br> Jeśli aplikacja oczekuje nagłówka serwera proxy TCP v2, wybierz pozycję **tak**. |
    | **Ustawienia prywatnego adresu IP** |  |
    | Pozostaw ustawienia domyślne |  |

8. Wybierz kartę **zabezpieczenia dostępu** lub wybierz pozycję **Dalej: zabezpieczenia dostępu** w dolnej części strony.

9. Domyślne ustawienie **kontroli dostępu opartej na rolach** należy pozostawić tylko na karcie **zabezpieczenia dostępu** .

10. Wybierz kartę **Tagi** lub wybierz pozycję **Dalej: Tagi** w dolnej części strony.

11. Wybierz kartę **Recenzja + tworzenie** lub wybierz pozycję **Dalej: przegląd + Utwórz** w dolnej części strony.

12. Wybierz pozycję **Utwórz** na karcie **Recenzja + tworzenie** .

Usługa link prywatny jest tworzona i może odbierać ruch. Jeśli chcesz zobaczyć przepływy ruchu, skonfiguruj aplikację za usługą równoważenia obciążenia w warstwie Standardowa.


## <a name="create-private-endpoint"></a>Utwórz prywatny punkt końcowy

W tej sekcji utworzysz mapowanie prywatnego usługi linku do prywatnego punktu końcowego. Sieć wirtualna zawiera prywatny punkt końcowy usługi link prywatny. Ta sieć wirtualna zawiera zasoby, które będą miały dostęp do prywatnej usługi link.

### <a name="create-private-endpoint-virtual-network"></a>Utwórz prywatną sieć wirtualną punktu końcowego

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **CreatePrivLinkService — RG** |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myVNetPE**                                    |
    | Region (Region)           | Wybierz **Wschodnie stany USA 2** |

3. Wybierz kartę **adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

4. Na karcie **adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresowa IPv4 | Wprowadź **11.1.0.0/16** |

5. W obszarze **Nazwa podsieci** wybierz pozycję **domyślny** wyraz.

6. W obszarze **Edytuj podsieć** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wprowadź **mySubnetPE** |
    | Zakres adresów podsieci | Wprowadź **11.1.0.0/24** |

7. Wybierz pozycję **Zapisz**.

8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

9. Wybierz przycisk **Utwórz**.

### <a name="create-private-endpoint"></a>Utwórz prywatny punkt końcowy

1. W lewym górnym rogu ekranu w portalu wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **prywatny link** lub w polu wyszukiwania wprowadź **łącze prywatne**.

2. Wybierz przycisk **Utwórz**.

3. W **prywatnym centrum połączenia** wybierz pozycję **prywatne punkty końcowe** w menu po lewej stronie.

4. W obszarze **prywatne punkty końcowe** wybierz pozycję **+ Dodaj**.

5. Na karcie **podstawy** **Utwórz prywatny punkt końcowy**, wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **CreatePrivLinkService-RG**. Ta grupa zasobów została utworzona w poprzedniej sekcji.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa  | Wprowadź **myPrivateEndpoint**. |
    | Region (Region) | Wybierz pozycję **East US 2** (Wschodnie stany USA 2). |

6. Wybierz kartę **zasób** lub przycisk **Dalej: zasób** w dolnej części strony.
    
7. W obszarze **zasób** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Metoda połączenia | Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu**. |
    | Subskrypcja | Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. Network/privateLinkServices**. |
    | Zasób | Wybierz pozycję **myPrivateLinkService**. |

8. Wybierz kartę **Konfiguracja** lub przycisk **Dalej: Konfiguracja** w dolnej części ekranu.

9. W obszarze **Konfiguracja** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Sieć** |  |
    | Virtual Network | Wybierz pozycję **myVNetPE**. |
    | Podsieć | Wybierz pozycję **mySubnetPE**. |

10. Wybierz kartę **Recenzja + tworzenie** lub przycisk **Przeglądaj + Utwórz** w dolnej części ekranu.

11. Wybierz przycisk **Utwórz**.

### <a name="ip-address-of-private-endpoint"></a>Adres IP prywatnego punktu końcowego

W tej sekcji znajdziesz adres IP prywatnego punktu końcowego, który odnosi się do modułu równoważenia obciążenia i usługi łącza prywatnego.

1. W kolumnie po lewej stronie Azure Portal wybierz pozycję **grupy zasobów**.

2. Wybierz grupę zasobów **CreatePrivLinkService-RG** .

3. W grupie zasobów **CreatePrivLinkService-RG** wybierz pozycję **myPrivateEndpoint**.

4. Na stronie **Przegląd** **myPrivateEndpoint** wybierz nazwę interfejsu sieciowego skojarzonego z prywatnym punktem końcowym.  Nazwa interfejsu sieciowego rozpoczyna się od **myPrivateEndpoint. nic**.

5. Na stronie **Przegląd** prywatnej karty sieciowej punktu końcowego adres IP punktu końcowego jest wyświetlany w polu **prywatny adres IP**.
    

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy skończysz korzystać z usługi link prywatny, Usuń grupę zasobów, aby wyczyścić zasoby używane w tym przewodniku Szybki Start.

1. Wprowadź ciąg **CreatePrivLinkService-RG** w polu wyszukiwania w górnej części portalu, a następnie wybierz pozycję **CreatePrivLinkService-RG** z wyników wyszukiwania.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. W polu **wpisz nazwę grupy zasobów** wpisz **CreatePrivLinkService-RG**.
1. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W ramach tego przewodnika Szybki start wykonasz następujące czynności:

* Utworzono sieć wirtualną i wewnętrzną Azure Load Balancer.
* Utworzono usługę linku prywatnego.
* Utworzono sieć wirtualną i prywatny punkt końcowy usługi link prywatny.

Aby dowiedzieć się więcej o prywatnym punkcie końcowym platformy Azure, przejdź do:
> [!div class="nextstepaction"]
> [Szybki Start: Tworzenie prywatnego punktu końcowego przy użyciu Azure Portal](create-private-endpoint-portal.md)