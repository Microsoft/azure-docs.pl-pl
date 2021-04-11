---
title: 'Samouczek: Tworzenie publicznego modułu równoważenia obciążenia przy użyciu zaplecza opartego na protokole IP Azure Portal'
titleSuffix: Azure Load Balancer
description: W tym samouczku dowiesz się, jak utworzyć publiczny moduł równoważenia obciążenia za pomocą puli zaplecza opartego na protokole IP.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 3/31/2021
ms.custom: template-tutorial
ms.openlocfilehash: f8174d46d1674e0cf81e36e89f6fb6180091a9b9
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123591"
---
# <a name="tutorial-create-a-public-load-balancer-with-an-ip-based-backend-using-the-azure-portal"></a>Samouczek: Tworzenie publicznego modułu równoważenia obciążenia z zapleczem opartym na protokole IP przy użyciu Azure Portal

W tym samouczku dowiesz się, jak utworzyć publiczny moduł równoważenia obciążenia za pomocą puli zaplecza opartego na protokole IP. 

Tradycyjne wdrożenie Azure Load Balancer używa interfejsu sieciowego maszyn wirtualnych. W przypadku zaplecza opartego na protokole IP maszyny wirtualne są dodawane do zaplecza według adresu IP.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie bramy translatora adresów sieciowych na potrzeby łączności wychodzącej
> * Tworzenie Azure Load Balancer
> * Tworzenie puli zaplecza opartego na protokole IP
> * Tworzenie dwóch maszyn wirtualnych
> * Testowanie modułu równoważenia obciążenia
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną dla modułu równoważenia obciążenia, bramy translatora adresów sieciowych i maszyn wirtualnych.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > Sieć > Sieć wirtualna** lub wyszukaj frazę **Sieć wirtualna** w polu wyszukiwania.

2. Wybierz przycisk **Utwórz**. 

3. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz subskrypcję platformy Azure                                  |
    | Grupa zasobów   | Wybierz pozycję **TutorPubLBIP — RG** |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myVNet**                                    |
    | Region (Region)           | Wybierz **(US) Wschodnie stany USA** |

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
    | Przestrzeń adresowa AzureBastionSubnet | Wprowadź **10.1.1.0/27** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> W obszarze **Nazwa** wprowadź **myBastionIP**. </br> Wybierz przycisk **OK**. |


11. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

12. Wybierz przycisk **Utwórz**.
## <a name="create-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych

W tej sekcji utworzysz bramę translatora adresów sieciowych i przypiszesz ją do podsieci w utworzonej wcześniej sieci wirtualnej.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób > sieci > Brama translatora adresów** sieciowych lub Wyszukaj **bramę translatora adresów sieciowych** w polu wyszukiwania.

2. Wybierz przycisk **Utwórz**. 

3. W obszarze **Utwórz bramę translacji adresów sieciowych (NAT)** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | **Ustawienie**          | **Wartość**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Szczegóły projektu**  |                                                                 |
    | Subskrypcja     | Wybierz swoją subskrypcję platformy Azure.                                  |
    | Grupa zasobów   | W polu tekstowym wybierz pozycję **Utwórz nową** i wprowadź **TutorPubLBIP-RG** . </br> Wybierz przycisk **OK**. |
    | **Szczegóły wystąpienia** |                                                                 |
    | Nazwa             | Wprowadź **myNATgateway**                                    |
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
## <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji utworzysz Azure Load Balancer standardowego. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Utwórz zasób**. 
3. W polu wyszukiwania wprowadź **moduł równoważenia obciążenia**. Wybierz pozycję **moduł równoważenia obciążenia** w wynikach wyszukiwania.
4. Na stronie **moduł równoważenia obciążenia** wybierz pozycję **Utwórz**.
5. Na stronie **Tworzenie modułu równoważenia obciążenia** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | **Szczegóły projektu** |   |
    | Subskrypcja               | Wybierz subskrypcję.    |    
    | Grupa zasobów         | Wybierz pozycję **TutorPubLBIP-RG**.|
    | **Szczegóły wystąpienia** |   |
    | Nazwa                   | Wprowadź **myLoadBalancer**                                   |
    | Region (Region)         | Wybierz pozycję **(US) Wschodnie stany USA**.                                        |
    | Typ          | Wybierz pozycję **Publiczna**.                                        |
    | SKU           | Pozostaw wartość domyślną **Standardowa**. |
    | Warstwa          | Pozostaw domyślne ustawienie **regionalne**. |
    | **Publiczny adres IP** |   |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> Jeśli masz istniejący publiczny adres IP, którego chcesz użyć, wybierz pozycję **Użyj istniejącej**. |
    | Nazwa publicznego adresu IP | W polu tekstowym wprowadź **myPublicIP-lb** .|
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

2. W obszarze **Ustawienia** wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **+ Dodaj**.

3. Na stronie **Dodawanie puli zaplecza** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myBackendPool**. |
    | Sieć wirtualna | Wybierz pozycję **myVNet**. |
    | Konfiguracja puli zaplecza | Wybierz pozycję **adres IP**. |
    | Wersja protokołu IP | Wybierz pozycję **IPv4**. |

4. Wybierz pozycję **Dodaj**.

### <a name="create-a-health-probe"></a>Tworzenie sondy kondycji

Moduł równoważenia obciążenia monitoruje stan aplikacji przy użyciu sondy kondycji. 

Sonda kondycji dodaje lub usuwa maszyny wirtualne z modułu równoważenia obciążenia na podstawie odpowiedzi na testy kondycji. 

Utwórz sondę kondycji o nazwie **myHealthProbe**, aby monitorować kondycję maszyn wirtualnych.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **+ Dodaj**.
    
    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHealthProbe**. |
    | Protokół | Wybierz pozycję **TCP**. |
    | Port | Wprowadź **80**.|
    | Interwał | Wprowadź **15** dla liczby **interwałów** (w sekundach) między kolejnymi próbami sondowania. |
    | Próg złej kondycji | Wybierz wartość **2**. |
   
3. Pozostaw ustawienia domyślne i wybierz pozycję **Dodaj**.

### <a name="create-a-load-balancer-rule"></a>Tworzenie reguły modułu równoważenia obciążenia

Reguła modułu równoważenia obciążenia służy do definiowania sposobu dystrybucji ruchu do maszyn wirtualnych. Należy zdefiniować konfigurację adresu IP frontonu dla ruchu przychodzącego i pulę adresów IP zaplecza do odbierania ruchu. Port źródłowy i docelowy są zdefiniowane w regule. 

W tej sekcji utworzysz regułę modułu równoważenia obciążenia:

* O nazwie **myHTTPRule**.
* Na frontonie o nazwie **LoadBalancerFrontEnd**.
* Nasłuchiwanie na **porcie 80**.
* Kieruje ruch o zrównoważonym obciążeniu do zaplecza o nazwie **myBackendPool** na **porcie 80**.

1. W menu po lewej stronie wybierz pozycję Wszystkie **usługi** , wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myLoadBalancer** z listy zasoby.

2. W obszarze **Ustawienia** wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **+ Dodaj**.

3. Wprowadź lub wybierz następujące informacje dotyczące reguły modułu równoważenia obciążenia:
    
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
    | Trwałość sesji | Pozostaw wartość domyślną **Brak**. |
    | Limit czasu bezczynności (minuty) | Wprowadź **15** minut. |
    | Resetowanie protokołu TCP | Wybierz pozycję **Włączone**. |
    | Pływający adres IP | Wybierz opcję **Wyłączone**. |
    | Translacja adresów sieciowych dla ruchu wychodzącego (Resources) | Wybierz **(zalecane) Użyj reguł ruchu wychodzącego, aby zapewnić członkom puli zaplecza dostęp do Internetu.** |

4. Pozostaw pozostałe wartości domyślne, a następnie wybierz pozycję **Dodaj**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tej sekcji utworzysz dwie maszyny wirtualne (**myVM1** i **myVM2**) w dwóch różnych strefach (**Strefa 1** i **strefa 2**).

Te maszyny wirtualne są dodawane do puli zaplecza modułu równoważenia obciążenia, który został utworzony wcześniej.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wprowadź lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję **TutorPubLBIP — RG** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM1** |
    | Region (Region) | Wybierz **(US) Wschodnie stany USA** |
    | Opcje dostępności | Wybierz **strefy dostępności** |
    | Strefa dostępności | Wybierz **1** |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter** |
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
    | Konfigurowanie sieciowej grupy zabezpieczeń | Wybierz pozycję **Utwórz nowy**. </br> W **grupie Tworzenie zabezpieczeń sieci** wprowadź **MyNSG** w polu **Nazwa**. </br> W obszarze **reguły ruchu przychodzącego** wybierz pozycję **+ Dodaj regułę ruchu przychodzącego**. </br> W obszarze **Usługa** wybierz pozycję **http**. </br> W polu **priorytet** wprowadź **100**. </br> W obszarze **Nazwa** wprowadź **myHTTPRule** </br> Wybierz pozycję **Dodaj**. </br> Wybierz przycisk **OK**. |
    | **Równoważenie obciążenia**  |
    | Umieścić tę maszynę wirtualną za istniejącym rozwiązaniem równoważenia obciążenia? | zaznacz pole wyboru.|
    | **Ustawienia równoważenia obciążenia** |
    | Opcje równoważenia obciążenia | Wybieranie **modułu równoważenia obciążenia platformy Azure** |
    | Wybierz moduł równoważenia obciążenia | Wybierz **myLoadBalancer**  |
    | Wybierz pulę zaplecza | Wybierz **myBackendPool** |
   
5. Wybierz pozycję **Przejrzyj i utwórz**. 
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

7. Wykonaj kroki od 1 do 6, aby utworzyć maszynę wirtualną z następującymi wartościami i innymi ustawieniami takimi jak **myVM1**:

    | Ustawienie | MW 2 |
    | ------- | ----- |
    | Nazwa |  **myVM2** |
    | Strefa dostępności | **2** |
    | Sieciowa grupa zabezpieczeń | Wybierz istniejący **myNSG**| 

## <a name="install-iis"></a>Instalowanie usług IIS

1. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie na liście zasobów wybierz pozycję **myVM1** , która znajduje się w grupie zasobów **TutorPubLBIP-RG** .

2. Na stronie **Przegląd** wybierz opcję **Połącz**, a następnie **bastionu**.

3. Wybierz przycisk **Użyj bastionu** .

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
    Remove-Item C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Zamknij sesję bastionu z **myVM1**.

9. Powtórz kroki od 1 do 7, aby zainstalować usługi IIS i zaktualizowany plik iisstart.htm w **myVM2**.

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

1. Znajdź publiczny adres IP dla usługi Load Balancer na ekranie **Przegląd**. Wybierz pozycję **wszystkie usługi** w menu po lewej stronie, wybierz pozycję **wszystkie zasoby**, a następnie wybierz pozycję **myPublicIP-lb**.

2. Skopiuj publiczny adres IP, a następnie wklej go na pasku adresu przeglądarki. W przeglądarce jest wyświetlana domyślna strona internetowego serwera usług IIS.

   ![Internetowy serwer usług IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Aby zobaczyć, jak moduł równoważenia obciążenia dystrybuuje ruch do myVM2, Wymuś odświeżenie przeglądarki internetowej z komputera klienckiego.
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń sieć wirtualną, maszynę wirtualną i bramę translatora adresów sieciowych, wykonując następujące czynności:

1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.

2. Wybierz grupę zasobów **TutorPubLBIP-RG** .

3. Wybierz pozycję **Usuń grupę zasobów**.

4. Wprowadź **TutorPubLBIP-RG** i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku:

* Utworzono sieć wirtualną
* Tworzenie bramy translatora adresów sieciowych
* Tworzenie modułu równoważenia obciążenia za pomocą puli zaplecza opartego na protokole IP
* Przetestowano moduł równoważenia obciążenia

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć moduł równoważenia obciążenia między regionami:
> [!div class="nextstepaction"]
> [Tworzenie Azure Load Balancer międzyregionowych przy użyciu Azure Portal](tutorial-cross-region-portal.md)
