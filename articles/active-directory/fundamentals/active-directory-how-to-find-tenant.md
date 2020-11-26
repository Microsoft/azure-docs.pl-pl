---
title: Jak znaleźć identyfikator dzierżawy — Azure Active Directory
description: Instrukcje dotyczące znajdowania i Azure Active Directoryowania identyfikatora dzierżawy w istniejącej subskrypcji platformy Azure.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32526a31a9d52fbfac57f1d384b25e0939b5e297
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172877"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Jak znaleźć identyfikator dzierżawy Azure Active Directory

Subskrypcje platformy Azure mają relację zaufania z usługą Azure Active Directory (Azure AD). Usługa Azure AD jest zaufana do uwierzytelniania użytkowników, usług i urządzeń w ramach subskrypcji. Z każdą subskrypcją jest skojarzony identyfikator dzierżawy i istnieje kilka sposobów znalezienia identyfikatora dzierżawy dla subskrypcji.

## <a name="find-tenant-id-through-the-azure-portal"></a>Znajdź identyfikator dzierżawy za pomocą Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
 
1. Wybierz pozycję **Azure Active Directory**.

1. Wybierz pozycję **Właściwości**.

1. Następnie przewiń w dół do pola **Identyfikator dzierżawy** . Identyfikator dzierżawy będzie w tym polu.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory — właściwości — identyfikator dzierżawy — pole identyfikatora dzierżawy":::

## <a name="find-tenant-id-with-powershell"></a>Znajdowanie identyfikatora dzierżawy za pomocą programu PowerShell

Możesz również wyszukać dzierżawę programowo. Aby znaleźć identyfikator dzierżawy z Azure PowerShell, użyj polecenia cmdlet `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Aby uzyskać więcej informacji, zobacz ten Azure PowerShell dokumentacja poleceń cmdlet dla polecenia [Get-AzTenant](/powershell/module/az.accounts/get-aztenant).


## <a name="find-tenant-id-with-cli"></a>Znajdowanie identyfikatora dzierżawy przy użyciu interfejsu wiersza polecenia
Jeśli chcesz użyć interfejsu wiersza polecenia w celu znalezienia identyfikatora dzierżawy, możesz to zrobić za pomocą interfejsu użytkownika [platformy Azure](/cli/azure/install-azure-cli) lub interfejsu wiersza [Microsoft 365](https://pnp.github.io/cli-microsoft365/). 

W przypadku interfejsu wiersza polecenia platformy Azure Użyj jednego z poleceń **AZ login**, **AZ Account List** lub **AZ Account dzierżawca** , jak pokazano w poniższym przykładzie. Zwróć uwagę na Właściwość **tenantId** dla każdej subskrypcji w danych wyjściowych z każdego polecenia.

```azurecli-interactive
az login
az account list
az account tenant list
```

Aby uzyskać więcej informacji, zobacz [AZ login](/cli/azure/reference-index#az_login) Command Reference, [AZ Account](/cli/azure/ext/account/account) Command Reference lub [AZ Account dzierżawca](/cli/azure/ext/account/account/tenant) Command Reference.


W przypadku interfejsu wiersza polecenia Microsoft 365 Użyj **identyfikatora dzierżawy** poleceń cmdlet, jak pokazano w następującym przykładzie:
 
```cli
m365 tenant id get
```

Aby uzyskać więcej informacji, zobacz informacje dotyczące polecenia [Get identyfikatora dzierżawy](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) Microsoft 365.


## <a name="next-steps"></a>Następne kroki

- Aby utworzyć nową dzierżawę usługi Azure AD, zobacz [Szybki Start: Tworzenie nowej dzierżawy w Azure Active Directory](active-directory-access-create-new-tenant.md).

- Aby dowiedzieć się, jak skojarzyć lub dodać subskrypcję do dzierżawy, zobacz [kojarzenie lub Dodawanie subskrypcji platformy Azure do dzierżawy Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).

- Aby dowiedzieć się, jak znaleźć identyfikator obiektu, zobacz [Znajdowanie identyfikatora obiektu użytkownika](/partner-center/find-ids-and-domain-names#find-the-user-object-id).