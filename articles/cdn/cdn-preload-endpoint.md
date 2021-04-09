---
title: Wstępne ładowanie zasobów w punkcie końcowym Azure CDN | Microsoft Docs
description: Dowiedz się, jak wstępnie ładować zawartość pamięci podręcznej w punkcie końcowym usługi Azure Content Delivery Network. Ta funkcja jest dostępna w niektórych wersjach produktu.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2018
ms.author: allensu
ms.openlocfilehash: 186ded90b504420a2f315d054551d97821cf8465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385051"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Wstępne ładowanie zasobów w punkcie końcowym usługi Azure CDN
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Domyślnie elementy zawartości są buforowane tylko wtedy, gdy są żądane. Ponieważ serwery brzegowe nie zawierały jeszcze pamięci podręcznej i muszą przesłać żądanie do serwera pochodzenia, pierwsze żądanie z każdego regionu może trwać dłużej niż kolejne żądania. Aby uniknąć tego opóźnienia pierwszego trafienia, należy wstępnie załadować zasoby. Oprócz zapewnienia lepszej obsługi klienta, wstępne ładowanie zasobów w pamięci podręcznej może obniżyć ruch sieciowy na serwerze pochodzenia.

> [!NOTE]
> Wstępne ładowanie zasobów jest przydatne w przypadku dużych zdarzeń lub zawartości, które staną się jednocześnie dostępne dla wielu użytkowników, takich jak nowa wersja filmu lub aktualizacja oprogramowania.
> 
> 

Ten samouczek przeprowadzi Cię przez wstępne ładowanie zawartości z pamięci podręcznej we wszystkich Azure CDN węzłach brzegowych.

## <a name="to-pre-load-assets"></a>Aby wstępnie załadować zasoby
1. W [Azure Portal](https://portal.azure.com)przejdź do profilu CDN zawierającego punkt końcowy, który ma zostać wstępnie załadowany. Zostanie otwarte okienko profil.
    
2. Kliknij punkt końcowy na liście. Zostanie otwarte okienko punkt końcowy.
3. W okienku punkt końcowy usługi CDN wybierz pozycję **Załaduj**.
   
    ![Okienko punktu końcowego usługi CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Zostanie otwarte okienko **ładowanie** .
   
    ![Okienko ładowania usługi CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. W polu **ścieżka zawartości** wprowadź pełną ścieżkę do każdego zasobu, który ma zostać załadowany (na przykład `/pictures/kitten.png` ).
   
   > [!TIP]
   > Po rozpoczęciu wprowadzania tekstu, pojawią się więcej pól tekstowych **ścieżki zawartości** , aby umożliwić tworzenie listy wielu zasobów. Aby usunąć zasoby z listy, wybierz przycisk wielokropka (...), a następnie wybierz pozycję **Usuń**.
   > 
   > Każda ścieżka zawartości musi być względnym adresem URL, który pasuje do następujących [wyrażeń regularnych](/dotnet/standard/base-types/regular-expression-language-quick-reference):  
   > - Załaduj pojedynczą ścieżkę pliku: `^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Załaduj pojedynczy plik z ciągiem zapytania: `^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Ponieważ każdy element zawartości musi mieć własną ścieżkę, nie ma funkcji wieloznacznych do wstępnego ładowania zasobów.
   > 
   > 
   
    ![Przycisk ładowania](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Po zakończeniu wprowadzania ścieżek zawartości wybierz pozycję **Załaduj**.
   

> [!NOTE]
> Istnieje limit 10 żądań obciążenia na minutę na profil CDN, a w tym samym czasie można przetwarzać ścieżki współbieżne 50. Każda ścieżka ma limit długości ścieżki wynoszący 1024 znaków.
> 
> 

## <a name="see-also"></a>Zobacz też
* [Przeczyszczanie punktu końcowego Azure CDN](cdn-purge-endpoint.md)
* [Azure CDN informacje o interfejsie API REST: Załaduj wstępnie zawartość w punkcie końcowym](/rest/api/cdn/cdn/endpoints/loadcontent)
* [Azure CDN informacje o interfejsie API REST: Przeczyść zawartość z punktu końcowego](/rest/api/cdn/cdn/endpoints/purgecontent)