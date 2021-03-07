---
title: Filtrowanie ruchu sieciowego — samouczek — Azure Portal
titlesuffix: Azure Virtual Network
description: W tym samouczku dowiesz się, jak filtrować ruch sieciowy w podsieci z grupą zabezpieczeń sieci przy użyciu Azure Portal.
services: virtual-network
author: KumudD
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: 746e44c85d4dd9a662556a73f1e4ab0701d31400
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435928"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Samouczek: filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń przy użyciu Azure Portal

Za pomocą grupy zabezpieczeń sieci można filtrować ruch sieciowy przychodzący i wychodzący z podsieci sieci wirtualnej.

Sieciowe grupy zabezpieczeń zawierają reguły zabezpieczeń, które filtrują ruch sieciowy według adresów IP, portów i protokołów. Reguły zabezpieczeń są stosowane do zasobów wdrożonych w podsieci. 

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie sieciowej grupy zabezpieczeń i reguł zabezpieczeń
> * Tworzenie sieci wirtualnej i kojarzenie sieciowej grupy zabezpieczeń z podsiecią
> * Wdrażanie maszyn wirtualnych w podsieci
> * Testowanie filtrów ruchu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu portalu.

2. W polu wyszukiwania wprowadź **Virtual Network**. Wybierz **Virtual Network** w wynikach wyszukiwania.

3. Na stronie **Virtual Network** wybierz pozycję **Utwórz**.

4. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy**.  </br> Wprowadź nazwę **zasobu**. </br> Wybierz przycisk **OK**. |
    | **Szczegóły wystąpienia** |   |
    | Nazwa | Wprowadź nazwę **myVNet**. |
    | Region (Region) | Wybierz pozycję **(US) Wschodnie stany USA**. |

5. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.

6. Wybierz przycisk **Utwórz**.

## <a name="create-application-security-groups"></a>Tworzenie grup zabezpieczeń aplikacji

Grupa zabezpieczeń aplikacji umożliwia grupowanie serwerów o podobnych funkcjach, np. serwerów internetowych.

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu portalu.

2. W polu wyszukiwania wprowadź **grupę zabezpieczeń aplikacji**. W wynikach wyszukiwania wybierz pozycję **Grupa zabezpieczeń aplikacji** .

3. Na stronie **Grupa zabezpieczeń aplikacji** wybierz pozycję **Utwórz**.

4. W obszarze **Tworzenie grupy zabezpieczeń aplikacji** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. |
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź **myAsgWebServers**. |
    | Region (Region) | Wybierz pozycję **(US) Wschodnie stany USA**. | 

5. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.

6. Wybierz przycisk **Utwórz**.

7. Powtórz ponownie krok 4, określając następujące wartości:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. |
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź **myAsgMgmtServers**. |
    | Region (Region) | Wybierz pozycję **(US) Wschodnie stany USA**. |

8. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.

9. Wybierz przycisk **Utwórz**.

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Sieciowa Grupa zabezpieczeń zabezpiecza ruch sieciowy w sieci wirtualnej.

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu portalu.

2. W polu wyszukiwania wprowadź **sieciową grupę zabezpieczeń**. W wynikach wyszukiwania wybierz pozycję **sieciowa Grupa zabezpieczeń** .

3. Na stronie **Grupa zabezpieczeń sieci** wybierz pozycję **Utwórz**.

4. W obszarze **Tworzenie sieciowej grupy zabezpieczeń** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |   |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. |
    | **Szczegóły wystąpienia** |   |
    | Nazwa | Wprowadź **myNSG**. |
    | Lokalizacja | Wybierz pozycję **(US) Wschodnie stany USA**. | 

5. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.

6. Wybierz przycisk **Utwórz**.

## <a name="associate-network-security-group-to-subnet"></a>Przypisywanie sieciowej grupy zabezpieczeń do podsieci

W tej sekcji połączymy sieciową grupę zabezpieczeń z podsiecią sieci wirtualnej, która została utworzona wcześniej.

1. W polu **Szukaj zasobów, usług i dokumentów** w górnej części portalu rozpocznij wpisywanie ciągu **myNsg**. Gdy pozycja **myNsg** pojawi się w wynikach wyszukiwania, wybierz ją.

2. Na stronie Przegląd **myNSG** wybierz pozycję **podsieci** w **ustawieniach**.

3. Na stronie **Ustawienia** wybierz pozycję **Skojarz**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="Skojarz sieciowej grupy zabezpieczeń z podsiecią." border="true":::

3. W obszarze **Skojarz podsieć** wybierz pozycję **Sieć wirtualna** , a następnie wybierz pozycję **myVNet**. 

4. Wybierz pozycję **podsieć**, wybierz pozycję **domyślne**, a następnie wybierz przycisk **OK**.

## <a name="create-security-rules"></a>Tworzenie reguł zabezpieczeń

1. W  obszarze Ustawienia **myNSG** wybierz pozycję **reguły zabezpieczeń dla ruchu przychodzącego**.

2. W przystawce **reguły zabezpieczeń dla ruchu przychodzącego** wybierz pozycję **+ Dodaj**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Dodaj regułę zabezpieczeń dla ruchu przychodzącego." border="true":::

3. Utwórz regułę zabezpieczeń, która zezwala na stosowanie portów 80 i 443 do grupy zabezpieczeń aplikacji **myAsgWebServers**. W obszarze **Dodawanie reguły zabezpieczeń ruchu przychodzącego** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Źródło | Pozostaw wartość **domyślną.** |
    | Zakresy portów źródłowych | Pozostaw wartość domyślną **(*)** |
    | Element docelowy | Wybierz **grupę zabezpieczeń aplikacji**. |
    | Grupa zabezpieczeń aplikacji docelowej | Wybierz pozycję **myAsgWebServers**. |
    | Usługa | Pozostaw domyślną wartość **niestandardową**. |
    | Zakresy portów docelowych | Wprowadź **80 443**. |
    | Protokół | Wybierz pozycję **TCP**. |
    | Akcja | Pozostaw wartość domyślną **Zezwalaj**. |
    | Priorytet | Pozostaw wartość domyślną **100**. |
    | Nazwa | Wprowadź **Zezwól-Web-All**. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Reguła zabezpieczeń dla ruchu przychodzącego." border="true":::

3. Ponownie wykonaj krok 2, używając następujących wartości:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Źródło | Pozostaw wartość **domyślną.** |
    | Zakresy portów źródłowych | Pozostaw wartość domyślną **(*)** |
    | Element docelowy | Wybierz **grupę zabezpieczeń aplikacji**. |
    | Grupa zabezpieczeń aplikacji docelowej | Wybierz pozycję **myAsgMgmtServers**. |
    | Usługa | Pozostaw domyślną wartość **niestandardową**. |
    | Zakresy portów docelowych | Wprowadź **3389**. |
    | Protokół | Wybierz pozycję **TCP**. |
    | Akcja | Pozostaw wartość domyślną **Zezwalaj**. |
    | Priorytet | Pozostaw wartość domyślną **110**. |
    | Nazwa | Wprowadź **Allow-RDP-All**. |

    > [!CAUTION]
    > W tym artykule protokół RDP (Port 3389) został uwidoczniony w Internecie dla maszyny wirtualnej, która jest przypisana do grupy zabezpieczeń aplikacji **myAsgMgmtServers** . 
    >
    > W przypadku środowisk produkcyjnych zamiast udostępniania portu 3389 do Internetu zaleca się nawiązanie połączenia z zasobami platformy Azure, które mają być zarządzane przy użyciu sieci VPN, połączenia sieci prywatnej lub platformy Azure bastionu.
    >
    > Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [co to jest Azure bastionu?](../bastion/bastion-overview.md).

Po zakończeniu kroków 1–3 sprawdź utworzone reguły. Twoja lista powinna wyglądać podobnie do listy w poniższym przykładzie:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Reguły zabezpieczeń." border="true":::

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne.

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu portalu.

2. Wybierz pozycję **obliczenia**, a następnie pozycję **maszyna wirtualna**.

3. W obszarze **Utwórz maszynę wirtualną** wprowadź lub wybierz te informacje na karcie **podstawowe** :

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** |  |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. |
    | **Szczegóły wystąpienia** |   |
    | Nazwa maszyny wirtualnej | Wprowadź **myVMWeb**. |
    | Region (Region) | Wybierz pozycję **(US) Wschodnie stany USA**. |
    | Opcje dostępności | Pozostaw wartość domyślną nie wymaga nadmiarowości. |
    | Image (Obraz) | Wybierz pozycję **Windows Server 2019 Datacenter-Gen1**. |
    | Wystąpienie usługi Azure Spot | Pozostaw domyślnie niezaznaczone. |
    | Rozmiar | Wybierz **Standard_D2s_V3**. |
    | **Konto administratora** |   |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika. |
    | Hasło | Wprowadź hasło. |
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **Reguły portów ruchu przychodzącego** |   |
    | Publiczne porty wejściowe | Wybierz pozycję **Brak**. |

4. Wybierz kartę **Sieć**.

5. Na karcie **Sieć** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Interfejs sieciowy** |   |
    | Sieć wirtualna | Wybierz pozycję **myVNet**. |
    | Podsieć | Wybierz opcję **domyślne (10.0.0.0/24)**. |
    | Publiczny adres IP | Pozostaw wartość domyślną nowego publicznego adresu IP. |
    | Grupa zabezpieczeń sieci karty sieciowej | Wybierz pozycję **Brak**. | 

6. Wybierz kartę **Recenzja + tworzenie** lub wybierz przycisk niebieski **Przegląd + Utwórz** w dolnej części strony.

7. Wybierz przycisk **Utwórz**.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Ponownie wykonaj kroki 1-7, ale w kroku 3 nadaj maszynie wirtualnej nazwę **myVMMgmt**. Wdrożenie maszyny wirtualnej potrwa kilka minut. 

Nie Kontynuuj do następnego kroku, dopóki maszyna wirtualna nie zostanie wdrożona.

## <a name="associate-network-interfaces-to-an-asg"></a>Kojarzenie interfejsów sieciowych z grupą zabezpieczeń aplikacji

Podczas tworzenia maszyn wirtualnych przez portal dla każdej maszyny wirtualnej został utworzony interfejs sieciowy, który następnie został połączony z maszyną wirtualną. 

Dodaj interfejs sieciowy dla każdej maszyny wirtualnej do jednej z wcześniej utworzonych grup zabezpieczeń aplikacji:

1. W polu **Wyszukaj zasoby, usługi i dokumenty** w górnej części portalu zacznij pisać **myVMWeb**. Gdy maszyna wirtualna **myVMWeb** zostanie wyświetlona w wynikach wyszukiwania, wybierz ją.

2. W obszarze **Ustawienia** wybierz pozycję **Sieć**.  

3. Wybierz kartę **grupy zabezpieczeń aplikacji** , a następnie wybierz pozycję **Konfiguruj grupy zabezpieczeń aplikacji**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Skonfiguruj grupy zabezpieczeń aplikacji." border="true":::

4. W obszarze **Konfigurowanie grup zabezpieczeń aplikacji** wybierz pozycję **myAsgWebServers**. Wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Wybierz pozycję grupy zabezpieczeń aplikacji." border="true":::

5. Wykonaj ponownie kroki 1 i 2, wyszukując maszynę wirtualną **myVMMgmt** i wybierając ASG  **myAsgMgmtServers** .

## <a name="test-traffic-filters"></a>Testowanie filtrów ruchu

1. Nawiąż połączenie z maszyną wirtualną **myVMMgmt** . Wprowadź **myVMMgmt** w polu wyszukiwania w górnej części portalu. Gdy **myVMMgmt** pojawia się w wynikach wyszukiwania, wybierz ją. Wybierz przycisk **Połącz**.

2. Wybierz pozycję **Pobierz plik RDP**.

3. Otwórz pobrany plik RDP i wybierz pozycję **Połącz**. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

4. Wybierz przycisk **OK**.

5. Podczas procesu połączenia może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie, wybierz opcję **tak** lub **Kontynuuj**, aby kontynuować połączenie.

    Połączenie powiedzie się, ponieważ port 3389 jest dozwolony dla ruchu przychodzącego z Internetu do grupy zabezpieczeń aplikacji **myAsgMgmtServers** . 
    
    Interfejs sieciowy dla **myVMMgmt** jest skojarzony z grupą zabezpieczeń aplikacji **myAsgMgmtServers** i umożliwia połączenie.

6. Otwórz sesję programu PowerShell w usłudze **myVMMgmt**. Połącz się z usługą **myVMWeb** , korzystając z następującego przykładu: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    Połączenie RDP od **myVMMgmt** do **myVMWeb** kończy się pomyślnie, ponieważ maszyny wirtualne w tej samej sieci mogą komunikować się z każdym portem domyślnie.
    
    Nie można utworzyć połączenia RDP z maszyną wirtualną **myVMWeb** z Internetu. Reguła zabezpieczeń dla **myAsgWebServers** uniemożliwia połączenia z portem 3389 przychodzące z Internetu. Ruch przychodzący z Internetu jest domyślnie odrzucany do wszystkich zasobów.

7. Aby zainstalować program Microsoft IIS na maszynie wirtualnej **myVMWeb** , wprowadź następujące polecenie w sesji programu PowerShell na maszynie wirtualnej **myVMWeb** :

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Po zakończeniu instalacji usług IIS odłącz się od maszyny wirtualnej **myVMWeb** , która opuszcza połączenie pulpitu zdalnego **myVMMgmt** maszyny wirtualnej.

9. Rozłącz z maszyną wirtualną **myVMMgmt** .

10. W polu **Wyszukaj zasoby, usługi i dokumenty** w górnej części Azure Portal zacznij pisać **myVMWeb** z komputera. Gdy **myVMWeb** pojawia się w wynikach wyszukiwania, wybierz ją. Zanotuj **publiczny adres IP** maszyny wirtualnej. Adres przedstawiony w poniższym przykładzie to 23.96.39.113, ale adres różni się od siebie:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Publiczny adres IP." border="true":::
    
11. Aby upewnić się, że możesz uzyskać dostęp do serwera sieci Web **myVMWeb** z Internetu, Otwórz przeglądarkę internetową na swoim komputerze i przejdź do witryny `http://<public-ip-address-from-previous-step>` . 

Zostanie wyświetlony ekran powitalny usług IIS, ponieważ port 80 jest dozwolony dla ruchu przychodzącego z Internetu do grupy zabezpieczeń aplikacji **myAsgWebServers** . 

Interfejs sieciowy dołączony do usługi **myVMWeb** jest skojarzony z grupą zabezpieczeń aplikacji **myAsgWebServers** i umożliwia połączenie. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie znajdujące się w niej zasoby nie będą już potrzebne, usuń je:

1. Wprowadź ciąg **myResourceGroup** w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź dla elementu **Webresourcename** **Typ Nazwa grupy zasobów:** a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

* Utworzono sieciową grupę zabezpieczeń i skojarzono ją z podsiecią sieci wirtualnej. 
* Utworzono grupy zabezpieczeń aplikacji dla sieci Web i zarządzania.
* Utworzono dwie maszyny wirtualne.
* Przetestowano Filtrowanie sieci grupy zabezpieczeń aplikacji.

Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [Network security groups overview (Omówienie sieciowych grup zabezpieczeń)](./network-security-groups-overview.md) oraz [Manage a network security group (Zarządzanie sieciową grupą zabezpieczeń)](manage-network-security-group.md).

Platforma Azure domyślnie kieruje ruch pomiędzy podsieciami. Zamiast tego możesz przykładowo skierować ruch pomiędzy podsieciami przez maszynę wirtualną, która będzie służyć jako zapora. 

Aby dowiedzieć się więcej o sposobie tworzenia tabeli tras, przejdź do następnego samouczka.
> [!div class="nextstepaction"]
> [Tworzenie tabeli tras](./tutorial-create-route-table-portal.md)