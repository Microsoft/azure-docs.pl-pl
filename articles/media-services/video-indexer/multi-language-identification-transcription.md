---
title: Automatycznie zidentyfikuj i transkrypcja zawartość dla wielu języków za pomocą Video Indexer
titleSuffix: Azure Media Services
description: W tym temacie pokazano, jak automatycznie identyfikować i transkrypcja zawartość dla wielu języków za pomocą Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 657ccafa0e7b7f640122fd6b397b3fa2a7c5f0fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015559"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Automatyczne identyfikowanie i transkrybowanie zawartości w wielu językach

Video Indexer obsługuje automatyczne identyfikowanie i transkrypcję języka w zawartości w wielu językach. Ten proces obejmuje automatyczne identyfikowanie języka mówionego w różnych segmentach z audio, wysyłanie każdego segmentu pliku nośnika do uzyskanego i łączenie transkrypcji z powrotem do jednego ujednoliconego transkrypcji. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Wybieranie wielojęzycznej identyfikacji na indeksowanie za pomocą portalu

Można wybrać **wykrywanie wielu języków** podczas przekazywania i indeksowania wideo. Alternatywnie można wybrać **wykrywanie wielu języków**  podczas ponownego indeksowania wideo. Poniższe kroki opisują sposób ponownego indeksowania:

1. Przejdź do witryny internetowej [Video Indexer](https://vi.microsoft.com/) i zaloguj się.
1. Przejdź do strony **Biblioteka** i umieść kursor nad nazwą wideo, które chcesz ponownie zindeksować. 
1. W prawym dolnym rogu kliknij przycisk **ponownie Indeksuj wideo** . 
1. W oknie dialogowym **ponowne indeksowanie wideo** wybierz opcję **wykrywanie wielu języków** z listy rozwijanej **Język źródłowy wideo** .

    * Gdy film wideo jest indeksowany jako wiele języków, na stronie wglądu zostanie uwzględniona ta opcja, a zostanie wyświetlony dodatkowy typ informacji o tym, który umożliwi użytkownikowi wyświetlenie segmentu, który jest uzyskanego w języku "język mówiony".
    * Tłumaczenie na wszystkie języki jest w pełni dostępne z transkrypcji w wielu językach.
    * Wszystkie inne szczegółowe informacje będą wyświetlane w wykrytym języku głównym — jest to język, który pojawił się w większości w dźwięku.
    * Podpisy zamknięte w odtwarzaczu są również dostępne w wielu językach.

![Środowisko portalu](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Wybieranie identyfikacji wielojęzycznej na indeksowanie za pomocą interfejsu API

Podczas indeksowania lub ponownego [indeksowania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) wideo przy użyciu interfejsu API, wybierz `multi-language detection` opcję w `sourceLanguage` parametrze.

### <a name="model-output"></a>Dane wyjściowe modelu

Model pobierze wszystkie języki wykryte w filmie wideo na jednej liście

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Ponadto każde wystąpienie w sekcji transkrypcji będzie zawierać język, w którym został uzyskanego

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Wytyczne i ograniczenia

* Zestaw obsługiwanych języków: angielski, francuski, niemiecki, hiszpański.
* Obsługa zawartości wielojęzycznej przy użyciu maksymalnie trzech obsługiwanych języków.
* Jeśli dźwięk zawiera języki inne niż lista obsługiwanych powyżej, wynik jest nieoczekiwany.
* Minimalna długość segmentu do wykrycia dla każdego języka — 15 sekund.
* Przesunięcie wykrywania języka jest średnio 3 s.
* Oczekiwana jest ciągłość mowy. Częste różnice między językami mogą mieć wpływ na wydajność modeli.
* Mowę nienatywnych głośników może mieć wpływ na wydajność modelu (na przykład gdy głośniki używają języka natywnego i przełączają się do innego języka).
* Model jest przeznaczony do rozpoznawania spontanicznych funkcji rozpoznawania mowy przy użyciu rozsądnych akustycznych dźwięku (nie poleceń głosowych, zarejestrowaniem itp.).
* Tworzenie i edytowanie projektu nie jest obecnie dostępne w przypadku wideo w wielu językach.
* Niestandardowe modele językowe nie są dostępne w przypadku korzystania z wykrywania wielu języków.
* Dodawanie słów kluczowych nie jest obsługiwane.
* W przypadku eksportowania plików napisów oznaczenie języka nie zostanie wyświetlone.
* Interfejs API transkrypcji aktualizacji nie obsługuje plików z wieloma językami.

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)
