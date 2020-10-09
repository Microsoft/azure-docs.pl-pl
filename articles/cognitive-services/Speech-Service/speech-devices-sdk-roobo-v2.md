---
title: Speech Devices SDK roobo inteligentne audio dev Kit v2-Speech Service
titleSuffix: Azure Cognitive Services
description: Wymagania wstępne i instrukcje dotyczące rozpoczynania pracy z zestawem SDK usługi Speech Devices, roobo Smart audio dev Kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80371578"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Urządzenie: roobo Smart audio dev Kit v2

Ten artykuł zawiera informacje specyficzne dla urządzenia dla roobo Smart audio dev Kit2.

## <a name="set-up-the-development-kit"></a>Konfigurowanie zestawu deweloperskiego

1. Zestaw deweloperski ma dwa łączniki Micro USB. Lewy łącznik to Włączanie zestawu deweloperskiego i został wyróżniony jako energia na poniższym obrazie. Po prawej stronie jest on kontrolowany i jest oznaczony jako debugowanie w obrazie. 
    ![Łączenie z zestawem deweloperskim](media/speech-devices-sdk/roobo-v2-connections.png)
1. Włącz zestaw deweloperski przy użyciu kabla micro USB, aby podłączyć port do komputera lub zasilacza. Zielony wskaźnik napięcia zostanie wyróżniony w górnej części tablicy.
1. Aby sterować zestawem deweloperskim, Podłącz port debugowania do komputera przy użyciu drugiego kabla micro USB. Aby zapewnić niezawodne komunikację, należy użyć kabla wysokiej jakości.
1. Orientuje się, aby zestaw programistyczny został rozbudowany cyklicznie, z mikroportami, jak pokazano powyżej.


## <a name="development-information"></a>Informacje o programowaniu

Aby uzyskać więcej informacji na temat programowania, zobacz [Przewodnik programowania w roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Nagrywanie/odtwarzanie audio

Operacje audio DDK2 można wykonać w następujący sposób:
* Używaj bibliotek Open Source ALSA i ich aplikacji.
* Użyj interfejsu appmainprog do tworzenia aplikacji. DDK2 środowisko oprogramowania powiązane z dźwiękiem używa standardowej platformy ALSA, można użyć libasound. Tak, aby bezpośrednio opracowywać oprogramowanie. Dzięki temu można używać ALSA arecord i aplay bezpośrednio do nagrywania i odtwarzania dźwięku.
