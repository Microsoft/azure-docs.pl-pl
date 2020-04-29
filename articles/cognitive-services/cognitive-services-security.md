---
title: Zabezpieczenia
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat różnych zagadnień związanych z zabezpieczeniami Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80131531"
---
# <a name="azure-cognitive-services-security"></a>Zabezpieczenia usługi Azure Cognitive Services

W przypadku tworzenia wszelkich i wszystkich aplikacji zabezpieczenia należy traktować jako najwyższy priorytet. Na początku aplikacji z obsługą sztucznej analizy zabezpieczenia są jeszcze ważniejsze. W tym artykule opisano różne aspekty zabezpieczeń usługi Azure Cognitive Services, takie jak użycie zabezpieczeń warstwy transportu, uwierzytelnianie i bezpieczne Konfigurowanie poufnych danych.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Wszystkie Cognitive Services punkty końcowe uwidocznione za pośrednictwem protokołu HTTP wymuszają protokół TLS 1,2. W przypadku wymuszonego protokołu zabezpieczeń klienci próbujący wywołać Cognitive Services punkt końcowy powinien przestrzegać następujących wytycznych:

* System operacyjny klienta (OS) musi obsługiwać protokół TLS 1,2
* Język (i platforma) służący do wywołania protokołu HTTP musi określić protokół TLS 1,2 w ramach żądania
  * W zależności od języka i platformy, określanie protokołu TLS jest wykonywane jawnie lub jawnie

W przypadku użytkowników platformy .NET należy wziąć pod uwagę <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">Transport Layer Security najlepszych <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>rozwiązań.

## <a name="authentication"></a>Authentication

Podczas omawiania uwierzytelniania istnieje kilka typowych nielicznych koncepcji. Uwierzytelnianie i autoryzacja są często mylone ze sobą. Tożsamość jest również głównym składnikiem zabezpieczeń. Tożsamość to zbiór informacji o <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">podmiotu zabezpieczeń <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Dostawcy tożsamości (dostawcy tożsamości) dostarczają tożsamości do usług uwierzytelniania. Uwierzytelnianie jest czynnością weryfikowania tożsamości użytkownika. Autoryzacja to specyfikacja praw dostępu i uprawnień do zasobów dla danej tożsamości. Niektóre oferty Cognitive Services obejmują kontrolę dostępu opartą na rolach (RBAC). RBAC może służyć do uproszczenia niektórych procedury związanych z ręcznym zarządzaniem podmiotami zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach dla zasobów platformy Azure](../role-based-access-control/overview.md).

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu kluczy subskrypcji, tokenów dostępu i Azure Active Directory (AAD), zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">uwierzytelnianie<span class="docon docon-navigate-external x-hidden-focus"></span>żądań na platformie Azure Cognitive Services</a>.

## <a name="environment-variables-and-application-configuration"></a>Zmienne środowiskowe i konfiguracja aplikacji

Zmienne środowiskowe są parami nazw-wartości przechowywanych w określonym środowisku. Bardziej bezpieczną alternatywą do używania wartości stałe dla poufnych danych jest użycie zmiennych środowiskowych. Wartości stałe są niezabezpieczone i należy je unikać.

> [!CAUTION]
> **Nie** należy używać wartości stałe dla poufnych danych, dlatego jest to główna Luka w zabezpieczeniach.

> [!NOTE]
> Chociaż zmienne środowiskowe są przechowywane w postaci zwykłego tekstu, są one izolowane dla środowiska. W przypadku naruszenia bezpieczeństwa środowiska, tak jak zbyt zmienne w środowisku.

### <a name="set-environment-variable"></a>Ustaw zmienną środowiskową

Aby ustawić zmienne środowiskowe, użyj jednego z następujących poleceń — Jeśli `ENVIRONMENT_VARIABLE_KEY` jest to nazwany klucz i `value` jest wartością przechowywaną w zmiennej środowiskowej.

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

Utwórz i przypisz utrwaloną zmienną środowiskową, uwzględniając wartość.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

W nowym wystąpieniu **wiersza polecenia**Przeczytaj zmienną środowiskową.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Utwórz i przypisz utrwaloną zmienną środowiskową, uwzględniając wartość.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

W nowym wystąpieniu środowiska **Windows PowerShell**Przeczytaj zmienną środowiskową.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Utwórz i przypisz utrwaloną zmienną środowiskową, uwzględniając wartość.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

W nowym wystąpieniu **bash**, przeczytaj zmienną środowiskową.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Po ustawieniu zmiennej środowiskowej Uruchom ponownie zintegrowane środowisko programistyczne (IDE), aby upewnić się, że nowo dodane zmienne środowiskowe są dostępne.

### <a name="get-environment-variable"></a>Pobierz zmienną środowiskową

Aby uzyskać zmienną środowiskową, należy ją odczytać w pamięci. W zależności od używanego języka należy wziąć pod uwagę następujące fragmenty kodu. Te fragmenty kodu pokazują, `ENVIRONMENT_VARIABLE_KEY` jak uzyskać zmienną środowiskową i przypisać ją do zmiennej o nazwie. `value`

# <a name="c"></a>[S #](#tab/csharp)

Aby uzyskać więcej informacji, <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>Zobacz.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Aby uzyskać więcej informacji, <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>Zobacz.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Aby uzyskać więcej informacji, <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>Zobacz.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Aby uzyskać więcej informacji, <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>Zobacz.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Aby uzyskać więcej informacji, <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>Zobacz.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Obiektowy C](#tab/objective-c)

Aby uzyskać więcej informacji, <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>Zobacz.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>Następne kroki

* Poznaj różne [Cognitive Services](welcome.md)
* Dowiedz się więcej na temat [Cognitive Services sieci wirtualnych](cognitive-services-virtual-networks.md)
