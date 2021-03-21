---
title: Integracja Log Analytics z programami Power BI i Excel
description: Jak wysłać wyniki z Log Analytics do Power BI
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: 894b87bfa17a59db20b424199a4021ad75b182fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041063"
---
# <a name="log-analytics-integration-with-power-bi"></a>Log Analytics integrację z usługą Power BI

Ten artykuł koncentruje się na sposobach tworzenia źródła danych z Log Analytics w usłudze Microsoft Power BI w celu utworzenia bardziej atrakcyjnych wizualnie raportów i pulpitów nawigacyjnych. 

## <a name="background"></a>Tło 

Dzienniki Azure Monitor to platforma, która oferuje kompleksowe rozwiązanie do pozyskiwania dzienników. [Azure Monitor Log Analytics](../platform/data-platform.md#) jest interfejsem do wykonywania zapytań dotyczących tych dzienników. Aby uzyskać więcej informacji na temat całej Azure Monitor platformy danych, w tym Log Analytics, zobacz [platformę danych Azure monitor](../data-platform.md). 

Microsoft Power BI to platforma wizualizacji danych firmy Microsoft. Aby uzyskać więcej informacji na temat rozpoczynania pracy, zobacz [stronę główną Power BI](https://powerbi.microsoft.com/). 


Ogólnie rzecz biorąc, możesz korzystać z bezpłatnych Power BI funkcji do integrowania i tworzenia atrakcyjnych wizualnie raportów i pulpitów nawigacyjnych.

Bardziej zaawansowane funkcje mogą wymagać zakupu konta Power BI Pro lub Premium. Są one następujące: 
 - Udostępnianie pracy 
 - zaplanowane odświeżanie
 - Aplikacje usługi Power BI 
 - dataflows i odświeżanie przyrostowe 

Aby uzyskać więcej informacji, zobacz temat [Dowiedz się więcej o Power BI cenach i funkcji](https://powerbi.microsoft.com/pricing/) 

## <a name="integrating-queries"></a>Integrowanie zapytań  

W Power BI jest stosowany [język zapytań M](/powerquery-m/power-query-m-language-specification/) jako główny język zapytań. 

Zapytania Log Analytics można eksportować do M i używać bezpośrednio Power BI. Po pomyślnym wykonaniu zapytania wybierz pozycję **Eksportuj do Power BI (kwerenda M)** z przycisku **Eksportuj** na pasku narzędzi log Analytics najwyższego poziomu.


:::image type="content" source="media/log-powerbi/export-query2.png" alt-text="Log Analytics zapytanie zawierające menu opcji eksportu" border="true":::

Log Analytics tworzy plik txt zawierający kod M, który może być używany bezpośrednio w Power BI.

## <a name="connecting-your-logs-to-a-dataset"></a>Łączenie dzienników z zestawem danych 

Zestaw danych Power BI jest źródłem danych gotowych do raportowania i wizualizacji. Aby połączyć Log Analytics kwerendę z zestawem danych, skopiuj kod M wyeksportowany z Log Analytics do pustego zapytania w Power BI. 

Aby uzyskać więcej informacji, zobacz [Omówienie zestawów danych Power BI](/power-bi/service-datasets-understand/). 

## <a name="collect-data-with-power-bi-dataflows"></a>Zbieranie danych za pomocą Power BI dataflows 

Power BI dataflows umożliwiają również zbieranie i przechowywanie danych. Aby uzyskać więcej informacji, zobacz [Power BI dataflows](/power-bi/service-dataflows-overview).

Przepływu danych to typ "ETL" w chmurze, który ułatwia zbieranie i przygotowywanie danych. Zestaw danych to "model" zaprojektowany w celu ułatwienia łączenia różnych jednostek i modelowania ich w zależności od potrzeb.

## <a name="incremental-refresh"></a>Odświeżanie przyrostowe 

Elementy Power BI zestawy danych i Power BI Dataflow mają opcję odświeżania przyrostowego. Power BI dataflows i Power BI DataSets obsługują tę funkcję, ale trzeba Power BI Premium jej używać.  


Odświeżanie przyrostowe uruchamia małe zapytania i aktualizuje mniejsze ilości danych na przebieg, a nie pobiera ponownie wszystkich danych i ponownie po uruchomieniu zapytania. Istnieje możliwość zapisania dużej ilości danych, ale dodanie nowego przyrostu danych przy każdym uruchomieniu zapytania. Takie zachowanie jest idealne do uruchamiania raportów.

Power BI odświeżanie przyrostowe bazuje na istnieniu *daty/godziny* zapisanej w zestawie wyników. Przed skonfigurowaniem odświeżania przyrostowego upewnij się, że zestaw wyników zapytania Log Analytics zawiera co najmniej jedną *wartość daty i godziny* . 

Aby dowiedzieć się więcej i jak skonfigurować odświeżanie przyrostowe, zobacz [Power BI zestawy danych i odświeżanie przyrostowe](/power-bi/service-premium-incremental-refresh) oraz [Power BI przepływów danych i odświeżanie przyrostowe](/power-bi/service-dataflows-incremental-refresh).

## <a name="reports-and-dashboards"></a>Raporty i pulpity nawigacyjne

Po wysłaniu danych do Power BI można nadal używać Power BI do tworzenia raportów i pulpitów nawigacyjnych.

Aby uzyskać więcej informacji, zobacz [ten przewodnik dotyczący tworzenia pierwszego modelu Power BI i raportu](/learn/modules/build-your-first-power-bi-report/).  

## <a name="excel-integration"></a>Integracja z programem Excel

W celu zintegrowania z arkuszem kalkulacyjnym programu Excel można użyć tej samej integracji M, która jest używana w Power BI. Aby uzyskać więcej informacji, zobacz ten przewodnik dotyczący [sposobu integracji z programem Excel](https://support.microsoft.com/office/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) , a następnie wklejenia zapytania M wyeksportowanego z log Analytics.

Dodatkowe informacje można znaleźć w temacie [integracja log Analytics i programu Excel](log-excel.md)

## <a name="next-steps"></a>Następne kroki

Rozpocznij pracę z [zapytaniami log Analytics](./log-query-overview.md).