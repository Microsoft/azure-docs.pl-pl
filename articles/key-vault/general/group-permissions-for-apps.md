---
title: Udzielanie uprawnień aplikacjom w celu uzyskania dostępu do magazynu kluczy platformy Azure — usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Dowiedz się, jak udzielić wielu aplikacjom uprawnień dostępu do magazynu kluczy
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 008058e42dfeb84cb2812ac4e8378cb5a8b5913a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422599"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Zapewnianie uwierzytelniania usługi Key Vault z zasadą kontroli dostępu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Najprostszym sposobem uwierzytelniania aplikacji opartej na chmurze do usługi Key Vault jest tożsamość zarządzana; Zobacz [Korzystanie z tożsamości zarządzanej usługi App Service, aby uzyskać dostęp do usługi Azure Key Vault, aby](managed-identity.md) uzyskać szczegółowe informacje.  Jeśli tworzysz aplikację prem, wykonujesz rozwój lokalny lub w inny sposób nie możesz użyć tożsamości zarządzanej, możesz zamiast tego zarejestrować jednostkę usługi ręcznie i zapewnić dostęp do magazynu kluczy przy użyciu zasad kontroli dostępu.  

Magazyn kluczy obsługuje maksymalnie 1024 wpisy zasad dostępu, przy czym każdy wpis przyznający odrębny zestaw uprawnień do "podmiotu zabezpieczeń": Na przykład w ten sposób aplikacja konsoli w [bibliotece klienta usługi Azure Key Vault dla programu .NET Szybki start](../secrets/quick-create-net.md) uzyskuje dostęp do magazynu kluczy.

Aby uzyskać szczegółowe informacje na temat kontroli dostępu usługi Key Vault, zobacz [Zabezpieczenia usługi Azure Key Vault: zarządzanie tożsamościami i dostępem.](overview-security.md#identity-and-access-management) Aby uzyskać szczegółowe informacje na temat kontroli dostępu, zobacz: 

- [Klucze](../keys/index.yml)
- [Kontrola dostępu do wpisów tajnych](../secrets/index.yml)
- [Kontrola dostępu do certyfikatów](../certificates/index.yml)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Przechowalnia kluczy. Można użyć istniejącego magazynu kluczy lub utworzyć nowy, wykonując kroki opisane w jednym z tych przewodników Szybki start:
   - [Tworzenie magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure](../secrets/quick-create-cli.md)
   - [Tworzenie magazynu kluczy za pomocą programu Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Utwórz magazyn kluczy za pomocą portalu Azure](../secrets/quick-create-portal.md).
- [Interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub programu Azure [PowerShell](/powershell/azure/overview). Alternatywnie można użyć [witryny Azure portal](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Udziel dostępu do magazynu kluczy

Każdy wpis zasad dostępu do magazynu kluczy udziela odrębny zestaw uprawnień do podmiotu zabezpieczeń:

- **Aplikacja** Jeśli aplikacja jest oparta na chmurze, należy zamiast [tego użyć tożsamości zarządzanej, aby uzyskać dostęp do usługi Azure Key Vault](managed-identity.md), jeśli to możliwe
- **Grupa usługi Azure AD** Chociaż magazyn kluczy obsługuje tylko wpisy zasad dostępu 1024, można dodać wiele aplikacji i użytkowników do jednej grupy usługi Azure AD, a następnie dodać tę grupę jako pojedynczy wpis do zasad kontroli dostępu.
- **Użytkownik** Nie zniechęca się do bezpośredniego dostępu użytkowników do magazynu **kluczy.** W idealnym przypadku użytkownicy powinni zostać dodani do grupy usługi Azure AD, która z kolei ma dostęp do magazynu kluczy. Zobacz [zabezpieczenia usługi Azure Key Vault: zarządzanie tożsamościami i dostępem](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Pobierz identyfikator obiektu

Aby nadać aplikacji, grupie usługi Azure AD lub użytkownikowi dostęp do magazynu kluczy, należy najpierw uzyskać jej identyfikator objectId.

#### <a name="applications"></a>Aplikacje

ObjectId dla aplikacji odpowiada jego jednostki skojarzonej usługi. Aby uzyskać szczegółowe informacje na temat podmiotów świadczących usługi. Zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). 

Istnieją dwa sposoby uzyskania objectId dla aplikacji.  Pierwszym z nich jest zarejestrowanie aplikacji w usłudze Azure Active Directory. Aby to zrobić, wykonaj kroki opisane w przewodniku Szybki start [Zarejestruj aplikację na platformie tożsamości firmy Microsoft](../../active-directory/develop/quickstart-register-app.md). Po zakończeniu rejestracji identyfikator obiektu zostanie wyświetlony jako identyfikator "Application (client) ID".

Drugim jest utworzenie jednostki usługi w oknie terminala. W przypadku interfejsu wiersza polecenia azure cli użyj polecenia [az ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

ObjectId zostanie wyświetlony w danych `clientID`wyjściowych jako .

W programie Azure PowerShell użyj polecenia cmdlet [New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0)


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

ObjectId zostanie wyświetlony na wyjściu `Id` `ApplicationId`jako (nie).

#### <a name="azure-ad-groups"></a>Grupy usługi Azure AD

Można dodać wiele aplikacji i użytkowników do grupy usługi Azure AD, a następnie dać grupie dostęp do magazynu kluczy.  Aby uzyskać więcej informacji, zobacz [Tworzenie i dodawanie członków do](#creating-and-adding-members-to-an-azure-ad-group) grupy usługi Azure AD sekcji poniżej.

Aby znaleźć identyfikator objectId grupy usługi Azure AD z interfejsu wiersza polecenia platformy Azure, użyj polecenia [az ad group list.](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) Ze względu na dużą liczbę grup, które mogą znajdować się w `--display-name` organizacji, należy również podać ciąg wyszukiwania do parametru.

```azurecli-interactive
az ad group list --display-name <search-string>
```
ObjectId zostaną zwrócone w JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Aby znaleźć identyfikator objectId grupy usługi Azure AD za pomocą programu Azure PowerShell, należy użyć polecenia cmdlet [Get-AzADGroup.](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) Ze względu na dużą liczbę grup, które mogą znajdować się w organizacji, `-SearchString` prawdopodobnie chcesz również podać ciąg wyszukiwania do parametru.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

W danych wyjściowych identyfikator objectId jest wymieniony jako: `Id`

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Użytkownicy

Można również dodać indywidualnego użytkownika do zasad kontroli dostępu magazynu kluczy. **Nie polecamy tego.** Zamiast tego zachęcamy do dodawania użytkowników do grupy usługi Azure AD i dodawania grupy do zasad.

Jeśli jednak chcesz znaleźć użytkownika z interfejsem wiersza polecenia platformy Azure, użyj polecenia [az ad user show,](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) przekazując adres e-mail użytkowników do parametru. `--id`


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

Identyfikator obiektu użytkownika zostanie zwrócony w danych wyjściowych:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Aby znaleźć użytkownika za pomocą programu Azure PowerShell, użyj polecenia cmdlet [Get-AzADUser,](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) przekazując adres e-mail użytkowników do parametru. `-UserPrincipalName`

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

Identyfikator obiektu użytkownika zostanie zwrócony w `Id`danych wyjściowych jako .

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Nadaj głównemu dostęp do magazynu kluczy

Teraz, gdy masz objectID podmiotu zabezpieczeń, można utworzyć zasady dostępu dla magazynu kluczy, który daje mu get, list, set, i delete uprawnienia dla kluczy i wpisów tajnych, a także wszelkie dodatkowe uprawnienia, które chcesz.

Za pomocą interfejsu wiersza polecenia platformy Azure odbywa się to przez przekazanie objectId do polecenia [az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

Za pomocą programu Azure PowerShell odbywa się to przez przekazanie objectId do polecenia cmdlet [Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Tworzenie i dodawanie członków do grupy usługi Azure AD

Można utworzyć grupę usługi Azure AD, dodać aplikacje i użytkowników do grupy i dać grupie dostęp do magazynu kluczy.  Dzięki temu można dodać wiele aplikacji do magazynu kluczy jako pojedynczy wpis zasad dostępu i eliminuje konieczność zapewnienia użytkownikom bezpośredniego dostępu do magazynu kluczy (co zniechęcamy). Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup usługi Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Dodatkowe wymagania wstępne

Oprócz [powyższych wymagań wstępnych](#prerequisites)potrzebne będą uprawnienia do tworzenia/edytowania grup w dzierżawie usługi Azure Active Directory. Jeśli nie masz uprawnień, może być konieczne skontaktowanie się z administratorem usługi Azure Active Directory.

Jeśli zamierzasz korzystać z programu PowerShell, potrzebny będzie również [moduł programu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Tworzenie grupy usługi Azure Active Directory

Utwórz nową grupę usługi Azure Active Directory przy użyciu polecenia [tworzenia grupy reklam az](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) platformy Azure lub polecenia cmdlet Azure PowerShell [New-AzureADGroup.](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0)


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

W obu przypadkach zanotuj na nowo utworzonych grupach GroupId, ponieważ będzie ona potrzebna do wykonania poniższych kroków.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Znajdowanie identyfikatorów obiektów aplikacji i użytkowników

Identyfikatory aplikacji można znaleźć za pomocą interfejsu wiersza polecenia platformy Azure za pomocą polecenia [listy reklam az ad sp](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) z parametrem. `--show-mine`

```azurecli-interactive
az ad sp list --show-mine
```

Znajdź objectIds aplikacji przy użyciu programu Azure PowerShell z [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) polecenia `-SearchString` cmdlet, przekazując ciąg wyszukiwania do parametru.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Aby znaleźć identyfikatory użytkowników, wykonaj czynności opisane w sekcji [Użytkownicy](#users) powyżej.

### <a name="add-your-applications-and-users-to-the-group"></a>Dodawanie aplikacji i użytkowników do grupy

Teraz dodaj objectIds do nowo utworzonej grupy usługi Azure AD.

W przypadku interfejsu wiersza polecenia platformy Azure użyj elementu członkowskiego `--member-id` grupy reklam [az , przekazując](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)identyfikator objectId do parametru.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

W programie Azure PowerShell użyj polecenia cmdlet [Add-AzADGroupMember,](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) przekazując objectId do parametru. `-MemberObjectId`

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Udzielanie grupie usług AD dostępu do magazynu kluczy

Na koniec należy nadać uprawnienia grupy usługi AD do magazynu kluczy przy użyciu polecenia [zasad zestawu kluczy interfejsu wiersza](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) polecenia azure az lub polecenia cmdlet azure PowerShell [Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) Na przykład zobacz [Give aplikacji, grupy usługi Azure AD lub dostęp użytkownika do](#give-the-principal-access-to-your-key-vault) magazynu kluczy sekcji powyżej.

Aplikacja potrzebuje również co najmniej jednej roli zarządzania tożsamością i dostępem (IAM) przypisanej do magazynu kluczy. W przeciwnym razie nie będzie w stanie się zalogować i zakończy się niepowodzeniem z niewystarczającymi prawami dostępu do subskrypcji.

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczenia usługi Azure Key Vault: zarządzanie tożsamościami i dostępem](overview-security.md#identity-and-access-management)
- [Zapewnianie uwierzytelniania usługi Key Vault za pomocą tożsamości zarządzanej usługi App Service](managed-identity.md)
- [Zabezpiecz przechowalnię kluczy).](secure-your-key-vault.md)
- [Przewodnik dla deweloperów usługi Azure Key Vault](developers-guide.md)
- Zapoznaj się z [najlepszymi rozwiązaniami usługi Azure Key Vault](best-practices.md)
