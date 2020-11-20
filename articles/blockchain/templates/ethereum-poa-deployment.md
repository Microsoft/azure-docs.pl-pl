---
title: Wdróż szablon rozwiązania typu "Weryfikacja urzędu certyfikacji Ethereum" na platformie Azure
description: Użyj rozwiązania Ethereum The-Authority Consortium do wdrożenia i skonfigurowania wieloskładnikowej sieci Ethereum konsorcjum na platformie Azure
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: devx-track-js
ms.openlocfilehash: 5bbfca4d890440574ee6717ca910969226fc781a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987069"
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

Domyślnie punkty końcowe wywołania RPC i komunikacji równorzędnej są dostępne za pośrednictwem publicznego adresu IP, aby umożliwić uproszczoną łączność między subskrypcjami i chmurami. W przypadku kontroli dostępu na poziomie aplikacji można użyć kontraktów z uprawnieniami z parzystością. Sieci, które są wdrożone za pośrednictwem sieci VPN, są obsługiwane w przypadku łączności między subskrypcjami. Ze względu na to, że wdrożenia VPN i sieci wirtualnych są bardziej skomplikowane, warto zacząć od publicznego modelu IP podczas tworzenia prototypów rozwiązania.

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
Istniejący identyfikator obszaru roboczego usługi log Analytics (Połącz z istniejącymi dziennikami Azure Monitor = Dołącz istniejący)|Identyfikator obszaru roboczego istniejącego wystąpienia dzienników Azure Monitor||Nie dotyczy
Istniejący klucz podstawowy usługi log Analytics (łączenie z istniejącymi dziennikami Azure Monitor = Połącz istniejące)|Klucz podstawowy używany do nawiązywania połączenia z istniejącym wystąpieniem dzienników Azure Monitor||Nie dotyczy

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

## <a name="service-monitoring"></a>Monitorowanie usługi

Portal Azure Monitor można znaleźć, korzystając z linku w wiadomości e-mail dotyczącej wdrożenia lub lokalizacji parametru w danych wyjściowych wdrożenia [OMS_PORTAL_URL].

W portalu będzie najpierw wyświetlana statystyka sieci wysokiego poziomu i Omówienie węzła.

![Kategorie monitora](./media/ethereum-poa-deployment/monitor-categories.png)

Wybór **węzła przegląd**  przedstawia statystykę infrastruktury na węzeł.

![Statystyka węzła](./media/ethereum-poa-deployment/node-stats.png)

Wybranie pozycji **Statystyka sieci** pokazuje statystykę sieci Ethereum.

![Statystyka sieci](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Możesz badać dzienniki monitorowania, aby zbadać błędy lub alerty dotyczące progów ustawień. Następujące zapytania są przykładami, które można uruchomić w narzędziu *Wyszukiwanie dzienników* :

Bloki list, które zostały zgłoszone przez więcej niż jedno zapytanie modułu sprawdzania poprawności, mogą być przydatne do znajdowania rozwidleniów łańcucha.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Pobierz średnią liczbę elementów równorzędnych dla określonego węzła modułu sprawdzania poprawności średniego z 5-minutowych zasobników.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>Dostęp SSH

Ze względów bezpieczeństwa dostęp do portu SSH jest domyślnie odrzucony przez regułę zabezpieczeń grupy sieciowej. Aby uzyskać dostęp do wystąpień maszyn wirtualnych w sieci PoA, należy zmienić następujące zabezpieczenia reguły, aby *zezwolić*.

1. Przejdź do sekcji **Omówienie** wdrożonej grupy zasobów w Azure Portal.

    ![Omówienie protokołu SSH](./media/ethereum-poa-deployment/ssh-overview.png)

1. Wybierz **grupę zabezpieczeń sieci** dla regionu maszyny wirtualnej, do której chcesz uzyskać dostęp.

    ![sieciowej grupy zabezpieczeń SSH](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Wybierz regułę **zezwalania na używanie protokołu SSH** .

    ![Przechwytywanie ekranu zawiera okno przegląd z wybraną obsługą protokołu SSH.](./media/ethereum-poa-deployment/ssh-allow.png)

1. Zmień **akcję** na **Zezwalaj**

    ![Zezwalaj na włączanie protokołu SSH](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Wybierz pozycję **Zapisz**. Zastosowanie zmian może potrwać kilka minut.

Można zdalnie nawiązać połączenie z maszynami wirtualnymi dla węzłów modułu sprawdzania poprawności za pośrednictwem protokołu SSH z podaną nazwą użytkownika administratora i hasła/klucza SSH. Polecenie SSH, aby uzyskać dostęp do pierwszego węzła modułu sprawdzania poprawności, znajduje się na liście wyjściowej wdrożenia szablonu. Przykład:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Aby przejść do dodatkowych węzłów transakcji, Zwiększ numer portu o jeden.

Jeśli wdrożono w więcej niż jednym regionie, należy zmienić polecenie na nazwę DNS lub adres IP modułu równoważenia obciążenia w tym regionie. Aby znaleźć nazwę DNS lub adres IP innych regionów, Znajdź zasób z konwencją nazewnictwa **\* \* \* \* \* -lbpip-reg \#** i Wyświetl jego właściwości nazwy DNS i adresu IP.

## <a name="azure-traffic-manager-load-balancing"></a>Równoważenie obciążenia Traffic Manager platformy Azure

Usługa Azure Traffic Manager może pomóc w ograniczeniu przestoju i skrócić czas odpowiedzi sieci PoA przez kierowanie ruchu przychodzącego do wielu wdrożeń w różnych regionach. Wbudowane funkcje kontroli kondycji i automatycznego ponownego routingu pomagają zapewnić wysoką dostępność punktów końcowych RPC i zarządzanie DApp. Ta funkcja jest przydatna, jeśli wdrożono w wielu regionach i są gotowe do produkcji.

Użyj Traffic Manager, aby poprawić dostępność sieci PoA przy użyciu automatycznej pracy awaryjnej. Możesz również użyć Traffic Manager, aby zwiększyć czas odpowiedzi sieci przez kierowanie użytkowników końcowych do lokalizacji platformy Azure z najniższym opóźnieniem sieci.

Jeśli zdecydujesz się utworzyć profil Traffic Manager, możesz użyć nazwy DNS profilu, aby uzyskać dostęp do sieci. Po dodaniu innych członków konsorcjum do sieci Traffic Manager może również służyć do równoważenia obciążenia w ramach wdrożonych modułów walidacji.

### <a name="creating-a-traffic-manager-profile"></a>Tworzenie profilu Traffic Manager

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób** w lewym górnym rogu.
1. Wyszukaj **profil Traffic Manager**.

    ![Wyszukaj w usłudze Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Nadaj profilowi unikatową nazwę i wybierz grupę zasobów, która została użyta do wdrożenia PoA.

1. Wybierz pozycję **Utwórz** do wdrożenia.

    ![Utwórz Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Po wdrożeniu wybierz wystąpienie w grupie zasobów. Nazwę DNS, aby uzyskać dostęp do usługi Traffic Manager, można znaleźć na karcie Przegląd.

    ![Lokalizowanie usługi Traffic Manager DNS](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Wybierz kartę **punkty końcowe** i wybierz przycisk **Dodaj** .
1. Określ unikatową nazwę punktu końcowego.
1. W obszarze **Typ zasobu docelowego** wybierz pozycję **publiczny adres IP**.
1. Wybierz publiczny adres IP modułu równoważenia obciążenia pierwszego regionu.

    ![Routing Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Powtórz te czynności dla każdego regionu w wdrożonej sieci. Gdy punkty końcowe są w stanie **włączonym** , są one automatycznie ładowane i są bilansowane według nazwy DNS usługi Traffic Manager. Teraz można użyć tej nazwy DNS zamiast parametru [CONSORTIUM_DATA_URL] w innych krokach artykułu.

## <a name="data-api"></a>Interfejs API danych

Każdy członek konsorcjum hostuje informacje niezbędne do nawiązania połączenia z siecią przez inne osoby. Aby zapewnić łatwość łączności, każdy element członkowski hostuje zestaw informacji o połączeniu w punkcie końcowym interfejsu API danych.

Istniejący element członkowski zawiera [CONSORTIUM_DATA_URL] przed wdrożeniem elementu członkowskiego. Po wdrożeniu członek łączący będzie pobierał informacje z interfejsu JSON w następującym punkcie końcowym:

`<CONSORTIUM_DATA_URL>/networkinfo`

Odpowiedź zawiera informacje przydatne do przyłączania elementów członkowskich (Genesis Block, walidator Set Contract ABI, bootnodes) i informacje przydatne dla istniejącego elementu członkowskiego (adresy walidacji). Można użyć tej standaryzacji, aby zwiększyć konsorcjum między dostawcami chmury. Ten interfejs API zwraca odpowiedź sformatowaną w formacie JSON o następującej strukturze:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

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

## <a name="ethereum-development"></a>Programowanie Ethereum<a id="tutorials"></a>

Aby kompilować, wdrażać i testować inteligentne kontrakty, poniżej przedstawiono kilka opcji, które można wziąć pod uwagę podczas opracowywania Ethereum:
* [Pakiet Truffle](https://www.trufflesuite.com/docs/truffle/overview) — środowisko deweloperskie Ethereum oparte na kliencie
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) — środowisko programistyczne oparte na przeglądarce i lokalnej Ethereum

### <a name="compile-deploy-and-execute-smart-contract"></a>Kompilowanie, wdrażanie i wykonywanie kontraktu inteligentnego

W poniższym przykładzie utworzysz prosty kontrakt inteligentny. Użyj Truffle, aby skompilować i wdrożyć inteligentny kontrakt do sieci łańcucha bloków. Po wdrożeniu wywoływana jest funkcja kontraktu inteligentnego za pośrednictwem transakcji.

#### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj środowisko [Python 2.7.15](https://www.python.org/downloads/release/python-2715/). Środowisko Python jest niezbędne dla Truffle i Web3. Wybierz opcję Zainstaluj, aby dołączyć Język Python do ścieżki.
* Zainstaluj Truffle v 5.0.5 `npm install -g truffle@v5.0.5` . Truffle wymaga zainstalowania kilku narzędzi, w tym [Node.js](https://nodejs.org), [git](https://git-scm.com/). Aby uzyskać więcej informacji, zobacz [dokumentację Truffle](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Utwórz projekt Truffle

Zanim będzie możliwe skompilowanie i wdrożenie kontraktu inteligentnego, należy utworzyć projekt Truffle.

1. Otwórz wiersz polecenia lub powłokę.
1. Utwórz folder o nazwie `HelloWorld`.
1. Zmień katalog na nowy `HelloWorld` folder.
1. Zainicjuj nowy projekt Truffle za pomocą polecenia `truffle init` .

    ![Utwórz nowy projekt Truffle](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Dodawanie kontraktu inteligentnego

Twórz inteligentne kontrakty w podkatalogu **kontraktów** projektu Truffle.

1. Utwórz plik o nazwie `postBox.sol` w podkatalogu **kontraktów** projektu Truffle.
1. Dodaj następujący kod kryjący do **postBox. peruwiański**.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Wdrażanie kontraktu inteligentnego za pomocą Truffle

Projekty Truffle zawierają plik konfiguracyjny łańcucha bloków szczegóły połączenia sieciowego. Zmodyfikuj plik konfiguracji w taki sposób, aby zawierał informacje o połączeniu dla sieci.

> [!WARNING]
> Nigdy nie wysyłaj klucza prywatnego Ethereum przez sieć. Upewnij się, że każda transakcja jest podpisana lokalnie, a podpisana transakcja jest wysyłana za pośrednictwem sieci.

1. Potrzebujesz frazy dla [konta administratora Ethereum używanego podczas wdrażania sieci łańcucha bloków](#ethereum-settings). Jeśli do utworzenia konta użyto symboli, możesz pobrać wartość z maski. Wybierz ikonę konta administratora w prawym górnym rogu rozszerzenia dbmask i wybierz pozycję **ustawienia > zabezpieczenia & prywatność > Odsłoń słowa inicjatora**.
1. Zastąp zawartość `truffle-config.js` w projekcie Truffle następującą zawartością. Zastąp symbol zastępczy i wartości.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Ponieważ korzystamy z dostawcy portfela Truffle HD, zainstaluj moduł w projekcie za pomocą polecenia `npm install truffle-hdwallet-provider --save` .

Truffle używa skryptów migracji do wdrożenia inteligentnych kontraktów w sieci łańcucha bloków. Do wdrożenia nowego kontraktu inteligentnego potrzebny jest skrypt migracji.

1. Dodaj nową migrację, aby wdrożyć nowy kontrakt. Utwórz plik `2_deploy_contracts.js` w podkatalogu **migracji** projektu Truffle.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Wdróż w sieci PoA przy użyciu polecenia Truffle migracji. W wierszu polecenia w katalogu projektu Truffle Uruchom polecenie:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Wywoływanie funkcji kontraktu inteligentnego

Po wdrożeniu kontraktu inteligentnego można wysłać transakcję, aby wywołać funkcję.

1. W katalogu projektu Truffle Utwórz nowy plik o nazwie `sendtransaction.js` .
1. Dodaj następującą zawartość do **sendtransaction.js**.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Wykonaj skrypt przy użyciu polecenia Truffle Execute.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Wykonaj skrypt, aby wywołać funkcję za pośrednictwem transakcji](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Obsługa zestawu webassembly (WASM)

Obsługa zestawu webassembly jest już włączona dla nowo wdrożonych sieci PoA. Umożliwia ona programowanie aplikacji inteligentnych w dowolnym języku, który transstertuje Web-Assembly (Rust, C, C++). Aby uzyskać więcej informacji, zobacz [samouczek od firmy z parzystością techniczną](https://github.com/paritytech/pwasm-tutorial).

## <a name="faq"></a>Często zadawane pytania

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Zauważ, że w sieci nie wysłano wielu transakcji. Skąd pochodzą?

Odblokowanie [osobistego interfejsu API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)jest niebezpieczne. Botów nasłuchuje odblokowanych kont Ethereum i próbuje opróżnić środki. Bot zakłada, że te konta zawierają rzeczywiste i próbują być pierwszym, aby Siphon saldo. Nie należy włączać osobistego interfejsu API w sieci. Zamiast tego Przedstaw transakcje ręcznie przy użyciu portfela, takiego jak dbmaske lub programowo.

### <a name="how-to-ssh-onto-a-vm"></a>Jak SSH na maszynę wirtualną?

Port SSH nie jest narażony ze względów bezpieczeństwa. Postępuj zgodnie z [tym przewodnikiem, aby włączyć port SSH](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Jak mogę skonfigurować członka lub węzły transakcji inspekcji?

Węzły transakcji to zbiór klientów z parzystością, które są połączone z siecią, ale nie uczestniczą w konsensusie. Te węzły mogą nadal służyć do przesyłania transakcji Ethereum i odczytywania stanu kontraktu inteligentnego. Ten mechanizm działa w celu zapewnienia inspekcji dla członków konsorcjum bez urzędu certyfikacji w sieci. Aby to osiągnąć, postępuj zgodnie z instrukcjami w temacie [rozwój konsorcjum](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Dlaczego transakcje związane z maską są długotrwałe?

Aby zapewnić, że transakcje są odbierane w odpowiedniej kolejności, każda transakcja Ethereum ma przyrostowy identyfikator jednorazowy. Jeśli w innej sieci użyto konta w usłudze maskującej, należy zresetować wartość nonce. Kliknij ikonę ustawień (trzy paski), ustawienia, zresetuj konto. Historia transakcji zostanie wyczyszczona, a teraz można ponownie przesłać transakcję.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Czy muszę określić opłatę za gaz w ramach maskowania?

Eter nie jest celem w przypadku weryfikacji z urzędu certyfikacji. W związku z tym nie ma potrzeby określania opłaty za gaz podczas przesyłania transakcji w ramach maski.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Co należy zrobić, jeśli moje wdrożenie nie powiedzie się z powodu niepowodzenia aprowizacji pakietu OMS platformy Azure?

Monitorowanie jest funkcją opcjonalną. W niektórych rzadkich przypadkach, gdy wdrożenie nie powiedzie się z powodu niemożliwości pomyślnego zainicjowania obsługi zasobów Azure Monitor, można je wdrożyć ponownie bez Azure Monitor.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Czy publiczne wdrożenia adresów IP są zgodne z wdrożeniami w sieci prywatnej?

Nie. Komunikacja równorzędna wymaga komunikacji dwukierunkowej, dlatego cała sieć musi być publiczna lub prywatna.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Jaka jest oczekiwana przepływność transakcji dla potwierdzenia urzędu?

Przepływność transakcji będzie wysoce zależna od typów transakcji i topologii sieci. Przy użyciu prostych transakcji firma Microsoft testuje średnio 400 transakcji na sekundę z siecią wdrożoną w wielu regionach.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Jak mogę subskrybować zdarzenia dotyczące kontraktu inteligentnego?

Ethereum potwierdzenie urzędu obsługuje teraz sieci Web-Sockets.  Sprawdź dane wyjściowe wdrożenia, aby znaleźć adres URL i port gniazda sieci Web.

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

W przypadku usługi Azure łańcucha bloków News odwiedź [blog usługi Azure łańcucha bloków](https://azure.microsoft.com/blog/topics/blockchain/) , aby zachować aktualność w zakresie usług łańcucha bloków i uzyskać informacje od zespołu inżynierów ds. platformy Azure łańcucha bloków.

Aby przekazać opinie o produkcie lub zażądać nowych funkcji, opublikuj lub zagłosuj na pomysł za pośrednictwem [forum opinii platformy Azure dla usługi łańcucha bloków](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Pomoc techniczna w społeczności

Współpracuj z inżynierami firmy Microsoft i ekspertów społeczności Azure łańcucha bloków.

* [Microsoft Q&strony pytania](/answers/topics/azure-blockchain-workbench.html). Wsparcie inżynieryjne dla szablonów łańcucha bloków jest ograniczone do problemów z wdrażaniem.
* [Witryna Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Następne kroki

Więcej rozwiązań łańcucha bloków platformy Azure można znaleźć w [dokumentacji usługi Azure łańcucha bloków](../index.yml).
