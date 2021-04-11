---
title: Kierowanie ruchu sieciowego — samouczek — Azure Portal
titlesuffix: Azure Virtual Network
description: W tym samouczku dowiesz się, jak kierować ruchem sieciowym za pomocą tabeli tras z użyciem witryny Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: 7da59e996ec37d3653dbde68c5f56caa9e8261ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061914"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Samouczek: kierowanie ruchem sieciowym za pomocą tabeli tras z użyciem witryny Azure Portal

Platforma Azure domyślnie kieruje ruchem między wszystkimi podsieciami w sieci wirtualnej. Możesz tworzyć własne trasy zastępujące domyślne trasy platformy Azure. Trasy niestandardowe są przydatne, gdy na przykład chcesz kierować ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego (urządzenie WUS). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie urządzenia NVA, które kieruje ruchem
> * Tworzenie tabeli tras
> * Tworzenie trasy
> * Kojarzenie tabeli tras z podsiecią
> * Wdrażanie maszyn wirtualnych w różnych podsieciach
> * Kierowanie ruchem z jednej podsieci do drugiej za pomocą urządzenia NVA

Ten samouczek używa [Azure Portal](https://portal.azure.com). Możesz również użyć [interfejsu wiersza polecenia platformy Azure](tutorial-create-route-table-cli.md) lub [Azure PowerShell](tutorial-create-route-table-powershell.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem musisz mieć konto platformy Azure z aktywną subskrypcją. Jeśli go nie masz, możesz [utworzyć konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W menu witryny Azure Portal wybierz pozycję **Utwórz zasób**. W portalu Azure Marketplace wybierz pozycję **Sieć**  >  **Sieć wirtualna** lub Wyszukaj **Virtual Network** w polu wyszukiwania.

2. Wybierz przycisk **Utwórz**.

2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę **zasobu**. </br> Wybierz przycisk **OK**. |
    | Nazwa | Wpisz **myVirtualNetwork**. |
    | Lokalizacja | Wybierz pozycję **(US) Wschodnie stany USA**.|

3. Wybierz kartę **adresy IP** lub kliknij przycisk **Dalej: adresy IP** w dolnej części strony.

4. W **obszarze przestrzeń adresowa IPv4** wybierz istniejącą przestrzeń adresową i zmień ją na **10.0.0.0/16**.

4. Wybierz pozycję **+ Dodaj podsieć**, a następnie wprowadź **Public** dla **nazwy podsieci** i **10.0.0.0/24** dla **zakresu adresów podsieci**.

5. Wybierz pozycję **Dodaj**.

6. Wybierz pozycję **+ Dodaj podsieć**, a następnie wprowadź **Private** dla **nazwy podsieci** i **10.0.1.0/24** dla **zakresu adresów podsieci**.

7. Wybierz pozycję **Dodaj**.

8. Wybierz pozycję **+ Dodaj podsieć**, a następnie wprowadź **strefę DMZ** dla **nazwy podsieci** i **10.0.2.0/24** dla **zakresu adresów podsieci**.

9. Wybierz pozycję **Dodaj**.

10. Wybierz kartę **zabezpieczenia** lub wybierz przycisk **Dalej: zabezpieczenia** w dolnej części strony.

11. W obszarze **BastionHost** wybierz pozycję **enable (Włącz**). Wprowadź następujące informacje:

    | Ustawienie            | Wartość                      |
    |--------------------|----------------------------|
    | Nazwa bastionu | Wprowadź **myBastionHost** |
    | Przestrzeń adresowa AzureBastionSubnet | Wprowadź **10.0.3.0/24** |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy**. </br> W obszarze **Nazwa** wprowadź **myBastionIP**. </br> Wybierz przycisk **OK**. |

12. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk **Recenzja + tworzenie** .

13. Wybierz przycisk **Utwórz**.

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Wirtualne urządzenia sieciowe (urządzeń WUS) są maszynami wirtualnymi, które ułatwiają funkcje sieciowe, takie jak optymalizacja routingu i zapory. W tym samouczku przyjęto założenie, że używasz **systemu Windows Server 2019 Datacenter**. Możesz wybrać inny system operacyjny.

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVMNVA** |
    | Region (Region) | Wybierz **(US) Wschodnie stany USA** |
    | Opcje dostępności | Nie wybieraj **nadmiarowości infrastruktury** |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |
    | **Reguły portów ruchu przychodzącego** |    |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
    |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | Wybierz pozycję **myVirtualNetwork**. |
    | Podsieć | Wybierz **strefę DMZ** |
    | Publiczny adres IP | Nie zaznaczaj **niczego** |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **podstawowa**|
    | Sieć publicznych portów przychodzących | Wybierz pozycję **Brak**. |
   
5. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. W polu wyszukiwania wprowadź **tabelę tras**. Gdy **tabela tras** zostanie wyświetlona w wynikach wyszukiwania, wybierz ją.

3. Na stronie **tabela tras** wybierz pozycję **Utwórz**.

4. W obszarze **Utwórz tabelę tras** na karcie **podstawy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. |
    | **Szczegóły wystąpienia** |    |
    | Region (Region) | Wybierz pozycję **Wschodnie stany USA**. |
    | Nazwa | Wpisz **myRouteTablePublic**. |
    | Propaguj trasy bramy | Wybierz pozycję **Tak**. |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="Utwórz tabelę tras, Azure Portal." border="true":::

5. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.

## <a name="create-a-route"></a>Tworzenie trasy

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać tabelą tras. Wyszukaj i wybierz pozycję **tabele tras**.

2. Wybierz nazwę tabeli tras **myRouteTablePublic**.

3. Na stronie **myRouteTablePublic** w sekcji **Ustawienia** wybierz pozycję **trasy**.

4. Na stronie trasy wybierz przycisk **+ Dodaj** .

5. W obszarze **Dodawanie trasy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa trasy | Wprowadź **ToPrivateSubnet** |
    | Prefiks adresu | Wprowadź **10.0.1.0/24** (zakres adresów podsieci **prywatnej** utworzonej wcześniej) |
    | Typ następnego przeskoku | Wybierz pozycję **Urządzenie wirtualne**. |
    | Adres następnego skoku | Wprowadź **10.0.2.4** (adres znajdujący się w zakresie adresów podsieci **DMZ** ) |

6. Wybierz przycisk **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać siecią wirtualną. Wyszukaj i wybierz pozycję **sieci wirtualne**.

2. Wybierz nazwę sieci wirtualnej **myVirtualNetwork**.

3. Na stronie **myVirtualNetwork** w sekcji **Ustawienia** wybierz pozycję **podsieci**.

4. Na liście podsieć sieci wirtualnej wybierz pozycję **publiczny**.

5. W **tabeli tras** Wybierz utworzoną tabelę tras **myRouteTablePublic**. 

6. Wybierz pozycję **Zapisz** , aby skojarzyć tabelę tras z podsiecią **publiczną** .

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="Skojarz tabelę tras, listę podsieci, sieć wirtualną, Azure Portal." border="true":::

## <a name="turn-on-ip-forwarding"></a>Włączanie przekazywania dalej adresu IP

Następnie Włącz przekazywanie IP dla nowej maszyny wirtualnej urządzenie WUS, **myVMNVA**. Gdy platforma Azure wysyła ruch sieciowy do **myVMNVA**, jeśli ruch jest przeznaczony dla innego adresu IP, przekazywanie IP wysyła ruch do odpowiedniej lokalizacji.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać maszyną wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

2. Wybierz nazwę maszyny wirtualnej **myVMNVA**.

3. Na stronie Przegląd **myVMNVA** w obszarze **Ustawienia** wybierz pozycję **Sieć**.

4. Na stronie **Sieć** **myVMNVA** wybierz interfejs sieciowy obok **interfejsu sieciowego**.  Nazwa interfejsu rozpocznie się od **myvmnva**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="Sieć, maszyna wirtualna w sieci (urządzenie WUS), maszyny wirtualne (VM), Azure Portal" border="true":::

5. Na stronie Przegląd interfejsu sieciowego w obszarze **Ustawienia** wybierz pozycję **konfiguracje adresów IP**.

6. Na stronie **konfiguracje adresów IP** ustaw opcję **przekazywanie adresów IP** na **włączone**, a następnie wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="Włączanie przekazywania dalej adresu IP" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>Tworzenie publicznych i prywatnych maszyn wirtualnych

Utwórz publiczną i prywatną maszynę wirtualną w sieci wirtualnej. Później użyjesz ich, aby sprawdzić, jak platforma Azure kieruje ruch podsieci **publicznej** do podsieci **prywatnej** za pośrednictwem urządzenia WUS.


### <a name="public-vm"></a>Publiczna maszyna wirtualna

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVMPublic** |
    | Region (Region) | Wybierz **(US) Wschodnie stany USA** |
    | Opcje dostępności | Nie wybieraj **nadmiarowości infrastruktury** |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |
    | **Reguły portów ruchu przychodzącego** |    |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
    |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | Wybierz pozycję **myVirtualNetwork**. |
    | Podsieć | Wybierz pozycję **publiczny** |
    | Publiczny adres IP | Nie zaznaczaj **niczego** |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **podstawowa**|
    | Sieć publicznych portów przychodzących | Wybierz pozycję **Brak**. |
   
5. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

### <a name="private-vm"></a>Prywatna maszyna wirtualna

1. W lewym górnym rogu portalu wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**. 
   
2. W obszarze **Utwórz maszynę wirtualną** wpisz lub wybierz wartości z karty **podstawowe** :

    | Ustawienie | Wartość                                          |
    |-----------------------|----------------------------------|
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję platformy Azure |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName** |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź **myVMPrivate** |
    | Region (Region) | Wybierz **(US) Wschodnie stany USA** |
    | Opcje dostępności | Nie wybieraj **nadmiarowości infrastruktury** |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter** |
    | Wystąpienie usługi Azure Spot | Wybierz pozycję **nie** |
    | Rozmiar | Wybierz rozmiar maszyny wirtualnej lub ustaw ustawienie domyślne |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika |
    | Hasło | Wprowadź hasło |
    | Potwierdź hasło | Ponownie wprowadź hasło |
    | **Reguły portów ruchu przychodzącego** |    |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |
    |

3. Wybierz kartę **Sieć** lub wybierz pozycję **Dalej: Dyski**, a następnie pozycję **Dalej: Sieć**.
  
4. Na karcie Sieć wybierz lub wprowadź:

    | Ustawienie | Wartość |
    |-|-|
    | **Interfejs sieciowy** |  |
    | Sieć wirtualna | Wybierz pozycję **myVirtualNetwork**. |
    | Podsieć | Wybierz pozycję **prywatny** |
    | Publiczny adres IP | Nie zaznaczaj **niczego** |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **podstawowa**|
    | Sieć publicznych portów przychodzących | Wybierz pozycję **Brak**. |
   
5. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.
  
6. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

### <a name="sign-in-to-private-vm"></a>Zaloguj się do prywatnej maszyny wirtualnej

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać PRYWATNą maszyną wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

2. Wybierz nazwę prywatnej maszyny wirtualnej **myVmPrivate**.

3. Na pasku menu maszyny wirtualnej wybierz pozycję **Połącz**, a następnie wybierz pozycję **bastionu**.

4. Na stronie **Połącz** wybierz przycisk **bastionu Użyj** niebieska.

5. Na stronie **bastionu** wprowadź wcześniej nazwę użytkownika i hasło, które zostały utworzone dla maszyny wirtualnej.

6. Wybierz pozycję **Połącz**.

### <a name="configure-firewall"></a>Konfigurowanie zapory

W kolejnym kroku przetestujesz routing przy użyciu narzędzia śledzenia trasy. Narzędzie śledzenia trasy używa protokołu ICMP (Internet Control Message Protocol), którego ruch jest domyślnie blokowany przez Zaporę systemu Windows. 

Włącz protokół ICMP za pomocą Zapory systemu Windows.

1. W bastionu Connection **myVMPrivate** Otwórz program PowerShell z uprawnieniami administracyjnymi.

2. Wprowadź następujące polecenie:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Będziesz używać trasy śledzenia do testowania routingu w tym samouczku. W środowiskach produkcyjnych nie zalecamy zezwalania na protokół ICMP przez Zaporę systemu Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Włączanie przekazywania adresów IP w ramach myVMNVA

[Włączono przekazywanie adresu IP](#turn-on-ip-forwarding) dla interfejsu sieciowego maszyny wirtualnej przy użyciu platformy Azure. System operacyjny maszyny wirtualnej ma również przekazywać ruch sieciowy. 

Włącz przekazywanie IP dla **myVMNVA** za pomocą tych poleceń.

1. Z poziomu programu PowerShell na maszynie wirtualnej **myVMPrivate** Otwórz pulpit zdalny na maszynie wirtualnej **myVMNVA** :

    ```powershell
    mstsc /v:myvmnva
    ```

2. W programie PowerShell na maszynie wirtualnej **myVMNVA** wprowadź następujące polecenie, aby włączyć przekazywanie adresów IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. Uruchom ponownie **myVMNVA**.

    ```powershell
    Restart-Computer
    ```

4. Po ponownym uruchomieniu **myVMNVA** utwórz sesję pulpitu zdalnego do **myVMPublic**. 
    
    Mimo że nadal nawiązano połączenie z usługą **myVMPrivate**, Otwórz program PowerShell i uruchom następujące polecenie:

    ```powershell
    mstsc /v:myvmpublic
    ```
5. Na pulpicie zdalnym **myVMPublic** Otwórz program PowerShell.

6. Włącz protokół ICMP za pomocą Zapory systemu Windows przez wprowadzenie tego polecenia:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testowanie routingu ruchu sieciowego

Najpierw Przetestujmy Routing ruchu sieciowego z **myVMPublic** do **myVMPrivate**.

1. W programie PowerShell w systemie **myVMPublic** wprowadź następujące polecenie:

    ```powershell
    tracert myvmprivate
    ```

    Odpowiedź jest podobna do poniższego przykładu:

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * Pierwszy przeskok to 10.0.2.4, który jest **myVMNVA** prywatnym adresem IP. 

    * Drugi przeskok to prywatny adres IP **myVMPrivate**: 10.0.1.4. 

    Wcześniej dodano trasę do tabeli tras **myRouteTablePublic** i skojarzono ją z podsiecią *publiczną*. Platforma Azure wysłała ruch przez urządzenie WUS, a nie bezpośrednio do podsieci *prywatnej* .

2. Zamknij sesję pulpitu zdalnego, aby **myVMPublic**, co pozostawia połączenie z **myVMPrivate**.

3. Otwórz program PowerShell w systemie **myVMPrivate**, wprowadź następujące polecenie:

    ```powershell
    tracert myvmpublic
    ```

    To polecenie testuje Routing ruchu sieciowego z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic* . Odpowiedź jest podobna do poniższego przykładu:

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Możesz zobaczyć, że platforma Azure kieruje ruch bezpośrednio z *myVMPrivate* do *myVMPublic*. Domyślnie platforma Azure kieruje ruch bezpośrednio między podsieciami.

4. Zamknij sesję bastionu do **myVMPrivate**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli grupa zasobów nie jest już wymagana, Usuń grupę **zasobów** i wszystkie zawarte w niej zasoby:

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać grupą zasobów. Wyszukaj i wybierz pozycję **grupy zasobów**.

2. Wybierz **nazwę grupy zasobów**.

3. Wybierz pozycję **Usuń grupę zasobów**.

4. W oknie dialogowym **potwierdzenia wprowadź** wartość w polu **Nazwa grupy zasobów**, a następnie wybierz pozycję **Usuń**. 


## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

* Utworzono tabelę tras i skojarzono ją z podsiecią.
* Utworzono prostą urządzenie WUS, która skierowała ruch z podsieci publicznej do podsieci prywatnej. 

Możesz wdrożyć różne wstępnie skonfigurowane urządzeń WUS z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), które udostępniają wiele przydatnych funkcji sieciowych. 

Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Aby filtrować ruch sieciowy w sieci wirtualnej, zobacz:
> [!div class="nextstepaction"]
> [Samouczek: filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń przy użyciu Azure Portal](tutorial-filter-network-traffic.md)
