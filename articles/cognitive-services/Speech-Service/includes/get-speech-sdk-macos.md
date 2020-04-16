---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 57de7f1e7c37fec66cda666d3f144e52849a026f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399975"
---
Podczas tworzenia dla systemu macOS dostępne są trzy skomuny SDK mowy.

- SDK mowy Objective-C jest dostępny natywnie jako pakiet CocoaPod
- Pakiet .NET Speech SDK może być używany z systemem **Xamarin.Mac,** ponieważ implementuje standard .NET Standard 2.0
- Moduł SDK mowy języka Python jest dostępny jako moduł PyPI

> [!TIP]
> Aby uzyskać szczegółowe informacje za pomocą SDK mowy języka Objective-C z programem Swift, zobacz <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importowanie funkcji <span class="docon docon-navigate-external x-hidden-focus"> </span>Objective-C do programu Swift </a>.

### <a name="system-requirements"></a>Wymagania systemowe

- System macOS w wersji 10.13 lub nowszej

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Pakiet macOS CocoaPod jest dostępny do pobrania i użycia ze zintegrowanym środowiskiem <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">programistycznym <span class="docon docon-navigate-external x-hidden-focus"></span> Xcode 9.4.1 (lub nowszym)</a> (IDE). Najpierw <a href="https://aka.ms/csspeech/macosbinary" target="_blank">pobierz binarny <span class="docon docon-navigate-external x-hidden-focus"> </span>CocoaPod </a>. Wyodrębnij zasobnik w tym samym katalogu dla jego zamierzonego `pod` zastosowania, `target`utwórz *Podfile* i wyświetl jako .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Rozszerzenie Xamarin.Mac uwidacznia kompletny zestaw macOS SDK dla deweloperów platformy .NET, co pozwala pisać natywne aplikacje dla komputerów Mac w języku C#. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Zasoby dodatkowe

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">Kod źródłowy programu MacOS Speech SDK Objective-C<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">Kod źródłowy programu Szybki start programu Swift programu macOS Speech SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>