---
title: Rozproszona geograficznie skala
description: Dowiedz się, jak skalować aplikacje w poziomie przy użyciu dystrybucji geograficznej Traffic Manager i App Service środowiskach.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions, devx-track-azurepowershell
ms.openlocfilehash: 215132888749a54996b3341e43ef8d91c101a460
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834304"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Rozproszona geograficznie skala przy użyciu środowisk usługi App Service
## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Scenariusze aplikacji, które wymagają bardzo dużej skali, mogą przekraczać pojemność zasobów obliczeniowych dostępną dla pojedynczego wdrożenia aplikacji.  Aplikacje do głosowania, wydarzenia sportowe i televiserowe wydarzenia sportowe to przykłady scenariuszy, które wymagają bardzo dużej skali. Wymagania dotyczące dużej skali można spełnić, skalując aplikacje w poziomie. Aby spełnić wymagania dotyczące skrajnego obciążenia, można utworzyć wiele wdrożeń aplikacji w jednym regionie i w różnych regionach.

App Service środowiska to idealna platforma do skalowania w poziomie. Po wybraniu konfiguracji App Service Environment, która może obsługiwać znaną stawkę żądań, deweloperzy mogą wdrażać dodatkowe środowiska App Service w sposób "plik cookie" w celu uzyskania wymaganej szczytowej wydajności ładowania.

Załóżmy na przykład, że aplikacja uruchomiona w konfiguracji App Service Environment została przetestowana pod kątem obsługi 20 000 żądań na sekundę (RPS).  Jeśli żądana pojemność szczytowego obciążenia wynosi 100 000 PUNKTÓW NA, można utworzyć pięć (5) środowisk App Service i skonfigurować je w celu zapewnienia, że aplikacja może obsłużyć maksymalne oczekiwane obciążenie.

Ponieważ klienci zwykle mają dostęp do aplikacji przy użyciu domeny niestandardowej (lub niestandardowej), deweloperzy potrzebują sposobu dystrybucji żądań aplikacji we wszystkich App Service Environment wystąpieniach.  Doskonałym sposobem osiągnięcia tego celu jest rozwiązanie domeny niestandardowej przy użyciu [Azure Traffic Manager profilu.][AzureTrafficManagerProfile]  Profil Traffic Manager można skonfigurować tak, aby wskazać wszystkie poszczególne środowiska App Service danych.  Traffic Manager będzie automatycznie obsługiwać dystrybucję klientów we wszystkich środowiskach App Service, na podstawie ustawień równoważenia obciążenia w Traffic Manager sieci.  To podejście działa niezależnie od tego, czy wszystkie środowiska App Service są wdrażane w jednym regionie świadczenia usługi Azure, czy wdrażane na całym świecie w wielu regionach świadczenia usługi Azure.

Ponadto, ponieważ klienci mają dostęp do aplikacji za pośrednictwem domeny o dużej liczbie klientów, nie wiedzą, jak wiele App Service, w których działa aplikacja.  Dzięki temu deweloperzy mogą szybko i łatwo dodawać i usuwać środowiska App Service na podstawie zaobserwowanego obciążenia ruchem.

Na poniższym diagramie koncepcyjnym przedstawiono aplikację skalowaną w poziomie na trzy App Service w jednym regionie.

![Architektura koncepcyjna][ConceptualArchitecture] 

W pozostałej części tego tematu o krokach związanych z konfigurowaniem rozproszonej topologii dla przykładowej aplikacji przy użyciu wielu środowisk App Service aplikacji.

## <a name="planning-the-topology"></a>Planowanie topologii
Przed rozpoczęciem tworzenia rozproszonego zasięgu aplikacji warto mieć z wyprzedzeniem kilka informacji.

* **Domena niestandardowa dla aplikacji:**  Jaka jest niestandardowa nazwa domeny, której klienci będą używać do uzyskiwania dostępu do aplikacji?  W przypadku przykładowej aplikacji niestandardowa nazwa domeny to `www.scalableasedemo.com` .
* **Traffic Manager domeny:** Wybierz nazwę domeny podczas tworzenia profilu [Azure Traffic Manager domeny.][AzureTrafficManagerProfile]  Ta nazwa zostanie połączona z sufiksem *trafficmanager.net,* aby zarejestrować wpis domeny, który jest zarządzany przez Traffic Manager.  W przypadku przykładowej aplikacji wybrana nazwa to *scalable-ase-demo*.  W rezultacie pełna nazwa domeny zarządzana przez program Traffic Manager *jest* scalable-ase-demo.trafficmanager.net .
* **Strategia skalowania zasięgu aplikacji:**  Czy ślad aplikacji będzie dystrybuowany w wielu środowiskach App Service w jednym regionie?  Wiele regionów?  Połączenie obu podejść?  Decyzja należy podjąć na podstawie oczekiwań co do miejsca, z którego będzie pochodzić ruch klientów, oraz tego, jak dobrze można skalować pozostałą część infrastruktury zapasowej aplikacji.  Na przykład dzięki 100% bez stanowej aplikacji aplikację można skalować na ogromną skalę przy użyciu kombinacji wielu środowisk App Service w każdym regionie platformy Azure pomnożonych przez środowiska App Service wdrożone w wielu regionach świadczenia usługi Azure.  Dzięki ponad 15 globalnym regionom świadczenia usługi Azure do wyboru klienci mogą naprawdę tworzyć aplikacje o dużej skali na całym świecie.  W przypadku przykładowej aplikacji używanej w tym artykule trzy środowiska App Service zostały utworzone w jednym regionie świadczenia usługi Azure (Południowo-środkowe usa).
* **Konwencja nazewnictwa dla App Service środowiskach:**  Każdy App Service Environment wymaga unikatowej nazwy.  Poza jednym lub dwoma App Service środowiskami warto mieć konwencję nazewnictwa, która pomaga identyfikować poszczególne App Service Environment.  W przypadku przykładowej aplikacji została użyta prosta konwencja nazewnictwa.  Nazwy tych trzech środowisk App Service to *fe1ase,* *fe2ase* i *fe3ase*.
* **Konwencja nazewnictwa dla aplikacji:**  Ponieważ zostanie wdrożonych wiele wystąpień aplikacji, nazwa jest potrzebna dla każdego wystąpienia wdrożonej aplikacji.  Jedną z mało znanych, ale wygodnych funkcji środowiska App Service jest to, że tej samej nazwy aplikacji można używać w wielu App Service środowisk.  Ponieważ każda App Service Environment ma unikatowy sufiks domeny, deweloperzy mogą zdecydować się na ponowne użycie dokładnie tej samej nazwy aplikacji w każdym środowisku.  Na przykład deweloper może mieć aplikacje o nazwach takich jak:  *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net* itp.  Jednak w przypadku przykładowej aplikacji każde wystąpienie aplikacji ma również unikatową nazwę.  Używane nazwy wystąpień aplikacji to *webfrontend1,* *webfrontend2* i *webfrontend3.*

## <a name="setting-up-the-traffic-manager-profile"></a>Konfigurowanie profilu Traffic Manager danych
Po wdrożeniu wielu wystąpień aplikacji w wielu środowiskach App Service, poszczególne wystąpienia aplikacji można zarejestrować w Traffic Manager.  W przypadku przykładowej aplikacji Traffic Manager profil  aplikacji, który scalable-ase-demo.trafficmanager.net przekierowywować klientów do dowolnego z następujących wdrożonych wystąpień aplikacji:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  Wystąpienie przykładowej aplikacji wdrożonej w pierwszym App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  Wystąpienie przykładowej aplikacji wdrożone w drugim App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  Wystąpienie przykładowej aplikacji wdrożonej w trzecim App Service Environment.

Najprostszym sposobem zarejestrowania wielu punktów Azure App Service, wszystkich uruchomionych w tym samym regionie świadczenia usługi **Azure,** jest obsługa programu PowerShell [Azure Resource Manager Traffic Manager .][ARMTrafficManager]  

Pierwszym krokiem jest utworzenie profilu Azure Traffic Manager aplikacji.  Poniższy kod pokazuje, jak profil został utworzony dla przykładowej aplikacji:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Zwróć uwagę, że *parametr RelativeDnsName* został ustawiony na *skalowalny-ase-demo.*  Ten parametr powoduje, że *nazwa scalable-ase-demo.trafficmanager.net* zostać utworzona i skojarzona z Traffic Manager profilu.

Parametr *TrafficRoutingMethod* definiuje zasady równoważenia obciążenia Traffic Manager które będą używać do określenia sposobu rozłożenia obciążenia klientów na wszystkie dostępne punkty końcowe.  W tym przykładzie *wybrano metodę Ważone.*  Z powodu tego wyboru żądania klientów będą rozłożone na wszystkie zarejestrowane punkty końcowe aplikacji na podstawie względnych wag skojarzonych z każdym punktem końcowym. 

Po utworzeniu profilu każde wystąpienie aplikacji jest dodawane do profilu jako natywny punkt końcowy platformy Azure.  Poniższy kod pobiera odwołanie do każdej aplikacji internetowej frontou. Następnie dodaje każdą aplikację jako punkt końcowy Traffic Manager za pomocą *parametru TargetResourceId.*

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

Zwróć uwagę, że istnieje jedno wywołanie do *pliku Add-AzureTrafficManagerEndpointConfig* dla każdego wystąpienia aplikacji.  Parametr *TargetResourceId* w każdym poleceniu programu PowerShell odwołuje się do jednego z trzech wdrożonych wystąpień aplikacji.  Obciążenie Traffic Manager zostanie rozłożone na wszystkie trzy punkty końcowe zarejestrowane w profilu.

Wszystkie trzy punkty końcowe używają tej samej wartości (10) dla *parametru Waga.*  Ta sytuacja powoduje, że Traffic Manager klientów we wszystkich trzech wystąpieniach aplikacji stosunkowo równomiernie. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Wskaż nazwę aplikacji Custom Domain domenie Traffic Manager domeny
Ostatnim krokiem jest wskazanie domeny niestandardowej aplikacji w domenie Traffic Manager domeny.  W przypadku przykładowej aplikacji wskaż `www.scalableasedemo.com` . `scalable-ase-demo.trafficmanager.net`  Wykonaj ten krok u rejestratora domen, który zarządza domeną niestandardową.  

Przy użyciu narzędzi do zarządzania domenami rejestratora należy utworzyć rekordy CNAME, które wskazuje domenę niestandardową Traffic Manager domeny.  Na poniższej ilustracji przedstawiono przykład konfiguracji CNAME:

![Rekord CNAME dla Custom Domain][CNAMEforCustomDomain] 

Chociaż nie zostały one uwzględnione w tym temacie, należy pamiętać, że każde pojedyncze wystąpienie aplikacji musi również mieć zarejestrowaną w nim domenę niestandardową.  W przeciwnym razie, jeśli żądanie zostanie wykonane do wystąpienia aplikacji, a aplikacja nie zarejestrowała domeny niestandardowej w aplikacji, żądanie nie powiedzie się.

W tym przykładzie domena niestandardowa to , a z każdym wystąpieniem aplikacji jest skojarzona `www.scalableasedemo.com` domena niestandardowa.

![Domena niestandardowa][CustomDomain] 

Aby uzyskać podsumowanie rejestrowania domeny niestandardowej w aplikacjach Azure App Service, zobacz [rejestrowanie domen niestandardowych.][RegisterCustomDomain]

## <a name="trying-out-the-distributed-topology"></a>Wypróbowanie topologii rozproszonej
Wynik końcowy konfiguracji usługi Traffic Manager DNS jest taki, że żądania będą `www.scalableasedemo.com` przepływać przez następującą sekwencję:

1. Przeglądarka lub urządzenie będzie szukać w systemie DNS `www.scalableasedemo.com`
2. Wpis CNAME u rejestratora domen powoduje przekierowywanie wyszukiwania DNS do Azure Traffic Manager.
3. Wyszukiwania DNS jest dla *scalable-ase-demo.trafficmanager.net* jeden z serwerów DNS Azure Traffic Manager DNS.
4. Na podstawie zasad równoważenia obciążenia określonych wcześniej w parametrze *TrafficRoutingMethod* Traffic Manager wybiera jeden ze skonfigurowanych punktów końcowych. Następnie zwraca ono w przeglądarce lub na urządzeniu wartość FQDN tego punktu końcowego.
5. Ponieważ nazwa FQDN punktu końcowego jest adresem URL wystąpienia aplikacji uruchamianego na serwerze App Service Environment, przeglądarka lub urządzenie poprosi serwer DNS usługi Microsoft Azure o rozpoznawanie nazwy FQDN jako adresu IP. 
6. Przeglądarka lub urządzenie wyśle żądanie HTTP/S na adres IP.  
7. Żądanie zostanie przysłane do jednego z wystąpień aplikacji uruchomionych w jednym z App Service środowiskach.

Na poniższej ilustracji konsoli przedstawiono odnośnik DNS dla domeny niestandardowej przykładowej aplikacji. Pomyślnie zostanie on rozpoznany jako wystąpienie aplikacji, które działa w jednym z trzech przykładowych środowisk App Service (w tym przypadku drugim z trzech App Service środowisk):

![DNS Lookup][DNSLookup] 

## <a name="additional-links-and-information"></a>Dodatkowe linki i informacje
Dokumentacja dotycząca obsługi poleceń [Azure Resource Manager Traffic Manager PowerShell.][ARMTrafficManager]  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
