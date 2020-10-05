---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a33cc604d45d51a63a73ea6ed58abc67269437d7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673146"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Dodaj funkcję do projektu za pomocą następującego polecenia, gdzie `--name` argument jest unikatową nazwą funkcji (HttpExample), a `--template` argument określa wyzwalacz funkcji (http). 

```
func new --name HttpExample --template "HTTP trigger"
```  
::: zone-end  
::: zone pivot="programming-language-csharp"
`func new` tworzy plik kodu HttpExample.cs.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
`func new` tworzy podfolder pasujący do nazwy funkcji, która zawiera plik kodu odpowiedni dla wybranego języka projektu i plik konfiguracji o nazwie *function.json*.
::: zone-end