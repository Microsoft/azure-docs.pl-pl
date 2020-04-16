---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 138a9ef9d483ca0d460bab7185d646669650f83e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400037"
---
:::row:::
    :::column span="3":::
        Zestaw SDK mowy obsługuje systemy Windows 10 i Windows Server 2016 lub nowsze wersje. Wcześniejsze wersje **nie** są oficjalnie obsługiwane. Możliwe jest użycie części zestawu SDK mowy z wcześniejszymi wersjami systemu Windows, chociaż nie jest to zalecane.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>Wymagania systemowe

Pakiet SDK mowy w systemie Windows wymaga <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">redystrybucyjnego <span class="docon docon-navigate-external x-hidden-focus"></span> programu Microsoft Visual C++ dla programu Visual Studio 2019</a> w systemie.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">Instalacja dla x86<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">Instalacja dla x64<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">Instalacja dla ARMx64<span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

W przypadku wejścia mikrofonu należy zainstalować biblioteki Media Foundation. Te biblioteki są częścią systemu Windows 10 i Windows Server 2016. Można używać zestawu SDK mowy bez tych bibliotek, o ile mikrofon nie jest używany jako urządzenie wejściowe audio.

Wymagane pliki SDK mowy można wdrożyć w tym samym katalogu co aplikacja. W ten sposób aplikacja może bezpośrednio uzyskać dostęp do bibliotek. Upewnij się, że wybrano poprawną wersję (x86/x64), która pasuje do aplikacji.

| Nazwa                                            | Funkcja                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | Podstawowy sdk wymagany do wdrożenia natywnego i zarządzanego |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Wymagane do wdrożenia zarządzanego                      |

> [!NOTE]
> Począwszy od wersji 1.3.0 plik `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (dostarczany w poprzednich wersjach) nie jest już potrzebny. Funkcja jest teraz zintegrowana z podstawowym SDK.

> [!IMPORTANT]
> W przypadku projektu C# aplikacji Windows Forms (.NET Framework) upewnij się, że biblioteki są uwzględnione w ustawieniach wdrażania projektu. Możesz to sprawdzić `Properties -> Publish Section`w obszarze . Kliknij `Application Files` przycisk i znajdź odpowiednie biblioteki z listy przewijania w dół. Upewnij się, że `Included`wartość jest ustawiona na . Visual Studio będzie zawierać plik, gdy projekt jest publikowany/wdrażany.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
