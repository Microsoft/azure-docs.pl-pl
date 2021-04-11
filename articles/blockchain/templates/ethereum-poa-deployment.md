---
title: Wdróż szablon rozwiązania typu "Weryfikacja urzędu certyfikacji Ethereum" na platformie Azure
description: Użyj rozwiązania Ethereum The-Authority Consortium do wdrożenia i skonfigurowania wieloskładnikowej sieci Ethereum konsorcjum na platformie Azure
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: f4b12a9b3b830fcc4f45cb5e957232fee5a756a3
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078649"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Wdróż szablon rozwiązania typu "Weryfikacja urzędu certyfikacji Ethereum" na platformie Azure

Za pomocą [szablonu rozwiązania Ethereum weryfikacji urzędu certyfikacji w wersji zapoznawczej](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) można wdrażać, konfigurować i zarządzać wieloskładnikową siecią Ethereum z obsługą urzędu certyfikacji z minimalnymi możliwościami platformy Azure i Ethereum.

Szablon rozwiązania może być używany przez każdego członka konsorcjum do aprowizacji łańcucha bloków sieci Microsoft Azure za pomocą usług obliczeniowych, sieciowych i magazynowych. Każdy element sieci członkowski konsorcjum składa się z zestawu węzłów modułu sprawdzania o zrównoważonym obciążeniu, z którymi aplikacja lub użytkownik może współdziałać w celu przesyłania transakcji Ethereum.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Wybierz rozwiązanie Azure łańcucha bloków

Przed wybraniem opcji użycia szablonu rozwiązania do Ethereum weryfikacji dla urzędu certyfikacji Porównaj swój scenariusz z typowymi przypadkami użycia dostępnych opcji usługi Azure łańcucha bloków.

> [!IMPORTANT]
> Rozważ użycie [usługi Azure łańcucha bloków Service](../service/overview.md) zamiast szablonu rozwiązania Ethereum na platformie Azure. Usługa Azure łańcucha bloków jest obsługiwaną zarządzaną usługą platformy Azure. Ethereum z parzystością przeszedł do projektowania i konserwacji na podstawie społeczności. Aby uzyskać więcej informacji, zobacz [przechodzenie z parzystości Ethereum do OPENETHEREUM DAO](https://www.parity.io/parity-ethereum-openethereum-dao/).

Opcja | Model usług | Typowy przypadek użycia
-------|---------------|-----------------
Szablony rozwiązań | IaaS | Szablony rozwiązań są Azure Resource Manager szablonów, których można użyć do aprowizacji w pełni skonfigurowanej topologii sieci łańcucha bloków. Szablony wdrażają i konfigurują Microsoft Azure usług obliczeniowych, sieci i magazynu dla danego typu sieci łańcucha bloków. Szablony rozwiązań są udostępniane bez umowy dotyczącej poziomu usług. Aby uzyskać pomoc techniczną, Skorzystaj z [&stronie pytań](/answers/topics/azure-blockchain-workbench.html) i odpowiedzi.
[Usługa Azure Blockchain](../service/overview.md) | PaaS | Usługa Azure łańcucha bloków w wersji zapoznawczej upraszcza tworzenie, zarządzanie i nadzór nad sieciami łańcucha bloków konsorcjum. Korzystaj z usługi Azure łańcucha bloków Service, aby uzyskać rozwiązania wymagające PaaS, zarządzania konsorcjum oraz prywatności umów i transakcji.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS i PaaS | Usługa Azure łańcucha bloków Workbench w wersji zapoznawczej to zbiór usług i funkcji platformy Azure, które ułatwiają tworzenie i wdrażanie aplikacji łańcucha bloków w celu udostępniania procesów i danych firmowych innym organizacjom. Użyj usługi Azure łańcucha bloków Workbench do tworzenia prototypów rozwiązania łańcucha bloków lub weryfikacji koncepcji łańcucha blokówowej aplikacji. Usługa Azure Blockchain Workbench jest świadczona bez umowy dotyczącej poziomu usług. Aby uzyskać pomoc techniczną, Skorzystaj z [&stronie pytań](/answers/topics/azure-blockchain-workbench.html) i odpowiedzi.

## <a name="solution-architecture"></a>Architektura rozwiązania

Korzystając z szablonu rozwiązania Ethereum, można wdrożyć jedną lub wiele regionów opartych na wieloregionowej sieci konsorcjum Ethereum.

![Architektura wdrożenia](./media/ethereum-poa-deployment/deployment-architecture.png)

Każde wdrożenie składowe konsorcjum obejmuje:

* Virtual Machines do uruchamiania modułów walidacji PoA
* Azure Load Balancer do dystrybuowania żądań usługi RPC, komunikacji równorzędnej i ładu DApp
* Azure Key Vault zabezpieczania tożsamości modułu sprawdzania poprawności
* Usługa Azure Storage do hostowania trwałych informacji o sieci i koordynowania dzierżawy
* Azure Monitor agregowania dzienników i statystyk wydajności
* Brama sieci wirtualnej (opcjonalnie) do zezwalania na połączenia VPN między prywatnym sieci wirtualnych

Domyślnie punkty końcowe wywołania RPC i komunikacji równorzędnej są dostępne za pośrednictwem publicznego adresu IP, aby umożliwić uproszczoną łączność między subskrypcjami i chmurami. W przypadku kontroli dostępu na poziomie aplikacji można użyć [kontraktów z uprawnieniami z parzystością](https://openethereum.github.io/Permissioning.html). Sieci, które są wdrożone za pośrednictwem sieci VPN, są obsługiwane w przypadku łączności między subskrypcjami. Ze względu na to, że wdrożenia VPN i sieci wirtualnych są bardziej skomplikowane, warto zacząć od publicznego modelu IP podczas tworzenia prototypów rozwiązania.

Kontenery platformy Docker są używane w celu zapewnienia niezawodności i modularności. Azure Container Registry służy do hostowania i obsługi obrazów z wersjami w ramach każdego wdrożenia. Obrazy kontenerów składają się z:

* Orchestrator — generuje tożsamości i kontrakty ładu. Przechowuje tożsamości w magazynie tożsamości.
* Parzystość klienta — dzierżawa jest tożsamość z magazynu tożsamości. Odnajduje i nawiązuje połączenie z elementami równorzędnymi.
* Agent EthStats — zbiera dzienniki lokalne i statystyki za pośrednictwem wywołania RPC i wypycha informacje do Azure Monitor.
* Ładu DApp — interfejs sieci Web służący do współpracy z kontraktami ładu.

### <a name="validator-nodes"></a>Węzły modułu sprawdzania poprawności

W protokole dowodu uwierzytelniania węzły modułu sprawdzania poprawności przenoszą miejsce tradycyjnych węzłów Miner. Każdy moduł sprawdzania poprawności ma unikatową tożsamość Ethereum pozwalającą na uczestnictwo w procesie tworzenia bloku. Każdy członek konsorcjum może zainicjować co najmniej dwa węzły modułu sprawdzania poprawności w pięciu regionach w celu zapewnienia nadmiarowości geograficznej. Węzły modułu sprawdzania poprawności komunikują się z innymi węzłami modułu sprawdzania poprawności w celu osiągnięcia konsensusu na stanie podstawowej księgi rozproszonej. Aby zapewnić uczciwe uczestnictwo w sieci, każdy członek konsorcjum jest zabroniony do używania więcej modułów sprawdzania poprawności niż pierwszy element członkowski w sieci. Na przykład, jeśli pierwszy element członkowski wdraża trzy moduły walidacji, każdy element członkowski może mieć maksymalnie trzy moduły walidacji.

### <a name="identity-store"></a>Magazyn tożsamości

Magazyn tożsamości jest wdrażany w ramach subskrypcji każdej składowej, która bezpiecznie przechowuje wygenerowane tożsamości Ethereum. Dla każdego modułu weryfikacji kontener aranżacji generuje klucz prywatny Ethereum i zapisuje go w Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Wdróż sieć Ethereum Consortium

W tym przewodniku przyjęto założenie, że tworzysz wielofirmową sieć konsorcjum Ethereum. Następujący przepływ jest przykładem wdrożenia wieloskładnikowego:

1. Trzy składowe generują konto Ethereum przy użyciu funkcji dbmask
1. *Składowa A* wdraża Ethereum POA, dostarczając swój publiczny adres Ethereum
1. *Członek A* zawiera adres URL konsorcjum do *składowej B* i *elementu członkowskiego C*
1. *Składowa B* i *element członkowski C* Deploy, Ethereum POA, dostarczając swój adres publiczny Ethereum i adres URL konsorcjum *elementu członkowskiego*
1. *Składowa* głosów w *członku B* jako administrator
1. *Członek a* i *członek B* *zarówno prawo* głosujące jako administrator

W następnych sekcjach przedstawiono sposób konfigurowania zasięgu pierwszego elementu członkowskiego w sieci.

### <a name="create-resource"></a>Tworzenie zasobu

W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób** w lewym górnym rogu.

Wybierz pozycję **łańcucha bloków**  >  **Ethereum weryfikacji urzędu certyfikacji (wersja zapoznawcza)**.

### <a name="basics"></a>Podstawy

W obszarze **podstawowe** Określ wartości parametrów standardowych dla każdego wdrożenia.

![Podstawy](./media/ethereum-poa-deployment/basic-blade.png)

Parametr | Opis | Przykładowa wartość
----------|-------------|--------------
Utwórz nową sieć lub Dołącz do istniejącej sieci | Można utworzyć nową sieć konsorcjum lub dołączyć do istniejącej sieci konsorcjum. Dołączenie do istniejącej sieci wymaga dodatkowych parametrów. | Tworzenie nowego elementu
Adres e-mail | Otrzymasz powiadomienie e-mail po zakończeniu wdrożenia z informacjami o danym wdrożeniu. | Prawidłowy adres e-mail
Nazwa użytkownika maszyny wirtualnej | Nazwa użytkownika administratora każdej wdrożonej maszyny wirtualnej | 1-64 znaków alfanumerycznych
Typ uwierzytelniania | Metoda do uwierzytelnienia na maszynie wirtualnej. | Hasło
Hasło | Hasło dla konta administratora dla każdej wdrożonej maszyny wirtualnej. Wszystkie maszyny wirtualne mają początkowo to samo hasło. Hasło można zmienić po zainicjowaniu obsługi administracyjnej. | 12-72 znaków 
Subskrypcja | Subskrypcja, do której ma zostać wdrożona sieć konsorcjum |
Grupa zasobów| Grupa zasobów, w której ma zostać wdrożona sieć konsorcjum. | myResourceGroup
Lokalizacja | Region świadczenia usługi Azure dla grupy zasobów. | Zachodnie stany USA 2

Wybierz przycisk **OK**.

### <a name="deployment-regions"></a>Regiony wdrożenia

W obszarze *regiony wdrożenia* Określ liczbę regionów i lokalizacji dla każdego z nich. Można wdrożyć maksymalnie pięć regionów. Pierwszy region powinien odpowiadać lokalizacji grupy zasobów z sekcji *podstawowe* . W przypadku sieci programistycznych lub testowych można użyć jednego regionu na członka. W przypadku produkcji należy wdrożyć w dwóch lub większej liczbie regionów w celu zapewnienia wysokiej dostępności.

![regiony wdrożenia](./media/ethereum-poa-deployment/deployment-regions.png)

Parametr | Opis | Przykładowa wartość
----------|-------------|--------------
Liczba regionów|Liczba regionów do wdrożenia sieci konsorcjum| 2
Pierwszy region | Pierwszy region wdrożenia sieci konsorcjum | Zachodnie stany USA 2
Drugi region | Drugi region do wdrożenia sieci konsorcjum. Dodatkowe regiony są widoczne, gdy liczba regionów jest równa co najmniej dwa. | Wschodnie stany USA 2

Wybierz przycisk **OK**.

### <a name="network-size-and-performance"></a>Rozmiar i wydajność sieci

W obszarze *rozmiar sieci i wydajność* Określ dane wejściowe rozmiaru sieci konsorcjum. Rozmiar magazynu węzła modułu sprawdzania poprawności określa potencjalną wielkość łańcucha bloków. Rozmiar można zmienić po wdrożeniu.

![Rozmiar i wydajność sieci](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parametr | Opis | Przykładowa wartość
----------|-------------|--------------
Liczba węzłów modułu sprawdzania z równoważeniem obciążenia | Liczba węzłów modułu sprawdzania poprawności do zainicjowania obsługi administracyjnej w ramach sieci. | 2
Wydajność magazynu węzłów modułu sprawdzania poprawności | Typ dysku zarządzanego dla każdego ze wdrożonych węzłów modułu sprawdzania poprawności. Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Storage](https://azure.microsoft.com/pricing/details/managed-disks/) | Dysk SSD w warstwie Standardowa
Rozmiar maszyny wirtualnej węzła modułu sprawdzania poprawności | Rozmiar maszyny wirtualnej używany dla węzłów modułu sprawdzania poprawności. Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Virtual](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) Machines | Standardowa D2 v3

Warstwy maszyny wirtualnej i magazynu mają wpływ na wydajność sieci.  Skorzystaj z poniższej tabeli, aby uzyskać pomoc w zakresie kosztów:

Jednostka SKU maszyny wirtualnej|Warstwa magazynowania|Cena|Przepływność|Opóźnienie
---|---|---|---|---
F1|Dysk SSD w warstwie Standardowa|małą|małą|wysoka
D2_v3|Dysk SSD w warstwie Standardowa|średni|średni|średni
F16s|Dysk SSD w warstwie Premium|wysoka|wysoka|małą

Wybierz przycisk **OK**.

### <a name="ethereum-settings"></a>Ustawienia Ethereum

W obszarze *Ustawienia Ethereum* Określ ustawienia konfiguracji związane z Ethereum.

![Ustawienia Ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

Parametr | Opis | Przykładowa wartość
----------|-------------|--------------
Identyfikator członka konsorcjum | Identyfikator skojarzony z każdym członkiem uczestniczącym w sieci konsorcjum. Służy do konfigurowania przestrzeni adresów IP w celu uniknięcia kolizji. W przypadku sieci prywatnej identyfikator elementu członkowskiego musi być unikatowy w różnych organizacjach w tej samej sieci.  Unikatowy identyfikator elementu członkowskiego jest wymagany nawet wtedy, gdy ta sama organizacja jest wdrażana w wielu regionach. Zanotuj wartość tego parametru, ponieważ musisz udostępnić go innym członkom, aby upewnić się, że nie ma kolizji. Prawidłowy zakres to od 0 do 255. | 0
Identyfikator sieci | Identyfikator sieci dla wdrażanej sieci Ethereum. Każda sieć Ethereum ma swój własny identyfikator sieci, a 1 to identyfikator sieci publicznej. Prawidłowy zakres to od 5 do 999 999 999 | 10101010
Adres Ethereum administratora | Adres konta Ethereum używany przez uczestnictwo w ładu PoA. W celu wygenerowania adresu Ethereum można użyć elementubinding. |
Opcje zaawansowane | Opcje zaawansowane dla ustawień Ethereum | Włącz
Wdróż przy użyciu publicznego adresu IP | Jeśli wybrana jest prywatna sieć wirtualna, Sieć zostanie wdrożona za bramą sieci wirtualnej i zostanie usunięta funkcja dostępu równorzędnego. W przypadku prywatnej sieci wirtualnej wszystkie elementy członkowskie muszą używać bramy sieci wirtualnej, aby połączenie było zgodne. | Publiczny adres IP
Limit blokowania gazu | Limit wychodzący z bloków wyjściowych sieci. | 50000000
Okres ponownego pieczętowania bloku (s) | Częstotliwość tworzenia pustych bloków, gdy nie ma żadnych transakcji w sieci. Wyższa częstotliwość będzie miała szybszy wzrost, ale zwiększy się koszty magazynowania. | 15
Kontrakt uprawnień transakcji | Kod bajtowy dla kontraktu uprawniającego do transakcji. Ogranicza wdrożenie i wykonywanie kontraktu inteligentnego do listy dozwolonych kont Ethereum. |

Wybierz przycisk **OK**.

### <a name="monitoring"></a>Monitorowanie

Monitorowanie umożliwia skonfigurowanie zasobu dziennika dla sieci. Agent monitorowania zbiera i wyświetla użyteczne metryki i dzienniki z sieci, co zapewnia możliwość szybkiego sprawdzania kondycji sieci lub problemów z debugowaniem.

![Monitor platformy Azure](./media/ethereum-poa-deployment/azure-monitor.png)

Parametr | Opis | Przykładowa wartość
----------|-------------|--------------
Monitorowanie | Opcja włączania monitorowania | Włącz
Nawiązywanie połączenia z istniejącymi dziennikami Azure Monitor | Opcja tworzenia nowego wystąpienia dzienników Azure Monitor lub łączenia istniejącego wystąpienia | Tworzenie nowego elementu
Lokalizacja | Region, w którym jest wdrożone nowe wystąpienie | East US
Istniejący identyfikator obszaru roboczego usługi log Analytics (Połącz z istniejącymi dziennikami Azure Monitor = Dołącz istniejący)|Identyfikator obszaru roboczego istniejącego wystąpienia dzienników Azure Monitor|NA
Istniejący klucz podstawowy usługi log Analytics (łączenie z istniejącymi dziennikami Azure Monitor = Połącz istniejące)|Klucz podstawowy używany do nawiązywania połączenia z istniejącym wystąpieniem dzienników Azure Monitor|NA

Wybierz przycisk **OK**.

### <a name="summary"></a>Podsumowanie

Kliknij podsumowanie, aby przejrzeć określone dane wejściowe i uruchomić podstawową weryfikację przed wdrożeniem. Przed wdrożeniem można pobrać szablon i parametry.

Wybierz pozycję **Utwórz** do wdrożenia.

Jeśli wdrożenie obejmuje bramy sieci wirtualnej, wdrożenie może potrwać od 45 do 50 minut.

## <a name="deployment-output"></a>Dane wyjściowe wdrożenia

Po zakończeniu wdrażania można uzyskać dostęp do wymaganych parametrów przy użyciu Azure Portal.

### <a name="confirmation-email"></a>Wiadomość e-mail z potwierdzeniem

Jeśli podano adres e-mail ([sekcja podstawy](#basics)), zostanie wysłana wiadomość e-mail zawierająca informacje o wdrożeniu i linki do tej dokumentacji.

![adres e-mail wdrożenia](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Po pomyślnym zakończeniu wdrożenia i udostępnieniu wszystkich zasobów można wyświetlić parametry wyjściowe w grupie zasobów.

1. Przejdź do grupy zasobów w portalu.
1. Wybierz kolejno pozycje **przegląd > wdrożenia**.

    ![Przegląd grupy zasobów](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Wybierz pozycję **Microsoft-Azure-łańcucha bloków. Azure-łańcucha bloków-eter-...** wdrożenia.
1. Wybierz sekcję dane **wyjściowe** .

    ![Dane wyjściowe wdrożenia](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Rosnąco konsorcjum

Aby rozszerzyć konsorcjum, należy najpierw połączyć sieć fizyczną. W przypadku wdrażania za pośrednictwem sieci VPN Zobacz sekcję [łączenie bramy sieci wirtualnej](#connecting-vnet-gateways) Konfigurowanie połączenia sieciowego w ramach wdrożenia nowego elementu członkowskiego. Po zakończeniu wdrożenia Użyj [DApp ładu](#governance-dapp) , aby zostać administratorem sieci.

### <a name="new-member-deployment"></a>Wdrożenie nowego elementu członkowskiego

Udostępnij następujące informacje z przyłączanym członkiem. Informacje znajdują się w wiadomości e-mail po wdrożeniu lub w danych wyjściowych wdrożenia portalu.

* Adres URL danych konsorcjum
* Liczba wdrożonych węzłów
* Identyfikator zasobu bramy sieci wirtualnej (w przypadku korzystania z sieci VPN)

Element członkowski wdrożenia powinien korzystać z tego samego szablonu rozwiązania Ethereum weryfikacji dla urzędu certyfikacji podczas wdrażania ich obecności w sieci przy użyciu następujących wskazówek:

* Wybierz **sprzężenie istniejące**
* Wybierz tę samą liczbę węzłów modułu sprawdzania poprawności jako resztę elementów członkowskich w sieci, aby zapewnić rzetelną reprezentację
* Użyj tego samego adresu administratora Ethereum
* Użyj podanego *adresu URL danych konsorcjum* w *ustawieniach Ethereum*
* Jeśli pozostała część sieci należy do sieci VPN, wybierz pozycję **prywatna sieć wirtualna** w sekcji Zaawansowane.

### <a name="connecting-vnet-gateways"></a>Łączenie bram sieci wirtualnej

Ta sekcja jest wymagana tylko w przypadku wdrożenia przy użyciu prywatnej sieci wirtualnej. Jeśli używasz publicznych adresów IP, możesz pominąć tę sekcję.

W przypadku sieci prywatnej różne elementy członkowskie są połączone za pośrednictwem połączeń bramy sieci wirtualnej. Zanim członek będzie mógł dołączyć do sieci i wyświetlić ruch związany z transakcjami, istniejący element członkowski musi wykonać ostateczną konfigurację na swojej bramie sieci VPN, aby zaakceptować połączenie. Węzły Ethereum należące do elementu członkowskiego sprzęgania nie będą działać, dopóki nie zostanie nawiązane połączenie. Aby zmniejszyć prawdopodobieństwo single point of failure, należy utworzyć nadmiarowe połączenia sieciowe w konsorcjum.

Po wdrożeniu nowego elementu członkowskiego istniejący element członkowski musi zakończyć połączenie dwukierunkowe przez skonfigurowanie połączenia bramy sieci wirtualnej z nowym członkiem. Istniejący element członkowski wymaga:

* Identyfikator zasobu bramy sieci wirtualnej łączący się z członkiem. Zobacz [dane wyjściowe wdrożenia](#deployment-output).
* Klucz połączenia udostępnionego.

Istniejący element członkowski musi uruchomić następujący skrypt programu PowerShell, aby zakończyć połączenie. Możesz użyć Azure Cloud Shell znajdującego się w prawym górnym pasku nawigacyjnym w portalu.

![Usługa Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="governance-dapp"></a>DApp ładu

W celu potwierdzenia uwierzytelnienia jest zdecentralizowane ładu. Ponieważ potwierdzenie urzędu jest zależne od dozwolonej listy urzędów sieciowych w celu zapewnienia dobrej kondycji sieci, ważne jest, aby zapewnić sprawiedliwy mechanizm wprowadzania modyfikacji tej listy uprawnień. Każde wdrożenie jest dostarczane z zestawem inteligentnych kontraktów i portalu na potrzeby nadzoru w łańcuchu na tej liście dozwolonych. Gdy proponowana zmiana osiągnie większość głosów przez członków konsorcjum, zmiana zostanie wdrożona. Głosowanie pozwala na dodawanie lub naruszenie przez uczestników nowych uczestników w przejrzysty sposób, który zachęca do uczciwej sieci.

DApp ładu to zestaw wstępnie wdrożonych, [inteligentnych kontraktów](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) i aplikacji sieci Web, które są używane do zarządzania władzami w sieci. Urzędy są podzielone na tożsamości administratora i węzły modułu sprawdzania poprawności.
Administratorzy mogą delegować jednomyślne uczestnictwo w zestawie węzłów modułu sprawdzania poprawności. Administratorzy mogą również zagłosować innych administratorów do sieci lub z niej.

![DApp ładu](./media/ethereum-poa-deployment/governance-dapp.png)

* **Zdecentralizowane ładu:** Zmiany w urzędach sieciowych są zarządzane za pomocą głosowania w łańcuchu przez wybieranie administratorów.
* **Delegowanie modułu weryfikacji:** Urzędy mogą zarządzać węzłami modułu sprawdzania poprawności skonfigurowanymi w każdym wdrożeniu PoA.
* **Historia zmian podlegających inspekcji:** Każda zmiana jest rejestrowana w łańcucha bloków, zapewniając przejrzystość i inspekcję.

### <a name="getting-started-with-governance"></a>Wprowadzenie do ładu

Do wykonania dowolnego rodzaju transakcji za pomocą DApp ładu należy użyć portfela Ethereum. Najbardziej proste podejście polega na użyciu portfela w przeglądarce, takiego jak [Dbmasking](https://metamask.io); Jednak ponieważ te inteligentne kontrakty są wdrażane w sieci, można także zautomatyzować interakcje z umową ładu.

Po zainstalowaniu maski, przejdź do ładu DApp w przeglądarce.  Adres URL można znaleźć za pomocą Azure Portal w danych wyjściowych wdrożenia.  Jeśli nie masz zainstalowanej portfela w przeglądarce, nie będzie można wykonywać żadnych akcji; można jednak wyświetlić stan administratora.  

### <a name="becoming-an-admin"></a>Stać się administratorem

Jeśli jesteś pierwszym członkiem, który został wdrożony w sieci, automatycznie staniesz się administratorem, a węzły parzystości są wyświetlane jako moduły walidacji. Jeśli dołączysz do sieci, musisz otrzymać głos jako administratora z większością (ponad 50%) istniejącego zestawu administracyjnego. Jeśli nie chcesz stać się administratorem, węzły nadal będą synchronizowane i weryfikują łańcucha bloków; jednak nie uczestniczą w procesie tworzenia bloku. Aby rozpocząć proces głosowania jako administrator, wybierz pozycję **nominacja** i wprowadź adres Ethereum oraz Alias.

![Nominowanie](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Sugesti

Wybranie karty **kandydatów** pokazuje bieżący zestaw administratorów kandydujących.  Gdy kandydat osiągnie większość głosów od bieżących administratorów, kandydat zostanie podwyższony do administratora.  Aby zagłosować na kandydata, zaznacz wiersz i wybierz pozycję **głos w**. Jeśli zmienisz zdanie w głosowaniu, wybierz kandydata i wybierz pozycję **odwołaj głos**.

![Sugesti](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administratorzy

Na karcie **administratorzy** jest wyświetlany bieżący zestaw administratorów, który umożliwia głosowanie.  Gdy administrator utraci więcej niż 50%, zostanie usunięty jako administrator w sieci. Wszystkie węzły modułu sprawdzania poprawności, które są w stanie utracić stan modułu sprawdzania poprawności i stają się węzłami transakcji w sieci. Administrator może zostać usunięty z dowolnej liczby powodów; jednak przede wszystkim konsorcjum wyraża zgodę na zasady z góry.

![Administratorzy](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Modułów sprawdzania

Wybranie karty **moduły sprawdzania poprawności** powoduje wyświetlenie bieżących wdrożonych węzłów parzystości dla wystąpienia i ich bieżącego stanu (typ węzła). Każdy członek konsorcjum ma inny zestaw modułów sprawdzania poprawności na tej liście, ponieważ ten widok reprezentuje aktualnie wdrożony element członkowski konsorcjum. Jeśli wystąpienie jest nowo wdrożone i nie dodano żadnych modułów sprawdzania poprawności, uzyskasz możliwość **dodania modułów walidacji**. Dodanie modułów walidacji automatycznie wybiera zestaw węzłów parzystości z zrównoważonym regionem i przypisuje je do zestawu modułów walidacji. Jeśli wdrożono więcej węzłów niż dozwolona pojemność, pozostałe węzły stają się węzłami transakcji w sieci.

Adres każdego modułu sprawdzania poprawności jest automatycznie przypisywany za pośrednictwem [magazynu tożsamości](#identity-store) na platformie Azure.  Jeśli węzeł ulegnie awarii, zwalnia jego tożsamość, umożliwiając innym węzłom wdrożenia. Ten proces zapewnia wysoką dostępność tego konsensusu.

![Modułów sprawdzania](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Nazwa konsorcjum

Każdy administrator może zaktualizować nazwę konsorcjum.  Wybierz ikonę koła zębatego w lewym górnym rogu, aby zaktualizować nazwę konsorcjum.

### <a name="account-menu"></a>Menu konto

W prawym górnym rogu jest alias konta Ethereum i identicon.  Jeśli jesteś administratorem, możesz zaktualizować alias.

![Konto](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie<a id="tutorials"></a>

W przypadku usługi Azure łańcucha bloków News odwiedź [blog usługi Azure łańcucha bloków](https://azure.microsoft.com/blog/topics/blockchain/) , aby zachować aktualność w zakresie usług łańcucha bloków i uzyskać informacje od zespołu inżynierów ds. platformy Azure łańcucha bloków.

Aby przekazać opinie o produkcie lub zażądać nowych funkcji, opublikuj lub zagłosuj na pomysł za pośrednictwem [forum opinii platformy Azure dla usługi łańcucha bloków](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Pomoc techniczna w społeczności

Współpracuj z inżynierami firmy Microsoft i ekspertów społeczności Azure łańcucha bloków.

* [Microsoft Q&strony pytania](/answers/topics/azure-blockchain-workbench.html). Wsparcie inżynieryjne dla szablonów łańcucha bloków jest ograniczone do problemów z wdrażaniem.
* [Witryna Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Następne kroki

Więcej rozwiązań łańcucha bloków platformy Azure można znaleźć w [dokumentacji usługi Azure łańcucha bloków](../index.yml).
