---
title: 'Szybki start: tworzenie szablonu Azure Synapse roboczego Azure Resource Manager roboczego'
description: Dowiedz się, jak utworzyć obszar roboczy usługi Synapse przy użyciu Azure Resource Manager (szablonu usługi ARM).
services: azure-resource-manager
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 7317b7f51c6d0f9d72e3aad81794a569276d2145
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566124"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Szybki start: tworzenie nowego Azure Synapse roboczego przy użyciu szablonu usługi ARM

Ten Azure Resource Manager (szablon usługi ARM) utworzy obszar roboczy Azure Synapse z podstawowymi Data Lake Storage. Obszar Azure Synapse jest zabezpieczaną granicą współpracy dla procesów analitycznych w Azure Synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Możesz przejrzeć szablon, wybierając link **Visualize (Wizualizacja).** Następnie wybierz **pozycję Edytuj szablon.**

[![Wizualizacja](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

Szablon definiuje dwa zasoby:

- Konto magazynu
- Workspace

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon. Ten szablon tworzy obszar roboczy usługi Synapse.

   [![Wdrażanie na platformie Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Wprowadź lub zaktualizuj następujące wartości:

   - **Subskrypcja:** wybierz subskrypcję platformy Azure.
   - **Grupa zasobów:** wybierz **pozycję Utwórz nową,** wprowadź unikatową nazwę grupy zasobów, a następnie wybierz przycisk **OK.** Nowa grupa zasobów ułatwi oczyszczanie zasobów.
   - **Region:** Wybierz region.  Na przykład **Środkowe stany USA**.
   - **Nazwa:** wprowadź nazwę obszaru roboczego.
   - **Identyfikator logowania administratora SQL:** wprowadź nazwę użytkownika administratora SQL Server.
   - **Hasło administratora SQL:** wprowadź hasło administratora SQL Server.
   - **Wartości tagów:** zaakceptuj wartość domyślną.
   - **Przejrzyj i utwórz:** Wybierz.
   - **Utwórz:** Wybierz.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej Azure Synapse Analytics i Azure Resource Manager, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
- Dowiedz się więcej o usłudze [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- [Tworzenie i wdrażanie pierwszego szablonu usługi ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
