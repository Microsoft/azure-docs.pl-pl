---
title: Analiza ruchu na platformie Azure | Microsoft Docs
description: Dowiedz się, jak analizować dzienniki przepływu sieciowych grup zabezpieczeń platformy Azure przy użyciu analizy ruchu.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.reviewer: vinigam
ms.custom: references_regions
ms.openlocfilehash: 6cd1965ab51e7a7bbcc65836383000f0773b9b82
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070945"
---
# <a name="traffic-analytics"></a>Analiza ruchu

Analiza ruchu to rozwiązanie oparte na chmurze, które zapewnia wgląd w aktywność użytkowników i aplikacji w sieciach w chmurze. Analiza ruchu analizuje dzienniki przepływu Network Watcher sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń), aby uzyskać wgląd w przepływ ruchu w chmurze platformy Azure. Analiza ruchu umożliwia:

- Wizualizuj aktywność sieci w ramach subskrypcji platformy Azure i zidentyfikuj punkty aktywne.
- Zidentyfikuj zagrożenia bezpieczeństwa, a także Zabezpiecz sieć, korzystając z informacji takich jak Open-Ports, Applications próbujących uzyskać dostęp do Internetu oraz maszyn wirtualnych łączących się z nieautoryzowanymi sieciami.
- Zrozumienie wzorców przepływu ruchu w regionach platformy Azure oraz w Internecie w celu zoptymalizowania wdrożenia sieci pod kątem wydajności i pojemności.
- Lokalizowanie błędnych konfiguracji sieciowych prowadzących do nieudanych połączeń w sieci.

> [!NOTE]
> Analiza ruchu teraz obsługuje zbieranie danych dzienników przepływu sieciowej grupy zabezpieczeń o wyższej częstotliwości wynoszącej 10 minut

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Dlaczego Analiza ruchu?

Niezbędne jest monitorowanie własnej sieci i zarządzanie nią w celu naruszenia bezpieczeństwa, zgodności i wydajności. Znajomość własnego środowiska ma najważniejsze znaczenie dla ochrony i optymalizacji. Często trzeba znać bieżący stan sieci, który nawiązuje połączenie, z którym są nawiązywane połączenia, które porty są otwarte dla Internetu, oczekiwano zachowania sieci, nieregularne zachowanie sieci i nagłe wzrost ruchu.

Sieci w chmurze są inne niż lokalne sieci przedsiębiorstwa, w których są dostępne lub równoważne routery i przełączniki obsługujące protokół, które umożliwiają zbieranie ruchu sieciowego IP w miarę wprowadzania lub wyłączania interfejsu sieciowego. Analizując dane przepływu ruchu, można utworzyć analizę przepływu ruchu sieciowego i woluminu.

Usługi Azure Virtual Networks zawierają dzienniki przepływu sieciowej grupy zabezpieczeń, które udostępniają informacje dotyczące ruchu przychodzącego i wychodzącego IP za pośrednictwem sieciowej grupy zabezpieczeń skojarzonej z poszczególnymi interfejsami sieciowymi, maszynami wirtualnymi lub podsieciami. Analizując nieprzetworzone dzienniki przepływu sieciowej grupy zabezpieczeń i wstawiając inteligencję zabezpieczeń, topologię i lokalizację geograficzną, Analiza ruchu może zapewnić wgląd w przepływ ruchu w środowisku. Analiza ruchu zawiera informacje takie jak większość hostów komunikujących się, większość komunikacji między protokołami aplikacji, większość par hostów, które są dozwolone/blokowany ruch, ruch przychodzący/wychodzący, otwieranie portów internetowych, większość reguł blokowania, dystrybucja ruchu w centrum danych platformy Azure, Sieć wirtualna, podsieci lub nieautoryzowane sieci.

## <a name="key-components"></a>Główne składniki

- **Network Security Group (sieciowej grupy zabezpieczeń)**: zawiera listę reguł zabezpieczeń, które zezwalają na ruch sieciowy lub odmawiają go zasobom podłączonym do Virtual Network platformy Azure. Sieciowe grupy zabezpieczeń można skojarzyć z podsieciami, poszczególnymi maszynami wirtualnymi (model klasyczny) lub poszczególnymi interfejsami sieciowymi (NIC) dołączonymi do maszyn wirtualnych (model usługi Resource Manager). Aby uzyskać więcej informacji, zobacz [Omówienie grup zabezpieczeń sieci](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Dzienniki przepływu sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)**: umożliwiają wyświetlanie informacji dotyczących ruchu przychodzącego i wychodzącego IP za pomocą sieciowej grupy zabezpieczeń. Dzienniki przepływu sieciowej grupy zabezpieczeń są zapisywane w formacie JSON i pokazują przepływy wychodzące i przychodzące dla każdej reguły, karta sieciowa przepływu ma zastosowanie do pięciu informacji o przepływie (źródłowy/docelowy adres IP, port źródłowy/docelowy i protokół) oraz jeśli ruch był dozwolony lub zabroniony. Aby uzyskać więcej informacji na temat dzienników przepływów sieciowej grupy zabezpieczeń, zobacz [dzienniki przepływu sieciowej grupy zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: usługa platformy Azure, która zbiera dane monitorowania i zapisuje dane w centralnym repozytorium. Te dane mogą obejmować zdarzenia, dane dotyczące wydajności lub dane niestandardowe udostępniane za pomocą interfejsu API platformy Azure. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu. Aplikacje monitorujące, takie jak Monitor wydajności sieci i Analiza ruchu, są tworzone przy użyciu Azure Monitor dzienników jako podstawy. Aby uzyskać więcej informacji, zobacz [dzienniki Azure monitor](../azure-monitor/log-query/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Obszar roboczy log Analytics**: wystąpienie dzienników Azure monitor, w których są przechowywane dane odnoszące się do konta platformy Azure. Aby uzyskać więcej informacji na temat obszarów roboczych Log Analytics, zobacz [tworzenie log Analytics obszaru roboczego](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Network Watcher**: usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci na platformie Azure. Dzienniki przepływu sieciowej grupy zabezpieczeń można włączać i wyłączać za pomocą Network Watcher. Aby uzyskać więcej informacji, zobacz [Network Watcher](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Jak działa Analiza ruchu

Analiza ruchu analizuje dzienniki nieprzetworzonych przepływów sieciowej grupy zabezpieczeń i przechwytuje zredukowane dzienniki poprzez agregowanie typowych przepływów między tym samym źródłowym adresem IP, docelowym adresem IP, portem docelowym i protokołem. Na przykład host 1 (adres IP: 10.10.10.10), który komunikuje się z hostem 2 (adres IP: 10.10.20.10), 100 razy w okresie 1 godziny przy użyciu portu (na przykład 80) i protokołu (na przykład http). Zredukowany Dziennik ma jeden wpis, który host 1 & Host 2 komunikuje się 100 razy w okresie 1 godziny przy użyciu portu *80* i protokołu *http*, a nie do 100 wpisów. Zredukowane dzienniki są ulepszone przy użyciu informacji o lokalizacji geograficznej, zabezpieczeń i topologii, a następnie są przechowywane w obszarze roboczym Log Analytics. Na poniższej ilustracji przedstawiono przepływ danych:

![Przepływ danych dla przetwarzania dzienników przepływu sieciowej grupy zabezpieczeń](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>Obsługiwane regiony: sieciowej grupy zabezpieczeń 

Analizy ruchu można użyć dla sieciowych grup zabezpieczeń w jednym z następujących obsługiwanych regionów:
:::row:::
   :::column span="":::
      Australia Środkowa  
      Australia Wschodnia  
      Australia Południowo-Wschodnia  
      Brazil South  
      Kanada Środkowa  
      Kanada Wschodnia  
      Indie Środkowe  
      Central US  
      Chiny Wschodnie 2  
      Chiny Północne 2  
   :::column-end:::
   :::column span="":::
      Azja Wschodnia  
      East US  
      Wschodnie stany USA 2  
      Wschodnie stany USA 2 — EUAP  
      Francja Środkowa  
      Japonia Wschodnia  
      Japonia Zachodnia  
      Korea Środkowa  
      Korea Południowa  
      Północno-środkowe stany USA  
   :::column-end:::
   :::column span="":::
      Europa Północna  
      Północna Republika Południowej Afryki  
      South Central US  
      Indie Południowe  
      Southeast Asia  
      Szwajcaria Północna  
      Szwajcaria Zachodnia  
      Południowe Zjednoczone Królestwo  
      Zachodnie Zjednoczone Królestwo  
      USGov Arizona  
   :::column-end:::
   :::column span="":::
      USGov Teksas  
      USGov Wirginia  
      USNat wschód  
      USNat zachodni  
      USSec wschód  
      USSec zachodni  
      Zachodnio-środkowe stany USA  
      West Europe  
      Zachodnie stany USA  
      Zachodnie stany USA 2  
   :::column-end:::
:::row-end:::

## <a name="supported-regions-log-analytics-workspaces"></a>Obsługiwane regiony: Log Analytics obszary robocze

Obszar roboczy Log Analytics musi istnieć w następujących regionach:
:::row:::
   :::column span="":::
      Australia Środkowa  
      Australia Wschodnia  
      Australia Południowo-Wschodnia  
      Brazil South  
      Kanada Środkowa  
      Indie Środkowe  
      Central US  
      Chiny Wschodnie 2  
      Azja Wschodnia  
      East US  
   :::column-end:::
   :::column span="":::
      Wschodnie stany USA 2  
      Wschodnie stany USA 2 — EUAP  
      Francja Środkowa  
      Niemcy Środkowo-Zachodnie  
      Japan East  
      Korea Środkowa  
      Północno-środkowe stany USA  
      Europa Północna  
      Północna Republika Południowej Afryki  
      South Central US  
   :::column-end:::
   :::column span="":::
      Southeast Asia  
      Szwajcaria Północna  
      Szwajcaria Zachodnia  
      Środkowy Zjednoczone Emiraty Arabskie  
      Południowe Zjednoczone Królestwo  
      Zachodnie Zjednoczone Królestwo  
      USGov Arizona  
      USGov Wirginia  
      USNat wschód  
      USNat zachodni  
   :::column-end:::
   :::column span="":::
      USSec wschód  
      USSec zachodni  
      Zachodnio-środkowe stany USA  
      West Europe  
      Zachodnie stany USA  
      Zachodnie stany USA 2  
   :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="user-access-requirements"></a>Wymagania dotyczące dostępu użytkowników

Twoje konto musi być członkiem jednej z następujących [wbudowanych ról platformy Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json):

|Model wdrażania   | Rola                   |
|---------          |---------               |
|Resource Manager   | Właściciel                  |
|                   | Współautor            |
|                   | Czytelnik                 |
|                   | Współautor sieci    |

Jeśli Twoje konto nie jest przypisane do jednej z ról wbudowanych, musi być przypisane do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) , do której przypisano następujące akcje, na poziomie subskrypcji:

- "Microsoft. Network/applicationGateways/Read"
- "Microsoft. Network/Connections/Read"
- "Microsoft. Network/loadBalancers/Read"
- "Microsoft. Network/localNetworkGateways/Read"
- "Microsoft. Network/networkInterfaces/Read"
- "Microsoft. Network/networkSecurityGroups/Read"
- "Microsoft. Network/adresów publicipaddress/Read"
- "Microsoft. Network/routeTables/Read"
- "Microsoft. Network/virtualNetworkGateways/Read"
- "Microsoft. Network/virtualNetworks/Read"
- "Microsoft. Network/expressRouteCircuits/Read"

Aby uzyskać informacje na temat sprawdzania uprawnień dostępu użytkownika, zobacz Omówienie usługi [Traffic Analytics — często zadawane pytania](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Aby analizować ruch, musisz mieć istniejący obserwator sieci lub [włączyć obserwatora sieci](network-watcher-create.md) w każdym regionie, w którym sieciowych grup zabezpieczeń chcesz analizować ruch. Analiza ruchu może być włączona dla usługi sieciowych grup zabezpieczeń hostowanej w dowolnym z [obsługiwanych regionów](#supported-regions-nsg).

### <a name="select-a-network-security-group"></a>Wybierz sieciową grupę zabezpieczeń

Przed włączeniem rejestrowania przepływu sieciowej grupy zabezpieczeń należy mieć sieciową grupę zabezpieczeń, aby rejestrować przepływy dla programu. Jeśli nie masz sieciowej grupy zabezpieczeń, zobacz [Tworzenie sieciowej grupy zabezpieczeń](../virtual-network/manage-network-security-group.md#create-a-network-security-group) , aby ją utworzyć.

W Azure Portal przejdź do **monitora sieci**, a następnie wybierz pozycję **dzienniki przepływów sieciowej grupy zabezpieczeń**. Wybierz sieciową grupę zabezpieczeń, dla której chcesz włączyć dziennik przepływu sieciowej grupy zabezpieczeń, jak pokazano na poniższej ilustracji:

![Wybór sieciowych grup zabezpieczeń, które wymagają włączenia dziennika przepływu sieciowej grupy zabezpieczeń](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Jeśli spróbujesz włączyć analizę ruchu dla sieciowej grupy zabezpieczeń, który jest hostowany w dowolnym regionie innym niż [Obsługiwane regiony](#supported-regions-nsg), zostanie wyświetlony komunikat o błędzie "nie znaleziono".

## <a name="enable-flow-log-settings"></a>Włącz ustawienia dziennika przepływu

Przed włączeniem ustawień dziennika przepływu należy wykonać następujące zadania:

Zarejestruj dostawcę usługi Azure Insights, jeśli nie został jeszcze zarejestrowany dla Twojej subskrypcji:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Jeśli nie masz jeszcze konta usługi Azure Storage do przechowywania dzienników przepływu sieciowej grupy zabezpieczeń w programie, musisz utworzyć konto magazynu. Można utworzyć konto magazynu za pomocą poniższego polecenia. Przed uruchomieniem polecenia Zamień na `<replace-with-your-unique-storage-account-name>` nazwę, która jest unikatowa we wszystkich lokalizacjach platformy Azure, od 3-24 znaków, używając tylko cyfr i małych liter. W razie potrzeby można również zmienić nazwę grupy zasobów.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Wybierz poniższe opcje, jak pokazano na ilustracji:

1. Wybierz pozycję *włączone* dla **stanu**
2. Wybierz *wersję 2* dla **wersji dzienników przepływów**. Wersja 2 zawiera statystykę przepływu/sesji (bajty i pakiety)
3. Wybierz istniejące konto magazynu, w którym mają być przechowywane dzienniki przepływów. Upewnij się, że magazyn nie ma ustawionej wartości "Data Lake Storage Gen2 hierarchiczny obszar nazw".
4. Ustaw wartość **przechowywanie** na liczbę dni, przez którą mają być przechowywane dane. Jeśli chcesz przechowywać dane w nieskończoność, ustaw wartość na *0*. Opłaty za usługę Azure Storage są naliczane za konto magazynu. 
5. Wybierz pozycję *włączone* , aby uzyskać **stan Analiza ruchu**.
6. Wybierz interwał przetwarzania. W zależności od wybranych dzienników przepływów będą zbierane z konta magazynu i przetwarzane przez Analiza ruchu. Można wybrać interwał przetwarzania co 1 godzinę lub co 10 minut. 
7. Wybierz istniejący obszar roboczy usługi Log Analytics (OMS) lub wybierz pozycję **Utwórz nowy obszar roboczy** , aby utworzyć nowy. Obszar roboczy Log Analytics jest używany przez Analiza ruchu do przechowywania zagregowanych i indeksowanych danych, które są następnie używane do generowania analizy. Jeśli wybierzesz istniejący obszar roboczy, musi on znajdować się w jednym z [obsługiwanych regionów](#supported-regions-log-analytics-workspaces) i został uaktualniony do nowego języka zapytań. Jeśli nie chcesz uaktualnić istniejącego obszaru roboczego lub nie masz obszaru roboczego w obsługiwanym regionie, Utwórz nowy. Aby uzyskać więcej informacji na temat języków zapytań, zobacz [Azure log Analytics Upgrade to New Search log](../azure-monitor/log-query/log-query-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

> [!NOTE]
>Obszar roboczy usługi log Analytics obsługujący rozwiązanie do analizy ruchu i sieciowych grup zabezpieczeń nie muszą znajdować się w tym samym regionie. Na przykład możesz mieć dostęp do analizy ruchu w obszarze roboczym w regionie Europa Zachodnia, a ty możesz mieć sieciowych grup zabezpieczeń w regionach Wschodnie stany USA i zachodnie stany USA. W tym samym obszarze roboczym można skonfigurować wiele sieciowych grup zabezpieczeń.

8. Wybierz pozycję **Zapisz**.

    ![Wybór konta magazynu, obszaru roboczego Log Analytics i włączenia Analiza ruchu](./media/traffic-analytics/ta-customprocessinginterval.png)

Powtórz poprzednie kroki dla wszystkich innych sieciowych grup zabezpieczeń, dla których chcesz włączyć funkcję analizy ruchu dla programu. Dane z dzienników przepływów są wysyłane do obszaru roboczego, dlatego należy się upewnić, że lokalne prawa i regulacje w Twoim kraju/regionie zezwalają na przechowywanie danych w regionie, w którym znajduje się obszar roboczy. Jeśli ustawiono różne interwały przetwarzania dla różnych sieciowych grup zabezpieczeń, dane będą zbierane w różnych interwałach. Na przykład: można włączyć interwał przetwarzania równy 10 minut dla sieci wirtualnych krytycznych i 1 godzinę dla niekrytycznego sieci wirtualnychu.

Analiza ruchu można również skonfigurować za pomocą polecenia cmdlet [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) środowiska PowerShell w Azure PowerShell. Uruchom `Get-Module -ListAvailable Az` , aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Wyświetlanie analizy ruchu

Aby wyświetlić Analiza ruchu, Wyszukaj **Network Watcher** na pasku wyszukiwania portalu. Po Network Watcher, aby poznać analizę ruchu i jej możliwości, wybierz pozycję **Analiza ruchu** z menu po lewej stronie. 

![Uzyskiwanie dostępu do pulpitu nawigacyjnego Analiza ruchu](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

Po raz pierwszy pulpit nawigacyjny może potrwać do 30 minut, ponieważ Analiza ruchu musi najpierw agregować wystarczającą ilość danych, aby uzyskać istotny wgląd, zanim będzie mógł generować raporty.

## <a name="usage-scenarios"></a>Scenariusze użycia

Niektóre szczegółowe informacje, które można uzyskać po skonfigurowaniu Analiza ruchu jest w pełni skonfigurowane, są następujące:

### <a name="find-traffic-hotspots"></a>Znajdź hotspoty ruchu

**Szukać**

- Które hosty, podsieci i sieci wirtualne wysyłają lub odbierają najwięcej ruchu, przechodząc do maksymalnego złośliwego ruchu i blokując znaczące przepływy?
    - Sprawdź Wykres porównawczy dla hosta, podsieci i sieci wirtualnej. Informacje o tym, które hosty, podsieci i sieci wirtualne są wysyłane lub odbierające najwięcej ruchu, mogą ułatwić zidentyfikowanie hostów, które przetwarzają większość ruchu, oraz tego, czy dystrybucja ruchu jest wykonywana prawidłowo.
    - Można oszacować, czy wolumin ruchu jest odpowiedni dla hosta. Czy jest to natężenie normalnego działania ruchu lub czy ma ono na celu dalsze badanie?
- Jak dużo ruchu przychodzącego/wychodzącego?
    -   Czy host powinien odbierać więcej ruchu przychodzącego niż wychodzące lub odwrotnie?
- Statystyka zablokowanego ruchu.
    - Dlaczego host blokuje znaczną ilość nieszkodliwego ruchu? To zachowanie wymaga dalszych badań i prawdopodobnie zoptymalizować konfigurację
- Statystyka złośliwego dozwolonego/zablokowanego ruchu
  - Dlaczego Host odbiera złośliwy ruch i dlaczego są obsługiwane przepływy ze złośliwych źródeł? To zachowanie wymaga dalszych badań i prawdopodobnie zoptymalizować konfigurację.

    Wybierz pozycję **Zobacz wszystkie** w obszarze **host**, jak pokazano na poniższej ilustracji:

    ![Pokaz pulpitu nawigacyjnego z większością szczegółów ruchu](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- Na poniższej ilustracji przedstawiono trend czasu dla pięciu najlepszych hostów oraz szczegóły dotyczące przepływów (dozwolone – przychodzące/wychodzące i odrzucone — przepływy przychodzące/wychodzące) dla hosta:

    ![Pięć najważniejszych trendów hosta](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Szukać**

- Które są najbardziej odwracające pary hostów?
    - Oczekiwane zachowanie, takie jak komunikacja frontonu lub komunikacji zaplecza lub nieregularnego zachowania, na przykład ruch internetowy zaplecza.
- Statystyki dozwolone/zablokowanego ruchu
    - Dlaczego Host dopuszcza lub blokuje znaczną ilość ruchu sieciowego
- Najczęściej używany protokół aplikacji wśród pary hostów z największą ilością:
    - Czy te aplikacje są dozwolone w tej sieci?
    - Czy poprawnie skonfigurowano aplikacje? Czy korzystają one z odpowiedniego protokołu do komunikacji? Zaznacz opcję **Zobacz wszystko** w obszarze **częste konwersacje**, jak pokazano na poniższej ilustracji:

        ![Pulpit nawigacyjny przedstawiający najczęstsze konwersacje](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- Na poniższej ilustracji przedstawiono trend czasu dla pięciu pierwszych konwersacji oraz szczegóły dotyczące przepływu, takie jak dozwolone i odrzucane przepływy ruchu przychodzącego i wychodzącego dla pary konwersacji:

    ![Pięć najważniejszych szczegółów konwersacji i trendów](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Szukać**

- Który protokół aplikacji jest najczęściej używany w danym środowisku, a pary hostów z odwracaniem używają protokołu aplikacji?
    - Czy te aplikacje są dozwolone w tej sieci?
    - Czy poprawnie skonfigurowano aplikacje? Czy korzystają one z odpowiedniego protokołu do komunikacji? Oczekiwane zachowanie to typowe porty, takie jak 80 i 443. W przypadku komunikacji standardowej, jeśli są wyświetlane jakiekolwiek nietypowe porty, mogą one wymagać zmiany konfiguracji. Wybierz pozycję **Zobacz wszystko** w obszarze **port aplikacji**, na poniższej ilustracji:

        ![Pulpit nawigacyjny przedstawiający najpopularniejsze protokoły aplikacji](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- Następujące obrazy przedstawiają trendy czasu dla pięciu pierwszych protokołów P7 oraz szczegóły dotyczące przepływów (na przykład przepływy dozwolone i odrzucone) dla protokołu P7:

    ![Pięć najważniejszych szczegółowych protokołów warstwy 7 i trendu](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Szczegóły przepływu dla protokołu aplikacji w przeszukiwaniu dzienników](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Szukać**

- Trendy wykorzystania pojemności bramy sieci VPN w danym środowisku.
    - Każda jednostka SKU sieci VPN pozwala uzyskać określoną przepustowość. Czy bramy sieci VPN są niewykorzystywane?
    - Czy bramy docierają do pojemności? Czy należy uaktualnić do następnej wyższej jednostki SKU?
- Które są największej liczbie hostów, za pośrednictwem której Brama sieci VPN przekroczy port?
    - Czy ten wzorzec jest normalny? Wybierz pozycję **Zobacz wszystko** w obszarze **Brama sieci VPN**, jak pokazano na poniższej ilustracji:

        ![Pulpit nawigacyjny przedstawiający najpopularniejsze aktywne połączenia sieci VPN](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- Na poniższej ilustracji przedstawiono trendy czasu dla wykorzystania pojemności VPN Gateway platformy Azure i szczegółowe informacje dotyczące przepływu (takie jak dozwolone przepływy i porty):

    ![Szczegóły dotyczące trendu i przepływu użycia bramy sieci VPN](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Wizualizowanie dystrybucji ruchu według lokalizacji geograficznej

**Szukać**

- Rozkład ruchu na centra danych, takie jak najpopularniejsze źródła ruchu, do centrum danych, najpopularniejszych nieautoryzowanych sieci, które są odwracane w usłudze Data Center i górnego odwrócenia protokołów aplikacji.
  - Jeśli zaobserwujesz więcej obciążeń w centrum danych, możesz zaplanować efektywną dystrybucję ruchu.
  - Jeśli nieautoryzowane sieci są w centrum danych, Popraw reguły sieciowej grupy zabezpieczeń, aby je zablokować.

    Wybierz pozycję **Wyświetl mapę** w **środowisku**, jak pokazano na poniższej ilustracji:

    ![Pokaz pulpitu nawigacyjnego — dystrybucja ruchu](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- Mapa geograficzna pokazuje górną Wstążkę do wybierania parametrów, takich jak centra danych (wdrożone/niewymagające wdrożenia/Active/nieaktywny/Analiza ruchu włączone/Analiza ruchu nie włączono), a kraje/regiony, w których działa niegroźny/złośliwy ruch do aktywnego wdrożenia:

    ![Widok mapy geograficznej przedstawiający aktywne wdrożenie](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- Mapa geograficzna przedstawia rozkład ruchu do centrum danych z krajów/regionów i kontynentuje komunikację z nim w kolorze niebieskim (niegroźny ruch) i czerwony (złośliwy ruch) kolorowe linie:

    ![Widok mapy geograficznej przedstawiający rozkład ruchu do krajów/regionów i kontynentów](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Szczegóły przepływu dla dystrybucji ruchu w przeszukiwaniu dzienników](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Wizualizowanie dystrybucji ruchu przez sieci wirtualne

**Szukać**

- Dystrybucja ruchu na sieć wirtualna, topologia, najważniejsze źródła ruchu do sieci wirtualnej, najważniejsze nieautoryzowane sieci odwracające do sieci wirtualnej i górne protokoły aplikacji.
  - Znajomość, która Sieć wirtualna jest odwracana do tej sieci wirtualnej. Jeśli konwersacja nie jest oczekiwana, można ją poprawić.
  - Jeśli nieautoryzowane sieci są odwracające do sieci wirtualnej, możesz poprawić reguły sieciowej grupy zabezpieczeń, aby blokować nieautoryzowane sieci.
 
    Wybierz pozycję **Wyświetl sieci wirtualnych** w **środowisku**, jak pokazano na poniższej ilustracji:

    ![Pulpit nawigacyjny — pokaz dystrybucji sieci wirtualnej](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- Topologia Virtual Network przedstawia najwyższą Wstążkę do wybierania parametrów, takich jak sieć wirtualna (połączenia sieci wirtualnej/aktywne/nieaktywne), połączenia zewnętrzne, aktywne przepływy i złośliwe przepływy sieci wirtualnej.
- Topologię Virtual Network można filtrować na podstawie subskrypcji, obszarów roboczych, grup zasobów i interwału czasu. Dodatkowe filtry, które ułatwiają zrozumienie przepływu: typ przepływu (ramach, IntraVNET itd.), kierunek przepływu (ruch przychodzący), stan przepływu (dozwolone, zablokowane), sieci wirtualnych (ukierunkowane i połączone), typ połączenia (Komunikacja równorzędna lub Brama-P2S i S2S) oraz sieciowej grupy zabezpieczeń. Użyj tych filtrów, aby skoncentrować się na sieci wirtualnych, który ma zostać szczegółowo sprawdzony.
- Topologia Virtual Network przedstawia dystrybucję ruchu do sieci wirtualnej w odniesieniu do przepływów (dozwolony/zablokowany/przychodzący/wychodzący/niegroźny/złośliwy), protokołu aplikacji i sieciowych grup zabezpieczeń, na przykład:

    ![Topologia sieci wirtualnej przedstawiające informacje o dystrybucji i przepływie ruchu](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Doprezentowanie topologii sieci wirtualnej najwyższego poziomu i wielu filtrów](./media/traffic-analytics/virtual-network-filters.png)

    ![Szczegóły przepływu dla dystrybucji ruchu w sieci wirtualnej w przeszukiwaniu dzienników](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Szukać**

- Dystrybucja ruchu na podsieci, topologia, najważniejsze źródła ruchu do podsieci, najważniejsze nieautoryzowane sieci odwracające do podsieci oraz górne protokoły aplikacji.
    - Poznanie podsieci, do której odnosi się ta podsieć. Jeśli zobaczysz nieoczekiwane konwersacje, możesz poprawić konfigurację.
    - Jeśli nieautoryzowane sieci są odwracane z podsiecią, można je poprawić przez skonfigurowanie reguł sieciowej grupy zabezpieczeń, aby blokować nieautoryzowane sieci.
- Topologia podsieci pokazuje górną Wstążkę do wyboru parametrów, takich jak podsieć aktywna/nieaktywna, połączenia zewnętrzne, aktywne przepływy i złośliwe przepływy podsieci.
- Topologia podsieci przedstawia dystrybucję ruchu do sieci wirtualnej w odniesieniu do przepływów (dozwolony/zablokowany/przychodzący/wychodzący/niegroźny/złośliwy), protokołu aplikacji i sieciowych grup zabezpieczeń, na przykład:

    ![Topologia podsieci przedstawia dystrybucja ruchu w podsieci sieci wirtualnej w odniesieniu do przepływów](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Szukać**

Dystrybucja ruchu na bramę aplikacji & Load Balancer, topologia, najważniejsze źródła ruchu, najważniejsze sieci nieautoryzowane odwracające do bramy Application Gateway & Load Balancer i górne protokoły aplikacji. 
    
 - Poznanie podsieci, do której odnosi się Brama aplikacji lub Load Balancer. Jeśli zauważysz nieoczekiwane konwersacje, możesz poprawić konfigurację.
 - Jeśli nieautoryzowane sieci są odwracające do bramy aplikacji lub Load Balancer, można je poprawić, konfigurując reguły sieciowej grupy zabezpieczeń w celu blokowania nieautoryzowanych sieci. 

    ![Zrzut ekranu przedstawia topologię podsieci z dystrybucją ruchu do podsieci bramy aplikacji w odniesieniu do przepływów.](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Wyświetlanie portów i maszyn wirtualnych odbierających ruch z Internetu

**Szukać**

- Które otwarte porty są nawracane przez Internet?
  - W przypadku znalezienia nieoczekiwanych portów można poprawić konfigurację:

    ![Pulpit nawigacyjny przedstawiający porty odbierające i wysyłające ruch do Internetu](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Szczegóły dotyczące portów i hostów docelowych platformy Azure](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Szukać**

Czy masz złośliwy ruch w Twoim środowisku? Skąd pochodzi? Gdzie jest on przeznaczony?

![Szczegóły dotyczące złośliwego ruchu w przeszukiwaniu dzienników](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Wizualizuj trendy w sieciowej grupy zabezpieczeń/sieciowej grupy ZABEZPIECZEŃe trafień reguł

**Szukać**

- Które reguły sieciowej grupy zabezpieczeń/sieciowej grupy zabezpieczeń mają najwięcej trafień na wykresie porównawczym z dystrybucją przepływów?
- Jakie są najlepsze źródłowe i docelowe pary konwersacji dla reguł sieciowej grupy zabezpieczeń/sieciowej grupy zabezpieczeń?

    ![Pokaz pulpitu nawigacyjnego — statystyki trafień sieciowej grupy zabezpieczeń](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- Następujące obrazy przedstawiają trendy czasu dla trafień reguł sieciowej grupy zabezpieczeń i szczegółów przepływu źródła danych dla sieciowej grupy zabezpieczeń:

  - Szybko wykrywaj, które reguły sieciowych grup zabezpieczeń i sieciowej grupy zabezpieczeń przechodzą przez złośliwe przepływy, a które są najbardziej złośliwymi adresami IP, które uzyskują dostęp do środowiska chmury
  - Ustal, które reguły sieciowej grupy zabezpieczeń/sieciowej grupy zabezpieczeń umożliwiają lub blokują znaczny ruch sieciowy
  - Wybierz górne filtry do szczegółowej inspekcji reguł sieciowej grupy zabezpieczeń lub sieciowej grupy zabezpieczeń

    ![Trend czasu prezentowania dla trafień reguł sieciowej grupy zabezpieczeń i reguł sieciowej grupy zabezpieczeń najważniejszych](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Szczegóły statystyk sieciowej grupy zabezpieczeń najważniejszych reguł w przeszukiwaniu dzienników](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Aby uzyskać odpowiedzi na często zadawane pytania, zobacz [Analiza ruchu — często zadawane](traffic-analytics-faq.md)pytania.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak włączyć dzienniki przepływów, zobacz [Włączanie rejestrowania przepływu sieciowej grupy zabezpieczeń](network-watcher-nsg-flow-logging-portal.md).
- Aby zrozumieć schemat i informacje o przetwarzaniu Analiza ruchu, zobacz [schemat analizy ruchu](traffic-analytics-schema.md).
