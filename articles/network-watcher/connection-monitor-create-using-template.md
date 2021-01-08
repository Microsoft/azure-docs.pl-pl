---
title: Tworzenie monitora połączeń — szablon ARM
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak utworzyć monitor połączeń przy użyciu ARMClient.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 46d569eae768178815a933d9a857e19bacdaaeb9
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013230"
---
# <a name="create-a-connection-monitor-using-the-arm-template"></a>Tworzenie monitora połączeń przy użyciu szablonu ARM

Dowiedz się, jak utworzyć monitor połączeń do monitorowania komunikacji między zasobami przy użyciu ARMClient. Obsługuje wdrożenia w chmurze hybrydowej i platformy Azure.


## <a name="before-you-begin"></a>Przed rozpoczęciem 

W monitorach połączeń utworzonych w monitorze połączeń można dodawać zarówno maszyny lokalne, jak i maszyny wirtualne platformy Azure jako źródła. Te monitory połączeń mogą również monitorować łączność z punktami końcowymi. Punkty końcowe mogą znajdować się na platformie Azure lub dowolnym innym adresem URL lub adresie IP.

Monitor połączeń obejmuje następujące jednostki:

* **Zasób monitora połączeń** — zasób platformy Azure specyficzny dla regionu. Wszystkie poniższe jednostki są właściwościami zasobu monitora połączeń.
* **Endpoint** — Źródło lub miejsce docelowe, które uczestniczy w sprawdzaniu łączności. Przykładowe punkty końcowe obejmują maszyny wirtualne platformy Azure, agentów lokalnych, adresy URL i adresy IP.
* **Konfiguracja testu** — Konfiguracja specyficzna dla protokołu dla testu. Na podstawie wybranego protokołu można zdefiniować port, progi, częstotliwość testów i inne parametry.
* **Grupa testowa** — grupa zawierająca źródłowe punkty końcowe, docelowe punkty końcowe i konfiguracje testów. Monitor połączeń może zawierać więcej niż jedną grupę testową.
* **Test** — połączenie źródłowego punktu końcowego, docelowego punktu końcowego i konfiguracji testu. Test to najbardziej szczegółowy poziom, na którym dane monitorowania są dostępne. Dane monitorowania obejmują procent testów zakończonych niepowodzeniem i czas błądzenia (RTT).

    ![Diagram przedstawiający monitor połączeń, który definiuje relację między grupami testów i testami](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Procedura tworzenia z przykładowym szablonem ARM

Użyj poniższego kodu, aby utworzyć monitor połączeń przy użyciu ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'endpoint_workspace_machine',

type: 'MMAWorkspaceMachine',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

//Example 1: Choose a machine

address : '<non-Azure machine FQDN>'
}

//Example 2: Select IP from chosen machines

address : '<non-Azure machine FQDN>

"scope": {
      "include": [
            {
                  "address": "<IP belonging to machine chosen above>"  
        }
       ]
      }
   }    
   
name: 'endpoint_workspace_network',

type: 'MMAWorkspaceNetwork',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

 coverage level : 'high', //Optional
 
 //Include subnets. You can also exclude IPs from subnet to exclude from monitoring
 
 scope: {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28
            },
            {
                  "address": "<subnet 2 mask>" 
            }
      ],
      "exclude": [
            { 
            "address" : "<ip-from-included-subnets-that-should-be-excluded>"
        }
      ]
     }
},

//Use a Azure VM as an endpoint
{

name: 'endpoint_virtualmachine',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

//Use an Azure VNET or Subnet as an endpoint

 {

name: 'endpoint_vnet_subnet',

resourceId: '<resource id of VNET or subnet'
coverage level: 'high' //Optional

//Scope is optional.

  "scope": {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28 .This subnet should match with any existing subnet in vnet
            }
      ],
    "exclude": [
            {
                  "address": "<ip-from-included-subnets-that-should-be-excluded>" // If used with include, IP should be part of the subnet defined above. Without include, this could be any address within vnet range or any specific subnet range as a whole.
            }
      ]
  }
   },

//Endpoint as a URL
{

name: 'azure portal'

address: '<URL>'

   },

//Endpoint as an IP 
 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {
    
     port: '<port of choice>'
  
    preferHTTPS: true // If port chosen is not 80 or 443
    
    method: 'GET', //Choose GET or POST
    
    path: '/', //Specify path for request
         
    requestHeaders: [
            {
              "name": "Content-Type",
              "value": "appication/json"
            }
          ],
          
    validStatusCodeRanges: [ "102", "200-202", "3xx" ], //Samples
          
    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, 
   {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Oto polecenie wdrożenia:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Opis właściwości

* connectionMonitorName — nazwa zasobu monitora połączeń

* Identyfikator subskrypcji PODRZĘDNEj subskrypcji, w której chcesz utworzyć monitor połączeń

* Identyfikator zasobu NW Network Watcher, w którym zostanie utworzony Menedżer CM 

* Lokalizacja — region, w którym zostanie utworzony monitor połączeń

* Punkty końcowe
    * Nazwa — unikatowa nazwa dla każdego punktu końcowego
    * resourceId — dla punktów końcowych platformy Azure identyfikator zasobu odnosi się do Azure Resource Manager IDENTYFIKATORem zasobu dla maszyn wirtualnych. W przypadku punktów końcowych spoza platformy Azure identyfikator zasobu odnosi się do Azure Resource Manager identyfikator zasobu dla obszaru roboczego Log Analytics połączonego z agentami nienależącymi do platformy Azure.
    * adres — dotyczy tylko sytuacji, gdy nie określono identyfikatora zasobu lub jeśli identyfikator zasobu jest Log Analytics obszarze roboczym. Jeśli jest używany z IDENTYFIKATORem zasobu Log Analytics, to odnosi się do nazwy FQDN agenta, która może być używana do monitorowania. Jeśli jest używany bez identyfikatora zasobu, może to być adres URL lub IP dowolnego publicznego punktu końcowego.
    * Filtr — w przypadku punktów końcowych spoza platformy Azure Użyj opcji Filtruj, aby wybrać agentów z Log Analytics obszaru roboczego, które będą używane do monitorowania w ramach zasobu monitora połączeń. Jeśli filtry nie są ustawione, Wszyscy agenci należący do obszaru roboczego Log Analytics mogą być używani do monitorowania
        * Typ — Ustaw typ jako "adres agenta"
        * Address — Ustaw adres jako nazwę FQDN lokalnego agenta

* Grupy testowe
    * Nazwa — Nadaj nazwę grupie testowej.
    * testConfigurations-test Configurations, na podstawie których źródłowe punkty końcowe nawiązują połączenie z docelowym punktami końcowymi
    * źródła — wybierz spośród utworzonych powyżej punktów końcowych. Punkty końcowe źródłowej platformy Azure muszą mieć zainstalowane rozszerzenie usługi Azure Network Watcher i punkty końcowe źródłowe na platformie Azure muszą być haveAzure Log Analytics zainstalowanym agentem. Aby zainstalować agenta dla źródła, zobacz [Instalowanie agentów monitorowania](./connection-monitor-overview.md#install-monitoring-agents).
    * miejsca docelowe — wybierz spośród utworzonych powyżej punktów końcowych. Można monitorować łączność z maszynami wirtualnymi platformy Azure lub dowolnym punktem końcowym (publicznym adresem IP, URL lub nazwą FQDN) przez określenie ich jako miejsc docelowych. W jednej grupie testowej można dodać maszyny wirtualne platformy Azure, adresy URL pakietu Office 365, adresy URL Dynamics 365 i niestandardowe punkty końcowe.
    * Wyłącz — to pole służy do wyłączania monitorowania dla wszystkich źródeł i miejsc docelowych określanych przez grupę testową.

* Konfiguracje testowe
    * Nazwa — nazwa konfiguracji testu.
    * testFrequencySec — Określ, jak często źródła będą wysyłać polecenia ping do określonych protokołów i portów. Możesz wybrać 30 sekund, 1 minutę, 5 minut, 15 minut lub 30 minut. Źródła przetestują łączność do miejsc docelowych na podstawie wybranej wartości. Jeśli na przykład wybierzesz 30 sekund, źródła będą sprawdzać łączność z miejscem docelowym co najmniej raz w okresie 30 sekund.
    * Protokół — można wybrać TCP, ICMP, HTTP lub HTTPS. W zależności od protokołu można wykonać kilka konfiguracji specyficznych dla protokołu
    
        * preferHTTPS — Określ, czy używać protokołu HTTPS przez HTTP, gdy port jest używany nie 80 ani 443
        * Port — określ wybrany port docelowy.
        * disableTraceRoute — dotyczy to konfiguracji testów, których protokół to TCP lub ICMP. Uniemożliwia ona źródłem odnajdywania topologii i RTT przeskoków przez przeskok.
        * Metoda — dotyczy to konfiguracji testów, których protokół to HTTP. Wybierz metodę żądania HTTP — Pobierz lub Opublikuj
        * ścieżka — Określ parametry ścieżki do dołączenia do adresu URL
        * validStatusCodes — wybierz odpowiednie kody stanu. Jeśli kod odpowiedzi nie jest zgodny z tą listą, otrzymasz komunikat diagnostyczny
        * requestHeaders — określ niestandardowe ciągi nagłówka żądania, które zostaną przesłane do miejsca docelowego
        
    * successThreshold — można ustawić progi dla następujących parametrów sieci:
        * checksFailedPercent — Ustaw procent kontroli, które mogą zakończyć się niepowodzeniem, gdy źródła sprawdzają łączność z lokalizacjami docelowymi przy użyciu określonych kryteriów. W przypadku protokołu TCP lub ICMP wartość procentowa nieudanych testów może być równa wartości procentowej utraty pakietów. W przypadku protokołu HTTP to pole reprezentuje procent żądań HTTP, które nie otrzymały odpowiedzi.
        * roundTripTimeMs — Ustaw czas RTT w milisekundach, przez jaki źródła mogą być wykonywane w celu nawiązania połączenia z miejscem docelowym za pośrednictwem konfiguracji testu.

## <a name="scale-limits"></a>Limity skalowania

Monitory połączeń mają następujące limity skali:

* Maksymalna liczba monitorów połączeń na subskrypcję na region: 100
* Maksymalna liczba grup testów na monitor połączeń: 20
* Maksymalna liczba źródeł i miejsc docelowych na monitor połączeń: 100
* Maksymalna liczba konfiguracji testu na monitor połączeń: 20 za pośrednictwem ARMClient

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak analizować dane monitorowania i ustawiać alerty](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Dowiedz się [, jak zdiagnozować problemy w sieci](./connection-monitor-overview.md#diagnose-issues-in-your-network)