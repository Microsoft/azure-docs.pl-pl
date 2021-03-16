---
title: Dokumentacja zestawu SDK urządzeń mowy
titleSuffix: Azure Cognitive Services
description: Informacje o wersji zawierają dziennik aktualizacji, ulepszeń, poprawek błędów i zmian w zestawie SDK urządzeń mowy. Ten artykuł jest aktualizowany przy użyciu każdej wersji zestawu SDK urządzeń typu Speech.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 1b2418b248e995b0f64eec82ccb477b15583c81e
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562335"
---
# <a name="release-notes-speech-devices-sdk"></a>Informacje o wersji: zestaw SDK urządzeń mowy

W poniższych sekcjach przedstawiono zmiany w najnowszych wersjach.

## <a name="speech-devices-sdk-1150"></a>1.15.0 zestawu SDK urządzeń mowy:

- Uaktualniono do nowego stosu audio firmy Microsoft (MAS) z ulepszoną Beamforming i redukcją szumów.
- Zmniejszono rozmiar binarny do 70% w zależności od celu.
- Obsługa [usługi Azure Percept audio](https://docs.microsoft.com/azure/azure-percept/overview-azure-percept-audio) przy użyciu [wersji binarnej](https://aka.ms/sdsdk-download-APAudio).
- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 1.15.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).

## <a name="speech-devices-sdk-1110"></a>1.11.0 zestawu SDK urządzeń mowy:

- Obsługa [dowolnych geometrie macierzy mikrofonu](how-to-devices-microphone-array-configuration.md) i ustawianie kąta pracy za pomocą [pliku konfiguracji](https://aka.ms/sdsdk-micarray-json).
- Obsługa [zestawu DDK Urbetter](http://www.urbetter.com/products_56/278.html).
- Wydane pliki binarne dla [głośnika GGEC](https://aka.ms/sdsdk-download-speaker) użytego w [przykładowym Asystencie głosu](https://aka.ms/sdsdk-speaker).
- Wydane pliki binarne dla systemów [Linux ARM32](https://aka.ms/sdsdk-download-linux-arm32) i [Linux ARM 64](https://aka.ms/sdsdk-download-linux-arm64) dla Raspberry Pi i podobnych urządzeń.
- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 1.11.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).

## <a name="speech-devices-sdk-190"></a>1.9.0 zestawu SDK urządzeń mowy:

- Podano początkowe pliki binarne dla [zestawu DDK Urbetter](https://aka.ms/sdsdk-download-urbetter) (Linux arm64).
- Roobo V1 teraz używa Maven dla zestawu Speech SDK
- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 1.9.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).

## <a name="speech-devices-sdk-170"></a>1.7.0 zestawu SDK urządzeń mowy:

- System Linux ARM jest teraz obsługiwany.
- Dostępne są początkowe pliki binarne dla [zestawu DDK systemu roobo v2](https://aka.ms/sdsdk-download-roobov2) (Linux arm64).
- Użytkownicy systemu Windows mogą użyć programu `AudioConfig.fromDefaultMicrophoneInput()` lub `AudioConfig.fromMicrophoneInput(deviceName)` , aby określić mikrofon, który ma być używany.
- Rozmiar biblioteki został zoptymalizowany.
- Obsługa rozpoznawania wieloskładnikowego przy użyciu tego samego obiektu rozpoznawania mowy/konwersji.
- Rozwiąż problem okazjonalny, gdy proces przestanie odpowiadać podczas zatrzymywania rozpoznawania.
- Przykładowe aplikacje zawierają teraz przykładowy plik uczestników. właściwości, aby przedstawić format pliku.
- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 1.7.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).

## <a name="speech-devices-sdk-160"></a>1.6.0 zestawu SDK urządzeń mowy:

- Obsługa [usługi Azure urządzenia Kinect DK](https://azure.microsoft.com/services/kinect-dk/) w systemach Windows i Linux przy użyciu typowej [aplikacji przykładowej](./speech-devices-sdk.md)
- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 1.6.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).

## <a name="speech-devices-sdk-151"></a>1.5.1 zestawu SDK urządzeń mowy:

- Uwzględnij [transkrypcję konwersacji](./conversation-transcription.md) w przykładowej aplikacji.
- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 1.5.1. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech Devices SDK 1.5.0:2019 — może wydać

- Zestaw SDK urządzeń mowy jest teraz w wersji GA i nie jest już w trakcie przetwarzania warunkowego.
- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 1.5.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).
- Nowa technologia słowa kluczowego zapewnia znaczące ulepszenia jakości, zobacz artykuł wprowadzenie zmian.
- Nowy potok przetwarzania audio dla ulepszonego rozpoznawania pól.

**Fundamentalne zmiany**

- Ze względu na nową technologię słów kluczowych wszystkie słowa kluczowe muszą zostać utworzone w naszym ulepszonym portalu słów kluczowych. Aby w pełni usunąć stare słowa kluczowe z urządzenia, Odinstaluj starą aplikację.
  - ADB Odinstaluj com. Microsoft. cognitiveservices. Speech. Samples. sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0:2019-kwi Release

- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 1.4.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1:2019 — wydanie mar

- Zaktualizowano składnik [SDK mowy](./speech-sdk.md) do wersji 1.3.1. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).
- Obsługa zaktualizowanych słów kluczowych, zobacz istotne zmiany.
- Przykładowa aplikacja dodaje wybór języka dla rozpoznawania mowy i tłumaczenia.

**Fundamentalne zmiany**

- [Instalacja słowa kluczowego](./custom-keyword-basics.md) została uproszczona, jest teraz częścią aplikacji i nie wymaga osobnej instalacji na urządzeniu.
- Rozpoznawanie słów kluczowych zostało zmienione i obsługiwane są dwa zdarzenia.
  - `RecognizingKeyword,` wskazuje, że wynik mowy zawiera (niezweryfikowany) tekst słowa kluczowego.
  - `RecognizedKeyword`wskazuje, że rozpoznawanie słowa kluczowego zostało zakończone rozpoznanie danego słowa kluczowego.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK 1.1.0:2018-lis Release

- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 1.1.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).
- Ulepszona precyzja rozpoznawania mowy z użyciem udoskonalonego algorytmu przetwarzania audio.
- Przykładowa aplikacja dodała chińską obsługę rozpoznawania mowy.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1:2018 — wydanie z października

- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 1.0.1. Aby uzyskać więcej informacji, zobacz informacje o [wersji](./releasenotes.md).
- Dokładność rozpoznawania mowy zostanie ulepszona dzięki ulepszonemu algorytmowi przetwarzania dźwięku
- Została naprawiona jedna usterka sesji audio ciągłego rozpoznawania.

**Fundamentalne zmiany**

- W tej wersji wprowadzono kilka istotnych zmian. Sprawdź [Tę stronę](https://aka.ms/csspeech/breakingchanges_1_0_0) , aby uzyskać szczegółowe informacje dotyczące interfejsów API.
- Pliki modelu KWS są niezgodne z zestawem SDK urządzeń Speech 1.0.1. Istniejące pliki słów kluczowych zostaną usunięte po zapisaniu nowych plików słów kluczowych na urządzeniu.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK 0.5.0:2018-sie Release

- Ulepszono dokładność rozpoznawania mowy przez naprawianie usterki w kodzie przetwarzania audio.
- Zaktualizowano składnik [zestawu Speech SDK](./speech-sdk.md) do wersji 0.5.0. Aby uzyskać więcej informacji, zobacz informacje o [wersji](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK 0.2.12733:2018 — może wydać

Pierwsza publiczna wersja zapoznawcza zestawu SDK urządzeń Cognitive Services Speech.