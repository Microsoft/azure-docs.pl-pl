---
title: Kontroluj, co użytkownik może zrobić na poziomie pliku — udziały plików platformy Azure
description: Dowiedz się, jak skonfigurować uprawnienia do lokalnego uwierzytelniania AD DS w udziałach plików na platformie Azure. Umożliwienie korzystania z szczegółowej kontroli dostępu.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 698b4ebedfc9b41e8c5732a0a81226a971d65585
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470767"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Część trzecia: Konfigurowanie uprawnień na poziomie katalogu i pliku za pośrednictwem protokołu SMB 

Przed rozpoczęciem pracy z tym artykułem upewnij się, że wykonano Poprzedni artykuł, [Przypisz uprawnienia na poziomie udziału do tożsamości](storage-files-identity-ad-ds-assign-permissions.md) , aby upewnić się, że Twoje uprawnienia na poziomie udziału zostały wprowadzone.

Po przypisaniu uprawnień na poziomie udziału za pomocą usługi Azure RBAC należy skonfigurować odpowiednie listy ACL systemu Windows na poziomie katalogu głównego, katalogu lub pliku, aby skorzystać z szczegółowej kontroli dostępu. Należy traktować uprawnienia na poziomie udziałów platformy Azure jako strażnika wysokiego poziomu, który określa, czy użytkownik może uzyskać dostęp do udziału. Listy ACL systemu Windows działają na bardziej szczegółowym poziomie, aby określić, jakie operacje może wykonywać użytkownik na poziomie katalogu lub pliku. Uprawnienia na poziomie udziału i pliku/katalogu są wymuszane, gdy użytkownik próbuje uzyskać dostęp do pliku/katalogu, więc jeśli istnieje różnica między jednym z nich, zostanie zastosowane tylko najbardziej restrykcyjne. Na przykład jeśli użytkownik ma dostęp do odczytu/zapisu na poziomie pliku, ale tylko do odczytu na poziomie udziału, może odczytać tylko ten plik. Ta sama wartość powinna być równa true, jeśli została odwrócona, a użytkownik miał dostęp do odczytu/zapisu na poziomie udziału, ale tylko do odczytu na poziomie pliku może nadal tylko odczytać plik.

## <a name="azure-rbac-permissions"></a>Uprawnienia kontroli RBAC platformy Azure

Poniższa tabela zawiera uprawnienia usługi Azure RBAC powiązane z tą konfiguracją:


| Wbudowana rola  | Uprawnienie NTFS  | Uzyskany dostęp  |
|---------|---------|---------|
|Czytelnik udziału SMB danych w pliku magazynu | Pełna kontrola, modyfikowanie, Odczyt, zapis, wykonywanie | Odczyt i wykonanie  |
|     |   Odczyt |     Odczyt  |
|Współautor udziału SMB danych w pliku magazynu  |  Pełna kontrola    |  Modyfikowanie, Odczyt, zapis, wykonywanie |
|     |  Modyfikowanie         |  Modyfikowanie    |
|     |  Odczyt i wykonanie |  Odczyt i wykonanie |
|     |  Odczyt           |  Odczyt    |
|     |  Zapisywanie          |  Zapisywanie   |
|Współautor udziału SMB danych w pliku magazynu z podwyższonym poziomem uprawnień | Pełna kontrola  |  Modyfikowanie, Odczyt, zapis, Edycja, wykonywanie |
|     |  Modyfikowanie          |  Modyfikowanie |
|     |  Odczyt i wykonanie  |  Odczyt i wykonanie |
|     |  Odczyt            |  Odczyt   |
|     |  Zapisywanie           |  Zapisywanie  |



## <a name="supported-permissions"></a>Obsługiwane uprawnienia

Azure Files obsługuje pełny zestaw podstawowych i zaawansowanych list ACL systemu Windows. Można wyświetlać i konfigurować listy ACL systemu Windows dla katalogów i plików w udziale plików platformy Azure, instalując udział, a następnie używając Eksploratora plików systemu Windows, uruchamiając polecenie [icacls](/windows-server/administration/windows-commands/icacls) systemu Windows lub polecenie [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) . 

Aby skonfigurować listy ACL z uprawnieniami administratora, należy zainstalować udział przy użyciu klucza konta magazynu z maszyny wirtualnej przyłączonej do domeny. Postępuj zgodnie z instrukcjami w następnej sekcji, aby zainstalować udział plików platformy Azure z wiersza polecenia i skonfigurować listy ACL systemu Windows.

W katalogu głównym udziału plików znajdują się następujące uprawnienia:

- Builtin\administratorzy: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (we/wy) (GR, GE)
- Użytkownicy NT AUTHORITY\Authenticated: (OI) (CI) (M)
- NT NT\SYSTEM: (OI) (CI) (CI) (F)
- ZARZĄDZANIE NT\SYSTEM: (F)
- TWÓRCA-WŁAŚCICIEL: (OI) (WE/WY) (IO) (F)

|Użytkownicy|Definicja|
|---|---|
|BUILTIN\Administrators|Wszyscy użytkownicy, którzy są administratorami domeny środowiska Premium AD DS.
|BUILTIN\Users|Wbudowana grupa zabezpieczeń w usłudze AD. Domyślnie obejmuje użytkowników NT AUTHORITY\Authenticated. W przypadku tradycyjnego serwera plików można skonfigurować definicję członkostwa na serwer. W przypadku Azure Files nie istnieje serwer hostingu, dlatego BUILTIN\Users obejmuje ten sam zestaw użytkowników jako użytkowników NT AUTHORITY\Authenticated.|
|ZARZĄDZANIE NT\SYSTEM|Konto usługi systemu operacyjnego serwera plików. Takie konto usługi nie ma zastosowania w kontekście Azure Files. Jest ona uwzględniona w katalogu głównym, aby była spójna ze środowiskiem Windows Files dla scenariuszy hybrydowych.|
|Użytkownicy NT AUTHORITY\Authenticated|Wszyscy użytkownicy w usłudze AD, którzy mogą uzyskać prawidłowy token Kerberos.|
|WŁAŚCICIEL TWÓRCY|Każdy obiekt albo katalog lub plik ma właściciela dla tego obiektu. Jeśli istnieją listy ACL przypisane do elementu "Twórca-właściciel" tego obiektu, użytkownik będący właścicielem tego obiektu ma uprawnienia do obiektu zdefiniowanego przez listę ACL.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Instalowanie udziału plików z wiersza polecenia

Użyj polecenia systemu Windows, `net use` Aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić wartości symboli zastępczych w poniższym przykładzie własnymi wartościami. Aby uzyskać więcej informacji na temat instalowania udziałów plików, zobacz [Korzystanie z udziału plików platformy Azure w systemie Windows](storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Jeśli występują problemy z nawiązywaniem połączenia z usługą Azure Files, zapoznaj się z [narzędziem rozwiązywania problemów opublikowanym pod kątem Azure Files instalowania błędów w systemie Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Udostępniamy również [wskazówki](./storage-files-faq.md#on-premises-access) ułatwiające obejście tego problemu, gdy port 445 jest zablokowany. 

## <a name="configure-windows-acls"></a>Skonfiguruj listy ACL systemu Windows

Po zainstalowaniu udziału plików z kluczem konta magazynu należy skonfigurować listy ACL systemu Windows (znane także jako uprawnienia NTFS). Listy ACL systemu Windows można skonfigurować przy użyciu Eksploratora plików systemu Windows lub icacls.

Jeśli masz katalogi lub pliki na lokalnych serwerach plików z listami DACL systemu Windows skonfigurowanymi pod kątem tożsamości AD DS, możesz skopiować ją do poziomu Azure Files utrwalania list ACL przy użyciu tradycyjnych narzędzi do kopiowania plików, takich jak Robocopy lub [Azure AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases). Jeśli katalogi i pliki są warstwami do Azure Files za pośrednictwem Azure File Sync, listy ACL są przenoszone i utrwalane w formacie natywnym.

### <a name="configure-windows-acls-with-icacls"></a>Konfigurowanie list ACL systemu Windows przy użyciu icacls

Użyj następującego polecenia systemu Windows, aby przyznać pełne uprawnienia do wszystkich katalogów i plików w udziale plików, w tym katalogu głównego. Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Aby uzyskać więcej informacji na temat używania icacls do ustawiania list ACL systemu Windows i różnych typów obsługiwanych uprawnień, zobacz [informacje dotyczące wiersza polecenia dla icacls](/windows-server/administration/windows-commands/icacls).

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Konfigurowanie list ACL systemu Windows za pomocą Eksploratora plików systemu Windows

Użyj Eksploratora plików systemu Windows, aby przyznać pełne uprawnienia do wszystkich katalogów i plików w udziale plików, w tym katalogu głównego. Jeśli nie można prawidłowo załadować informacji o domenie usługi AD w Eksploratorze plików systemu Windows, jest to prawdopodobnie spowodowane konfiguracją zaufania w środowisku usługi AD Premium. Komputer kliencki nie mógł nawiązać połączenia z kontrolerem domeny usługi AD zarejestrowanym do Azure Files uwierzytelniania. W takim przypadku należy użyć icacls do configurating list ACL systemu Windows.

1. Otwórz Eksploratora plików systemu Windows i kliknij prawym przyciskiem myszy plik/katalog i wybierz polecenie **Właściwości**.
1. Wybierz kartę **zabezpieczenia** .
1. Wybierz pozycję **Edytuj.** Aby zmienić uprawnienia.
1. Możesz zmienić uprawnienia istniejących użytkowników lub wybrać przycisk **Dodaj...** , aby przyznać uprawnienia nowym użytkownikom.
1. W oknie monitu do dodawania nowych użytkowników wprowadź nazwę docelowej nazwy użytkownika, do której chcesz udzielić uprawnień, w polu **Wprowadź nazwy obiektów do wybrania** , a następnie wybierz pozycję **Sprawdź nazwy** , aby znaleźć pełną nazwę UPN użytkownika docelowego.
1.    Wybierz przycisk **OK**.
1.    Na karcie **zabezpieczenia** wybierz pozycję wszystkie uprawnienia, które chcesz udzielić nowemu użytkownikowi.
1.    Wybierz przycisk **Zastosuj**.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy ta funkcja jest włączona i skonfigurowana, przejdź do następnego artykułu, w którym można zainstalować udział plików platformy Azure z maszyny wirtualnej przyłączonej do domeny.

[Część czwarta: Instalowanie udziału plików z maszyny wirtualnej przyłączonej do domeny](storage-files-identity-ad-ds-mount-file-share.md)
