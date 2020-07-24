---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3f5c7021461b407e90d61ed045c112aab3c1c4e9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87056004"
---
## <a name="configure-your-local-environment"></a>Konfigurowanie środowiska lokalnego

Przed rozpoczęciem należy wykonać następujące czynności:

+ Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) wersja 2.7.1846 lub nowsza wersja 2. x.
::: zone-end  
::: zone pivot="programming-language-python"
+ Wersja Azure Functions Core Tools, która odpowiada zainstalowanej wersji języka Python:

   | Wersja języka Python | Wersja podstawowych narzędzi |
   | -------------- | ------------------ |
   | Python 3,8     | [Wersja 3. x](../articles/azure-functions/functions-run-local.md#v2) |
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
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [Zestaw .NET Core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Zestaw Java developer Kit](https://aka.ms/azure-jdks), wersja 8. 

    > [!IMPORTANT]
    > + Obsługa języka Java 11 jest obecnie dostępna w wersji zapoznawczej, a Maven Archetype domyślnie tworzy wdrożenie języka Java 8. Jeśli chcesz zamiast tego uruchomić aplikację funkcji w środowisku Java 11, musisz ręcznie zaktualizować plik pom.xml przy użyciu wartości języka Java 11. Aby dowiedzieć się więcej, zobacz [wersje Java](../articles/azure-functions/functions-reference-java.md#java-versions). 
    > + `JAVA_HOME`Aby ukończyć ten przewodnik Szybki Start, zmienna środowiskowa musi być ustawiona na lokalizację instalacji poprawnej wersji JDK.

+ [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza.

::: zone-end
