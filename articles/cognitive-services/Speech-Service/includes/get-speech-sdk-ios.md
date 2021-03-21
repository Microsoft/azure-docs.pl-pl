---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 0a9ca21cc14bb87797c962a89cf87ac184e73735
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434567"
---
:::row:::
    :::column span="3":::
        Podczas tworzenia aplikacji dla systemu iOS dostępne są dwa zestawy SDK mowy. Zestaw SDK mowy dla języka C jest dostępny natywnie jako pakiet CocoaPod systemu iOS. Alternatywnie można używać zestawu Speech SDK dla platformy Xamarin. iOS, ponieważ implementuje on .NET Standard 2,0.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Aby uzyskać szczegółowe informacje dotyczące korzystania z zestawu Speech-C mowy z użyciem SWIFT, zobacz <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Importowanie zamierzenia-c do SWIFT </a>.

### <a name="system-requirements"></a>Wymagania systemowe

- MacOS w wersji 10,3 lub nowszej
- Docelowy system iOS 9,3 lub nowszy

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        Pakiet CocoaPod systemu iOS jest dostępny do pobrania i użycia z <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (lub nowszym) </a> zintegrowanego środowiska programistycznego (IDE). Najpierw <a href="https://aka.ms/csspeech/iosbinary" target="_blank">Pobierz plik binarny CocoaPod </a>. Wyodrębnij element w tym samym katalogu dla zamierzonego użycia, Utwórz *plik podfile* i wystaw go `pod` jako `target` .
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
        Rozszerzenie Xamarin.iOS uwidacznia kompletny zestaw iOS SDK dla deweloperów platformy .NET. Twórz całkowicie natywne aplikacje dla systemu iOS przy użyciu języka C# lub F# w programie Visual Studio. Aby uzyskać więcej informacji, zobacz <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin. iOS </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>Dodatkowe zasoby

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">zestaw SDK rozpoznawania mowy dla systemu iOS — kod źródłowy języka C </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">kod źródłowy SWIFT zestawu SDK mowy dla systemu iOS </a>