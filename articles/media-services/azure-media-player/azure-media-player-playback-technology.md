---
title: Technologia odtwarzania odtwarzacza multimediów azure
description: Dowiedz się więcej o technologii odtwarzania używanej do odtwarzania wideo lub audio.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 85eaa04836774b838da67e073017f4af3d2fe179
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726487"
---
# <a name="playback-technology-tech"></a>Technologia odtwarzania ("technika") #

Technologia odtwarzania odnosi się do konkretnej przeglądarki lub technologii wtyczki używanej do odtwarzania wideo lub audio.

- **azureHtml5JS**: wykorzystuje standardy MSE i EME w połączeniu z elementem wideo do odtwarzania zawartości DASH bez wtyczek z obsługą zaszyfrowanej zawartości koperty AES-128 lub wspólnej zaszyfrowanej zawartości DRM (za pośrednictwem PlayReady i Widevine, gdy przeglądarka ją obsługuje) z usługi Azure Media Services
- **flashSS**: wykorzystuje technologię flash player do odtwarzania Płynnej zawartości z obsługą odszyfrowywania kopert aes-128 bitów z usługi Azure Media Services - wymaga wersji Flash 11.4 lub nowszej
- **html5FairPlayHLS**: wykorzystuje Safari specyficzne w technologii odtwarzania opartego na przeglądarce za pośrednictwem HLS z elementem wideo. Ta technologia wymaga odtwarzania zawartości chronionej fairplay z usługi Azure Media Services i została dodana do techOrder od 10/19/16
- **silverlightSS**: wykorzystuje technologię silverlight do odtwarzania płynnej zawartości z obsługą zawartości chronionej przez PlayReady z usługi Azure Media Services.
- **html5**: wykorzystuje technologię odtwarzania opartą na przeglądarce z elementem wideo.  Gdy na urządzeniu Apple iOS lub Android, ta technologia umożliwia odtwarzanie strumieni HLS z podstawową obsługą szyfrowania kopert AES-128 bit lub zawartości DRM (za pośrednictwem FairPlay, gdy przeglądarka obsługuje).

## <a name="tech-order"></a>Zamówienie techniczne ##

Aby upewnić się, że twój zasób jest grywalny na wielu różnych urządzeniach, zalecane jest następujące zamówienie techniczne i jest domyślne, jeśli: `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` i można je ustawić bezpośrednio na `<video>` lub programowo w opcjach:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

lub

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Macierz zgodności ##

Biorąc pod uwagę zalecane zamówienie techniczne z zawartością przesyłania strumieniowego z usługi Azure Media Services, oczekuje się następującej macierzy odtwarzania zgodności

| Przeglądarka        | System operacyjny                                                       | Oczekiwana technologia (wyczyść)  | Oczekiwana technologia (AES)  | Oczekiwana technologia (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| Urządzenie EdgeIE 11      | Windows 10, Windows 8.1, Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11IE 9-101  | Windows 7, Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | silverlightss (PlayReady)    |
| IE 11          | Windows Phone 8,1                                        | azureHtml5JS           | azureHtml5JS         | nie jest obsługiwana                |
| Brzeg           | Xbox One<sup>1</sup> (aktualizacja z listopada 2015 r.)                   | azureHtml5JS           | azureHtml5JS         | nie jest obsługiwana                |
| Chrome 37+     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47+    | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightss (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | flashSS                | flashSS              | silverlightss (PlayReady)    |
| Safari         | iOS 6+                                                   | html5                  | html5 (bez żetonu)3    | nie jest obsługiwana                |
| Safari 8+      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Lew Górski<sup>1</sup>                           | flashSS                | flashSS              | silverlightss (PlayReady)    |
| Chrome 37+     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37+     | Android 4.02                                             | html5                  | html5 (bez żetonu)<sup>3</sup>    | nie jest obsługiwana                |
| Firefox 42+    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | nie jest obsługiwana                |
| IE 8           | Windows                                                  | nie jest obsługiwana          | nie jest obsługiwana        | nie jest obsługiwana                |

<sup>1</sup> Konfiguracja nie jest obsługiwana ani testowana; jako punkt odniesienia do uzupełnienia.

<sup>2</sup> Pomyślne odtwarzanie na urządzeniach z systemem Android wymaga kombinacji możliwości urządzenia, obsługi grafiki, renderowania kodeków, obsługi systemu operacyjnego i innych. Ponieważ Android jest platformą open source, która pozwala producentom telefonów na zmianę systemu operacyjnego Vanilla Android zapewnianego przez Google, powoduje to pewne rozdrobnienie w przestrzeni Androida, a niektóre urządzenia mogą nie być obsługiwane z powodu braku funkcji. Ponadto niektóre urządzenia z systemem Android nie obsługują wszystkich kodeków.  

<sup>3</sup> W przypadkach, gdy nie ma obsługi tokenu, serwer proxy może służyć do dodawania tej funkcji. Zapoznaj się z tym [blogiem,](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) aby dowiedzieć się więcej o tym rozwiązaniu.

> [!NOTE]
> Jeśli wybrana technologia wymaga zainstalowania wtyczki, takiej jak Flash, która nie jest zainstalowana na komputerze użytkownika, AMP będzie nadal sprawdzać możliwości następnej technologii, w połączeniu z typami źródeł i informacjami o ochronie, na liście technologii. Jeśli na przykład próba wyświetlenia niechronionego strumienia na żądanie w przeglądarce Safari 8 w systemie OS X Yosemite nie zostanie zainstalowana, amp wybierze natywną technologię Html5 do odtwarzania.<br/><br/>Nowe technologie przeglądarki pojawiają się codziennie i jako takie mogą mieć wpływ na tę matrycę.

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)