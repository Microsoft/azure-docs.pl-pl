---
title: Utwórz Azure Media Services dane wejściowe zadania na podstawie adresu URL HTTPS
description: W tym temacie pokazano, jak utworzyć Azure Media Services dane wejściowe zadania na podstawie adresu URL HTTPS.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b71262b57e9e07c58eb6b3f6e0506151063d77d5
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895360"
---
# <a name="create-a-job-input-from-an-https-url"></a>Utwórz dane wejściowe zadania na podstawie adresu URL HTTPS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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
