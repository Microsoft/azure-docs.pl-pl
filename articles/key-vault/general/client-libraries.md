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
ms.openlocfilehash: 37e67ad869af23b51497e1497865f620bae612aa
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749190"
---
# <a name="client-libraries-for-azure-key-vault"></a>Biblioteki klienckie dla Azure Key Vault

Biblioteki klienckie programu Azure Key Vault programowy dostęp do funkcji Key Vault z różnych języków, w tym .NET, Python, Java i JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Biblioteki klienta na język i obiekt

Każdy zestaw SDK ma oddzielne biblioteki klienta dla magazynu kluczy, wpisów tajnych, kluczy i certyfikatów, według poniższej tabeli.

| Język | Wpisy tajne | Klucze | Certyfikaty | Key Vault (płaszczyzna zarządzania) |
|--|--|--|--|--|
| .NET | - [Dokumentacja interfejsu API](/dotnet/api/azure.security.keyvault.secrets)<br>- [Pakiet NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Quickstart](../secrets/quick-create-net.md) | - [Dokumentacja interfejsu API](/dotnet/api/azure.security.keyvault.keys)<br>- [Pakiet NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [Quickstart](../keys/quick-create-net.md) | - [Dokumentacja interfejsu API](/dotnet/api/azure.security.keyvault.certificates)<br>- [Pakiet NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [Quickstart](../certificates/quick-create-net.md) | - [Dokumentacja interfejsu API](/dotnet/api/microsoft.azure.management.keyvault)<br>- [Pakiet NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [Dokumentacja interfejsu API](/python/api/overview/azure/keyvault-secrets-readme)<br>- [Pakiet PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-python.md) |- [Dokumentacja interfejsu API](/python/api/overview/azure/keyvault-keys-readme)<br>- [Pakiet PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Quickstart](../keys/quick-create-python.md) | - [Dokumentacja interfejsu API](/python/api/overview/azure/keyvault-certificates-readme)<br>- [Pakiet PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-python.md) | - [Dokumentacja interfejsu API](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br> - [Pakiet PyPi](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [Dokumentacja interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-java.md) |- [Dokumentacja interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [Quickstart](../keys/quick-create-java.md) | - [Dokumentacja interfejsu API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-java.md) |- [Dokumentacja interfejsu API](/java/api/com.microsoft.azure.management.keyvault)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [Dokumentacja interfejsu API](/javascript/api/@azure/keyvault-secrets/)<br>- [Pakiet npm](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Quickstart](../secrets/quick-create-node.md) |- [Dokumentacja interfejsu API](/javascript/api/@azure/keyvault-keys/)<br>- [Pakiet npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [Quickstart](../keys/quick-create-node.md)| - [Dokumentacja interfejsu API](/javascript/api/@azure/keyvault-certificates/)<br>- [Pakiet npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [Quickstart](../certificates/quick-create-node.md) |  - [Dokumentacja interfejsu API](/javascript/api/@azure/arm-keyvault/)<br>- [Pakiet npm](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Następne kroki

- Zobacz przewodnik [Azure Key Vault deweloperów](developers-guide.md)
- Przeczytaj więcej na [temat uwierzytelniania w magazynie kluczy](authentication.md)
- Przeczytaj więcej na [temat zabezpieczania dostępu do Key Vault](security-overview.md)
