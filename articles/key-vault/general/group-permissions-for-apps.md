---
title: Udziel uprawnień aplikacjom dostępu do magazynu kluczy Azure — Azure Key Vault | Microsoft Docs
description: Dowiedz się, jak udzielić uprawnienia wielu aplikacjom dostępu do magazynu kluczy
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 28765d3a4a0812f6f3631427432105fdc4650808
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126233"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Zapewnianie uwierzytelniania Key Vault przy użyciu zasad kontroli dostępu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Najprostszym sposobem na uwierzytelnianie aplikacji opartej na chmurze w celu Key Vault jest tożsamość zarządzana; Aby uzyskać szczegółowe informacje [, zobacz używanie Azure Key Vault tożsamości zarządzanej App Service](managed-identity.md) .  Jeśli tworzysz aplikację Premium, przenosząc lokalne programowanie lub w inny sposób nie można użyć tożsamości zarządzanej, możesz zamiast tego zarejestrować jednostkę usługi ręcznie i zapewnić dostęp do magazynu kluczy przy użyciu zasad kontroli dostępu.  

Magazyn kluczy obsługuje maksymalnie 1024 wpisów zasad dostępu, przy czym każdy wpis przyznaje odrębny zestaw uprawnień dla "podmiotu zabezpieczeń": na przykład, w jaki sposób Aplikacja konsolowa w [Azure Key Vaultej bibliotece klienta dla platformy .NET szybkiego startu](../secrets/quick-create-net.md) uzyskuje dostęp do magazynu kluczy.

Aby uzyskać szczegółowe informacje na temat kontroli dostępu Key Vault, zobacz [Azure Key Vault Security: Zarządzanie tożsamościami i dostępem](overview-security.md#identity-and-access-management). Aby uzyskać szczegółowe informacje na temat kontroli dostępu, zobacz: 

- [Klucze](../keys/index.yml)
- [Kontrola dostępu do kluczy tajnych](../secrets/index.yml)
- [Kontrola dostępu do certyfikatów](../certificates/index.yml)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Magazyn kluczy. Możesz użyć istniejącego magazynu kluczy lub utworzyć nowy, wykonując czynności opisane w jednym z następujących przewodników szybki start:
   - [Tworzenie magazynu kluczy za pomocą interfejsu wiersza polecenia platformy Azure](../secrets/quick-create-cli.md)
   - [Tworzenie magazynu kluczy za pomocą Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Utwórz magazyn kluczy z Azure Portal](../secrets/quick-create-portal.md).
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub [Azure PowerShell](/powershell/azure/overview). Alternatywnie możesz użyć [Azure Portal](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Każdy wpis zasad dostępu magazynu kluczy przyznaje odrębny zestaw uprawnień dla podmiotu zabezpieczeń:

- **Aplikacja** Jeśli aplikacja jest oparta na chmurze, należy [użyć tożsamości zarządzanej, aby uzyskać dostęp do Azure Key Vault](managed-identity.md), jeśli jest to możliwe.
- **Grupa usługi Azure AD** Chociaż magazyn kluczy obsługuje tylko wpisy zasad dostępu 1024, można dodać wiele aplikacji i użytkowników do pojedynczej grupy usługi Azure AD, a następnie dodać tę grupę jako pojedynczy wpis do zasad kontroli dostępu.
- **Użytkownik** Przyznanie użytkownikom bezpośredniego dostępu do magazynu kluczy jest **niezalecane**. Najlepiej, aby użytkownicy mogli dodawać do grupy usługi Azure AD, która z kolei ma dostęp do magazynu kluczy. Zobacz [zabezpieczenia Azure Key Vault: Zarządzanie tożsamościami i dostępem](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Pobieranie identyfikatora obiektu

Aby nadać aplikacji, grupie usługi Azure AD lub użytkownikowi dostęp do magazynu kluczy, należy najpierw uzyskać jego identyfikator objectId.

#### <a name="applications"></a>Aplikacje

Identyfikator objectId aplikacji odpowiada skojarzonej z nią jednostce usługi. Aby uzyskać pełne szczegóły dotyczące jednostek usługi. Zobacz [obiekty główne aplikacji i usługi w Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md). 

Istnieją dwa sposoby uzyskania identyfikatora objectId dla aplikacji.  Pierwszy polega na zarejestrowaniu aplikacji w Azure Active Directory. Aby to zrobić, wykonaj kroki opisane w sekcji Szybki Start [zarejestruj aplikację na platformie tożsamości firmy Microsoft](../../active-directory/develop/quickstart-register-app.md). Po zakończeniu rejestracji identyfikator objectID zostanie wyświetlony jako "Identyfikator aplikacji".

Drugim jest utworzenie jednostki usługi w oknie terminalu. Za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) i podaj unikatową nazwę główną usługi dla flagi-n w formacie "http:// &lt; My-Unique-Service-Principal-Name &gt; ".

```azurecli-interactive
az ad sp create-for-rbac -n "http://<my-unique-service-principal-name"
```

Identyfikator objectId zostanie wyświetlony w danych wyjściowych jako `clientID` .

Za pomocą Azure PowerShell Użyj polecenia cmdlet [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) .


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName <my-unique-service-principal-name>
```

Identyfikator objectId zostanie wyświetlony w danych wyjściowych jako `Id` (nie `ApplicationId` ).

#### <a name="azure-ad-groups"></a>Grupy usługi Azure AD

Można dodać wiele aplikacji i użytkowników do grupy usługi Azure AD, a następnie nadać grupie dostęp do magazynu kluczy.  Aby uzyskać więcej informacji, zobacz sekcję [Tworzenie i Dodawanie członków do grupy usługi Azure AD](#creating-and-adding-members-to-an-azure-ad-group) poniżej.

Aby znaleźć identyfikator objectId grupy usługi Azure AD za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenie [AZ AD Group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) . Ze względu na dużą liczbę grup, które mogą znajdować się w organizacji, należy również podać ciąg wyszukiwania do `--display-name` parametru.

```azurecli-interactive
az ad group list --display-name <search-string>
```
Identyfikator objectId zostanie zwrócony w formacie JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Aby znaleźć identyfikator objectId grupy usługi Azure AD z Azure PowerShell, należy użyć polecenia cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) . Ze względu na dużą liczbę grup, które mogą znajdować się w organizacji, prawdopodobnie chcesz również podać ciąg wyszukiwania do `-SearchString` parametru.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

W danych wyjściowych identyfikator objectId jest wymieniony jako `Id` :

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Użytkownicy

Możesz również dodać pojedynczego użytkownika do zasad kontroli dostępu magazynu kluczy. **Nie jest to zalecane.** Zamiast tego zachęcamy do dodawania użytkowników do grupy usługi Azure AD i dodawania grupy do zasad.

Jeśli jednak chcesz znaleźć użytkownika przy użyciu interfejsu wiersza polecenia platformy Azure, użyj [AZ AD User show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) , przekazując adres e-mail użytkowników do `--id` parametru.


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

Aby znaleźć użytkownika z Azure PowerShell, należy użyć polecenia cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) , przekazując adres e-mail użytkownika do `-UserPrincipalName` parametru.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

Identyfikator obiektu użytkownika zostanie zwrócony w danych wyjściowych jako `Id` .

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Nadaj głównemu dostępowi do magazynu kluczy

Teraz, gdy masz identyfikator objectID podmiotu zabezpieczeń, możesz utworzyć zasady dostępu dla magazynu kluczy, które udostępniają uprawnienia Get, list, Set i DELETE dla kluczy i wpisów tajnych, a także wszelkie dodatkowe uprawnienia, które chcesz.

Korzystając z interfejsu wiersza polecenia platformy Azure, można to zrobić przez przekazanie identyfikatora objectId poleceniem [AZ datamagazyn Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

W Azure PowerShell jest to realizowane przez przekazanie identyfikatora objectId do polecenia cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) . 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Tworzenie i Dodawanie członków do grupy usługi Azure AD

Można utworzyć grupę usługi Azure AD, dodać do niej aplikacje i użytkowników, a następnie nadać grupie dostęp do magazynu kluczy.  Pozwala to dodać wiele aplikacji do magazynu kluczy jako pojedynczy wpis zasad dostępu i eliminuje konieczność zapewnienia użytkownikom bezpośredniego dostępu do Twojego magazynu kluczy (w naszym przypadku). Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Dodatkowe wymagania wstępne

Oprócz [powyższych wymagań wstępnych](#prerequisites)wymagane są uprawnienia do tworzenia/edytowania grup w dzierżawie Azure Active Directory. Jeśli nie masz uprawnień, może być konieczne skontaktowanie się z administratorem Azure Active Directory.

Jeśli zamierzasz używać programu PowerShell, będziesz potrzebować również [modułu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Utwórz grupę Azure Active Directory

Utwórz nową grupę Azure Active Directory przy użyciu interfejsu wiersza polecenia platformy Azure [AZ AD Group Create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) lub Azure PowerShell polecenie cmdlet [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) .


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

W obu przypadkach należy pamiętać o nowo utworzonym identyfikatorze grupy, ponieważ będzie on potrzebny do wykonania poniższych czynności.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Znajdowanie obiektów objectId aplikacji i użytkowników

Identyfikatory obiektów aplikacji można znaleźć za pomocą interfejsu wiersza polecenia platformy Azure z poleceniem [AZ AD Sp list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) z `--show-mine` parametrem.

```azurecli-interactive
az ad sp list --show-mine
```

Znajdź identyfikatory obiektów aplikacji przy użyciu Azure PowerShell za pomocą polecenia cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) , przekazując ciąg wyszukiwania do `-SearchString` parametru.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Aby znaleźć identyfikatory obiektów użytkowników, wykonaj kroki opisane w sekcji [Użytkownicy](#users) powyżej.

### <a name="add-your-applications-and-users-to-the-group"></a>Dodawanie aplikacji i użytkowników do grupy

Teraz Dodaj identyfikatory obiektów do nowo utworzonej grupy usługi Azure AD.

Za pomocą interfejsu wiersza polecenia platformy Azure, użyj [elementu członkowskiego AZ AD Group Add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add), przekazując identyfikator objectid do `--member-id` parametru.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

W Azure PowerShell Użyj polecenia cmdlet [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) , przekazując identyfikator objectid do `-MemberObjectId` parametru.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Nadaj grupie usługi AD dostęp do magazynu kluczy

Na koniec nadaj grupie usługi AD uprawnienia do Twojego magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure AZ Key Directory [Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) lub Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) polecenia cmdlet. Przykłady można znaleźć w sekcji [nadawanie aplikacji, grupie usługi Azure AD lub dostęp użytkownika do magazynu kluczy](#give-the-principal-access-to-your-key-vault) .

Aplikacja wymaga również co najmniej jednej roli zarządzania tożsamościami i dostępem (IAM) przypisanej do magazynu kluczy. W przeciwnym razie nie będzie można zalogować się i nie powiedzie się z powodu niewystarczających praw dostępu do subskrypcji.

> [!WARNING]
> Grupy usługi Azure AD z tożsamościami zarządzanymi mogą wymagać do 8hr tokenu odświeżania i zaczynają obowiązywać.

## <a name="next-steps"></a>Następne kroki

- [Zabezpieczenia Azure Key Vault: Zarządzanie tożsamościami i dostępem](overview-security.md#identity-and-access-management)
- [Zapewnianie uwierzytelniania Key Vault przy użyciu tożsamości zarządzanej App Service](managed-identity.md)
- [Zabezpiecz swój magazyn kluczy](secure-your-key-vault.md)).
- [Przewodnik dewelopera Azure Key Vault](developers-guide.md)
- Przegląd [Azure Key Vault najlepszych](best-practices.md) rozwiązań
