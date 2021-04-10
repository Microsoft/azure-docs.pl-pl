---
title: Kody błędów kodowania Azure Media Services | Microsoft Docs
description: Ten temat zawiera listę kodów błędów, które mogą zostać zwrócone w przypadku wystąpienia błędu podczas wykonywania zadania kodowania.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 7e6848fb49dd63fa67a639d09754a28dd5953a32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013486"
---
# <a name="encoding-error-codes"></a>Kody błędów kodowania

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Poniższa tabela zawiera listę kodów błędów, które mogą zostać zwrócone w przypadku wystąpienia błędu podczas wykonywania zadania kodowania.  Aby uzyskać szczegółowe informacje o błędach w kodzie .NET, należy użyć klasy [ErrorDetails](/previous-versions/azure/jj126075(v=azure.100)) . Aby uzyskać szczegółowe informacje o błędach w kodzie REST, użyj interfejsu API REST [ErrorDetail](/rest/api/media/operations/errordetail) .

| ErrorDetail. Code | Możliwe przyczyny błędu |
| --- | --- |
| Nieznane |Nieznany błąd podczas wykonywania zadania |
| ErrorDownloadingInputAssetMalformedContent |Kategoria błędów obejmująca błędy podczas pobierania wejściowego zasobu, takie jak nieprawidłowe nazwy plików, pliki o zerowej długości, niepoprawne formaty itd. |
| ErrorDownloadingInputAssetServiceFailure |Kategoria błędów obejmująca problemy po stronie usługi — na przykład błędy sieci lub magazynu podczas pobierania. |
| ErrorParsingConfiguration |Kategoria błędów, w której zadanie \<see cref="MediaTask.PrivateData"/> (Konfiguracja) jest nieprawidłowe, na przykład konfiguracja nie jest prawidłowym ustawieniem wstępnym systemu lub zawiera nieprawidłowe dane XML. |
| ErrorExecutingTaskMalformedContent |Kategoria błędów podczas wykonywania zadania, w którym problemy w plikach multimediów wejściowych powodują awarię. |
| ErrorExecutingTaskUnsupportedFormat |Kategoria błędów, w których procesor multimediów nie może przetworzyć dostarczonych plików — format nośnika nie jest obsługiwany lub nie jest zgodny z konfiguracją. Na przykład próba wygenerowania danych wyjściowych z tylko dźwiękiem z elementu zawartości, który ma tylko wideo |
| ErrorProcessingTask |Kategoria innych błędów napotykanych przez procesor multimediów podczas przetwarzania zadania, które nie są związane z zawartością. |
| ErrorUploadingOutputAsset |Kategoria błędów podczas przekazywania elementu zawartości wyjściowej |
| ErrorCancelingTask |Kategoria błędów, która obejmuje błędy podczas próby anulowania zadania |
| TransientError |Kategoria błędów, która obejmuje problemy przejściowe (np. tymczasowe problemy z siecią w usłudze Azure Storage) |

Aby uzyskać pomoc od zespołu **Media Services** , Otwórz [bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Wyraź opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywanie zaawansowanych zadań kodowania przez dostosowanie Media Encoder Standard ustawień wstępnych](media-services-custom-mes-presets-with-dotnet.md)
* [Limity przydziału i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
