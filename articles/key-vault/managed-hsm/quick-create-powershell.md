---
title: Tworzenie i pobieranie atrybutów klucza zarządzanego w programie Azure Key Vault — Azure PowerShell
description: Przewodnik Szybki start przedstawiający sposób ustawienia i pobrania klucza zarządzanego z usługi Azure Key Vault użyciu Azure PowerShell
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom:
- mode-api
ms.openlocfilehash: ba1cd8d6b1410be30eefe9dca9675daaf6c16256
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534657"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Szybki start: konfigurowanie i pobieranie klucza zarządzanego z usługi Azure Key Vault pomocą programu PowerShell

W tym przewodniku Szybki start utworzysz magazyn kluczy w Azure Key Vault za pomocą Azure PowerShell. Azure Key Vault to usługa w chmurze, która działa jako bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Aby uzyskać więcej informacji na Key Vault, zapoznaj się z tematem [Omówienie.](../general/overview.md) Azure PowerShell służy do tworzenia zasobów platformy Azure i zarządzania nimi za pomocą poleceń lub skryptów. Po zakończeniu tego procesu będziesz przechowywać klucz.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli postanowisz zainstalować program PowerShell i używać go lokalnie, to ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Wpisz polecenie `$PSVersionTable.PSVersion`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby utworzyć połączenie z platformą Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Użyj Azure PowerShell cmdlet [New-AzResourceGroup,](/powershell/module/az.resources/new-azresourcegroup) aby utworzyć grupę zasobów o nazwie *myResourceGroup* w *lokalizacji westus.* 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Identyfikator podmiotu zabezpieczeń zostanie zwrócony w formacie "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

## <a name="create-a-managed-hsm"></a>Tworzenie zarządzanego modułu HSM

Użyj Azure PowerShell cmdlet [New-AzKeyVaultManagedHsm,](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) aby utworzyć nowy Key Vault HSM. Konieczne będzie podanie pewnych informacji:

- Nazwa zarządzanego modułu HSM: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry (0–9), litery (a–z, A–Z) i łączniki (-)

  > [!Important]
  > Każdy zarządzany moduł HSM musi mieć unikatową nazwę. Zastąp <your-unique-managed-hsm-name> nazwą zarządzanego modułu HSM w poniższych przykładach.

- Nazwa grupy zasobów: **myResourceGroup.**
- Lokalizacja: **EastUS**.
- Identyfikator podmiotu zabezpieczeń: przekaż Azure Active Directory identyfikator podmiotu zabezpieczeń uzyskany w ostatniej sekcji do parametru "Administrator". 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

Dane wyjściowe tego polecenia cmdlet pokazują właściwości nowo utworzonego zarządzanego modułu HSM. Zanotuj dwie poniższe właściwości:

- **Nazwa zarządzanego modułu HSM:** nazwa podana parametrowi --name powyżej.
- **Identyfikator URI magazynu:** w tym przykładzie jest https:// &lt; nazwa &gt; .vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="activate-your-managed-hsm"></a>Aktywowanie zarządzanego modułu HSM

Wszystkie polecenia płaszczyzny danych są wyłączone do momentu aktywowania modułu HSM. Nie będzie można tworzyć kluczy ani przypisywać ról. Tylko wyznaczeni administratorzy, którzy zostali przypisani podczas tworzenia polecenia, mogą aktywować moduł HSM. Aby aktywować moduł HSM, należy pobrać [domenę zabezpieczeń](security-domain.md).

Aby aktywować moduł HSM, potrzebne są:
- Co najmniej 3 pary kluczy RSA (maksymalnie 10)
- Określ minimalną liczbę kluczy wymaganych do odszyfrowania domeny zabezpieczeń (kworum)

Aby aktywować moduł HSM, należy wysłać co najmniej 3 (maksymalnie 10) publicznych kluczy RSA do modułu HSM. Moduł HSM szyfruje domenę zabezpieczeń przy użyciu tych kluczy i wysyła ją z powrotem. Po pomyślnym ukończeniu pobierania tej domeny zabezpieczeń moduł HSM jest gotowy do użycia. Należy również określić kworum, czyli minimalną liczbę kluczy prywatnych wymaganych do odszyfrowania domeny zabezpieczeń.

W poniższym przykładzie pokazano, jak `openssl` używać funkcji (dostępnej dla systemu Windows [tutaj](https://slproweb.com/products/Win32OpenSSL.html)) do wygenerowania 3 certyfikatu z podpisem własnym.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Utwórz i przechowaj bezpiecznie pary kluczy RSA i plik domeny zabezpieczeń wygenerowany w tym kroku.

Użyj polecenia cmdlet [Export-Azure PowerShell Export-AzKeyVaultSecurityDomain,](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) aby pobrać domenę zabezpieczeń i aktywować zarządzany moduł HSM. W poniższym przykładzie użyto 3 par kluczy RSA (dla tego polecenia potrzebne są tylko klucze publiczne) i określono kworum na 2.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Przechowaj bezpiecznie plik domeny zabezpieczeń i pary kluczy RSA. Będą one potrzebne do odzyskiwania po awarii lub tworzenia innego zarządzanego modułu HSM, który współużytkuje tę samą domenę zabezpieczeń, aby można było udostępniać klucze.

Po pomyślnym pobraniu domeny zabezpieczeń moduł HSM będzie w stanie aktywnym i będzie gotowy do użycia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Key Vault przechowywany w nim certyfikat. Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, przejdź do poniższych artykułów.

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zapoznaj się z odwołaniem do [Azure PowerShell Key Vault cmdlet](/powershell/module/az.keyvault/)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-overview.md)
