---
title: Korzystanie z tożsamości zarządzanych na maszynie wirtualnej platformy Azure z zestawami Azure SDK — Azure AD
description: Przykłady kodu służące do używania zestawów Azure SDK z maszyną wirtualną platformy Azure, która ma zarządzane tożsamości dla zasobów platformy Azure.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eb5f1d81874c8303d095aec320f60cc9ae6a7dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360568"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Jak używać tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure z zestawami Azure SDK 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ten artykuł zawiera listę przykładów zestawu SDK, które demonstrują korzystanie z odpowiedniego zestawu Azure SDK do obsługi tożsamości zarządzanych dla zasobów platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - We wszystkich przykładowym kodzie/skrypcie w tym artykule przyjęto założenie, że klient działa na maszynie wirtualnej z włączonymi tożsamościami zarządzanymi dla zasobów platformy Azure. Użyj funkcji "Połącz" maszyny wirtualnej w Azure Portal, aby zdalnie nawiązać połączenie z maszyną wirtualną. Aby uzyskać szczegółowe informacje na temat włączania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](qs-configure-portal-windows-vm.md)lub jednego z artykułów wariantów (przy użyciu programu PowerShell, interfejsu wiersza polecenia, szablonu lub zestawu Azure SDK). 

## <a name="sdk-code-samples"></a>Przykłady kodu zestawu SDK

| SDK             | Przykład kodu |
| --------------- | ----------- |
| .NET            | [Wdrażanie szablonu Azure Resource Manager z maszyny wirtualnej z systemem Windows przy użyciu tożsamości zarządzanych dla zasobów platformy Azure](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Wywoływanie usług platformy Azure z maszyny wirtualnej z systemem Linux przy użyciu tożsamości zarządzanych dla zasobów platformy Azure](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Zarządzanie zasobami przy użyciu zarządzanych tożsamości dla zasobów platformy Azure](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Uwierzytelnianie za pomocą zarządzanych tożsamości zasobów platformy Azure po prostu z poziomu maszyny wirtualnej](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Zarządzanie zasobami z maszyny wirtualnej z tożsamościami zarządzanymi dla zasobów platformy Azure](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Następne kroki

- Zobacz zestaw [Azure](https://azure.microsoft.com/downloads/) SDK, aby uzyskać pełną listę zasobów zestawu Azure SDK, w tym pliki do pobrania biblioteki, dokumentację i inne.
- Aby włączyć zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](qs-configure-portal-windows-vm.md).








