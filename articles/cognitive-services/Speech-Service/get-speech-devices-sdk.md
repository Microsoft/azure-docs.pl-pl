---
title: Pobieranie zestawu Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Usługa Mowy współpracuje z szeroką gamą urządzeń i źródeł dźwięku. Teraz możesz przejść do następnego poziomu aplikacji mowy z dopasowanym sprzętem i oprogramowaniem. W tym artykule dowiesz się, jak uzyskać dostęp do zestawu SDK urządzeń mowy i rozpocząć tworzenie.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 756ec976c4643c1cd80552b7fa552b70fd1f5d24
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391267"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Pobierz sdk urządzeń mowy usług Cognitive Services

Zestaw SDK urządzeń mowy to wstępnie dostrojona biblioteka przeznaczona do pracy z specjalnie zaprojektowanymi zestawami deweloperskimi i różnymi konfiguracjami macierzy mikrofonu.

## <a name="choose-a-development-kit"></a>Wybierz zestaw deweloperski

|Urządzenia|Specyfikacja|Opis|Scenariusze|
|--|--|--|--|
|[URbetter T11 Development Board](https://www.alibaba.com/product-detail/URBETTER-Dev-Kits-for-Microsoft-Speech_62358746402.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mic Array, ARM SOC, WIFI, Ethernet, HDMI, KAMERA USB. <br>Linux|Zestaw SDK urządzeń mowy na poziomie branżowym, który dostosowuje macierz mikrofonu Firmy Microsoft i obsługuje rozszerzone operacje we/wy, takie jak HDMI/Ethernet i więcej urządzeń peryferyjnych USB|Transkrypcja konwersacji, Edukacja, Szpital, Roboty, OTT Box, Agent głosowy, Drive Thru|
|[Roobo Smart Audio Dev Kit](https://ddk.roobo.com)<br>[Konfiguracja](speech-devices-sdk-roobo-v1.md) / [zestawu Szybki start](speech-devices-sdk-android-quickstart.md)![Roobo Smart Audio Dev Kit](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Mic Array, ARM SOC, WIFI, Wyjście audio, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|Pierwszy zestaw SDK urządzeń mowy do dostosowania microsoft mic array i zestawu SDK do przetwarzania z przodu w celu opracowania wysokiej jakości scenariuszy transkrypcji i mowy|Transkrypcja konwersacji, inteligentny głośnik, agent głosowy, poręczny|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Konfigurowanie przewodnika](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [Szybki start](speech-devices-sdk-windows-quickstart.md)![usługi Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 mic array RGB i kamery głębi. <br>[System Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Zestaw dla programistów z zaawansowanymi czujnikami sztucznej inteligencji (AI) do tworzenia zaawansowanych modeli widzenia komputerowego i mowy. Łączy w sobie najlepszą w swojej klasie tablicę mikrofonów przestrzennych i kamerę głębi z kamerą wideo i czujnikiem orientacji — wszystko w jednym małym urządzeniu z wieloma trybami, opcjami i zestawami SDK, aby pomieścić szereg typów obliczeń.|Transkrypcja konwersacji, Robotyka, Inteligentny budynek|
|Zestaw roobo Smart Audio Dev Kit 2<br>[Konfiguracja](speech-devices-sdk-roobo-v2.md)<br>![Zestaw roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Mic Array, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|SDK urządzeń mowy drugiej generacji, który zapewnia alternatywny system operacyjny i więcej funkcji w opłacalnym projekcie referencyjnym.|Transkrypcja konwersacji, inteligentny głośnik, agent głosowy, poręczny|


## <a name="download-the-speech-devices-sdk"></a>Pobieranie sdk urządzeń mowy

Pobierz [sdk urządzeń mowy](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do sdk urządzeń mowy](https://aka.ms/sdsdk-quickstart)
