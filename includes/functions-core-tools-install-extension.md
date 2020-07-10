---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75564702"
---
## <a name="register-extensions"></a>Rejestrowanie rozszerzeń

Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania funkcji w środowisku uruchomieniowym w wersji 2. x i nowszych są implementowane jako pakiety rozszerzeń. W wersji 2. x i większej niż środowisko uruchomieniowe Azure Functions należy jawnie zarejestrować rozszerzenia dla typów powiązań używanych w funkcjach. Wyjątkami tego są powiązania HTTP i wyzwalacze czasomierza, które nie wymagają rozszerzeń.

Można wybrać opcję instalacji rozszerzeń powiązań pojedynczo lub dodać odwołanie do pakietu rozszerzenia do host.jsw pliku projektu. Pakiety rozszerzeń usuwają prawdopodobieństwo problemów ze zgodnością pakietu w przypadku korzystania z wielu typów powiązań. Jest to zalecane podejście do rejestrowania rozszerzeń powiązań. Pakiety rozszerzeń również usuwają wymagania dotyczące instalowania zestawu SDK programu .NET Core 2. x. 

### <a name="extension-bundles"></a>Pakiety rozszerzeń

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

Aby dowiedzieć się więcej, zobacz [Rejestrowanie rozszerzeń powiązań Azure Functions](../articles/azure-functions/functions-bindings-register.md#extension-bundles). Przed dodaniem powiązań do function.jsw pliku należy dodać pakiety rozszerzeń do host.js.

### <a name="register-individual-extensions"></a>Rejestrowanie pojedynczych rozszerzeń

Jeśli trzeba zainstalować rozszerzenia, które nie należą do pakietu, można ręcznie zarejestrować poszczególne pakiety rozszerzeń dla konkretnych powiązań. 

> [!NOTE]
> Aby ręcznie zarejestrować rozszerzenia przy użyciu programu `func extensions install` , należy zainstalować zestaw SDK programu .NET Core 2. x.

Po zaktualizowaniu *function.jsw* pliku, aby uwzględnić wszystkie powiązania wymagane przez funkcję, uruchom następujące polecenie w folderze projektu.

```bash
func extensions install
```

Polecenie odczytuje *function.jsw* pliku, aby zobaczyć, które pakiety są potrzebne, zainstaluje je i ponownie kompiluje projekt rozszerzeń. Dodaje nowe powiązania w bieżącej wersji, ale nie aktualizuje istniejących powiązań. Użyj `--force` opcji, aby zaktualizować istniejące powiązania do najnowszej wersji podczas instalowania nowych.
