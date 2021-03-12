---
title: Przekazywanie poświadczeń do platformy Azure przy użyciu konfiguracji żądanego stanu
description: Informacje o sposobie bezpiecznego przekazywania poświadczeń do usługi Azure Virtual Machines przy użyciu konfiguracji żądanego stanu (DSC) programu PowerShell.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 05/02/2018
ms.openlocfilehash: 6817dd6baacd835b7d433177ff18af1238ee44a6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560083"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Przekaż poświadczenia do procedury obsługi usługi Azure DSCExtension

Ten artykuł dotyczy rozszerzenia konfiguracji żądanego stanu (DSC) dla platformy Azure. Aby zapoznać się z omówieniem procedury obsługi rozszerzeń DSC, zobacz [wprowadzenie do programu obsługi rozszerzenia konfiguracji żądanego stanu platformy Azure](dsc-overview.md).

 

## <a name="pass-in-credentials"></a>Poświadczenia przekazywania

W ramach procesu konfiguracji może być konieczne skonfigurowanie kont użytkowników, usług dostępu lub zainstalowanie programu w kontekście użytkownika. Aby wykonać te czynności, musisz podać poświadczenia.

Do konfigurowania konfiguracji sparametryzowanej można użyć DSC. W konfiguracji sparametryzowanej poświadczenia są przesyłane do konfiguracji i bezpiecznie przechowywane w plikach MOF. Program obsługi rozszerzeń platformy Azure upraszcza zarządzanie poświadczeniami, zapewniając automatyczne zarządzanie certyfikatami.

Następujący skrypt konfiguracji DSC tworzy konto użytkownika lokalnego z określonym hasłem:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Ważne jest, aby dołączyć **węzeł localhost** w ramach konfiguracji. Procedura obsługi rozszerzeń szuka instrukcji **Node localhost** . W przypadku braku tej instrukcji nie działają następujące czynności. Ważne jest również uwzględnienie rzutowanie **[PsCredential]**. Ten konkretny typ wyzwala rozszerzenie w celu zaszyfrowania poświadczenia.

Aby opublikować ten skrypt w usłudze Azure Blob Storage:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Aby ustawić rozszerzenie DSC platformy Azure i podać poświadczenia:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Jak jest zabezpieczone poświadczenie

Uruchomienie tego kodu powoduje wpisanie prośby o poświadczenie. Po podaniu poświadczeń zostanie ono krótko zapisane w pamięci. Po opublikowaniu poświadczenia za pomocą polecenia cmdlet **Set-AzVMDscExtension** poświadczenia są przesyłane za pośrednictwem protokołu HTTPS do maszyny wirtualnej. Na maszynie wirtualnej platforma Azure przechowuje poświadczenia zaszyfrowane na dysku przy użyciu lokalnego certyfikatu maszyny wirtualnej. Poświadczenie jest z chwilą odszyfrowywane w pamięci, a następnie ponownie szyfrowane w celu przekazania go do DSC.

Ten proces różni się od [używania bezpiecznych konfiguracji bez obsługi rozszerzenia](/powershell/scripting/dsc/pull-server/securemof). Środowisko platformy Azure umożliwia bezpieczne przesyłanie danych konfiguracji za pośrednictwem certyfikatów. W przypadku korzystania z programu obsługi rozszerzeń DSC nie trzeba podawać **$CertificatePath** ani **$CertificateID** /  **$Thumbprint** wpisów w **ConfigurationData**.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj [się z wprowadzeniem do procedury obsługi rozszerzenia usługi Azure DSC](dsc-overview.md).
- Zapoznaj się z [szablonem Azure Resource Manager dla rozszerzenia DSC](dsc-template.md).
- Aby uzyskać więcej informacji na temat programu PowerShell DSC, przejdź do [centrum dokumentacji programu PowerShell](/powershell/scripting/dsc/overview/overview).
- Aby uzyskać więcej funkcji, którymi można zarządzać za pomocą programu PowerShell DSC i więcej zasobów DSC, przejrzyj [galerię programu PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).