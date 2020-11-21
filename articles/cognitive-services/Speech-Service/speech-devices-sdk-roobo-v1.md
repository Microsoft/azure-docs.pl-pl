---
title: Speech Devices SDK roobo Smart audio dev Kit V1-Speech Service
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dotyczące rozpoczynania pracy z zestawem SDK usługi Speech Devices, roobo Smart audio dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 73eb1225ffc5fd01f9a27ca99ad2b059d45a36cf
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015294"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Urządzenie: roobo Smart audio dev Kit

Ten artykuł zawiera informacje dotyczące urządzeń dla zestawu roobo Smart audio dev Kit.

## <a name="set-up-the-development-kit"></a>Konfigurowanie zestawu deweloperskiego

1. Zestaw deweloperski ma dwa łączniki Micro USB. Lewy łącznik to Włączanie zestawu deweloperskiego i został wyróżniony jako energia na poniższym obrazie. Po prawej stronie jest on kontrolowany i jest oznaczony jako debugowanie w obrazie.

    ![Łączenie z zestawem deweloperskim](media/speech-devices-sdk/qsg-1.png)

1. Włącz zestaw deweloperski przy użyciu kabla micro USB, aby podłączyć port do komputera lub zasilacza. Zielony wskaźnik napięcia zostanie wyróżniony w górnej części tablicy.

1. Aby sterować zestawem deweloperskim, Podłącz port debugowania do komputera przy użyciu drugiego kabla micro USB. W celu zapewnienia niezawodnej komunikacji należy użyć kabla wysokiej jakości.

1. Ukierunkowanie na zestaw deweloperski dla konfiguracji cyklicznej lub liniowej.

    |Konfiguracja zestawu SDK|Orientacja|
    |-----------------------------|------------|
    |Okólnik|Pionowo, z mikrotelefonami skierowanymi do granicy|
    |Liniowe|Po stronie z mikrotelefonami wyświetlonymi na poniższej ilustracji.|

    ![Orientacja liniowa zestawu deweloperów](media/speech-devices-sdk/qsg-2.png)

1. Zainstaluj certyfikaty i Ustaw uprawnienia urządzenia dźwiękowego. W oknie wiersza polecenia wpisz następujące polecenia:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Te polecenia używają Android Debug Bridge, `adb.exe` , który jest częścią instalacji Android Studio. To narzędzie znajduje się w \[ nazwie użytkownika C:\Users] \AppData\Local\Android\Sdk\platform-Tools. Możesz dodać ten katalog do ścieżki, aby był wygodniejszy do wywoływania `adb` . W przeciwnym razie należy określić pełną ścieżkę do instalacji adb.exe w każdym poleceniu, które wywołuje `adb` .
    >
    > Jeśli zobaczysz błąd, `no devices/emulators found` Sprawdź, czy kabel USB jest podłączony i jest kablem wysokiej jakości. Możesz użyć, `adb devices` Aby sprawdzić, czy komputer może komunikować się z zestawem deweloperskim, ponieważ zwróci listę urządzeń.
    >
    > [!TIP]
    > Wycisz mikrofon i prelegenta, aby upewnić się, że pracujesz z mikrotelefonem zestawu deweloperskiego. W ten sposób nie można przypadkowo wyzwolić urządzenia z dźwiękiem z komputera.

1. Jeśli chcesz dołączyć prelegenta do zestawu deweloperów, możesz połączyć go z linią dźwiękową. Należy wybrać głośnik o wysokiej jakości z dwukierunkowym złączem 3,5 mm.

    ![Vysor audio](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informacje o programowaniu

Aby uzyskać więcej informacji na temat programowania, zobacz [Przewodnik programowania w roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Urządzenia audio

Roobo zapewnia narzędzie, które przechwytuje cały dźwięk do pamięci flash. Może pomóc w rozwiązywaniu problemów z dźwiękiem. Dostępna jest wersja narzędzia dla każdej konfiguracji zestawu deweloperskiego. W  [witrynie roobo](http://ddk.roobo.com/)wybierz urządzenie, a następnie wybierz łącze **Narzędzia roobo** w dolnej części strony.

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie przykładowej aplikacji dla systemu Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)