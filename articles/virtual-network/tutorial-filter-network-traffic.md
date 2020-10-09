---
title: Filtrowanie ruchu sieciowego — samouczek — Azure Portal
titlesuffix: Azure Virtual Network
description: W tym samouczku dowiesz się, jak filtrować ruch sieciowy w podsieci z grupą zabezpieczeń sieci przy użyciu Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: b5a136ae05b3cd410ca252b6d5a1df443aff6f7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75350137"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Samouczek: filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń przy użyciu Azure Portal

Ruch sieciowy przychodzący do podsieci sieci wirtualnej i wychodzący z niej możesz filtrować za pomocą sieciowej grupy zabezpieczeń. Sieciowe grupy zabezpieczeń zawierają reguły zabezpieczeń, które filtrują ruch sieciowy według adresów IP, portów i protokołów. Reguły zabezpieczeń są stosowane do zasobów wdrożonych w podsieci. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieciowej grupy zabezpieczeń i reguł zabezpieczeń
> * Tworzenie sieci wirtualnej i kojarzenie sieciowej grupy zabezpieczeń z podsiecią
> * Wdrażanie maszyn wirtualnych w podsieci
> * Testowanie filtrów ruchu

Jeśli chcesz, możesz wykonać ten samouczek przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-filter-network-traffic-cli.md) lub [programu PowerShell](tutorial-filter-network-traffic-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. 
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie                 | Wartość                                              |
    | ---                     | ---                                                |
    | Nazwa                    | myVirtualNetwork                                   |
    | Przestrzeń adresowa           | 10.0.0.0/16                                        |
    | Subskrypcja            | Wybierz subskrypcję.                          |
    | Grupa zasobów          | Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę *myResourceGroup*. |
    | Lokalizacja                | Wybierz pozycję **Wschodnie stany USA**.                                |
    | Nazwa podsieci            | mySubnet                                           |
    | Zakres adresów podsieci: 10.41.0.0/24  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>Tworzenie grup zabezpieczeń aplikacji

Grupa zabezpieczeń aplikacji umożliwia grupowanie serwerów o podobnych funkcjach, np. serwerów internetowych.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. 
2. W polu **Wyszukaj w witrynie Marketplace** wpisz *Grupa zabezpieczeń aplikacji*. Gdy **Grupa zabezpieczeń aplikacji** pojawi się w wynikach wyszukiwania, wybierz ją, wybierz opcję **Grupa zabezpieczeń aplikacji** ponownie w pozycji **Wszystko**, a następnie wybierz opcję **Utwórz**.
3. Wprowadź lub wybierz następujące informacje, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie        | Wartość                                                         |
    | ---            | ---                                                           |
    | Nazwa           | myAsgWebServers                                               |
    | Subskrypcja   | Wybierz subskrypcję.                                     |
    | Grupa zasobów | Wybierz pozycję **Użyj istniejącej** , a następnie wybierz pozycję Moja  **resourceName**. |
    | Lokalizacja       | Wschodnie stany USA                                                       |

4. Ponownie wykonaj krok 3, używając następujących wartości:

    | Ustawienie        | Wartość                                                         |
    | ---            | ---                                                           |
    | Nazwa           | myAsgMgmtServers                                              |
    | Subskrypcja   | Wybierz subskrypcję.                                     |
    | Grupa zasobów | Wybierz pozycję **Użyj istniejącej** , a następnie wybierz pozycję Moja  **resourceName**. |
    | Lokalizacja       | Wschodnie stany USA                                                       |

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. 
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieciowa grupa zabezpieczeń**.
3. Wprowadź lub wybierz następujące informacje, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa|myNsg|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów | Wybierz pozycję **Użyj istniejącej** i wybierz grupę *myResourceGroup*.|
    |Lokalizacja|Wschodnie stany USA|

## <a name="associate-network-security-group-to-subnet"></a>Przypisywanie sieciowej grupy zabezpieczeń do podsieci

1. W polu *Szukaj zasobów, usług i dokumentów* w górnej części portalu rozpocznij wpisywanie ciągu *myNsg*. Gdy pozycja **myNsg** pojawi się w wynikach wyszukiwania, wybierz ją.
2. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci**, a następnie wybierz pozycję **+ Przypisz**, jak pokazano na poniższym obrazie:

    ![Kojarzenie sieciowej grupy zabezpieczeń z podsiecią](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. W obszarze **Skojarz podsieć** wybierz pozycję **Sieć wirtualna**, a następnie wybierz pozycję **myVirtualNetwork**. Wybierz opcję **Podsieć**, wybierz **mySubnet**, a następnie wybierz przycisk **OK**.

## <a name="create-security-rules"></a>Tworzenie reguł zabezpieczeń

1. W sekcji **USTAWIENIA** wybierz **Reguły zabezpieczeń dla ruchu przychodzącego**, a następnie wybierz przycisk **+ Dodaj**, jak pokazano na poniższej ilustracji:

    ![Dodawanie reguły zabezpieczeń ruchu przychodzącego](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. Utwórz regułę zabezpieczeń, która zezwala na stosowanie portów 80 i 443 do grupy zabezpieczeń aplikacji **myAsgWebServers**. W obszarze **Dodaj regułę zabezpieczeń ruchu przychodzącego** wprowadź lub wybierz następujące wartości, zaakceptuj pozostałe wartości domyślne, a następnie wybierz opcję **Dodaj**:

    | Ustawienie                 | Wartość                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Element docelowy             | Wybierz opcję **Grupa zabezpieczeń aplikacji**, a następnie wybierz opcję **myAsgWebServers** w pozycji **Grupa zabezpieczeń aplikacji**.  |
    | Zakresy portów docelowych | Wprowadź wartości 80,443                                                                                                    |
    | Protokół                | Wybierz pozycję TCP                                                                                                      |
    | Nazwa                    | Allow-Web-All                                                                                                   |

3. Ponownie wykonaj krok 2, używając następujących wartości:

    | Ustawienie                 | Wartość                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Element docelowy             | Wybierz opcję **Grupa zabezpieczeń aplikacji**, a następnie wybierz opcję **myAsgMgmtServers** w pozycji **Grupa zabezpieczeń aplikacji**. |
    | Zakresy portów docelowych | Wprowadź 3389                                                                                                      |
    | Protokół                | Wybierz pozycję TCP                                                                                                      |
    | Priorytet                | Wprowadź wartość 110                                                                                                       |
    | Nazwa                    | Allow-RDP-All                                                                                                   |

    W tym samouczku protokół RDP (port 3389) jest połączony z Internetem dla maszyny wirtualnej przydzielonej do grupy zabezpieczeń aplikacji *myAsgMgmtServers*. W przypadku środowisk produkcyjnych zamiast uwidaczniania portu 3389 w Internecie zaleca się połączenie z zasobami platformy Azure, którymi chcesz zarządzać, przy użyciu sieci VPN lub prywatnego połączenia sieciowego.

Po zakończeniu kroków 1–3 sprawdź utworzone reguły. Lista powinna wyglądać tak jak na poniższej ilustracji:

![Reguły zabezpieczeń](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**. 
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**.
3. Wprowadź lub wybierz poniższe informacje, a następnie zaakceptuj wartości domyślne dla pozostałych ustawień:

    |Ustawienie|Wartość|
    |---|---|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.|
    |Nazwa|myVmWeb|
    |Lokalizacja| Wybierz pozycję **Wschodnie stany USA**.|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|

   

4. Wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz**.
5. W obszarze **Sieć**wybierz następujące wartości i zaakceptuj pozostałe ustawienia domyślne:

    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna |Wybierz pozycję **myVirtualNetwork**.|
    |Grupa zabezpieczeń sieci karty sieciowej |Wybierz pozycję **Brak**.|
  

6. Wybierz pozycję **Recenzja + Utwórz** w lewym górnym rogu, a następnie wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie maszyny wirtualnej.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Wykonaj ponownie kroki od 1 do 6, ale w kroku 3 nadaj maszynie wirtualnej nazwę *myVmMgmt*. Wdrożenie maszyny wirtualnej potrwa kilka minut. Nie przechodź do kolejnego kroku, dopóki maszyna wirtualna nie zostanie wdrożona.

## <a name="associate-network-interfaces-to-an-asg"></a>Kojarzenie interfejsów sieciowych z grupą zabezpieczeń aplikacji

Podczas tworzenia maszyn wirtualnych przez portal dla każdej maszyny wirtualnej został utworzony interfejs sieciowy, który następnie został połączony z maszyną wirtualną. Dodaj interfejs sieciowy dla każdej maszyny wirtualnej do jednej z wcześniej utworzonych grup zabezpieczeń aplikacji:

1. W polu *Szukaj zasobów, usług i dokumentów* w górnej części portalu rozpocznij wpisywanie ciągu *myVmWeb*. Gdy maszyna wirtualna **myVmWeb** pojawi się w wynikach wyszukiwania, wybierz ją.
2. W obszarze **USTAWIENIA** wybierz pozycję **Sieć**.  Wybierz opcję **Skonfiguruj grupy zabezpieczeń aplikacji**, wybierz opcję **myAsgWebServers** w pozycji **Grupy zabezpieczeń aplikacji**, a następnie wybierz opcję **Zapisz**, jak pokazano na następującej ilustracji:

    ![Kojarzenie z grupą zabezpieczeń aplikacji](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. Wykonaj kroki 1 i 2 ponownie, wyszukując maszynę wirtualną **myVmMgmt** i wybierając grupę zabezpieczeń aplikacji **myAsgMgmtServers**.

## <a name="test-traffic-filters"></a>Testowanie filtrów ruchu

1. Połącz się z maszyną wirtualną *myVmMgmt*. Wprowadź nazwę *myVmMgmt* w polu wyszukiwania w górnej części portalu. Gdy pozycja **myVmMgmt** pojawi się w wynikach wyszukiwania, wybierz ją. Wybierz przycisk **Połącz**.
2. Wybierz pozycję **Pobierz plik RDP**.
3. Otwórz pobrany plik RDP i wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej. Może okazać się konieczne wybranie pozycji **Więcej opcji**, a następnie pozycji **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.
4. Wybierz pozycję **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz pozycję **Tak** lub **Kontynuuj**, aby nawiązać połączenie.

    Połączenie powiedzie się, ponieważ port 3389 zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myAsgMgmtServers*, w której znajduje się interfejs sieciowy dołączony do maszyny wirtualnej *myVmMgmt*.

6. Połącz się z maszyną wirtualną *myVmWeb* z maszyny wirtualnej *myVmMgmt*, wprowadzając następujące polecenie w sesji programu PowerShell:

    ``` 
    mstsc /v:myVmWeb
    ```

    Możesz nawiązać połączenie z maszyną wirtualną myVmWeb z maszyny wirtualnej myVmMgmt, ponieważ maszyny wirtualne znajdujące się w tej samej sieci wirtualnej mogą domyślnie komunikować się ze sobą za pośrednictwem dowolnego portu. Nie możesz jednak utworzyć połączenia pulpitu zdalnego z maszyną wirtualną *myVmWeb* z Internetu, ponieważ reguły zabezpieczeń *myAsgWebServers* nie zezwalają na ruch przychodzący z Internetu przez port 3389, a ruch przychodzący z Internetu jest domyślnie zablokowany dla wszystkich zasobów.

7. Aby zainstalować usługi Microsoft IIS na maszynie wirtualnej *myVmWeb*, wprowadź następujące polecenie w sesji programu PowerShell na maszynie wirtualnej *myVmWeb*:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Po zakończeniu instalacji usług IIS odłącz się od maszyny wirtualnej *myVmWeb*, co spowoduje pozostanie w połączeniu pulpitu zdalnego z maszyną wirtualną *myVmMgmt*.
9. Odłącz się od maszyny wirtualnej *myVmMgmt*.
10. W polu *Szukaj zasobów, usług i dokumentów* w górnej części witryny Azure Portal rozpocznij wpisywanie ciągu *myVmWeb* na komputerze. Gdy pozycja **myVmWeb** pojawi się w wynikach wyszukiwania, wybierz ją. Zanotuj **publiczny adres IP** maszyny wirtualnej. Na poniższej ilustracji pokazano adres 137.135.84.74, ale Twój adres będzie się różnić:

    ![Publiczny adres IP](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. Aby potwierdzić, że możesz uzyskać dostęp do serwera internetowego *myVmWeb* z Internetu, otwórz przeglądarkę internetową na komputerze i przejdź do adresu `http://<public-ip-address-from-previous-step>`. Zobaczysz ekran powitalny usług IIS, ponieważ port 80 zezwala na ruch przychodzący z Internetu do grupy zabezpieczeń aplikacji *myAsgWebServers*, w której znajduje się interfejs sieciowy dołączony do maszyny wirtualnej *myVmWeb*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie znajdujące się w niej zasoby nie będą już potrzebne, usuń je:

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź dla elementu *Webresourcename* **Typ Nazwa grupy zasobów:** a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono sieciową grupę zabezpieczeń i skojarzono ją z podsiecią sieci wirtualnej. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Network security groups overview (Omówienie sieciowych grup zabezpieczeń)](security-overview.md) oraz [Manage a network security group (Zarządzanie sieciową grupą zabezpieczeń)](manage-network-security-group.md).

Platforma Azure domyślnie kieruje ruch pomiędzy podsieciami. Zamiast tego możesz przykładowo skierować ruch pomiędzy podsieciami przez maszynę wirtualną, która będzie służyć jako zapora. Aby dowiedzieć się więcej o sposobie tworzenia tabeli tras, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Tworzenie tabeli tras](./tutorial-create-route-table-portal.md)
