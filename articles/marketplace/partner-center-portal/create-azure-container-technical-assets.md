---
title: Tworzenie zasobu technicznego maszyny Wirtualnej platformy Azure
description: W tym artykule opisano kroki i wymagania dotyczące konfigurowania oferty kontenerów w portalu Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: e51b8c705533fd74a5e46eaa2570563fef485309
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730639"
---
# <a name="create-an-azure-vm-technical-asset"></a>Tworzenie zasobu technicznego maszyny Wirtualnej platformy Azure

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami usługi Azure Container z portalu cloud partnerów do centrum partnerów. Dopóki oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami w [aplikacji Przygotowanie zasobów technicznych kontenera](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) dla portalu Cloud Partner Portal, aby zarządzać ofertami.

W tym artykule opisano kroki i wymagania dotyczące konfigurowania oferty kontenera w portalu Azure Marketplace.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby uzyskać przewodników Szybki start, samouczków i przykładów, zobacz [Wystąpienia kontenera platformy Azure](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Podstawowa wiedza techniczna

Projektowanie, tworzenie i testowanie tych zasobów wymaga czasu i wymaga wiedzy technicznej zarówno platformy Azure, jak i technologii używanych do tworzenia oferty.

Oprócz domeny rozwiązania zespół inżynierów powinien posiadać wiedzę na temat następujących technologii firmy Microsoft:

- Podstawowa wiedza o [usługach platformy Azure](https://azure.microsoft.com/services/)
- Jak [projektować i projektować aplikacje platformy Azure](https://azure.microsoft.com/solutions/architecture/)
- Wiedza robocza na temat [maszyn wirtualnych platformy Azure,](https://azure.microsoft.com/services/virtual-machines/) [usługi Azure Storage](https://azure.microsoft.com/services/?filter=storage)i sieci platformy [Azure](https://azure.microsoft.com/services/?filter=networking)
- Wiedza robocza na temat [usługi Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Wiedza robocza [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Sugerowane narzędzia

Wybierz jedno lub oba z następujących środowisk skryptów, aby ułatwić zarządzanie obrazem kontenera:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Narzędzie CLI platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Zalecamy dodanie tych narzędzi do środowiska programistycznego:

- [Eksplorator usługi Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Rozszerzenie: [narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Rozszerzenie: [Upiększyć](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Rozszerzenie: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Przejrzyj dostępne narzędzia na stronie [Narzędzia deweloperskie platformy Azure.](https://azure.microsoft.com/) Jeśli używasz programu Visual Studio, zapoznaj się z narzędziami dostępnymi w [portalu Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

## <a name="create-the-container-image"></a>Tworzenie obrazu kontenera

Aby uzyskać więcej informacji, zobacz następujące samouczki:

- [Samouczek: Tworzenie obrazu kontenera do wdrożenia w wystąpieniach kontenera platformy Azure](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Samouczek: Tworzenie i wdrażanie obrazów kontenerów w chmurze za pomocą zadań rejestru kontenerów platformy Azure](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-step"></a>Następny krok

- [Utwórz ofertę kontenera](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
