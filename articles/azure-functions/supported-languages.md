---
title: Obsługiwane języki w Azure Functions
description: Dowiedz się, które języki są obsługiwane (wersja zapoznawcza) i które są dostępne w wersji zapoznawczej oraz jak można rozciągnąć programowanie funkcji do innych języków.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: da3100521faae61e9803aaa82dbf91045a7ecbc9
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108555"
---
# <a name="supported-languages-in-azure-functions"></a>Obsługiwane języki w Azure Functions

W tym artykule opisano poziomy pomocy technicznej oferowane w przypadku języków, których można używać z programem Azure Functions. Opisano w nim również strategie tworzenia funkcji wykorzystujących Języki, które nie są natywnie obsługiwane.

## <a name="levels-of-support"></a>Poziomy pomocy technicznej

Istnieją dwa poziomy wsparcia:

* **Ogólnie dostępna (ga)** — w pełni obsługiwana i zatwierdzona do użycia w środowisku produkcyjnym.
* **Wersja zapoznawcza** — nie jest jeszcze obsługiwana, ale oczekuje się, że w przyszłości zostanie osiągnięty stan ga.

## <a name="languages-by-runtime-version"></a>Języki według wersji środowiska uruchomieniowego 

Dostępne są [trzy wersje środowiska uruchomieniowego Azure Functions](functions-versions.md) . W poniższej tabeli przedstawiono języki obsługiwane w poszczególnych wersjach środowiska uruchomieniowego.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

## <a name="custom-handlers"></a>Niestandardowe programy obsługi

Niestandardowe programy obsługi to lekkie serwery sieci Web, które odbierają zdarzenia z hosta Azure Functions. Każdy język, który obsługuje elementy podstawowe HTTP, może zaimplementować obsługę niestandardową. Oznacza to, że niestandardowe programy obsługi mogą służyć do tworzenia funkcji w językach, które nie są oficjalnie obsługiwane. Aby dowiedzieć się więcej, zobacz [Azure Functions obsługi niestandardowe](functions-custom-handlers.md).

## <a name="language-extensibility"></a>Rozszerzalność języka

Począwszy od wersji 2. x, środowisko uruchomieniowe zostało zaprojektowane, aby oferować [rozszerzalność języka](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Języki JavaScript i Java w środowisku uruchomieniowym 2. x zostały skompilowane przy użyciu tej rozszerzalności.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sposobach tworzenia funkcji w obsługiwanych językach, zobacz następujące zasoby:

+ [Dokumentacja dewelopera biblioteki klas C#](functions-dotnet-class-library.md)
+ [Dokumentacja dewelopera skryptów języka C#](functions-reference-csharp.md)
+ [Dokumentacja dewelopera języka Java](functions-reference-java.md)
+ [Dokumentacja dewelopera języka JavaScript](functions-reference-node.md)
+ [Dokumentacja dewelopera programu PowerShell](functions-reference-powershell.md)
+ [Dokumentacja dla deweloperów języka Python](functions-reference-python.md)
+ [Dokumentacja dewelopera języka TypeScript](functions-reference-node.md#typescript)
