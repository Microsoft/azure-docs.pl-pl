---
title: Kontrola dostępu do udziałów plików platformy Azure — lokalnego uwierzytelniania AD DS
description: Dowiedz się, jak przypisać uprawnienia do tożsamości Active Directory Domain Services, która reprezentuje Twoje konto magazynu. Dzięki temu można kontrolować dostęp przy użyciu uwierzytelniania opartego na tożsamościach.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91716041"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Część druga: przypisywanie uprawnień na poziomie udziału do tożsamości

Przed rozpoczęciem pracy z tym artykułem upewnij się, że wykonano Poprzedni artykuł, [włącz AD DS uwierzytelnianie dla Twojego konta](storage-files-identity-ad-ds-enable.md).

Po włączeniu uwierzytelniania Active Directory Domain Services (AD DS) na koncie magazynu należy skonfigurować uprawnienia na poziomie udziału w celu uzyskania dostępu do udziałów plików. Tożsamość, do której chcesz uzyskać dostęp do zasobów udziału plików platformy Azure, musi być tożsamością hybrydową, która istnieje zarówno w AD DS, jak i w usłudze Azure AD. Załóżmy na przykład, że masz użytkownika w AD DS, który został user1@onprem.contoso.com zsynchronizowany z usługą Azure AD jako user1@contoso.com używany do Azure AD Connect synchronizacji. Aby zezwolić temu użytkownikowi na dostęp do Azure Files, należy przypisać uprawnienia na poziomie udziału do programu user1@contoso.com . To samo pojęcie dotyczy grup lub nazw podmiotów usługi. W związku z tym należy zsynchronizować użytkowników i grupy z AD DS do usługi Azure AD przy użyciu funkcji Azure AD Connect Sync. 

Uprawnienia na poziomie udziału muszą być przypisane do tożsamości usługi Azure AD reprezentującej tego samego użytkownika lub grupę w AD DS do obsługi uwierzytelniania AD DS w udziale plików platformy Azure. Uwierzytelnianie i autoryzacja w przypadku tożsamości, które istnieją tylko w usłudze Azure AD, takich jak tożsamości zarządzane przez platformę Azure (MSIs), nie są obsługiwane w ramach uwierzytelniania AD DS. W tym artykule przedstawiono sposób przypisywania uprawnień na poziomie udziałów do udziału plików w tożsamości.


## <a name="share-level-permissions"></a>Uprawnienia na poziomie udziału

Ogólnie rzecz biorąc, zalecamy używanie uprawnień na poziomie udziału do zarządzania dostępem wysokiego poziomu w grupie usługi Azure AD reprezentującej grupę użytkowników i tożsamości, a następnie wykorzystanie list ACL systemu Windows w celu uzyskania szczegółowej kontroli dostępu do poziomu katalogu/pliku. 

Istnieją trzy wbudowane role platformy Azure do udzielania użytkownikom uprawnień na poziomie udziału:

- **Czytnik udziałów plików dla plików magazynu (SMB** ) umożliwia dostęp do odczytu w udziałach pliku usługi Azure Storage za pośrednictwem protokołu SMB.
- **Współautor udostępniania danych plików magazynu SMB** umożliwia dostęp do odczytu, zapisu i usuwania w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB.
- **Współautorzy udziałów SMB danych plików magazynu** umożliwia odczyt, zapis, usuwanie i modyfikowanie list ACL systemu Windows w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB.

> [!IMPORTANT]
> Pełna kontrola administracyjna udziału plików, w tym możliwość przejęcia na własność pliku, wymaga użycia klucza konta magazynu. Kontrolka administracyjna nie jest obsługiwana w przypadku poświadczeń usługi Azure AD.

Możesz użyć interfejsu wiersza polecenia Azure Portal, Azure PowerShell lub Azure, aby przypisać wbudowane role do tożsamości usługi Azure AD użytkownika w celu udzielenia uprawnień na poziomie udziału.

## <a name="assign-an-azure-role"></a>Przypisywanie roli platformy Azure

### <a name="azure-portal"></a>Azure Portal

Aby przypisać rolę platformy Azure do tożsamości usługi Azure AD przy użyciu [Azure Portal](https://portal.azure.com), wykonaj następujące czynności:

1. W Azure Portal przejdź do udziału plików lub [Utwórz udział plików](storage-how-to-create-file-share.md).
1. Wybierz pozycję **Access Control (IAM)**.
1. Wybierz pozycję **Dodaj przypisanie roli**
1. W bloku **Dodaj przypisanie roli** wybierz odpowiednią rolę wbudowaną (plik magazynu: czytnik udziałów SMB, współautor udziału danych plików magazynu) z listy **rola** . Pozostaw ustawienie domyślne do **przypisywania** : **użytkownik, Grupa lub nazwa główna usługi Azure AD**. Wybierz docelową tożsamość usługi Azure AD według nazwy lub adresu e-mail. **Wybrana tożsamość usługi Azure AD musi być tożsamością hybrydową i nie może być tożsamością tylko do chmury.** Oznacza to, że ta sama tożsamość jest również reprezentowana w AD DS.
1. Wybierz pozycję **Zapisz** , aby ukończyć operację przypisywania roli.

### <a name="powershell"></a>PowerShell

Poniższy przykład programu PowerShell pokazuje, jak przypisać rolę platformy Azure do tożsamości usługi Azure AD na podstawie nazwy logowania. Aby uzyskać więcej informacji na temat przypisywania ról platformy Azure za pomocą programu PowerShell, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu modułu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Przed uruchomieniem następującego przykładowego skryptu Zastąp wartości symboli zastępczych, w tym nawiasów, wartościami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>Interfejs wiersza polecenia
  
Następujące polecenie interfejsu wiersza polecenia 2,0 przypisuje rolę platformy Azure do tożsamości usługi Azure AD na podstawie nazwy logowania. Aby uzyskać więcej informacji o przypisywaniu ról platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia](../../role-based-access-control/role-assignments-cli.md) 

Przed uruchomieniem następującego przykładowego skryptu pamiętaj, aby zastąpić wartości symboli zastępczych, w tym nawiasów, z własnymi wartościami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>Następne kroki

Po przypisaniu uprawnień na poziomie udziału należy skonfigurować uprawnienia do katalogów i na poziomie plików. Przejdź do następnego artykułu.

[Część trzecia: Konfigurowanie uprawnień na poziomie katalogu i pliku za pośrednictwem protokołu SMB](storage-files-identity-ad-ds-configure-permissions.md)
