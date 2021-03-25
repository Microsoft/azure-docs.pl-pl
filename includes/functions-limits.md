---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 91f72117fdbcdbeda1d906a9760243e66404920c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104667"
---
| Zasób |[Plan Zużycie](../articles/azure-functions/consumption-plan.md)|[Plan Premium](../articles/azure-functions/functions-premium-plan.md)|[Dedykowany plan](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|Domyślny [limit czasu](../articles/azure-functions/functions-scale.md#timeout) (w min) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|Maksymalny [czas trwania](../articles/azure-functions/functions-scale.md#timeout) (w min) |10 | niepowiązane<sup>7</sup> | niepowiązane<sup>2</sup> | Unbounded | Unbounded |
| Maksymalna liczba połączeń wychodzących (na wystąpienie) | 600 aktywne (łącznie 1200) | Unbounded | Unbounded | Unbounded | Unbounded |
| Maksymalny rozmiar żądania (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Zależy od klastra |
| Maksymalna długość ciągu zapytania<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Zależy od klastra |
| Maksymalna długość adresu URL żądania<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Zależy od klastra |
|[ACU](../articles/virtual-machines/acu.md) na wystąpienie | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [Cennik AKS](https://azure.microsoft.com/pricing/details/container-service/) |
| Maksymalna ilość pamięci (GB na wystąpienie) | 1.5 | 3,5-14 | 1,75-14 | 3,5 – 14 | Obsługiwany jest dowolny węzeł |
| Maksymalna liczba wystąpień | 200 | 100<sup>9</sup> | zależy od jednostki SKU<sup>10</sup> | 100<sup>10</sup> | Zależy od klastra |   
| Aplikacje funkcji na plan |100 |100 |niepowiązane<sup>4</sup> | Unbounded | Unbounded |
| [Plany usługi App Service](../articles/app-service/overview-hosting-plans.md) | 100 na [region](https://azure.microsoft.com/global-infrastructure/regions/) |100 na grupę zasobów |100 na grupę zasobów | - | - |
| Magazyn<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | n/d |
| Domeny niestandardowe na aplikację</a> |500<sup>6</sup> |500 |500 | 500 | n/d |
| [Obsługa protokołu SSL](../articles/app-service/configure-ssl-bindings.md) domeny niestandardowej |uwzględniono niepowiązane połączenie SNI SSL | uwzględniono niepowiązane SNI SSL i 1 Połączenie SSL z adresu IP połączeń |uwzględniono niepowiązane SNI SSL i 1 Połączenie SSL z adresu IP połączeń | uwzględniono niepowiązane SNI SSL i 1 Połączenie SSL z adresu IP połączeń | n/d |

<sup>1</sup> domyślnie przekroczenie limitu czasu dla funkcji 1. x środowiska uruchomieniowego w planie App Service jest niepowiązane.  
<sup>2</sup> wymaga, aby plan App Service był ustawiony na wartość [zawsze włączone](../articles/azure-functions/dedicated-plan.md#always-on). Płatność według [stawek](https://azure.microsoft.com/pricing/details/app-service/)standardowych.  
<sup>3</sup> te limity są [ustawiane na hoście](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> rzeczywista liczba aplikacji funkcji, które można hostować, zależy od aktywności aplikacji, rozmiaru wystąpień maszyn i odpowiedniego wykorzystania zasobów.  
<sup>5</sup> limit magazynowania to całkowity rozmiar zawartości w magazynie tymczasowym dla wszystkich aplikacji w ramach tego samego planu App Service. W planie użycia Azure Files tymczasowego magazynu.  
<sup>6</sup> gdy aplikacja funkcji jest hostowana w [planie zużycia](../articles/azure-functions/consumption-plan.md), obsługiwana jest tylko opcja CNAME. W przypadku aplikacji funkcji w [planie Premium](../articles/azure-functions/functions-premium-plan.md) lub w [planie App Service](../articles/azure-functions/dedicated-plan.md)można zmapować domenę niestandardową przy użyciu rekordu CNAME lub a.  
<sup>7</sup> gwarantujemy do 60 minut.  
<sup>8</sup> procesów roboczych to role obsługujące aplikacje klienta. Procesy robocze są dostępne w trzech stałych rozmiarach: jeden vCPU/3,5 GB pamięci RAM; Dwa vCPU/7 GB pamięci RAM; Cztery vCPU/14 GB pamięci RAM.   
<sup>9</sup> w przypadku uruchamiania w systemie Linux w planie Premium masz obecnie ograniczenie do 20 wystąpień.  
<sup>10</sup> Aby uzyskać szczegółowe informacje, zobacz [limity App Service](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits) .