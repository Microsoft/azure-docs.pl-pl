---
title: Dostosowywanie modelu marek w Video Indexer na platformie Azure
titleSuffix: Azure Media Services
description: Ten artykuł zawiera omówienie modelu marki w Video Indexer i sposobu jego dostosowywania.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/15/2019
ms.author: juliako
ms.openlocfilehash: 81d7dda854c6afcc9397289ff23ba45b02ed9fc4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97586078"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Dostosowywanie modelu marek w Video Indexer

Video Indexer obsługuje wykrywanie marki z mowy i tekstu wizualnego podczas indeksowania i ponownego indeksowania zawartości audio i wideo. Funkcja wykrywania marki identyfikuje wzmianki o produktach, usługach i firmach sugerowanych przez bazę danych marek usługi Bing. Na przykład jeśli firma Microsoft jest wymieniona w zawartości wideo lub audio lub jeśli zostanie wyświetlona w tekście wizualnym w filmie wideo, Video Indexer wykrywa ją jako markę zawartości. Marki różnią się od innych warunków przy użyciu kontekstu.

Wykrywanie marki jest przydatne w różnorodnych scenariuszach biznesowych, takich jak archiwum zawartości i odnajdywanie, reklama kontekstowa, analiza mediów społecznościowych, analiza konkurowania detaliczna i wiele innych. Funkcja wykrywania marki Video Indexer umożliwia indeksowanie marek w postaci mowy i tekstu wizualnego przy użyciu bazy danych marek Bing, a także dostosowanie przez utworzenie niestandardowego modelu marek dla każdego konta Video Indexer. Funkcja model niestandardowych marek pozwala określić, czy Video Indexer będą wykrywać marki z bazy danych marek Bing, wykluczać Niektóre marki (głównie tworząc listę niezatwierdzonych marek), a także dodawać marki, które powinny być częścią modelu, który może nie znajdować się w bazie danych marek usługi Bing (głównie w tworzeniu listy zatwierdzonych marek). Tworzony model marek niestandardowych będzie dostępny tylko na koncie, w którym został utworzony model.

## <a name="out-of-the-box-detection-example"></a>Przykład wykrycia pola

W prezentacji "Microsoft Build 2017 Day 2" marki "Microsoft Windows" pojawia się wiele razy. Czasami w transkrypcji, czasami jako tekst wizualny i nigdy nie jako Verbatim. Video Indexer wykrywa z wysoką dokładnością, że termin jest istotny na podstawie kontekstu, obejmującego 90k marek od pudełka i ciągle aktualizuje. W 02:25 Video Indexer wykrywa markę z mowy, a następnie ponownie w 02:40 od tekstu wizualnego, który jest częścią logo systemu Windows.

![Przegląd marek](./media/content-model-customization/brands-overview.png)

Mówiąc o systemie Windows w kontekście konstrukcji nie wykryje wyrazu "Windows" jako marki i tego samego dla pola, firmy Apple, Fox itp., na podstawie zaawansowanych algorytmów Machine Learning, które wiedzą, jak odróżnić się od kontekstu. Wykrywanie marki działa w przypadku wszystkich naszych obsługiwanych języków.  

## <a name="next-steps"></a>Następne kroki

Aby wprowadzić własne marki, zapoznaj się z następującymi tematami:

[Dostosowywanie modelu marek przy użyciu interfejsów API](customize-brands-model-with-api.md)

[Dostosowywanie modelu marek przy użyciu witryny sieci Web](customize-brands-model-with-website.md)
