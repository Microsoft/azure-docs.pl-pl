---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399949"
---
:::row:::
    :::column span="3":::
        Podczas tworzenia dla systemu iOS dostępne są dwa skomuny SDK mowy. Pakiet SDK mowy języka Objective-C jest dostępny natywnie jako pakiet CocoaPod dla systemu iOS. Alternatywnie.NET Speech SDK może być używany z xamarin.iOS, ponieważ implementuje .NET Standard 2.0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Aby uzyskać szczegółowe informacje za pomocą SDK mowy języka Objective-C z programem Swift, zobacz <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importowanie funkcji <span class="docon docon-navigate-external x-hidden-focus"> </span>Objective-C do programu Swift </a>.

### <a name="system-requirements"></a>Wymagania systemowe

- System macOS w wersji 10.3 lub nowszej
- Docelowy iOS 9.3 lub nowszy

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Pakiet iOS CocoaPod jest dostępny do pobrania i użycia ze zintegrowanym środowiskiem <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">programistycznym <span class="docon docon-navigate-external x-hidden-focus"></span> Xcode 9.4.1 (lub nowszym)</a> (IDE). Najpierw <a href="https://aka.ms/csspeech/iosbinary" target="_blank">pobierz binarny <span class="docon docon-navigate-external x-hidden-focus"> </span>CocoaPod </a>. Wyodrębnij zasobnik w tym samym katalogu dla jego zamierzonego `pod` zastosowania, `target`utwórz *Podfile* i wyświetl jako .
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

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Rozszerzenie Xamarin.iOS uwidacznia kompletny zestaw iOS SDK dla deweloperów platformy .NET. Twórz całkowicie natywne aplikacje dla systemu iOS przy użyciu języka C# lub F# w programie Visual Studio. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;&nbsp; ❤️        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Zasoby dodatkowe

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">Kod źródłowy SDK programu iOS Dla języka Podręcznka Mowy Objective-C<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">Kod źródłowy swift programu iOS Speech SDK Swift<span class="docon docon-navigate-external x-hidden-focus"></span></a>