---
title: Przekazywanie multimediów
titleSuffix: Azure Media Services
description: Dowiedz się, jak przekazać multimedia do przesyłania strumieniowego lub kodowania.
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
ms.openlocfilehash: 0bdb2c36bc895c9229e4c04e9e0d76aa852bd139
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297310"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Przekazywanie multimediów do przesyłania strumieniowego lub kodowania

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W Media Services można przekazać pliki cyfrowe (Multimedia) do kontenera obiektów BLOB skojarzonego z elementem zawartości. Jednostka [zasobu](/rest/api/media/operations/asset) może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki napisów (oraz metadane dotyczące tych plików). Gdy pliki zostaną przekazane do kontenera zasobów, zawartość jest bezpiecznie przechowywana w chmurze w celu dalszej przetwarzania i przesyłania strumieniowego.

Przed rozpoczęciem pracy należy zebrać lub myśleć o kilku wartościach.

1. Lokalna ścieżka pliku do pliku, który chcesz przekazać
1. Identyfikator elementu zawartości dla elementu zawartości (kontenera)
1. Nazwa, która ma być używana dla przekazanego pliku, łącznie z rozszerzeniem.
1. Nazwa konta magazynu, z którego korzystasz
1. Klucz dostępu do konta magazynu

## <a name="portal"></a>[Portal](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[Interfejs wiersza polecenia](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Po [utworzeniu elementu zawartości przy użyciu programu Poster lub innej metody REST i uzyskać adres URL sygnatury dostępu współdzielonego dla elementu zawartości](how-to-create-asset.md?tabs=rest)Użyj interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md) lub [zestawu SDK platformy .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)).

---
<!-- add these to the tabs when available -->
Aby zapoznać się z innymi metodami, zobacz [dokumentację usługi Azure Storage](https://docs.microsoft.com/azure/storage/blobs/) służącą do pracy z obiektami BLOB w językach [.NET](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)i [JavaScript (Node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs).

## <a name="next-steps"></a>Następne kroki

> [Przegląd Media Services](media-services-overview.md)
