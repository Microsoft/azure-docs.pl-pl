---
title: Zarządzanie dostępem do danych wskaźnikowych platformy Azure według zasobu | Microsoft Docs
description: W tym artykule wyjaśniono, jak można zarządzać dostępem do danych wskaźnikowych platformy Azure przez zasoby, do których użytkownik może uzyskać dostęp. Zarządzanie dostępem według zasobów pozwala zapewnić dostęp tylko do określonych danych, bez całego środowiska usługi Azure wskaźnikowej. Ta metoda jest również znana jako RBAC do kontekstu zasobów.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: fc1246d079760fd86513840aebbffa34d192f8ed
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044179"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>Zarządzanie dostępem do danych wskaźnikowych platformy Azure według zasobu

Zazwyczaj użytkownicy mający dostęp do obszaru roboczego wskaźnikowego platformy Azure mają również dostęp do wszystkich danych obszaru roboczego, w tym zawartości zabezpieczeń. Administratorzy mogą używać [ról platformy Azure](roles.md) , aby skonfigurować dostęp do określonych funkcji na platformie Azure, w zależności od wymagań dostępu w zespole.

Może jednak istnieć niektórzy użytkownicy, którzy muszą uzyskać dostęp tylko do określonych danych w obszarze roboczym wskaźnikowym platformy Azure, ale nie powinni mieć dostępu do całego środowiska wskaźnikowego platformy Azure. Na przykład może być konieczne dostarczenie zespołu operacji niezwiązanych z zabezpieczeniami (niesocd) z dostępem do danych zdarzeń systemu Windows dla serwerów, których są właścicielami.

W takich przypadkach zaleca się skonfigurowanie kontroli dostępu opartej na rolach (RBAC) na podstawie zasobów, które są dozwolone dla użytkowników, zamiast zapewniać im dostęp do obszaru roboczego wskaźnikowego platformy Azure lub do określonych funkcji wskaźnikowych platformy Azure. Ta metoda jest również znana jako Konfiguracja **RBAC kontekstu zasobów**.

Gdy użytkownicy mają dostęp do danych wskaźnikowych platformy Azure za pośrednictwem zasobów, do których mogą uzyskać dostęp zamiast obszaru roboczego wskaźnik platformy Azure, mogą wyświetlać dzienniki i skoroszyty przy użyciu następujących metod:

- **Za pośrednictwem samego zasobu**, takiego jak maszyna wirtualna platformy Azure. Ta metoda służy do wyświetlania dzienników i skoroszytów tylko dla określonego zasobu.

- **Za pośrednictwem Azure monitor**. Tej metody należy użyć, gdy chcesz utworzyć zapytania obejmujące wiele zasobów i/lub grup zasobów. Podczas nawigowania do dzienników i skoroszytów w Azure Monitor, zdefiniuj swój zakres do co najmniej jednej konkretnej grupy zasobów lub zasobów.

Włącz funkcję RBAC dla kontekstu zasobów w Azure Monitor. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do danych dziennika i obszarów roboczych w Azure monitor](../azure-monitor/logs/manage-access.md).

> [!NOTE]
> Jeśli dane nie są zasobami platformy Azure, takimi jak dziennik systemowy, CEF lub AAD lub dane zbierane przez moduł zbierający niestandardowy, należy ręcznie skonfigurować identyfikator zasobu używany do identyfikowania danych i włączyć dostęp.
>
> Aby uzyskać więcej informacji, zobacz [jawnie Konfigurowanie kontroli dostępu do kontekstu zasobów](#explicitly-configure-resource-context-rbac).
>
## <a name="scenarios-for-resource-context-rbac"></a>Scenariusze kontroli RBAC dla kontekstu zasobów

W poniższej tabeli przedstawiono scenariusze, w których kontrola dostępu do kontekstu zasobów jest najbardziej przydatna. Należy zwrócić uwagę na różnice w wymaganiach dotyczących dostępu między zespoły SOC i zespoły niesocowe.

| Typ wymagania |Zespół SOC  |Zespół niebędący SOCem  |
|---------|---------|---------|
|**Uprawnienia**     | Cały obszar roboczy        |   Tylko określone zasoby      |
|**Dostęp do danych**     |  Wszystkie dane w obszarze roboczym       | Tylko dane dla zasobów, do których zespół ma uprawnienia dostępu        |
|**Środowisko użytkownika**     |  Pełne środowisko wskaźnikowe platformy Azure, które może być ograniczone przez [uprawnienia funkcjonalne](roles.md) przypisane do użytkownika       |  Rejestruj tylko zapytania i skoroszyty       |
|     |         |         |

Jeśli zespół ma podobne wymagania dotyczące dostępu do zespołu niesocowego opisanego w powyższej tabeli, kontrola dostępu do kontekstu zasobów może być dobrym rozwiązaniem dla Twojej organizacji.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Alternatywne metody implementowania funkcji RBAC dla kontekstu zasobów

W zależności od uprawnień wymaganych w organizacji korzystanie z funkcji RBAC kontekstu zasobów może nie zapewniać pełnego rozwiązania.

Na poniższej liście opisano scenariusze, w których inne rozwiązania do uzyskiwania dostępu do danych mogą lepiej dopasować się do wymagań:

|Scenariusz  |Rozwiązanie  |
|---------|---------|
|**Jednostka zależna ma zespół SOC, który wymaga pełnego środowiska Azure wskaźnikowego**.     |  W takim przypadku należy użyć architektury wieloskładnikowej w celu oddzielenia uprawnień do danych. <br><br>Aby uzyskać więcej informacji, zobacz: <br>- [Rozszerzona wskaźnik platformy Azure na obszary robocze i dzierżawy](extend-sentinel-across-workspaces-tenants.md)<br>    - [Pracuj ze zdarzeniami w wielu obszarach roboczych naraz](multiple-workspace-view.md)          |
|**Chcesz zapewnić dostęp do określonego typu zdarzenia**.     |  Na przykład Podaj administratorowi systemu Windows dostęp do zdarzeń zabezpieczeń systemu Windows w obszarze Wszystkie systemy. <br><br>W takich przypadkach należy użyć [RBAC na poziomie tabeli](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) , aby zdefiniować uprawnienia dla każdej tabeli.       |
| **Ogranicz dostęp do bardziej szczegółowego poziomu, albo nie na podstawie zasobu, albo tylko do podzbioru pól w zdarzeniu**   |   Na przykład możesz chcieć ograniczyć dostęp do dzienników pakietu Office 365 w oparciu o jednostkę zależną użytkownika. <br><br>W takim przypadku należy zapewnić dostęp do danych przy użyciu wbudowanej integracji z [pulpitami nawigacyjnymi i raportami Power BI](../azure-monitor/visualize/powerbi.md).      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Jawne konfigurowanie kontroli dostępu do kontekstu zasobów

Poniższe kroki umożliwiają skonfigurowanie kontroli dostępu do kontekstu zasobów, ale dane nie są zasobami platformy Azure.

Na przykład dane w obszarze roboczym wskaźnikowym platformy Azure, które nie są zasobami platformy Azure, obejmują dane dziennika systemowego, CEF lub AAD lub dane zbierane przez moduł zbierający niestandardowe.

**Aby jawnie skonfigurować kontrolę kontekstu zasobów**:

1. Upewnij się, że [włączono funkcję RBAC dla kontekstu zasobów](../azure-monitor/logs/manage-access.md) w Azure monitor. 

1. [Utwórz grupę zasobów](../azure-resource-manager/management/manage-resource-groups-portal.md) dla każdego zespołu użytkowników, którzy muszą uzyskać dostęp do zasobów bez całego środowiska wskaźnikowego platformy Azure.

    Przypisz [uprawnienia odczytywania dziennika](../azure-monitor/logs/manage-access.md#resource-permissions) dla każdego członka zespołu.

1. Przypisz zasoby do utworzonych grup zespołu zasobów i Oznacz zdarzenia za pomocą odpowiednich identyfikatorów zasobów.

    Gdy zasoby platformy Azure wysyłają dane do danych wskaźnikowych platformy Azure, rekordy dziennika są automatycznie oznaczane IDENTYFIKATORem zasobu źródła danych.

    > [!TIP]
    > Zalecamy grupowanie zasobów, do których udzielany jest dostęp, w ramach określonej grupy zasobów utworzonej w tym celu.
    >
    > Jeśli nie możesz, upewnij się, że Twój zespół ma uprawnienia do odczytywania dziennika bezpośrednio do zasobów, do których chcesz uzyskać dostęp.
    >

    Aby uzyskać więcej informacji na temat identyfikatorów zasobów, zobacz:

    - [Identyfikatory zasobów z przekazywaniem dzienników](#resource-ids-with-log-forwarding)
    - [Identyfikatory zasobów z kolekcją logstash](#resource-ids-with-logstash-collection)
    - [Identyfikatory zasobów z kolekcją interfejsów API Log Analytics](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>Identyfikatory zasobów z przekazywaniem dzienników

W przypadku zbierania zdarzeń przy użyciu usługi [Common Event format (CEF)](connect-common-event-format.md) [lub dziennika](connect-syslog.md)systemowego przekazywanie dzienników służy do zbierania zdarzeń z wielu systemów źródłowych.

Na przykład po przekierowaniu maszyny wirtualnej CEF lub dziennika systemowego do źródeł wysyłających zdarzenia dziennika systemowego i przekazują je do funkcji wskaźnikowej platformy Azure, identyfikator zasobu maszyny wirtualnej przekazywania dziennika jest przypisywany do wszystkich zdarzeń, które przesyłają dalej.

Jeśli masz wiele zespołów, upewnij się, że masz oddzielne maszyny wirtualne przekazywania do dziennika przetwarzania zdarzeń dla każdego oddzielnego zespołu.

Na przykład oddzielenie maszyn wirtualnych zapewnia, że zdarzenia dziennika systemowego należące do zespołu A są zbierane przy użyciu maszyny wirtualnej modułu zbierającego A.

> [!TIP]
> - W przypadku korzystania z lokalnej maszyny wirtualnej lub innej maszyny wirtualnej w chmurze, takiej jak AWS, jako usługi przesyłania dalej dzienników upewnij się, że ma ona identyfikator zasobu przez implementację [usługi Azure Arc](../azure-arc/servers/overview.md).
> - Aby skalować środowisko maszyny wirtualnej do przekazywania dzienników, rozważ utworzenie [zestawu skalowania maszyn wirtualnych](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) w celu zebrania dzienników CEF i Sylog.


### <a name="resource-ids-with-logstash-collection"></a>Identyfikatory zasobów z kolekcją logstash

Jeśli zbierasz dane przy użyciu [wtyczki danych wyjściowych logstash](connect-logstash.md)platformy Azure, użyj pola **azure_resource_id** , aby skonfigurować moduł zbierający niestandardowe w celu uwzględnienia identyfikatora zasobu w danych wyjściowych.

Jeśli używasz funkcji RBAC dla kontekstu zasobów i chcesz, aby zdarzenia zbierane przez interfejs API były dostępne dla określonych użytkowników, użyj identyfikatora zasobu grupy zasobów [utworzonej dla użytkowników](#explicitly-configure-resource-context-rbac).

Na przykład poniższy kod przedstawia przykładowy plik konfiguracji logstash:

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> Możesz chcieć dodać wiele `output` sekcji, aby odróżnić Tagi zastosowane do różnych zdarzeń.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>Identyfikatory zasobów z kolekcją interfejsów API Log Analytics

Podczas zbierania przy użyciu [interfejsu API modułu zbierającego dane log Analytics](../azure-monitor/logs/data-collector-api.md)można przypisać do zdarzeń z identyfikatorem zasobu przy użyciu nagłówka żądania HTTP [*x-MS-AzureResourceId*](../azure-monitor/logs/data-collector-api.md#request-headers) .

Jeśli używasz funkcji RBAC dla kontekstu zasobów i chcesz, aby zdarzenia zbierane przez interfejs API były dostępne dla określonych użytkowników, użyj identyfikatora zasobu grupy zasobów [utworzonej dla użytkowników](#explicitly-configure-resource-context-rbac).


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [uprawnienia w wskaźniku na platformie Azure](roles.md).