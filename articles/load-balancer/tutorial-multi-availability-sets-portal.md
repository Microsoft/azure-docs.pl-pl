---
title: 'Samouczek: tworzenie usługi równoważenia obciążenia z więcej niż jednym zestawem dostępności w puli zaplecza — Azure Portal'
titleSuffix: Azure Load Balancer
description: W tym samouczku wdrożysz Azure Load Balancer więcej niż jeden zestaw dostępności w puli zaplecza.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 04/16/2021
ms.custom: template-tutorial
ms.openlocfilehash: 21ff43217a7b2bd874a384f3b07a48d5223a1be2
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602356"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>Samouczek: tworzenie usługi równoważenia obciążenia z więcej niż jednym zestawem dostępności w puli zaplecza przy użyciu Azure Portal

W ramach wdrożenia o wysokiej dostępności maszyny wirtualne są często grupowane w wiele zestawów dostępności. 

Load Balancer obsługuje więcej niż jeden zestaw dostępności z maszynami wirtualnymi w puli zaplecza.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie bramy nat dla łączności wychodzącej
> * Tworzenie standardowej wersji SKU Azure Load Balancer
> * Tworzenie czterech maszyn wirtualnych i dwóch zestawów dostępności
> * Dodawanie maszyn wirtualnych w zestawach dostępności do puli zaplecza usługi Load Balancer
> * Testowanie modułu równoważenia obciążenia

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną dla usługi równoważenia obciążenia i innych zasobów używanych w tym samouczku.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W polu wyszukiwania w górnej części portalu wprowadź wartość **Sieć wirtualna**.

3. W wynikach wyszukiwania wybierz pozycję **Sieci wirtualne**.

4. Wybierz **pozycję + Utwórz.**

5. Na karcie **Podstawowe** w obszarze **Tworzenie sieci wirtualnej** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ------|
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy**. </br> Wprowadź **tutorLBmultiAVS-rg w** **nazwie**. |
    | **Szczegóły wystąpienia** |   |
    | Nazwa | Wprowadź nazwę **myVNet**. |
    | Region (Region) | Wybierz pozycję **(Stany Zjednoczone) Zachodnie stany USA 2**. |

6. Wybierz **kartę Adresy IP** lub przycisk **Dalej:** adresy IP w dolnej części strony.

7. Na karcie **Adresy IP** w obszarze Nazwa **podsieci wybierz** wartość **domyślną**.

8. W **okienku Edytowanie podsieci** w obszarze **Nazwa podsieci wprowadź** **myBackendSubnet.**

9. Wybierz pozycję **Zapisz**.

10. Wybierz **kartę** Zabezpieczenia lub przycisk **Dalej:** Zabezpieczenia w dolnej części strony.

11. Na karcie **Zabezpieczenia** w obszarze **BastionHost wybierz** pozycję **Włącz.**

12. Wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa bastionu | Wprowadź **myBastionHost**. |
    | Przestrzeń adresowa podsieci AzureBastionSubnet | Wprowadź **wartość 10.1.1.0/27.** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> Wprowadź **wartość myBastionIP w** **nazwie**. |

13. Wybierz **kartę Przeglądanie + tworzenie** lub niebieski przycisk **Przejrzyj i utwórz** w dolnej części strony.

14. Wybierz przycisk **Utwórz**.

## <a name="create-nat-gateway"></a>Tworzenie bramy translatora adresów sieciowych 

W tej sekcji utworzysz bramę nat dla łączności wychodzącej maszyn wirtualnych.

1. W polu wyszukiwania w górnej części portalu wprowadź wartość **brama nat.**

2. Wybierz **pozycję Bramy nat** w wynikach wyszukiwania.

3. Wybierz **pozycję + Utwórz.**

4. Na karcie **Podstawowe w** obszarze Tworzenie **bramy translatora adresów sieciowych (NAT)** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz **pozycję TutorLBmultiAVS-rg.** |
    | **Szczegóły wystąpienia** |   |
    | Nazwa bramy nat | Wprowadź **nazwę myNATgateway.** |
    | Region (Region) | Wybierz pozycję **(Stany Zjednoczone) Zachodnie stany USA 2**. |
    | Strefa dostępności | Wybierz pozycję **Brak**. |
    | Limit czasu bezczynności (minuty) | Wprowadź **wartość 15**. |

5. Wybierz **kartę Wychodzący adres IP** lub wybierz przycisk **Dalej: Wychodzący adres IP** w dolnej części strony.

6. Wybierz **pozycję Utwórz nowy publiczny adres IP** obok opcji Publiczne adresy **IP** na karcie **Wychodzący adres IP.**

7. Wprowadź **myPublicIP-nat w** **nazwie**.

8. Wybierz przycisk **OK**.

9. Wybierz **kartę Podsieć** lub wybierz przycisk **Dalej: Podsieć** w dolnej części strony.

10. Wybierz **pozycję myVNet** z menu rozwijanego w obszarze **Sieć wirtualna.**

11. Zaznacz pole wyboru obok podsieci **myBackendSubnet.**

12. Wybierz **kartę Przeglądanie + tworzenie** lub niebieski przycisk **Przejrzyj i utwórz** w dolnej części strony.

13. Wybierz przycisk **Utwórz**.

## <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

W tej sekcji utworzysz równoważenie obciążenia dla maszyn wirtualnych.

1. W polu wyszukiwania w górnej części portalu wprowadź load **balancer**.

2. W **wynikach wyszukiwania wybierz** pozycję Równoważenie obciążenia.

3. Wybierz **pozycję + Utwórz.**

4. Na karcie **Podstawowe w** obszarze **Tworzenie równoważenia obciążenia** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz **pozycję TutorLBmultiAVS-rg.** |
    | **Szczegóły wystąpienia** |   |
    | Nazwa | Wprowadź **myLoadBalancer**. |
    | Region (Region) | Wybierz pozycję **(Stany Zjednoczone) Zachodnie stany USA 2**. |
    | Typ | Pozostaw wartość domyślną **Publiczne.** |
    | SKU | Pozostaw wartość domyślną **Standardowa**. |
    | Warstwa | Pozostaw wartość **domyślną Regional (Region).** |
    | **Publiczny adres IP** |   |
    | Publiczny adres IP | Pozostaw wartość domyślną **Utwórz nowy.** |
    | Nazwa publicznego adresu IP | Wprowadź **myPublicIP-lb**. |
    | Strefa dostępności | Wybierz pozycję **Strefowo nadmiarowa**. |
    | Dodaj publiczny adres IPv6 | Pozostaw wartość **domyślną Nie.** |
    | Preferencja routingu | Pozostaw wartość domyślną **sieć firmy Microsoft**. |

5. Wybierz **kartę Przeglądanie + tworzenie** lub niebieski przycisk **Przejrzyj i** utwórz w dolnej części strony.

6. Wybierz przycisk **Utwórz**.

### <a name="configure-load-balancer-settings"></a>Konfigurowanie ustawień usługi równoważenia obciążenia

W tej sekcji utworzysz pulę zaplecza dla **myLoadBalancer.**

Utworzysz sondę kondycji do monitorowania protokołu **HTTP** **i portu 80.** Sonda kondycji będzie monitorować kondycję maszyn wirtualnych w puli zaplecza. 

Utworzysz regułę równoważenia obciążenia dla **portu 80** z wyłączonymi wychodzącymi sieciami SNAT. Utworzona wcześniej brama nat będzie obsługiwać łączność wychodzącą maszyn wirtualnych.

1. W polu wyszukiwania w górnej części portalu wprowadź tekst **Load balancer**.

2. W **wynikach wyszukiwania wybierz** pozycję Równoważenie obciążenia.

3. Wybierz **pozycję myLoadBalancer.**

4. W **programie myLoadBalancer** wybierz **pozycję Pule zaplecza w** **ustawieniach**.

5. Wybierz **pozycję + Dodaj** w **pulach zaplecza.**

6. W **dodaj pulę zaplecza**, wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **wartość myBackendPool.** |
    | Sieć wirtualna | Wybierz **pozycję myVNet.** |
    | Konfiguracja puli zaplecza | Pozostaw wartość domyślną **Nic (Karta sieciowa).** |
    | Wersja adresu IP | Pozostaw wartość domyślną **IPv4.** |

7. Wybierz pozycję **Dodaj**.

8. Wybierz **pozycję Sondy kondycji**.

9. Wybierz pozycję **+ Dodaj**.

10. W **dodaj sondę kondycji**, wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHTTPProbe.** |
    | Protokół | Wybierz **pozycję HTTP**. |
    | Port | Pozostaw wartość **domyślną 80**. |
    | Ścieżka | Pozostaw wartość domyślną **/** . |
    | Interwał | Pozostaw wartość **domyślną 5** sekund. |
    | Próg złej kondycji | Pozostaw wartość domyślną **2** kolejnych niepowodzeń. |

11. Wybierz pozycję **Dodaj**.

12. Wybierz **pozycję Reguły równoważenia obciążenia.** 

13. Wybierz pozycję **+ Dodaj**.

14. Wprowadź lub wybierz następujące informacje w dodaj **regułę równoważenia obciążenia:**

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myHTTPRule**. |
    | Wersja adresu IP | Pozostaw wartość domyślną **IPv4.** |
    | Adres IP frontony | Wybierz **pozycję LoadBalancerFrontEnd.** |
    | Protokół | Wybierz wartość domyślną **TCP**. |
    | Port | Wprowadź **wartość 80**. |
    | Port zaplecza | Wprowadź **wartość 80**. |
    | Pula zaplecza | Wybierz **pozycję myBackendPool.** |
    | Sonda kondycji | Wybierz **pozycję myHTTPProbe.** |
    | Trwałość sesji | Pozostaw wartość domyślną **Brak.** |
    | Limit czasu bezczynności (minuty) | Zmień suwak na **15**. |
    | Resetowanie protokołu TCP | Wybierz pozycję **Włączone**. |
    | Pływający adres IP | Pozostaw wartość domyślną **Wyłączone.** |
    | Translator źródłowych adresów sieciowych (SNAT) dla ruchu wychodzącego | Pozostaw wartość domyślną **(Zalecane) Użyj** reguł ruchu wychodzącego, aby zapewnić członkom puli zaplecza dostęp do Internetu. |

5. Wybierz pozycję **Dodaj**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W tej sekcji utworzysz dwie grupy dostępności z dwiema maszynami wirtualnymi na grupę. Te maszyny zostaną dodane do puli zaplecza równoważenia obciążenia podczas tworzenia. 

### <a name="create-first-set-of-vms"></a>Tworzenie pierwszego zestawu maszyn wirtualnych

1. Wybierz **pozycję + Utwórz zasób** w lewej górnej części portalu.

2. W **nowej opcji** wybierz pozycję **Obliczeniowa**  >  **maszyna wirtualna.**

3. Na karcie **Podstawowe** w **obszarze Tworzenie maszyny wirtualnej** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz swoją subskrypcję |
    | Grupa zasobów | Wybierz **pozycję TutorLBmultiAVS-rg.** |
    | **Szczegóły wystąpienia** |   |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM1.** |
    | Region (Region) | Wybierz pozycję **(Stany Zjednoczone) Zachodnie stany USA 2**. |
    | Opcje dostępności | Wybierz **pozycję Zestaw dostępności.** |
    | Zestaw dostępności | Wybierz pozycję **Utwórz nowy**. </br> Wprowadź **wartość myAvailabilitySet1 w** **nazwie**. </br> Wybierz przycisk **OK**. |
    | Obraz | Wybierz **pozycję Windows Server 2019 Datacenter - Gen1.** |
    | Wystąpienie usługi Azure Spot | Pozostaw wartość domyślną niezaznaczone. |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej. |
    | **Konto administratora** |   |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika. |
    | Hasło | Wprowadź hasło. |
    | **Reguły portów przychodzących** |   |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |

4. Wybierz **kartę Sieć** lub wybierz przycisk **Dalej: Dyski,** a następnie przycisk **Dalej: Sieć** w dolnej części strony.

5. Na karcie **Sieć** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Interfejs sieciowy** |   |
    | Sieć wirtualna | Wybierz **pozycję myVNet.** |
    | Podsieć | Wybierz **pozycję myBackendSubnet.** |
    | Publiczny adres IP | Wybierz pozycję **Brak**. |
    | Sieciowa grupa zabezpieczeń kart sieciowych | Wybierz pozycję **Zaawansowane**. |
    | Konfigurowanie sieciowej grupy zabezpieczeń | Wybierz pozycję **Utwórz nowy**. </br> W **nazwa**, wprowadź **myNSG**. </br> Wybierz **pozycję +Dodaj regułę ruchu przychodzącego** w **regułach ruchu przychodzącego.** </br> Wybierz **pozycję HTTP** dla opcji **Usługa.** </br> Wprowadź **myHTTPrule w** **nazwie**. </br> Wybierz pozycję **Dodaj**. </br> Wybierz przycisk **OK**. | 
    | **Równoważenie obciążenia** |   |
    | Umieścić tę maszynę wirtualną za istniejącym rozwiązaniem do równoważenia obciążenia? | zaznacz pole wyboru. |
    | **Ustawienia równoważenia obciążenia** |   |
    | Opcje równoważenia obciążenia | Wybierz **pozycję Azure Load Balancer**. |
    | Wybieranie równoważenia obciążenia | Wybierz **pozycję myLoadBalancer**. |
    | Wybieranie puli zaplecza | Wybierz **pozycję myBackendPool.** |

6. Wybierz **kartę Przeglądanie + tworzenie** lub niebieski przycisk **Przejrzyj i** utwórz znajdujący się u dołu strony.

7. Wybierz przycisk **Utwórz**.

8. Powtórz kroki od 1 do 7, aby utworzyć drugą maszynę wirtualną zestawu. Zastąp ustawienia maszyny wirtualnej następującymi informacjami:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myVM2.** |
    | Zestaw dostępności | Wybierz **pozycję myAvailabilitySet1.** |
    | Virtual Network | Wybierz **pozycję myVNet.** |
    | Podsieć | Wybierz **pozycję myBackendSubnet.** |
    | Publiczny adres IP | Wybierz pozycję **Brak**. |
    | Sieciowa grupa zabezpieczeń | Wybierz **pozycję myNSG**. |
    | Opcje równoważenia obciążenia | Wybierz **pozycję Azure Load Balancer**. |
    | Wybieranie równoważenia obciążenia | Wybierz **pozycję myLoadBalancer.** |
    | Wybieranie puli zaplecza | Wybierz **pozycję myBackendPool.** |

### <a name="create-second-set-of-vms"></a>Tworzenie drugiego zestawu maszyn wirtualnych

1. Wybierz **pozycję + Utwórz zasób** w lewej górnej części portalu.

2. W **nowej opcji** wybierz pozycję **Obliczeniowa**  >  **maszyna wirtualna.**

3. Na karcie **Podstawowe** w **obszarze Tworzenie maszyny wirtualnej** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz swoją subskrypcję |
    | Grupa zasobów | Wybierz **pozycję TutorLBmultiAVS-rg.** |
    | **Szczegóły wystąpienia** |   |
    | Nazwa maszyny wirtualnej | Wprowadź **myVM3.** |
    | Region (Region) | Wybierz pozycję **(Stany Zjednoczone) Zachodnie stany USA 2**. |
    | Opcje dostępności | Wybierz **pozycję Zestaw dostępności.** |
    | Zestaw dostępności | Wybierz pozycję **Utwórz nowy**. </br> Wprowadź **myAvailabilitySet2 w** **nazwie**. </br> Wybierz przycisk **OK**. |
    | Obraz | Wybierz **pozycję Windows Server 2019 Datacenter — Gen1.** |
    | Wystąpienie usługi Azure Spot | Pozostaw wartość domyślną niezaznaczone. |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej. |
    | **Konto administratora** |   |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika. |
    | Hasło | Wprowadź hasło. |
    | **Reguły portów przychodzących** |   |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |

4. Wybierz **kartę Sieć** lub wybierz przycisk **Dalej: Dyski,** a następnie przycisk **Dalej: Sieć** w dolnej części strony.

5. Na karcie **Sieć** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Interfejs sieciowy** |   |
    | Sieć wirtualna | Wybierz **pozycję myVNet.** |
    | Podsieć | Wybierz **pozycję myBackendSubnet.** |
    | Publiczny adres IP | Wybierz pozycję **Brak**. |
    | Sieciowa grupa zabezpieczeń kart sieciowych | Wybierz pozycję **Zaawansowane**. |
    | Konfigurowanie sieciowej grupy zabezpieczeń | Wybierz **pozycję myNSG**. | 
    | **Równoważenie obciążenia** |   |
    | Umieścić tę maszynę wirtualną za istniejącym rozwiązaniem do równoważenia obciążenia? | zaznacz pole wyboru. |
    | **Ustawienia równoważenia obciążenia** |   |
    | Opcje równoważenia obciążenia | Wybierz **pozycję Azure Load Balancer**. |
    | Wybieranie równoważenia obciążenia | Wybierz **pozycję myLoadBalancer.** |
    | Wybieranie puli zaplecza | Wybierz **pozycję myBackendPool.** |

6. Wybierz **kartę Przeglądanie + tworzenie** lub niebieski przycisk **Przejrzyj i** utwórz znajdujący się u dołu strony.

7. Wybierz przycisk **Utwórz**.

8. Powtórz kroki od 1 do 7, aby utworzyć drugą maszynę wirtualną zestawu. Zastąp ustawienia maszyny wirtualnej następującymi informacjami:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź **myVM4.** |
    | Zestaw dostępności | Wybierz **pozycję myAvailabilitySet2.** |
    | Virtual Network | Wybierz **pozycję myVNet.** |
    | Sieciowa grupa zabezpieczeń | Wybierz **pozycję myNSG**. |
    | Opcje równoważenia obciążenia | Wybierz **pozycję Azure Load Balancer**. |
    | Wybieranie równoważenia obciążenia | Wybierz **pozycję myLoadBalancer.** |
    | Wybieranie puli zaplecza | Wybierz **pozycję myBackendPool.** |

## <a name="install-iis"></a>Instalowanie usług IIS

W tej sekcji użyjesz utworzonego wcześniej Azure Bastion, aby nawiązać połączenie z maszynami wirtualnymi i zainstalować usługi IIS.

1. W polu wyszukiwania w górnej części portalu wprowadź wartość **Maszyna wirtualna**.

2. W **wynikach** wyszukiwania wybierz pozycję Maszyny wirtualne.

3. Wybierz **pozycję myVM1.**

4. Na stronie **Przegląd** myVM1 wybierz pozycję **Połącz z**  >  **bastionem**.

5. Wybierz **pozycję Użyj bastionu.**

6. Wprowadź nazwę **użytkownika** i **hasło** utworzone podczas tworzenia maszyny wirtualnej.

7. Wybierz pozycję **Połącz**.

7. Na pulpicie serwera przejdź do narzędzia administracyjne systemu **Windows Windows PowerShell**  >  .

8. W oknie programu PowerShell uruchom następujące polecenia, aby:

    * Instalowanie serwera usług IIS
    * Usuwanie domyślnego iisstart.htm plików
    * Dodaj nowy iisstart.htm, który wyświetla nazwę maszyny wirtualnej:

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Zamknij sesję bastionu przy użyciu **myVM1.**

9. Powtórz kroki od 1 do 8 dla **myVM2,** **myVM3** i **myVM4.**

## <a name="test-the-load-balancer"></a>Testowanie modułu równoważenia obciążenia

W tej sekcji odkryjesz publiczny adres IP usługi równoważenia obciążenia. Użyjesz adresu IP, aby przetestować działanie usługi równoważenia obciążenia.

1. W polu wyszukiwania w górnej części portalu wprowadź **publiczny adres IP**.

2. W **wynikach wyszukiwania wybierz** pozycję Publiczne adresy IP.

3. Wybierz **pozycję myPublicIP-lb.**

4. Zanotuj publiczny adres IP wymieniony w **adresie IP** na **stronie Przegląd** **w myPublicIP-lb:**

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="Znajdź publiczny adres IP usługi równoważenia obciążenia." border="true":::

5. Otwórz przeglądarkę internetową i wprowadź publiczny adres IP na pasku adresu:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="Przetestuj usługę równoważenia obciążenia za pomocą przeglądarki internetowej." border="true":::

6. Wybierz pozycję odśwież w przeglądarce, aby wyświetlić ruch równoważony do innych maszyn wirtualnych w puli zaplecza.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz w dalszym ciągu korzystać z tej aplikacji, usuń równoważenie obciążenia i zasoby obsługi, aby wykonać następujące czynności:

1. W polu wyszukiwania w górnej części portalu wprowadź **grupę zasobów**.

2. W **wynikach** wyszukiwania wybierz pozycję Grupy zasobów.

3. Wybierz **pozycję TutorLBmultiAVS-rg.**

4. Na stronie przeglądu aplikacji **TutorLBmultiAVS-rg** wybierz **pozycję Usuń grupę zasobów.**

5. Wprowadź **tutorLBmultiAVS-rg** w **wpisz nazwę grupy zasobów**.

6. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

* Utworzono sieć wirtualną i Azure Bastion hosta.
* Utworzono usługę Azure usługa Load Balancer w warstwie Standardowa.
* Utworzono dwa zestawy dostępności z dwiema maszynami wirtualnymi na zestaw.
* Zainstalowano program IIS i przetestowano usługę równoważenia obciążenia.

Aby dowiedzieć się, jak utworzyć projekt między regionami, należy przejść do następnego Azure Load Balancer:
> [!div class="nextstepaction"]
> [Tworzenie między regionami równoważenia obciążenia](tutorial-cross-region-portal.md)

