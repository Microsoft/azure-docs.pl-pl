---
title: Używanie usługi Azure Private Link do bezpiecznego łączenia sieci z usługą Azure Monitor
description: Używanie usługi Azure Private Link do bezpiecznego łączenia sieci z usługą Azure Monitor
author: nkiest
ms.author: nikiest
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: 8633aba2f7cda5dec4a48e9f7132283f8235f746
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317524"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Używanie usługi Azure Private Link do bezpiecznego łączenia sieci z usługą Azure Monitor

[Link prywatny platformy Azure](../../private-link/private-link-overview.md) umożliwia bezpieczne łączenie usług Azure PaaS z siecią wirtualną za pomocą prywatnych punktów końcowych. W przypadku wielu usług wystarczy skonfigurować punkt końcowy dla każdego zasobu. Azure Monitor jest jednak Constellation różnych połączonych usług, które współpracują ze sobą w celu monitorowania obciążeń. W związku z tym został utworzony zasób o nazwie Azure Monitor prywatny zakres linków (AMPLS), który umożliwia definiowanie granic sieci monitorowania i nawiązywanie połączenia z siecią wirtualną. W tym artykule omówiono, kiedy należy używać programu i jak skonfigurować Azure Monitor zakres linków prywatnych.

## <a name="advantages"></a>Zalety

Za pomocą linku prywatnego można:

- Połącz się prywatnie, aby Azure Monitor bez otwierania dostępu do sieci publicznej
- Upewnij się, że dane monitorowania są dostępne tylko za poorednictwem autoryzowanych sieci prywatnych
- Zapobiegaj eksfiltracji danych z sieci prywatnych przez definiowanie określonych zasobów Azure Monitor, które łączą się za pośrednictwem prywatnego punktu końcowego
- Bezpieczne łączenie prywatnej sieci lokalnej w celu Azure Monitor przy użyciu linku ExpressRoute i prywatnego
- Zachowaj cały ruch w sieci szkieletowej Microsoft Azure

Aby uzyskać więcej informacji, zobacz  [najważniejsze zalety linku prywatnego](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Jak to działa

Azure Monitor prywatny zakres łączy jest zasobem grupującym, aby połączyć co najmniej jeden prywatny punkt końcowy (i w związku z tym sieci wirtualne, w których są zawarte), do co najmniej jednego zasobu Azure Monitor. Zasoby obejmują Log Analytics obszary robocze i składniki Application Insights.

![Diagram topologii zasobów](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> Pojedynczy zasób Azure Monitor może należeć do wielu AMPLSs, ale nie można połączyć jednej sieci wirtualnej z więcej niż jedną AMPLSą. 

## <a name="planning-based-on-your-network"></a>Planowanie w oparciu o sieć

Przed skonfigurowaniem zasobów AMPLS należy wziąć pod uwagę wymagania dotyczące izolacji sieci. Oceń dostęp sieci wirtualnych do publicznego Internetu i ograniczenia dostępu do poszczególnych zasobów Azure Monitor (to znaczy składniki Application Insights i Log Analytics obszary robocze).

> [!NOTE]
> Sieci typu Hub i szprych lub jakakolwiek inna topologia sieci równorzędnych, można skonfigurować połączenie prywatne między siecią wirtualną centralną (główną) i odpowiednimi zasobami Azure Monitor, zamiast konfigurować prywatne łącze do każdej sieci wirtualnej. Jest to szczególnie przydatne, jeśli Azure Monitor zasoby używane przez te sieci są udostępniane. Jeśli jednak chcesz zezwolić każdej sieci wirtualnej na dostęp do oddzielnego zestawu zasobów monitorowania, Utwórz prywatny link do dedykowanej AMPLS dla każdej z nich.

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Oceń, które sieci wirtualne powinny łączyć się z prywatnym linkiem

Zacznij od oceny, które sieci wirtualne (sieci wirtualnych) mają ograniczony dostęp do Internetu. Sieci wirtualnych z bezpłatną Internetem może nie wymagać prywatnego linku, aby uzyskać dostęp do zasobów Azure Monitor. Zasoby monitorowania, z którymi nawiązuje połączenie sieci wirtualnych, mogą ograniczać ruch przychodzący i wymagać połączenia prywatnego (na potrzeby pozyskiwania dziennika lub zapytania). W takich przypadkach nawet sieć wirtualna, która ma dostęp do publicznej sieci Internet, musi łączyć się z tymi zasobami za pośrednictwem prywatnego linku i AMPLS.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Oceń, które zasoby Azure Monitor powinny mieć link prywatny

Przejrzyj wszystkie zasoby Azure Monitor:

- Czy zasób powinien zezwalać na pozyskiwanie dzienników z zasobów znajdujących się tylko w określonych sieci wirtualnych?
- Czy zasób ma być wysyłany tylko przez klientów znajdujących się w określonym sieci wirtualnych?

Jeśli odpowiedź na dowolne z tych pytań ma wartość tak, ustaw ograniczenia zgodnie z opisem w temacie [konfigurowanie log Analytics](#configure-log-analytics) obszarów roboczych i [Konfigurowanie składników Application Insights](#configure-application-insights) i kojarzenie tych zasobów z jednym lub kilkoma AMPLS. Sieci wirtualne, które powinny uzyskać dostęp do tych zasobów monitorowania, muszą mieć prywatny punkt końcowy, który nawiązuje połączenie z odpowiednim AMPLS.
Należy pamiętać, że można połączyć te same obszary robocze lub aplikacje z wieloma AMPLSami, aby umożliwić ich osiągnięcie przez różne sieci.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Grupuj wspólnie zasoby monitorowane według ułatwień dostępu do sieci

Ponieważ każda sieć wirtualna może łączyć się tylko z jednym zasobem AMPLS, należy grupować wspólnie monitorowane zasoby, które powinny być dostępne dla tych samych sieci. Najprostszym sposobem zarządzania tą grupą jest utworzenie jednego AMPLS na sieć wirtualną i wybranie zasobów do połączenia z tą siecią. Aby jednak ograniczyć zasoby i zwiększyć możliwości zarządzania, warto ponownie wykorzystać AMPLS między sieciami.

Na przykład jeśli wewnętrzne sieci wirtualne VNet1 i VNet2 powinny łączyć się z obszarami roboczymi Workspace1 i Workspace2, a składnik Application Insights Application Insights 3, należy skojarzyć wszystkie trzy zasoby z tym samym AMPLS. Jeśli sieci vnet3 powinna mieć dostęp tylko do Workspace1, utwórz kolejny zasób AMPLS, skojarz Workspace1 z nim i Połącz sieci vnet3, jak pokazano na następujących diagramach:

![Diagram AMPLS topologii](./media/private-link-security/ampls-topology-a-1.png)

![Diagram topologii AMPLS B](./media/private-link-security/ampls-topology-b-1.png)

### <a name="consider-limits"></a>Uwzględnij limity

Istnieje szereg ograniczeń, które należy wziąć pod uwagę podczas planowania konfiguracji linku prywatnego:

* Sieć wirtualna może łączyć się tylko z 1 AMPLS obiektem. Oznacza to, że obiekt AMPLS musi zapewnić dostęp do wszystkich zasobów Azure Monitor, do których sieć wirtualna powinna mieć dostęp.
* Zasób Azure Monitor (składnik obszaru roboczego lub Application Insights) może łączyć się z 5 AMPLSs.
* Obiekt AMPLS może łączyć się z zasobami 50 Azure Monitor.
* Obiekt AMPLS może łączyć się z 10 prywatnymi punktami końcowymi.

W poniższej topologii:
* Każda sieć wirtualna nawiązuje połączenie tylko z **1** AMPLS obiektem.
* AMPLS B jest połączona z prywatnymi punktami końcowymi dwóch sieci wirtualnych (VNet2 i sieci vnet3), przy użyciu 2/10 (20%) z możliwych połączeń prywatnych punktów końcowych.
* AMPLS łączy się do dwóch obszarów roboczych i jednego składnika usługi Application Insights, używając 3/50 (6%) z możliwych Azure Monitor połączeń zasobów.
* Workspace2 nawiązuje połączenie z AMPLS a i AMPLS B przy użyciu 2/5 (40%) z możliwych połączeń AMPLS.

![Diagram limitów AMPLS](./media/private-link-security/ampls-limits.png)

## <a name="example-connection"></a>Przykładowe połączenie

Zacznij od utworzenia zasobu zakresu prywatnego linku Azure Monitor.

1. Przejdź do pozycji **Utwórz zasób** w Azure Portal i Wyszukaj **Azure monitor prywatny zakres linków**.

   ![Znajdź Azure Monitor zakres linków prywatnych](./media/private-link-security/ampls-find-1c.png)

2. Kliknij przycisk **Utwórz**.
3. Wybierz subskrypcję i grupę zasobów.
4. Nadaj nazwę AMPLS. Najlepiej użyć nazwy, która jest jednoznaczne i granicą zabezpieczeń, której zakres będzie używany, aby ktoś nie pomógł przypadkowo przerwać granic zabezpieczeń sieci. Na przykład "AppServerProdTelem".
5. Kliknij pozycję **Przejrzyj i utwórz**. 

   ![Utwórz Azure Monitor zakres linków prywatnych](./media/private-link-security/ampls-create-1d.png)

6. Pozwól na przekazanie walidacji, a następnie kliknij przycisk **Utwórz**.

### <a name="connect-azure-monitor-resources"></a>Łączenie Azure Monitor zasobów

Połącz zasoby Azure Monitor (Log Analytics obszary robocze i składniki Application Insights) z AMPLS.

1. W zakresie prywatnego łącza Azure Monitor kliknij pozycję **zasoby Azure monitor** w menu po lewej stronie. Kliknij przycisk **Dodaj** .
2. Dodaj obszar roboczy lub składnik. Kliknięcie przycisku **Dodaj** powoduje wyświetlenie okna dialogowego, w którym można wybrać Azure monitor zasoby. Możesz przeglądać subskrypcje i grupy zasobów lub wpisywać ich nazwy, aby filtrować do nich. Wybierz obszar roboczy lub składnik, a następnie kliknij przycisk **Zastosuj** , aby dodać je do zakresu.

    ![Zrzut ekranu przedstawiający środowisko Select a Scope](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Usuwanie zasobów Azure Monitor wymaga, aby najpierw odłączyć je od wszelkich obiektów AMPLS, z którymi są połączone. Nie jest możliwe usunięcie zasobów połączonych z AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Nawiązywanie połączenia z prywatnym punktem końcowym

Teraz, gdy masz zasoby połączone z AMPLS, Utwórz prywatny punkt końcowy, aby połączyć naszą sieć. To zadanie można wykonać w ramach [prywatnego centrum linków Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)lub wewnątrz zakresu prywatnego linku Azure monitor, jak zostało to zrobione w tym przykładzie.

1. W przystawce zasób zakresu kliknij pozycję **połączenia prywatnych punktów końcowych** w menu zasobów po lewej stronie. Kliknij pozycję **prywatny punkt końcowy** , aby uruchomić proces tworzenia punktu końcowego. Możesz także zatwierdzać połączenia, które zostały uruchomione w centrum linku prywatnego, wybierając je i klikając przycisk **Zatwierdź**.

    ![Zrzut ekranu środowiska użytkownika połączenia z prywatnymi punktami końcowymi](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Wybierz subskrypcję, grupę zasobów i nazwę punktu końcowego oraz region, w którym powinien się znajdować. Region musi być tym samym regionem co sieć wirtualna, z którą zostanie nawiązane połączenie.

3. Kliknij przycisk **Dalej: zasób**. 

4. Na ekranie zasób

   a. Wybierz **subskrypcję** zawierającą zasób zakresu prywatnego Azure monitor. 

   b. W obszarze **Typ zasobu** wybierz pozycję **Microsoft. Insights/privateLinkScopes**. 

   c. Z listy rozwijanej **zasób** wybierz swój prywatny zakres linków, który został utworzony wcześniej. 

   d. Kliknij przycisk **Dalej: konfiguracja >**.
      ![Zrzut ekranu przedstawiający pozycję Utwórz prywatny punkt końcowy](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. W okienku Konfiguracja,

   a.    Wybierz **sieć wirtualną** i **podsieć** , którą chcesz połączyć z zasobami Azure monitor. 
 
   b.    Wybierz opcję **tak** dla **integracji z prywatną strefą DNS** i zezwól na automatyczne tworzenie nowej strefy prywatna strefa DNS. Rzeczywiste strefy DNS mogą się różnić od tego, co pokazano na poniższym zrzucie ekranu. 
   > [!NOTE]
   > Jeśli wybierzesz opcję **nie** i wolisz ręcznie zarządzać rekordami DNS, najpierw Ukończ Konfigurowanie linku prywatnego — łącznie z tym prywatnym punktem końcowym i konfiguracją AMPLS. Następnie skonfiguruj system DNS zgodnie z instrukcjami w temacie [Konfiguracja DNS prywatnego punktu końcowego platformy Azure](../../private-link/private-endpoint-dns.md). Upewnij się, że nie chcesz tworzyć pustych rekordów jako przygotowania do konfiguracji linku prywatnego. Tworzone rekordy DNS mogą przesłaniać istniejące ustawienia i mieć wpływ na łączność z Azure Monitor.
 
   c.    Kliknij pozycję **Przejrzyj i utwórz**.
 
   d.    Zezwalaj na weryfikację. 
 
   e.    Kliknij pozycję **Utwórz**. 

    ![Zrzut ekranu przedstawiający pozycję Utwórz prywatny Endpoint2](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Utworzono nowy prywatny punkt końcowy, który jest połączony z tym Azure Monitor zakresem linków prywatnych.

## <a name="configure-log-analytics"></a>Konfigurowanie usługi Log Analytics

Przejdź do witryny Azure Portal. W obszarze Log Analytics zasobów obszaru roboczego istnieje **izolacja sieciowa** elementu menu po lewej stronie. W tym menu można kontrolować dwa różne stany.

![Izolacja sieci LA](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Połączone Azure Monitor zakresy linków prywatnych
Wszystkie zakresy połączone z tym obszarem roboczym są wyświetlane na tym ekranie. Łączenie z zakresami (AMPLSs) zezwala na ruch sieciowy z sieci wirtualnej połączonej z każdym AMPLS, aby dotrzeć do tego obszaru roboczego. Utworzenie połączenia w tym miejscu ma taki sam skutek jak ustawienie go w zakresie, tak jak w przypadku [łączenia Azure Monitor zasobów](#connect-azure-monitor-resources). Aby dodać nowe połączenie, kliknij przycisk **Dodaj** i wybierz prywatny zakres linków Azure monitor. Kliknij przycisk **Zastosuj** , aby nawiązać połączenie. Należy pamiętać, że obszar roboczy może połączyć się z 5 AMPLS obiektów, jak wyjaśniono w temacie [limity](#consider-limits). 

### <a name="access-from-outside-of-private-links-scopes"></a>Dostęp spoza zakresów linków prywatnych
Ustawienia w dolnej części tej strony kontrolują dostęp z sieci publicznych, czyli oznacza sieci, które nie są połączone przez wymienione powyżej zakresy. Jeśli ustawisz opcję **Zezwalaj na dostęp do sieci publicznej na potrzeby** pozyskiwania na **nie**, maszyny spoza połączonych zakresów nie mogą przekazywać danych do tego obszaru roboczego. Jeśli ustawisz opcję **Zezwalaj na dostęp do sieci publicznej dla zapytań** na wartość **nie**, wówczas maszyny spoza zakresów nie będą miały dostępu do danych w tym obszarze roboczym, co oznacza, że nie będzie możliwe wykonywanie zapytań dotyczących danych obszaru roboczego. Obejmuje zapytania w skoroszytach, pulpitach nawigacyjnych, środowiska klienta opartego na interfejsie API, szczegółowe informacje w Azure Portal i wiele innych. Środowiska działające poza Azure Portal i że zapytanie Log Analytics dane muszą być uruchomione w prywatnej sieci wirtualnej.

### <a name="exceptions"></a>Wyjątki
Ograniczanie dostępu, jak wyjaśniono powyżej, nie ma zastosowania do Azure Resource Manager i dlatego ma następujące ograniczenia:
* Dostęp do danych — podczas blokowania/zezwalania na zapytania z sieci publicznych stosuje się do większości Log Analyticsych środowisk, ale niektóre z nich wykonują zapytania dotyczące danych za pośrednictwem Azure Resource Manager i w związku z tym nie będą mogły wysyłać zapytań o dane, chyba że prywatne ustawienia linku są stosowane również do Menedżer zasobów (funkcja jest dostępna wkrótce). Dotyczy to na przykład Azure Monitor rozwiązań, skoroszytów i szczegółowych informacji oraz łącznika LogicApp.
* Zarządzanie obszarem roboczym — ustawienia obszaru roboczego i zmiany konfiguracji (w tym Włączanie lub wyłączanie tych ustawień dostępu) są zarządzane przez Azure Resource Manager. Ogranicz dostęp do zarządzania obszarami roboczymi przy użyciu odpowiednich ról, uprawnień, kontroli sieci i inspekcji. Aby uzyskać więcej informacji, zobacz [Azure monitor role, uprawnienia i zabezpieczenia](roles-permissions-security.md).

> [!NOTE]
> Dzienniki i metryki przekazane do obszaru roboczego za pośrednictwem [ustawień diagnostycznych](diagnostic-settings.md) korzystają z bezpiecznego prywatnego kanału firmy Microsoft i nie są kontrolowane przez te ustawienia.

### <a name="log-analytics-solution-packs-download"></a>Pobieranie pakietów rozwiązań Log Analytics

Aby zezwolić agentowi Log Analytics na pobieranie pakietów rozwiązań, Dodaj odpowiednie nazwy FQDN do listy dozwolonych zapór. 


| Środowisko chmury | Zasób agenta | Porty | Kierunek |
|:--|:--|:--|:--|
|Azure — publiczna     | scadvisorcontent.blob.core.windows.net         | 443 | Wychodzący
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Wychodzący
|Azure w Chinach — 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Wychodzący

## <a name="configure-application-insights"></a>Konfigurowanie Application Insights

Przejdź do witryny Azure Portal. W Azure Monitor Application Insights zasobów składnika jest **izolacja sieci** elementu menu po lewej stronie. W tym menu można kontrolować dwa różne stany.

![Izolacja sieci AI](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Najpierw można podłączyć ten zasób Application Insights do Azure Monitor prywatnych zakresów łączy, do których masz dostęp. Kliknij przycisk **Dodaj** i wybierz **prywatny zakres linków Azure monitor**. Kliknij przycisk Zastosuj, aby nawiązać połączenie. Wszystkie połączone zakresy są wyświetlane na tym ekranie. Nawiązanie tego połączenia zezwala na dostęp do tego składnika przez ruch sieciowy podłączonych sieci wirtualnych. Nawiązywanie połączenia ma ten sam skutek, co połączenie z zakresem, tak jak w przypadku [łączenia Azure Monitor zasobów](#connect-azure-monitor-resources). 

Następnie można kontrolować sposób, w jaki można uzyskać dostęp do tego zasobu poza wymienionymi powyżej zakresami linków prywatnych. Jeśli ustawisz opcję **Zezwalaj na dostęp do sieci publicznej na potrzeby** pozyskiwania **nie**, wówczas maszyny lub zestawy SDK poza połączonymi zakresami nie mogą przekazywać danych do tego składnika. Jeśli ustawisz opcję **Zezwalaj na dostęp do sieci publicznej dla zapytań** na wartość **nie**, wówczas maszyny spoza zakresów nie mogą uzyskać dostępu do danych w tym zasobie Application Insights. Te dane obejmują dostęp do dzienników APM, metryk i strumienia metryk na żywo, a także wbudowanych środowisk, takich jak skoroszyty, pulpity nawigacyjne, zapytania dotyczące środowiska klienta opartego na interfejsie API, szczegółowe informacje w Azure Portal i inne. 

Należy zauważyć, że środowiska użycia poza portalem również muszą być uruchomione w ramach prywatnej sieci wirtualnej, która obejmuje monitorowane obciążenia. 

Do prywatnego linku należy dodać zasoby obsługujące monitorowane obciążenia. [W tym](../../app-service/networking/private-endpoint.md) artykule opisano, jak to zrobić dla App Services.

Ograniczanie dostępu w ten sposób dotyczy tylko danych w zasobie Application Insights. Zmiany konfiguracji, w tym Włączanie lub wyłączanie ustawień dostępu, są zarządzane przez Azure Resource Manager. Zamiast tego należy ograniczyć dostęp do Menedżer zasobów przy użyciu odpowiednich ról, uprawnień, kontroli sieci i inspekcji. Aby uzyskać więcej informacji, zobacz [Azure monitor role, uprawnienia i zabezpieczenia](roles-permissions-security.md).

> [!NOTE]
> Aby w pełni zabezpieczyć Application Insights oparte na obszarze roboczym, musisz zablokować zarówno dostęp do zasobu Application Insights, jak i bazowego obszaru roboczego Log Analytics.
>
> Diagnostyka na poziomie kodu (Profiler/debuger) wymaga podania własnego konta magazynu do obsługi linku prywatnego. Tutaj znajduje się [Dokumentacja](../app/profiler-bring-your-own-storage.md) umożliwiająca wykonanie tej czynności.

## <a name="use-apis-and-command-line"></a>Korzystanie z interfejsów API i wiersza polecenia

Proces opisany wcześniej można zautomatyzować za pomocą szablonów Azure Resource Manager i interfejsów wiersza polecenia.

Aby utworzyć prywatne zakresy łączy i zarządzać nimi, użyj polecenie [AZ monitor Private-Scope](/cli/azure/monitor/private-link-scope?view=azure-cli-latest). Za pomocą tego polecenia można tworzyć zakresy, kojarzyć Log Analytics obszary robocze i składniki Application Insights oraz dodawać/usuwać/zatwierdzać prywatne punkty końcowe.

Aby zarządzać dostępem do sieci, użyj flag `[--ingestion-access {Disabled, Enabled}]` i `[--query-access {Disabled, Enabled}]` na [log Analytics obszarach roboczych](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest) lub [składników Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest).

## <a name="collect-custom-logs-over-private-link"></a>Zbierz niestandardowe dzienniki za pośrednictwem prywatnego linku

Konta magazynu są używane w procesie pozyskiwania dzienników niestandardowych. Domyślnie są używane konta magazynu zarządzane przez usługę. Jednak w przypadku linków prywatnych można korzystać z własnych kont magazynu i kojarzyć je z obszarami roboczymi Log Analytics. Zobacz więcej szczegółowych informacji na temat konfigurowania takich kont przy użyciu [wiersza polecenia](/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest).

Aby uzyskać więcej informacji na temat przełączania własnych kont magazynu, zobacz [konta magazynu należące do klienta na potrzeby](private-storage.md) pozyskiwania dziennika

## <a name="restrictions-and-limitations"></a>Ograniczenia i ograniczenia

### <a name="agents"></a>Agenci

Najnowsze wersje agentów systemów Windows i Linux muszą być używane w sieciach prywatnych w celu umożliwienia bezpiecznego pozyskiwania Log Analytics obszarów roboczych. Starsze wersje nie mogą przekazywać danych monitorowania w sieci prywatnej.

**Agent usługi Log Analytics dla systemu Windows**

Użyj agenta Log Analytics w wersji 10.20.18038.0 lub nowszej.

**Agent usługi Log Analytics dla systemu Linux**

Użyj agenta w wersji 1.12.25 lub nowszej. Jeśli nie możesz, uruchom następujące polecenia na maszynie wirtualnej.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

Aby korzystać z środowisk Azure Monitor Portal, takich jak Application Insights i Log Analytics, należy zezwolić na dostęp rozszerzeń Azure Portal i Azure Monitor w sieciach prywatnych. Dodaj [znaczniki usługi](../../firewall/service-tags.md) **usługi azureactivedirectory**, **AzureResourceManager**, **AzureFrontDoor. FirstParty** i **AzureFrontDoor. frontonu** do sieciowej grupy zabezpieczeń.

### <a name="programmatic-access"></a>Dostęp programowy

Aby użyć interfejsu API REST, interfejsu [wiersza polecenia](/cli/azure/monitor?view=azure-cli-latest) lub programu PowerShell z Azure monitor w sieciach prywatnych, należy dodać do zapory [Tagi usług](../../virtual-network/service-tags-overview.md)  **usługi azureactivedirectory** i **AzureResourceManager** .

Dodanie tych tagów umożliwia wykonywanie akcji, takich jak wykonywanie zapytań dotyczących danych dzienników, tworzenie i zarządzanie obszarami roboczymi Log Analytics i składnikami AI.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights pobierania zestawu SDK z usługi Content Delivery Network

W skrypcie należy powiązać kod JavaScript w taki sposób, aby przeglądarka nie próbowała pobrać kodu z sieci CDN. Przykład jest dostępny w witrynie [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>Ustawienia usługi DNS przeglądarki

W przypadku łączenia się z zasobami Azure Monitor za pośrednictwem prywatnego linku ruch do tych zasobów musi przechodzić przez prywatny punkt końcowy skonfigurowany w sieci. Aby włączyć prywatny punkt końcowy, zaktualizuj ustawienia DNS zgodnie z opisem w temacie [Connect to Private Endpoint](#connect-to-a-private-endpoint). Niektóre przeglądarki używają własnych ustawień DNS zamiast ustawionych przez użytkownika. Przeglądarka może próbować nawiązać połączenie z Azure Monitor publicznymi punktami końcowymi i całkowicie ominąć prywatny link. Sprawdź, czy ustawienia przeglądarki nie przesłaniają ani nie buforują starych ustawień DNS. 

## <a name="next-steps"></a>Następne kroki

- Informacje o [magazynie prywatnym](private-storage.md)