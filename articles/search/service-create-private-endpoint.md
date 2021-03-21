---
title: Utwórz prywatny punkt końcowy dla bezpiecznego połączenia
titleSuffix: Azure Cognitive Search
description: Skonfiguruj prywatny punkt końcowy w sieci wirtualnej w celu nawiązania bezpiecznego połączenia z usługą Wyszukiwanie poznawcze platformy Azure.
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 7445ac5d750ac29d3e6ce466a48e82efd1bcde40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545534"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Utwórz prywatny punkt końcowy dla bezpiecznego połączenia z platformą Azure Wyszukiwanie poznawcze

W tym artykule opisano Azure Portal tworzenia nowego wystąpienia usługi Azure Wyszukiwanie poznawcze, do którego nie można uzyskać dostępu za pośrednictwem Internetu. Następnie skonfigurujesz maszynę wirtualną platformy Azure w tej samej sieci wirtualnej i używasz jej do uzyskiwania dostępu do usługi wyszukiwania za pośrednictwem prywatnego punktu końcowego.

Prywatne punkty końcowe są udostępniane przez [łącze prywatne platformy Azure](../private-link/private-link-overview.md)jako oddzielna usługa. Aby uzyskać więcej informacji o kosztach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/private-link/).

Możesz utworzyć prywatny punkt końcowy w Azure Portal, zgodnie z opisem w tym artykule. Alternatywnie możesz użyć [interfejsu API REST zarządzania w wersji 2020-03-13](/rest/api/searchmanagement/), [Azure PowerShell](/powershell/module/az.search)lub [interfejsu wiersza polecenia platformy Azure](/cli/azure/search).

> [!NOTE]
> Gdy punkt końcowy usługi jest prywatny, niektóre funkcje portalu są wyłączone. Można wyświetlać i zarządzać informacjami o poziomie usług, ale informacje dotyczące indeksowania, indeksatora i zestawu umiejętności są ukryte ze względów bezpieczeństwa. Jako alternatywę dla portalu można użyć [rozszerzenia vs Code](https://aka.ms/vscode-search) , aby korzystać z różnych składników usługi.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Dlaczego należy używać prywatnego punktu końcowego na potrzeby bezpiecznego dostępu?

[Prywatne punkty końcowe](../private-link/private-endpoint-overview.md) dla systemu Azure wyszukiwanie poznawcze umożliwiają klientowi w sieci wirtualnej bezpieczne uzyskiwanie dostępu do danych w indeksie wyszukiwania za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z [przestrzeni adresowej sieci wirtualnej](../virtual-network/private-ip-addresses.md) dla usługi wyszukiwania. Ruch sieciowy między klientem a usługą wyszukiwania odbywa się za pośrednictwem sieci wirtualnej i łączy prywatnych w sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Aby zapoznać się z listą innych usług PaaS, które obsługują link prywatny, należy zapoznać się z [sekcją dostępność](../private-link/private-link-overview.md#availability) w dokumentacji produktu.

Prywatne punkty końcowe usługi wyszukiwania umożliwiają:

- Zablokuj wszystkie połączenia w publicznym punkcie końcowym usługi wyszukiwania.
- Zwiększ bezpieczeństwo sieci wirtualnej, umożliwiając zablokowanie eksfiltracji danych z sieci wirtualnej.
- Bezpiecznie łącz się z usługą wyszukiwania z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [Usługa expressroutes](../expressroute/expressroute-locations.md) z prywatną komunikację równorzędną.

## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć do hostowania maszyny wirtualnej, która zostanie użyta w celu uzyskania dostępu do prywatnego punktu końcowego usługi wyszukiwania.

1. Na karcie Narzędzia główne Azure Portal wybierz pozycję **Utwórz zasób**  >  **Sieć**  >  **sieci wirtualnej**.

1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz swoją subskrypcję|
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy**, wprowadź nazwę *zasobu*, a następnie wybierz przycisk **OK** . |
    | Nazwa | Wprowadź *MyVirtualNetwork* |
    | Region (Region) | Wybierz żądany region |
    |||

1. Pozostaw wartości domyślne pozostałych ustawień. Kliknij przycisk **Przegląd + Utwórz** , a następnie **Utwórz**

## <a name="create-a-search-service-with-a-private-endpoint"></a>Tworzenie usługi wyszukiwania za pomocą prywatnego punktu końcowego

W tej sekcji utworzysz nową usługę Wyszukiwanie poznawcze platformy Azure z prywatnym punktem końcowym. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**  >  **Sieć Web**  >  **Azure wyszukiwanie poznawcze**.

1. W obszarze **nowe Search Service — podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Adres URL | Wprowadź unikatową nazwę. |
    | Lokalizacja | Wybierz żądany region. |
    | Warstwa cenowa | Wybierz pozycję **Zmień warstwę cenową** i wybierz żądaną warstwę usług. (Nieobsługiwane w warstwie **bezpłatna** . Musi być w warstwie **podstawowa** lub wyższa.) |
    |||
  
1. Wybierz pozycję **Dalej: skala**.

1. Pozostaw wartości domyślne, a następnie wybierz pozycję **Dalej: sieć**.

1. W obszarze **nowy Search Service — sieć** wybierz pozycję **prywatne** dla **połączenia punktu końcowego (dane)**.

1. W obszarze **nowy Search Service — sieć** wybierz pozycję **+ Dodaj** w obszarze **prywatny punkt końcowy**. 

1. W obszarze **Utwórz prywatny punkt końcowy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | Lokalizacja | wybierz pozycję **Zachodnie stany USA**.|
    | Nazwa | Wprowadź *myPrivateEndpoint*.  |
    | Docelowy zasób podrzędny | Pozostaw wartość domyślną **searchService**. |
    | **SIEĆ** |  |
    | Sieć wirtualna  | Wybierz pozycję *MyVirtualNetwork* *z grupy zasobów*. |
    | Podsieć | Wybierz pozycję Moja *podsieć*. |
    | **INTEGRACJA PRYWATNEJ USŁUGI DNS** |  |
    | Integruj z prywatną strefą DNS  | Pozostaw wartość domyślną **tak**. |
    | Prywatna strefa DNS  | Pozostaw wartość domyślną * * (New) privatelink.search.windows.net * *. |
    |||

1. Wybierz przycisk **OK**. 

1. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację. 

1. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**. 

1. Po zakończeniu aprowizacji nowej usługi przejdź do zasobu, który właśnie został utworzony.

1. Z menu po lewej stronie wybierz pozycję **klucze** .

1. Skopiuj **podstawowy klucz administratora** w celu późniejszego nawiązania połączenia z usługą.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**  >  **obliczeniowy**  >  **maszyny wirtualnej**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wprowadź *myVm*. |
    | Region (Region) | Wybierz pozycję **zachodnie stany USA** lub region, którego używasz. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter**. |
    | Rozmiar | Pozostaw domyślną wartość **Standard DS1 v2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw domyślne ustawienie **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Pozostaw domyślny **protokół RDP (3389)**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **nie**. |
    |||

1. Wybierz pozycję **Dalej: dyski**.

1. W obszarze **Tworzenie maszyny wirtualnej — dyski** pozostaw wartości domyślne i wybierz przycisk **Dalej: Sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **MyVirtualNetwork**.  |
    | Przestrzeń adresowa | Pozostaw wartość domyślną **10.1.0.0/24**.|
    | Podsieć | Pozostaw domyślną wartość moja **podsieć (10.1.0.0/24)**.|
    | Publiczny adres IP | Pozostaw wartość domyślną **(New) myVm-IP**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    ||

   > [!NOTE]
   > Adresy IPv4 można wyrazić w formacie [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Należy pamiętać, aby uniknąć zakresu adresów IP zarezerwowanych dla sieci prywatnej, zgodnie z opisem w [dokumencie RFC 1918](https://tools.ietf.org/html/rfc1918):
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. Wybierz pozycję **Przeglądanie + tworzenie**. Nastąpi przekierowanie do strony **Przeglądanie i tworzenie**, na której platforma Azure zweryfikuje konfigurację.

1. Po wyświetleniu komunikatu **Sprawdzanie poprawności zakończone powodzeniem** kliknij przycisk **Utwórz**. 

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Pobierz *myVm* maszyny wirtualnej, a następnie połącz się z nią w następujący sposób:

1. Na pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz pozycję **Pobierz plik RDP**. Platforma Azure tworzy plik Remote Desktop Protocol (*RDP*) i pobiera go na komputer.

1. Otwórz pobrany plik RDP *.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może być konieczne wybranie **pozycji więcej opcji**  >  **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz pozycję **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="test-connections"></a>Testuj połączenia

W tej sekcji będziesz weryfikować dostęp do sieci prywatnej do usługi wyszukiwania i łączyć się z prywatnymi z prywatnym punktem końcowym.

Gdy punkt końcowy usługi wyszukiwania jest prywatny, niektóre funkcje portalu są wyłączone. Będziesz mieć możliwość wyświetlania ustawień poziomu usług i zarządzania nimi, ale dostęp do portalu do indeksowania danych i różnych innych składników w usłudze, takich jak indeks, indeksator i definicje zestawu umiejętności, jest ograniczony ze względów bezpieczeństwa.

1. W Pulpit zdalny *myVM* Otwórz program PowerShell.

1. Wprowadź ciąg "nslookup [nazwa usługi wyszukiwania]. Search. Windows. NET"

    Zostanie wyświetlony komunikat podobny do tego:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Z poziomu maszyny wirtualnej Połącz się z usługą wyszukiwania i Utwórz indeks. Możesz skorzystać z tego [przewodnika Szybki Start](search-get-started-rest.md) , aby utworzyć nowy indeks wyszukiwania w usłudze przy użyciu interfejsu API REST. Konfigurowanie żądań za pomocą narzędzia testowego interfejsu API sieci Web wymaga punktu końcowego usługi wyszukiwania (https://[nazwa usługi wyszukiwania]. Search. Windows. NET) i administratora klucza API-Key skopiowano w poprzednim kroku.

1. Ukończenie szybkiego startu z maszyny wirtualnej to potwierdzenie, że usługa jest w pełni funkcjonalna.

1. Zamknij połączenie pulpitu zdalnego z *myVM*. 

1. Aby sprawdzić, czy usługa jest niedostępna w publicznym punkcie końcowym, Otwórz program Poster na lokalnej stacji roboczej i spróbuj wykonać kilka pierwszych zadań w przewodniku Szybki Start. Jeśli zostanie wyświetlony komunikat o błędzie, że serwer zdalny nie istnieje, pomyślnie skonfigurowano prywatny punkt końcowy dla usługi wyszukiwania.

## <a name="clean-up-resources"></a>Czyszczenie zasobów 
Gdy skończysz korzystać z prywatnego punktu końcowego, usługi wyszukiwania i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:
1. Wprowadź  **   w polu **wyszukiwania** w górnej części portalu i wybierz pozycję Moja zasobów  **   z wyników wyszukiwania. 
1. Wybierz pozycję **Usuń grupę zasobów**. 
1. Wprowadź wartość  *Webresourcename*   **, aby wpisać nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono MASZYNę wirtualną w sieci wirtualnej i usługę wyszukiwania z prywatnym punktem końcowym. Nawiązano połączenie z maszyną wirtualną z Internetu i bezpiecznie komunikuje się z usługą wyszukiwania za pomocą linku prywatnego. Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [co to jest prywatny punkt końcowy platformy Azure?](../private-link/private-endpoint-overview.md).
