---
title: Biblioteki klienckie dla Azure Key Vault | Microsoft Docs
description: Biblioteki klienckie dla Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: 34321ac9baf3d3cb43dace33b574cb582c2d72a0
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378954"
---
# <a name="client-libraries-for-azure-key-vault"></a>Biblioteki klienckie dla Azure Key Vault

Biblioteki klienckie dla Azure Key Vault umożliwiają programowy dostęp do Key Vault funkcjonalności z różnych języków, w tym .NET, Python, Java i JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Biblioteki klienckie na język i obiekt

Każdy zestaw SDK ma osobne biblioteki klienckie dla wpisów tajnych, kluczy i certyfikatów, zgodnie z poniższą tabelą.

| Język | Wpisy tajne | Klucze | Certyfikaty |
|--|--|--|--|
| .NET | - [Dokumentacja interfejsu API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)<br>- [Pakiet NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Start](../secrets/quick-create-net.md) | - [Dokumentacja interfejsu API](/dotnet/api/azure.security.keyvault.keys?view=azure-dotnet)<br>- [Pakiet NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys) | - [Dokumentacja interfejsu API](/dotnet/api/azure.security.keyvault.certificates?view=azure-dotnet)<br>- [Pakiet NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates) |
| Python| - [Dokumentacja interfejsu API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)<br>- [Pakiet PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Start](../secrets/quick-create-python.md) |- [Dokumentacja interfejsu API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)<br>- [Pakiet PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Start](../keys/quick-create-python.md) | - [Dokumentacja interfejsu API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)<br>- [Pakiet PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Start](../certificates/quick-create-python.md) |
| Java | - [Dokumentacja interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Start](../secrets/quick-create-java.md) |- [Dokumentacja interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys) | - [Dokumentacja interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates) |
| Node.js | - [Dokumentacja interfejsu API](/javascript/api/@azure/keyvault-secrets/?view=azure-node-latest)<br>- [Pakiet npm](https://www.npmjs.com/package/@azure/keyvault-secrest)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Start](../secrets/quick-create-node.md) |- [Dokumentacja interfejsu API](/javascript/api/@azure/keyvault-keys/?view=azure-node-latest)<br>- [Pakiet npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)| - [Dokumentacja interfejsu API](/javascript/api/@azure/keyvault-certificates/?view=azure-node-latest)<br>- [Pakiet npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates) |

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [przewodnikiem dla deweloperów Azure Key Vault](developers-guide.md)
- Przeczytaj więcej [na temat uwierzytelniania w magazynie kluczy](authentication.md)
