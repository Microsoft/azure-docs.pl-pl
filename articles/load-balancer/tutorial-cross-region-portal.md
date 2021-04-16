---
title: 'Samouczek: tworzenie usługi równoważenia obciążenia między regionami przy użyciu Azure Portal'
titleSuffix: Azure Load Balancer
description: Rozpoczynanie pracy z tym samouczkiem w celu wdrożenia aplikacji między regionami Azure Load Balancer pomocą Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: 16320021ede4a4e285c4e1973c166d2cdf643c4a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529536"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Samouczek: tworzenie aplikacji między regionami Azure Load Balancer pomocą Azure Portal

Usługa równoważenia obciążenia między regionami zapewnia, że usługa jest dostępna globalnie w wielu regionach świadczenia usługi Azure. Jeśli jeden region ulegnie awarii, ruch jest przekierowyny do następnego regionu równoważenia obciążenia o najbliższej dobrej kondycji.  

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz między regionami równoważenia obciążenia.
> * Utwórz pulę zaplecza zawierającą dwa regionalne równoważenia obciążenia.
> * Utwórz regułę modułu równoważenia obciążenia.
> * Przetestuj równoważenie obciążenia.

Jeśli nie masz subskrypcji platformy Azure, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem utwórz bezpłatne konto.

> [!IMPORTANT]
> Wiele regionów Azure Load Balancer obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Dwie **standardowe sku** usługi Azure Load Balancer z pulami zaplecza wdrożonych w dwóch różnych regionach świadczenia usługi Azure.
    - Aby uzyskać informacje na temat tworzenia regionalnego standardowego równoważenia obciążenia i maszyn wirtualnych dla pul zaplecza, zobacz Szybki [start:](quickstart-load-balancer-standard-public-portal.md)tworzenie publicznego równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu Azure Portal .
        - Dołącz nazwę usługi równoważenia obciążenia, maszyn wirtualnych i innych zasobów w każdym regionie za pomocą **-R1** i **-R2.** 

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-cross-region-load-balancer"></a>Tworzenie między regionami równoważenia obciążenia

W tej sekcji utworzysz usługę równoważenia obciążenia między regionami i publiczny adres IP.

1. Wybierz pozycję **Utwórz zasób**. 
2. W polu wyszukiwania wprowadź tekst **Load balancer**. Wybierz **pozycję Load balancer (Równoważenie** obciążenia) w wynikach wyszukiwania.
3. Na stronie **Load balancer (Równoważenie** obciążenia) wybierz pozycję **Create (Utwórz).**
4. Na karcie **Podstawowe** na stronie **Tworzenie równoważenia** obciążenia wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz **pozycję Utwórz nową** i wprowadź **wartość CreateCRLBTutorial-rg** w polu tekstowym.|
    | Nazwa                   | Wprowadź **wartość myLoadBalancer-CR**                                   |
    | Region (Region)         | Wybierz **pozycję (Stany Zjednoczone) Zachodnie stany USA.**                                        |
    | Typ          | Wybierz pozycję **Publiczna**.                                        |
    | SKU           | Pozostaw wartość domyślną **Standardowa**. |
    | Warstwa           | Wybierz pozycję **Globalne** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**.|
    | Nazwa publicznego adresu IP | W **polu tekstowym wpisz myPublicIP-CR.**|
    | Preferencja routingu| Wybierz **pozycję Microsoft Network**. </br> Aby uzyskać więcej informacji na temat preferencji routingu, zobacz [Co to jest preferencja routingu (wersja zapoznawcza)?](../virtual-network/routing-preference-overview.md). |

    > [!NOTE]
    > Usługę równoważenia obciążenia między regionami można wdrożyć tylko w następujących regionach domowych: Wschodnie stany USA 2, Zachodnie stany USA, Europa Zachodnia, Azja Południowo-Wschodnia, Środkowe stany USA, Europa **Północna, Azja Wschodnia**. Aby uzyskać więcej informacji, zobacz **https://aka.ms/homeregionforglb**.


3. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przejrzyj i utwórz.**

4. Na karcie **Przeglądanie + tworzenie** wybierz pozycję **Utwórz.**   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Tworzenie między regionami równoważenia obciążenia" border="true":::

## <a name="create-backend-pool"></a>Tworzenie puli zaplecza

W tej sekcji dodasz dwa regionalne standardowe równoważenia obciążenia do puli zaplecza między regionami.

> [!IMPORTANT]
> Aby wykonać te kroki, upewnij się, że w twojej subskrypcji zostały wdrożone dwa regionalne usługi równoważenia obciążenia z pulami zaplecza.  Aby uzyskać więcej informacji, zobacz **[Szybki start: tworzenie publicznego](quickstart-load-balancer-standard-public-portal.md)** usługi równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu Azure Portal .

Utwórz pulę adresów zaplecza **myBackendPool-CR,** aby uwzględnić regionalne standardowe usługi równoważenia obciążenia.

1. Wybierz **pozycję Wszystkie** usługi w menu po lewej stronie, wybierz pozycję **Wszystkie** zasoby, a następnie wybierz **pozycję myLoadBalancer-CR** z listy zasobów.

2. W **obszarze Ustawienia** wybierz pozycję **Pule zaplecza,** a następnie wybierz **pozycję Dodaj**.

3. Na stronie **Dodawanie puli zaplecza** w nazwie wpisz **myBackendPool-CR.**

4. Wybierz pozycję **Dodaj**.

4. Wybierz **pozycję myBackendPool-CR.**

5. W **obszarze Load balancers (Równoważenie** obciążenia) wybierz pole rozwijane w obszarze **Load balancer (Równoważenie obciążenia).**

5. Wybierz **pozycję myLoadBalancer-R1** lub nazwę swojego równoważenia obciążenia w regionie 1.

6. Wybierz pole rozwijane w obszarze **Konfiguracja adresu IP frontendu**. Wybierz **pozycję LoadBalancerFrontEnd.**

7. Powtórz kroki 4–6, aby dodać **myLoadBalancer-R2.**

8. Wybierz pozycję **Dodaj**.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Dodawanie regionalnych równoważenia obciążenia do puli zaplecza" border="true":::

## <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

W tej sekcji utworzysz regułę równoważenia obciążenia:

* Nosi **nazwę myHTTPRule.**
* W frontonie o **nazwie LoadBalancerFrontEnd**.
* Nasłuchiwanie **na porcie 80.**
* Kieruje ruch o zrównoważonym obciążeniu do zaplecza o nazwie **myBackendPool-CR** na **porcie 80.**

    > [!NOTE]
    > Port frontendu musi być zgodne z portem zaplecza i portem frontendu regionalnych usług równoważenia obciążenia w puli zaplecza.

1. Wybierz **pozycję Wszystkie** usługi w menu po lewej stronie, wybierz pozycję **Wszystkie** zasoby, a następnie wybierz **pozycję myLoadBalancer-CR** z listy zasobów.

2. W **obszarze Ustawienia** wybierz pozycję Reguły **równoważenia obciążenia,** a następnie wybierz pozycję **Dodaj**.

3. Użyj tych wartości, aby skonfigurować regułę równoważenia obciążenia:
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHTTPRule**. |
    | Wersja adresu IP | Wybieranie **protokołu IPv4** |
    | Adres IP frontony | Wybierz **pozycję LoadBalancerFrontEnd** |
    | Protokół | Wybierz pozycję **TCP**. |
    | Port | Wprowadź **wartość 80**.|
    | Port zaplecza | Wprowadź **wartość 80**. |
    | Pula zaplecza | Wybierz **pozycję myBackendPool.**|
    | Limit czasu bezczynności (minuty) | Przesuń suwak do **15**. |
    | Resetowanie protokołu TCP | Wybierz pozycję **Włączone**. |

4. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **OK.**

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Tworzenie reguły równoważenia obciążenia" border="true":::

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

W tej sekcji przetestujemy między regionami równoważenia obciążenia. Połączysz się z publicznym adresem IP w przeglądarce internetowej.  Zatrzymasz maszyny wirtualne w jednej z regionalnych pul zaplecza usługi równoważenia obciążenia i zobaczysz, jak jest to używane w przypadku trybu failover.

1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Wybierz **pozycję Wszystkie usługi** w menu po lewej stronie, wybierz pozycję Wszystkie **zasoby,** a następnie wybierz **pozycję myPublicIP-CR.**

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Testowanie modułu równoważenia obciążenia" border="true":::

3. Zatrzymaj maszyny wirtualne w puli zaplecza jednego z regionalnych równoważenia obciążenia.

4. Odśwież przeglądarkę internetową i obserwuj, jak połączenie jest w stanie failover z innym regionalnym usługą równoważenia obciążenia.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Testowanie równoważenia obciążenia po pracy w awarii" border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. 

W tym celu wybierz grupę zasobów **CreateCRLBTutorial-rg** zawierającą zasoby, a następnie wybierz pozycję **Usuń.**

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

* Utworzono między regionami równoważenie obciążenia.
* Dodano regionalne równoważenie obciążenia do puli zaplecza między regionami.
* Utworzono regułę równoważenia obciążenia.
* Przetestowano równoważenie obciążenia.

Aby uzyskać więcej informacji na temat równoważenia obciążenia między regionami, zobacz:
> [!div class="nextstepaction"]
> [Równoważenie obciążenia między regionami (wersja zapoznawcza)](cross-region-overview.md)
