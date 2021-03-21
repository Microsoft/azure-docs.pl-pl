---
title: Odtwarzanie przy użyciu Azure Media Player na platformie Azure
description: Azure Media Player to odtwarzacz wideo w sieci Web zbudowany w celu odtwarzania zawartości multimedialnej z Microsoft Azure Media Services w wielu różnych przeglądarkach i urządzeniach.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: inhenkel
ms.openlocfilehash: cf4916341a97868de757804b570212f1cc1105b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898125"
---
# <a name="playback-with-azure-media-player"></a>Odtwarzanie przy użyciu Azure Media Player

Azure Media Player to odtwarzacz wideo w sieci Web zbudowany w celu odtwarzania zawartości multimedialnej z Microsoft Azure Media Services w wielu różnych przeglądarkach i urządzeniach. Azure Media Player wykorzystuje standardy branżowe, takie jak HTML5, rozszerzenia źródła nośników (MSE) i rozszerzenia nośników zaszyfrowanych (EME), aby zapewnić wzbogacone, adaptacyjne środowisko przesyłania strumieniowego. Jeśli te standardy nie są dostępne na urządzeniu lub w przeglądarce, Azure Media Player używa programu Flash i Silverlight jako technologii rezerwowej. Niezależnie od używanej technologii odtwarzania, deweloperzy będą mieli ujednolicony interfejs JavaScript do uzyskiwania dostępu do interfejsów API. Pozwala to na odtwarzanie zawartości obsługiwanej przez Azure Media Services na szeroką gamę urządzeń i w przeglądarkach bez dodatkowych nakładów pracy.

Microsoft Azure Media Services umożliwia obsługę zawartości przy użyciu HLS, ŁĄCZNIKów Smooth Streaming transmisji strumieniowych, aby odtwarzać zawartość. Azure Media Player uwzględnia te różne formaty i automatycznie odtwarza najlepsze łącze na podstawie możliwości platformy/przeglądarki. Media Services umożliwia również szyfrowanie dynamiczne zasobów za pomocą szyfrowania PlayReady lub szyfrowania koperty AES-128. Azure Media Player umożliwia odszyfrowywanie szyfrowanej zawartości w wersji PlayReady i AES-128, gdy zostanie odpowiednio skonfigurowana.

> [!NOTE]
> Odtwarzanie HTTPS jest wymagane do zaszyfrowanej zawartości Widevine.

## <a name="use-azure-media-player-demo-page"></a>Użyj Azure Media Player stronie demonstracyjnej

### <a name="start-using"></a>Rozpocznij korzystanie z

Korzystając ze [strony demonstracyjnej Azure Media Player](https://aka.ms/azuremediaplayer) , można odtwarzać Azure Media Services przykłady lub własny strumień.  

Aby odtworzyć nowe wideo, wklej inny adres URL i naciśnij przycisk **Aktualizuj**.

Aby skonfigurować różne opcje odtwarzania (na przykład Tech, language lub Encryption), naciśnij przycisk **Zaawansowane opcje**.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Monitorowanie diagnostyki strumienia wideo

Możesz użyć [strony demonstracyjnej Azure Media Player](https://aka.ms/azuremediaplayer) do monitorowania diagnostyki strumienia wideo.

![Diagnostyka Azure Media Player](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Konfigurowanie Azure Media Player w kodzie HTML

Azure Media Player można łatwo skonfigurować. Uzyskanie podstawowego odtwarzania zawartości multimedialnej z konta Media Services trwa zaledwie kilka minut. Szczegółowe informacje na temat konfigurowania i konfigurowania Azure Media Player można znaleźć w [dokumentacji Azure Media Player](../azure-media-player/azure-media-player-overview.md) .

## <a name="additional-notes"></a>Uwagi dodatkowe

* Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja usługi Azure Media Player](../azure-media-player/azure-media-player-overview.md)
* [Przykłady Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)