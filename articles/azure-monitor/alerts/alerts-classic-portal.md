---
title: Tworzenie klasycznych alertów metryk i zarządzanie nimi przy użyciu Azure Monitor
description: Dowiedz się, jak używać Azure Portal, interfejsu wiersza polecenia lub programu PowerShell do tworzenia i wyświetlania reguł alertów dotyczących częstotliwości klasycznych oraz zarządzania nimi.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/18/2018
ms.openlocfilehash: 015a84ab6f81875407f460c4a45423f3f5e22b7f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620973"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Twórz i wyświetlaj klasyczne alerty metryk i zarządzaj nimi za pomocą Azure Monitor

> [!WARNING]
> W tym artykule opisano sposób tworzenia starszych klasycznych alertów dotyczących metryk. Azure Monitor teraz obsługuje [nowsze alerty metryk niemal w czasie rzeczywistym i nowe środowisko alertów](../platform/alerts-overview.md). Alerty klasyczne są [wycofywane](../platform/monitoring-classic-retirement.md) dla użytkowników chmury publicznej, mimo że nadal są ograniczone dla zasobów, które jeszcze nie obsługują nowych alertów.
>

Klasyczne alerty metryk w Azure Monitor umożliwiają otrzymywanie powiadomień, gdy jedna z metryk przekracza próg. Klasyczne alerty metryk są starszą funkcją, która umożliwia generowanie alertów tylko w przypadku metryk niewymiarowych. Istnieje już nowa funkcja, nazywana alertami metryk, która oferuje ulepszone funkcje w porównaniu z klasycznymi alertami metryk. Więcej informacji o nowych funkcjach alertów metryk można znaleźć w temacie [Omówienie alertów dotyczących metryk](../platform/alerts-metric-overview.md). W tym artykule opisano sposób tworzenia i wyświetlania klasycznych reguł alertów dotyczących metryk oraz zarządzania nimi za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure i programu PowerShell.

## <a name="with-azure-portal"></a>Z Azure Portal

1. W [portalu](https://portal.azure.com/)Znajdź zasób, który chcesz monitorować, a następnie wybierz go.

2. W sekcji **monitorowanie** wybierz pozycję **alerty (klasyczne)**. Tekst i ikona mogą się nieco różnić w zależności od różnych zasobów. Jeśli w tym miejscu nie znajdziesz **alertów (klasyczny)** , możesz go znaleźć w **alertach** lub **regułach alertów**.

    ![Monitorowanie](media/alerts-classic-portal/AlertRulesButton.png)

3. Wybierz polecenie **Dodaj alert metryki (klasyczny)** , a następnie wypełnij pola.

    ![Dodawanie alertu](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Nadaj nazwę** regule alertu. Następnie wybierz **Opis**, który również pojawia się w wiadomości e-mail z powiadomieniem.

5. Wybierz **metrykę** , którą chcesz monitorować. Następnie wybierz **warunek** i wartość **progu** dla metryki. Należy również wybrać **okres** , przez jaki reguła metryki musi być spełniona przed wyzwalaczem alertu. Na przykład jeśli używasz okresu "w ciągu ostatnich 5 minut", a alert wyszukuje procesor powyżej 80%, alert jest wyzwalany w przypadku, gdy procesor CPU był spójny powyżej 80% przez 5 minut. Po wystąpieniu pierwszego wyzwalacza jest wyzwalane ponownie, gdy procesor CPU pozostaje poniżej 80% przez 5 minut. Pomiar metryki procesora CPU występuje co minutę.

6. Wybierz **właścicieli poczty e-mail...** , jeśli chcesz, aby administratorzy i współadministratorzy otrzymywali powiadomienia e-mail o alertach.

7. Jeśli chcesz wysyłać powiadomienia do dodatkowych adresów e-mail po uruchomieniu alertu, Dodaj je w polu dodatkowe adresy **E-mail administratora** . Oddziel wiele wiadomości e-mail średnikami, w następującym formacie: *e-mail \@ contoso. com; email2 \@ contoso.com*

8. Wprowadź prawidłowy identyfikator URI w polu **elementu webhook** , jeśli chcesz, aby był on wywoływany po uruchomieniu alertu.

9. Jeśli używasz Azure Automation, możesz wybrać element Runbook, który ma być uruchamiany po uruchomieniu alertu.

10. Wybierz pozycję **OK**, aby utworzyć alert.

W ciągu kilku minut alert jest aktywny i wyzwalacze opisane wcześniej.

Po utworzeniu alertu można go wybrać i wykonać jedną z następujących czynności:

* Wyświetl wykres, który pokazuje próg metryki i rzeczywiste wartości z poprzedniego dnia.
* Edytuj lub Usuń.
* **Wyłącz** lub **Włącz** tę opcję, jeśli chcesz tymczasowo zatrzymać lub wznowić otrzymywanie powiadomień dla tego alertu.

## <a name="with-azure-cli"></a>Z interfejsem wiersza polecenia platformy Azure

Poprzednie sekcje opisują sposób tworzenia, wyświetlania i zarządzania regułami alertów dotyczących metryk przy użyciu Azure Portal. W tej sekcji opisano, jak wykonać to samo przy użyciu wieloplatformowego [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli). Najszybszym sposobem na rozpoczęcie korzystania z interfejsu wiersza polecenia platformy Azure jest [Azure Cloud Shell](../../cloud-shell/overview.md).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Pobierz wszystkie reguły alertu dotyczącego klasycznej metryki w grupie zasobów

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Zobacz szczegóły konkretnej reguły alertu dotyczącego klasycznej metryki

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Tworzenie reguły klasycznego alertu dotyczącego metryki

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Usuwanie reguły klasycznego alertu dotyczącego metryki

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Z programem PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tych sekcjach pokazano, jak za pomocą poleceń programu PowerShell tworzyć i wyświetlać alerty metryk klasycznej oraz zarządzać nimi. Przykłady w tym artykule ilustrują, jak można użyć poleceń cmdlet Azure Monitor dla klasycznych alertów dotyczących metryk.

1. Jeśli jeszcze tego nie zrobiono, skonfiguruj program PowerShell do uruchamiania na komputerze. Aby uzyskać więcej informacji, zobacz [jak zainstalować i skonfigurować program PowerShell](/powershell/azure/). Możesz również przejrzeć całą listę Azure Monitor poleceń cmdlet programu PowerShell w [Azure monitor polecenia cmdlet (Insights)](/powershell/module/az.applicationinsights).

2. Najpierw Zaloguj się do subskrypcji platformy Azure.

    ```powershell
    Connect-AzAccount
    ```

3. Zobaczysz ekran logowania. Po zalogowaniu się na koncie zostanie wyświetlony TenantID i domyślny identyfikator subskrypcji. Wszystkie polecenia cmdlet platformy Azure działają w kontekście domyślnej subskrypcji. Aby wyświetlić listę subskrypcji, do których masz dostęp, użyj następującego polecenia:

    ```powershell
    Get-AzSubscription
    ```

4. Aby zmienić kontekst roboczy na inną subskrypcję, użyj następującego polecenia:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Wszystkie klasyczne reguły alertów metryk można pobrać w grupie zasobów:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Możesz wyświetlić szczegóły klasycznej reguły alertu dotyczącego metryki

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Można pobrać wszystkie reguły alertów ustawione dla zasobu docelowego. Na przykład wszystkie reguły alertów ustawione na maszynie wirtualnej.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Nie można już tworzyć klasycznych reguł alertów za pomocą programu PowerShell. Aby utworzyć regułę alertu, należy użyć nowego polecenia ["Add-AzMetricAlertRule"](/powershell/module/az.monitor/add-azmetricalertrule) .

## <a name="next-steps"></a>Następne kroki

- [Utwórz klasyczny alert dotyczący metryki z szablonem Menedżer zasobów](../platform/alerts-enable-template.md).
- [Zawiadom klasyczny alert dotyczący metryki systemu spoza platformy Azure przy użyciu elementu webhook](../platform/alerts-webhooks.md).

