---
title: Konfiguracja po wdrożeniu przy użyciu rozszerzeń
description: Dowiedz się, jak za pomocą rozszerzeń szablonów Azure Resource Manager zapewnić konfiguracje po wdrożeniu.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: be55138a2aa6dc0552c7556438ffd43705687c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86055053"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Zapewnianie konfiguracji po wdrożeniu przy użyciu rozszerzeń

Rozszerzenia szablonów to małe aplikacje, które zapewniają konfigurację po wdrożeniu i zadania automatyzacji w zasobach platformy Azure. Najbardziej popularnym z nich są rozszerzenia maszyny wirtualnej. Zobacz [rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](../../virtual-machines/extensions/features-windows.md)oraz [rozszerzenia i funkcje maszyny wirtualnej w systemie Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Rozszerzenia

Istniejące rozszerzenia to:

- [Microsoft. COMPUTE/virtualMachines/rozszerzenia](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft. COMPUTE virtualMachineScaleSets/rozszerzenia](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Klastry/rozszerzenia usługi HDInsight firmy Microsoft](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft. SQL Servers/Databases/Extensions](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft. Web/Sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Aby dowiedzieć się, jakie rozszerzenia są dostępne, przejdź do [odwołania do szablonu](/azure/templates/). W polu **Filtruj według tytułu**wprowadź **rozszerzenie**.

Aby dowiedzieć się, jak korzystać z tych rozszerzeń, zobacz:

- [Samouczek: Wdrażanie rozszerzeń maszyn wirtualnych za pomocą szablonów Azure Resource Manager](template-tutorial-deploy-vm-extensions.md).
- [Samouczek: importowanie plików BACPAC bazy danych SQL za pomocą szablonów usługi Azure Resource Manager](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: wdrażanie rozszerzeń maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager](template-tutorial-deploy-vm-extensions.md)
