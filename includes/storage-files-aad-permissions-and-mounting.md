---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 4773446ec0007ffbed99bc01939d1f92f5823d99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95563850"
---
## <a name="assign-access-permissions-to-an-identity"></a>Przypisywanie uprawnień dostępu do tożsamości

Aby uzyskać dostęp do zasobów Azure Files przy użyciu uwierzytelniania opartego na tożsamościach, tożsamość (nazwa użytkownika, Grupa lub nazwa główna usługi) musi mieć odpowiednie uprawnienia na poziomie udziału. Ten proces jest podobny do określania uprawnień do udziału w systemie Windows, w którym można określić typ dostępu określonego użytkownika do udziału plików. Wskazówki zawarte w tej sekcji przedstawiają sposób przypisywania uprawnień do odczytu, zapisu lub usuwania udziału plików do tożsamości. 

Wprowadziliśmy trzy wbudowane role platformy Azure na potrzeby udzielania użytkownikom uprawnień na poziomie udziału:

- **Czytnik udziałów plików dla plików magazynu (SMB** ) umożliwia dostęp do odczytu w udziałach pliku usługi Azure Storage za pośrednictwem protokołu SMB.
- **Współautor udostępniania danych plików magazynu SMB** umożliwia dostęp do odczytu, zapisu i usuwania w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB.
- **Współautorzy udziałów SMB danych plików magazynu** umożliwia odczyt, zapis, usuwanie i modyfikowanie uprawnień NTFS w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB.

> [!IMPORTANT]
> Pełna kontrola administracyjna udziału plików, w tym możliwość przejęcia na własność pliku, wymaga użycia klucza konta magazynu. Kontrolka administracyjna nie jest obsługiwana w przypadku poświadczeń usługi Azure AD.

Korzystając z Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure, można przypisać wbudowane role do tożsamości usługi Azure AD użytkownika w celu udzielenia uprawnień na poziomie udziału. Należy pamiętać, że w efekcie przypisanie roli platformy Azure na poziomie udziału może zająć trochę czasu. 

> [!NOTE]
> Pamiętaj, aby [zsynchronizować poświadczenia AD DS z usługą Azure AD](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) , jeśli planujesz używać lokalnego AD DS do uwierzytelniania. Synchronizacja skrótów haseł z AD DS do usługi Azure AD jest opcjonalna. Uprawnienie na poziomie udziału zostanie przyznane tożsamości usługi Azure AD synchronizowanej z AD DS lokalnej.

Ogólnym zaleceniem jest użycie uprawnienia na poziomie udziału do zarządzania dostępem wysokiego poziomu do grupy usługi AD reprezentującej grupę użytkowników i tożsamości, a następnie skorzystanie z uprawnień systemu plików NTFS w celu uzyskania szczegółowej kontroli dostępu na poziomie katalogu/pliku. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Przypisywanie roli platformy Azure do tożsamości usługi AD

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby przypisać rolę platformy Azure do tożsamości usługi Azure AD przy użyciu [Azure Portal](https://portal.azure.com), wykonaj następujące czynności:

1. W Azure Portal przejdź do udziału plików lub [Utwórz udział plików](../articles/storage/files/storage-how-to-create-file-share.md).
2. Wybierz pozycję **Access Control (IAM)**.
3. Wybierz pozycję **Dodaj przypisanie roli**
4. W bloku **Dodaj przypisanie roli** wybierz odpowiednią rolę wbudowaną (plik magazynu: czytnik udziałów SMB, współautor udziału danych plików magazynu) z listy **rola** . Pozostaw ustawienie domyślne do **przypisywania** : **użytkownik, Grupa lub nazwa główna usługi Azure AD**. Wybierz docelową tożsamość usługi Azure AD według nazwy lub adresu e-mail.
5. Wybierz pozycję **Zapisz** , aby ukończyć operację przypisywania roli.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Poniższy przykład programu PowerShell pokazuje, jak przypisać rolę platformy Azure do tożsamości usługi Azure AD na podstawie nazwy logowania. Aby uzyskać więcej informacji na temat przypisywania ról platformy Azure za pomocą programu PowerShell, zobacz [Zarządzanie dostępem przy użyciu RBAC i Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Przed uruchomieniem następującego przykładowego skryptu pamiętaj, aby zastąpić wartości symboli zastępczych, w tym nawiasów, z własnymi wartościami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
  
Następujące polecenie interfejsu wiersza polecenia 2,0 pokazuje, jak przypisać rolę platformy Azure do tożsamości usługi Azure AD na podstawie nazwy logowania. Aby uzyskać więcej informacji o przypisywaniu ról platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Zarządzanie dostępem przy użyciu RBAC i interfejsu wiersza polecenia platformy Azure](../articles/role-based-access-control/role-assignments-cli.md). 

Przed uruchomieniem następującego przykładowego skryptu pamiętaj, aby zastąpić wartości symboli zastępczych, w tym nawiasów, z własnymi wartościami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurowanie uprawnień systemu plików NTFS przy użyciu protokołu SMB

Po przypisaniu uprawnień na poziomie udziałów przy użyciu RBAC należy przypisać odpowiednie uprawnienia systemu plików NTFS do poziomu głównego, katalogu lub pliku. Należy traktować uprawnienia na poziomie udziałów jako strażnika wysokiego poziomu, który określa, czy użytkownik może uzyskać dostęp do udziału. Uprawnienia NTFS działają na bardziej szczegółowym poziomie, aby określić, jakie operacje może wykonać użytkownik na poziomie katalogu lub pliku.

Azure Files obsługuje pełny zestaw uprawnień systemu plików NTFS podstawowych i zaawansowanych. Uprawnienia NTFS można wyświetlać i konfigurować dla katalogów i plików w udziale plików platformy Azure, instalując udział, a następnie używając Eksploratora plików systemu Windows lub uruchamiając polecenie [icacls](/windows-server/administration/windows-commands/icacls) lub [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) . 

Aby skonfigurować system plików NTFS z uprawnieniami administratora, należy zainstalować udział przy użyciu klucza konta magazynu z maszyny wirtualnej przyłączonej do domeny. Postępuj zgodnie z instrukcjami w następnej sekcji, aby zainstalować udział plików platformy Azure z wiersza polecenia i odpowiednio skonfigurować uprawnienia systemu plików NTFS.

W katalogu głównym udziału plików są obsługiwane następujące zestawy uprawnień:

- Builtin\administratorzy: (OI) (CI) (F)
- NT NT\SYSTEM: (OI) (CI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (we/wy) (GR, GE)
- Użytkownicy NT AUTHORITY\Authenticated: (OI) (CI) (M)
- ZARZĄDZANIE NT\SYSTEM: (F)
- TWÓRCA-WŁAŚCICIEL: (OI) (WE/WY) (IO) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Instalowanie udziału plików z wiersza polecenia

Użyj polecenia Windows **net use** , aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić wartości symboli zastępczych w poniższym przykładzie własnymi wartościami. Aby uzyskać więcej informacji na temat instalowania udziałów plików, zobacz [Korzystanie z udziału plików platformy Azure w systemie Windows](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Jeśli występują problemy z nawiązywaniem połączenia z usługą Azure Files, zapoznaj się z [narzędziem do rozwiązywania problemów opublikowanym pod kątem Azure Files instalowania błędów w systemie Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Udostępniamy również [wskazówki](../articles/storage/files/storage-files-faq.md#on-premises-access) ułatwiające obejście tego problemu, gdy port 445 jest zablokowany. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurowanie uprawnień systemu plików NTFS przy użyciu Eksploratora plików systemu Windows

Użyj Eksploratora plików systemu Windows, aby przyznać pełne uprawnienia do wszystkich katalogów i plików w udziale plików, w tym katalogu głównego.

1. Otwórz Eksploratora plików systemu Windows i kliknij prawym przyciskiem myszy plik/katalog i wybierz polecenie **Właściwości**.
2. Wybierz kartę **zabezpieczenia** .
3. Wybierz pozycję **Edytuj.** Aby zmienić uprawnienia.
4. Możesz zmienić uprawnienia istniejących użytkowników lub wybrać przycisk **Dodaj...** , aby przyznać uprawnienia nowym użytkownikom.
5. W oknie monitu, aby dodać nowych użytkowników, wprowadź nazwę użytkownika docelowego, do którego chcesz udzielić uprawnień w polu **Wprowadź nazwy obiektów do wybrania** , a następnie wybierz pozycję **Sprawdź nazwy** , aby znaleźć pełną nazwę UPN użytkownika docelowego.
7.    Wybierz przycisk **OK**.
8.    Na karcie **zabezpieczenia** wybierz pozycję wszystkie uprawnienia, które chcesz udzielić nowemu użytkownikowi.
9.    Wybierz przycisk **Zastosuj**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurowanie uprawnień systemu plików NTFS przy użyciu icacls

Użyj następującego polecenia systemu Windows, aby przyznać pełne uprawnienia do wszystkich katalogów i plików w udziale plików, w tym katalogu głównego. Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Aby uzyskać więcej informacji na temat używania icacls do ustawiania uprawnień systemu plików NTFS i różnych typów obsługiwanych uprawnień, zobacz [informacje dotyczące wiersza polecenia dla icacls](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Instalowanie udziału plików z maszyny wirtualnej przyłączonej do domeny

Następujący proces sprawdza, czy udział plików i uprawnienia dostępu zostały prawidłowo skonfigurowane i że można uzyskać dostęp do udziału plików platformy Azure z poziomu maszyny wirtualnej przyłączonej do domeny. Należy pamiętać, że w efekcie przypisanie roli platformy Azure na poziomie udziału może zająć trochę czasu. 

Zaloguj się do maszyny wirtualnej przy użyciu tożsamości usługi Azure AD, do której udzielono uprawnień, jak pokazano na poniższej ilustracji. Jeśli włączono uwierzytelnianie AD DS lokalnego dla Azure Files, Użyj poświadczeń AD DS. W przypadku uwierzytelniania za pomocą usługi Azure AD DS Zaloguj się przy użyciu poświadczeń usługi Azure AD.

![Zrzut ekranu przedstawiający ekran logowania do usługi Azure AD na potrzeby uwierzytelniania użytkowników](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Użyj poniższego polecenia, aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami. Ponieważ użytkownik został uwierzytelniony, nie musisz podawać klucza konta magazynu, poświadczeń lokalnych AD DS ani poświadczeń AD DS platformy Azure. Obsługa logowania jednokrotnego jest obsługiwana w przypadku uwierzytelniania za pomocą lokalnego AD DS lub AD DS platformy Azure. Jeśli wystąpią problemy z instalowaniem przy użyciu poświadczeń AD DS, zapoznaj się z tematem [Rozwiązywanie problemów dotyczących Azure Files problemów w systemie Windows](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) .

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```