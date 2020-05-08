---
title: Koncepcje techniczne dotyczące ofert kontenerów platformy Azure — Microsoft Commercial Marketplace
description: Zasoby techniczne i wytyczne pomocne w konfigurowaniu oferty kontenera w witrynie Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: b3c6f88df151cc497f0de670d5d78a05c7477459
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791888"
---
# <a name="create-an-azure-container-offer"></a>Tworzenie oferty kontenera platformy Azure

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami kontenerów platformy Azure z portal Cloud Partner do Centrum partnerskiego. Dopóki Twoje oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami zawartymi w temacie [przygotowanie zasobów technicznych kontenera](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) do Portal Cloud partner, aby zarządzać ofertami.

W tym artykule przedstawiono zasoby techniczne i zalecenia ułatwiające tworzenie oferty kontenera w witrynie Azure Marketplace.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby zapoznać się z przewodnikami Szybki Start, samouczkami i przykładami, zobacz [dokumentację Azure Container Instances](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, kompilowanie i testowanie tych zasobów zabiera czas i wymaga znajomości wiedzy technicznej platformy Azure oraz technologii używanych do tworzenia oferty.

Poza domeną rozwiązania Zespół inżynieryjny powinien znać następujące technologie firmy Microsoft:

- Podstawowe informacje o [usługach platformy Azure](https://azure.microsoft.com/services/)
- [Projektowanie i tworzenie architektury aplikacji platformy Azure](https://azure.microsoft.com/solutions/architecture/)
- Wiedza praktyczna [Virtual Machines platformy](https://azure.microsoft.com/services/virtual-machines/)Azure, [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage)i [sieci platformy Azure](https://azure.microsoft.com/services/?filter=networking)
- Wiedza o pracy [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Praktyczna wiedza o formacie [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz co najmniej jedno z następujących środowisk skryptów, aby ułatwić zarządzanie obrazem kontenera:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Zalecamy dodanie tych narzędzi do środowiska deweloperskiego:

- [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Rozszerzenie: [narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Rozszerzenie: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Rozszerzenie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Zapoznaj się z dostępnymi narzędziami na stronie [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/) . Jeśli używasz programu Visual Studio, przejrzyj narzędzia dostępne w [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Tworzenie obrazu kontenera

Aby uzyskać więcej informacji, zobacz następujące samouczki:

- [Samouczek: Tworzenie obrazu kontenera na potrzeby wdrożenia Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Samouczek: kompilowanie i wdrażanie obrazów kontenerów w chmurze za pomocą Azure Container Registryych zadań](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-steps"></a>Następne kroki

- [Utwórz ofertę kontenera](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
