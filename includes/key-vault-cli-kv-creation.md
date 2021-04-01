---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: a87fbbb276fd8813492cc293bc08b958ee3d6b7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070286"
---
Użyj interfejsu wiersza polecenia platformy Azure [AZ Resource Create](/cli/azure/keyvault#az_keyvault_create) , aby utworzyć Key Vault w grupie zasobów z poprzedniego kroku. Konieczne będzie podanie pewnych informacji:

- Nazwa magazynu kluczy: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry (0-9), litery (a-z, A-Z) i łączniki (-)

  > [!Important]
  > Każdy Magazyn kluczy musi mieć unikatową nazwę. Zastąp <unikatowym identyfikatorem magazynu kluczy> nazwą magazynu klucza w poniższych przykładach.

- Nazwa grupy **zasobów: Grupa zasobów.**
- Lokalizacja: **wschód**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

Dane wyjściowe tego polecenia przedstawiają właściwości nowo utworzonego magazynu kluczy. Zanotuj dwie poniższe właściwości:

- **Nazwa magazynu**: podana nazwa powyższego parametru--name.
- **Identyfikator URI magazynu**: w tym przykładzie jest to https:// &lt; — unikatowy — Nazwa magazynu kluczy &gt; . Vault.Azure.NET/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.
