---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 200bf290543747cf9abab6113b8013e2eec852a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512737"
---
## <a name="assign-access-permissions-to-an-identity"></a>Przypisywanie uprawnień dostępu do tożsamości

Aby uzyskać Azure Files zasobów przy użyciu uwierzytelniania opartego na tożsamości, tożsamość (użytkownik, grupa lub nazwa główna usługi) musi mieć niezbędne uprawnienia na poziomie udziału. Ten proces jest podobny do określania uprawnień udziału systemu Windows, w którym można określić typ dostępu określonego użytkownika do udziału plików. Wskazówki w tej sekcji pokazują, jak przypisać uprawnienia do odczytu, zapisu lub usuwania dla udziału plików do tożsamości. 

Wprowadziliśmy trzy wbudowane role platformy Azure do udzielania użytkownikom uprawnień na poziomie udziału:

- **Czytnik udziałów SMB danych plików magazynu umożliwia** dostęp do odczytu w udziałach plików usługi Azure Storage za pośrednictwem SMB.
- **Współautor udziału SMB danych plików magazynu umożliwia** dostęp do odczytu, zapisu i usuwania w udziałach plików usługi Azure Storage za pośrednictwem SMB.
- **Współautor udziału SMB** danych plików magazynu z podwyższonym poziomem uprawnień umożliwia odczyt, zapis, usuwanie i modyfikowanie uprawnień NTFS w udziałach plików usługi Azure Storage za pośrednictwem SMB.

> [!IMPORTANT]
> Pełna kontrola administracyjna nad udziałem plików, w tym możliwość przeniesienia na własność pliku, wymaga użycia klucza konta magazynu. Kontrola administracyjna nie jest obsługiwana przy użyciu poświadczeń usługi Azure AD.

Możesz użyć interfejsu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby przypisać wbudowane role do tożsamości użytkownika usługi Azure AD w celu udzielenia uprawnień na poziomie udziału. Należy pamiętać, że przypisanie roli platformy Azure na poziomie udziału może zająć trochę czasu. 

> [!NOTE]
> Pamiętaj, [aby zsynchronizować AD DS z](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) usługą Azure AD, jeśli zamierzasz używać lokalnych poświadczeń AD DS do uwierzytelniania. Synchronizacja skrótów haseł AD DS z usługą Azure AD jest opcjonalna. Uprawnienie na poziomie udziału zostanie przyznane tożsamości usługi Azure AD, która jest synchronizowana z lokalnym AD DS.

Ogólne zalecenie to użycie uprawnień na poziomie udziału do zarządzania dostępem wysokiego poziomu do grupy usługi AD reprezentującej grupę użytkowników i tożsamości, a następnie wykorzystanie uprawnień systemu plików NTFS w celu uzyskania szczegółowej kontroli dostępu na poziomie katalogu/pliku. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Przypisywanie roli platformy Azure do tożsamości usługi AD

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby przypisać rolę platformy Azure do tożsamości usługi Azure AD przy użyciu Azure Portal [,](https://portal.azure.com)wykonaj następujące kroki:

1. W Azure Portal przejdź do udziału plików lub utwórz [udział plików.](../articles/storage/files/storage-how-to-create-file-share.md)
2. Wybierz **Access Control (IAM)**.
3. Wybieranie **opcji Dodaj przypisanie roli**
4. W bloku **Dodawanie przypisania roli** wybierz odpowiednią rolę wbudowaną (Czytelnik udziału SMB danych pliku magazynu, Współautor udziału SMB danych pliku magazynu) z listy **Rola.** Pozostaw **ustawienie domyślne** Przypisz dostęp do: użytkownik, grupa lub **nazwa główna usługi Azure AD.** Wybierz docelową tożsamość usługi Azure AD według nazwy lub adresu e-mail.
5. Wybierz **pozycję Zapisz,** aby zakończyć operację przypisywania roli.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

W poniższym przykładzie programu PowerShell pokazano, jak przypisać rolę platformy Azure do tożsamości usługi Azure AD na podstawie nazwy logowania. Aby uzyskać więcej informacji na temat przypisywania ról platformy Azure za pomocą programu PowerShell, zobacz [Zarządzanie dostępem](../articles/role-based-access-control/role-assignments-powershell.md)przy użyciu kontroli dostępu na Azure PowerShell.

Przed uruchomieniem poniższego przykładowego skryptu pamiętaj, aby zastąpić wartości symboli zastępczych, w tym nawiasy, własnymi wartościami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
  
Następujące polecenie interfejsu wiersza polecenia w wersji 2.0 pokazuje, jak przypisać rolę platformy Azure do tożsamości usługi Azure AD na podstawie nazwy logowania. Aby uzyskać więcej informacji na temat przypisywania ról platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure, zobacz Manage access by using RBAC and Azure CLI (Zarządzanie dostępem przy użyciu kontroli dostępu na rolach i [interfejsu wiersza polecenia platformy Azure).](../articles/role-based-access-control/role-assignments-cli.md) 

Przed uruchomieniem poniższego przykładowego skryptu pamiętaj, aby zastąpić wartości symboli zastępczych, w tym nawiasy, własnymi wartościami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurowanie uprawnień systemu plików NTFS za pośrednictwem SMB

Po przypisaniu uprawnień na poziomie udziału za pomocą kontroli dostępu na poziomie udziału należy przypisać odpowiednie uprawnienia ntfs na poziomie katalogu głównego, katalogu lub pliku. Uprawnienia na poziomie udziału należy myśleć jak o strażniku wysokiego poziomu, który określa, czy użytkownik może uzyskać dostęp do udziału. Uprawnienia NTFS działają na bardziej szczegółowym poziomie, aby określić, jakie operacje użytkownik może wykonać na poziomie katalogu lub pliku.

Azure Files obsługuje pełny zestaw uprawnień podstawowych i zaawansowanych systemu plików NTFS. Uprawnienia ntfs do katalogów i plików w udziałach plików platformy Azure można wyświetlać i konfigurować, ując udział, a następnie używając systemu Windows Eksplorator plików lub uruchamiając polecenie [icacls systemu](/windows-server/administration/windows-commands/icacls) Windows lub [Set-ACL.](/powershell/module/microsoft.powershell.security/set-acl) 

Aby skonfigurować system plików NTFS z uprawnieniami superużytkownika, należy zainstalować udział przy użyciu klucza konta magazynu z maszyny wirtualnej przyłączone do domeny. Postępuj zgodnie z instrukcjami w następnej sekcji, aby zainstalować udział plików platformy Azure z wiersza polecenia i odpowiednio skonfigurować uprawnienia systemu plików NTFS.

Następujące zestawy uprawnień są obsługiwane w katalogu głównym udziału plików:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Zainstaluj udział plików z wiersza polecenia

Użyj polecenia Windows **net use,** aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić wartości symboli zastępczych w poniższym przykładzie własnymi wartościami. Aby uzyskać więcej informacji na temat instalowanie udziałów plików, zobacz [Korzystanie z udziału plików platformy Azure w systemie Windows.](../articles/storage/files/storage-how-to-use-files-windows.md) 

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

Jeśli podczas nawiązywania połączenia z usługą Azure Files występują problemy, zapoznaj się z opublikowanym przez nas narzędziem do rozwiązywania problemów, aby uzyskać informacje o Azure Files instalacji w [systemie Windows.](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/) Zapewniamy również [wskazówki dotyczące](../articles/storage/files/storage-files-faq.md#on-premises-access) scenariuszy, w których zablokowany jest port 445. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurowanie uprawnień systemu plików NTFS przy użyciu systemu Windows Eksplorator plików

Użyj programu Windows Eksplorator plików, aby udzielić pełnych uprawnień wszystkim katalogom i plikom w ramach udziału plików, w tym katalogowi główneowi.

1. Otwórz okno Eksplorator plików Windows i kliknij prawym przyciskiem myszy plik/katalog, a następnie wybierz pozycję **Właściwości.**
2. Wybierz **kartę** Zabezpieczenia.
3. Wybierz **pozycję Edytuj.** w celu zmiany uprawnień.
4. Możesz zmienić uprawnienia istniejących użytkowników lub wybrać pozycję **Dodaj...,** aby udzielić uprawnień nowym użytkownikom.
5. W oknie monitu o dodanie nowych użytkowników wprowadź nazwę użytkownika  docelowego, do którego chcesz  udzielić uprawnień, w polu Wprowadź nazwy obiektów do wybrania, a następnie wybierz pozycję Sprawdź nazwy, aby znaleźć pełną nazwę UPN użytkownika docelowego.
7.    Wybierz przycisk **OK**.
8.    Na karcie **Zabezpieczenia** wybierz wszystkie uprawnienia, które chcesz przyznać noweowi użytkownikowi.
9.    Wybierz przycisk **Zastosuj**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurowanie uprawnień ntfs za pomocą icacls

Użyj następującego polecenia systemu Windows, aby udzielić pełnych uprawnień do wszystkich katalogów i plików w ramach udziału plików, w tym do katalogu głównego. Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Aby uzyskać więcej informacji na temat używania poleceń icacls do ustawienia uprawnień systemu plików NTFS i różnych typów obsługiwanych uprawnień, zobacz informacje w wierszu polecenia dla [polecenia icacls](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Zainstaluj udział plików z maszyny wirtualnej przyłączone do domeny

Poniższy proces sprawdza, czy udział plików i uprawnienia dostępu zostały poprawnie skonfigurowane i czy można uzyskać dostęp do udziału plików platformy Azure z maszyny wirtualnej przyłączone do domeny. Należy pamiętać, że przypisanie roli platformy Azure na poziomie udziału może zająć trochę czasu. 

Zaloguj się do maszyny wirtualnej przy użyciu tożsamości usługi Azure AD, której udzielono uprawnień, jak pokazano na poniższej ilustracji. Jeśli włączono lokalne uwierzytelnianie AD DS na Azure Files, użyj AD DS poświadczeń. Aby Azure AD DS uwierzytelniania, zaloguj się przy użyciu poświadczeń usługi Azure AD.

![Zrzut ekranu przedstawiający ekran logowania usługi Azure AD do uwierzytelniania użytkowników](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Użyj następującego polecenia, aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić wartości symboli zastępczych własnymi wartościami. Ponieważ użytkownik został uwierzytelniony, nie trzeba poświadczeń konta magazynu, lokalnego konta AD DS poświadczeń ani poświadczeń Azure AD DS magazynu. Środowisko logowania pojedynczego jest obsługiwane w przypadku uwierzytelniania za pomocą lokalnego AD DS lub Azure AD DS. Jeśli występują problemy z instalowaniem przy użyciu AD DS, zobacz Rozwiązywanie problemów z Azure Files w systemie [Windows,](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) aby uzyskać wskazówki.

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
