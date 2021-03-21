---
title: Diagram architektury referencyjnej | Portal Azure Marketplace
description: Jak utworzyć diagram architektury referencyjnej dla oferty w komercyjnej witrynie Microsoft Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604495"
---
# <a name="reference-architecture-diagram"></a>Diagram architektury referencyjnej

Diagram architektury referencyjnej jest modelem, który reprezentuje infrastrukturę, na której bazuje oferta. W przypadku rozwiązań IP platformy Azure Diagram powinien również zawierać informacje o tym, w jaki sposób oferta używa usług w chmurze firmy Microsoft zgodnie z wymaganiami technicznymi dotyczącymi współsprzedaży. Nie jest ona przeznaczona do oceny jakości architektury. Zaprojektowano, aby pokazać, w jaki sposób rozwiązanie korzysta z usług firmy Microsoft.

Diagram architektury referencyjnej można utworzyć za pomocą wielu narzędzi. Zalecamy korzystanie z programu Microsoft Visio, ponieważ zawiera on wiele wzorników, które przedstawiają modele architektury platformy Azure.

Przydatnym punktem wyjścia do tworzenia diagramów architektury referencyjnej jest wykorzystanie [modeli architektury platformy Azure](/azure/architecture/browse/).

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Typowe składniki diagramu architektury referencyjnej

- Usługi w chmurze, które obsługują i współpracują z ofertą, w tym te, które zużywają zasoby platformy Azure
- Połączenia danych, warstwy danych i usługi danych używane przez ofertę
- Usługi w chmurze służące do kontrolowania zabezpieczeń, uwierzytelniania i użytkowników oferty
- Interfejsy użytkownika i inne usługi, które uwidaczniają ofertę użytkownikom
- Połączenie hybrydowe lub lokalne, integracje lub połączenie obu

Ten zrzut ekranu przedstawia przykład diagramu architektury referencyjnej.

[![Ten obraz to przykładowy diagram architektury.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Ten przykładowy diagram architektury referencyjnej dotyczy chatbot branżowych, które można zintegrować z witrynami intranetowymi w celu ułatwienia prognozowania scenariuszy popytu za pośrednictwem algorytmu uczenia maszynowego. To rozwiązanie używa łańcucha dostaw i danych harmonogramu produkcji z różnych systemów ERP. Bot zaprojektowano w celu uzyskania odpowiedzi na pytania dotyczące tego, kiedy sprzedawca może zatwierdzić na ewentualne daty dostawy dla zamówienia.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie współsprzedaży dla komercyjnej oferty portalu Marketplace](commercial-marketplace-co-sell.md)
