---
title: Korzystanie z zestawu SDK w celu skonfigurowania zarządzanych tożsamości na maszynie wirtualnej — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania i używania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu zestawu Azure SDK.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41d1be35d2add24cbbc171c3da3bd4d06bce4337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93359667"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurowanie maszyny wirtualnej z tożsamościami zarządzanymi dla zasobów platformy Azure przy użyciu zestawu Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure Active Directory (AD). Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i usunąć zarządzane tożsamości dla zasobów platformy Azure dla maszyny wirtualnej platformy Azure przy użyciu zestawu Azure SDK.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Zestawy Azure SDK z zarządzanymi tożsamościami dla obsługi zasobów platformy Azure 

Platforma Azure obsługuje wiele platform programistycznych za pomocą szeregu [zestawów SDK platformy Azure](https://azure.microsoft.com/downloads). Niektóre z nich zostały zaktualizowane w celu obsługi tożsamości zarządzanych dla zasobów platformy Azure i zapewniają odpowiednie przykłady w celu pokazania użycia. Ta lista jest aktualizowana w miarę dodawania dodatkowej pomocy technicznej:

| SDK | Przykład |
| --- | ------ | 
| .NET   | [Zarządzanie zasobem z maszyny wirtualnej z włączoną tożsamościami zarządzanymi dla zasobów platformy Azure](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Zarządzanie magazynem z poziomu maszyny wirtualnej z włączoną tożsamościami zarządzanymi dla zasobów platformy Azure](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Tworzenie maszyny wirtualnej z włączoną tożsamością zarządzaną przypisaną przez system](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Tworzenie maszyny wirtualnej z włączoną tożsamością zarządzaną przypisaną przez system](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Tworzenie maszyny wirtualnej platformy Azure z włączoną tożsamością przypisaną przez system](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Następne kroki

- Zobacz powiązane artykuły w obszarze **Konfigurowanie tożsamości dla maszyny wirtualnej platformy Azure**, aby dowiedzieć się, jak można także używać szablonów Azure Portal, PowerShell, interfejsu wiersza polecenia i zasobów.
