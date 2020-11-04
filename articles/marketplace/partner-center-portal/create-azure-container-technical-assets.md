---
title: Koncepcje techniczne dotyczące ofert kontenerów platformy Azure — Microsoft Commercial Marketplace
description: Zasoby techniczne i wytyczne pomocne w konfigurowaniu oferty kontenera w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/09/2020
ms.openlocfilehash: 3599052e49b91747580bf506f06ebfb8d2e34423
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93346812"
---
# <a name="create-an-azure-container-offer"></a>Tworzenie oferty kontenera platformy Azure

W tym artykule przedstawiono zasoby techniczne i zalecenia ułatwiające tworzenie oferty kontenera w witrynie Azure Marketplace.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby zapoznać się z przewodnikami Szybki Start, samouczkami i przykładami, zobacz [dokumentację Azure Container Instances](../../container-instances/index.yml).

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

- [Azure PowerShell](/powershell/azure/)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/).

Zalecamy dodanie tych narzędzi do środowiska deweloperskiego:

- [Eksplorator usługi Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Rozszerzenie: [narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Rozszerzenie: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Rozszerzenie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Zapoznaj się z dostępnymi narzędziami na stronie [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/) . Jeśli używasz programu Visual Studio, przejrzyj narzędzia dostępne w [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Tworzenie obrazu kontenera

Aby uzyskać więcej informacji, zobacz następujące samouczki:

- [Samouczek: Tworzenie obrazu kontenera na potrzeby wdrożenia Azure Container Instances](../../container-instances/container-instances-tutorial-prepare-app.md)
- [Samouczek: kompilowanie i wdrażanie obrazów kontenerów w chmurze za pomocą Azure Container Registryych zadań](../../container-registry/container-registry-tutorial-quick-task.md).

## <a name="next-steps"></a>Następne kroki

- [Utwórz ofertę kontenera](create-azure-container-offer.md).