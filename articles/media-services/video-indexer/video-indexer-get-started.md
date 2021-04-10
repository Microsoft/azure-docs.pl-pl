---
title: Tworzenie konta w usłudze Video Indexer i przekazywanie pierwszego pliku wideo — Azure
titleSuffix: Azure Media Services
description: Dowiedz się, jak utworzyć konto i przekazać pierwszy plik wideo za pomocą portalu usługi Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: 5b38c731db141052e6700472020cd60b6a4d13a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797799"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Szybki Start: jak utworzyć konto i przekazać swoje pierwsze wideo

W tym przewodniku szybki start pokazano, jak zalogować się do witryny sieci Web Video Indexer i jak przekazać swoje pierwsze wideo.

Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. W przypadku opcji płatnej utworzone zostaje konto usługi Video Indexer [połączone z subskrypcją platformy Azure i kontem usługi Azure Media Services](connect-to-azure.md). Płacisz za minuty, aby uzyskać więcej informacji, zobacz [Cennik usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/). 

## <a name="sign-up-for-video-indexer"></a>Tworzenie konta w usłudze Video Indexer

Aby rozpocząć tworzenie rozwiązań za pomocą usługi Video Indexer, przejdź do witryny internetowej usługi [Video Indexer](https://www.videoindexer.ai/) i utwórz konto.

Po rozpoczęciu korzystania z Video Indexer wszystkie przechowywane dane i przekazana zawartość są szyfrowane przy użyciu klucza zarządzanego przez firmę Microsoft.

> [!NOTE]
> Przejrzyj [Video Indexer planowane zmiany authenticatication witryny sieci Web](release-notes.md#planned-video-indexer-website-authenticatication-changes).

## <a name="upload-a-video-using-the-video-indexer-website"></a>Przekazywanie pliku wideo za pomocą witryny internetowej usługi Video Indexer

### <a name="supported-file-formats-for-video-indexer"></a>Obsługiwane formaty plików dla Video Indexer

Listę formatów plików, których można używać z Video Indexer, zawiera artykuł [dane wejściowe dotyczące formatów kontenerów i plików](../latest/media-encoder-standard-formats.md#input-containerfile-formats) .

### <a name="upload-a-video"></a>Przekazywanie wideo

1. Zaloguj się w witrynie internetowej usługi [Video Indexer](https://www.videoindexer.ai/).
1. Aby przekazać plik wideo, naciśnij przycisk lub link **Upload** (Przekaż).

    > [!NOTE]
    > Nazwa filmu wideo nie może być dłuższa niż 80 znaków.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Przekaż":::
1. Po przekazaniu pliku wideo usługa Video Indexer rozpocznie jego indeksowanie i analizowanie. Zobaczysz postęp. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Postęp przekazywania":::
1. Po zakończeniu analizy Video Indexer otrzymasz wiadomość e-mail z linkiem do wideo i krótkim opisem tego, co zostało znalezione w filmie wideo. Na przykład: ludzie, mówiony i pisany wyraz, tematy i nazwane jednostki.
1. Film wideo można później znaleźć na liście biblioteka i wykonać różne operacje. Na przykład: Wyszukaj, ponownie Indeksuj, Edytuj.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Przekazane przekazywanie":::

## <a name="supported-browsers"></a>Obsługiwane przeglądarki

Aby uzyskać więcej informacji, zobacz [obsługiwane przeglądarki](video-indexer-overview.md#supported-browsers).

## <a name="see-also"></a>Zobacz też

Aby uzyskać więcej informacji [, zobacz Przekazywanie i indeksowanie filmów wideo](upload-index-videos.md) .

Po przekazaniu i poindeksowania wideo możesz zacząć korzystać z [witryny sieci web video Indexer](video-indexer-view-edit.md) lub [Video Indexer portalu dla deweloperów](video-indexer-use-apis.md) , aby zobaczyć szczegółowe informacje o filmie wideo. 

[Rozpocznij korzystanie z interfejsów API](video-indexer-use-apis.md)

## <a name="next-steps"></a>Następne kroki

Szczegółowe wprowadzenie można znaleźć w naszym [laboratorium wprowadzającym](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Na końcu warsztatu będziesz mieć dobrą wiedzę o rodzaju informacji, które mogą zostać wyodrębnione z zawartości wideo i audio, ale bardziej przygotowasz się do identyfikowania możliwości związanych z analizą zawartości, wideo o skoku AI na platformie Azure i demonstracją kilku scenariuszy na Video Indexer.

