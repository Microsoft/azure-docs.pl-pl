---
title: dołączanie pliku
description: dołączanie pliku
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77205730"
---
Kod dla wszystkich funkcji w określonej aplikacji funkcji znajduje się w folderze głównym projektu zawierającym plik konfiguracji hosta i jeden lub więcej podfolderów. Każdy podfolder zawiera kod dla oddzielnej funkcji. Struktura folderów jest pokazana w następującej reprezentacji:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

W wersji 2. x i nowszych w czasie wykonywania funkcji wszystkie funkcje w aplikacji funkcji muszą mieć ten sam stos języka.  

[host.jsw](../articles/azure-functions/functions-host-json.md) pliku zawiera konfiguracje specyficzne dla środowiska uruchomieniowego i znajduje się w folderze głównym aplikacji funkcji. Folder *bin* zawiera pakiety i inne pliki bibliotek wymagane przez aplikację funkcji. Zobacz wymagania specyficzne dla języka dla projektu aplikacji funkcji:

* [Biblioteka klas języka C# (. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Skrypt języka C# (csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Skrypt języka F #](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
