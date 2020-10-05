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
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: f6c3953947e6f7e84f4cf9b565d2f66648b177f7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85130759"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Szybki Start: jak utworzyć konto i przekazać swoje pierwsze wideo

W tym przewodniku szybki start pokazano, jak zalogować się do witryny sieci Web Video Indexer i jak przekazać swoje pierwsze wideo.

Podczas tworzenia konta w usłudze Video Indexer można wybrać konto bezpłatnej wersji próbnej (w ramach którego otrzymuje się określoną liczbę bezpłatnych minut indeksowania) lub opcję płatną (w przypadku której nie ma ograniczeń przydziału). Usługa Video Indexer w bezpłatnej wersji próbnej udostępnia do 600 minut bezpłatnego indeksowania u użytkowników witryn internetowych oraz do 2400 minut bezpłatnego indeksowania u użytkowników interfejsów API. W przypadku opcji płatnej utworzone zostaje konto usługi Video Indexer [połączone z subskrypcją platformy Azure i kontem usługi Azure Media Services](connect-to-azure.md). Naliczane są opłaty za minuty indeksowania, a także opłaty powiązane z kontem usługi Azure Media Services. 

## <a name="sign-up-for-video-indexer"></a>Tworzenie konta w usłudze Video Indexer

Aby rozpocząć tworzenie rozwiązań za pomocą usługi Video Indexer, przejdź do witryny internetowej usługi [Video Indexer](https://www.videoindexer.ai/) i utwórz konto.

> [!NOTE]
> Po rozpoczęciu korzystania z Video Indexer wszystkie przechowywane dane i przekazana zawartość są szyfrowane przy użyciu klucza zarządzanego przez firmę Microsoft.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Przekazywanie pliku wideo za pomocą witryny internetowej usługi Video Indexer

### <a name="supported-file-formats-for-video-indexer"></a>Obsługiwane formaty plików dla Video Indexer

Listę formatów plików, których można używać z Video Indexer, zawiera artykuł [dane wejściowe dotyczące formatów kontenerów i plików](../latest/media-encoder-standard-formats.md#input-containerfile-formats) .

### <a name="upload-a-video"></a>Przekazywanie wideo

1. Zaloguj się w witrynie internetowej usługi [Video Indexer](https://www.videoindexer.ai/).
2. Aby przekazać plik wideo, naciśnij przycisk lub link **Upload** (Przekaż).

    > [!NOTE]
    > Nazwa filmu wideo nie może być dłuższa niż 80 znaków.

    ![Przekazywanie](./media/video-indexer-get-started/video-indexer-upload.png)

    Po przekazaniu pliku wideo usługa Video Indexer rozpocznie jego indeksowanie i analizowanie.

    ![Przekazano](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Po zakończeniu analizowania przez usługę Video Indexer otrzymasz powiadomienie z linkiem do tego pliku wideo i krótki opis tego, co znaleziono w tym nagraniu. Na przykład: osoby, tematy i wyniki przetwarzania OCR.

## <a name="see-also"></a>Zobacz też

Aby uzyskać więcej informacji [, zobacz Przekazywanie i indeksowanie filmów wideo](upload-index-videos.md) .

Po przekazaniu i poindeksowania wideo możesz zacząć korzystać z witryny sieci Web [Video Indexer](video-indexer-view-edit.md) lub [Video Indexer portalu dla deweloperów](video-indexer-use-apis.md) , aby zobaczyć szczegółowe informacje o filmie wideo. 

[Rozpocznij korzystanie z interfejsów API](video-indexer-use-apis.md)

## <a name="next-steps"></a>Następne kroki

Szczegółowe wprowadzenie można znaleźć w naszym [laboratorium wprowadzającym](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

Na końcu warsztatu będziesz mieć dobrą wiedzę o rodzaju informacji, które mogą zostać wyodrębnione z zawartości wideo i audio, ale bardziej przygotowasz się do identyfikowania możliwości związanych z analizą zawartości, wideo o skoku AI na platformie Azure i demonstracją kilku scenariuszy na Video Indexer.

