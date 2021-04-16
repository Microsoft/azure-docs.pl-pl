---
title: 'Szybki start: tworzenie konta usługi Automation — szablon platformy Azure'
titleSuffix: Azure Automation
description: W tym przewodniku Szybki start pokazano, jak utworzyć konto usługi Automation przy użyciu Azure Resource Manager szablonu.
services: automation
ms.author: magoedte
ms.date: 01/07/2021
ms.topic: quickstart
ms.workload: infrastructure-services
ms.custom:
- mvc
- subject-armqs
- mode-arm
ms.openlocfilehash: 50a8c057a6fdf6350a18225cd2dc538942d6686d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538406"
---
# <a name="quickstart-create-an-automation-account-by-using-arm-template"></a>Szybki start: tworzenie konta usługi Automation przy użyciu szablonu usługi ARM

Azure Automation oferuje opartą na chmurze usługę automatyzacji i konfiguracji, która obsługuje spójne zarządzanie w środowiskach platformy Azure i poza platformą Azure. W tym przewodniku Szybki start pokazano, jak wdrożyć szablon usługi Azure Resource Manager (arm), który tworzy konto usługi Automation. Korzystanie z szablonu usługi ARM wymaga mniej kroków w porównaniu z innymi metodami wdrażania.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Ten przykładowy szablon wykonuje następujące czynności:

* Automatyzuje tworzenie nowego obszaru roboczego Azure Monitor Log Analytics.
* Automatyzuje tworzenie konta Azure Automation konta.
* Łączy konto usługi Automation z obszarem roboczym usługi Log Analytics.
* Dodaje przykładowe podręczniki automatyzacji do konta.

>[!NOTE]
>Tworzenie konta Uruchom jako usługi Automation nie jest obsługiwane w przypadku korzystania z szablonu usługi ARM. Aby ręcznie utworzyć konto Uruchom jako z portalu lub przy użyciu programu PowerShell, zobacz [Tworzenie konta Uruchom jako.](create-run-as-account.md)

Po ukończeniu tych kroków [](automation-manage-send-joblogs-log-analytics.md) należy skonfigurować ustawienia diagnostyczne dla konta usługi Automation w celu wysyłania stanu zadania i strumieni zadań runbook do połączonego obszaru roboczego usługi Log Analytics.

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-automation/).

:::code language="json" source="~/quickstart-templates/101-automation/azuredeploy.json":::

### <a name="api-versions"></a>Wersje interfejsu API

W poniższej tabeli wymieniono wersję interfejsu API dla zasobów używanych w tym przykładzie.

| Zasób | Typ zasobu | Wersja interfejsu API |
|:---|:---|:---|
| [Workspace](/azure/templates/microsoft.operationalinsights/workspaces) | obszary robocze | 2020-03-01-preview |
| [Konto usługi Automation](/azure/templates/microsoft.automation/automationaccounts) | automatyzacja | 2020-01-13-preview |
| [Połączone usługi obszaru roboczego](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | obszary robocze | 2020-03-01-preview |

### <a name="before-you-use-the-template"></a>Przed użyciem szablonu

Szablon parametrów JSON jest konfigurowany w celu określenia:

* Nazwa obszaru roboczego.
* Region, w celu utworzenia obszaru roboczego.
* Nazwa konta usługi Automation.
* Region, w których ma być konto usługi Automation.

Następujące parametry w szablonie są ustawiane z wartością domyślną dla obszaru roboczego usługi Log Analytics:

* *W przypadku jednostki SKU* domyślną warstwą cenową za GB wydaną w modelu cenowym z kwietnia 2018 r.
* *Wartość domyślna dla ustawienia dataRetention* to 30 dni.

>[!WARNING]
>Jeśli chcesz utworzyć lub skonfigurować obszar roboczy usługi Log Analytics w subskrypcji, która wybrała model cenowy z kwietnia 2018 r., jedyną prawidłową warstwą cenową usługi Log Analytics jest *PerGB2018.*
>

Szablon JSON określa wartość domyślną dla innych parametrów, które prawdopodobnie będą używane jako standardowa konfiguracja w twoim środowisku. Szablon można przechowywać na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonych w organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz Deploy resources with ARM templates and the Azure CLI (Wdrażanie zasobów za [pomocą szablonów usługi ARM i interfejsu wiersza polecenia platformy Azure).](../azure-resource-manager/templates/deploy-cli.md)

Jeśli dopiero poznajemy Azure Automation i Azure Monitor, ważne jest, aby zrozumieć następujące szczegóły konfiguracji. Mogą one pomóc uniknąć błędów podczas próby utworzenia, skonfigurowania i użycia obszaru roboczego usługi Log Analytics połączonego z nowym kontem usługi Automation.

* Przejrzyj [dodatkowe szczegóły,](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) aby w pełni zrozumieć opcje konfiguracji obszaru roboczego, takie jak tryb kontroli dostępu, warstwa cenowa, przechowywanie i poziom rezerwacji pojemności.

* Przejrzyj [mapowania obszarów roboczych,](how-to/region-mappings.md) aby określić obsługiwane regiony w tekście lub w pliku parametrów. Tylko niektóre regiony są obsługiwane w przypadku łączenia obszaru roboczego usługi Log Analytics i konta usługi Automation w ramach subskrypcji.

* Jeśli nie wiesz już, jak Azure Monitor obszaru roboczego, zapoznaj się ze wskazówkami w zakresie projektowania [obszaru roboczego.](../azure-monitor/logs/design-logs-deployment.md) Pomoże ci to dowiedzieć się więcej na temat kontroli dostępu i poznać strategie implementacji projektu zalecane dla Twojej organizacji.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy konto Azure Automation, obszar roboczy usługi Log Analytics i łączy konto usługi Automation z obszarem roboczym.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-automation%2Fazuredeploy.json)

2. Wprowadź wartości.

3. Wdrożenie może potrwać kilka minut. Po zakończeniu dane wyjściowe będą podobne do następujących:

    ![Przykładowy wynik po zakończeniu wdrażania](media/quickstart-create-automation-account-template/template-output.png)

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W Azure Portal otwórz właśnie utworzone konto usługi Automation. 

3. W okienku po lewej stronie wybierz pozycję **Runbook**. Na stronie **Runbook znajdują** się trzy samouczki runbook utworzone przy użyciu konta usługi Automation.

    ![Samouczek dotyczący podręczników Runbook utworzonych przy użyciu konta usługi Automation](./media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. W okienku po lewej stronie wybierz pozycję **Połączony obszar roboczy.** Na stronie **Połączony obszar roboczy** jest on przedstawia określony wcześniej obszar roboczy usługi Log Analytics połączony z kontem usługi Automation.

    ![Konto usługi Automation połączone z obszarem roboczym usługi Log Analytics](./media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie będą już potrzebne, odłącz konto usługi Automation od obszaru roboczego usługi Log Analytics, a następnie usuń konto i obszar roboczy usługi Automation.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono konto usługi Automation i obszar roboczy usługi Log Analytics oraz powiązyliśmy je ze sobą.

Aby dowiedzieć się więcej, przejdź do samouczków dla Azure Automation.

> [!div class="nextstepaction"]
> [Azure Automation samouczków](learn/automation-tutorial-runbook-graphical.md)
