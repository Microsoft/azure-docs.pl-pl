---
title: Tworzenie danych wejściowych zadania z pliku lokalnego
description: W tym artykule pokazano, jak utworzyć Azure Media Services dane wejściowe zadania z pliku lokalnego.
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
ms.openlocfilehash: 6fe1274c6f659f97e528e788116949fce60e6020
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960069"
---
# <a name="create-a-job-input-from-a-local-file"></a>Tworzenie danych wejściowych zadania z pliku lokalnego

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W przypadku usługi Media Services 3 po przesłaniu zadań w celu przetworzenia wideo należy poinformować usługę Media Services o tym, gdzie można znaleźć wejściowe wideo. Wejściowy film wideo może być przechowywany jako zasób usługi Media. w takim przypadku tworzony jest wejściowy zasób oparty na pliku (przechowywany lokalnie lub w usłudze Azure Blob Storage). W tym temacie pokazano, jak utworzyć dane wejściowe zadania z pliku lokalnego. Aby zapoznać się z pełnymi przykładami, zobacz ten [przykład serwisu GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Wymagania wstępne

* [Utwórz konto Media Services](./account-create-how-to.md).

## <a name="net-sample"></a>Przykład platformy .NET

Poniższy kod pokazuje, jak utworzyć zasób wejściowy i użyć go jako danych wejściowych dla zadania. Funkcja CreateInputAsset wykonuje następujące akcje:

* Tworzy element zawartości
* Pobiera zapisywalny [adres URL sygnatury dostępu współdzielonego](../../storage/common/storage-sas-overview.md) do [kontenera zasobów w magazynie](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container)
* Przekazanie pliku do kontenera w magazynie przy użyciu adresu URL sygnatury dostępu współdzielonego

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Poniższy fragment kodu tworzy wyjściowy element zawartości, jeśli jeszcze nie istnieje:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Poniższy fragment kodu przesyła zadanie kodowania:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Następne kroki

[Utwórz dane wejściowe zadania na podstawie adresu URL https](job-input-from-http-how-to.md).
