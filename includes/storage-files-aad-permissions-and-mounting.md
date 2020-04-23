---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/11/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 608c2619c19a2b5fa7e39c1ecb82be40ff4e83f4
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072624"
---
## <a name="2-assign-access-permissions-to-an-identity"></a>2. Przypisywanie uprawnień dostępu do tożsamości

Aby uzyskać dostęp do zasobów usługi Azure Files z uwierzytelnianiem opartym na tożsamości, tożsamość (użytkownik, grupa lub podmiot usługi) musi mieć niezbędne uprawnienia na poziomie udziału. Ten proces jest podobny do określania uprawnień udziału systemu Windows, w którym można określić typ dostępu określonego użytkownika do udziału plików. Wskazówki w tej sekcji pokazuje, jak przypisać uprawnienia do odczytu, zapisu lub usunięcia dla udziału plików do tożsamości. 

Wprowadziliśmy trzy wbudowane role platformy Azure do udzielania użytkownikom uprawnień na poziomie udziału:

- **Czytnik udostępniania plików danych magazynu umożliwia** dostęp do odczytu w udziałach plików usługi Azure Storage za pomocą pamięci SMB.
- **Współautor udostępniania danych pliku magazynu** umożliwia dostęp do odczytu, zapisu i usuwania w udziałach plików usługi Azure Storage za pomocą miejsca SMB.
- **Zwiększony współautor udziału w pliku danych magazynu** umożliwia odczyt, zapis, usuwanie i modyfikowanie uprawnień NTFS w udziałach plików usługi Azure Storage za pomocą usługi SMB.

> [!IMPORTANT]
> Pełna kontrola administracyjna udziału plików, w tym możliwość przejęcia na własność pliku, wymaga użycia klucza konta magazynu. Kontrola administracyjna nie jest obsługiwana przy użyciu poświadczeń usługi Azure AD.

Za pomocą witryny Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure można przypisać wbudowane role do tożsamości usługi Azure AD użytkownika w celu przyznania uprawnień na poziomie udziału.

> [!NOTE]
> Pamiętaj, aby [zsynchronizować poświadczenia usług AD DS z usługą Azure AD,](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) jeśli planujesz używać lokalnych usług AD DS do uwierzytelniania. Synchronizacja skrótów haseł z usług AD DS do usługi Azure AD jest opcjonalna. Uprawnienia na poziomie udziału zostaną przyznane tożsamości usługi Azure AD, która jest synchronizowana z lokalnych usług AD DS.

Ogólne zalecenie polega na użyciu uprawnień na poziomie udziału do zarządzania dostępem wysokiego poziomu do grupy usług AD reprezentującej grupę użytkowników i tożsamości, a następnie użyj uprawnień NTFS do szczegółowej kontroli dostępu na poziomie katalogu/pliku. 

#### <a name="azure-portal"></a>Azure Portal
Aby przypisać rolę RBAC do tożsamości usługi Azure AD przy użyciu [witryny Azure portal,](https://portal.azure.com)wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do udziału plików lub [Utwórz udział plików](../articles/storage/files/storage-how-to-create-file-share.md).
2. Wybierz **pozycję Kontrola dostępu (IAM)**.
3. Wybieranie **opcji Dodaj przypisanie roli**
4. W bloku **Dodawanie przypisania roli** wybierz odpowiednią rolę wbudowaną (Czytnik udostępniania danych SMB danych magazynu, współautor udostępniania danych pliku magazynu) z listy **Role.** Pozostaw **dostęp Przypisz** przy domyślnym ustawieniu: **użytkownik, grupa lub podmiot usługi Azure AD**. Wybierz docelową tożsamość usługi Azure AD według nazwy lub adresu e-mail.
5. Wybierz **pozycję Zapisz,** aby ukończyć operację przypisania roli.

#### <a name="powershell"></a>PowerShell

W poniższym przykładzie programu PowerShell pokazano, jak przypisać rolę RBAC do tożsamości usługi Azure AD na podstawie nazwy logowania. Aby uzyskać więcej informacji na temat przypisywania ról RBAC za pomocą programu PowerShell, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i azure powershell](../articles/role-based-access-control/role-assignments-powershell.md).

Przed uruchomieniem następującego przykładowego skryptu należy pamiętać o zastąpieniu wartości zastępczych, w tym nawiasów, własnymi wartościami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Interfejs wiersza polecenia
  
Następujące polecenie CLI 2.0 pokazuje, jak przypisać rolę RBAC do tożsamości usługi Azure AD na podstawie nazwy logowania. Aby uzyskać więcej informacji na temat przypisywania ról RBAC za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [Zarządzanie dostępem przy użyciu funkcji RBAC i interfejsu wiersza polecenia platformy Azure](../articles/role-based-access-control/role-assignments-cli.md). 

Przed uruchomieniem następującego przykładowego skryptu należy pamiętać o zastąpieniu wartości zastępczych, w tym nawiasów, własnymi wartościami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="3-configure-ntfs-permissions-over-smb"></a>3. Konfigurowanie uprawnień NTFS za 600 000 
Po przypisaniu uprawnień na poziomie udziału do funkcji RBAC należy przypisać odpowiednie uprawnienia NTFS na poziomie głównym, katalogu lub pliku. Pomyśl o uprawnieniach na poziomie udziału jako strażnik wysokiego poziomu, który określa, czy użytkownik może uzyskać dostęp do udziału. Uprawnienia NTFS działają na bardziej szczegółowym poziomie, aby określić, jakie operacje użytkownik może wykonać na poziomie katalogu lub pliku.

Usługa Azure Files obsługuje pełny zestaw podstawowych i zaawansowanych uprawnień systemu NTFS. Uprawnienia NTFS do katalogów i plików w udziale plików platformy Azure można wyświetlać i konfigurować, montując udział, a następnie używając Eksploratora plików systemu Windows lub uruchamiając polecenie [Icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) lub [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-acl) 

Aby skonfigurować system NTFS z uprawnieniami administratora, należy zainstalować udział przy użyciu klucza konta magazynu z maszyny wirtualnej przyłączone do domeny. Postępuj zgodnie z instrukcjami w następnej sekcji, aby zainstalować udział plików platformy Azure z wiersza polecenia i odpowiednio skonfigurować uprawnienia NTFS.

Następujące zestawy uprawnień są obsługiwane w katalogu głównym udziału plików:

- BUILTIN\Administratorzy:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Użytkownicy:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- UPRAWNIENIA NT\Uwierzytelnieni użytkownicy:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- WŁAŚCICIEL TWÓRCY:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Instalowanie udziału plików w wierszu polecenia

Użyj polecenia **użyj sieci** systemu Windows, aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić wartości zastępcze w poniższym przykładzie własnymi wartościami. Aby uzyskać więcej informacji na temat [instalowania](../articles/storage/files/storage-how-to-use-files-windows.md)udziałów plików, zobacz Używanie udziału plików platformy Azure w systemie Windows . 

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
```

### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurowanie uprawnień NTFS za pomocą Eksploratora plików systemu Windows
Eksploratora plików systemu Windows umożliwia przyznanie pełnych uprawnień wszystkim katalogom i plikom w ramach udziału plików, w tym katalogowi główne.

1. Otwórz Eksploratora plików w systemie Windows i kliknij prawym przyciskiem myszy plik/katalog i wybierz polecenie **Właściwości**.
2. Wybierz kartę **Zabezpieczenia.**
3. Wybierz **pozycję Edytuj..** , aby zmienić uprawnienia.
4. Można zmienić uprawnienia istniejących użytkowników lub wybrać **Dodaj...** aby udzielić uprawnień nowym użytkownikom.
5. W oknie monitu o dodanie nowych użytkowników wprowadź docelową nazwę użytkownika, której chcesz udzielić uprawnień, w polu **Wprowadź nazwy obiektów do zaznaczenia,** a następnie wybierz pozycję Sprawdź **nazwy,** aby znaleźć pełną nazwę nazwy UPN użytkownika docelowego.
7.    Kliknij przycisk **OK**.
8.    Na karcie **Zabezpieczenia** wybierz wszystkie uprawnienia, które chcesz udzielić nowemu użytkownikowi.
9.    Wybierz przycisk **Zastosuj**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurowanie uprawnień ntfs za pomocą icacls
Poniższe polecenie systemu Windows umożliwia przyznanie pełnych uprawnień do wszystkich katalogów i plików w ramach udziału plików, w tym do katalogu głównego. Pamiętaj, aby zastąpić wartości zastępcze w przykładzie własnymi wartościami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Aby uzyskać więcej informacji na temat używania icacls do ustawiania uprawnień NTFS i różnych typów obsługiwanych uprawnień, zobacz [odwołanie do wiersza polecenia dla icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="4-mount-a-file-share-from-a-domain-joined-vm"></a>4. Instalowanie udziału plików z maszyny wirtualnej przyłączanej do domeny

Poniższy proces sprawdza, czy udział plików i uprawnienia dostępu zostały skonfigurowane poprawnie i czy można uzyskać dostęp do udziału pliku platformy Azure z maszyny wirtualnej przyłączone do domeny. Należy pamiętać, że przypisanie roli RBAC na poziomie udziału może zająć trochę czasu, aby wejść w życie. 

Zaloguj się do maszyny Wirtualnej przy użyciu tożsamości usługi Azure AD, do której udzielono uprawnień, jak pokazano na poniższej ilustracji. Jeśli włączono lokalne uwierzytelnianie usług AD DS dla usług Azure Files, użyj poświadczeń usług AD DS. W przypadku uwierzytelniania usług Azure AD DS zaloguj się przy użyciu poświadczeń usługi Azure AD.

![Zrzut ekranu przedstawiający ekran logowania usługi Azure AD do uwierzytelniania użytkowników](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Użyj następującego polecenia, aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami. Ponieważ zostałeś uwierzytelniony, nie musisz podawać klucza konta magazynu, lokalnych poświadczeń usług AD DS ani poświadczeń usługi Azure AD DS. Środowisko logowania jednokrotnego jest obsługiwane w przypadku uwierzytelniania za pomocą lokalnych usług AD DS lub usług Azure AD DS. Jeśli napotkasz problemy z instalowaniem poświadczeń usług AD DS, zapoznaj się [z rozwiązywaniem problemów z plikami azure w systemie Windows,](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems) aby uzyskać wskazówki.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
