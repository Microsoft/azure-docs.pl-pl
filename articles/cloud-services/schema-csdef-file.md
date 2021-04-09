---
title: Schemat definicji platformy Azure Cloud Services (klasyczny) (plik. csdef) | Microsoft Docs
description: Plik definicji usługi (. csdef) definiuje model usługi dla aplikacji, zawierający dostępne role, punkty końcowe i wartości konfiguracyjne dla usługi.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: f201bc05795fa6aece256f3d3b4bd650385fef48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934142"
---
# <a name="azure-cloud-services-classic-definition-schema-csdef-file"></a>Schemat definicji platformy Azure Cloud Services (klasyczny) (plik. csdef)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Plik definicji usługi definiuje model usługi dla aplikacji. Plik zawiera definicje ról, które są dostępne dla usługi w chmurze, określa punkty końcowe usługi i ustanawia ustawienia konfiguracji usługi. Wartości ustawień konfiguracji są ustawiane w pliku konfiguracji usługi zgodnie z opisem w [schemacie konfiguracji usługi w chmurze (klasycznej)](/previous-versions/azure/reference/ee758710(v=azure.100)).

Domyślnie plik schematu konfiguracji Diagnostyka Azure jest instalowany w `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` katalogu. Zamień na `<version>` zainstalowaną wersję [zestawu Azure SDK](https://www.windowsazure.com/develop/downloads/).

Domyślnym rozszerzeniem dla pliku definicji usługi jest. csdef.

## <a name="basic-service-definition-schema"></a>Schemat definicji usługi podstawowej
Plik definicji usługi musi zawierać jeden `ServiceDefinition` element. Definicja usługi musi zawierać co najmniej jeden element roli ( `WebRole` lub `WorkerRole` ). Może zawierać do 25 ról zdefiniowanych w jednej definicji i można mieszać typy ról. Definicja usługi zawiera również opcjonalny `NetworkTrafficRules` element, który ogranicza, które role mogą komunikować się z określonymi wewnętrznymi punktami końcowymi. Definicja usługi zawiera również opcjonalny `LoadBalancerProbes` element, który zawiera określone przez klienta sondy kondycji punktów końcowych.

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

- [Schemat LoadBalancerProbe](schema-csdef-loadbalancerprobe.md)
- [Schemat WebRole](schema-csdef-webrole.md)
- [Schemat WorkerRole](schema-csdef-workerrole.md)
- [Schemat NetworkTrafficRules](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a> Element ServiceDefinition
`ServiceDefinition`Element jest elementem najwyższego poziomu pliku definicji usługi.

W poniższej tabeli opisano atrybuty `ServiceDefinition` elementu.

| Atrybut               | Opis |
| ----------------------- | ----------- |
| name                    |Wymagane. Nazwa usługi. Nazwa musi być unikatowa w ramach konta usługi.|
| topologyChangeDiscovery | Opcjonalny. Określa typ powiadomienia o zmianie topologii. Możliwe wartości:<br /><br /> -   `Blast` -Wysyła aktualizację tak szybko, jak to możliwe, do wszystkich wystąpień ról. W przypadku wybrania opcji, rola powinna być w stanie obsługiwać aktualizację topologii bez ponownego uruchamiania.<br />-   `UpgradeDomainWalk` — Wysyła aktualizację do każdego wystąpienia roli w sposób sekwencyjny po pomyślnym zaakceptowaniu przez poprzednie wystąpienie aktualizacji.|
| schemaVersion           | Opcjonalny. Określa wersję schematu definicji usługi. Wersja schematu umożliwia programowi Visual Studio wybranie poprawnych narzędzi zestawu SDK, które będą używane do sprawdzania poprawności schematu w przypadku, gdy więcej niż jedna wersja zestawu SDK jest zainstalowana obok siebie.|
| upgradeDomainCount      | Opcjonalny. Określa liczbę domen uaktualnienia, dla których przydzielono role w tej usłudze. Wystąpienia roli są przypisywane do domeny uaktualnienia podczas wdrażania usługi. Aby uzyskać więcej informacji, zobacz temat [Aktualizowanie roli usługi w chmurze lub wdrożenia](cloud-services-how-to-manage-portal.md#update-a-cloud-service-role-or-deployment), [Zarządzanie dostępnością maszyn wirtualnych](../virtual-machines/availability.md) i [co to jest model usługi w chmurze](./cloud-services-model-and-package.md).<br /><br /> Można określić maksymalnie 20 domen uaktualnienia. Jeśli nie zostanie określony, domyślna liczba domen uaktualnienia to 5.|