---
title: Szybki Start — Tworzenie usługi linku prywatnego przy użyciu Azure Portal
titlesuffix: Azure Private Link
description: Dowiedz się, jak utworzyć usługę link prywatny przy użyciu Azure Portal w tym przewodniku Szybki Start
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 5b7bc8be89068f0d3cf6722c36ae7fd5cc560736
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012122"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Szybki Start: Tworzenie usługi linku prywatnego przy użyciu Azure Portal

Usługa łącza prywatnego platformy Azure odnosi się do własnej usługi, która jest zarządzana przez link prywatny. Możesz dać prywatny link do usługi lub zasobu, który działa za usługa Load Balancer w warstwie Standardowa platformy Azure. Konsumenci Twojej usługi mogą uzyskać do nich dostęp prywatnie z własnych sieci wirtualnych. W tym przewodniku szybki start dowiesz się, jak utworzyć usługę linku prywatnego przy użyciu Azure Portal.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-an-internal-load-balancer"></a>Utwórz wewnętrzny moduł równoważenia obciążenia.

Najpierw Utwórz sieć wirtualną. Następnie utwórz wewnętrzny moduł równoważenia obciążenia, który będzie używany z usługą link prywatny.

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji utworzysz sieć wirtualną. Należy również utworzyć podsieć, która będzie hostować moduł równoważenia obciążenia, który uzyskuje dostęp do usługi linków prywatnych.

W tej sekcji należy zamienić następujące parametry w krokach z poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Wschodnie stany USA 2      |
| **\<IPv4-address-space>**   | 10.3.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Tworzenie modułu równoważenia obciążenia w warstwie Standardowa

Użyj portalu do utworzenia standardowego wewnętrznego modułu równoważenia obciążenia. Określona nazwa i adres IP są automatycznie konfigurowane jako fronton modułu równoważenia obciążenia.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób zasobów**  >  **Networking**  >  **Load Balancer**.

1. Na karcie **podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | **Subskrypcja**               | Wybierz subskrypcję.    |
    | **Grupa zasobów**         | W polu Wybierz pozycję **myResourceGroupLB** .|
    | **Nazwa**                   | Wprowadź **myLoadBalancer**.                                   |
    | **Region**         | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).                                        |
    | **Typ**          | wybierz pozycję **Wewnętrzny**.                                        |
    | **SKU**           | Wybierz pozycję **Standardowy**.                          |
    | **Sieć wirtualna**           | Wybierz pozycję **myVNet**.                          |
    | **Przypisanie adresu IP**              | Wybierz wartość **Statyczny**.   |
    | **Prywatny adres IP**|Wprowadź adres znajdujący się w przestrzeni adresowej sieci wirtualnej i podsieci. Przykładem jest 10.3.0.7.  |

1. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz** .

1. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.

### <a name="create-standard-load-balancer-resources"></a>Tworzenie zasobów usługi równoważenia obciążenia w warstwie Standardowa

W tej sekcji skonfigurujesz ustawienia modułu równoważenia obciążenia dla puli adresów zaplecza i sondy kondycji. Należy również określić reguły usługi równoważenia obciążenia.

#### <a name="create-a-back-end-pool"></a>Tworzenie puli zaplecza

Pula adresów zaplecza zawiera adresy IP wirtualnych kart sieciowych podłączonych do modułu równoważenia obciążenia. Ta Pula umożliwia dystrybucję ruchu do zasobów. Utwórz pulę adresów zaplecza o nazwie **myBackendPool** , aby uwzględnić zasoby, które równoważą obciążenie ruchem.

1. Wybierz pozycję **wszystkie usługi** z menu z lewej strony.
1. Wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.
1. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.
1. Na stronie **Dodawanie puli zaplecza** wprowadź **myBackendPool** jako nazwę puli zaplecza, a następnie wybierz pozycję **Dodaj**.

#### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Użyj sondy kondycji, aby umożliwić usłudze równoważenia obciążenia monitorowanie stanu zasobów. W oparciu o odpowiedź zasobów na testy kondycji sonda kondycji dynamicznie dodaje lub usuwa zasoby z rotacji modułu równoważenia obciążenia.

Aby utworzyć sondę kondycji do monitorowania kondycji zasobów:

1. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **myLoadBalancer** z listy zasobów.

1. W obszarze **Ustawienia** wybierz pozycję **Sondy kondycji**, a następnie wybierz pozycję **Dodaj**.

1. Na stronie **Dodawanie sondy kondycji** wprowadź lub wybierz następujące wartości:

   - **Nazwa**: wprowadź **myHealthProbe**.
   - **Protokół**: wybierz pozycję **TCP**.
   - **Port**: wprowadź **80**.
   - **Interwał**: wprowadź **15**. Ta wartość to liczba sekund między próbami sondy.
   - **Próg złej kondycji**: wprowadź wartość **2**. Ta wartość jest liczbą kolejnych niepowodzeń sondy, które wystąpiły, zanim maszyna wirtualna zostanie uznana za złą.

1. Wybierz pozycję **OK**.

#### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia definiuje sposób dystrybucji ruchu do zasobów. Zasada definiuje:

- Konfiguracja adresu IP frontonu dla ruchu przychodzącego.
- Pula adresów IP zaplecza do odbierania ruchu sieciowego.
- Wymagane porty źródłowe i docelowe.

Reguła modułu równoważenia obciążenia o nazwie **myLoadBalancerRule** nasłuchuje na porcie 80 w frontonie **LoadBalancerFrontEnd** . Reguła wysyła ruch sieciowy do puli adresów zaplecza **myBackendPool** na tym samym porcie 80.

Aby utworzyć regułę modułu równoważenia obciążenia:

1. Wybierz pozycję **wszystkie zasoby** w menu po lewej stronie, a następnie wybierz pozycję **myLoadBalancer** z listy zasobów.

1. W obszarze **Ustawienia** wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **Dodaj**.

1. Na stronie **Dodaj regułę równoważenia obciążenia** wprowadź lub wybierz następujące wartości, jeśli nie są one jeszcze obecne:

   - **Nazwa**: wprowadź **myLoadBalancerRule**.
   - **Adres IP frontonu:** Wprowadź **LoadBalancerFrontEnd**.
   - **Protokół**: wybierz pozycję **TCP**.
   - **Port**: wprowadź **80**.
   - **Port zaplecza**: wprowadź **80**.
   - **Pula zaplecza**: wybierz pozycję **myBackendPool**.
   - **Sonda kondycji**: wybierz pozycję **myHealthProbe**. 

1. Wybierz pozycję **OK**.

## <a name="create-a-private-link-service"></a>Tworzenie usługi łącza prywatnego

W tej sekcji utworzysz usługę linku prywatnego za pomocą usługi równoważenia obciążenia w warstwie Standardowa.

1. W lewym górnym rogu strony w Azure Portal wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **prywatne centrum linków (wersja zapoznawcza)**. Możesz również użyć pola wyszukiwania portalu, aby wyszukać link prywatny.

1. W **centrum linków prywatnych — Omówienie**  >  **udostępniania własnej usługi, aby inne osoby mogły nawiązać połączenie**, wybierz pozycję **Uruchom**.

1. W obszarze **Tworzenie usługi link prywatny — podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie           | Wartość                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Szczegóły projektu:  |                                                                              |
    | **Subskrypcja**      | Wybierz subskrypcję.                                                     |
    | **Grupa zasobów**    | Wybierz pozycję **myResourceGroupLB**.                                                    |
    | Szczegóły wystąpienia: |                                                                              |
    | **Nazwa**              | Wprowadź **myPrivateLinkService**. |
    | **Region**            | Wybierz pozycję **East US 2** (Wschodnie stany USA 2).                                                        |

1. Wybierz pozycję **Dalej: ustawienia wychodzące**.

1. W obszarze **Tworzenie usługi link prywatny — ustawienia wychodzące** wprowadź lub wybierz następujące informacje:

    | Ustawienie                           | Wartość                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | Wybierz pozycję **myLoadBalancer**.                                                           |
    | **Load Balancer adres IP frontonu** | Wybierz adres IP frontonu **myLoadBalancer**.                                |
    | **Źródłowa sieć wirtualna translatora adresów sieciowych**        | Wybierz pozycję **myVNet**.                                                                   |
    | **Źródłowa podsieć NAT**                 | Wybierz pozycję **myBackendSubnet**.                                                          |
    | **Włącz serwer proxy TCP v2**               | Wybierz opcję **tak** lub **nie** , w zależności od tego, czy aplikacja oczekuje nagłówka proxy protokołu TCP v2. |
    | **Ustawienia prywatnego adresu IP**       | Skonfiguruj metodę alokacji i adres IP dla każdego adresu IP translatora adresów sieciowych.                  |

1. Wybierz pozycję **Dalej: zabezpieczenia dostępu**.

1. W obszarze **Tworzenie usługi link prywatny — zabezpieczenia dostępu** wybierz pozycję **widoczność**, a następnie wybierz pozycję **Kontrola dostępu oparta na rolach**.
  
1. Wybierz pozycję **Dalej:**  >  **Przegląd tagów + Utwórz** lub wybierz kartę **Recenzja + tworzenie** w górnej części strony.

1. Przejrzyj informacje, a następnie wybierz pozycję **Utwórz**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu korzystania z usługi link prywatny Usuń grupę zasobów, aby wyczyścić zasoby używane w tym przewodniku Szybki Start.

1. Wprowadź **myResourceGroupLB** w polu wyszukiwania w górnej części portalu, a następnie wybierz pozycję **myResourceGroupLB** w wynikach wyszukiwania.
1. Wybierz pozycję **Usuń grupę zasobów**.
1. W polu **wpisz nazwę grupy zasobów** wprowadź wartość **WebResource**.
1. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono wewnętrzny moduł równoważenia obciążenia platformy Azure i usługę łącza prywatnego. Możesz również dowiedzieć się, jak [utworzyć prywatny punkt końcowy przy użyciu Azure Portal](./create-private-endpoint-portal.md).