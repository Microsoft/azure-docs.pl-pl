---
title: Przegląd Azure Media Player
description: Dowiedz się więcej na temat Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.openlocfilehash: ec36f6bd19728d47b73a0186fb1fb192ba57f4cf
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449821"
---
# <a name="azure-media-player-overview"></a>Omówienie usługi Azure Media Player #

Azure Media Player to odtwarzacz wideo w sieci Web, który odtwarza zawartość multimedialną z [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) na wielu różnych przeglądarkach i urządzeniach. Azure Media Player używa standardów branżowych, takich jak HTML5, rozszerzenia źródła nośników (MSE) i rozszerzeń zaszyfrowanej multimediów (EME), aby zapewnić wzbogacone, adaptacyjne środowisko przesyłania strumieniowego.  Jeśli te standardy nie są dostępne na urządzeniu lub w przeglądarce, Azure Media Player używa programu Flash i Silverlight jako technologii rezerwowej. Niezależnie od używanej technologii odtwarzania, deweloperzy mają ujednolicony interfejs JavaScript do uzyskiwania dostępu do interfejsów API, dzięki czemu zawartość obsługiwana przez Azure Media Services mogą być odtwarzane między szeroką gamę urządzeń i przeglądarek bez dodatkowych nakładów pracy.

Microsoft Azure Media Services umożliwia obsługę zawartości z użyciem ŁĄCZNIKów, Smooth Streaming i HLS przesyłania strumieniowego w celu odtwarzania zawartości. Azure Media Player uwzględnia te różne formaty i automatycznie odtwarza najlepsze łącze na podstawie możliwości platformy/przeglądarki. Microsoft Azure Media Services zapewnia także dynamiczne szyfrowanie zasobów przy użyciu szyfrowania kopert o wspólnym szyfrowaniu (PlayReady lub Widevine) lub bitowej wersji AES-128. Azure Media Player umożliwia odszyfrowywanie szyfrowanej zawartości w wersji PlayReady i AES-128, gdy zostanie odpowiednio skonfigurowana.  Aby dowiedzieć się, jak skonfigurować odtwarzacz, zobacz sekcję [chroniona zawartość](azure-media-player-protected-content.md) .

Aby zażądać nowych funkcji, podaj pomysły lub opinie, prześlij je do usługi [UserVoice, aby uzyskać Azure Media Player](https://aka.ms/ampuservoice). W przypadku problemów, pytań lub wyszukania błędów Porzuć nam wiersz w ampinfo@microsoft.com .

> [!NOTE]
> Należy pamiętać, że Azure Media Player obsługuje tylko strumienie multimediów z Azure Media Services.

## <a name="license"></a>Licencja ##

Azure Media Player jest licencjonowana i podlega postanowieniom opisanym w postanowień licencyjnych dotyczących oprogramowania firmy Microsoft dotyczących Azure Media Player. Zobacz [plik licencji](/legal/azure-media-player/azure-media-player-license) , aby uzyskać pełne warunki. Aby uzyskać więcej informacji, zobacz [zasady zachowania poufności](https://www.microsoft.com/en-us/privacystatement/default.aspx)informacji.

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)
