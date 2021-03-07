---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 4a4705647b90d29f47e37b88531f3432c6a2f448
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434558"
---
Podczas tworzenia aplikacji dla macOS dostępne są trzy zestawy SDK mowy.

- Zestaw SDK mowy dla języka C jest dostępny natywnie jako pakiet CocoaPod
- Zestaw SDK mowy dla platformy .NET może być używany z platformą **Xamarin. Mac** , ponieważ implementuje .NET Standard 2,0
- Zestaw SDK mowy języka Python jest dostępny jako moduł PyPI

> [!TIP]
> Aby uzyskać szczegółowe informacje dotyczące korzystania z zestawu Speech-C mowy z użyciem SWIFT, zobacz <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importowanie zamierzenia-c do SWIFT </a>.

### <a name="system-requirements"></a>Wymagania systemowe

- MacOS w wersji 10,13 lub nowszej

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Pakiet macOS CocoaPod jest dostępny do pobrania i użycia z <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (lub nowszym) </a> zintegrowanego środowiska programistycznego (IDE). Najpierw <a href="https://aka.ms/csspeech/macosbinary" target="_blank">Pobierz plik binarny CocoaPod </a>. Wyodrębnij element w tym samym katalogu dla zamierzonego użycia, Utwórz *plik podfile* i wystaw go `pod` jako `target` .
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
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Rozszerzenie Xamarin.Mac uwidacznia kompletny zestaw macOS SDK dla deweloperów platformy .NET, co pozwala pisać natywne aplikacje dla komputerów Mac w języku C#. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin. Mac </a>.
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

#### <a name="additional-resources"></a>Dodatkowe zasoby

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech SDK — kod źródłowy języka C </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">kod źródłowy SWIFT narzędzia macOS Speech SDK szybkiego startu </a>