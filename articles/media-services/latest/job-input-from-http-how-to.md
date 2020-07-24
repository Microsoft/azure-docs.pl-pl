---
title: Utwórz Azure Media Services dane wejściowe zadania na podstawie adresu URL HTTPS | Microsoft Docs
description: W tym temacie pokazano, jak utworzyć Azure Media Services dane wejściowe zadania na podstawie adresu URL HTTPS.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: 1ff2bc6d4bb4f7b983787b287fbb27e1c33cc179
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022994"
---
# <a name="create-a-job-input-from-an-https-url"></a>Utwórz dane wejściowe zadania na podstawie adresu URL HTTPS

W przypadku usługi Media Services 3 po przesłaniu zadań w celu przetworzenia wideo należy poinformować usługę Media Services o tym, gdzie można znaleźć wejściowe wideo. Jedną z możliwości jest określenie adresu URL protokołu HTTPS jako danych wejściowych zadania (jak pokazano w tym przykładzie). Pamiętaj, że obecnie usługa AMS w wersji 3 nie obsługuje fragmentarycznego kodowania transferu za pośrednictwem adresów URL HTTPS. Aby zapoznać się z pełnymi przykładami, zobacz ten [przykład serwisu GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Przed rozpoczęciem opracowywania, zobacz [Tworzenie aplikacji przy użyciu interfejsów api Media Services v3](media-services-apis-overview.md) (w tym informacje na temat uzyskiwania dostępu do interfejsów API, konwencji nazewnictwa itp.).

## <a name="net-sample"></a>Przykład platformy .NET

Poniższy kod przedstawia sposób tworzenia zadania z danymi wejściowymi HTTPS adresu URL.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Następne kroki

[Utwórz dane wejściowe zadania z pliku lokalnego](job-input-from-local-file-how-to.md).
