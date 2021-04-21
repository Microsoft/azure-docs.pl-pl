---
title: Używanie usługi Azure API Management z sieciami wirtualnymi
description: Dowiedz się, jak skonfigurować połączenie z siecią wirtualną na platformie Azure API Management uzyskać za jej pośrednictwem dostęp do usług internetowych.
services: api-management
author: vladvino
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 5808cda95cdf9ce6477f47fcdbb8a0421d92e72a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817134"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Używanie usługi Azure API Management z sieciami wirtualnymi
Sieci wirtualne platformy Azure umożliwiają umieszczanie dowolnych zasobów platformy Azure w sieci nieobsługującej routingu internetowego, do której kontrolujesz dostęp. Te sieci można następnie połączyć z sieciami lokalnymi przy użyciu różnych technologii sieci VPN. Aby dowiedzieć się więcej na temat sieci wirtualnych platformy Azure, zapoznaj się z informacjami dostępnymi tutaj: Azure Virtual Network Overview (Omówienie usługi [Azure).](../virtual-network/virtual-networks-overview.md)

Usługę Azure API Management można wdrożyć w sieci wirtualnej, aby mieć dostęp do usług zaplecza w sieci. Portal dla deweloperów i brama interfejsu API można skonfigurować tak, aby były dostępne z Internetu lub tylko w sieci wirtualnej.

> [!NOTE]
> Adres URL dokumentu importu interfejsu API musi być hostowany na publicznie dostępnym adresie internetowym.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

+ **Aktywna subskrypcja platformy Azure.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Wystąpienie API Management danych.** Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management .](get-started-create-service-instance.md)

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>Włączanie połączenia sieci wirtualnej

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Włączanie łączności z siecią wirtualną przy użyciu Azure Portal

1. Przejdź do [Azure Portal,](https://portal.azure.com) aby znaleźć wystąpienie usługi API Management. Wyszukaj i wybierz **pozycję API Management usług .**

1. Wybierz swoje API Management wystąpienie.

1. Wybierz **pozycję Sieć wirtualna.**
1. Skonfiguruj API Management do wdrożenia w sieci wirtualnej.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Wybierz sieć wirtualną w Azure Portal.":::
    
1. Wybierz żądany typ dostępu:

    * **Wyłączone:** jest to wartość domyślna. API Management nie jest wdrożony w sieci wirtualnej.

    * **Zewnętrzne:** brama API Management i portal dla deweloperów są dostępne z publicznego Internetu za pośrednictwem zewnętrznego usługi równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

        ![Publiczna komunikacja równorzędna][api-management-vnet-public]

    * **Wewnętrzne:** brama API Management portal dla deweloperów są dostępne tylko z poziomu sieci wirtualnej za pośrednictwem wewnętrznego równoważenia obciążenia. Brama może uzyskać dostęp do zasobów w sieci wirtualnej.

        ![Prywatna komunikacja równorzędna][api-management-vnet-private]

1. Jeśli wybrano **opcję Zewnętrzne** lub **Wewnętrzne,** zostanie wyświetlona lista wszystkich lokalizacji (regionów), w których API Management jest aprowizowana. Wybierz **lokalizację**, a następnie wybierz jej sieć **wirtualną,** **podsieć** i **adres IP.** Lista sieci wirtualnych jest wypełniana Resource Manager wirtualnych dostępnych w subskrypcjach platformy Azure, które są konfigurowane w konfigurowalnym regionie.


    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Ustawienia sieci wirtualnej w portalu.":::

    > [!IMPORTANT]
    > * Jeśli klient używa interfejsu API w wersji **2020-12-01** lub starszej do wdrożenia wystąpienia usługi Azure API Management w sieci wirtualnej usługi Resource Manager, usługa musi znajdować się w dedykowanej podsieci, która nie zawiera żadnych zasobów z wyjątkiem wystąpień usługi Azure API Management. W przypadku próby wdrożenia wystąpienia usługi Azure API Management w podsieci sieci Resource Manager wirtualnej, która zawiera inne zasoby, wdrożenie nie powiedzie się.
    > * Jeśli klient używa interfejsu API w wersji **2021-01-01-preview** lub nowszej do wdrożenia wystąpienia usługi Azure API Management w sieci wirtualnej, obsługiwana jest tylko Resource Manager wirtualna. Ponadto używana podsieć może zawierać inne zasoby. Nie musisz używać podsieci dedykowanej dla API Management wystąpień. 

1. Wybierz przycisk **Zastosuj**. Strona **Sieć wirtualna** wystąpienia API Management zostanie zaktualizowana o nową sieć wirtualną i podsieć.

1. Kontynuuj konfigurowanie ustawień sieci wirtualnej dla pozostałych lokalizacji wystąpienia API Management wirtualnego.

7. Na górnym pasku nawigacyjnym wybierz pozycję **Zapisz,** a następnie wybierz pozycję **Zastosuj konfigurację sieci.**

    Zaktualizowanie wystąpienia API Management może potrwać od 15 do 45 minut.

> [!NOTE]
> W przypadku klientów korzystających z interfejsu API w wersji 2020-12-01 i starszych adres VIP wystąpienia usługi API Management zmienia się za każdym razem, gdy sieć wirtualna jest włączona lub wyłączona. Adres VIP również zmieni się, gdy adres API Management zostanie przeniesiony **z zewnętrznej** do wewnętrznej sieci **wirtualnej** lub na odwrót.

> [!IMPORTANT]
> Jeśli usuniesz API Management wirtualnej lub zmienisz sieć wdrożoną w tej sieci, wcześniej używana sieć wirtualna może pozostać zablokowana przez maksymalnie sześć godzin. W tym okresie nie będzie można usunąć sieci wirtualnej ani wdrożyć w nim nowego zasobu. To zachowanie dotyczy klientów korzystających z interfejsu API w wersji 2018-01-01 i starszych. Klienci korzystający z interfejsu API w wersji 2019-01-01 lub nowszej mogą korzystać z sieci wirtualnej natychmiast po usunięciu skojarzonej API Management wirtualnej.

### <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>Wdrażanie API Management w zewnętrznej sieci wirtualnej

Możesz również włączyć łączność z siecią wirtualną przy użyciu następujących metod.

### <a name="api-version-2021-01-01-preview"></a>Interfejs API w wersji 2021-01-01-preview

* Azure Resource Manager [szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet-publicip)

     [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet-publicip%2Fazuredeploy.json)

### <a name="api-version-2020-12-01"></a>Interfejs API w wersji 2020-12-01

* Azure Resource Manager [szablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet)
    
     [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* Azure PowerShell cmdlet — [tworzenie](/powershell/module/az.apimanagement/new-azapimanagement) lub [aktualizowanie](/powershell/module/az.apimanagement/update-azapimanagementregion) wystąpienia API Management w sieci wirtualnej

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Nawiązywanie połączenia z usługą internetową hostowaną w sieci wirtualnej
Po na API Management sieci wirtualnej uzyskiwanie dostępu do usług zaplecza w tej sieci nie różni się od uzyskiwania dostępu do usług publicznych. Wystarczy wpisać lokalny adres IP lub nazwę hosta (jeśli serwer DNS jest skonfigurowany dla sieci wirtualnej) usługi internetowej w polu Adres URL usługi internetowej podczas tworzenia nowego interfejsu API lub edytowania istniejącego interfejsu **API.**

![Dodawanie interfejsu API z sieci VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Typowe problemy z konfiguracją sieci
Poniżej znajduje się lista typowych problemów z błędną konfiguracją, które mogą wystąpić podczas API Management usługi w Virtual Network.

* **Niestandardowa konfiguracja serwera DNS:** usługa API Management zależy od kilku usług platformy Azure. Jeśli API Management jest hostowana w sieci wirtualnej z niestandardowym serwerem DNS, musi rozpoznać nazwy hostów tych usług platformy Azure. Postępuj zgodnie z [tym wskazówkami](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) w zakresie niestandardowej konfiguracji dns. Zapoznaj się z tabelą portów poniżej i innymi wymaganiami sieciowymi, aby uzyskać informacje referencyjne.

> [!IMPORTANT]
> Jeśli zamierzasz używać niestandardowych serwerów DNS dla sieci wirtualnej,  należy skonfigurować ją przed wdrożeniem w nim API Management DNS. W przeciwnym razie musisz zaktualizować usługę API Management przy każdej zmianie serwerów DNS, uruchamiając operację [Zastosuj konfigurację sieci](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)

* **Porty wymagane dla usługi API Management:** ruch przychodzący i wychodzący do podsieci, w której wdrożono API Management sieciową grupę zabezpieczeń, można kontrolować za [pomocą sieciowej grupy zabezpieczeń.][Network Security Group] Jeśli którykolwiek z tych portów jest niedostępny, API Management działać prawidłowo i mogą stać się niedostępne. Zablokowanie co najmniej jednego z tych portów jest kolejnym powszechnym problemem z błędną konfiguracją podczas korzystania API Management z siecią wirtualną.

<a name="required-ports"></a> Gdy wystąpienie API Management jest hostowane w sieci wirtualnej, używane są porty w poniższej tabeli.

| Porty źródłowe/docelowe | Kierunek          | Protokół transportowy |   [Tagi usługi](../virtual-network/network-security-groups-overview.md#service-tags) <br> Źródło/miejsce docelowe   | Cel ( \* )                                                 | Virtual Network typu |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Przychodzący            | TCP                | INTERNET/VIRTUAL_NETWORK            | Komunikacja klienta z API Management                      | Zewnętrzna             |
| * / 3443                     | Przychodzący            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Punkt końcowy zarządzania dla Azure Portal i programu PowerShell         | Zewnętrzne & wewnętrzne  |
| * / 443                  | Wychodzący           | TCP                | VIRTUAL_NETWORK /Storage             | **Zależność od usługi Azure Storage**                             | Zewnętrzne & wewnętrzne  |
| * / 443                  | Wychodzący           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) i zależność usługi Azure KeyVault                  | Zewnętrzne & wewnętrzne  |
| * / 1433                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / SQL                 | **Dostęp do Azure SQL końcowych**                           | Zewnętrzne & wewnętrzne  |
| * / 443                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / AzureKeyVault                 | **Dostęp do usługi Azure KeyVault**                           | Zewnętrzne & wewnętrzne  |
| * / 5671, 5672, 443          | Wychodzący           | TCP                | VIRTUAL_NETWORK / EventHub            | Zależność dla [zasad i agenta monitorowania usługi Log to Event Hubs](api-management-howto-log-event-hubs.md) | Zewnętrzne & wewnętrzne  |
| * / 445                      | Wychodzący           | TCP                | VIRTUAL_NETWORK / Storage             | Zależność od udziału plików platformy Azure dla usługi [GIT](api-management-configuration-repository-git.md)                      | Zewnętrzne & wewnętrzne  |
| * / 443, 12000                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Rozszerzenie kondycji i monitorowania         | Zewnętrzne & wewnętrzne  |
| * / 1886, 443                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publikowanie [dzienników diagnostycznych i metryk,](api-management-howto-use-azure-monitor.md) [Resource Health](../service-health/resource-health-overview.md) i [Application Insights](api-management-howto-app-insights.md)                   | Zewnętrzne & wewnętrzne  |
| * / 25, 587, 25028                       | Wychodzący           | TCP                | VIRTUAL_NETWORK / INTERNET            | Nawiązywanie połączenia z przekaźnikiem SMTP w celu wysyłania wiadomości e-mail                    | Zewnętrzne & wewnętrzne  |
| * / 6381 - 6383              | Ruch przychodzący & wychodzący | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Uzyskiwanie dostępu do usługi Redis dla [zasad pamięci podręcznej](api-management-caching-policies.md) między maszynami         | Zewnętrzne & wewnętrzne  |
| * / 4290              | Ruch przychodzący & wychodzący | UDP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Liczniki synchronizacji dla [zasad limitu szybkości](api-management-access-restriction-policies.md#LimitCallRateByKey) między maszynami         | Zewnętrzne & wewnętrzne  |
| * / *                        | Przychodzący            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure Infrastructure Load Balancer                          | Zewnętrzne & wewnętrzne  |

>[!IMPORTANT]
> Porty, dla których cel *jest* **pogrubiony,** są wymagane API Management usługi do pomyślnego wdrożenia. Zablokowanie innych portów spowoduje jednak pogorszenie **możliwości** korzystania z uruchomionej usługi i monitorowania jej oraz zapewnienia **zatwierdzonej umowy SLA.**

+ **Funkcja protokołu TLS:** aby umożliwić tworzenie i walidację łańcucha certyfikatów TLS/SSL, usługa API Management musi mieć łączność sieciową wychodzącą z sieciami ocsp.msocsp.com, mscrl.microsoft.com i crl.microsoft.com. Ta zależność nie jest wymagana, jeśli dowolny certyfikat API Management zawiera pełny łańcuch do katalogu głównego urzędu certyfikacji.

+ **Dostęp DNS:** dostęp wychodzący na porcie 53 jest wymagany do komunikacji z serwerami DNS. Jeśli niestandardowy serwer DNS istnieje na drugim końcu bramy sieci VPN, serwer DNS musi być dostępny z podsieci hostującym API Management.

+ **Monitorowanie metryk i kondycji:** wychodząca łączność sieciowa z punktami końcowymi monitorowania platformy Azure, które są rozwiązywane w następujących domenach. Jak pokazano w tabeli, te adresy URL są reprezentowane w tagu usługi AzureMonitor do użycia z sieciowymi grupami zabezpieczeń.

    | Środowisko platformy Azure | Punkty końcowe                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure — publiczna      | <ul><li>gcs.prod.monitoring.core.windows.net(**nowy**)</li><li>global.prod.microsoftmetrics.com(**nowy**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**nowy**)</li><li>prod3-black.prod.microsoftmetrics.com(**nowy**)</li><li>prod3-red.prod.microsoftmetrics.com(**nowy**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com(**nowy**)</li><li>shoebox2.prod.microsoftmetrics.com(**nowy**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**nowy**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure w Chinach — 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com(**nowy**)</li><li>shoebox2.prod.microsoftmetrics.com(**new**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**nowy**)</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > Zmiana powyższych klastrów ze strefą DNS **na .nsatc.net** **na .microsoftmetrics.com** jest przede wszystkim zmianą DNS. Adres IP klastra nie zmieni się.

+ Tagi usług regionalnych: reguły sieciowej organizacji zabezpieczeń zezwalają na łączność wychodzącą do tagów usług Storage, SQL i Event Hubs mogą używać regionalnych wersji tych tagów odpowiadających regionowi zawierającego wystąpienie usługi API Management (na przykład Storage.WestUS dla wystąpienia usługi API Management w regionie Zachodnie stany USA). We wdrożeniach w wielu regionach sieciowa żadna z tych sieci w każdym regionie powinna zezwalać na ruch do tagów usługi dla tego regionu i regionu podstawowego.

    > [!IMPORTANT]
    > Aby umożliwić publikowanie [portalu](api-management-howto-developer-portal.md) dla deweloperów dla wystąpienia API Management w sieci wirtualnej, upewnij się, że zezwalasz również na łączność wychodzącą z magazynem obiektów blob w regionie Zachodnie stany USA. Na przykład użyj tagu **usługi Storage.WestUS** w regułę sieciowej sieciowej organizacji. Łączność z magazynem obiektów blob w regionie Zachodnie stany USA jest obecnie wymagana do publikowania portalu dla deweloperów dla dowolnego API Management wystąpienia.

+ **Smtp Relay:** wychodząca łączność sieciowa dla przekaźnika SMTP, która jest rozpoznawzana w ramach hosta `smtpi-co1.msn.com` , , `smtpi-ch1.msn.com` `smtpi-db3.msn.com` i `smtpi-sin.msn.com``ies.global.microsoft.com`

+ **CAPTCHA** portalu dla deweloperów: łączność sieciowa ruchu wychodzącego dla karty CAPTCHA portalu dla deweloperów, która jest rozpoznawcza w ramach hostów `client.hip.live.com` i `partner.hip.live.com` .

+ **Azure Portal diagnostyki:** aby włączyć przepływ dzienników diagnostycznych z usługi Azure Portal w przypadku korzystania z rozszerzenia API Management z wewnątrz serwera Virtual Network, wymagany jest dostęp wychodzący do portu `dc.services.visualstudio.com` 443. Pomaga to w rozwiązywaniu problemów, które mogą wystąpić podczas korzystania z rozszerzenia.

+ **Azure Load Balancer:** Zezwalanie na żądanie przychodzące z tagu usługi nie jest wymagane dla jednostki SKU, ponieważ wdrażamy za nią tylko jedną jednostkę `AZURE_LOAD_BALANCER` `Developer` obliczeniową. Jednak ruch przychodzący z [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) staje się krytyczny w przypadku skalowania do wyższej wartości SKU, na przykład , ponieważ niepowodzenie sondy kondycji z programu `Premium` Load Balancer kończy się niepowodzeniem wdrożenia.

+ **Application Insights:** jeśli monitorowanie usługi [Azure Application Insights](api-management-howto-app-insights.md) jest włączone na platformie API Management, należy zezwolić [](../azure-monitor/app/ip-addresses.md#outgoing-ports) na łączność wychodzącą z punktu końcowego telemetrii z Virtual Network. 

+ Wymuszanie tunelowania ruchu do zapory lokalnej przy użyciu usługi **Express Route** lub wirtualnego urządzenia sieciowego: Typowa konfiguracja klienta to zdefiniowanie własnej trasy domyślnej (0.0.0.0/0), która wymusza przepływ całego ruchu z podsieci delegowanego programu API Management przez lokalną zaporę lub do wirtualnego urządzenia sieciowego. Ten przepływ ruchu niezmiennie przerywa łączność z usługą Azure API Management, ponieważ ruch wychodzący jest blokowany lokalnie lub nat do nierozpoznawalnego zestawu adresów, które nie działają już z różnymi punktami końcowymi platformy Azure. Rozwiązanie wymaga kilku czynności:

  * Włącz punkty końcowe usługi w podsieci, w której wdrożono API Management usługi. [Punkty końcowe usługi muszą][ServiceEndpoints] być włączone dla usług Azure Sql, Azure Storage, Azure EventHub i Azure ServiceBus. Włączenie punktów końcowych bezpośrednio API Management podsieci delegowanej do tych usług umożliwia im korzystanie z sieci Microsoft Azure sieci szkieletowej zapewniającej optymalny routing dla ruchu w usłudze. Jeśli używasz punktów końcowych usługi z wymuszonym tunelem usługi Api Management, powyższy ruch usług platformy Azure nie jest tunelowany w sposób wymuszony. Drugi ruch API Management zależności usługi jest tunelowany i nie można go utracić lub usługa API Management nie będzie działać prawidłowo.
    
  * Cały ruch płaszczyzny sterowania z Internetu do punktu końcowego zarządzania usługi API Management jest trasowany za pośrednictwem określonego zestawu przychodzących ip hostowanych przez API Management. Gdy ruch jest wymuszany tunelowanie odpowiedzi nie zostaną symetrycznie mapowane z powrotem na te źródłowe ip ruchu przychodzącego. Aby wyeliminować to ograniczenie, musimy dodać następujące trasy zdefiniowane przez użytkownika[(trasy][UDRs]zdefiniowane przez użytkownika), aby skierować ruch z powrotem na platformę Azure, ustawiając miejsce docelowe tych tras hosta na "Internet". Zestaw adresów IP dla ruchu płaszczyzny sterowania jest udokumentowany adresy [IP płaszczyzny sterowania](#control-plane-ips)

  * W przypadku API Management usług, które są tunelowane w sposób wymuszany, powinien być sposób rozpoznania nazwy hosta i nakierowania na punkt końcowy. Należą do nich:
      - Monitorowanie metryk i kondycji
      - Azure Portal diagnostyki
      - SMTP Relay
      - Portal dla deweloperów CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Rozwiązywanie problemów
* **Konfiguracja początkowa:** jeśli początkowe wdrożenie usługi API Management w podsieci nie powiedzie się, zaleca się, aby najpierw wdrożyć maszynę wirtualną w tej samej podsieci. Następnie na maszynie wirtualnej na pulpicie zdalnym sprawdź, czy istnieje łączność z jednym z poniższych zasobów w ramach subskrypcji platformy Azure
    * Obiekt blob usługi Azure Storage
    * Azure SQL Database
    * Tabela usługi Azure Storage

  > [!IMPORTANT]
  > Po weryfikacji łączności pamiętaj o usunięciu wszystkich zasobów wdrożonych w podsieci przed wdrożeniem API Management podsieci.

* **Sprawdź stan łączności sieciowej:** po wdrożeniu API Management podsieci użyj portalu, aby sprawdzić łączność wystąpienia z zależnościami, takimi jak usługa Azure Storage. W portalu w menu po lewej stronie w obszarze **Wdrożenie i infrastruktura** wybierz pozycję Stan łączności **sieciowej**.

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Weryfikowanie stanu łączności sieciowej w portalu":::

    * Wybierz **pozycję Wymagane,** aby przejrzeć łączność z wymaganymi usługami platformy Azure dla API Management. Błąd wskazuje, że wystąpienie nie może wykonać podstawowych operacji w celu zarządzania interfejsami API.
    * Wybierz **pozycję Opcjonalne,** aby przejrzeć łączność z usługami opcjonalnymi. Każdy błąd wskazuje tylko, że określone funkcje nie będą działać (na przykład SMTP). Awaria może prowadzić do pogorszenia możliwości używania i monitorowania wystąpienia API Management oraz zapewnienia zatwierdzonej umowy SLA.

Aby rozwiązać problemy z łącznością, zapoznaj się z [tematem Typowe problemy z konfiguracją sieci](#network-configuration-issues) i popraw wymagane ustawienia sieciowe.

* **Aktualizacje przyrostowe:** podczas dokonywania zmian w sieci zapoznaj się z interfejsem [API NetworkStatus,](/rest/api/apimanagement/2019-12-01/networkstatus)aby sprawdzić, czy usługa API Management nie utraciła dostępu do żadnych krytycznych zasobów, od których to zależy. Stan łączności powinien być aktualizowany co 15 minut.

* **Linki nawigacji po zasobach:** podczas wdrażania Resource Manager podsieci sieci wirtualnej w stylu API Management rezerwuje podsieć, tworząc link nawigacji po zasobach. Jeśli podsieć zawiera już zasób od innego dostawcy, wdrożenie nie **powiedzie się.** Podobnie w przypadku przeniesienia usługi API Management do innej podsieci lub jej usunięcia usuniemy ten link nawigacji po zasobach.

## <a name="subnet-size-requirement"></a><a name="subnet-size"></a> Wymaganie dotyczące rozmiaru podsieci
Platforma Azure rezerwuje niektóre adresy IP w każdej podsieci i nie można używać tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane do zachowania zgodności z protokołem, a kolejne trzy adresy są używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Czy istnieją ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresów IP używanych przez infrastrukturę sieci wirtualnej platformy Azure każde wystąpienie usługi Api Management w podsieci używa dwóch adresów IP na jednostkę jednostki SKU Premium lub jeden adres IP dla jednostki SKU dewelopera. Każde wystąpienie rezerwuje dodatkowy adres IP dla zewnętrznego usługi równoważenia obciążenia. Podczas wdrażania w wewnętrznej sieci wirtualnej wymagany jest dodatkowy adres IP dla wewnętrznego równoważenia obciążenia.

Biorąc pod uwagę obliczenia powyżej minimalnego rozmiaru podsieci, w której można wdrożyć API Management to /29, który zapewnia trzy możliwe do użycia adresy IP.

Każda dodatkowa jednostka skalowania API Management wymaga dwóch dodatkowych adresów IP.

## <a name="routing"></a><a name="routing"></a> Routing
+ Publiczny adres IP (VIP) ze zrównoważonym obciążeniem zostanie zarezerwowany w celu zapewnienia dostępu do wszystkich punktów końcowych usługi.
+ Adres IP z zakresu adresów IP podsieci (DIP) będzie używany do uzyskiwania dostępu do zasobów w sieci wirtualnej, a publiczny adres IP (VIP) będzie używany do uzyskiwania dostępu do zasobów poza siecią wirtualną.
+ Publiczny adres IP ze zrównoważonym obciążeniem można znaleźć w bloku Przegląd/Podstawowe elementy w Azure Portal.

## <a name="limitations"></a><a name="limitations"> </a>Ograniczenia
* W przypadku klientów korzystających z interfejsu API w wersji 2020-12-01 i starszych podsieć zawierająca API Management nie może zawierać żadnych innych typów zasobów platformy Azure.
* Podsieć i API Management muszą znajdować się w tej samej subskrypcji.
* Podsieci zawierającej API Management nie można przenosić między subskrypcjami.
* W przypadku wdrożeń sieci API Management wielu regionach skonfigurowanych w trybie wewnętrznej sieci wirtualnej użytkownicy są odpowiedzialni za zarządzanie równoważeniem obciążenia w wielu regionach, ponieważ są właścicielami routingu.
* Łączność z zasobu w globalnie równorzędnej sieci wirtualnej w innym regionie do usługi API Management w trybie wewnętrznym nie będzie działać z powodu ograniczenia platformy. Aby uzyskać więcej informacji, zobacz Zasoby w jednej sieci wirtualnej nie mogą komunikować się z wewnętrznym równoważeniem obciążenia platformy [Azure w równorzędnej sieci wirtualnej](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"></a> Adresy IP płaszczyzny sterowania

Adresy IP są podzielone przez środowisko **platformy Azure.** W przypadku zezwalania na adresy IP żądań przychodzących oznaczonych jako **Globalne** muszą być dozwolone wraz z **adresem** IP specyficznym dla regionu.

| **Środowisko platformy Azure**|   **Region**|  **Adres IP**|
|-----------------|-------------------------|---------------|
| Azure — publiczna| Południowo-środkowe usa (globalne)| 104.214.19.224|
| Azure — publiczna| Północno-środkowe usa (globalne)| 52.162.110.80|
| Azure — publiczna| Zachodnio-środkowe stany USA| 52.253.135.58|
| Azure — publiczna| Korea Środkowa| 40.82.157.167|
| Azure — publiczna| Zachodnie Zjednoczone Królestwo| 51.137.136.0|
| Azure — publiczna| Japonia Zachodnia| 40.81.185.8|
| Azure — publiczna| Północno-środkowe stany USA| 40.81.47.216|
| Azure — publiczna| Południowe Zjednoczone Królestwo| 51.145.56.125|
| Azure — publiczna| Indie Zachodnie| 40.81.89.24|
| Azure — publiczna| East US| 52.224.186.99|
| Azure — publiczna| West Europe| 51.145.179.78|
| Azure — publiczna| Japonia Wschodnia| 52.140.238.179|
| Azure — publiczna| Francja Środkowa| 40.66.60.111|
| Azure — publiczna| Kanada Wschodnia| 52.139.80.117|
| Azure — publiczna| Północne Zjednoczone Emiraty Zjednoczone| 20.46.144.85|
| Azure — publiczna| Brazylia Południowa| 191.233.24.179|
| Azure — publiczna| Brazylia Południowo-Wschodnia| 191.232.18.181|
| Azure — publiczna| Southeast Asia| 40.90.185.46|
| Azure — publiczna| Północna Republika Południowej Afryki| 102.133.130.197|
| Azure — publiczna| Kanada Środkowa| 52.139.20.34|
| Azure — publiczna| Korea Południowa| 40.80.232.185|
| Azure — publiczna| Indie Środkowe| 13.71.49.1|
| Azure — publiczna| Zachodnie stany USA| 13.64.39.16|
| Azure — publiczna| Australia Południowo-Wschodnia| 20.40.160.107|
| Azure — publiczna| Australia Środkowa| 20.37.52.67|
| Azure — publiczna| Indie Południowe| 20.44.33.246|
| Azure — publiczna| Central US| 13.86.102.66|
| Azure — publiczna| Australia Wschodnia| 20.40.125.155|
| Azure — publiczna| Zachodnie stany USA 2| 51.143.127.203|
| Azure — publiczna| Wschodnie stany USA 2 — EUAP| 52.253.229.253|
| Azure — publiczna| Środkowe stany USA — EUAP| 52.253.159.160|
| Azure — publiczna| South Central US| 20.188.77.119|
| Azure — publiczna| Wschodnie stany USA 2| 20.44.72.3|
| Azure — publiczna| Europa Północna| 52.142.95.35|
| Azure — publiczna| Azja Wschodnia| 52.139.152.27|
| Azure — publiczna| Francja Południowa| 20.39.80.2|
| Azure — publiczna| Szwajcaria Zachodnia| 51.107.96.8|
| Azure — publiczna| Australia Środkowa 2| 20.39.99.81|
| Azure — publiczna| Zjednoczone Emiraty Zjednoczone Emiraty Środkowe| 20.37.81.41|
| Azure — publiczna| Korea Północna| 51.107.0.91|
| Azure — publiczna| Zachodnia Republika Południowej Afryki| 102.133.0.79|
| Azure — publiczna| Niemcy Zachodnio-środkowe| 51.116.96.0|
| Azure — publiczna| Niemcy Północne| 51.116.0.0|
| Azure — publiczna| Holandia Wschodnia| 51.120.2.185|
| Azure — publiczna| Zachodnia Afryka| 51.120.130.134|
| Azure w Chinach — 21Vianet| Chiny Północne (globalne)| 139.217.51.16|
| Azure w Chinach — 21Vianet| Chiny Wschodnie (globalny)| 139.217.171.176|
| Azure w Chinach — 21Vianet| Chiny Północne| 40.125.137.220|
| Azure w Chinach — 21Vianet| Chiny Wschodnie| 40.126.120.30|
| Azure w Chinach — 21Vianet| Chiny Północne 2| 40.73.41.178|
| Azure w Chinach — 21Vianet| Chiny Wschodnie 2| 40.73.104.4|
| Azure Government| USGov Wirginia (globalny)| 52.127.42.160|
| Azure Government| USGov Teksas (globalny)| 52.127.34.192|
| Azure Government| USGov Wirginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Teksas| 52.243.154.118|
| Azure Government| USDoD (środkowe)| 52.182.32.132|
| Azure Government| USDoD East| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Powiązana zawartość
* [Łączenie serwera Virtual Network z zaplecza przy użyciu usługi Vpn Gateway](../vpn-gateway/design.md#s2smulti)
* [Łączenie Virtual Network z różnych modeli wdrażania](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Jak używać narzędzia API Inspector do śledzenia wywołań w usłudze Azure API Management](api-management-howto-api-inspector.md)
* [Virtual Network często zadawanych pytań](../virtual-network/virtual-networks-faq.md)
* [Tagi usługi](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
