---
title: Utwórz prywatny punkt końcowy dla bezpiecznego połączenia
titleSuffix: Azure Cognitive Search
description: Skonfiguruj prywatny punkt końcowy w sieci wirtualnej w celu nawiązania bezpiecznego połączenia z usługą Wyszukiwanie poznawcze platformy Azure
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945821"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Utwórz prywatny punkt końcowy dla bezpiecznego połączenia z usługą Azure Wyszukiwanie poznawcze (wersja zapoznawcza)

W tym artykule Użyj portalu, aby utworzyć nowe wystąpienie usługi Azure Wyszukiwanie poznawcze, do którego nie można uzyskać dostępu za pośrednictwem publicznego adresu IP. Następnie skonfiguruj maszynę wirtualną platformy Azure w tej samej sieci wirtualnej i użyj jej do uzyskiwania dostępu do usługi wyszukiwania za pośrednictwem prywatnego punktu końcowego.

> [!Important]
> Obsługa prywatnego punktu końcowego dla platformy Azure Wyszukiwanie poznawcze jest dostępna [na żądanie](https://aka.ms/SearchPrivateLinkRequestAccess) jako wersja zapoznawcza ograniczonego dostępu. Funkcje w wersji zapoznawczej są udostępniane bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
>
> Po udzieleniu dostępu do wersji zapoznawczej można skonfigurować prywatne punkty końcowe dla usługi przy użyciu Azure Portal lub [interfejsu API REST zarządzania w wersji 2019-10-06-Preview](https://docs.microsoft.com/rest/api/searchmanagement/).
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Dlaczego należy używać prywatnego punktu końcowego na potrzeby bezpiecznego dostępu?

[Prywatne punkty końcowe](../private-link/private-endpoint-overview.md) dla systemu Azure wyszukiwanie poznawcze umożliwiają klientowi w sieci wirtualnej bezpieczne uzyskiwanie dostępu do danych w indeksie wyszukiwania za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z [przestrzeni adresowej sieci wirtualnej](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) dla usługi wyszukiwania. Ruch sieciowy między klientem a usługą wyszukiwania odbywa się za pośrednictwem sieci wirtualnej i łączy prywatnych w sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Aby zapoznać się z listą innych usług PaaS, które obsługują link prywatny, należy zapoznać się z [sekcją dostępność](../private-link/private-link-overview.md#availability) w dokumentacji produktu.

Prywatne punkty końcowe usługi wyszukiwania umożliwiają:

- Zablokuj wszystkie połączenia w publicznym punkcie końcowym usługi wyszukiwania.
- Zwiększ bezpieczeństwo sieci wirtualnej, umożliwiając zablokowanie eksfiltracji danych z sieci wirtualnej.
- Bezpiecznie łącz się z usługą wyszukiwania z sieci lokalnych, które łączą się z siecią wirtualną przy użyciu [sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) lub [Usługa expressroutes](../expressroute/expressroute-locations.md) z prywatną komunikację równorzędną.

> [!NOTE]
> Istnieją obecnie pewne ograniczenia w wersji zapoznawczej, z którymi należy się zapoznać:
> * Dostępne tylko dla usług Search Services w warstwie **podstawowa** . 
> * Dostępne w regionach zachodnie stany USA 2, zachodnie stany USA, Wschodnie stany US, Południowo-środkowe stany USA, Australia Wschodnia i Australia Południowo-Wschodnia.
> * Gdy punkt końcowy usługi jest prywatny, niektóre funkcje portalu są wyłączone. Będziesz mieć możliwość wyświetlania informacji o poziomie usług i zarządzania nimi, ale dostęp portalu do danych indeksu i różne składniki usługi, takie jak indeks, indeksator i definicje zestawu umiejętności, są ograniczone ze względów bezpieczeństwa.
> * Gdy punkt końcowy usługi jest prywatny, należy użyć [interfejsu API REST wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/) do przekazania dokumentów do indeksu.
> * Aby wyświetlić opcję obsługi prywatnych punktów końcowych w Azure Portal, należy użyć poniższego linku:https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Żądanie dostępu 

Kliknij pozycję [Zażądaj dostępu](https://aka.ms/SearchPrivateLinkRequestAccess) , aby zarejestrować się w tej funkcji w wersji zapoznawczej. Formularz żąda informacji o użytkowniku, firmie i ogólnej topologii sieci. Po przejrzeniu Twojego żądania otrzymasz wiadomość e-mail z potwierdzeniem z dodatkowymi instrukcjami.

## <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej

W tej sekcji utworzysz sieć wirtualną i podsieć do hostowania maszyny wirtualnej, która zostanie użyta w celu uzyskania dostępu do prywatnego punktu końcowego usługi wyszukiwania.

1. Na karcie Narzędzia główne Azure Portal wybierz pozycję **Utwórz zasób** > **Networking** > Sieć**sieci wirtualnej**.

1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *MyVirtualNetwork* |
    | Przestrzeń adresowa | Wprowadź *10.1.0.0/16* |
    | Subskrypcja | Wybierz swoją subskrypcję|
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy**, wprowadź nazwę *zasobu*, a następnie wybierz przycisk **OK** . |
    | Lokalizacja | Wybierz **zachodnie stany USA** lub niezależnie od regionu, którego używasz|
    | Podsieć — nazwa | Wprowadź *maskę* |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24* |
    |||

1. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Tworzenie usługi wyszukiwania za pomocą prywatnego punktu końcowego

W tej sekcji utworzysz nową usługę Wyszukiwanie poznawcze platformy Azure z prywatnym punktem końcowym. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **Sieć Web** > **Azure wyszukiwanie poznawcze**.

1. W obszarze **nowe Search Service — podstawowe**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Adres URL | Wprowadź unikatową nazwę. |
    | Lokalizacja | Wybierz region określony podczas żądania dostępu do tej funkcji w wersji zapoznawczej. |
    | Warstwa cenowa | Wybierz pozycję **Zmień warstwę cenową** , a następnie wybierz pozycję **podstawowa**. Ta warstwa jest wymagana na potrzeby wersji zapoznawczej. |
    |||
  
1. Wybierz pozycję **Dalej: skala**.

1. Pozostaw wartości domyślne, a następnie wybierz pozycję **Dalej: sieć**.

1. W obszarze **nowy Search Service — sieć**wybierz pozycję **prywatne** dla **połączenia punktu końcowego (dane)**.

1. W obszarze **nowy Search Service — sieć**wybierz pozycję **+ Dodaj** w obszarze **prywatny punkt końcowy**. 

1. W obszarze **Utwórz prywatny punkt końcowy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | Lokalizacja | wybierz pozycję **Zachodnie stany USA**.|
    | Nazwa | Wprowadź *myPrivateEndpoint*.  |
    | Docelowy zasób podrzędny | Pozostaw wartość domyślną **searchService**. |
    | **SIECI** |  |
    | Sieć wirtualna  | Wybierz pozycję *MyVirtualNetwork* *z grupy zasobów*. |
    | Podsieć | Wybierz pozycję Moja *podsieć*. |
    | **PRYWATNA INTEGRACJA Z USŁUGĄ DNS** |  |
    | Integracja z prywatną strefą DNS  | Pozostaw wartość domyślną **tak**. |
    | Prywatna strefa DNS  | Pozostaw wartość domyślną * * (New) privatelink.search.windows.net * *. |
    |||

1. Wybierz przycisk **OK**. 

1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację. 

1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**. 

1. Po zakończeniu aprowizacji nowej usługi przejdź do zasobu, który właśnie został utworzony.

1. Z menu po lewej stronie wybierz pozycję **klucze** .

1. Skopiuj **podstawowy klucz administratora** w celu późniejszego nawiązania połączenia z usługą.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **obliczeniowy** > **maszyny wirtualnej**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wprowadź *myVm*. |
    | Region | Wybierz pozycję **zachodnie stany USA** lub region, którego używasz. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter**. |
    | Rozmiar | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw domyślne ustawienie **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Pozostaw domyślny **protokół RDP (3389)**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **Nie**. |
    |||

1. Wybierz pozycję **Dalej: dyski**.

1. W obszarze **Utwórz maszynę wirtualną**, pozostaw wartości domyślne, a następnie wybierz pozycję **Dalej: sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **MyVirtualNetwork**.  |
    | Przestrzeń adresowa | Pozostaw wartość domyślną **10.1.0.0/24**.|
    | Podsieć | Pozostaw domyślną wartość moja **podsieć (10.1.0.0/24)**.|
    | Publiczny adres IP | Pozostaw wartość domyślną **(nowy) myVm-ip**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    ||

1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację.

1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**. 


## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

Pobierz *myVm* maszyny wirtualnej, a następnie połącz się z nią w następujący sposób:

1. Na pasku wyszukiwania portalu wprowadź *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz pobrany plik RDP *.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może być konieczne wybranie **pozycji więcej opcji** > **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  


## <a name="test-connections"></a>Testuj połączenia

W tej sekcji będziesz weryfikować dostęp do sieci prywatnej do usługi wyszukiwania i łączyć się z prywatnymi z prywatnym punktem końcowym.

Odwołaj się z wprowadzenia, że wszystkie interakcje z usługą wyszukiwania wymagają [interfejsu API REST wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/). Portal i zestaw .NET SDK nie są obsługiwane w tej wersji zapoznawczej.

1. W Pulpit zdalny *myVM*Otwórz program PowerShell.

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

1. Z poziomu maszyny wirtualnej Połącz się z usługą wyszukiwania i Utwórz indeks. Możesz skorzystać z tego [przewodnika Szybki Start](search-get-started-postman.md) , aby utworzyć nowy indeks wyszukiwania w usłudze w programie Poster przy użyciu interfejsu API REST. Konfigurowanie żądań od programu Poster wymaga punktu końcowego usługi wyszukiwania (https://[Search Service Name]. Search. Windows. NET) i Admin API-Key, które zostały skopiowane w poprzednim kroku.

1. Ukończenie szybkiego startu z maszyny wirtualnej to potwierdzenie, że usługa jest w pełni funkcjonalna.

1. Zamknij połączenie pulpitu zdalnego z *myVM*. 

1. Aby sprawdzić, czy usługa jest niedostępna w publicznym punkcie końcowym, Otwórz program Poster na lokalnej stacji roboczej i spróbuj wykonać kilka pierwszych zadań w przewodniku Szybki Start. Jeśli zostanie wyświetlony komunikat o błędzie, że serwer zdalny nie istnieje, pomyślnie skonfigurowano prywatny punkt końcowy dla usługi wyszukiwania.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Gdy skończysz korzystać z prywatnego punktu końcowego, usługi wyszukiwania i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby:
1. Wprowadź w polu **wyszukiwania** w górnej części portalu *i wybierz pozycję*  *moja zasobów z*wyników wyszukiwania. 
1. Wybierz pozycję **Usuń grupę zasobów**. 
1. Wprowadź wartość *webresourcename*  **, aby wpisać nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono MASZYNę wirtualną w sieci wirtualnej i usługę wyszukiwania z prywatnym punktem końcowym. Nawiązano połączenie z maszyną wirtualną z Internetu i bezpiecznie komunikuje się z usługą wyszukiwania za pomocą linku prywatnego. Aby dowiedzieć się więcej o prywatnym punkcie końcowym, zobacz [co to jest prywatny punkt końcowy platformy Azure?](../private-link/private-endpoint-overview.md).
