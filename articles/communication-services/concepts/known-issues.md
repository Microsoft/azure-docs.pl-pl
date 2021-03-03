---
title: Azure Communication Services — często zadawane pytania/znane problemy
description: Dowiedz się więcej o usłudze Azure Communications Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e58335f1e266af651eb5867ca98e9ec979803b94
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101655891"
---
# <a name="faq--known-issues"></a>Często zadawane pytania/znane problemy
Ten artykuł zawiera informacje o znanych problemach i często zadawanych pytaniach dotyczących usług komunikacyjnych platformy Azure.

## <a name="faq"></a>Często zadawane pytania

### <a name="why-is-the-quality-of-my-video-degraded"></a>Dlaczego jakość wideo jest nieznacznie obniżona?

Jakość strumieni wideo jest określana na podstawie rozmiaru modułu renderowania po stronie klienta, który został użyty do zainicjowania tego strumienia. Podczas subskrybowania strumienia zdalnego odbiorca ustali własne rozwiązanie w oparciu o wymiary modułu renderowania po stronie klienta nadawcy.

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Dlaczego nie można wyliczyć/wybrać urządzeń MIC/prelegenta w przeglądarce Safari?

Aplikacje nie mogą wyliczyć/wybrać urządzeń MIC/prelegenta (takich jak Bluetooth) w systemie iOS/iPad. Jest to ograniczenie systemu operacyjnego — istnieje zawsze tylko jedno urządzenie.

W przypadku programu Safari w systemie MacOS — aplikacja nie może wyliczyć/wybrać głośnika za pośrednictwem usług komunikacyjnych Device Manager — należy je wybrać za pośrednictwem systemu operacyjnego. Jeśli używasz programu Chrome w systemie MacOS, aplikacja będzie mogła wyliczyć/wybrać urządzenia za pomocą Device Manager usług komunikacyjnych.

## <a name="known-issues"></a>Znane problemy

Ta sekcja zawiera informacje o znanych problemach związanych z usługami Azure Communications Services.

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Wielokrotne przełączanie urządzeń wideo może spowodować tymczasowe zatrzymanie przesyłania strumieniowego wideo

Przełączenie między urządzeniami wideo może spowodować wstrzymanie strumienia wideo w czasie, gdy strumień zostanie pobrany z wybranego urządzenia.

#### <a name="possible-causes"></a>Możliwe przyczyny
Przetwarzanie strumieniowe z i przełączanie między urządzeniami multimedialnymi jest czasochłonne. Przełączenie często może spowodować spadek wydajności. Deweloperzy są zachęcani do zatrzymania jednego strumienia urządzenia przed rozpoczęciem kolejnego.
