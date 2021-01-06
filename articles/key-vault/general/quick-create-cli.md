---
title: Szybki Start — tworzenie Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure
description: Przewodnik Szybki Start przedstawiający sposób tworzenia Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e29a692e3fdad1bea7132b3bed50444c7398ba46
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936315"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Szybki Start: Tworzenie magazynu kluczy przy użyciu interfejsu wiersza polecenia platformy Azure

Azure Key Vault to usługa w chmurze, która zapewnia bezpieczny magazyn [kluczy](../keys/index.yml), wpisów [tajnych](../secrets/index.yml)i [certyfikatów](../certificates/index.yml). Aby uzyskać więcej informacji na temat Key Vault, zobacz [Informacje o Azure Key Vault](overview.md); Aby uzyskać więcej informacji na temat tego, co może być przechowywane w magazynie kluczy, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](about-keys-secrets-certificates.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ten przewodnik Szybki Start wymaga wersji 2.0.4 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. W poniższym przykładzie pokazano tworzenie grupy zasobów o nazwie *ContosoResourceGroup* w lokalizacji *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Utwórz Key Vault w grupie zasobów z poprzedniego kroku. Konieczne będzie podanie pewnych informacji:

- Nazwa magazynu kluczy: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry (0-9), litery (a-z, A-Z) i łączniki (-)

  > [!Important]
  > Każdy Magazyn kluczy musi mieć unikatową nazwę. Zastąp <unikatowym identyfikatorem magazynu kluczy> nazwą magazynu klucza w poniższych przykładach.

- Nazwa grupy **zasobów: Grupa zasobów.**
- Lokalizacja: **wschód**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

Dane wyjściowe tego polecenia cmdlet pokazują właściwości nowo utworzonej usługi Key Vault. Zanotuj dwie poniższe właściwości:

- **Nazwa magazynu**: podana nazwa powyższego parametru--name.
- **Identyfikator URI magazynu**: w tym przykładzie jest to https:// &lt; — unikatowy — Nazwa magazynu kluczy &gt; . Vault.Azure.NET/. Aplikacje korzystające z magazynu za pomocą jego interfejsu API REST muszą używać tego identyfikatora URI.

Twoje konto platformy Azure jest teraz jedynym kontem z uprawnieniami do wykonywania jakichkolwiek operacji na tym nowym magazynie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz korzystać z kolejnych przewodników Szybki start i samouczków, pozostaw te zasoby na swoim miejscu.

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą interfejsu wiersza polecenia platformy Azure [AZ Group Delete](/cli/azure/group) .

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Key Vault i został on usunięty. Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Zapoznaj się [z omówieniem Azure Key Vault](overview.md)
- Zapoznaj się z [omówieniem zabezpieczeń Azure Key Vault](security-overview.md)
- Zobacz odwołanie do [interfejsu wiersza polecenia platformy Azure AZ](/cli/azure/keyvault)

