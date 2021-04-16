---
title: Automatyczne identyfikowanie i transkrypcja zawartości w wielu językach za pomocą Video Indexer
titleSuffix: Azure Media Services
description: W tym temacie pokazano, jak automatycznie identyfikować i transkrybować zawartość w wielu językach za pomocą Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: 319bd408943c560622dc3208a6701417b8ca010c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532913"
---
# <a name="automatically-identify-and-transcribe-multi-language-content"></a>Automatyczne identyfikowanie i transkrybowanie zawartości w wielu językach

Video Indexer obsługuje automatyczną identyfikację i transkrypcję języka w zawartości w wielu językach. Ten proces obejmuje automatyczne identyfikowanie języka mówionego w różnych segmentach od audio, wysyłanie każdego segmentu pliku multimedialnego do transkrypcji i łączenie transkrypcji z powrotem w jedną ujednoliconą transkrypcję. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Wybieranie identyfikacji wielojęzycznej podczas indeksowania za pomocą portalu

Podczas przekazywania **i indeksowania** pliku wideo można wybrać wykrywanie wielu języków. Możesz też wybrać wykrywanie wielu języków **podczas**  ponownego indeksowania wideo. W poniższych krokach opisano sposób ponownego indeksowania:

1. Przejdź do witryny internetowej [Video Indexer](https://vi.microsoft.com/) i zaloguj się.
1. Przejdź do strony **Biblioteka** i umieść kursor nad nazwą wideo, które chcesz indeksować ponownie. 
1. W prawym dolnym rogu kliknij przycisk **Ponownie zaindeksuj** wideo. 
1. W **oknie dialogowym** Ponowne indeksowanie wideo wybierz pozycję **Wykrywanie** wielu języków z listy rozwijanej **Język** źródłowy wideo.

    * Gdy wideo jest indeksowane jako wielojęzyczne, strona szczegółowych informacji będzie zawierać tę opcję i pojawi się dodatkowy typ szczegółowych informacji, dzięki czemu użytkownik będzie w stanie zobaczyć, który segment jest transkrypowany w którym języku "Język mówiony".
    * Tłumaczenie na wszystkie języki jest w pełni dostępne z transkrypcji w wielu językach.
    * Wszystkie inne szczegółowe informacje będą wyświetlane w wykrytym języku głównym — czyli języku, który najczęściej pojawiał się w dźwięku.
    * Napisy w odtwarzaczu są również dostępne w wielu językach.

![Środowisko portalu](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Wybieranie identyfikacji wielojęzycznej podczas indeksowania za pomocą interfejsu API

Podczas indeksowania [lub ponownego indeksowania](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) wideo przy użyciu interfejsu API wybierz `multi-language detection` opcję w `sourceLanguage` parametrze .

### <a name="model-output"></a>Dane wyjściowe modelu

Model pobierze wszystkie języki wykryte w wideo na jednej liście

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Ponadto każde wystąpienie w sekcji transkrypcji będzie zawierać język, w którym została transkrypcja

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
* Obsługa zawartości w wielu językach z maksymalnie trzema obsługiwanymi językami.
* Jeśli dźwięk zawiera języki inne niż obsługiwana lista powyżej, wynik jest nieoczekiwany.
* Minimalna długość segmentu do wykrycia dla każdego języka — 15 sekund.
* Przesunięcie wykrywania języka wynosi średnio 3 sekundy.
* Oczekuje się, że mowa będzie ciągła. Częste zmiany między językami mogą mieć wpływ na wydajność modeli.
* Mowa osób mówiących nienatywnych może mieć wpływ na wydajność modelu (na przykład gdy osoby mówiące używają swojego natywnego języka i przełączają się na inny język).
* Model zaprojektowano tak, aby rozpoznawał mowę konwersacyjną z odpowiednią akustyką audio (a nie poleceniami głosowymi, cytami itp.).
* Tworzenie i edytowanie projektu nie jest obecnie dostępne dla filmów wideo w wielu językach.
* Niestandardowe modele językowe nie są dostępne w przypadku korzystania z wykrywania wielu języków.
* Dodawanie słów kluczowych nie jest obsługiwane.
* Podczas eksportowania plików napisów oznaczanie języka nie będzie wyświetlane.
* Interfejs API transkrypcji aktualizacji nie obsługuje wielu plików języków.

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Video Indexer](video-indexer-overview.md)
