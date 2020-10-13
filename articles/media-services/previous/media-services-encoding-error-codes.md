---
title: Kody błędów kodowania Azure Media Services | Microsoft Docs
description: Ten temat zawiera listę kodów błędów, które mogą zostać zwrócone w przypadku wystąpienia błędu podczas wykonywania zadania kodowania.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6e56dbe1d1236a567ed6f59acfcca325a6c9ee7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269033"
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

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Pokrewne artykuły:
* [Wykonywanie zaawansowanych zadań kodowania przez dostosowanie Media Encoder Standard ustawień wstępnych](media-services-custom-mes-presets-with-dotnet.md)
* [Limity przydziału i ograniczenia](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
