---
title: Kierowanie ruchu sieciowego — samouczek — Azure Portal
titlesuffix: Azure Virtual Network
description: W tym samouczku dowiesz się, jak kierować ruchem sieciowym za pomocą tabeli tras z użyciem witryny Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: e047f46e110e1f7b1d544545c80bd1097ae65167
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221922"
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

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Wirtualne urządzenia sieciowe (urządzeń WUS) są maszynami wirtualnymi, które ułatwiają funkcje sieciowe, takie jak optymalizacja routingu i zapory. W tym samouczku założono, że używasz systemu **Windows Server 2016 Datacenter**. Możesz wybrać inny system operacyjny.

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Wybierz pozycję **zabezpieczenia**  >  **systemu Windows Server 2016 Datacenter**.

    ![System Windows Server 2016 Datacenter, Tworzenie maszyny wirtualnej, Azure Portal](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Na stronie **Tworzenie maszyny wirtualnej** w obszarze **podstawowe** wprowadź lub wybierz następujące informacje:

    | Sekcja | Ustawienie | Akcja |
    | ------- | ------- | ----- |
    | **Szczegóły projektu** | Subskrypcja | Wybierz subskrypcję. |
    | | Grupa zasobów | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | **Szczegóły wystąpienia** | Nazwa maszyny wirtualnej | Wpisz *myVmNva*. |
    | | Region (Region) | Wybierz pozycję **(US) Wschodnie stany USA**. |
    | | Opcje dostępności | Wybierz opcję **Brak wymaganej nadmiarowości infrastruktury**. |
    | | Image (Obraz) | Wybierz pozycję **Windows Server 2016 Datacenter**. |
    | | Rozmiar | Zachowaj domyślny **Standard DS1 v2**. |
    | **Konto administratora** | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | | Hasło | Wprowadź hasło, które musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). |
    | | Potwierdź hasło | Wprowadź hasło ponownie. |
    | **Reguły portów ruchu przychodzącego** | Publiczne porty wejściowe | Wybierz **opcję Brak**. |
    | **Oszczędność pieniędzy** | Masz już licencję na system Windows Server? | Wybierz pozycję **nie**. |

    ![Podstawowe informacje, Tworzenie maszyny wirtualnej, Azure Portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Następnie wybierz pozycję **Dalej: dyski >**.

1. W obszarze **dyski** wybierz ustawienia odpowiednie dla Twoich potrzeb, a następnie wybierz kolejno pozycje **dalej: sieć >**.

1. W obszarze **Sieć**:

    1. W obszarze **Sieć wirtualna** wybierz pozycję **Utwórz nową**.
    
    1. W oknie dialogowym **Tworzenie sieci wirtualnej** w polu **Nazwa** wprowadź *myVirtualNetwork*.

    1. W **obszarze przestrzeń adresowa** Zastąp istniejący zakres adresów adresem *10.0.0.0/16*.

    1. W obszarze **podsieci** wybierz ikonę **Usuń** , aby usunąć istniejącą podsieć, a następnie wprowadź następujące kombinacje **nazw podsieci** i **zakresu adresów**. Po wprowadzeniu prawidłowej nazwy i zakresu zostanie wyświetlony nowy pusty wiersz poniżej.

        | Nazwa podsieci | Zakres adresów |
        | ----------- | ------------- |
        | *Publiczne* | *10.0.0.0/24* |
        | *Prywatne* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. Wybierz **przycisk OK** , aby zamknąć okno dialogowe.

    1. W obszarze **podsieć** wybierz pozycję **DMZ (10.0.2.0/24)**.

    1. W obszarze **publiczny adres IP** wybierz pozycję **Brak**, ponieważ ta maszyna wirtualna nie będzie łączyć się za pośrednictwem Internetu.

    1. Wybierz pozycję **Dalej: >zarządzania**.

1. W obszarze **Zarządzanie**:

    1. W obszarze **konto magazynu diagnostyki** wybierz pozycję **Utwórz nowy**.
    
    1. W oknie dialogowym **Tworzenie konta magazynu** wprowadź lub wybierz następujące informacje:

        | Ustawienie | Wartość |
        | ------- | ----- |
        | Nazwa | *mynvastorageaccount* |
        | Rodzaj konta | **Storage (ogólnego przeznaczenia w wersji 1)** |
        | Wydajność | **Standardowa** |
        | Replikacja | **Magazyn lokalnie nadmiarowy (LRS)** |
    
    1. Wybierz **przycisk OK** , aby zamknąć okno dialogowe.

    1. Wybierz pozycję **Przejrzyj i utwórz**. Nastąpi przekierowanie do strony **Recenzja + tworzenie** , a platforma Azure zweryfikuje konfigurację.

1. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**.

    W ciągu kilku minut zostanie utworzona maszyna wirtualna. Poczekaj na zakończenie tworzenia maszyny wirtualnej przez platformę Azure. Na stronie **Twoje wdrożenie** zostanie wyświetlona strona Szczegóły wdrożenia.

1. Gdy maszyna wirtualna będzie gotowa, wybierz pozycję **Przejdź do zasobu**.

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

1. W menu witryny [Azure Portal](https://portal.azure.com) lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

2. W polu wyszukiwania wprowadź *tabelę tras*. Gdy **tabela tras** zostanie wyświetlona w wynikach wyszukiwania, wybierz ją.

3. Na stronie **tabela tras** wybierz pozycję **Utwórz**.

4. W obszarze **Tworzenie tabeli tras** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | *myRouteTablePublic* |
    | Subskrypcja | Twoja subskrypcja |
    | Grupa zasobów | **myResourceGroup** |
    | Lokalizacja | **(USA) Wschodnie stany USA** |
    | Propagacja trasy bramy sieci wirtualnej | **Włączono** |

    ![Tworzenie tabeli tras, Azure Portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Wybierz pozycję **Utwórz**.

## <a name="create-a-route"></a>Tworzenie trasy

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać tabelą tras. Wyszukaj i wybierz pozycję **tabele tras**.

1. Wybierz nazwę tabeli tras (**myRouteTablePublic**).

1. Wybierz pozycję **trasy**  >  **Dodaj**.

    ![Dodawanie trasy, tabeli tras, Azure Portal](./media/tutorial-create-route-table-portal/add-route.png)

1. W obszarze **Dodawanie trasy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa trasy | *ToPrivateSubnet* |
    | Prefiks adresu | *10.0.1.0/24* (zakres adresów podsieci *prywatnej* utworzonej wcześniej) |
    | Typ następnego przeskoku | **Urządzenie wirtualne** |
    | Adres następnego skoku | *10.0.2.4* (adres w zakresie adresów podsieci *DMZ* ) |

1. Wybierz przycisk **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać siecią wirtualną. Wyszukaj i wybierz pozycję **sieci wirtualne**.

1. Wybierz nazwę sieci wirtualnej (**myVirtualNetwork**).

1. Na pasku menu sieci wirtualnej wybierz **podsieć**.

1. Na liście podsieć sieci wirtualnej wybierz pozycję **publiczny**.

1. W **tabeli tras** Wybierz utworzoną tabelę tras (**myRouteTablePublic**), a następnie wybierz pozycję **Zapisz** , aby skojarzyć tabelę tras z podsiecią *publiczną* .

    ![Skojarz tabelę tras, listę podsieci, sieć wirtualną, Azure Portal](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Włączanie przekazywania dalej adresu IP

Następnie Włącz przekazywanie IP dla nowej maszyny wirtualnej urządzenie WUS, *myVmNva*. Gdy platforma Azure wysyła ruch sieciowy do *myVmNva*, jeśli ruch jest przeznaczony dla innego adresu IP, przekazywanie IP wysyła ruch do odpowiedniej lokalizacji.

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać maszyną wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

1. Wybierz nazwę maszyny wirtualnej (**myVmNva**).

1. Na pasku menu urządzenie WUS maszyny wirtualnej wybierz pozycję **Sieć**.

1. Wybierz pozycję **myvmnva123**. Jest to interfejs sieciowy utworzony przez platformę Azure dla maszyny wirtualnej. Platforma Azure dodaje liczby, aby zapewnić unikatową nazwę.

    ![Sieć, maszyna wirtualna w sieci (urządzenie WUS), maszyny wirtualne (VM), Azure Portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Na pasku menu interfejsu sieciowego wybierz pozycję **konfiguracje adresów IP**.

1. Na stronie **konfiguracje adresów IP** ustaw opcję **przekazywanie adresów IP** na **włączone**, a następnie wybierz pozycję **Zapisz**.

    ![Włącz przekazywanie adresów IP, konfiguracje protokołu IP, interfejs sieciowy, maszyna wirtualna w sieci (urządzenie WUS), Azure Portal](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Tworzenie publicznych i prywatnych maszyn wirtualnych

Utwórz publiczną i prywatną maszynę wirtualną w sieci wirtualnej. Później użyjesz ich, aby sprawdzić, jak platforma Azure kieruje ruch podsieci *publicznej* do podsieci *prywatnej* za pośrednictwem urządzenia WUS.

Aby utworzyć publiczną maszynę wirtualną i prywatną maszynę wirtualną, wykonaj kroki opisane w sekcji [Tworzenie urządzenie WUS](#create-an-nva) wcześniej. Nie musisz czekać na zakończenie wdrażania lub przejdź do zasobu maszyny wirtualnej. Będziesz korzystać z większości tych samych ustawień, z wyjątkiem przypadków opisanych poniżej.

Przed wybraniem pozycji **Utwórz** , aby utworzyć publiczną lub PRYWATNą maszynę wirtualną, przejdź do następujących dwóch podsekcji ([publiczna maszyna wirtualna](#public-vm) i [prywatna maszyna wirtualna](#private-vm)), która wyświetla wartości, które muszą być różne. Możesz przejść do następnej sekcji ([kierowanie ruchu przez urządzenie WUS](#route-traffic-through-an-nva)) po zakończeniu wdrażania obu maszyn wirtualnych na platformie Azure.

### <a name="public-vm"></a>Publiczna maszyna wirtualna

| Tab | Ustawienie | Wartość |
| --- | ------- | ----- |
| Podstawy | Grupa zasobów | **myResourceGroup** |
| | Nazwa maszyny wirtualnej | *myVmPublic* |
| | Publiczne porty wejściowe | **Zezwalaj na wybrane porty** |
| | Wybierz porty wejściowe | **RDP** |
| Sieć | Sieć wirtualna | **myVirtualNetwork** |
| | Podsieć | **Public (10.0.0.0/24)** |
| | Publiczny adres IP | Wartość domyślna |
| Zarządzanie | Konto magazynu diagnostyki | **mynvastorageaccount** |

### <a name="private-vm"></a>Prywatna maszyna wirtualna

| Tab | Ustawienie | Wartość |
| --- | ------- | ----- |
| Podstawy | Grupa zasobów | **myResourceGroup** |
| | Nazwa maszyny wirtualnej | *myVmPrivate* |
| | Publiczne porty wejściowe | **Zezwalaj na wybrane porty** |
| | Wybierz porty wejściowe | **RDP** |
| Sieć | Sieć wirtualna | **myVirtualNetwork** |
| | Podsieć | **Prywatny (10.0.1.0/24)** |
| | Publiczny adres IP | Wartość domyślna |
| Zarządzanie | Konto magazynu diagnostyki | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Logowanie się do maszyny wirtualnej myVmPrivate za pośrednictwem pulpitu zdalnego

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać PRYWATNą maszyną wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

1. Wybierz nazwę prywatnej maszyny wirtualnej (**myVmPrivate**).

1. Na pasku menu maszyny wirtualnej wybierz pozycję **Połącz** , aby utworzyć połączenie pulpitu zdalnego z PRYWATNą maszyną wirtualną.

1. Na stronie **Połącz z** protokołem RDP wybierz pozycję **Pobierz plik RDP**. Platforma Azure tworzy plik Remote Desktop Protocol (*RDP*) i pobiera go na komputer.

1. Otwórz pobrany plik *RDP* . Po wyświetleniu monitu wybierz pozycję **Połącz**. Wybierz opcję **więcej opcji**  >  ,**Użyj innego konta**, a następnie wprowadź nazwę użytkownika i hasło określone podczas tworzenia prywatnej maszyny wirtualnej.

1. Wybierz przycisk **OK**.

1. Jeśli podczas logowania zostanie wyświetlone ostrzeżenie dotyczące certyfikatu, wybierz pozycję **tak** , aby nawiązać połączenie z maszyną wirtualną.

### <a name="enable-icmp-through-the-windows-firewall"></a>Włączanie komunikacji protokołu ICMP przez Zaporę systemu Windows

W kolejnym kroku przetestujesz routing przy użyciu narzędzia śledzenia trasy. Narzędzie śledzenia trasy używa protokołu ICMP (Internet Control Message Protocol), którego ruch jest domyślnie blokowany przez Zaporę systemu Windows. Włącz protokół ICMP za pomocą Zapory systemu Windows.

1. Na pulpicie zdalnym maszyny *myVmPrivate* otwórz program PowerShell.

1. Wprowadź następujące polecenie:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Będziesz używać trasy śledzenia do testowania routingu w tym samouczku. W środowiskach produkcyjnych nie zalecamy zezwalania na protokół ICMP przez Zaporę systemu Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Włączanie przekazywania dalej adresu na maszynie wirtualnej myVmNva

[Włączono przekazywanie adresu IP](#turn-on-ip-forwarding) dla interfejsu sieciowego maszyny wirtualnej przy użyciu platformy Azure. System operacyjny maszyny wirtualnej również musi przekazywać dalej ruch sieciowy. Włącz przekazywanie adresu IP dla systemu operacyjnego maszyny wirtualnej *myVmNva* przy użyciu poniższych poleceń.

1. W wierszu polecenia maszyny wirtualnej *myVmPrivate* otwórz pulpit zdalny na maszynie wirtualnej *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. W programie PowerShell na maszynie wirtualnej *myVmNva* wprowadź następujące polecenie, aby włączyć przekazywanie adresów IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Uruchom ponownie maszynę wirtualną *myVmNva* : z paska zadań wybierz pozycję **Uruchom**  >  **energię**, **inne (zaplanowane)**  >  **Kontynuuj**.

    Spowoduje to również odłączenie sesji pulpitu zdalnego.

1. Po ponownym uruchomieniu maszyny wirtualnej *myVmNva* utwórz sesję pulpitu zdalnego z maszyną wirtualną *myVmPublic*. Mając nadal połączenie z maszyną *myVmPrivate*, otwórz wiersz polecenia i uruchom następujące polecenie:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Na pulpicie zdalnym *myVmPublic* Otwórz program PowerShell.

1. Włącz protokół ICMP za pomocą Zapory systemu Windows przez wprowadzenie tego polecenia:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testowanie routingu ruchu sieciowego

Najpierw przetestuj routing ruchu sieciowego z maszyny wirtualnej *myVmPublic* do maszyny wirtualnej *myVmPrivate*.

1. W programie PowerShell na maszynie wirtualnej *myVmPublic* wprowadź następujące polecenie:

    ```powershell
    tracert myVmPrivate
    ```

    Odpowiedź jest podobna do poniższego przykładu:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Pierwszy przeskok to 10.0.2.4, który jest urządzenie WUS prywatnym adresem IP. Drugi przeskok to prywatny adres IP maszyny wirtualnej *myVmPrivate* : 10.0.1.4. Wcześniej dodano trasę do tabeli tras *myRouteTablePublic* i skojarzono ją z podsiecią *publiczną*. W rezultacie platforma Azure wysyłała ruch przez urządzenie WUS, a nie bezpośrednio do podsieci *prywatnej*.

1. Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPublic*. Połączenie z maszyną wirtualną *myVmPrivate* pozostanie nadal aktywne.

1. W wierszu polecenia na maszynie wirtualnej *myVmPrivate* wprowadź następujące polecenie:

    ```cmd
    tracert myVmPublic
    ```

    To polecenie testuje Routing ruchu sieciowego z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic* . Odpowiedź jest podobna do poniższego przykładu:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Możesz zobaczyć, że platforma Azure kieruje ruch bezpośrednio z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic* . Domyślnie platforma Azure kieruje ruch bezpośrednio między podsieciami.

1. Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPrivate*.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli grupa zasobów nie jest już wymagana, Usuń grupę *zasobów* i wszystkie jej zasoby:

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby zarządzać grupą zasobów. Wyszukaj i wybierz pozycję **grupy zasobów**.

1. Wybierz nazwę grupy **zasobów (zasobu**).

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W oknie dialogowym *potwierdzenia wprowadź* wartość w polu **Nazwa grupy zasobów**, a następnie wybierz pozycję **Usuń**. *Na platformie Azure jest* usuwana Grupa zasobów i wszystkie zasoby powiązane z tą grupą zasobów, w tym tabele tras, konta magazynu, sieci wirtualne, maszyny wirtualne, interfejsy sieciowe i publiczne adresy IP.

## <a name="next-steps"></a>Następne kroki

W tym samouczku została utworzona tabela tras, która została następnie skojarzona z podsiecią. Utworzono proste urządzenie NVA, które kierowało ruch z podsieci publicznej do podsieci prywatnej. Teraz można wdrożyć różne wstępnie skonfigurowane urządzeń WUS z poziomu [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), które udostępniają wiele przydatnych funkcji sieciowych. Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, platforma Azure nie może wdrożyć zasobów dla niektórych usług PaaS w sieci wirtualnej. Istnieje możliwość ograniczenia dostępu do zasobów niektórych usług PaaS platformy Azure, chociaż ograniczenie musi być tylko ruchem z podsieci sieci wirtualnej. Aby dowiedzieć się, jak ograniczyć dostęp sieciowy do zasobów usługi Azure PaaS, zobacz następny samouczek.

> [!div class="nextstepaction"]
> [Ograniczanie dostępu sieciowego do zasobów PaaS](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> Opłaty za usługi platformy Azure. Azure Cost Management ułatwia ustawianie budżetów i Konfigurowanie alertów w celu utrzymywania wydatków pod kontrolą. Analizowanie i optymalizowanie kosztów platformy Azure oraz zarządzanie nimi za pomocą Cost Management. Aby dowiedzieć się więcej, zobacz [Przewodnik Szybki Start dotyczący analizowania kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).