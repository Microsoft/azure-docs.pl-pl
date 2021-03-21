---
title: Przygotuj swoje zasoby techniczne kontenera platformy Azure
description: Zasoby techniczne i wytyczne pomocne w konfigurowaniu oferty kontenera w witrynie Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 014bcd6fc519c267cdf17e9e98b850425c25ead6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459325"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Przygotuj swoje zasoby techniczne kontenera platformy Azure

W tym artykule przedstawiono zasoby techniczne i zalecenia ułatwiające tworzenie oferty kontenera w witrynie Azure Marketplace.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby zapoznać się z przewodnikami Szybki Start, samouczkami i przykładami, zobacz [dokumentację Azure Container Instances](../container-instances/index.yml).

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

- [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Rozszerzenie: [narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Rozszerzenie: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Rozszerzenie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Zapoznaj się z dostępnymi narzędziami na stronie [Narzędzia deweloperskie platformy Azure](https://azure.microsoft.com/) . Jeśli używasz programu Visual Studio, przejrzyj narzędzia dostępne w [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Tworzenie obrazu kontenera

Nie można wdrożyć obrazu do Azure Container Instances z rejestru lokalnego.

- Jeśli masz już kontener roboczy w lokalnym rejestrze, Utwórz rejestr platformy Azure i Przekaż obraz kontenera do Azure Container Registry. Aby dowiedzieć się więcej, zobacz [Samouczek: kompilowanie i wdrażanie obrazów kontenerów w chmurze za pomocą zadań Azure Container Registry](../container-registry/container-registry-tutorial-quick-task.md).

- Jeśli nie masz jeszcze obrazu kontenera i potrzebujesz konteneryzowanie istniejącej aplikacji lub utworzyć nową aplikację opartą na kontenerach, Sklonuj kod źródłowy aplikacji z usługi GitHub, Utwórz obraz kontenera ze źródła aplikacji i przetestuj go w lokalnym środowisku platformy Docker. Aby dowiedzieć się więcej, zobacz [Samouczek: Tworzenie obrazu kontenera na potrzeby wdrożenia do Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie oferty kontenera](create-azure-container-offer.md)