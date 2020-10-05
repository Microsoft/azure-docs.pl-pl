---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "78201948"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Rejestrowanie rozszerzeń do wiązania

Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania są implementowane jako pakiety rozszerzeń. Uruchom następujące polecenie [dotnet Add Package](/dotnet/core/tools/dotnet-add-package) w oknie terminalu, aby dodać pakiet rozszerzenia magazynu do projektu.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Teraz można dodać powiązanie danych wyjściowych magazynu do projektu.  
::: zone-end  