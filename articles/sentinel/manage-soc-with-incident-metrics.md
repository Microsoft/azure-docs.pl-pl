---
title: Lepsza obsługa usługi SOC przy użyciu metryk zdarzeń na platformie Azure — wskaźnikowa | Microsoft Docs
description: Skorzystaj z informacji podanych na ekranie metryk zdarzeń centrów danych platformy Azure i skoroszycie, aby ułatwić zarządzanie centrum operacji zabezpieczeń (SOC).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 9d8d0fc46a463bda31595988d807854ef146d333
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88761728"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>Lepsze zarządzanie centrum operacji zabezpieczeń przy użyciu metryk zdarzeń

> [!IMPORTANT]
> Funkcje metryk zdarzeń są obecnie dostępne w publicznej wersji zapoznawczej.
> Te funkcje są udostępniane bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jako Menedżer ds. zabezpieczeń (SOC), musisz mieć w wykorzystaniu ogólne metryki wydajności i miary, aby ocenić wydajność zespołu. W miarę upływu czasu można zobaczyć operacje na zdarzeniach według wielu różnych kryteriów, takich jak ważność, MITRE taktykę, średni czas do rozwiązania, średni czas rozpoznawania i inne. Wskaźnik na platformie Azure daje teraz dostęp do tych danych za pomocą nowej tabeli **SecurityIncident** i schematu w log Analytics oraz w załączonym skoroszycie **wydajności operacji związanych z zabezpieczeniami** . Będzie można wizualizować wydajność zespołu w miarę upływu czasu i korzystać z tego wglądu w celu zwiększenia wydajności. Możesz również napisać własne zapytania KQL i używać ich względem tabeli zdarzeń, aby utworzyć dostosowane skoroszyty zgodne z określonymi potrzebami inspekcji i kluczami KPI.

## <a name="use-the-security-incidents-table"></a>Korzystanie z tabeli zdarzeń zabezpieczeń

Tabela **SecurityIncident** jest wbudowana w wskaźnik platformy Azure. Znajdziesz go z innymi tabelami w kolekcji **SecurityInsights** w obszarze **dzienniki**. Można wysyłać zapytania do innych tabel w Log Analytics.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="Tabela zdarzeń zabezpieczeń":::

Za każdym razem, gdy tworzysz lub aktualizujesz zdarzenie, do tabeli zostanie dodany nowy wpis dziennika. Pozwala to na śledzenie zmian wprowadzonych w zdarzeniach i pozwala na jeszcze bardziej zaawansowane metryki SOC, ale należy to wiedzieć o tym podczas konstruowania zapytań dla tej tabeli, ponieważ może być konieczne usunięcie zduplikowanych wpisów dla zdarzenia (zależnie od dokładnego zapytania, które jest uruchomione). 

Jeśli na przykład chcesz zwrócić listę wszystkich incydentów posortowanych według numeru zdarzenia, ale potrzebujesz tylko zwrócić najnowszy dziennik na zdarzenie, możesz to zrobić za pomocą [operatora podsumowania](https://docs.microsoft.com/azure/data-explorer/kusto/query/summarizeoperator) KQL z `arg_max()` [funkcją agregacji](https://docs.microsoft.com/azure/data-explorer/kusto/query/arg-max-aggfunction):


```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>Więcej przykładowych zapytań

Średni czas zamykania:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

Średni czas do Klasyfikacja:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>Skoroszyt wydajności operacji zabezpieczeń

Aby uzupełnić tabelę **SecurityIncidents** , udostępniamy szablon skoroszytu **wydajności operacji zabezpieczeń** , który służy do monitorowania operacji SOC. Skoroszyt zawiera następujące metryki: 
- Zdarzenie utworzone w czasie 
- Zdarzenia utworzone przez zamknięcie klasyfikacji, ważności, właściciela i stanu 
- Średni czas do Klasyfikacja 
- Średni czas zamykania 
- Zdarzenia utworzone według ważności, właściciela, stanu, produktu i taktykę w czasie 
- Czas do Klasyfikacja percentylów 
- Czas zamykania percentylów 
- Średni czas do Klasyfikacja na właściciela 
- Ostatnie działania 
- Ostatnio zamykające klasyfikacje  

Ten nowy szablon skoroszytu można znaleźć, wybierając **skoroszyty** z menu nawigacyjnego wskaźnikowego platformy Azure i wybierając kartę **Szablony** . Wybierz opcję **wydajność operacji zabezpieczeń** z galerii, a następnie kliknij jeden z przycisków **Wyświetl zapisany skoroszyt** i **Wyświetl szablon** .

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="Tabela zdarzeń zabezpieczeń":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="Tabela zdarzeń zabezpieczeń":::

Możesz użyć szablonu, aby utworzyć własne niestandardowe skoroszyty dostosowane do konkretnych potrzeb.

## <a name="securityincidents-schema"></a>Schemat SecurityIncidents

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę z systemem Azure — wskaźnikiem, potrzebna jest subskrypcja do Microsoft Azure. Jeśli nie masz subskrypcji, możesz zarejestrować się, aby uzyskać dostęp do [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
- Dowiedz się, jak dołączać [dane do usługi Azure wskaźnikowej](quickstart-onboard.md)i [uzyskiwać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
