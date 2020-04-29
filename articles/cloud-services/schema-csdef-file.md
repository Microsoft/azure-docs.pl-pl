---
title: Schemat definicji Cloud Services platformy Azure (plik. csdef) | Microsoft Docs
description: Plik definicji usługi (. csdef) definiuje model usługi dla aplikacji, zawierający dostępne role, punkty końcowe i wartości konfiguracyjne dla usługi.
ms.custom: ''
ms.date: 04/14/2015
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 42
author: tgore03
ms.author: tagore
ms.openlocfilehash: dadb50bd0663f47e6a1bf3d58b5187c8b466964d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528374"
---
# <a name="azure-cloud-services-definition-schema-csdef-file"></a>Schemat definicji Cloud Services platformy Azure (plik. csdef)
Plik definicji usługi definiuje model usługi dla aplikacji. Plik zawiera definicje ról, które są dostępne dla usługi w chmurze, określa punkty końcowe usługi i ustanawia ustawienia konfiguracji usługi. Wartości ustawień konfiguracji są ustawiane w pliku konfiguracji usługi zgodnie z opisem w [schemacie konfiguracji usługi w chmurze (klasycznej)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Domyślnie plik schematu konfiguracji Diagnostyka Azure jest instalowany w `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` katalogu. Zamień `<version>` na zainstalowaną wersję [zestawu Azure SDK](https://www.windowsazure.com/develop/downloads/).

Domyślnym rozszerzeniem dla pliku definicji usługi jest. csdef.

## <a name="basic-service-definition-schema"></a>Schemat definicji usługi podstawowej
Plik definicji usługi musi zawierać jeden `ServiceDefinition` element. Definicja usługi musi zawierać co najmniej jeden element roli (`WebRole` lub `WorkerRole`). Może zawierać do 25 ról zdefiniowanych w jednej definicji i można mieszać typy ról. Definicja usługi zawiera również opcjonalny `NetworkTrafficRules` element, który ogranicza, które role mogą komunikować się z określonymi wewnętrznymi punktami końcowymi. Definicja usługi zawiera również opcjonalny `LoadBalancerProbes` element, który zawiera określone przez klienta sondy kondycji punktów końcowych.

Podstawowy format pliku definicji usługi jest następujący:.

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>Definicje schematu
W poniższych tematach opisano schemat:

- [LoadBalancerProbe — schemat](schema-csdef-loadbalancerprobe.md)
- [WebRole — schemat](schema-csdef-webrole.md)
- [WorkerRole — schemat](schema-csdef-workerrole.md)
- [NetworkTrafficRules — schemat](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a>Element ServiceDefinition
`ServiceDefinition` Element jest elementem najwyższego poziomu pliku definicji usługi.

W poniższej tabeli opisano atrybuty `ServiceDefinition` elementu.

| Atrybut               | Opis |
| ----------------------- | ----------- |
| name                    |Wymagany. Nazwa usługi. Nazwa musi być unikatowa w ramach konta usługi.|
| topologyChangeDiscovery | Opcjonalny. Określa typ powiadomienia o zmianie topologii. Możliwe wartości:<br /><br /> -   `Blast`-Wysyła aktualizację tak szybko, jak to możliwe, do wszystkich wystąpień ról. W przypadku wybrania opcji, rola powinna być w stanie obsługiwać aktualizację topologii bez ponownego uruchamiania.<br />-   `UpgradeDomainWalk`— Wysyła aktualizację do każdego wystąpienia roli w sposób sekwencyjny po pomyślnym zaakceptowaniu przez poprzednie wystąpienie aktualizacji.|
| schemaVersion           | Opcjonalny. Określa wersję schematu definicji usługi. Wersja schematu umożliwia programowi Visual Studio wybranie poprawnych narzędzi zestawu SDK, które będą używane do sprawdzania poprawności schematu w przypadku, gdy więcej niż jedna wersja zestawu SDK jest zainstalowana obok siebie.|
| upgradeDomainCount      | Opcjonalny. Określa liczbę domen uaktualnienia, dla których przydzielono role w tej usłudze. Wystąpienia roli są przypisywane do domeny uaktualnienia podczas wdrażania usługi. Aby uzyskać więcej informacji, zobacz temat [Aktualizowanie roli usługi w chmurze lub wdrożenia](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [Zarządzanie dostępnością maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) i [co to jest model usługi w chmurze](https://docs.microsoft.com/azure/cloud-services/cloud-services-model-and-package).<br /><br /> Można określić maksymalnie 20 domen uaktualnienia. Jeśli nie zostanie określony, domyślna liczba domen uaktualnienia to 5.|
