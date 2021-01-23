---
title: Schemat definicji usługi Azure Cloud Services (obsługa rozszerzona) (plik csdef) | Microsoft Docs
description: Informacje dotyczące schematu definicji (csdef) dla Cloud Services (obsługa rozszerzona)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ab85067184ebe5b34097a3c81aa521d509ae4b9a
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744859"
---
# <a name="azure-cloud-services-extended-support-definition-schema-csdef-file"></a>Schemat definicji Cloud Services platformy Azure (obsługa rozszerzona) (plik csdef)

Plik definicji usługi definiuje model usługi dla aplikacji. Plik zawiera definicje ról, które są dostępne dla usługi w chmurze, określa punkty końcowe usługi i ustanawia ustawienia konfiguracji usługi. Wartości ustawień konfiguracji są ustawiane w pliku konfiguracji usługi zgodnie z opisem w [schemacie konfiguracji usługi w chmurze (obsługa rozszerzona)](schema-cscfg-file.md)).

Domyślnie plik schematu konfiguracji Diagnostyka Azure jest instalowany w `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` katalogu. Zamień na `<version>` zainstalowaną wersję [zestawu Azure SDK](https://www.windowsazure.com/develop/downloads/).

Domyślnym rozszerzeniem dla pliku definicji usługi jest csdef.

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
| upgradeDomainCount      | Opcjonalny. Określa liczbę domen uaktualnienia, dla których przydzielono role w tej usłudze. Wystąpienia roli są przypisywane do domeny uaktualnienia podczas wdrażania usługi. Aby uzyskać więcej informacji, zobacz temat [Aktualizowanie roli usługi w chmurze lub wdrożenia](sample-update-cloud-service.md) oraz [Zarządzanie dostępnością maszyn wirtualnych](../virtual-machines/manage-availability.md) , można określić maksymalnie 20 domen uaktualnienia. Jeśli nie zostanie określony, domyślna liczba domen uaktualnienia to 5.|

## <a name="see-also"></a>Zobacz także

[Schemat konfiguracji usługi Azure Cloud Services (obsługa rozszerzona) (plik cscfg)](schema-cscfg-file.md).