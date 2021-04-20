---
title: Resource Manager przykładów szablonów dla szczegółowych informacji SQL
description: Przykładowe Azure Resource Manager szablonów do wdrażania i konfigurowania szczegółowych informacji SQL.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730532"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>Resource Manager przykładów szablonów dla szczegółowych informacji SQL
Ten artykuł zawiera przykładowe [szablony Azure Resource Manager, które](../../azure-resource-manager/templates/template-syntax.md) umożliwiają korzystanie z usługi SQL Insights w celu monitorowania bazy danych SQL uruchomionej na platformie Azure.  Szczegółowe informacje na temat oferty i wersji programu [SQL,](sql-insights-overview.md) które obsługujemy, można znaleźć w dokumentacji usługi SQL Insights. Każdy przykład zawiera plik szablonu i plik parametrów z przykładami wartości, które należy podać do szablonu.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>Tworzenie profilu monitorowania usługi SQL Insights
Poniższy przykład tworzy profil monitorowania usługi SQL Insights, który obejmuje dane monitorowania SQL do zbierania, częstotliwość zbierania danych i określa obszar roboczy, do którego będą wysyłane dane.


### <a name="template-file"></a>Plik szablonu

Wyświetl plik [szablonu w usłudze Git Hub.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate)

### <a name="parameter-file"></a>Plik parametrów

Wyświetl plik [parametrów w usłudze Git Hub.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json)


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>Dodawanie monitorowania maszyny wirtualnej do profilu monitorowania usługi SQL Insights
Po utworzeniu profilu monitorowania należy przydzielić maszyny wirtualne platformy Azure, które zostaną skonfigurowane do zdalnego zbierania danych z zasobów SQL, które określisz w konfiguracji dla tej maszyny wirtualnej.  Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą włączania funkcji SQL Insights.

Poniższy przykład umożliwia skonfigurowanie monitorowania maszyny wirtualnej w celu zbierania danych z określonych zasobów SQL.


### <a name="template-file"></a>Plik szablonu

Wyświetl plik [szablonu w usłudze Git Hub.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate)

### <a name="parameter-file"></a>Plik parametrów

Wyświetl plik [parametrów w usłudze Git Hub.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json)


## <a name="create-an-alert-rule-for-sql-insights"></a>Tworzenie reguły alertu dla usługi SQL Insights
Poniższy przykład tworzy regułę alertu, która będzie obejmować zasoby SQL w zakresie określonego profilu monitorowania.  Ta reguła alertu będzie wyświetlana w interfejsie użytkownika usługi SQL Insights w panelu kontekstowym interfejsu użytkownika alertów.  

Plik parametrów zawiera wartości z jednego z szablonów alertów zapewnianych przez nas w szczegółowych informacjach SQL. Można go zmodyfikować w celu ostrzegania o innych danych zbieranych dla języka SQL.  Szablon nie określa grupy akcji dla reguły alertu.


#### <a name="template-file"></a>Plik szablonu

Wyświetl plik [szablonu w usłudze Git Hub.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate)

### <a name="parameter-file"></a>Plik parametrów

Wyświetl plik [parametrów w usłudze Git Hub.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json)





## <a name="next-steps"></a>Następne kroki

* [Pobierz inne przykładowe szablony dla Azure Monitor](../resource-manager-samples.md).
* [Dowiedz się więcej na temat usługi SQL Insights.](sql-insights-overview.md)
