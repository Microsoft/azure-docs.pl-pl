---
title: Tworzenie niestandardowego punktu końcowego | Microsoft Docs
description: W tym artykule dowiesz się, jak skonfigurować niestandardowy punkt końcowy do mierzenia przy użyciu zasobu analizatora Internetu.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6ca71bce726f16eeacc96e10eb654bb3e21c5924
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84744089"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Mierzenie niestandardowych punktów końcowych do oceny w testach analizatora internetowego 

W tym artykule pokazano, jak skonfigurować niestandardowy punkt końcowy do mierzenia w ramach testów analizatora internetowego. Niestandardowe punkty końcowe pomagają w ocenie obciążeń lokalnych, obciążeń działających w innych dostawcach chmury i niestandardowych konfiguracjach platformy Azure.  Porównywanie dwóch niestandardowych punktów końcowych w jednym teście jest możliwe, jeśli jeden punkt końcowy jest zasobem platformy Azure. Aby uzyskać więcej informacji na temat programu Internet Analyzer, zobacz [Omówienie](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Upewnij się, że został skonfigurowany zasób analizatora internetowego i wybierz opcję "niestandardowy punkt końcowy". Analizator Internetu zakłada, że niestandardowy punkt końcowy jest dostępny z Internetu. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu analizatora internetowego](internet-analyzer-create-test-portal.md).


## <a name="create-custom-endpoint"></a>Tworzenie niestandardowego punktu końcowego

1. Pobierz przezroczysty [obraz testu](https://fpc.msedge.net/apc/trans.gif)o pojedynczej pikseli. Ten obraz z jednym pikselem jest zasobem, który klient JavaScript pobierze w celu zmierzenia wydajności.
2. W niestandardowej aplikacji sieci Web Wdróż obraz testowy w dostępnej publicznie ścieżce. Ścieżka powinna korzystać z protokołu HTTPS. 
3. Skopiuj pełny adres URL niestandardowego punktu końcowego (np. `https://contoso.com/test/trans.gif` ) do pola niestandardowego punktu końcowego podczas tworzenia testu.

## <a name="next-steps"></a>Następne kroki

Przeczytaj [często zadawane pytania dotyczące narzędzia Internet Analyzer](internet-analyzer-faq.md)

