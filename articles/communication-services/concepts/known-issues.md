---
title: Usługi komunikacyjne platformy Azure — znane problemy
description: Informacje o znanych problemach związanych z usługą Azure Communications Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628159"
---
# <a name="known-issues-azure-communication-services"></a>Znane problemy: usługi komunikacyjne Azure

Ten artykuł zawiera informacje o znanych problemach związanych z usługami komunikacyjnymi platformy Azure.

## <a name="video-streaming-quality-on-chromeandroid"></a>Jakość przesyłania strumieniowego wideo w programie Chrome/Android 

Wydajność przesyłania strumieniowego wideo może być obniżona w przypadku programu Chrome Android.

### <a name="possible-causes"></a>Możliwe przyczyny
Jakość zdalnych strumieni zależy od rozdzielczości modułu renderowania po stronie klienta, który został użyty do zainicjowania tego strumienia. Podczas subskrybowania strumienia zdalnego odbiorca ustali własne rozwiązanie w oparciu o wymiary modułu renderowania po stronie klienta nadawcy.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Nie wykryto mikrofonów z zestawem słuchawkowym Bluetooth

Mogą wystąpić problemy z połączeniem zestawu słuchawkowego Bluetooth z wywołaniem usług komunikacyjnych.

### <a name="possible-causes"></a>Możliwe przyczyny
Nie jest dostępna opcja wybierania mikrofonu Bluetooth w systemie iOS.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Wielokrotne przełączanie urządzeń wideo może spowodować tymczasowe zatrzymanie przesyłania strumieniowego wideo

Przełączenie między urządzeniami wideo może spowodować wstrzymanie strumienia wideo w czasie, gdy strumień zostanie pobrany z wybranego urządzenia.

### <a name="possible-causes"></a>Możliwe przyczyny
Przetwarzanie strumieniowe z i przełączanie między urządzeniami multimedialnymi jest czasochłonne. Przełączenie często może spowodować spadek wydajności. Deweloperzy są zachęcani do zatrzymania jednego strumienia urządzenia przed rozpoczęciem kolejnego.
