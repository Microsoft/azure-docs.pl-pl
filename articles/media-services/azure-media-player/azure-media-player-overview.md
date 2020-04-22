---
title: Omówienie programu Azure Media Player
description: Dowiedz się więcej o usłudze Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726522"
---
# <a name="azure-media-player-overview"></a>Omówienie usługi Azure Media Player #

Azure Media Player to internetowy odtwarzacz wideo stworzony do odtwarzania zawartości multimedialnej z [usługi Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) na różnych przeglądarkach i urządzeniach. Usługa Azure Media Player korzysta ze standardów branżowych, takich jak HTML5, Rozszerzenia źródeł multimediów (MSE) i Rozszerzenia zaszyfrowanych multimediów (EME), aby zapewnić wzbogacone środowisko adaptacyjnego przesyłania strumieniowego.  Jeśli te standardy nie są dostępne na urządzeniu lub w przeglądarce, program Azure Media Player używa programów Flash i Silverlight jako technologii rezerwowej. Niezależnie od zastosowanej technologii odtwarzania deweloperzy będą mieli ujednolicony interfejs JavaScript, aby uzyskać dostęp do interfejsów API.  Dzięki temu zawartość obsługiwana przez usługę Azure Media Services ma być odtwarzana na wielu różnych urządzeniach i przeglądarkach bez dodatkowego wysiłku.

Usługa Microsoft Azure Media Services umożliwia odtwarzanie zawartości za pomocą formatów DASH, Smooth Streaming i HLS do odtwarzania zawartości. Program Azure Media Player uwzględnia te różne formaty i automatycznie odtwarza najlepsze łącze oparte na możliwościach platformy/przeglądarki. Usługa Microsoft Azure Media Services umożliwia również dynamiczne szyfrowanie zasobów za pomocą wspólnego szyfrowania (PlayReady lub Widevine) lub szyfrowania kopert AES-128 bitów. Usługa Azure Media Player umożliwia odszyfrowywanie zawartości zaszyfrowanej bitem PlayReady i AES-128, gdy jest odpowiednio skonfigurowana.  Zobacz sekcję [Zawartość chroniona,](azure-media-player-protected-content.md) aby dowiedzieć się, jak to skonfigurować.

Aby poprosić o nowe funkcje, przedstawić pomysły lub opinie, prześlij do [UserVoice for Azure Media Player](https://aka.ms/ampuservoice). Jeśli masz i konkretne problemy, pytania lub znaleźć jakieś ampinfo@microsoft.combłędy, napisz do nas na .

[Zarejestruj się,](https://aka.ms/ampsignup) aby nigdy nie przegapić wydania i bądź na bieżąco z najnowszymi ofertami oferowanymi przez program Azure Media Player.

> [!NOTE]
> Należy pamiętać, że program Azure Media Player obsługuje tylko strumienie multimediów z usługi Azure Media Services.

## <a name="license"></a>Licencja ##

Usługa Azure Media Player jest licencjonowana i podlega warunkom określonym w postanowieniach licencyjnych dotyczących oprogramowania firmy Microsoft dla programu Azure Media Player. Pełnych warunków można znaleźć [w pliku licencji.](azure-media-player-license.md) Więcej informacji można znaleźć w [Oświadczeniu o ochronie prywatności.](https://www.microsoft.com/en-us/privacystatement/default.aspx)

Prawa autorskie 2015 Microsoft Corporation.

## <a name="next-steps"></a>Następne kroki ##

- [Szybki start programu Azure Media Player](azure-media-player-quickstart.md)