---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025742"
---
[Biblioteka programu Microsoft Azure Configuration Manager dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) zawiera klasę do analizowania parametrów połączenia w pliku konfiguracji. Klasa [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100)) analizuje ustawienia konfiguracji. Analizuje on ustawienia aplikacji klienckich uruchamianych na pulpicie, na urządzeniu przenośnym, na maszynie wirtualnej platformy Azure lub w usłudze w chmurze platformy Azure.

Aby odwołać się do `CloudConfigurationManager` pakietu, Dodaj następujące `using` dyrektywy:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Oto przykład, w którym przedstawiono, jak pobrać parametry połączenia z pliku konfiguracji:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Użycie programu Azure Configuration Manager jest opcjonalne. Można również użyć interfejsu API, takiego jak [Klasa configurationmanager](/dotnet/api/system.configuration.configurationmanager).NET Framework.