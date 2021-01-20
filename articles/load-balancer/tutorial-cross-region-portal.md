---
title: 'Samouczek: Tworzenie międzyregionowego modułu równoważenia obciążenia przy użyciu Azure Portal'
titleSuffix: Azure Load Balancer
description: Rozpocznij pracę z tym samouczkiem, wdrażając Azure Load Balancer międzyregionowe z Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/24/2020
ms.openlocfilehash: d94736656f691da9e893e4619a2299a061acd8e8
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611206"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Samouczek: Tworzenie Azure Load Balancer międzyregionowych przy użyciu Azure Portal

Moduł równoważenia obciążenia między regionami gwarantuje, że usługa jest dostępna globalnie w wielu regionach świadczenia usługi Azure. Jeśli jeden region ulegnie awarii, ruch jest kierowany do następnego najbliższego w dobrej kondycji regionalnego modułu równoważenia obciążenia.  

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Utwórz międzyregionowy moduł równoważenia obciążenia.
> * Utwórz pulę zaplecza zawierającą dwa regionalne moduły równoważenia obciążenia.
> * Utwórz regułę modułu równoważenia obciążenia.
> * Przetestuj moduł równoważenia obciążenia.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

> [!IMPORTANT]
> Azure Load Balancer międzyregionowe jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.
- Dwie usługi równoważenia obciążenia platformy Azure w **warstwie Standardowa** z pulami zaplecza wdrożonymi w dwóch różnych regionach świadczenia usługi Azure.
    - Aby uzyskać informacje na temat tworzenia regionalnego standardowego modułu równoważenia obciążenia i maszyn wirtualnych dla pul zaplecza, zobacz [Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu Azure Portal](quickstart-load-balancer-standard-public-portal.md).
        - Dołącz nazwy modułów równoważenia obciążenia, maszyn wirtualnych i innych zasobów w każdym regionie z **-R1** i **-R2**. 

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

[Zaloguj](https://portal.azure.com) się do portalu Azure w wersji zapoznawczej.

## <a name="create-cross-region-load-balancer"></a>Tworzenie międzyregionowego modułu równoważenia obciążenia

Ta sekcja umożliwia utworzenie międzyregionowego modułu równoważenia obciążenia i publicznego adresu IP.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > sieci > Load Balancer** lub Wyszukaj **Load Balancer** w polu wyszukiwania.

2. Na karcie **podstawy** na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | W polu tekstowym wybierz pozycję **Utwórz nową** i wprowadź **CreateCRLBTutorial-RG** .|
    | Nazwa                   | Wprowadź **myLoadBalancer-CR**                                   |
    | Region (Region)         | wybierz pozycję **Zachodnie stany USA**.                                        |
    | Typ          | Wybierz pozycję **Publiczna**.                                        |
    | Jednostka SKU           | Wybierz pozycję **standardowa** |
    | Warstwa           | Wybierz **globalne** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**.|
    | Nazwa publicznego adresu IP | W polu tekstowym wpisz **myPublicIP-CR** .|
    | Preferencja routingu| Wybieranie **sieci firmy Microsoft** |

    > [!NOTE]
    > Moduł równoważenia obciążenia między regionami można wdrożyć tylko w następujących regionach głównych: **Wschodnie stany USA 2, zachodnie stany USA, Europa Zachodnia, Azja Południowo-Wschodnia, środkowe stany USA, Europa Północna, Azja Wschodnia**. Aby uzyskać więcej informacji, zobacz **https://aka.ms/homeregionforglb**.


3. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**.

4. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Tworzenie międzyregionowego modułu równoważenia obciążenia" border="true":::

## <a name="create-backend-pool"></a>Tworzenie puli zaplecza

W tej sekcji dodasz dwa regionalne standardowe usługi równoważenia obciążenia do puli zaplecza modułu równoważenia obciążenia między regionami.

> [!IMPORTANT]
> Aby wykonać te kroki, należy się upewnić, że w subskrypcji wdrożono dwa regionalne moduły równoważenia obciążenia z pulami zaplecza.  Aby uzyskać więcej informacji, zobacz **[Przewodnik Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu Azure Portal](quickstart-load-balancer-standard-public-portal.md)**.

Utwórz pulę adresów zaplecza **myBackendPool-CR** , aby uwzględnić regionalne standardowe usługi równoważenia obciążenia.

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer-CR** z listy zasobów.

2. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **Dodaj**.

3. Na stronie **Dodawanie puli zaplecza** w polu Nazwa wpisz **myBackendPool-CR**.

4. Wybierz pozycję **Dodaj**.

4. Wybierz pozycję **myBackendPool-CR**.

5. W obszarze usługi **równoważenia obciążenia** wybierz pole ściągania w obszarze **moduł równoważenia obciążenia**.

5. Wybierz pozycję **myLoadBalancer-R1** lub nazwę modułu równoważenia obciążenia w regionie 1.

6. Wybierz pole ściągania w obszarze **Konfiguracja adresu IP frontonu**. Wybierz **LoadBalancerFrontEnd**.

7. Powtórz kroki 4-6, aby dodać **myLoadBalancer-R2**.

8. Wybierz pozycję **Dodaj**.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Dodawanie regionalnych modułów równoważenia obciążenia do usługi ustawień httpsettings elementu" border="true":::

## <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

W tej sekcji utworzysz sondę kondycji, aby utworzyć regułę równoważenia obciążenia:

* O nazwie **myHealthProbe**.
* Protokół **TCP**.
* Interwał **5** sekund.
* Próg złej kondycji **dwóch** awarii.

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer-CR** z listy zasobów.

2. W obszarze **Ustawienia** wybierz pozycję **Sondy kondycji**.

3. Użyj tych wartości, aby skonfigurować sondę kondycji:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHealthProbe**. |
    | Protokół | Wybierz pozycję **TCP**. |
    | Port | Wprowadź **80**. |
    | Interwał | Wprowadź **5**. |
    | Próg złej kondycji | Wprowadź **2**. |

4. Wybierz przycisk **OK**.

    > [!NOTE]
    > Moduł równoważenia obciążenia między regionami ma wbudowaną sondę kondycji. To sondowanie jest symbolem zastępczym dla tworzenia reguły równoważenia obciążenia.  Aby uzyskać więcej informacji, zobacz **[ograniczenia dotyczące międzyregionowego modułu równoważenia obciążenia](cross-region-overview.md#limitations)**.

## <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

W tej sekcji utworzysz regułę modułu równoważenia obciążenia:

* O nazwie **myHTTPRule**.
* Na frontonie o nazwie **LoadBalancerFrontEnd**.
* Nasłuchiwanie na **porcie 80**.
* Kieruje ruch o zrównoważonym obciążeniu do zaplecza o nazwie **myBackendPool-CR** na **porcie 80**.

    > [!NOTE]
    > Port frontonu musi być zgodny z portem zaplecza i portem frontonu regionalnych modułów równoważenia obciążenia w puli zaplecza.

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer-CR** z listy zasobów.

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
    | Limit czasu bezczynności (minuty) | Przesuń suwak do **15**. |
    | Resetowanie protokołu TCP | Wybierz pozycję **Włączone**. |

4. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **OK**.

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Utwórz regułę modułu równoważenia obciążenia" border="true":::

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

W tej sekcji przetestujesz moduł równoważenia obciążenia między regionami. Nastąpi połączenie z publicznym adresem IP w przeglądarce internetowej.  Zatrzymasz maszyny wirtualne w jednym z pul zaplecza modułu równoważenia obciążenia i obserwuj przełączenia w tryb failover.

1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myPublicIP-CR**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Testowanie modułu równoważenia obciążenia" border="true":::

3. Zatrzymaj maszyny wirtualne w puli zaplecza jednego z regionalnych modułów równoważenia obciążenia.

4. Odśwież przeglądarkę internetową i zaobserwuj tryb failover połączenia z innym regionalnym modułem równoważenia obciążenia.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Testowanie modułu równoważenia obciążenia po przejściu w tryb failover" border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, moduł równoważenia obciążenia i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. 

W tym celu wybierz grupę zasobów **CreateCRLBTutorial-RG** , która zawiera zasoby, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

* Utworzono moduł równoważenia obciążenia między regionami.
* Dodano regionalne moduły równoważenia obciążenia do puli zaplecza międzyregionowego modułu równoważenia obciążenia.
* Utworzono regułę równoważenia obciążenia.
* Przetestowano moduł równoważenia obciążenia.

Aby uzyskać więcej informacji na temat modułu równoważenia obciążenia obejmującego wiele regionów, zobacz [moduł równoważenia obciążenia między regionami (wersja zapoznawcza)](cross-region-overview.md).


Przejdź do następnego artykułu, aby dowiedzieć się, jak:
> [!div class="nextstepaction"]
> [Równoważenie obciążenia maszyn wirtualnych w różnych strefach dostępności](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
