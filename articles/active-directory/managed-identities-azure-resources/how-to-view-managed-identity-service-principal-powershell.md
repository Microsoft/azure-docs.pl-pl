---
title: Wyświetlanie jednostki usługi zarządzanej tożsamości przy użyciu programu PowerShell — Azure AD
description: Instrukcje krok po kroku dotyczące wyświetlania nazwy głównej usługi zarządzanej tożsamości przy użyciu programu PowerShell.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15ccc0faa4d74a2ef95aca00a6257f27b9a209c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611949"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Wyświetlanie jednostki usługi zarządzanej tożsamości przy użyciu programu PowerShell

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić nazwę główną usługi tożsamości zarządzanej przy użyciu programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamość przypisaną przez system na maszynie wirtualnej lub w](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) [aplikacji](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- Do uruchomienia przykładowych skryptów są dostępne dwie opcje:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md), którą można otworzyć za pomocą przycisku **Wypróbuj** w prawym górnym rogu bloków kodu.
    - Uruchom skrypty lokalnie, instalując najnowszą wersję [Azure PowerShell](/powershell/azure/install-az-ps), a następnie zaloguj się do platformy Azure przy użyciu `Connect-AzAccount` .

## <a name="view-the-service-principal"></a>Wyświetlanie nazwy głównej usługi

Następujące polecenie demonstruje sposób wyświetlania nazwy głównej usługi maszyny wirtualnej lub aplikacji z włączoną tożsamością przypisanej do systemu. Zamień `<Azure resource name>` na własne wartości.

```azurepowershell-interactive
Get-AzADServicePrincipal -DisplayName <Azure resource name>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o wyświetlaniu nazw podmiotów usługi Azure AD przy użyciu programu PowerShell, zobacz [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).
