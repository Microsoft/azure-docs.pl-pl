---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 01e62685fa73a7f547ef5246b28fdfdf659e7afa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623330"
---
|Język                                 |1.x         |2.x| wersji |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET Framework 4,7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3,1)<br/>[Wersja zapoznawcza (.NET 5,0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (węzeł 6)|GA (Node 10 & 8)| GA (Node 14, 12, & 10) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4,7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3,1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|Nie dotyczy|GA (Java 8)| GA (Java 11 & 8)|
|[Program PowerShell](../articles/azure-functions/functions-reference-powershell.md) |Nie dotyczy|GA (program PowerShell Core 6)| GA (program PowerShell 7 & Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|Nie dotyczy|GA (Python 3,7 & 3,6)| GA (Python 3,8, 3,7, & 3,6) <br/> Wersja zapoznawcza (Python 3,9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |Nie dotyczy|GA<sup>2</sup>| GA<sup>2</sup> |


<sup>1</sup> aplikacje biblioteki klas .NET ukierunkowane na środowisko uruchomieniowe w wersji 2. x mogą być teraz uruchamiane na platformie .net Core 3,1 w trybie zgodności z programem .NET Core 2. x. Aby dowiedzieć się więcej, zobacz temat [funkcje w wersji 2. x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> obsługiwane przez Transpiling do języka JavaScript.

Aby uzyskać więcej informacji o obsługiwanych wersjach językowych, zobacz artykuł dotyczący przewodnika dla deweloperów.   
Aby uzyskać informacje o planowanych zmianach w obsłudze języka, zobacz [Azure plan](https://azure.microsoft.com/roadmap/?tag=functions).
