---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: dfacbfbc57ec536eb983394c28920da719af4b1d
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996531"
---
## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy wykonać następujące czynności:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) wersja 2.7.1846 lub nowsza wersja 2. x.
::: zone-end  
::: zone pivot="programming-language-python"
+ Wersja Azure Functions Core Tools, która odpowiada zainstalowanej wersji języka Python:

   | Wersja języka Python | Wersja podstawowych narzędzi |
   | -------------- | ------------------ |
   | Python 3,8     | [Wersja 3. x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3,6<br/>Python 3.7 | [Wersja 2.7.1846 lub nowsza wersja 2. x](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2,4 lub nowszej. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node. js](https://nodejs.org/), Active LTS i Maintenance LTS wersje (zalecane 8.11.1 i 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,8 (64-bitowy)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), które są obsługiwane przez Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [Program PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [Zestaw .NET Core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Zestaw Java developer Kit](https://aka.ms/azure-jdks), wersja 8.

+ [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza.

> [!IMPORTANT]
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.
::: zone-end
