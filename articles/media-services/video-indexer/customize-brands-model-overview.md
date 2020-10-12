---
title: Dostosowywanie modelu marek w Video Indexer na platformie Azure
titleSuffix: Azure Media Services
description: Ten artykuł zawiera omówienie modelu marki w Video Indexer i sposobu jego dostosowywania.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: e0df698cb431e5e26c69047a9f464904f0546487
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294241"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Dostosowywanie modelu marek w Video Indexer

Video Indexer obsługuje wykrywanie marki z mowy i tekstu wizualnego podczas indeksowania i ponownego indeksowania zawartości audio i wideo. Funkcja wykrywania marki identyfikuje wzmianki o produktach, usługach i firmach sugerowanych przez bazę danych marek usługi Bing. Na przykład jeśli firma Microsoft jest wymieniona w zawartości wideo lub audio lub jeśli zostanie wyświetlona w tekście wizualnym w filmie wideo, Video Indexer wykrywa ją jako markę zawartości. Marki różnią się od innych warunków przy użyciu kontekstu.

Wykrywanie marki jest przydatne w różnorodnych scenariuszach biznesowych, takich jak archiwum zawartości i odnajdywanie, reklama kontekstowa, analiza mediów społecznościowych, analiza konkurowania detaliczna i wiele innych. Funkcja wykrywania marki Video Indexer umożliwia indeksowanie marek w postaci mowy i tekstu wizualnego przy użyciu bazy danych marek Bing, a także dostosowanie przez utworzenie niestandardowego modelu marek dla każdego konta Video Indexer. Funkcja model niestandardowych marek pozwala określić, czy Video Indexer będą wykrywać marki z bazy danych marek Bing, wykluczać Niektóre marki (głównie tworząc listę niezatwierdzonych marek), a także dodawać marki, które powinny być częścią modelu, który może nie znajdować się w bazie danych marek usługi Bing (głównie w tworzeniu listy zatwierdzonych marek). Tworzony model marek niestandardowych będzie dostępny tylko na koncie, w którym został utworzony model.

## <a name="out-of-the-box-detection-example"></a>Przykład wykrycia pola

W prezentacji [Microsoft Build 2017 Day 2](https://www.videoindexer.ai/media/ed6ede78ad/) Marka "Microsoft Windows" pojawia się wiele razy. Czasami w transkrypcji, czasami jako tekst wizualny i nigdy nie jako Verbatim. Video Indexer wykrywa z wysoką dokładnością, że termin jest istotny na podstawie kontekstu, obejmującego 90k marek od pudełka i ciągle aktualizuje. W 02:25 Video Indexer wykrywa markę z mowy, a następnie ponownie w 02:40 od tekstu wizualnego, który jest częścią logo systemu Windows.

![Przegląd marek](./media/content-model-customization/brands-overview.png)

Mówiąc o systemie Windows w kontekście konstrukcji nie wykryje wyrazu "Windows" jako marki i tego samego dla pola, firmy Apple, Fox itp., na podstawie zaawansowanych algorytmów Machine Learning, które wiedzą, jak odróżnić się od kontekstu. Wykrywanie marki działa w przypadku wszystkich naszych obsługiwanych języków. Kliknij tutaj, aby uzyskać [pełną kompilację Microsoft Build 2017 Day 2 prezentację wideo i indeks](https://www.videoindexer.ai/media/ed6ede78ad/).

Aby wprowadzić własne marki, zapoznaj się z następnymi krokami.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu marek przy użyciu interfejsów API](customize-brands-model-with-api.md)

[Dostosowywanie modelu marek przy użyciu witryny sieci Web](customize-brands-model-with-website.md)
