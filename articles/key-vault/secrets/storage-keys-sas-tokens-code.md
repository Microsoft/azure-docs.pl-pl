---
title: Pobieranie tokenów sygnatury dostępu współdzielonych w | Azure Key Vault
description: Funkcja zarządzanego konta magazynu zapewnia bezproblemową integrację między Azure Key Vault i kontem usługi Azure Storage. W tym przykładzie do zarządzania tokenami SAS używany jest zestaw Azure SDK dla platformy .NET.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c8a7b357efc2e30d1ca93d0b45fd2563c980045
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752754"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>Tworzenie definicji sygnatury dostępu współdzielonego i pobieranie tokenów sygnatury dostępu współdzielonego w kodzie

Możesz zarządzać kontem magazynu przy użyciu tokenów sygnatury dostępu współdzielonego (SAS) przechowywanych w magazynie kluczy. Aby uzyskać więcej informacji, zobacz [Grant limited access to Azure Storage resources using SAS (Udzielanie ograniczonego dostępu do zasobów usługi Azure Storage przy użyciu sygnatury dostępu współdzielonego).](../../storage/common/storage-sas-overview.md)

> [!NOTE]
> Zalecamy korzystanie z kontroli dostępu opartej na rolach [(RBAC)](../../storage/common/storage-auth-aad.md) platformy Azure w celu zabezpieczenia konta magazynu w celu zapewnienia najwyższego bezpieczeństwa i łatwości użycia za pośrednictwem autoryzacji za pomocą klucza wspólnego.

Ten artykuł zawiera przykłady kodu .NET, który tworzy definicję sygnatury dostępu współdzielonego i pobiera tokeny SAS. Zobacz nasz [przykład ShareLink,](/samples/azure/azure-sdk-for-net/share-link/) aby uzyskać szczegółowe informacje, w tym wygenerowanego klienta Key Vault zarządzanych kont magazynu. Aby uzyskać informacje na temat tworzenia i przechowywania tokenów SAS, zobacz Manage storage account keys with Key Vault and the Azure CLI (Zarządzanie kluczami konta magazynu za pomocą usługi Key Vault i interfejsu wiersza polecenia platformy [Azure)](overview-storage-keys.md) lub Manage storage account keys with Key Vault and Azure PowerShell (Zarządzanie kluczami kont magazynu przy użyciu Key Vault [i Azure PowerShell).](overview-storage-keys-powershell.md)

## <a name="code-samples"></a>Przykłady kodu

W poniższym przykładzie utworzymy szablon sygnatury dostępu współdzielonego:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Za pomocą tego szablonu możemy utworzyć definicję sygnatury dostępu współdzielonego przy użyciu 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Po utworzeniu definicji sygnatury dostępu współdzielonego można pobrać tokeny SAS, takie jak wpisy tajne, przy użyciu . `SecretClient` Nazwa tajnego musi być poprzedniana nazwą konta magazynu, po której następuje kreska:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Jeśli token sygnatury dostępu współdzielonych wygaśnie, możesz ponownie pobrać ten sam klucz tajny, aby wygenerować nowy.

Aby uzyskać przewodnik dotyczący sposobu używania tokenu SAS pobranego z usługi Key Vault do uzyskiwania dostępu do usług Azure Storage, zobacz Używanie sygnatury dostępu współdzielonego konta do uzyskiwania dostępu [do Blob service](../../storage/common/storage-account-sas-create-dotnet.md#use-an-account-sas-from-a-client)

> [!NOTE]
> Aplikacja musi być przygotowana do odświeżenia sygnatury dostępu współdzielonego, jeśli otrzyma ona wartość 403 z usługi Storage, aby można było obsłużyć przypadek naruszenia klucza i trzeba było obrócić go szybciej niż w normalnym okresie rotacji. 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [przyznać ograniczony dostęp do zasobów usługi Azure Storage przy użyciu sygnatury dostępu współdzielonego.](../../storage/common/storage-sas-overview.md)
- Dowiedz się, jak [zarządzać kluczami konta magazynu](overview-storage-keys.md) za pomocą Key Vault i interfejsu wiersza polecenia platformy Azure lub [Azure PowerShell](overview-storage-keys-powershell.md).
- Zobacz [Przykłady kluczy konta magazynu zarządzanego](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)