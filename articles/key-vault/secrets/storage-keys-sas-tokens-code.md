---
title: Pobieranie tokenów sygnatury dostępu współdzielonego w kodzie | Azure Key Vault
description: Funkcja zarządzanego konta magazynu zapewnia bezproblemową integrację między Azure Key Vault i kontem usługi Azure Storage. Ten przykład używa zestawu Azure SDK dla platformy .NET do zarządzania tokenami SAS.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: afe7d5ce3dd1756ddb9e33fe402fb2eb699ce8f7
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007421"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Pobieranie tokenów sygnatury dostępu współdzielonego

Kontem magazynu można zarządzać za pomocą tokenów sygnatury dostępu współdzielonego (SAS) przechowywanych w magazynie kluczy. Aby uzyskać więcej informacji, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego](../../storage/common/storage-sas-overview.md).

> [!NOTE]
> Zalecamy używanie [Access Control opartej na rolach (RBAC)](../../storage/common/storage-auth-aad.md) , aby zabezpieczyć konto magazynu w celu zapewnienia bezpieczeństwa i łatwość użycia w ramach autoryzacji klucza współużytkowanego.

Ten artykuł zawiera przykłady kodu platformy .NET, który tworzy definicję sygnatury dostępu współdzielonego i pobiera tokeny sygnatury dostępu współdzielonego. Zapoznaj się z naszym Przykładem [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) , aby uzyskać pełne szczegóły, w tym wygenerowany klient dla kont magazynu zarządzanych przez Key Vault. Aby uzyskać informacje na temat tworzenia i przechowywania tokenów SAS, zobacz [Zarządzanie kluczami konta magazynu przy użyciu Key Vault i interfejsu wiersza polecenia platformy Azure](overview-storage-keys.md) lub [Zarządzanie kluczami konta magazynu za pomocą Key Vault i Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Przykłady kodu

W poniższym przykładzie utworzymy szablon sygnatury dostępu współdzielonego:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Za pomocą tego szablonu możemy utworzyć definicję sygnatury dostępu współdzielonego za pomocą 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Po utworzeniu definicji sygnatury dostępu współdzielonego można pobrać tokeny SAS, takie jak wpisy tajne, przy użyciu `SecretClient` . Musisz najpierw uzyskać nazwę wpisu tajnego z nazwą konta magazynu, a następnie kreską:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Jeśli token sygnatury dostępu współdzielonego niedługo wygaśnie, możesz pobrać ten sam klucz tajny, aby wygenerować nowy.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [udzielić ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego](../../storage/common/storage-sas-overview.md).
- Dowiedz się, jak [zarządzać kluczami konta magazynu za pomocą Key Vault i interfejsu wiersza polecenia platformy Azure](overview-storage-keys.md) lub [Azure PowerShell](overview-storage-keys-powershell.md).
- Zobacz pełny [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) przykład.
- Więcej [przykładów Key Vault](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-key-vault)
- Zobacz [przykłady kluczy zarządzanego konta magazynu](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
