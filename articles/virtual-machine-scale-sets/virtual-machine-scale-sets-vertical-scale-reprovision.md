---
title: Skalowanie w pionie zestawów skalowania maszyn wirtualnych platformy Azure
description: Jak skalować w pionie maszynę wirtualną w odpowiedzi na monitorowanie alertów za pomocą Azure Automation
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/18/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: b172f1f7137b53e98384d92c9c709694eaf0b7e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594493"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Automatyczne skalowanie w pionie za pomocą zestawów skalowania maszyn wirtualnych

W tym artykule opisano, jak w pionie skalować [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/) platformy Azure z lub bez ponownego aprowizacji. 

Skalowanie w pionie, nazywane również skalowaniem w *górę* i *w dół*, oznacza zwiększenie lub zmniejszenie rozmiaru maszyny wirtualnej w odpowiedzi na obciążenie. Porównaj to zachowanie przy użyciu [skalowania poziomego](virtual-machine-scale-sets-autoscale-overview.md), nazywanego również *skalą* i *skalą w*, gdzie liczba maszyn wirtualnych jest zmieniana w zależności od obciążenia.

Ponowne Inicjowanie obsługi administracyjnej oznacza usunięcie istniejącej maszyny wirtualnej i zastąpienie jej nową. W przypadku zwiększenia lub zmniejszenia rozmiaru maszyn wirtualnych w zestawie skalowania maszyn wirtualnych, w niektórych przypadkach, chcesz zmienić rozmiar istniejących maszyn wirtualnych i zachować dane, w innych przypadkach należy wdrożyć nowe maszyny wirtualne o nowym rozmiarze. Ten dokument obejmuje oba przypadki.

Skalowanie w pionie może być przydatne w przypadku:

* Usługa wbudowana na maszynach wirtualnych jest w pełni wykorzystywana (na przykład w weekendy). Zmniejszenie rozmiaru maszyny wirtualnej może obniżyć koszty miesięczne.
* Zwiększenie rozmiaru maszyny wirtualnej w celu sprostania większym zapotrzebowaniu bez tworzenia dodatkowych maszyn wirtualnych.

Można skonfigurować skalowanie w pionie na podstawie alertów opartych na metrykach z zestawu skalowania maszyn wirtualnych. Po aktywowaniu alertu wyzwala element webhook, który wyzwala element Runbook, który może skalować konfigurację skalowania w górę lub w dół. Skalowanie w pionie można skonfigurować, wykonując następujące czynności:

1. Utwórz konto Azure Automation przy użyciu funkcji run as.
2. Zaimportuj Azure Automation elementy Runbook skalowania w pionie dla zestawów skalowania maszyn wirtualnych do subskrypcji.
3. Dodaj element webhook do elementu Runbook.
4. Dodaj alert do zestawu skalowania maszyn wirtualnych za pomocą powiadomienia elementu webhook.

> [!NOTE]
> Ze względu na rozmiar pierwszej maszyny wirtualnej rozmiary, do których można skalować, mogą być ograniczone ze względu na dostępność innych rozmiarów z bieżącej maszyny wirtualnej klastra wdrożonych w programie. W opublikowanym elemencie Runbook usługi Automation używanym w tym artykule zajmiemy się tym przypadkiem i skalujemy ją tylko w obrębie poniższych par rozmiaru maszyny wirtualnej. Oznacza to, że Standard_D1v2 maszynie wirtualnej nie można nagle skalować do Standard_G5 lub przeskalować do Basic_A0. Nie są obsługiwane również rozmiary maszyn wirtualnych z ograniczeniami skalowania w górę/w dół. Można wybrać skalowanie między następującymi parami rozmiarów:
> 
> | Składowa pary skalowania rozmiaru maszyny wirtualnej | Członek |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standardowa_A0 |Standardowa_A4 |
> | Standardowa_A5 |Standardowa_A7 |
> | Standard_A8 |Standard_A9 |
> | Standardowa_A10 |Standardowa_A11 |
> | Standardowa_A1_v2 |Standardowa_A8_v2 |
> | Standardowa_A2m_v2 |Standardowa_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standardowa_D1 |Standardowa_D4 |
> | Standardowa_D11 |Standardowa_D14 |
> | Standardowa_DS1 |Standardowa_DS4 |
> | Standardowa_DS11 |Standardowa_DS14 |
> | Standardowa_D1_v2 |Standardowa_D5_v2 |
> | Standardowa_D11_v2 |Standardowa_D14_v2 |
> | Standardowa_DS1_v2 |Standard_DS5_v2 |
> | Standardowa_DS11_v2 |Standardowa_DS14_v2 |
> | Standardowa_D2_v3 |Standard_D64_v3 |
> | Standardowa_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standardowa_E2_v3 |Standardowa_E64_v3 |
> | Standardowa_E2s_v3 |Standardowa_E64s_v3 |
> | Standardowa_F1 |Standardowa_F16 |
> | Standardowa_F1s |Standardowa_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standardowa_G1 |Standard_G5 |
> | Standardowa_GS1 |Standardowa_GS5 |
> | Standardowa_H8 |Standardowa_H16 |
> | Standardowa_H8m |Standardowa_H16m |
> | Standardowa_L4s |Standardowa_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standardowa_NC6 |Standardowa_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standardowa_NV6 |Standardowa_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Tworzenie konta Azure Automation przy użyciu funkcji Uruchom jako
Najpierw należy utworzyć konto Azure Automation, które hostuje elementy Runbook używane do skalowania wystąpień zestawu skalowania maszyn wirtualnych. Ostatnio [Azure Automation](https://azure.microsoft.com/services/automation/) wprowadzono funkcję "Uruchom jako", która umożliwia skonfigurowanie nazwy głównej usługi do automatycznego uruchamiania elementów Runbook w imieniu użytkownika. Aby uzyskać więcej informacji, zobacz:

* [Uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure](../automation/manage-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importuj Azure Automation elementów Runbook skalowania w pionie do subskrypcji

Elementy Runbook, które są konieczne do skalowania w pionie zestawów skalowania maszyn wirtualnych, są już opublikowane w galerii elementów Runbook Azure Automation. Aby zaimportować je do subskrypcji, wykonaj kroki opisane w tym artykule:

* [Galerie elementów Runbook i modułów dla usługi Azure Automation](../automation/automation-runbook-gallery.md)

Wybierz opcję przeglądania galerii z menu elementy Runbook:

![Elementy Runbook do zaimportowania][runbooks]

Wyświetlane są elementy Runbook, które muszą zostać zaimportowane. Wybierz element Runbook w oparciu o to, czy chcesz skalować w pionie przy użyciu lub bez ponownego inicjowania obsługi administracyjnej:

![Galeria elementów Runbook][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Dodawanie elementu webhook do elementu Runbook

Po zaimportowaniu elementów Runbook Dodaj element webhook do elementu Runbook, aby mógł zostać wyzwolony przez alert z zestawu skalowania maszyn wirtualnych. Szczegóły dotyczące tworzenia elementu webhook dla elementu Runbook zostały opisane w tym artykule:

* [Azure Automation elementów webhook](../automation/automation-webhooks.md)

> [!NOTE]
> Upewnij się, że skopiujesz identyfikator URI elementu webhook przed zamknięciem okna dialogowego elementu webhook, ponieważ ten adres będzie potrzebny w następnej sekcji.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Dodawanie alertu do zestawu skalowania maszyn wirtualnych

Poniżej znajduje się skrypt programu PowerShell, który pokazuje, jak dodać alert do zestawu skalowania maszyn wirtualnych. Zapoznaj się z następującym artykułem, aby uzyskać nazwę metryki, na której ma zostać wyzwolony alert: [Azure monitor automatyczne skalowanie metryk](../azure-monitor/autoscale/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Zaleca się skonfigurowanie odpowiedniego przedziału czasu dla alertu, aby uniknąć wyzwalania skalowania w pionie oraz wszelkich powiązanych przerwań usługi. Uwzględnij okno z co najmniej 20-30 min. Należy rozważyć skalowanie w poziomie, jeśli konieczne jest uniknięcie jakichkolwiek przerw w działaniu.
> 
> 

Aby uzyskać więcej informacji na temat tworzenia alertów, zobacz następujące artykuły:

* [Przykłady programu Azure Monitor PowerShell](../azure-monitor/powershell-samples.md)
* [Azure Monitor Międzyplatformowe przykłady interfejsu wiersza polecenia](../azure-monitor/cli-samples.md)

## <a name="summary"></a>Podsumowanie

W tym artykule pokazano proste przykładowe skalowanie w pionie. Za pomocą tych bloków konstrukcyjnych — konta usługi Automation, elementów Runbook, elementu webhook, alertów — można połączyć wiele różnych zdarzeń z dostosowanym zestawem akcji.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
