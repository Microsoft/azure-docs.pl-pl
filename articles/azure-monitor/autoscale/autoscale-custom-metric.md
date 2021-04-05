---
title: Skalowanie automatyczne na platformie Azure przy użyciu metryki niestandardowej
description: Dowiedz się, jak skalować zasób według metryki niestandardowej na platformie Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 8e744e6a91eb6fbe23a6b45f95c39b1acfdcb61f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100617710"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Wprowadzenie do automatycznego skalowania według metryki niestandardowej na platformie Azure
W tym artykule opisano sposób skalowania zasobu według metryki niestandardowej w Azure Portal.

Automatyczne skalowanie Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service-Web Apps](https://azure.microsoft.com/services/app-service/web/), [klastra Eksplorator danych platformy Azure](https://azure.microsoft.com/services/data-explorer/) ,   
Usługi środowisko usługi integracji i [API Management](../../api-management/api-management-key-concepts.md).

## <a name="lets-get-started"></a>Umożliwia rozpoczęcie pracy
W tym artykule przyjęto założenie, że masz aplikację sieci Web z skonfigurowanym usługą Application Insights. Jeśli jeszcze tego nie zrobiono, możesz [skonfigurować Application Insights dla witryny sieci web ASP.NET][1]

- Otwórz [Azure Portal][2]
- Kliknij ikonę Azure Monitor w okienku nawigacji po lewej stronie.
  ![Azure Monitor uruchamiania][3]
- Kliknij ustawienie skalowania automatycznego, aby wyświetlić wszystkie zasoby, dla których ma zastosowanie Autoskalowanie, wraz z bieżącym stanem automatycznego skalowania ![ odnajdywanie automatyczne skalowanie w usłudze Azure monitor][4]
- Otwórz blok "Skalowanie automatyczne" w Azure Monitor i wybierz zasób, który chcesz skalować
  > Uwaga: poniższe kroki używają planu usługi App Service skojarzonego z aplikacją sieci Web, dla której skonfigurowano usługę App Insights.
- W bloku ustawienia skalowania zasobu Zwróć uwagę, że bieżąca liczba wystąpień to 1. Kliknij pozycję "Włącz Skalowanie automatyczne".
  ![Ustawienie skalowania dla nowej aplikacji sieci Web][5]
- Podaj nazwę ustawienia skalowania i kliknij pozycję "Dodaj regułę". Zwróć uwagę na Opcje reguły skalowania, które są otwierane w okienku kontekstowym po prawej stronie. Domyślnie ustawia opcję skalowania liczby wystąpień o 1, jeśli wartość procentowa procesora CPU zasobu przekracza 70%. Zmień źródło metryki w górnej części na "Application Insights", wybierz zasób usługi App Insights na liście rozwijanej "Resource", a następnie wybierz metrykę niestandardową, na podstawie której chcesz skalować.
  ![Skalowanie według metryki niestandardowej][6]
- Podobnie jak powyżej, Dodaj regułę skalowania, która będzie skalowana w poziomie i zmniejsz liczbę skalowania o 1, jeśli Metryka niestandardowa jest niższa od wartości progowej.
  ![Skalowanie w oparciu o procesor CPU][7]
- Ustaw limity wystąpień. Na przykład jeśli chcesz skalować między 2-5 wystąpieniami w zależności od niestandardowych fluktuacji metryk, ustaw wartość "minimum" na "2", "maksimum" na "5" i "domyślne" na "2"
  > Uwaga: w przypadku wystąpienia problemu z odczytem metryk zasobów i aktualna pojemność jest niższa niż domyślna pojemność, aby zapewnić dostępność zasobu, Skalowanie automatyczne zostanie przeskalowane do wartości domyślnej. Jeśli bieżąca pojemność jest już wyższa niż domyślna pojemność, Skalowanie automatyczne nie będzie skalowane.
- Kliknij pozycję "Zapisz".

Gratulacje. Pomyślnie utworzono ustawienia skalowania w celu automatycznego skalowania aplikacji sieci Web na podstawie metryki niestandardowej.

> Uwaga: te same kroki mają zastosowanie, aby rozpocząć pracę z rolą usługi VMSS lub w chmurze.

<!--Reference-->
[1]: ../app/asp-net.md
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png
