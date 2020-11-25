---
title: Przeczyszczanie punktu końcowego Azure CDN | Microsoft Docs
description: Dowiedz się, jak przeczyścić całą buforowaną zawartość z punktu końcowego usługi Azure Content Delivery Network. Węzły brzegowe buforują zasoby, dopóki ich czas wygaśnięcia upłynie.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/17/2019
ms.author: allensu
ms.openlocfilehash: e90086be18e344e3061b9e683780f4427b3c15a1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018634"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Przeczyszczanie punktu końcowego Azure CDN
## <a name="overview"></a>Omówienie
Azure CDN węzły brzegowe będą buforować zasoby do momentu wygaśnięcia czasu wygaśnięcia elementu zawartości.  Po upływie czasu wygaśnięcia zasobu, gdy klient zażąda zasobu z węzła brzegowego, węzeł brzegowy pobierze nową zaktualizowaną kopię elementu zawartości, aby obsłużyć żądanie klienta i zapisać Odświeżanie pamięci podręcznej.

Najlepszym rozwiązaniem, aby upewnić się, że użytkownicy zawsze uzyskują najnowszą kopię zasobów, są w wersji zasobów dla każdej aktualizacji i publikują je jako nowe adresy URL.  Usługa CDN natychmiast pobierze nowe zasoby dla kolejnych żądań klientów.  Czasami możesz chcieć przeczyścić zawartość pamięci podręcznej ze wszystkich węzłów brzegowych i wymusić, aby wszystkie nowe zasoby były pobierane.  Może to być spowodowane aktualizacjami aplikacji sieci Web lub szybkiej aktualizacji zasobów zawierających nieprawidłowe informacje.

> [!TIP]
> Należy zauważyć, że przeczyszczanie czyści zawartość z pamięci podręcznej na serwerach brzegowych usługi CDN.  Wszystkie podrzędne pamięci podręczne, takie jak serwery proxy i pamięci podręczne przeglądarki, mogą nadal przechowywać w pamięci podręcznej kopię pliku.  Ważne jest, aby pamiętać, że podczas ustawiania czasu wygaśnięcia pliku.  Możesz wymusić, aby klient podrzędny zażądał najnowszej wersji pliku, nadając mu unikatową nazwę przy każdej aktualizacji lub korzystając z [buforowania ciągu zapytania](cdn-query-string.md).  
> 
> 

Ten samouczek przeprowadzi Cię przez przeczyszczanie zasobów ze wszystkich węzłów brzegowych punktu końcowego.

## <a name="walkthrough"></a>Przewodnik
1. W [witrynie Azure Portal](https://portal.azure.com)przejdź do profilu CDN zawierającego punkt końcowy, który chcesz przeczyścić.
2. W bloku profil usługi CDN kliknij przycisk Przeczyść.
   
    ![Blok profilu CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Zostanie otwarty blok przeczyszczania.
   
    ![Blok przeczyszczania usługi CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. W bloku przeczyszczania wybierz adres usługi, który chcesz przeczyścić z listy rozwijanej adresów URL.
   
    ![Wyczyść formularz](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > Możesz również przejść do bloku przeczyszczania, klikając przycisk **Przeczyść** w bloku punkt końcowy usługi CDN.  W takim przypadku pole **URL** zostanie wstępnie wypełnione adresem usługi danego punktu końcowego.
   > 
   > 
4. Wybierz zasoby, które chcesz przeczyścić z węzłów krawędzi.  Jeśli chcesz wyczyścić wszystkie zasoby, kliknij pole wyboru **Przeczyść wszystkie** .  W przeciwnym razie wpisz ścieżkę do każdego zasobu, który ma zostać przeczyszczony, w polu tekstowym **ścieżka** . W ścieżce obsługiwane są poniższe formaty.
    1. **Przeczyszczanie pojedynczego adresu URL**: Przeczyść pojedynczy element zawartości, określając pełny adres URL, z rozszerzeniem pliku lub bez niego, np. `/pictures/strasbourg.png` ; `/pictures/strasbourg`
    2. **Przeczyszczanie symboli wieloznacznych**: gwiazdka ( \* ) może być używana jako symbol wieloznaczny. Przeczyść wszystkie foldery, podfoldery i pliki w punkcie końcowym z `/*` ścieżką lub Przeczyść wszystkie podfoldery i pliki w określonym folderze, określając folder, a następnie, na przykład `/*` , `/pictures/*` .  Należy zauważyć, że przeczyszczanie symboli wieloznacznych nie jest obsługiwane przez Azure CDN z Akamai obecnie. 
    3. **Przeczyszczanie domeny głównej**: Przeczyść element główny punktu końcowego z "/" w ścieżce.
   
   > [!TIP]
   > Należy określić ścieżki dla przeczyszczania i musi być względnym adresem URL, który pasuje do poniższego [wyrażenia regularnego](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Przeczyszczanie wszystkich** i **symboli wieloznacznych** nie jest obsługiwane przez **Azure CDN obecnie z Akamai** .
   > > Przeczyszczanie pojedynczego adresu URL `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Ciąg zapytania `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Przeczyszczanie symboli wieloznacznych `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";` . 
   > 
   > Po wprowadzeniu tekstu w celu umożliwienia utworzenia listy wielu zasobów zostaną wyświetlone pola **TextPath** .  Aby usunąć zasoby z listy, kliknij przycisk wielokropka (...).
   > 
5. Kliknij przycisk **Przeczyść** .
   
    ![Przycisk przeczyszczania](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Żądania przeczyszczania trwają około 10 minut, aby przetwarzać w **Azure CDN od firmy Microsoft**, około 2 minut z **Azure CDN z Verizon** (standardowa i Premium) oraz około 10 sekund z **Azure CDN z Akamai**.  Azure CDN ma limit 100 współbieżnych żądań przeczyszczania w danym momencie na poziomie profilu. 
> 
> 

## <a name="see-also"></a>Zobacz też
* [Wstępne ładowanie zasobów w punkcie końcowym usługi Azure CDN](cdn-preload-endpoint.md)
* [Azure CDN dokumentacja interfejsu API REST — przeczyszczanie lub wstępne ładowanie punktu końcowego](/rest/api/cdn/endpoints)

