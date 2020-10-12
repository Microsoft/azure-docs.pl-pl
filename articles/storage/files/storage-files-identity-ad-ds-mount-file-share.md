---
title: Instalowanie udziału plików platformy Azure na przyłączonym do AD DS maszynie wirtualnej
description: Dowiedz się, jak zainstalować udział plików na lokalnych komputerach przyłączonych do Active Directory Domain Services.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: 9807563c768b82c823ff754aaa679ddc917bf62d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87535063"
---
# <a name="part-four-mount-a-file-share-from-a-domain-joined-vm"></a>Część czwarta: Instalowanie udziału plików z maszyny wirtualnej przyłączonej do domeny

Przed rozpoczęciem tego artykułu upewnij się, że wykonano Poprzedni artykuł, [Skonfiguruj uprawnienia katalogu i poziomu plików za pośrednictwem protokołu SMB](storage-files-identity-ad-ds-configure-permissions.md).

Proces opisany w tym artykule sprawdza, czy udział plików i uprawnienia dostępu są poprawnie skonfigurowane oraz czy można uzyskać dostęp do udziału plików platformy Azure z poziomu maszyny wirtualnej przyłączonej do domeny. Przypisanie roli platformy Azure na poziomie udziału może zająć trochę czasu. 

Zaloguj się do klienta przy użyciu poświadczeń, do których udzielono uprawnień, jak pokazano na poniższej ilustracji.

![Zrzut ekranu przedstawiający ekran logowania do usługi Azure AD na potrzeby uwierzytelniania użytkowników](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

## <a name="mounting-prerequisites"></a>Wymagania wstępne dotyczące instalowania

Przed zainstalowaniem udziału plików upewnij się, że zostały utracone następujące wymagania wstępne:

- Jeśli udział plików jest instalowany z klienta, który wcześniej zainstalował udział plików przy użyciu klucza konta magazynu, należy się upewnić, że udział został odłączony, usunięty z poświadczeń trwałych klucza konta magazynu i obecnie jest używany AD DS poświadczenia na potrzeby uwierzytelniania.
- Twój klient musi mieć linię wglądu w AD DS. Jeśli maszyna lub maszyna wirtualna jest poza siecią zarządzaną przez AD DS, musisz włączyć sieć VPN, aby uzyskać dostęp do AD DS na potrzeby uwierzytelniania.

Zastąp wartości zastępcze własnymi wartościami, a następnie użyj następującego polecenia, aby zainstalować udział plików platformy Azure:

```PSH
# Always mount your share using.file.core.windows.net, even if you setup a private endpoint for your share.
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

Jeśli wystąpią problemy z instalowaniem przy użyciu poświadczeń AD DS, zapoznaj się z tematem [nie można zainstalować Azure Files z poświadczeniami usługi AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) , aby uzyskać wskazówki.

W przypadku pomyślnego zamontowania udziału plików pomyślnie włączono i skonfigurowano lokalne uwierzytelnianie AD DS dla udziałów plików platformy Azure.

## <a name="next-steps"></a>Następne kroki

Jeśli tożsamość utworzona w AD DS do reprezentowania konta magazynu znajduje się w domenie lub jednostce organizacyjnej, która wymusza rotację haseł, przejdź do następnego artykułu, aby uzyskać instrukcje dotyczące aktualizowania hasła:

[Zaktualizuj hasło tożsamości konta magazynu w AD DS](storage-files-identity-ad-ds-update-password.md)
