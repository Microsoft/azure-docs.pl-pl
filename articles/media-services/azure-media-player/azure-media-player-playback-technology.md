---
title: Azure Media Player technologia odtwarzania
description: Dowiedz się więcej o technologii odtwarzania używanej do odtwarzania wideo lub audio.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b9772f89f78f21cfb0f2f9baa7c6b9915fbacf37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91275697"
---
# <a name="playback-technology-tech"></a>Technologia odtwarzania ("Tech") #

Technologia odtwarzania odnosi się do określonej przeglądarki lub technologii wtyczek używanej do odtwarzania wideo lub audio.

- **azureHtml5JS**: wykorzystuje standardy MSE i EME w połączeniu z elementem wideo w celu wyłącznika w sposób niezależny od wtyczki zawartości z obsługą zaszyfrowanej koperty bitowej AES-128 lub zawartości chronionej przez technologię DRM (za pośrednictwem oprogramowania PlayReady i Widevine, gdy przeglądarka ją obsługuje) z Azure Media Services
- **błysk**: wykorzystuje technologię Flash Player do odtwarzania płynnej zawartości z obsługą odszyfrowywania kopert bitowych AES-128 z Azure Media Services — wymaga wersji Flash 11,4 lub nowszej
- **html5FairPlayHLS**: wykorzystuje przeglądarki Safari specyficzne dla technologii odtwarzania opartej na przeglądarce za pośrednictwem HLS z elementem wideo. Ta technika jest wymagana do odtwarzania chronionej zawartości FairPlay z Azure Media Services i została dodana do techOrder z 10/19/16
- **Silverlight**: wykorzystuje technologię Silverlight do odtwarzania płynnej zawartości z obsługą zawartości chronionej przez PlayReady w Azure Media Services.
- **HTML5**: program korzysta z technologii odtwarzania opartej na przeglądarce z elementem wideo.  W przypadku urządzeń z systemem iOS lub Android ta technika umożliwia odtwarzanie strumieni HLS z zastosowaniem podstawowej pomocy technicznej dla szyfrowania kopert w postaci bitowej AES-128 lub zawartości DRM (za pośrednictwem FairPlay, gdy przeglądarka ją obsługuje).

## <a name="tech-order"></a>Porządek techniczny ##

Aby upewnić się, że zasób jest zmieniany na wielu różnych urządzeniach, zaleca się wykonanie następujących czynności technicznych i jest to ustawienie domyślne, jeśli: `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` i można ustawić bezpośrednio w `<video>` lub programowo w opcjach:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

lub

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Macierz zgodności ##

Uwzględniając zalecaną kolejność Tech z zawartością przesyłania strumieniowego z Azure Media Services, oczekiwano następującej macierzy odtwarzania zgodności

| Przeglądarka        | System operacyjny                                                       | Oczekiwane techniczne (jasne)  | Oczekiwane techniczne (AES)  | Oczekiwane techniczne (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| Krawędź 11      | Windows 10, Windows 8.1, Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11IE 9-101  | Windows 7, Windows Vista<sup>1</sup>                     | błysk                | błysk              | Silverlight (PlayReady)    |
| IE 11          | Windows Phone 8,1                                        | azureHtml5JS           | azureHtml5JS         | nieobsługiwane                |
| Edge           | Xbox<sup>1</sup> (aktualizacja lis 2015)                   | azureHtml5JS           | azureHtml5JS         | nieobsługiwane                |
| Chrome 37 +     | Windows 10, Windows 8.1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47 +    | Windows 10, Windows 8.1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8.1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | Silverlight (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8.1                                  | błysk                | błysk              | Silverlight (PlayReady)    |
| Safari         | iOS 6+                                                   | HTML5                  | HTML5 (bez tokenu) 3    | nieobsługiwane                |
| Przeglądarka Safari 8 +      | OS X Yosemite +                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Przeglądarka Safari 6       | OS X górski Lion<sup>1</sup>                           | błysk                | błysk              | Silverlight (PlayReady)    |
| Chrome 37 +     | Android 4.4.4 +<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37 +     | System Android 4,02                                             | HTML5                  | HTML5 (bez tokenu)<sup>3</sup>    | nieobsługiwane                |
| Firefox 42 +    | System Android 5.0 +<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | nieobsługiwane                |
| PROGRAM IE 8           | Windows                                                  | nieobsługiwane          | nieobsługiwane        | nieobsługiwane                |

<sup>1</sup> konfiguracja nie jest obsługiwana lub przetestowana; wymienione jako odwołanie do ukończenia.

<sup>2</sup> pomyślne odtwarzanie na urządzeniach z systemem Android wymaga zastosowania kombinacji możliwości urządzeń, obsługi grafiki, renderowania kodera-dekoder, obsługi systemu operacyjnego i innych. Ponieważ system Android jest platformą typu "open source", która umożliwia producentom telefonu zmianę systemu operacyjnego Android w sieci, która jest dostępna w firmie Google, powoduje to pewne fragmentację w przestrzeni systemu Android, a niektóre urządzenia mogą nie być obsługiwane ze względu na brak funkcji. Ponadto na niektórych urządzeniach z systemem Android nie jest obsługiwane żadne kodery-dekoder.  

<sup>3</sup> w przypadkach, gdy nie ma żadnego wsparcia dla tokenu, można użyć serwera proxy, aby dodać tę funkcję. Zapoznaj się z tym [blogiem](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) , aby dowiedzieć się więcej o tym rozwiązaniu.

> [!NOTE]
> Jeśli oczekiwane wybrane czynności techniczne wymagają zainstalowania wtyczki, takiej jak Flash, i która nie jest zainstalowana na komputerze użytkownika, AMP będzie nadal sprawdzać możliwości następnej Tech, w połączeniu z typami źródłowymi i informacjami o ochronie, na liście technicznej. Na przykład jeśli spróbujesz wyświetlić niechroniony strumień na żądanie w przeglądarce Safari 8 w systemie OS X Yosemite, a programy Flash i Silverlight nie są zainstalowane, AMP wybierze natywną aplikację HTML5 do odtwarzania.<br/><br/>Nowe technologie przeglądarki pojawiają się codziennie i mogą mieć wpływ na tę macierz.

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)