---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421750"
---
Obsługa skompresowanego dźwięku jest zaimplementowana za pomocą [GStreamer](https://gstreamer.freedesktop.org). Ze względów licencyjnych pliki binarne GStreamer nie są kompilowane i połączone z zestawem SDK mowy. Zamiast tego biblioteka otoki zawierająca te funkcje musi zostać zbudowana i dostarczona z aplikacjami przy użyciu sdk.

Aby utworzyć tę bibliotekę otoki, najpierw pobierz i zainstaluj [SDK GStreamer](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Następnie pobierz projekt **Xcode** dla [biblioteki otoki](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Otwórz projekt w **xcode** i skompiluj go dla **ogólnego urządzenia z systemem iOS** docelowego — *nie* będzie działać, aby utworzyć go dla określonego obiektu docelowego.

Krok kompilacji wygeneruje dynamiczny pakiet ramowy z biblioteką `GStreamerWrapper.framework`dynamiczną dla wszystkich niezbędnych architektur o nazwie .

Ta struktura musi być uwzględniona we wszystkich aplikacjach, które używają skompresowanych strumieni audio z SDK usługi mowy.

Aby to osiągnąć, zastosuj następujące ustawienia w projekcie **Xcode:**

1. Skopiuj `GStreamerWrapper.framework` właśnie utworzony i ramy zestawu SDK mowy usług Cognitive Services, które można pobrać [z tego miejsca,](https://aka.ms/csspeech/iosbinary)do katalogu zawierającego przykładowy projekt.
1. Dostosuj ścieżki do struktur w *ustawieniach projektu*.
   1. Na karcie **Ogólne** w nagłówku **Osadzone pliki binarne** dodaj bibliotekę SDK jako platformę: **Dodaj osadzone pliki binarne** > **Dodaj inne...** > Przejdź do wybranego katalogu i wybierz obie struktury.
   1. Przejdź do karty **Build Settings** (Ustawienia kompilacji) i aktywuj ustawienia **All** (Wszystko).
1. Dodaj katalog `$(SRCROOT)/..` do pozycji _Framework Search Paths_ (Ścieżki wyszukiwania struktury) w ramach nagłówka **Search Paths** (Ścieżki wyszukiwania).
