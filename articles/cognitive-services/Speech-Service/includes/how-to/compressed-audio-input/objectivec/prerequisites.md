---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421750"
---
Obsługa skompresowanego dźwięku jest implementowana przy użyciu [GStreamer](https://gstreamer.freedesktop.org). Ze względów licencjonowania GStreamer pliki binarne nie są kompilowane i połączone z zestawem Speech SDK. Zamiast tego Biblioteka otoki zawierająca te funkcje musi być skompilowana i dostarczana z aplikacjami korzystającymi z zestawu SDK.

Aby skompilować tę bibliotekę otoki, najpierw Pobierz i zainstaluj [zestaw SDK GStreamer](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Następnie Pobierz projekt **Xcode** dla [biblioteki otoki](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Otwórz projekt w programie **Xcode** i skompiluj go dla **ogólnego celu urządzenia z systemem iOS** — *nie* będzie on działał w celu skompilowania go dla określonego celu.

Krok kompilacja spowoduje wygenerowanie dynamicznego zestawu struktury z biblioteką dynamiczną dla wszystkich potrzebnych architektur o nazwie `GStreamerWrapper.framework`.

Ta struktura musi być uwzględniona we wszystkich aplikacjach, które używają skompresowanych strumieni audio z zestawem SDK usługi Speech Service.

Zastosuj następujące ustawienia w projekcie **Xcode** , aby to zrobić:

1. Skopiuj właśnie `GStreamerWrapper.framework` utworzoną i strukturę zestawu Speech SDK Cognitive Services, który można pobrać z tego [miejsca](https://aka.ms/csspeech/iosbinary), do katalogu zawierającego przykładowy projekt.
1. Dostosuj ścieżki do struktur w *ustawieniach projektu*.
   1. Na karcie **Ogólne** w nagłówku **osadzone pliki binarne** Dodaj bibliotekę zestawu SDK jako strukturę: **Dodaj osadzone pliki binarne** > **Dodaj inne...** > przejdź do wybranego katalogu i wybierz obie platformy.
   1. Przejdź do karty **Build Settings** (Ustawienia kompilacji) i aktywuj ustawienia **All** (Wszystko).
1. Dodaj katalog `$(SRCROOT)/..` do pozycji _Framework Search Paths_ (Ścieżki wyszukiwania struktury) w ramach nagłówka **Search Paths** (Ścieżki wyszukiwania).
