---
title: Używanie usługi Azure API Management z sieciami wirtualnymi
description: Dowiedz się, jak skonfigurować połączenie z siecią wirtualną w usłudze Azure API Management i uzyskać za jej pośrednictwem dostęp do usług sieci web.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2020
ms.author: apimpm
ms.openlocfilehash: 0ecb7ee7f5c7c0ebaa87eb6b32eee1926d9e294d
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768959"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Używanie usługi Azure API Management z sieciami wirtualnymi
Sieci wirtualne platformy Azure umożliwiają umieszczanie dowolnych zasobów platformy Azure w sieci nieobsługującej routingu internetowego, do której kontrolujesz dostęp. Sieci te można następnie połączyć z sieciami lokalnymi przy użyciu różnych technologii sieci VPN. Aby dowiedzieć się więcej o sieciach wirtualnych platformy Azure, zacznij od informacji tutaj: [Omówienie sieci wirtualnej platformy Azure](../virtual-network/virtual-networks-overview.md).

Usługę Azure API Management można wdrożyć wewnątrz sieci wirtualnej (VNET), dzięki czemu można uzyskać dostęp do usług zaplecza w sieci. Portal dewelopera i bramę interfejsu API można skonfigurować tak, aby była dostępna z Internetu lub tylko w sieci wirtualnej.

> [!NOTE]
> Adres URL dokumentu importu interfejsu API musi znajdować się na publicznie dostępnym adresie internetowym.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz mieć:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie interfejsu APIM. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>Włącz połączenie sieci wirtualnej

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Włączanie łączności sieci wirtualnej przy użyciu witryny Azure portal

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby znaleźć wystąpienie zarządzania interfejsem API. Wyszukaj i wybierz **usługi zarządzania interfejsami API**.

2. Wybierz wystąpienie zarządzania interfejsami API.

3. Wybierz **sieć wirtualną**.
4. Skonfiguruj wystąpienie zarządzania interfejsami API do wdrożenia w sieci wirtualnej.

    ![Menu sieci wirtualnej zarządzania interfejsami API][api-management-using-vnet-menu]
5. Wybierz żądany typ dostępu:

    * **Wył.:** Jest to wartość domyślna. Usługa API Management nie jest wdrażana w sieci wirtualnej.

    * **Zewnętrzna**: Brama api Management i portal deweloperów są dostępne z publicznego Internetu za pośrednictwem zewnętrznego modułu równoważenia obciążenia. Brama może uzyskiwać dostęp do zasobów w sieci wirtualnej.

        ![Publiczna komunikacja równorzędna][api-management-vnet-public]

    * **Wewnętrzny:** Brama zarządzania interfejsami API i portal dewelopera są dostępne tylko z poziomu sieci wirtualnej za pośrednictwem wewnętrznego modułu równoważenia obciążenia. Brama może uzyskiwać dostęp do zasobów w sieci wirtualnej.

        ![Prywatna komunikacja równorzędna][api-management-vnet-private]

6. Jeśli wybrano **opcję Zewnętrzne** lub **Wewnętrzne,** zostanie wyświetlona lista wszystkich regionów, w których usługa api management jest aprowizowana. Wybierz **pozycję Lokalizacja**, a następnie wybierz jej **sieć wirtualną** i **podsieć**. Lista sieci wirtualnych jest wypełniona sieciami wirtualnymi klasycznymi i resource managerem dostępnymi w subskrypcjach platformy Azure skonfigurowanych w konfigurowanym regionie.

    > [!IMPORTANT]
    > Podczas wdrażania wystąpienia usługi Azure API Management w sieci wirtualnej Menedżera zasobów usługa musi znajdować się w dedykowanej podsieci, która nie zawiera żadnych innych zasobów z wyjątkiem wystąpień usługi Azure API Management. Jeśli zostanie podjęta próba wdrożenia wystąpienia usługi Azure API Management w podsieci sieci wirtualnej Menedżera zasobów, która zawiera inne zasoby, wdrożenie zakończy się niepowodzeniem.

    Następnie wybierz pozycję **Zastosuj**. Strona **sieci wirtualnej** wystąpienia usługi API Management jest aktualizowana o nowe opcje sieci wirtualnej i podsieci.

    ![Wybierz SIEĆ VPN][api-management-setup-vpn-select]

7. Na górnym pasku nawigacyjnym wybierz pozycję **Zapisz**, a następnie wybierz pozycję **Zastosuj konfigurację sieci**.

> [!NOTE]
> Adres VIP wystąpienia usługi API Management zmieni się za każdym razem, gdy usługa VNET jest włączona lub wyłączona.
> Adres VIP zmieni się również po przeniesieniu z **zewnętrznego** na **wewnętrzny**lub odwrotnie.
>

> [!IMPORTANT]
> Jeśli usuniesz zarządzanie interfejsem API z sieci wirtualnej lub zmienisz ten, w której jest wdrożony, poprzednio używana sieci wirtualnej może pozostać zablokowana przez maksymalnie sześć godzin. W tym okresie nie będzie możliwe usunięcie sieci wirtualnej lub wdrożenie nowego zasobu do niego. To zachowanie dotyczy klientów korzystających z wersji interfejsu API 2018-01-01 i wcześniejszych. Klienci korzystający z wersji interfejsu API 2019-01-01 i nowszych sieci wirtualnej są zwalniani natychmiast po usunięciu skojarzonej usługi zarządzania interfejsami API.

## <a name="enable-vnet-connection-using-powershell-cmdlets"></a><a name="enable-vnet-powershell"> </a>Włączanie połączenia sieci wirtualnej przy użyciu poleceń cmdlet programu PowerShell
Można również włączyć łączność sieci wirtualnej przy użyciu poleceń cmdlet programu PowerShell.

* **Tworzenie usługi zarządzania interfejsami API wewnątrz sieci wirtualnej:** Użyj polecenia cmdlet [New-AzApiManagement,](/powershell/module/az.apimanagement/new-azapimanagement) aby utworzyć usługę Azure API Management wewnątrz sieci wirtualnej.

* **Wdrażanie istniejącej usługi zarządzania interfejsami API wewnątrz sieci wirtualnej:** Użyj polecenia cmdlet [Update-AzApiManagementRegion,](/powershell/module/az.apimanagement/update-azapimanagementregion) aby przenieść istniejącą usługę Azure API Management wewnątrz sieci wirtualnej.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Łączenie się z usługą sieci web hostowanego w sieci wirtualnej
Po połączeniu usługi api Management z siecią wirtualną dostęp do usług zaplecza w niej nie różni się od uzyskiwania dostępu do usług publicznych. Wystarczy wpisać lokalny adres IP lub nazwę hosta (jeśli serwer DNS jest skonfigurowany dla sieci wirtualnej) usługi sieci web w polu **URL usługi sieci Web** podczas tworzenia nowego interfejsu API lub edytowania istniejącego.

![Dodawanie interfejsu API z sieci VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Typowe problemy z konfiguracją sieci
Poniżej znajduje się lista typowych problemów z nieprawidłową konfiguracją, które mogą wystąpić podczas wdrażania usługi api management w sieci wirtualnej.

* **Niestandardowa konfiguracja serwera DNS:** Usługa zarządzania interfejsami API zależy od kilku usług platformy Azure. Gdy usługa Zarządzanie interfejsami API jest hostowana w sieci wirtualnej z niestandardowym serwerem DNS, musi rozpoznać nazwy hostów tych usług platformy Azure. Postępuj zgodnie z [tymi](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wskazówkami dotyczącymi niestandardowej konfiguracji DNS. Zobacz tabelę portów poniżej i inne wymagania sieciowe w celach informacyjnych.

> [!IMPORTANT]
> Jeśli planujesz używać niestandardowych serwerów DNS dla sieci wirtualnej, należy ją skonfigurować **przed** wdrożeniem w niej usługi zarządzania interfejsami API. W przeciwnym razie należy zaktualizować usługę Zarządzanie interfejsami API za każdym razem, gdy zmienisz serwery DNS, uruchamiając [operację Zastosuj konfigurację sieci](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Porty wymagane do zarządzania interfejsami API:** Ruch przychodzący i wychodzący do podsieci, w której wdrożono zarządzanie interfejsem API, można sterować za pomocą [sieciowej grupy zabezpieczeń][Network Security Group]. Jeśli którykolwiek z tych portów jest niedostępny, usługa API Management może nie działać poprawnie i może stać się niedostępna. Po co najmniej jeden z tych portów zablokowany jest inny typowy problem z nieprawidłową konfiguracją podczas korzystania z zarządzania interfejsami API z siecią wirtualną.

<a name="required-ports"> </a> Gdy wystąpienie usługi zarządzania interfejsem API jest hostowane w sieci wirtualnej, używane są porty w poniższej tabeli.

| Źródło / Porty docelowe | Kierunek          | Protokół transportu |   [Tagi usług](../virtual-network/security-overview.md#service-tags) <br> Źródło / Cel podróży   | Cel\*( )                                                 | Typ sieci wirtualnej |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Przychodzący            | TCP                | INTERNET / VIRTUAL_NETWORK            | Komunikacja klienta z zarządzaniem interfejsami API                      | Zewnętrzna             |
| * / 3443                     | Przychodzący            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Punkt końcowy zarządzania dla witryny Azure portal i programu Powershell         | Zewnętrzne & wewnętrzne  |
| * / 443                  | Wychodzący           | TCP                | VIRTUAL_NETWORK / Przechowywanie             | **Zależność od usługi Azure Storage**                             | Zewnętrzne & wewnętrzne  |
| * / 443                  | Wychodzący           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Usługa Azure Active Directory](api-management-howto-aad.md) (w stosownych przypadkach)                   | Zewnętrzne & wewnętrzne  |
| * / 1433                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / SQL                 | **Dostęp do punktów końcowych SQL platformy Azure**                           | Zewnętrzne & wewnętrzne  |
| * / 5671, 5672, 443          | Wychodzący           | TCP                | VIRTUAL_NETWORK / EventHub            | Zależność dla [zasad i agenta](api-management-howto-log-event-hubs.md) monitorowania usługi Log to Event Hub | Zewnętrzne & wewnętrzne  |
| * / 445                      | Wychodzący           | TCP                | VIRTUAL_NETWORK / Przechowywanie             | Zależność od udziału plików platformy Azure dla [GIT](api-management-configuration-repository-git.md)                      | Zewnętrzne & wewnętrzne  |
| * / 1886                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Potrzebne do opublikowania stanu kondycji w kondycji zasobu          | Zewnętrzne & wewnętrzne  |
| * / 443                     | Wychodzący           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publikowanie [dzienników diagnostyki i metryk](api-management-howto-use-azure-monitor.md)                       | Zewnętrzne & wewnętrzne  |
| * / 25                       | Wychodzący           | TCP                | VIRTUAL_NETWORK / INTERNET            | Połącz się z przekaźnikiem SMTP w celu wysyłania wiadomości e-mail                    | Zewnętrzne & wewnętrzne  |
| * / 587                      | Wychodzący           | TCP                | VIRTUAL_NETWORK / INTERNET            | Połącz się z przekaźnikiem SMTP w celu wysyłania wiadomości e-mail                    | Zewnętrzne & wewnętrzne  |
| * / 25028                    | Wychodzący           | TCP                | VIRTUAL_NETWORK / INTERNET            | Połącz się z przekaźnikiem SMTP w celu wysyłania wiadomości e-mail                    | Zewnętrzne & wewnętrzne  |
| * / 6381 - 6383              | Przychodzące & wychodzące | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Dostęp do usługi Redis dla zasad [limitu szybkości](api-management-access-restriction-policies.md#LimitCallRateByKey) między komputerami         | Zewnętrzne & wewnętrzne  |
| * / *                        | Przychodzący            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Moduł równoważenia obciążenia infrastruktury platformy Azure                          | Zewnętrzne & wewnętrzne  |

>[!IMPORTANT]
> Porty, dla których *cel* jest **pogrubiony** są wymagane dla usługi api Management, które mają zostać pomyślnie wdrożone. Blokowanie innych portów spowoduje jednak degradację możliwości korzystania z uruchomionej usługi i monitorowania jej.

+ **Funkcja TLS**: Aby włączyć tworzenie i walidację łańcucha certyfikatów TLS/SSL, usługa api Management wymaga połączenia z siecią wychodzącą w celu ocsp.msocsp.com, mscrl.microsoft.com i crl.microsoft.com. Ta zależność nie jest wymagana, jeśli każdy certyfikat przekazany do usługi API Management zawiera pełny łańcuch do katalogu głównego urzędu certyfikacji.

+ **Dostęp DNS:** Dostęp wychodzący na porcie 53 jest wymagany do komunikacji z serwerami DNS. Jeśli niestandardowy serwer DNS istnieje na drugim końcu bramy sieci VPN, serwer DNS musi być osiągalny z podsieci hostingu zarządzania interfejsami API.

+ **Metryki i monitorowanie kondycji: Wychodząca**łączność sieciowa z punktami końcowymi monitorowania platformy Azure, które są rozpoznawane w następujących domenach. Jak pokazano w tabeli, te adresy URL są reprezentowane w tagu usługi AzureMonitor do użytku z grupami zabezpieczeń sieci.

    | Środowisko platformy Azure | Punkty końcowe                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure — publiczna      | <ul><li>gcs.prod.monitoring.core.windows.net(**nowy**)</li><li>prod.warmpath.msftcloudes.com( do**przestarzałego**)</li><li>shoebox2.metrics.microsoftmetrics.com(**nowy**)</li><li>shoebox2.metrics.nsatc.net(**do przestarzałego**)</li><li>prod3.metrics.microsoftmetrics.com(**nowy**)</li><li>prod3.metrics.nsatc.net( do**przestarzałego**)</li><li>prod3-black.prod3.metrics.microsoftmetrics.com(**nowy**)</li><li>prod3-black.prod3.metrics.nsatc.net( do**przestarzałego**)</li><li>prod3-red.prod3.metrics.microsoftmetrics.com(**nowy**)</li><li>prod3-red.prod3.metrics.nsatc.net( do**przestarzałego**)</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com gdzie `East US 2` jest eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.microsoftmetrics.com(**nowy**)</li><li>shoebox2.metrics.nsatc.net(**do przestarzałego**)</li><li>prod3.metrics.microsoftmetrics.com(**nowy**)</li><li>prod3.metrics.nsatc.net( do**przestarzałego**)</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |
    | Azure w Chinach — 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.microsoftmetrics.com(**nowy**)</li><li>shoebox2.metrics.nsatc.net(**do przestarzałego**)</li><li>prod3.metrics.microsoftmetrics.com(**nowy**)</li><li>prod3.metrics.nsatc.net( do**przestarzałego**)</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > Zmiana klastrów powyżej z dns zone **.nsatc.net** do **.microsoftmetrics.com** jest głównie DNS Change. Adres IP klastra nie ulegnie zmianie.

+ **Regionalne tagi usług: Reguły sieciowej**sieciowej zezwalające na łączność wychodzącą z tagami usługi Storage, SQL i Event Hubs mogą używać regionalnych wersji tych tagów odpowiadających regionowi zawierającemu wystąpienie zarządzania interfejsami API (na przykład Storage.WestUS dla wystąpienia usługi API Management w regionie Zachodnie stany USA). W wdrożeniach wieloregionasgowych sieciowej w każdym regionie należy zezwolić na ruch do tagów usługi dla tego regionu i regionu podstawowego.

+ **Przekaźnik SMTP:** Wychodząca łączność sieciowa dla przekaźnika `smtpi-db3.msn.com` `smtpi-sin.msn.com` SMTP, który jest rozpoznawany w obszarze hosta `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, i`ies.global.microsoft.com`

+ **Portal dla deweloperów CAPTCHA:** Wychodząca łączność sieciowa dla captcha `client.hip.live.com` portalu `partner.hip.live.com`dewelopera, który jest rozpoznawany w obszarze hostów i .

+ **Diagnostyka portalu Azure:** Aby włączyć przepływ dzienników diagnostycznych z witryny Azure portal podczas korzystania `dc.services.visualstudio.com` z rozszerzenia usługi API Management z wewnątrz sieci wirtualnej, wymagany jest dostęp wychodzący do portu 443. Pomaga to w rozwiązywaniu problemów, które mogą napotkać podczas korzystania z rozszerzenia.

+ **Wymuś ruch tunelowania do zapory lokalnej przy użyciu trasy ekspresowej lub wirtualnego urządzenia sieciowego:** Wspólną konfiguracją klienta jest zdefiniowanie własnej trasy domyślnej (0.0.0.0/0), która wymusza przepływ całego ruchu z podsieci delegowanej usługi API Management przez zaporę lokalną lub do wirtualnego urządzenia sieciowego. Ten przepływ ruchu niezmiennie przerywa łączność z usługą Azure API Management, ponieważ ruch wychodzący jest zablokowany lokalnie lub nat'd do nierozpoznawalnego zestawu adresów, które nie działają już z różnymi punktami końcowymi platformy Azure. Rozwiązanie wymaga wykonania kilku czynności:

  * Włącz punkty końcowe usługi w podsieci, w której wdrożono usługę api management. [Punkty końcowe usługi][ServiceEndpoints] muszą być włączone dla platformy Azure Sql, Azure Storage, Azure EventHub i Azure ServiceBus. Włączenie punktów końcowych bezpośrednio z podsieci delegowanej usługi API Management do tych usług umożliwia im korzystanie z sieci szkieletowej platformy Microsoft Azure zapewniającej optymalne routing dla ruchu usługi. Jeśli używasz punktów końcowych usługi z wymuszonym tunelowane zarządzaniem interfejsami api, powyższy ruch usług platformy Azure nie jest wymuszany tunelowania. Inny ruch zależności usługi zarządzania interfejsem API jest wymuszony tunelowanie i nie można go utracić lub usługa api Management nie będzie działać poprawnie.
    
  * Cały ruch płaszczyzny sterowania z Internetu do punktu końcowego zarządzania usługi api Management są kierowane za pośrednictwem określonego zestawu przychodzących adresów IP hostowanych przez usługę API Management. Gdy ruch jest wymuszany tunelowanie odpowiedzi nie będzie symetrycznie mapować z powrotem do tych przychodzących źródłowych usług IP. Aby przezwyciężyć ograniczenie, musimy dodać następujące trasy zdefiniowane przez użytkownika[(UDR),][UDRs]aby kierować ruch z powrotem do platformy Azure, ustawiając miejsce docelowe tych tras hosta na "Internet". Zestaw przychodzących adresów IP do sterowania Ruch samolotowy jest udokumentowany [Adresy IP płaszczyzny sterowania](#control-plane-ips)

  * W przypadku innych zależności usługi zarządzania interfejsem API, które są wymuszane tunelowanie, powinien istnieć sposób, aby rozwiązać nazwa hosta i dotrzeć do punktu końcowego. Są to m.in.
      - Metryki i monitorowanie stanu zdrowia
      - Diagnostyka portalu Azure
      - Przekaźnik SMTP
      - Portal dla deweloperów CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Rozwiązywanie problemów
* **Konfiguracja początkowa:** Gdy początkowe wdrożenie usługi API Management w podsieci nie powiedzie się, zaleca się najpierw wdrożenie maszyny wirtualnej w tej samej podsieci. Następny pulpit zdalny do maszyny wirtualnej i sprawdź, czy istnieje łączność z jednym z każdego zasobu poniżej w subskrypcji platformy Azure
    * Obiekt blob usługi Azure Storage
    * Azure SQL Database
    * Tabela usługi Azure Storage

  > [!IMPORTANT]
  > Po sprawdzeniu poprawności łączności przed wdrożeniem usługi Api Management w podsieci należy usunąć wszystkie zasoby wdrożone w podsieci.

* **Aktualizacje przyrostowe:** Podczas wprowadzania zmian w sieci należy zapoznać się z [interfejsem API NetworkStatus](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/networkstatus), aby sprawdzić, czy usługa api Management nie utraciła dostępu do żadnych krytycznych zasobów, od których zależy. Stan łączności powinien być aktualizowany co 15 minut.

* **Łącza nawigacji zasobów:** Podczas wdrażania w podsieci sieci sieci wirtualnej w stylu Menedżera zasobów usługa API Management rezerwuje podsieć, tworząc łącze nawigacji zasobów. Jeśli podsieć zawiera już zasób innego dostawcy, wdrożenie zakończy **się niepowodzeniem**. Podobnie po przeniesieniu usługi api Management do innej podsieci lub usunięciu jej usuniemy to łącze nawigacji zasobów.

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> Wymagania dotyczące rozmiaru podsieci
Platforma Azure rezerwuje niektóre adresy IP w każdej podsieci i tych adresów nie można używać. Pierwszy i ostatni adres IP podsieci są zarezerwowane dla zgodności protokołu, wraz z trzema adresami używanymi dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oprócz adresów IP używanych przez infrastrukturę sieci wirtualnej platformy Azure każde wystąpienie usługi Api Management w podsieci używa dwóch adresów IP na jednostkę jednostki SKU premium lub jednego adresu IP dla jednostki SKU dewelopera. Każde wystąpienie rezerwuje dodatkowy adres IP dla zewnętrznego modułu równoważenia obciążenia. Podczas wdrażania w wewnętrznej sieci wirtualnej, wymaga dodatkowego adresu IP dla wewnętrznego modułu równoważenia obciążenia.

Biorąc pod uwagę obliczenia powyżej minimalnego rozmiaru podsieci, w którym można wdrożyć zarządzanie interfejsami API jest /29, który daje trzy użyteczne adresy IP.

Każda dodatkowa jednostka skalowania usługi API Management wymaga dwóch dodatkowych adresów IP.

## <a name="routing"></a><a name="routing"> </a> Routing
+ Zrównoważony obciążenie publiczny adres IP (VIP) zostanie zarezerwowany w celu zapewnienia dostępu do wszystkich punktów końcowych usługi.
+ Adres IP z zakresu adresów IP podsieci (DIP) będzie używany do uzyskiwania dostępu do zasobów w sieci wirtualnej, a publiczny adres IP (VIP) będzie używany do uzyskiwania dostępu do zasobów poza siecią wirtualną.
+ Równoważenie obciążenia publiczny adres IP można znaleźć w przeglądzie/Essentials bloku w witrynie Azure portal.

## <a name="limitations"></a><a name="limitations"> </a>Ograniczenia
* Podsieć zawierająca wystąpienia usługi API Management nie może zawierać żadnych innych typów zasobów platformy Azure.
* Podsieć i usługa api Management muszą być w tej samej subskrypcji.
* Podsieci zawierającej wystąpienia usługi API Management nie można przenosić między subskrypcjami.
* W przypadku wdrożeń zarządzania interfejsami API w wielu regionach skonfigurowanych w trybie wewnętrznej sieci wirtualnej użytkownicy są odpowiedzialni za zarządzanie równoważeniem obciążenia w wielu regionach, ponieważ są właścicielami routingu.
* Łączność z zasobu w globalnie równorzędnej sieci wirtualnej w innym regionie do usługi api management w trybie wewnętrznym nie będzie działać z powodu ograniczenia platformy. Aby uzyskać więcej informacji, zobacz [Zasoby w jednej sieci wirtualnej nie mogą komunikować się z wewnętrznym modułem równoważenia obciążenia platformy Azure w sieci wirtualnej równorzędnej](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> Adresy IP płaszczyzny sterowania

Adresy IP są podzielone przez **środowisko platformy Azure**. Zezwalając na przychodzące żądania adres IP oznaczony **globalnie** musi być umieszczony na białej liście wraz z **regionu** określonego adresu IP.

| **Środowisko platformy Azure**|   **Region**|  **Adres IP**|
|-----------------|-------------------------|---------------|
| Azure — publiczna| Południowo-środkowe stany USA (globalne)| 104.214.19.224|
| Azure — publiczna| Północno-środkowe stany USA (globalne)| 52.162.110.80|
| Azure — publiczna| Zachodnio-środkowe stany USA| 52.253.135.58|
| Azure — publiczna| Korea Środkowa| 40.82.157.167|
| Azure — publiczna| Zachodnie Zjednoczone Królestwo| 51.137.136.0|
| Azure — publiczna| Japonia Zachodnia| 40.81.185.8|
| Azure — publiczna| Północno-środkowe stany USA| 40.81.47.216|
| Azure — publiczna| Południowe Zjednoczone Królestwo| 51.145.56.125|
| Azure — publiczna| Indie Zachodnie| 40.81.89.24|
| Azure — publiczna| Wschodnie stany USA| 52.224.186.99|
| Azure — publiczna| Europa Zachodnia| 51.145.179.78|
| Azure — publiczna| Japonia Wschodnia| 52.140.238.179|
| Azure — publiczna| Francja Środkowa| 40.66.60.111|
| Azure — publiczna| Kanada Wschodnia| 52.139.80.117|
| Azure — publiczna| Zjednoczone Emiraty Północne| 20.46.144.85|
| Azure — publiczna| Brazylia Południowa| 191.233.24.179|
| Azure — publiczna| Azja Południowo-Wschodnia| 40.90.185.46|
| Azure — publiczna| Republika Południowej Afryki Północ| 102.133.130.197|
| Azure — publiczna| Kanada Środkowa| 52.139.20.34|
| Azure — publiczna| Korea Południowa| 40.80.232.185|
| Azure — publiczna| Indie Środkowe| 13.71.49.1|
| Azure — publiczna| Zachodnie stany USA| 13.64.39.16|
| Azure — publiczna| Australia Południowo-Wschodnia| 20.40.160.107|
| Azure — publiczna| Australia Środkowa| 20.37.52.67|
| Azure — publiczna| Indie Południowe| 20.44.33.246|
| Azure — publiczna| Środkowe stany USA| 13.86.102.66|
| Azure — publiczna| Australia Wschodnia| 20.40.125.155|
| Azure — publiczna| Zachodnie stany USA 2| 51.143.127.203|
| Azure — publiczna| Wschodnie Stany Zjednoczone 2 EUAP| 52.253.229.253|
| Azure — publiczna| Centralna amerykańska EUAP| 52.253.159.160|
| Azure — publiczna| Południowo-środkowe stany USA| 20.188.77.119|
| Azure — publiczna| Wschodnie stany USA 2| 20.44.72.3|
| Azure — publiczna| Europa Północna| 52.142.95.35|
| Azure — publiczna| Azja Wschodnia| 52.139.152.27|
| Azure — publiczna| Francja Południowa| 20.39.80.2|
| Azure — publiczna| Szwajcaria Zachód| 51.107.96.8|
| Azure — publiczna| Australia Środkowa 2| 20.39.99.81|
| Azure — publiczna| Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone Emirat| 20.37.81.41|
| Azure — publiczna| Szwajcaria Północna| 51.107.0.91|
| Azure — publiczna| Republika Południowej Afryki Zachód| 102.133.0.79|
| Azure — publiczna| Niemcy Zachodnio-Środkowe| 51.116.96.0|
| Azure — publiczna| Niemcy Północne| 51.116.0.0|
| Azure — publiczna| Norwegia Wschodnia| 51.120.2.185|
| Azure — publiczna| Norwegia Zachodnia| 51.120.130.134|
| Azure w Chinach — 21Vianet| Chiny Północne (globalne)| 139.217.51.16|
| Azure w Chinach — 21Vianet| Chiny Wschodnie (globalny)| 139.217.171.176|
| Azure w Chinach — 21Vianet| Chiny Północne| 40.125.137.220|
| Azure w Chinach — 21Vianet| Chiny Wschodnie| 40.126.120.30|
| Azure w Chinach — 21Vianet| Chiny Północne 2| 40.73.41.178|
| Azure w Chinach — 21Vianet| Chiny Wschodnie 2| 40.73.104.4|
| Azure Government| USGov Virginia (globalny)| 52.127.42.160|
| Azure Government| USGov Texas (Globalny)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| UsDod Centralny| 52.182.32.132|
| Azure Government| UsDoD Wschód| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Powiązane treści
* [Podłączanie sieci wirtualnej do wewnętrznej bazy danych przy użyciu bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Podłączanie sieci wirtualnej z różnych modeli wdrażania](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Jak używać Inspektora interfejsu API do śledzenia wywołań w usłudze Azure API Management](api-management-howto-api-inspector.md)
* [Sieć wirtualna Często zadawane pytania](../virtual-network/virtual-networks-faq.md)
* [Tagi usługi](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
