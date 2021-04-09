---
title: Tworzenie i pobieranie atrybutów klucza zarządzanego w Azure Key Vault — Azure PowerShell
description: Przewodnik Szybki Start przedstawiający sposób ustawiania i pobierania klucza zarządzanego z Azure Key Vault przy użyciu Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/26/2021
ms.author: mbaldwin
ms.openlocfilehash: 943555e9f7a26530a075aee27dd310d96974e652
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99072915"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Szybki Start: Ustawianie i pobieranie klucza zarządzanego z Azure Key Vault przy użyciu programu PowerShell

W tym przewodniku szybki start utworzysz Magazyn kluczy w Azure Key Vault z Azure PowerShell. Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na Key Vault można zapoznać się z [omówieniem](../general/overview.md). Azure PowerShell służy do tworzenia zasobów platformy Azure i zarządzania nimi przy użyciu poleceń lub skryptów. Po zakończeniu tej operacji będziesz przechowywać klucz.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, to ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Wpisz polecenie `$PSVersionTable.PSVersion`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Użyj polecenia cmdlet Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) , aby utworzyć grupę zasobów o nazwie Moja *zasobów* w lokalizacji *zachodniej* . 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Identyfikator podmiotu zabezpieczeń zostanie zwrócony w formacie "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

## <a name="create-a-managed-hsm"></a>Tworzenie zarządzanego modułu HSM

Użyj polecenia cmdlet Azure PowerShell [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) , aby utworzyć nowy Key Vault ZARZĄDZANY moduł HSM. Konieczne będzie podanie pewnych informacji:

- Nazwa zarządzanego modułu HSM: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry (0-9), litery (a-z, A-Z) i łączniki (-)

  > [!Important]
  > Każdy zarządzany moduł HSM musi mieć unikatową nazwę. Zastąp <identyfikatorem modułu HSM, który jest unikatowym identyfikatorem modułu HSM,> nazwą zarządzanego modułu zarządzania w poniższych przykładach.

- Nazwa grupy **zasobów: Grupa zasobów.**
- Lokalizacja: **wschód**.
- Identyfikator podmiotu zabezpieczeń: Przekaż Azure Active Directory Identyfikator podmiotu zabezpieczeń uzyskany w ostatniej sekcji do parametru "Administrator". 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

Dane wyjściowe tego polecenia cmdlet pokazują właściwości nowo utworzonego zarządzanego modułu HSM. Zanotuj dwie poniższe właściwości:

- **Nazwa zarządzanego modułu HSM**: nazwa podana w powyższym parametrze--Name.
- **Identyfikator URI magazynu**: w tym przykładzie jest to https:// &lt; -unikatowy-zarządzany-HSM-Name &gt; . Vault.Azure.NET/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="activate-your-managed-hsm"></a>Aktywuj zarządzany moduł HSM

Wszystkie polecenia płaszczyzny danych są wyłączone do momentu aktywowania modułu HSM. Nie będzie można tworzyć kluczy ani przypisywać ról. Tylko Wyznaczeni Administratorzy przypisani podczas tworzenia polecenia mogą aktywować moduł HSM. Aby aktywować moduł HSM, należy pobrać [domenę zabezpieczeń](security-domain.md).

Aby aktywować moduł HSM, potrzebujesz:
- Minimum 3 pary kluczy RSA (maksymalnie 10)
- Określ minimalną liczbę kluczy potrzebną do odszyfrowania domeny zabezpieczeń (kworum)

Aby aktywować moduł HSM, należy wysłać co najmniej 3 (maksymalnie 10) kluczy publicznych RSA do modułu HSM. Moduł HSM szyfruje domenę zabezpieczeń przy użyciu tych kluczy i wysyła je z powrotem. Po pomyślnym ukończeniu pobierania tej domeny zabezpieczeń moduł HSM jest gotowy do użycia. Należy również określić kworum, czyli minimalną liczbę kluczy prywatnych wymaganych do odszyfrowania domeny zabezpieczeń.

W poniższym przykładzie pokazano, jak używać `openssl` programu (dostępnego dla systemu Windows w [tym miejscu](https://slproweb.com/products/Win32OpenSSL.html)) do generowania certyfikatu z podpisem własnym.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Utwórz i Zapisz pary kluczy RSA i plik domeny zabezpieczeń, które zostały wygenerowane w tym kroku bezpiecznie.

Użyj Azure PowerShell polecenie cmdlet [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) , aby pobrać domenę zabezpieczeń i aktywować ZARZĄDZANY moduł HSM. W poniższym przykładzie zastosowano 3 pary kluczy RSA (tylko klucze publiczne są konieczne dla tego polecenia) i ustawia kworum na 2.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Zapisz plik domeny zabezpieczeń i pary kluczy RSA bezpiecznie. Będą one potrzebne do odzyskiwania po awarii lub tworzenia innego zarządzanego modułu HSM, który ma udział w tej samej domenie zabezpieczeń, dzięki czemu mogą udostępniać klucze.

Po pomyślnym pobraniu domeny zabezpieczeń moduł HSM będzie w stanie aktywny i będzie gotowy do użycia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault i Zapisano w nim certyfikat. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zobacz informacje dotyczące [Azure PowerShell poleceń cmdlet Key Vault](/powershell/module/az.keyvault/)
- Zapoznaj się z [omówieniem zabezpieczeń Key Vault](../general/security-overview.md)
