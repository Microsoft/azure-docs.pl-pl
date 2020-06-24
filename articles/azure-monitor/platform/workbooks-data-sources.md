---
title: Azure Monitor skoroszyty ze źródłami danych | Dokumentacja firmy Microsoft
description: Uprość złożone raportowanie ze wstępnie skompilowanymi i niestandardowymi skoroszytami Azure Monitor utworzonymi na podstawie wielu źródeł danych
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d57910ae31d4db9be17b3dc46b5920a925ab4fcf
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84697021"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor skoroszyty ze źródłami danych

Skoroszyty są zgodne z dużą liczbą źródeł danych. Ten artykuł przeprowadzi Cię przez źródła danych, które są obecnie dostępne dla Azure Monitor skoroszytów.

## <a name="logs"></a>Dzienniki

Skoroszyty umożliwiają wykonywanie zapytań względem dzienników z następujących źródeł:

* Dzienniki Azure Monitor (zasoby Application Insights i Log Analytics obszary robocze).
* Dane skoncentrowane na zasobach (dzienniki aktywności)

Autorzy skoroszytu mogą używać zapytań KQL, które przekształcają bazowe dane zasobów, aby wybrać zestaw wyników, który można wizualizować jako tekst, wykresy lub siatki.

![Zrzut ekranu przedstawiający interfejs raportu dzienników skoroszytów](./media/workbooks-overview/logs.png)

Autorzy skoroszytu mogą łatwo wykonywać zapytania dotyczące wielu zasobów, tworząc naprawdę ujednolicone, bogate środowisko raportowania.

## <a name="metrics"></a>Metryki

Zasoby platformy Azure emitują [metryki](data-platform-metrics.md) , do których można uzyskiwać dostęp za pośrednictwem skoroszytów. Dostęp do metryk w skoroszytach można uzyskać za pomocą wyspecjalizowanej kontrolki, która umożliwia określenie zasobów docelowych, odpowiednich metryk i ich agregacji. Te dane można następnie wykreślić na wykresach lub siatkach.

![Zrzut ekranu przedstawiający wykresy metryk skoroszytu dotyczące użycia procesora CPU](./media/workbooks-overview/metrics-graph.png)

![Zrzut ekranu przedstawiający interfejs metryk skoroszytu](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph 

Skoroszyty obsługują zapytania dotyczące zasobów i ich metadanych przy użyciu usługi Azure Resource Graph (ARG). Ta funkcja jest używana głównie do tworzenia niestandardowych zakresów zapytań dla raportów. Zakres zasobów jest wyrażany za pomocą podzestawu KQL, który jest obsługiwany przez argument, który jest często wystarczający dla typowych przypadków użycia.

Aby formant zapytania używał tego źródła danych, Użyj listy rozwijanej Typ zapytania, aby wybrać pozycję Azure Resource Graph i wybrać subskrypcje docelowe. Za pomocą kontrolki zapytanie Dodaj wartość ARG KQL-podzestaw, która wybiera interesujący podzbiór zasobów.


![Zrzut ekranu przedstawiający zapytanie KQL wykresu zasobów platformy Azure](./media/workbooks-overview/azure-resource-graph.png)

## <a name="alerts-preview"></a>Alerty (wersja zapoznawcza)

Skoroszyty umożliwiają użytkownikom wizualizację aktywnych alertów związanych ze swoimi zasobami. Ta funkcja umożliwia tworzenie raportów, które łączą dane powiadomień (alertu) i informacje diagnostyczne (metryki, dzienniki) w jeden raport. Te informacje można również łączyć ze sobą, aby tworzyć zaawansowane raporty, które łączą szczegółowe dane w tych źródłach danych.

Aby formant zapytania używał tego źródła danych, Użyj listy rozwijanej Typ zapytania, aby wybrać alerty i wybrać subskrypcje, grupy zasobów lub zasoby docelowe. Użyj listy rozwijanej filtr alertów, aby wybrać interesujący podzbiór alertów dla potrzeb analitycznych.

![Zrzut ekranu zapytania o alerty](./media/workbooks-overview/alerts.png)

## <a name="workload-health-preview"></a>Kondycja obciążenia (wersja zapoznawcza)

Azure Monitor ma funkcję, która aktywnie monitoruje dostępność i wydajność systemów operacyjnych gościa z systemem Windows lub Linux. Azure Monitor modeluje składniki i ich relacje, kryteria służące do mierzenia kondycji tych składników oraz składniki ostrzegające o wykryciu złej kondycji. Skoroszyty umożliwiają użytkownikom korzystanie z tych informacji w celu tworzenia rozbudowanych raportów interaktywnych.

Aby formant zapytania używał tego źródła danych, Użyj listy rozwijanej **typ zapytania** , aby wybrać opcję kondycja obciążenia i wybrać opcję subskrypcja, Grupa zasobów lub zasoby maszyn wirtualnych. Użyj listy rozwijanej filtr kondycji, aby wybrać interesujący podzbiór zdarzeń związanych z kondycją dla potrzeb analitycznych.

![Zrzut ekranu zapytania o alerty](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Usługa Azure Resource Health 

Skoroszyty obsługują uzyskiwanie informacji o kondycji zasobów platformy Azure i łączenie ich z innymi źródłami danych w celu tworzenia zaawansowanych, interaktywnych raportów o kondycji

Aby formant zapytania używał tego źródła danych, Użyj listy rozwijanej **typ zapytania** , aby wybrać opcję kondycja platformy Azure, a następnie wybierz zasoby docelowe. Użyj listy rozwijanej filtr kondycji, aby wybrać interesujący podzbiór problemów dotyczących zasobów dla potrzeb analitycznych.

![Zrzut ekranu zapytania o alerty](./media/workbooks-overview/resource-health.png)

## <a name="azure-data-explorer-preview"></a>Azure Eksplorator danych (wersja zapoznawcza)

Skoroszyty obsługują teraz wykonywanie zapytań z klastrów [usługi Azure Eksplorator danych](https://docs.microsoft.com/azure/data-explorer/) przy użyciu zaawansowanego języka zapytań [Kusto](https://docs.microsoft.com/azure/kusto/query/index) .   

![Zrzut ekranu przedstawiający okno zapytania Kusto](./media/workbooks-overview/data-explorer.png)

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](workbooks-visualizations.md) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
* [Kontroluj](workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
