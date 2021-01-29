---
title: 'Szybki Start: Tworzenie konta usługi Automation — szablon platformy Azure'
titleSuffix: Azure Automation
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia konta usługi Automation przy użyciu szablonu Azure Resource Manager.
services: automation
Customer intent: I want to create an Automation account by using an Azure Resource Manager template so that I can automate processes with runbooks.
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: magoedte
ms.custom: mvc,subject-armqs
ms.openlocfilehash: d0582876a08b433a10106b97179ab603d4ec5762
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050672"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Szybki Start: Tworzenie konta usługi Automation przy użyciu szablonu ARM

Azure Automation oferuje chmurową usługę automatyzacji i konfiguracji, która obsługuje spójne zarządzanie w środowiskach platformy Azure i innych niż platformy Azure. W tym przewodniku szybki start pokazano, jak wdrożyć szablon Azure Resource Manager (szablon ARM), który tworzy konto usługi Automation. Użycie szablonu ARM zajmuje mniej kroków w porównaniu z innymi metodami wdrażania.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Ten przykładowy szablon wykonuje następujące czynności:

* Automatyzuje tworzenie obszaru roboczego Log Analytics Azure Monitor.
* Automatyzuje tworzenie konta Azure Automation.
* Łączy konto usługi Automation z obszarem roboczym Log Analytics.
* Dodaje Przykładowe elementy Runbook automatyzacji do konta.

>[!NOTE]
>Tworzenie konta Uruchom jako usługi Automation nie jest obsługiwane w przypadku korzystania z szablonu ARM. Aby ręcznie utworzyć konto Uruchom jako w portalu lub przy użyciu programu PowerShell, zobacz [Tworzenie konta Uruchom jako](create-run-as-account.md).

Po wykonaniu tych kroków należy [skonfigurować ustawienia diagnostyczne](automation-manage-send-joblogs-log-analytics.md) dla konta usługi Automation, aby wysyłać strumienie stanu zadań elementu Runbook i zadań do połączonego obszaru roboczego log Analytics.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>Wersje interfejsu API

W poniższej tabeli wymieniono wersje interfejsu API dla zasobów używanych w tym przykładzie.

| Zasób | Typ zasobu | Wersja interfejsu API |
|:---|:---|:---|
| [Workspace](/azure/templates/microsoft.operationalinsights/workspaces) | obszary robocze | 2020-03-01 — wersja zapoznawcza |
| [Konto usługi Automation](/azure/templates/microsoft.automation/automationaccounts) | automatyzacja | 2020-01-13 — wersja zapoznawcza |
| [Połączone usługi obszaru roboczego](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | obszary robocze | 2020-03-01 — wersja zapoznawcza |

### <a name="before-you-use-the-template"></a>Przed użyciem szablonu

Szablon parametrów JSON jest skonfigurowany do określenia:

* Nazwa obszaru roboczego.
* Region, w którym ma zostać utworzony obszar roboczy.
* Nazwa konta usługi Automation.
* Region, w którym ma zostać utworzone konto usługi Automation.

Następujące parametry w szablonie mają ustawioną wartość domyślną Log Analytics obszarze roboczym:

* Domyślna *jednostka SKU* to warstwa cenowa za GB wydaną w modelu cen z kwietnia 2018.
* wartość domyślna *przechowywania* danych to 30 dni.

>[!WARNING]
>Jeśli chcesz utworzyć lub skonfigurować obszar roboczy Log Analytics w ramach subskrypcji, która została wybrana w modelu cen z kwietnia 2018, jedyną prawidłową Log Analytics warstwą cenową jest *PerGB2018*.
>

Szablon JSON określa wartość domyślną dla innych parametrów, które mogą być używane jako Standardowa konfiguracja w danym środowisku. Szablon można przechowywać na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonego w organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md).

Jeśli dopiero zaczynasz Azure Automation i Azure Monitor, ważne jest zapoznanie się z poniższymi szczegółami konfiguracji. Mogą one pomóc uniknąć błędów podczas próby utworzenia, skonfigurowania i użycia obszaru roboczego Log Analytics połączonego z nowym kontem usługi Automation.

* Zapoznaj się z [dodatkowymi szczegółami](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace) , aby w pełni zrozumieć opcje konfiguracji obszaru roboczego, takie jak tryb kontroli dostępu, warstwa cenowa, przechowywanie i poziom rezerwacji pojemności.

* Przejrzyj [mapowania obszaru roboczego](how-to/region-mappings.md) , aby określić Obsługiwane regiony w tekście lub w pliku parametrów. Tylko niektóre regiony są obsługiwane na potrzeby łączenia obszaru roboczego Log Analytics i konta usługi Automation w ramach subskrypcji.

* Jeśli dopiero zaczynasz korzystać z dzienników Azure Monitor i nie wdrożono już obszaru roboczego, zapoznaj się z tematem [wskazówki dotyczące projektowania obszaru roboczego](../azure-monitor/platform/design-logs-deployment.md). Pomożemy Ci poznać kontrolę dostępu i zrozumieć strategie implementacji projektu, które zalecamy dla Twojej organizacji.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy konto Azure Automation, Log Analytics obszar roboczy i łączy konto usługi Automation z obszarem roboczym.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Wprowadź wartości.

3. Wdrożenie może potrwać kilka minut. Po zakończeniu dane wyjściowe są podobne do następujących:

    ![Przykładowy wynik po zakończeniu wdrażania](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W Azure Portal Otwórz właśnie utworzone konto usługi Automation. 

3. W okienku po lewej stronie wybierz pozycję **elementy Runbook**. Na stronie **elementy Runbook** są wyświetlane trzy elementy Runbook samouczka utworzone przy użyciu konta usługi Automation.

    ![Elementy Runbook samouczka utworzone przy użyciu konta usługi Automation](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. W okienku po lewej stronie wybierz pozycję **połączony obszar roboczy**. Na stronie **połączony obszar roboczy** zostanie wyświetlony obszar roboczy log Analytics określony wcześniej połączony z kontem usługi Automation.

    ![Konto usługi Automation połączone z obszarem roboczym Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, Odłącz konto usługi Automation od obszaru roboczego Log Analytics, a następnie usuń konto usługi Automation i obszar roboczy.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono konto usługi Automation, Log Analytics obszar roboczy i połączono je ze sobą.

Aby dowiedzieć się więcej, przejdź do samouczków dotyczących Azure Automation.

> [!div class="nextstepaction"]
> [Samouczki Azure Automation](learn/automation-tutorial-runbook-graphical.md)