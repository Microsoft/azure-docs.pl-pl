---
title: Konfigurowanie wystąpienia i uwierzytelniania (PowerShell)
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji przy użyciu Azure PowerShell
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65495da13bc6c9ed91c1ecbd587c16c949136d73
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98040692"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>Konfigurowanie wystąpienia i uwierzytelniania usługi Azure Digital bliźniaczych reprezentacji (PowerShell)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

W tym artykule opisano procedurę **konfigurowania nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji**, w tym tworzenia wystąpienia i konfigurowania uwierzytelniania. Po ukończeniu tego artykułu będzie dostępne wystąpienie usługi Azure Digital bliźniaczych reprezentacji gotowe do rozpoczęcia programowania.

W tej wersji tego artykułu przechodzą te kroki ręcznie, po jednym z nich przy użyciu Azure PowerShell.

* Aby wykonać te kroki ręcznie przy użyciu Azure Portal, zobacz wersję portalu w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (Portal)*](how-to-set-up-instance-portal.md).
* Aby przeprowadzić instalację zautomatyzowaną za pomocą przykładowego skryptu wdrożenia, zobacz wersję skryptu w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (skrypty)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

1. Jeśli zdecydujesz się używać Azure PowerShell lokalnie:
   1. [Zainstaluj moduł AZ PowerShell module](/powershell/azure/install-az-ps).
   1. Połącz się z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
1. Jeśli zdecydujesz się używać Azure Cloud Shell:
   1. Aby uzyskać więcej informacji [, zobacz omówienie Azure Cloud Shell](../cloud-shell/overview.md) .
   1. Na pasku ikon Cloud Shell upewnij się, że Cloud Shell jest ustawiony do uruchamiania wersji programu PowerShell.

      :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Okno Cloud Shell pokazujące wybór wersji programu PowerShell":::

1. Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Jeśli jest to pierwsze użycie usługi Azure Digital bliźniaczych reprezentacji z tą subskrypcją, należy zarejestrować dostawcę zasobów **Microsoft. DigitalTwins** .

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

> [!IMPORTANT]
> Mimo że moduł **AZ. DigitalTwins** PowerShell jest w wersji zapoznawczej, należy go zainstalować oddzielnie przy użyciu `Install-Module` polecenia cmdlet. Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, będzie częścią przyszłych wydań modułu Az programu PowerShell i będzie domyślnie dostępny z poziomu usługi Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.DigitalTwins
```

## <a name="create-the-azure-digital-twins-instance"></a>Tworzenie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

W tej sekcji utworzysz **nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji** przy użyciu Azure PowerShell.
Musisz podać:

* [Grupa zasobów platformy Azure](../azure-resource-manager/management/overview.md). Jeśli nie masz jeszcze istniejącej grupy zasobów, możesz ją utworzyć za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) .

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* Region wdrożenia. Aby zobaczyć, które regiony obsługują usługę Azure Digital bliźniaczych reprezentacji, odwiedź stronę [*usługi Azure dostępne według regionów*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Nazwa wystąpienia. Nazwa nowego wystąpienia musi być unikatowa w regionie w ramach subskrypcji. Jeśli Twoja subskrypcja ma inne wystąpienie usługi Azure Digital bliźniaczych reprezentacji w regionie, w którym już korzystasz z określonej nazwy, zostanie wyświetlony monit o wybranie innej nazwy.

Użyj wartości z następującego przykładu, aby utworzyć wystąpienie:

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Weryfikowanie sukcesu i zbieranie ważnych wartości

Jeśli wystąpienie zostało utworzone pomyślnie, wynik będzie wyglądać podobnie do następujących danych wyjściowych zawierających informacje o utworzonym przez Ciebie zasobie:

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

> [!TIP]
> Te właściwości, a także wszystkie właściwości wystąpienia, można wyświetlić w dowolnym momencie, uruchamiając `Get-AzDigitalTwinsInstance` i wykonując potoki z `Select-Object -Property *` .

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

Zwróć uwagę na nazwy **hosta**, **nazwę** i **resourceName** wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Są to ważne wartości, które mogą być potrzebne, jeśli będziesz kontynuować pracę z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, aby skonfigurować uwierzytelnianie i powiązane zasoby platformy Azure. Jeśli inni użytkownicy będą programowanie względem wystąpienia, należy udostępnić te wartości.

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji. Następnie uzyskasz odpowiednie uprawnienia użytkownika platformy Azure do zarządzania nim.

## <a name="set-up-user-access-permissions"></a>Konfigurowanie uprawnień dostępu użytkowników

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Określ identyfikator **obiektu dla konta** usługi Azure AD użytkownika, do którego ma zostać przypisana rola za pomocą polecenia cmdlet [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) .

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

Aby przypisać rolę, użyj następującego polecenia. Musi być uruchamiane przez użytkownika z [wystarczającymi uprawnieniami](#prerequisites-permission-requirements) w ramach subskrypcji platformy Azure. Polecenie wymaga przekazania **identyfikatora objectid** dla konta usługi Azure AD użytkownika, do którego ma zostać przypisana rola. Wymaga również użycia tego samego identyfikatora subskrypcji, nazwy grupy zasobów i nazwy wystąpienia usługi Azure Digital bliźniaczych reprezentacji, która została wybrana wcześniej.

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

Wynikiem tego polecenia jest informacje o utworzonym przypisaniu roli.

### <a name="verify-success"></a>Weryfikowanie sukcesu

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji i masz przypisane uprawnienia do zarządzania nim.

## <a name="next-steps"></a>Następne kroki

Zobacz, jak połączyć aplikację kliencką z wystąpieniem przy użyciu kodu uwierzytelniania:
* [*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)
