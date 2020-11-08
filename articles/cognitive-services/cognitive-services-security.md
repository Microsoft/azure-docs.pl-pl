---
title: Zabezpieczenia usługi Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat różnych zagadnień związanych z zabezpieczeniami Cognitive Services.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: f14ac72443dedc8e33e607a82b2145c7ebf95ad2
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368784"
---
# <a name="azure-cognitive-services-security"></a>Zabezpieczenia usługi Azure Cognitive Services

W przypadku tworzenia wszelkich i wszystkich aplikacji zabezpieczenia należy traktować jako najwyższy priorytet. Na początku aplikacji z obsługą sztucznej analizy zabezpieczenia są jeszcze ważniejsze. W tym artykule opisano różne aspekty zabezpieczeń usługi Azure Cognitive Services, takie jak użycie zabezpieczeń warstwy transportu, uwierzytelnianie, bezpieczne Konfigurowanie poufnych danych i Skrytka klienta na potrzeby dostępu do danych klienta.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Wszystkie Cognitive Services punkty końcowe uwidocznione za pośrednictwem protokołu HTTP wymuszają protokół TLS 1,2. W przypadku wymuszonego protokołu zabezpieczeń klienci próbujący wywołać Cognitive Services punkt końcowy powinien przestrzegać następujących wytycznych:

* System operacyjny klienta (OS) musi obsługiwać protokół TLS 1,2
* Język (i platforma) służący do wywołania protokołu HTTP musi określić protokół TLS 1,2 w ramach żądania
  * W zależności od języka i platformy, określanie protokołu TLS jest wykonywane jawnie lub jawnie

W przypadku użytkowników platformy .NET należy wziąć pod uwagę <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">Transport Layer Security najlepszych <span class="docon docon-navigate-external x-hidden-focus"></span> </a>rozwiązań.

## <a name="authentication"></a>Authentication

Podczas omawiania uwierzytelniania istnieje kilka typowych nielicznych koncepcji. Uwierzytelnianie i autoryzacja są często mylone ze sobą. Tożsamość jest również głównym składnikiem zabezpieczeń. Tożsamość to zbiór informacji o <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">podmiotu zabezpieczeń <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Dostawcy tożsamości (dostawcy tożsamości) dostarczają tożsamości do usług uwierzytelniania. Uwierzytelnianie jest czynnością weryfikowania tożsamości użytkownika. Autoryzacja to specyfikacja praw dostępu i uprawnień do zasobów dla danej tożsamości. Niektóre oferty Cognitive Services obejmują kontrolę dostępu opartą na rolach (Azure RBAC). Korzystając z usługi Azure RBAC, można uprościć niektóre z procedury związanych z ręcznym zarządzaniem podmiotami zabezpieczeń. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach na platformie Azure dla zasobów platformy Azure](../role-based-access-control/overview.md).

Aby uzyskać więcej informacji na temat uwierzytelniania przy użyciu kluczy subskrypcji, tokenów dostępu i Azure Active Directory (AAD), zobacz <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">uwierzytelnianie <span class="docon docon-navigate-external x-hidden-focus"></span> żądań na platformie Azure Cognitive Services</a>.

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

W nowym wystąpieniu **wiersza polecenia** Przeczytaj zmienną środowiskową.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Utwórz i przypisz utrwaloną zmienną środowiskową, uwzględniając wartość.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

W nowym wystąpieniu środowiska **Windows PowerShell** Przeczytaj zmienną środowiskową.

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

W nowym wystąpieniu **bash** , przeczytaj zmienną środowiskową.

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

Aby uzyskać zmienną środowiskową, należy ją odczytać w pamięci. W zależności od używanego języka należy wziąć pod uwagę następujące fragmenty kodu. Te fragmenty kodu pokazują, jak uzyskać zmienną środowiskową `ENVIRONMENT_VARIABLE_KEY` i przypisać ją do zmiennej o nazwie `value` .

# <a name="c"></a>[C#](#tab/csharp)

Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank">`Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

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

Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank">`getenv` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

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

Aby uzyskać więcej informacji, zobacz <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank">`System.getenv` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

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

Aby uzyskać więcej informacji, zobacz <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank">`process.env` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Aby uzyskać więcej informacji, zobacz <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank">`os.environ` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Obiektowy C](#tab/objective-c)

Aby uzyskać więcej informacji, zobacz <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank">`environment` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>Skrytka klienta

[Skrytka klienta dla Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md) udostępnia interfejs umożliwiający klientom przeglądanie i zatwierdzanie lub odrzucanie żądań dostępu do danych klienta. Jest ona używana, gdy inżynier firmy Microsoft musi uzyskać dostęp do danych klientów podczas rozpatrywania wniosku o pomoc techniczną. Aby uzyskać informacje dotyczące sposobu, w jaki żądania Skrytka klienta są inicjowane, śledzone i przechowywane na potrzeby późniejszych przeglądów i inspekcji, zobacz [skrytka klienta](../security/fundamentals/customer-lockbox-overview.md). 

Skrytka klienta jest dostępna dla tej usługi poznawczej:

* Translator

W przypadku następujących usług inżynierowie firmy Microsoft nie będą mieć dostępu do żadnych danych klienta w warstwie E0: 

* Language Understanding
* Rozpoznawanie twarzy
* Content Moderator
* Personalizacja

> [!IMPORTANT]
> W przypadku **aparatu rozpoznawania formularzy** inżynierowie firmy Microsoft nie będą mieć dostępu do żadnych danych klienta w zasobach utworzonych po 10 lipca 2020.

Aby zażądać możliwości korzystania z jednostki SKU E0, Wypełnij i Prześlij ten [formularz żądania](https://aka.ms/cogsvc-cmk). Potrwa około 3-5 dni roboczych, aby poznać stan Twojego żądania. W zależności od popytu można umieścić w kolejce i zatwierdzić, że jest ona dostępna. Po zatwierdzeniu do korzystania z jednostki SKU E0 z LUIS należy utworzyć nowy zasób na podstawie Azure Portal i wybrać E0 jako warstwę cenową. Użytkownicy nie będą mogli przeprowadzić uaktualnienia z F0 do nowej jednostki SKU E0.

Usługa mowy nie obsługuje obecnie Skrytka klienta. Dane klienta mogą jednak być przechowywane przy użyciu funkcji przenoszenia własnego magazynu (BYOS), co pozwala na uzyskanie podobnych kontroli danych do Skrytka klienta. Należy pamiętać, że dane usługi mowy pozostają i są przetwarzane w regionie, w którym został utworzony zasób mowy. Dotyczy to danych przechowywanych w czasie spoczynku i przesyłania danych. W przypadku korzystania z funkcji dostosowywania, takich jak Custom Speech i Niestandardowa obsługa głosu, wszystkie dane klienta są przesyłane, przechowywane i przetwarzane w tym samym regionie, w którym znajduje się zasób usługi BYOS (jeśli jest używany) i usługa Speech.

> [!IMPORTANT]
> Firma Microsoft **nie** używa danych klienta w celu ulepszania modeli mowy. Ponadto jeśli rejestrowanie punktów końcowych jest wyłączone i nie są używane żadne dostosowania, dane klienta nie są przechowywane. 

## <a name="next-steps"></a>Następne kroki

* Poznaj różne [Cognitive Services](./what-are-cognitive-services.md)
* Dowiedz się więcej na temat [Cognitive Services sieci wirtualnych](cognitive-services-virtual-networks.md)