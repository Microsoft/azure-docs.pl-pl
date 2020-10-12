---
title: Skala geograficzna rozproszona
description: Dowiedz się, jak skalować aplikacje w poziomie przy użyciu dystrybucji geograficznej w środowiskach Traffic Manager i App Service.
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions
ms.openlocfilehash: 004b32118521f72c5b59ad7bab2d4e41244b85c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85833608"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Rozproszona geograficznie skala przy użyciu środowisk usługi App Service
## <a name="overview"></a>Omówienie

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Scenariusze aplikacji, które wymagają bardzo dużej skali, mogą przekroczyć pojemność zasobów obliczeniowych dostępną dla jednego wdrożenia aplikacji.  W przypadku aplikacji do głosowania, wydarzeń sportowych i televised rozrywki są wszystkie przykładowe scenariusze, które wymagają bardzo dużej skali. Wymagania dotyczące wysokiej skali mogą być spełnione w przypadku aplikacji skalowania w poziomie. Aby obsługiwać skrajne wymagania dotyczące obciążenia, wiele wdrożeń aplikacji można przeprowadzić w jednym regionie i w różnych regionach.

Środowiska App Service są idealnym platformą dla skalowania w poziomie. Po wybraniu konfiguracji App Service Environment, która może obsługiwać znany odsetek żądań, deweloperzy mogą wdrożyć dodatkowe środowiska App Service w sposób "odcięcia plików cookie", aby osiągnąć pożądaną szczytową pojemność obciążenia.

Załóżmy na przykład, że aplikacja uruchomiona na App Service Environment konfiguracji została przetestowana pod kątem obsługi żądań 20 000 na sekundę (RPS pliku).  Jeśli żądana szczytowa pojemność obciążenia to 100 000 RPS pliku, można utworzyć i skonfigurować pięć (5) środowisk App Service, aby zapewnić, że aplikacja może obsłużyć maksymalny rozmiar przewidywanego obciążenia.

Ponieważ klienci zazwyczaj uzyskują dostęp do aplikacji przy użyciu domeny niestandardowej (lub znaczącym), deweloperzy muszą mieć możliwość dystrybucji żądań aplikacji we wszystkich wystąpieniach App Service Environment.  Doskonałym sposobem osiągnięcia tego celu jest rozwiązanie domeny niestandardowej przy użyciu [profilu usługi Azure Traffic Manager][AzureTrafficManagerProfile].  Profil Traffic Manager można skonfigurować tak, aby wskazywał wszystkie poszczególne środowiska App Service.  Traffic Manager automatycznie obsłużyć dystrybucję klientów we wszystkich środowiskach App Service, na podstawie ustawień równoważenia obciążenia w profilu Traffic Manager.  To podejście działa niezależnie od tego, czy wszystkie środowiska App Service są wdrożone w jednym regionie świadczenia usługi Azure, czy wdrożone na całym świecie w wielu regionach świadczenia usługi Azure.

Ponadto, ponieważ klienci uzyskują dostęp do aplikacji za pomocą domeny znaczącym, klienci nie znają liczby środowisk App Service, na których działa aplikacja.  W efekcie deweloperzy mogą szybko i łatwo dodawać i usuwać środowiska App Service w oparciu o zaobserwowane obciążenie ruchem.

Na poniższym diagramie koncepcyjnym aplikacja jest skalowana w poziomie w trzech App Service środowiskach w obrębie jednego regionu.

![Architektura koncepcyjna][ConceptualArchitecture] 

Pozostała część tego tematu zawiera instrukcje dotyczące konfigurowania rozproszonej topologii dla przykładowej aplikacji przy użyciu wielu środowisk App Service.

## <a name="planning-the-topology"></a>Planowanie topologii
Przed rozpoczęciem tworzenia rozproszonej aplikacji można uzyskać kilka części informacji przed upływem czasu.

* **Domena niestandardowa dla aplikacji:**  Jaka jest niestandardowa nazwa domeny, która będzie używana przez klientów w celu uzyskania dostępu do aplikacji?  W przypadku przykładowej aplikacji niestandardowa nazwa domeny to `www.scalableasedemo.com` .
* **Traffic Manager domeny:** Wybierz nazwę domeny podczas tworzenia [profilu usługi Azure Traffic Manager][AzureTrafficManagerProfile].  Ta nazwa zostanie połączona z sufiksem *trafficmanager.NET* w celu zarejestrowania wpisu domeny, który jest zarządzany przez Traffic Manager.  W przypadku przykładowej aplikacji wybrana nazwa to *skalowalne — Demonstracja*.  W związku z tym pełna nazwa domeny, która jest zarządzana przez Traffic Manager, to *Scalable-ASE-demo.trafficmanager.NET*.
* **Strategia skalowania rozmiaru aplikacji:**  Czy aplikacja będzie dystrybuowana w wielu środowiskach App Service w jednym regionie?  Wiele regionów?  Kombinacji obu metod?  Podstawowa decyzja dotycząca oczekiwań, w których nastąpi ruch klienta i jak również pozostała część aplikacji obsługującej infrastrukturę zaplecza można skalować.  Na przykład w przypadku aplikacji bezstanowej 100% aplikacja może być w znacznym stopniu skalowana przy użyciu kombinacji wielu środowisk App Service w każdym regionie świadczenia usługi Azure, pomnożonych przez App Service środowiska wdrożone w wielu regionach świadczenia usługi Azure.  Dzięki 15 i globalnym regionom platformy Azure dostępnym do wyboru klienci mogą naprawdę kompilować skalę na całym świecie.  W przypadku przykładowej aplikacji, która jest używana w tym artykule, trzy środowiska App Service zostały utworzone w jednym regionie świadczenia usługi Azure (Południowo-środkowe stany USA).
* **Konwencja nazewnictwa dla środowisk App Service:**  Każda App Service Environment wymaga unikatowej nazwy.  Poza jednym lub dwoma środowiskami App Service warto mieć konwencję nazewnictwa ułatwiającą identyfikację poszczególnych App Service Environment.  W przypadku przykładowej aplikacji użyto prostej konwencji nazewnictwa.  Nazwy trzech środowisk App Service to *fe1ase*, *fe2ase*i *fe3ase*.
* **Konwencja nazewnictwa dla aplikacji:**  Ponieważ zostanie wdrożonych wiele wystąpień aplikacji, wymagana jest nazwa dla każdego wystąpienia wdrożonej aplikacji.  Jedna mała — znana, ale wygodna funkcja środowisk App Servicech polega na tym, że ta sama nazwa aplikacji może być używana w wielu środowiskach App Service.  Ponieważ każdy App Service Environment ma unikatowy sufiks domeny, deweloperzy mogą skorzystać z tej samej nazwy aplikacji w każdym środowisku.  Na przykład deweloper może mieć aplikacje o nazwie w następujący sposób:  *MyApp.Foo1.p.azurewebsites.NET*, *MyApp.foo2.p.azurewebsites.NET*, *MyApp.Foo3.p.azurewebsites.NET*itd.  Jednak dla przykładowej aplikacji, każde wystąpienie aplikacji ma również unikatową nazwę.  Używane nazwy wystąpień aplikacji to *webfrontend1*, *webfrontend2*i *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Konfigurowanie profilu Traffic Manager
Po wdrożeniu wielu wystąpień aplikacji w wielu środowiskach App Service poszczególne wystąpienia aplikacji mogą być rejestrowane przy użyciu Traffic Manager.  W przypadku przykładowej aplikacji jest wymagany profil Traffic Manager *, który może* kierować klientów do dowolnego z następujących wdrożonych wystąpień aplikacji:

* **webfrontend1.fe1ase.p.azurewebsites.NET:**  Wystąpienie przykładowej aplikacji wdrożonej na pierwszej App Service Environment.
* **webfrontend2.fe2ase.p.azurewebsites.NET:**  Wystąpienie przykładowej aplikacji wdrożonej na drugim App Service Environment.
* **webfrontend3.fe3ase.p.azurewebsites.NET:**  Wystąpienie przykładowej aplikacji wdrożonej na trzecim App Service Environment.

Najprostszym sposobem na zarejestrowanie wielu punktów końcowych Azure App Service, wszystkie działające w **tym samym** regionie świadczenia usługi Azure, jest [Obsługa Traffic Manager Azure Resource Manager][ARMTrafficManager]programu PowerShell.  

Pierwszym krokiem jest utworzenie profilu usługi Azure Traffic Manager.  Poniższy kod przedstawia sposób tworzenia profilu dla przykładowej aplikacji:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Zwróć uwagę na to, jak parametr *RelativeDnsName* został ustawiony na *skalowalność-ASE-Demonstracja*.  Ten parametr powoduje utworzenie i skojarzenie nazwy domeny *Scalable-ASE-demo.trafficmanager.NET* z profilem Traffic Manager.

*TrafficRoutingMethod* parametr definiuje Traffic Manager zasad równoważenia obciążenia, które będą używane do określenia, jak rozłożyć obciążenie klienta na wszystkie dostępne punkty końcowe.  W tym przykładzie wybrano metodę *ważoną* .  Z tego powodu żądania klientów zostaną rozłożone na wszystkie zarejestrowane punkty końcowe aplikacji na podstawie wag względnych, które są skojarzone z każdym punktem końcowym. 

Po utworzeniu profilu każde wystąpienie aplikacji jest dodawane do profilu jako natywny punkt końcowy platformy Azure.  Poniższy kod pobiera odwołanie do każdej aplikacji sieci Web frontonu. Następnie dodaje każdą aplikację jako punkt końcowy Traffic Manager za pomocą parametru *element targetresourceid* .

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

Zwróć uwagę na to, jak jedno wywołanie elementu *Add-AzureTrafficManagerEndpointConfig* dla każdego pojedynczego wystąpienia aplikacji.  Parametr *element targetresourceid* w każdym poleceniu programu PowerShell odwołuje się do jednego z trzech wdrożonych wystąpień aplikacji.  Profil Traffic Manager będzie rozłożony na wszystkie trzy punkty końcowe zarejestrowane w profilu.

Wszystkie trzy punkty końcowe używają tej samej wartości (10) dla parametru *wagi* .  W tej sytuacji powstaje Traffic Manager rozproszenia żądań klientów między wszystkimi trzema wystąpieniami aplikacji. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Wskaż domenę niestandardową aplikacji w domenie Traffic Manager
Ostatnim krokiem niezbędnym jest wskazanie domeny niestandardowej aplikacji w domenie Traffic Manager.  W przypadku przykładowej aplikacji wskaż pozycję `www.scalableasedemo.com` o `scalable-ase-demo.trafficmanager.net` .  Wykonaj ten krok za pomocą rejestratora domen, który zarządza domeną niestandardową.  

Korzystając z narzędzi do zarządzania domeną rejestratora, należy utworzyć rekordy CNAME wskazujące domenę niestandardową w domenie Traffic Manager.  Na poniższej ilustracji przedstawiono przykład tego, jak wygląda ta konfiguracja CNAME:

![Rekord CNAME dla domeny niestandardowej][CNAMEforCustomDomain] 

Chociaż nie zostały omówione w tym temacie, należy pamiętać, że każde pojedyncze wystąpienie aplikacji musi mieć także zarejestrowaną domenę niestandardową.  W przeciwnym razie, jeśli żądanie przeprowadzi to do wystąpienia aplikacji, a aplikacja nie zarejestrowała domeny niestandardowej przy użyciu aplikacji, żądanie zakończy się niepowodzeniem.

W tym przykładzie domena niestandardowa jest `www.scalableasedemo.com` , a każde wystąpienie aplikacji ma skojarzoną z nią domenę niestandardową.

![Domena niestandardowa][CustomDomain] 

Podsumowanie rejestracji niestandardowej domeny za pomocą aplikacji Azure App Service można znaleźć w temacie [Rejestrowanie domen niestandardowych][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Próba wypróbowania topologii rozproszonej
Na końcu Traffic Manager i konfiguracji DNS żądania `www.scalableasedemo.com` będą przepływać przez następującą sekwencję:

1. Przeglądarka lub urządzenie przeprowadzi wyszukiwanie DNS `www.scalableasedemo.com`
2. Wpis CNAME w rejestratorze domeny powoduje przekierowanie wyszukiwania DNS do usługi Azure Traffic Manager.
3. Dla *Scalable-ASE-demo.trafficmanager.NET* na jednym z serwerów DNS na platformie Traffic Manager Azure jest tworzone wyszukiwanie DNS.
4. Na podstawie zasad równoważenia obciążenia określonych wcześniej w parametrze *TrafficRoutingMethod* Traffic Manager wybiera jeden ze skonfigurowanych punktów końcowych. Następnie zwraca nazwę FQDN tego punktu końcowego do przeglądarki lub urządzenia.
5. Ponieważ nazwa FQDN punktu końcowego jest adresem URL wystąpienia aplikacji uruchomionego na App Service Environment, przeglądarka lub urządzenie poprosiła Microsoft Azure DNS o rozpoznanie nazwy FQDN na adres IP. 
6. Przeglądarka lub urządzenie wyśle żądanie HTTP/S na adres IP.  
7. Żądanie będzie docierać do jednego z wystąpień aplikacji uruchomionych w jednym ze środowisk App Service.

Na poniższej ilustracji przedstawiono wyszukiwanie DNS dla domeny niestandardowej przykładowej aplikacji. Pomyślnie rozpoznano wystąpienie aplikacji działające w jednym z trzech przykładowych środowisk App Service (w tym przypadku jest to drugi z trzech środowisk App Service):

![Wyszukiwanie DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Dodatkowe linki i informacje
Dokumentacja dotycząca [obsługi Traffic Manager Azure Resource Manager][ARMTrafficManager]programu PowerShell.  

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
