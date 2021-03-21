---
title: 'Samouczek: integracja bramy translatora adresów sieciowych z publicznym modułem równoważenia obciążenia — Azure Portal'
titleSuffix: Virtual Network NAT
description: W tym samouczku dowiesz się, jak zintegrować Virtual Network bramę translatora adresów sieciowych z publicznym modułem równoważenia obciążenia przy użyciu Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: 345ccb68ebb31460f4a75b31a7d3a946160da6e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104657972"
---
# <a name="tutorial-integrate-a-nat-gateway-with-a-public-load-balancer-using-the-azure-portal"></a>Samouczek: Integrowanie bramy NAT z publicznym modułem równoważenia obciążenia przy użyciu Azure Portal

W tym samouczku dowiesz się, jak zintegrować bramę NAT z publicznym modułem równoważenia obciążenia.

Domyślnie usługa Azure usługa Load Balancer w warstwie Standardowa jest bezpieczna. Łączność wychodząca jest jawnie definiowana przez włączenie wychodzącego ruchu źródłowego (translator adresów sieciowych). W regule równoważenia obciążenia lub regułach ruchu wychodzącego jest włączony element równorzędny. 

Integracja z bramą translatora adresów sieciowych zastępuje potrzeby reguł ruchu wychodzącego dla wychodzącego przychodzącego zestawu danych puli zaplecza 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie Azure Load Balancer
> * Utwórz dwie maszyny wirtualne dla puli zaplecza Azure Load Balancer
> * Tworzenie bramy translatora adresów sieciowych
> * Weryfikowanie łączności wychodzącej maszyn wirtualnych w puli zaplecza modułu równoważenia obciążenia

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji utworzysz Azure Load Balancer standardowego. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Utwórz zasób**. 
3. W polu wyszukiwania wprowadź **moduł równoważenia obciążenia**. Wybierz pozycję **moduł równoważenia obciążenia** w wynikach wyszukiwania.
4. Na stronie **moduł równoważenia obciążenia** wybierz pozycję **Utwórz**.
5. Na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | W polu tekstowym wybierz pozycję **Utwórz nową** i wprowadź **TutorPubLBNAT-RG** .|
    | Nazwa                   | Wprowadź **myLoadBalancer**                                   |
    | Region (Region)         | Wybierz pozycję **(US) Wschodnie stany USA**.                                        |
    | Typ          | Wybierz pozycję **Publiczna**.                                        |
    | SKU           | Pozostaw wartość domyślną **Standardowa**. |
    | Warstwa          | Pozostaw domyślne ustawienie **regionalne**. |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. Jeśli masz istniejący publiczny adres IP, którego chcesz użyć, wybierz pozycję **Użyj istniejącej**. |
    | Nazwa publicznego adresu IP | W polu tekstowym wpisz **myPublicIP-lb** .|
    | Strefa dostępności | Wybierz opcję **strefa nadmiarowa** , aby utworzyć odporny moduł równoważenia obciążenia. Aby utworzyć strefowy moduł równoważenia obciążenia, wybierz określoną strefę z 1, 2 lub 3 |
    | Dodaj publiczny adres IPv6 | Wybierz pozycję **Nie**. </br> Aby uzyskać więcej informacji na temat adresów IPv6 i modułu równoważenia obciążenia, zobacz [co to jest protokół IPv6 dla platformy Azure Virtual Network?](../virtual-network/ipv6-overview.md)  |
    | Preferencja routingu | Pozostaw wartość domyślną **sieci Microsoft**. </br> Aby uzyskać więcej informacji o preferencjach routingu, zobacz [co to jest preferencja routingu (wersja zapoznawcza)?](../virtual-network/routing-preference-overview.md). |

6. Zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Przegląd + Utwórz**.

7. Na karcie **Recenzja i tworzenie** wybierz pozycję **Utwórz**.

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
   

3. Pozostaw ustawienia domyślne i wybierz pozycję **Dodaj**.

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
    | Translacja adresów sieciowych dla ruchu wychodzącego (Resources) | Wybierz **(zalecane) Użyj reguł ruchu wychodzącego, aby zapewnić członkom puli zaplecza dostęp do Internetu.** |

4. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **OK**.

## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. Wybierz przycisk **Utwórz**. 

3. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **TutorPubLBNAT — RG** |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myVNet**                                    |
    | Region (Region)           | Wybierz **Wschodnie stany USA** |

4. Wybierz kartę **adresy IP** lub wybierz przycisk **Dalej: adresy IP** w dolnej części strony.

5. Na karcie **adresy IP** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Przestrzeń adresowa IPv4 | Wprowadź **10.1.0.0/16** |

6. W obszarze **Nazwa podsieci** wybierz pozycję **domyślny** wyraz.

7. W obszarze **Edytuj podsieć** wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa podsieci | Wprowadź **myBackendSubnet** |
    | Zakres adresów podsieci | Wprowadź **10.1.0.0/24** |

8. Wybierz pozycję **Zapisz**.

9. Wybierz kartę **zabezpieczenia** .

10. W obszarze **BastionHost** wybierz pozycję **enable (Włącz**). Wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa bastionu | Wprowadź **myBastionHost** |
    | Przestrzeń adresowa AzureBastionSubnet | Wprowadź **10.1.1.0/24** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> W obszarze **Nazwa** wprowadź **myBastionIP**. </br> Wybierz przycisk **OK**. |


11. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

12. Wybierz przycisk **Utwórz**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tej sekcji utworzysz dwie maszyny wirtualne (**myVM1** i **myVM2**) w dwóch różnych strefach (**Strefa 1** i **strefa 2**).

Te maszyny wirtualne są dodawane do puli zaplecza modułu równoważenia obciążenia, który został utworzony wcześniej.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję **TutorPubLBNAT — RG** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM1** |
    | Region (Region) | Wybierz **Wschodnie stany USA** |
    | Opcje dostępności | Wybierz **strefy dostępności** |
    | Strefa dostępności | Wybierz **1** |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Pozostaw wartość domyślną |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |
    | **Reguły portów ruchu przychodzącego** |  |
    | Publiczne porty wejściowe | Nie zaznaczaj **niczego** |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | **myVNet** |
    | Podsieć | **myBackendSubnet** |
    | Publiczny adres IP | Wybierz pozycję **Brak**. |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **Zaawansowane**|
    | Konfigurowanie sieciowej grupy zabezpieczeń | Wybierz pozycję **Utwórz nowy**. </br> W **grupie Tworzenie zabezpieczeń sieci** wprowadź **MyNSG** w polu **Nazwa**. </br> W obszarze **reguły ruchu przychodzącego** wybierz pozycję **+ Dodaj regułę ruchu przychodzącego**. </br> W obszarze  **zakresy portów docelowych** wprowadź **80**. </br> W obszarze **priorytet** wprowadź **100**. </br> W polu **Nazwa** wprowadź **myHTTPRule** </br> Wybierz pozycję **Dodaj**. </br> Wybierz przycisk **OK**. |
    | **Równoważenie obciążenia**  |
    | Umieścić tę maszynę wirtualną za istniejącym rozwiązaniem równoważenia obciążenia? | Wybierz pozycję **Tak** |
    | **Ustawienia równoważenia obciążenia** |
    | Opcje równoważenia obciążenia | Wybieranie **modułu równoważenia obciążenia platformy Azure** |
    | Wybierz moduł równoważenia obciążenia | Wybierz **myLoadBalancer**  |
    | Wybierz pulę zaplecza | Wybierz **myBackendPool** |
   
5. Wybierz pozycję **Przejrzyj i utwórz**. 
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

7. Wykonaj kroki od 1 do 7, aby utworzyć maszynę wirtualną z następującymi wartościami i innymi ustawieniami takimi jak **myVM1**:

    | Ustawienie | MW 2 |
    | ------- | ----- |
    | Nazwa |  **myVM2** |
    | Strefa dostępności | **2** |
    | Sieciowa grupa zabezpieczeń | Wybierz istniejący **myNSG**| 

## <a name="create-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

W tej sekcji utworzysz bramę translatora adresów sieciowych i przypiszesz ją do podsieci w utworzonej wcześniej sieci wirtualnej.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > sieci > Brama translatora adresów** sieciowych lub Wyszukaj **bramę translatora adresów sieciowych** w polu wyszukiwania.

2. Wybierz przycisk **Utwórz**. 

3. W obszarze **Utwórz bramę translacji adresów sieciowych (NAT)** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz swoją subskrypcję platformy Azure.                                  |
    | Grupa zasobów   | Wybierz pozycję **TutorPubLBNAT-RG**. |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myNATGateway**                                    |
    | Region (Region)           | Wybierz **(US) Wschodnie stany USA**  |
    | Strefa dostępności | Wybierz pozycję **Brak**. |
    | Limit czasu bezczynności (minuty) | Wprowadź **10**. |

4. Wybierz kartę **wychodzący adres IP** lub wybierz przycisk **Dalej: wychodzący adres IP** w dolnej części strony.

5. Na karcie **wychodzący adres IP** wprowadź lub wybierz następujące informacje:

    | **Ustawienie** | **Wartość** |
    | ----------- | --------- |
    | Publiczne adresy IP | Wybierz pozycję **Utwórz nowy publiczny adres IP**. </br> W polu **Nazwa** wprowadź **myPublicIP-translator adresów sieciowych**. </br> Wybierz przycisk **OK**. |

6. Wybierz kartę **podsieć** lub wybierz przycisk **Dalej: podsieć** w dolnej części strony.

7. Na karcie **podsieć** wybierz pozycję **myVNet** w polu ściąganie **sieci wirtualnej** .

8. Zaznacz pole wyboru obok pozycji **myBackendSubnet**.

9. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.

10. Wybierz przycisk **Utwórz**.

## <a name="test-nat-gateway"></a>Testowanie bramy translatora adresów sieciowych

W tej sekcji przetestujemy bramę translatora adresów sieciowych. Najpierw odnajdziemy publiczny adres IP bramy translatora adresów sieciowych. Następnie nastąpi połączenie z testową maszyną wirtualną i zweryfikowanie połączenia wychodzącego za pomocą bramy translatora adresów sieciowych.
    
1. Znajdź publiczny adres IP dla bramy translatora adresów sieciowych na ekranie **Przegląd** . Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myPublicIP**.

2. Zanotuj publiczny adres IP:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/find-public-ip.png" alt-text="Odnajdź publiczny adres IP bramy translatora adresów sieciowych" border="true":::

3. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myVM1** , która znajduje się w grupie zasobów **TutorPubLBNAT-RG** .

4. Na stronie **Przegląd** wybierz opcję **Połącz**, a następnie **bastionu**.

5. Wybierz przycisk **bastionu Użyj** niebieska.

6. Wprowadź nazwę użytkownika i hasło wprowadzone podczas tworzenia maszyny wirtualnej.

7. Otwórz program **Internet Explorer** w systemie **myVM1**.

8. Wprowadź **https://whatsmyip.com** na pasku adresu.

9. Sprawdź, czy adres IP wyświetlany jest zgodny z adresem bramy NAT zanotowanym w poprzednim kroku:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-public-portal/my-ip.png" alt-text="Internet Explorer pokazujący zewnętrzny adres IP dla ruchu wychodzącego" border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń sieć wirtualną, maszynę wirtualną i bramę translatora adresów sieciowych, wykonując następujące czynności:

1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.

2. Wybierz grupę zasobów **TutorPubLBNAT-RG** .

3. Wybierz pozycję **Usuń grupę zasobów**.

4. Wprowadź **TutorPubLBNAT-RG** i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Virtual Network translator adresów sieciowych, zobacz:
> [!div class="nextstepaction"]
> [Omówienie Virtual Network NAT](nat-overview.md)
