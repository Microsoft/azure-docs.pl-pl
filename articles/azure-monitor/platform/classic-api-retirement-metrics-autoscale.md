---
title: Wycofywanie interfejsów API wdrażania dla metryk Azure Monitor i skalowanie automatyczne
description: Metryki i automatyczne skalowanie klasycznych interfejsów API, nazywane również usługą Azure Service Management (ASM) lub modelem wdrożenia frontonu reddog
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: a8900d238e7542864e3dc53f9d192e579e2c96d1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536697"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor wycofywanie interfejsów API klasycznego modelu wdrażania dla metryk i skalowania automatycznego

Azure Monitor (dawniej usługa Azure Insights podczas pierwszego wydania) oferuje obecnie możliwość tworzenia ustawień automatycznego skalowania i korzystania z nich przy użyciu metryk z klasycznych maszyn wirtualnych i klasycznych Cloud Services. Oryginalny zestaw klasycznych interfejsów API opartych na modelu wdrożenia zostanie **wycofany po 30 czerwca 2019** we wszystkich chmurach publicznych i prywatnych platformy Azure we wszystkich regionach.   

Te same operacje były obsługiwane przez zestaw interfejsów API opartych na Azure Resource Manager przez cały rok. W Azure Portal są używane nowe interfejsy API REST dla funkcji automatycznego skalowania i metryk. Dostępne są również nowe zestawy SDK, program PowerShell i interfejs wiersza polecenia na podstawie tych Menedżer zasobów interfejsów API. Nasze usługi partnerskie na potrzeby monitorowania wykorzystują nowe Menedżer zasobów oparte na interfejsie API REST w programie Azure Monitor.  

## <a name="who-is-not-affected"></a>Nie dotyczy

Jeśli zarządzasz skalowaniem automatycznym za pośrednictwem Azure Portal, [nowego Azure monitor zestawu SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), programu PowerShell, interfejsu wiersza polecenia lub szablonów Menedżer zasobów, nie trzeba podejmować żadnych działań.  

W przypadku korzystania z metryk za pośrednictwem Azure Portal lub różnych [usług partnerskich monitorowania](./partners.md)nie jest wymagane żadne działanie. Firma Microsoft współpracuje z partnerami monitorowania w celu migrowania do nowych interfejsów API.

## <a name="who-is-affected"></a>Kogo dotyczy

Ten artykuł ma zastosowanie w przypadku korzystania z następujących składników:

- **Klasyczny zestaw SDK usługi Azure Insights** — Jeśli korzystasz z [klasycznego zestawu SDK usługi Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), przełącz się do korzystania z nowego zestawu SDK Azure monitor dla [platformy .NET](https://github.com/azure/azure-libraries-for-net#download) lub [Java](https://github.com/azure/azure-libraries-for-java#download). Pobierz [pakiet NuGet zestawu SDK Azure monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klasyczne automatyczne skalowanie** — Jeśli wywołujesz [klasyczne interfejsy API ustawień automatycznego skalowania](/previous-versions/azure/reference/mt348562(v=azure.100)) z wbudowanych narzędzi niestandardowych lub korzystając z [klasycznego zestawu SDK usługi Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), należy przełączyć się do korzystania z [interfejsu API REST Azure monitor Menedżer zasobów](/rest/api/monitor/autoscalesettings).

- **Metryki klasyczne** — Jeśli korzystasz z metryk przy użyciu [klasycznych interfejsów API REST](/previous-versions/azure/reference/dn510374(v=azure.100)) lub  [klasycznego zestawu SDK usługi Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) z poziomu wbudowanych narzędzi, należy przełączyć się do korzystania z [interfejsu API REST Menedżer zasobów Azure monitor](/rest/api/monitor/autoscalesettings). 

Jeśli nie masz pewności, czy Twój kod lub narzędzia niestandardowe wywołuje klasyczne interfejsy API, zapoznaj się z następującymi tematami:

- Przejrzyj identyfikator URI, do którego istnieje odwołanie w kodzie lub narzędziu. Klasyczne interfejsy API używają identyfikatora URI https://management.core.windows.net . Należy użyć nowszego identyfikatora URI dla interfejsów API opartych na Menedżer zasobów rozpoczyna się od `https://management.azure.com/` .

- Porównaj nazwę zestawu na swojej maszynie. Starszy zestaw klasyczny ma wartość o  https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/ .

- Jeśli używasz uwierzytelniania certyfikatu w celu uzyskania dostępu do metryk lub interfejsów API automatycznego skalowania, używasz klasycznego punktu końcowego i biblioteki. Nowsze Menedżer zasobów interfejsy API wymagają uwierzytelniania Azure Active Directory za pośrednictwem jednostki usługi lub nazwy głównej użytkownika.

- W przypadku korzystania z wywołań, do których odwołuje się w dokumentacji na dowolnym z poniższych linków, używane są starsze klasyczne interfejsy API.

  - [Biblioteka klas Windows. Azure. Management. Monitoring](/previous-versions/azure/dn510414(v=azure.100))

  - [Monitorowanie (klasyczne) .NET](/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations, interfejs](/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Dlaczego należy przełączyć

Wszystkie istniejące możliwości skalowania automatycznego i metryk będą nadal działały za pośrednictwem nowych interfejsów API.  

Migrowanie do nowszych interfejsów API wiąże się z możliwościami opartymi na Menedżer zasobów, takimi jak obsługa spójnej kontroli dostępu opartej na rolach (Azure RBAC) w ramach wszystkich usług monitorowania. Możesz również uzyskać dodatkową funkcjonalność dla metryk: 

- Obsługa wymiarów
- spójność 1-minutowego stopnia szczegółowości dla wszystkich usług 
- lepsze wykonywanie zapytań
- wyższe przechowywanie danych (od 93 do 30 dni) 

Ogólnie, podobnie jak w przypadku wszystkich innych usług na platformie Azure, Azure Monitor interfejsy API oparte na Menedżer zasobów są dostępne w celu zapewnienia lepszej wydajności, skalowalności i niezawodności. 

## <a name="what-happens-if-you-do-not-migrate"></a>Co się stanie, jeśli nie wykonasz migracji

### <a name="before-retirement"></a>Przed wycofaniem

Nie będzie żadnego bezpośredniego wpływu na usługi platformy Azure ani ich obciążenia.  

### <a name="after-retirement"></a>Po wycofaniu

Wszystkie wywołania klasycznych interfejsów API wymienione wcześniej zakończą się niepowodzeniem i zwracają komunikaty o błędach podobne do następujących:

W przypadku automatycznego skalowania: *ten interfejs API jest przestarzały. Aby zarządzać ustawieniami automatycznego skalowania, użyj szablonów Azure Portal, Azure Monitor SDK, programu PowerShell, interfejsu wiersza polecenia lub Menedżer zasobów*.  

Metryki: *ten interfejs API jest przestarzały. Użyj Azure Portal, Azure Monitor SDK, programu PowerShell, interfejsu wiersza polecenia, aby wykonać zapytanie o metryki*.

## <a name="email-notifications"></a>Powiadomienia e-mail

Powiadomienie o wycofaniu zostało wysłane na adresy e-mail dla następujących ról konta: 

- Administratorzy konta i usługi
- Współadministratorów  

Jeśli masz jakieś pytania, skontaktuj się z nami pod adresem MonitorClassicAPIhelp@microsoft.com .  

## <a name="references"></a>Odwołania

- [Nowsze interfejsy API REST dla Azure Monitor](/rest/api/monitor/) 
- [Nowszy Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)

