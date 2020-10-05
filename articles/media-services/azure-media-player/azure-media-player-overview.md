---
title: Przegląd Azure Media Player
description: Dowiedz się więcej na temat Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 6f3c173c61e9f6d82a51fcaf7e11cda47eb47c1c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87011714"
---
# <a name="azure-media-player-overview"></a>Omówienie usługi Azure Media Player #

Azure Media Player to odtwarzacz wideo w sieci Web zbudowany w celu odtwarzania zawartości multimedialnej z [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) w wielu różnych przeglądarkach i urządzeniach. Azure Media Player wykorzystuje standardy branżowe, takie jak HTML5, rozszerzenia źródła nośników (MSE) i rozszerzenia nośników zaszyfrowanych (EME), aby zapewnić wzbogacone, adaptacyjne środowisko przesyłania strumieniowego.  Jeśli te standardy nie są dostępne na urządzeniu lub w przeglądarce, Azure Media Player używa programu Flash i Silverlight jako technologii rezerwowej. Niezależnie od używanej technologii odtwarzania, deweloperzy będą mieli ujednolicony interfejs JavaScript do uzyskiwania dostępu do interfejsów API.  Pozwala to na odtwarzanie zawartości obsługiwanej przez Azure Media Services na szeroką gamę urządzeń i w przeglądarkach bez dodatkowych nakładów pracy.

Microsoft Azure Media Services umożliwia obsługę zawartości z użyciem ŁĄCZNIKów, Smooth Streaming i HLS przesyłania strumieniowego w celu odtwarzania zawartości. Azure Media Player uwzględnia te różne formaty i automatycznie odtwarza najlepsze łącze na podstawie możliwości platformy/przeglądarki. Microsoft Azure Media Services umożliwia także dynamiczne szyfrowanie zasobów przy użyciu szyfrowania kopert o wspólnym szyfrowaniu (PlayReady lub Widevine) lub bitowej wersji AES-128. Azure Media Player umożliwia odszyfrowywanie szyfrowanej zawartości w wersji PlayReady i AES-128, gdy zostanie odpowiednio skonfigurowana.  Zapoznaj się z sekcją [chronioną zawartość](azure-media-player-protected-content.md) , aby dowiedzieć się, jak ją skonfigurować.

Aby zażądać nowych funkcji, podaj pomysły lub opinie, Prześlij do witryny [UserVoice, aby uzyskać Azure Media Player](https://aka.ms/ampuservoice). W przypadku problemów, pytań lub wyszukania błędów Porzuć nam wiersz w ampinfo@microsoft.com .

[Zarejestruj](https://aka.ms/ampsignup) się, aby nigdy nie przegapić wydania i zachować aktualność, korzystając z najnowszych Azure Media Player.

> [!NOTE]
> Należy pamiętać, że Azure Media Player obsługuje tylko strumienie multimediów z Azure Media Services.

## <a name="license"></a>Licencja ##

Azure Media Player jest licencjonowana i podlega postanowieniom opisanym w postanowień licencyjnych dotyczących oprogramowania firmy Microsoft dotyczących Azure Media Player. Zobacz [plik licencji](/legal/azure-media-player/azure-media-player-license) , aby uzyskać pełne warunki. Aby uzyskać więcej informacji, zobacz [zasady zachowania poufności](https://www.microsoft.com/en-us/privacystatement/default.aspx) .

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>Następne kroki ##

- [Azure Media Player — Szybki Start](azure-media-player-quickstart.md)
