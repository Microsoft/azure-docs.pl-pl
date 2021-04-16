---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d19c656946817b06cd620d8a48073bed8299af7d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502264"
---
Użyj Azure PowerShell cmdlet [New-AzKeyVault,](/powershell/module/az.keyvault/new-azkeyvault) aby utworzyć Key Vault w grupie zasobów z poprzedniego kroku. Konieczne będzie podanie pewnych informacji:

- Nazwa magazynu kluczy: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry (0–9), litery (a–z, A–Z) i łączniki (-)

  > [!Important]
  > Każdy magazyn kluczy musi mieć unikatową nazwę. Zastąp <your-unique-keyvault-name> nazwą magazynu kluczy w poniższych przykładach.

- Nazwa grupy zasobów: **myResourceGroup**.
- Lokalizacja: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

Dane wyjściowe tego polecenia cmdlet pokazują właściwości nowo utworzonej usługi Key Vault. Zanotuj dwie poniższe właściwości:

- **Nazwa magazynu:** nazwa podana powyżej parametru --name.
- **Identyfikator URI magazynu:** w tym przykładzie jest https:// &lt; your-unique-keyvault-name &gt; .vault.azure.net/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.
