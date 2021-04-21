---
title: Jak znaleźć identyfikator dzierżawy — Azure Active Directory
description: Instrukcje dotyczące sposobu Azure Active Directory identyfikatora dzierżawy do istniejącej subskrypcji platformy Azure.
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
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: cba823775849fdad8407c7bb697a53761e8ccbcd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764355"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>Jak znaleźć identyfikator Azure Active Directory dzierżawy

Subskrypcje platformy Azure mają relację zaufania z Azure Active Directory (Azure AD). Usługa Azure AD jest zaufana do uwierzytelniania użytkowników, usług i urządzeń dla subskrypcji. Z każdą subskrypcją jest skojarzony identyfikator dzierżawy. Istnieje kilka sposobów na znalezienie identyfikatora dzierżawy dla subskrypcji.

## <a name="find-tenant-id-through-the-azure-portal"></a>Znajdowanie identyfikatora dzierżawy za pośrednictwem Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
 
1. Wybierz pozycję **Azure Active Directory**.

1. Wybierz pozycję **Właściwości**.

1. Następnie przewiń w dół do **pola Identyfikator dzierżawy.** Identyfikator dzierżawy będzie w polu .

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory — Właściwości — Identyfikator dzierżawy — pole Identyfikator dzierżawy":::

## <a name="find-tenant-id-with-powershell"></a>Znajdowanie identyfikatora dzierżawy przy użyciu programu PowerShell

Dzierżawę można również znaleźć programowo. Aby znaleźć identyfikator dzierżawy za pomocą Azure PowerShell, użyj polecenia cmdlet `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
Aby uzyskać więcej informacji, zobacz ten Azure PowerShell polecenia cmdlet dla [polecenia Get-AzTenant.](/powershell/module/az.accounts/get-aztenant)


## <a name="find-tenant-id-with-cli"></a>Znajdowanie identyfikatora dzierżawy przy użyciu interfejsu wiersza polecenia
Jeśli chcesz użyć interfejsu wiersza polecenia do znalezienia identyfikatora dzierżawy, możesz to zrobić za pomocą interfejsu wiersza polecenia platformy [Azure](/cli/azure/install-azure-cli) lub [interfejsu wiersza Microsoft 365](https://pnp.github.io/cli-microsoft365/)wiersza polecenia. 

W przypadku interfejsu wiersza polecenia platformy Azure użyj jednego z poleceń **az login,** **az account list** lub az account tenant **list,** jak pokazano w poniższym przykładzie. Zwróć uwagę **na właściwość tenantId** dla każdej subskrypcji w danych wyjściowych każdego polecenia.

```azurecli-interactive
az login
az account list
az account tenant list
```

Aby uzyskać więcej informacji, zobacz [az login](/cli/azure/reference-index#az_login) command reference, [az account](/cli/azure/ext/account/account) command reference lub az account [tenant command](/cli/azure/ext/account/account/tenant) reference.


W Microsoft 365 wiersza polecenia użyj identyfikatora  dzierżawy polecenia cmdlet, jak pokazano w poniższym przykładzie:
 
```cli
m365 tenant id get
```

Aby uzyskać więcej informacji, zobacz Microsoft 365 [identyfikator dzierżawy pobierz](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) odwołanie do polecenia.


## <a name="next-steps"></a>Następne kroki

- Aby utworzyć nową dzierżawę usługi Azure AD, zobacz [Szybki start: tworzenie nowej dzierżawy w usłudze Azure Active Directory](active-directory-access-create-new-tenant.md).

- Aby dowiedzieć się, jak skojarzyć lub dodać subskrypcję do dzierżawy, zobacz Kojarzenie lub dodawanie subskrypcji platformy [Azure do Azure Active Directory dzierżawy.](active-directory-how-subscriptions-associated-directory.md)

- Aby dowiedzieć się, jak znaleźć identyfikator obiektu, zobacz [Znajdowanie identyfikatora obiektu użytkownika](/partner-center/find-ids-and-domain-names#find-the-user-object-id).
