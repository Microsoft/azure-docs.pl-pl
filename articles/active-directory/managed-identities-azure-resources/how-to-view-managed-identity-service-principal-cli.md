---
title: Wyświetlanie nazwy głównej usługi o tożsamości zarządzanej — interfejs wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące wyświetlania nazwy głównej usługi zarządzanej tożsamości przy użyciu interfejsu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8eec72666eadf90a401dc8f0adb77df77dbf782
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969305"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Wyświetlanie jednostki usługi dla tożsamości zarządzanej przy użyciu interfejsu wiersza polecenia platformy Azure

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić nazwę główną usługi tożsamości zarządzanej przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamość przypisaną przez system na maszynie wirtualnej lub w](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) [aplikacji](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Do uruchomienia przykładowych skryptów są dostępne dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą można otworzyć za pomocą przycisku **Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie, instalując najnowszą wersję [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli), a następnie zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, w której chcesz utworzyć zasoby.   

## <a name="view-the-service-principal"></a>Wyświetlanie nazwy głównej usługi

Następujące polecenie pokazuje, jak wyświetlić nazwę główną usługi maszyny wirtualnej lub aplikacji z włączoną tożsamością zarządzaną. Zamień `<VM or application name>` na własne wartości. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania nazwami główną usługi Azure AD przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [AZ AD Sp](/cli/azure/ad/sp).
