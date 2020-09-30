---
title: 'Szybki Start: Tworzenie szablonu Azure Resource Manager obszaru roboczego usługi Azure Synapse'
description: Dowiedz się, jak utworzyć obszar roboczy Synapse przy użyciu szablonu Azure Resource Manager.
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: dc6d36f2316e0ae19ce8b813fa9eb127b1a9cf1f
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569011"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-a-deployment-template"></a>Szybki Start: Tworzenie obszaru roboczego usługi Azure Synapse przy użyciu szablonu wdrożenia

Ten szablon Azure Resource Manager (szablon ARM) utworzy obszar roboczy Azure Synapse z podstawowym Data Lake Storage. Obszar roboczy usługi Azure Synapse to zabezpieczana granica współpracy dla procesów analitycznych w usłudze Azure Synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Możesz przejrzeć szablon, wybierając link **Wizualizacja** w następujący sposób:

[![Wizualizacja](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

Szablon definiuje dwa zasoby:

- Konto magazynu
- Workspace

## <a name="deploy-the-template"></a>Wdrażanie szablonu

1. Wybierz Poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon. Ten szablon służy do tworzenia obszaru roboczego Synapse.
   
   [![Wdrażanie na platformie Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Wprowadź lub zaktualizuj następujące wartości:

   * **Subskrypcja**: wybierz subskrypcję platformy Azure.
   * **Grupa zasobów**: wybierz pozycję **Utwórz nową** i wprowadź unikatową nazwę grupy zasobów, a następnie wybierz **przycisk OK**. Nowa grupa zasobów ułatwi czyszczenie zasobów.
   * **Region**: Wybierz region.  Na przykład **Środkowe stany USA**.
   * **Nazwa**: Wprowadź nazwę obszaru roboczego.
   * **Logowanie administratora SQL**: Wprowadź nazwę użytkownika administratora dla SQL Server.
   * **Hasło administratora SQL**: wprowadź hasło administratora dla SQL Server.
   * **Wartości tagów**: Zaakceptuj wartość domyślną. 
   * **Przejrzyj i Utwórz**: Wybierz.
   * **Utwórz**: Wybierz.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze Azure Synapse Analytics i Azure Resource Manager, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem usługi Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 
- Dowiedz się więcej o usłudze [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- [Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
