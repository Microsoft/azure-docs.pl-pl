---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7a390c0d19a37ea18f9eac8636683ec35ecbc844
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880218"
---
## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem musisz mieć następujące elementy:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java,programming-language-other"  
+ Wersja [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 lub nowsza.
::: zone-end  
::: zone pivot="programming-language-python"
+ Wersja Azure Functions Core Tools, która odpowiada zainstalowanej wersji języka Python:

   | Wersja języka Python | Wersja narzędzi Core Tools |
   | -------------- | ------------------ |
   | Python 3.8     | [wersja 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [Wersja 2.7.1846 lub nowsza](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ Interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.4 lub nowszej. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), wersje Active LTS i Maintenance LTS (zalecane wersje 8.11.1 i 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64-bitowy),](https://www.python.org/downloads/release/python-382/) [Python 3.7 (64-bitowy),](https://www.python.org/downloads/release/python-375/) [Python 3.6 (64-bitowy),](https://www.python.org/downloads/release/python-368/)które są obsługiwane przez Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ Zestaw [SDK platformy .NET Core 3.1](https://dotnet.microsoft.com/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ Zestaw [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)w wersji 8 lub 11. 

+ [Apache Maven,](https://maven.apache.org)wersja 3.0 lub nowsza.

::: zone-end
::: zone pivot="programming-language-other"
+ Narzędzia programskie dla języka, z których korzystasz. W tym samouczku jako [przykładu używany jest](https://www.r-project.org/) język programowania R.
::: zone-end