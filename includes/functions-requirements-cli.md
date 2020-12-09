---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: f102a5dd5b7dccba6643176d06d17a2a65171c90
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904088"
---
## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy wykonać następujące czynności:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java,programming-language-other"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) w wersji 2.7.1846 lub nowszej.
::: zone-end  
::: zone pivot="programming-language-python"
+ Wersja Azure Functions Core Tools, która odpowiada zainstalowanej wersji języka Python:

   | Wersja języka Python | Wersja podstawowych narzędzi |
   | -------------- | ------------------ |
   | Python 3.8     | [Wersja 3. x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3,6<br/>Python 3.7 | [Wersja 2.7.1846 lub nowsza](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2,4 lub nowszej. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), aktywne wersje LTS LTS i Maintenance (zalecane 8.11.1 i 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,8 (64-bitowy)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), które są obsługiwane przez Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Zestaw .NET Core SDK 3,1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Zestaw Java developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8 lub 11. 

+ [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza.

::: zone-end
::: zone pivot="programming-language-other"
+ Narzędzia programistyczne dla języka, którego używasz. Ten samouczek używa [języka programowania R](https://www.r-project.org/) jako przykładu.
::: zone-end