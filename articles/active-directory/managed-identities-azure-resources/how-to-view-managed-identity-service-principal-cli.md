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
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce3a35562bdef34f44ef1093a3196ea7afb0bd9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92892047"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Wyświetlanie jednostki usługi dla tożsamości zarządzanej przy użyciu interfejsu wiersza polecenia platformy Azure

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić nazwę główną usługi tożsamości zarządzanej przy użyciu interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zobacz co to [są tożsamości zarządzane dla zasobów platformy Azure?](overview.md).

- Włącz [tożsamość przypisaną przez system na maszynie wirtualnej lub w](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) [aplikacji](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="view-the-service-principal"></a>Wyświetlanie nazwy głównej usługi

Następujące polecenie pokazuje, jak wyświetlić nazwę główną usługi maszyny wirtualnej lub aplikacji z włączoną tożsamością zarządzaną. Zamień `<Azure resource name>` na własne wartości.

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania nazwami główną usługi Azure AD przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [AZ AD Sp](/cli/azure/ad/sp).
