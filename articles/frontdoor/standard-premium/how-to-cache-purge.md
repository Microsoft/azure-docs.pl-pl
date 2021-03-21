---
title: Przeczyszczanie pamięci podręcznej na platformie Azure — warstwa standardowa/Premium (wersja zapoznawcza)
description: Ten artykuł zawiera informacje ułatwiające zrozumienie sposobu przeczyszczania pamięci podręcznej w standardowym/Premium drzwi platformy Azure.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: aacbf2ceab8580727b1885bf6533cd74a7c4e60a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099813"
---
# <a name="cache-purging-in-azure-front-door-standardpremium-preview"></a>Przeczyszczanie pamięci podręcznej na platformie Azure — warstwa standardowa/Premium (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Zasoby pamięci podręcznej w warstwie Standardowa/Premium platformy Azure do momentu wygaśnięcia czasu wygaśnięcia elementu zawartości. Za każdym razem, gdy klient zażąda zasobu z wygasłym czasem wygaśnięcia, środowisko frontonu platformy Azure pobiera nową zaktualizowaną kopię zasobu do realizacji żądania, a następnie przechowuje odświeżoną pamięć podręczną.

Najlepszym rozwiązaniem jest upewnienie się, że użytkownicy zawsze uzyskują najnowszą kopię zasobów. W tym celu należy zapoznać się z elementami zawartości dla każdej aktualizacji i opublikować je jako nowe adresy URL. Platforma Azure Front-Standard/Premium natychmiast pobierze nowe zasoby dla kolejnych żądań klientów. Czasami możesz chcieć przeczyścić buforowaną zawartość ze wszystkich węzłów brzegowych i wymusić, aby wszystkie nowe zasoby były pobierane. Przyczyną przeczyszczenia buforowanej zawartości jest fakt, że wprowadzono nowe aktualizacje do aplikacji lub chcesz zaktualizować zasoby zawierające nieprawidłowe informacje.

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Przejrzyj [buforowanie drzwi platformy Azure](concept-caching.md) , aby zrozumieć, jak działa buforowanie.

## <a name="configure-cache-purge"></a>Konfigurowanie przeczyszczania pamięci podręcznej

1. Przejdź do strony Przegląd profilu dla drzwi platformy Azure z zasobami, które chcesz przeczyścić, a następnie wybierz pozycję **Przeczyść pamięć podręczną**.

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-1.png" alt-text="Zrzut ekranu przeczyszczania pamięci podręcznej na stronie Przegląd.":::

1. Wybierz punkt końcowy i domenę, które chcesz przeczyścić z węzłów krawędzi. *(Możesz wybrać więcej niż jedną domenę)*

   :::image type="content" source="../media/how-to-cache-purge/front-door-cache-purge-2.png" alt-text="Zrzut ekranu strony przeczyszczania pamięci podręcznej.":::

1. Aby wyczyścić wszystkie zasoby, wybierz pozycję **Przeczyść wszystkie zasoby dla wybranych domen**. W przeciwnym razie w obszarze **ścieżki** wprowadź ścieżkę do każdego zasobu, który ma zostać przeczyszczony.

Te formaty są obsługiwane na listach ścieżek do przeczyszczenia:

* **Przeczyszczanie pojedynczej ścieżki**: Przeczyść pojedyncze zasoby, określając pełną ścieżkę zasobu (bez protokołu i domeny), z rozszerzeniem pliku, na przykład/Pictures/strasbourg.png.
* **Przeczyszczanie domeny głównej**: Przeczyść element główny punktu końcowego z "/*" w ścieżce.

Przeczyszczanie pamięci podręcznej w standardowym/Preiumm drzwi platformy Azure nie uwzględnia wielkości liter. Ponadto są to ciąg zapytania niezależny od, co oznacza, że przeczyszczanie adresu URL spowoduje przeczyszczenie wszystkich odmian ciągu zapytania. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak utworzyć warstwę przednią/standardową](create-front-door-portal.md).
