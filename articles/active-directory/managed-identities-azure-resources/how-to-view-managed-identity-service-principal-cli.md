---
title: Wyświetlanie nazwy głównej usługi o tożsamości zarządzanej — interfejs wiersza polecenia platformy Azure — Azure AD
description: Instrukcje krok po kroku dotyczące wyświetlania nazwy głównej usługi zarządzanej tożsamości przy użyciu interfejsu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 767a0189ad9f55c615dc4bbd30ddfd90522f5761
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608402"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Wyświetlanie jednostki usługi dla tożsamości zarządzanej przy użyciu interfejsu wiersza polecenia platformy Azure

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak wyświetlić nazwę główną usługi tożsamości zarządzanej przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Włącz [tożsamość przypisaną przez system na maszynie wirtualnej lub w](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) [aplikacji](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity).
- Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, można korzystać z trzech opcji:
    - Użyj [Azure Cloud Shell](../../cloud-shell/overview.md) z Azure Portal (zobacz następną sekcję).
    - Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj go" znajdującego się w prawym górnym rogu każdego bloku kodu.
    - [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli) Jeśli wolisz korzystać z lokalnej konsoli interfejsu wiersza polecenia i zalogować się do platformy Azure przy użyciu polecenia`az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Wyświetlanie nazwy głównej usługi

Następujące polecenie pokazuje, jak wyświetlić nazwę główną usługi maszyny wirtualnej lub aplikacji z włączoną tożsamością zarządzaną. Zamień `<VM or application name>` na własne wartości. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zarządzania nazwami główną usługi Azure AD przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [AZ AD Sp](/cli/azure/ad/sp).


