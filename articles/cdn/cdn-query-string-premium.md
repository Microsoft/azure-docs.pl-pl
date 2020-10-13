---
title: Sterowanie zachowaniem Azure CDN buforowania za pomocą ciągów zapytań — warstwa Premium
description: Azure CDN buforowanie ciągu zapytania kontroluje, jak pliki są buforowane, gdy żądanie sieci Web zawiera ciąg zapytania. W tym artykule opisano buforowanie ciągu zapytania w Azure CDN Premium z produktu Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 99db4a85-4f5f-431f-ac3a-69e05518c997
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: a799309b6e5d00db3b6c206187eec7097c9dc11a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84887273"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---premium-tier"></a>Sterowanie zachowaniem Azure CDN buforowania za pomocą ciągów zapytań — warstwa Premium
> [!div class="op_single_selector"]
> * [Warstwa Standardowa](cdn-query-string.md)
> * [Warstwa Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Omówienie
Za pomocą usługi Azure Content Delivery Network (CDN) można kontrolować sposób, w jaki pliki są buforowane dla żądania sieci Web, które zawiera ciąg zapytania. W żądaniu sieci Web za pomocą ciągu zapytania ciąg zapytania jest częścią żądania, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać co najmniej jedną parę klucz-wartość, w której nazwa pola i jego wartość są oddzielone znakiem równości (=). Każda para klucz-wartość jest oddzielona znakiem handlowego "i" (&). Na przykład http: \/ /www.contoso.com/content.mov?field1=value1&Pole2 = wartość2. Jeśli w ciągu zapytania żądania występuje więcej niż jedna para klucz-wartość, ich kolejność nie ma znaczenia. 

> [!IMPORTANT]
> Produkty sieci CDN w warstwach Standardowa i Premium zapewniają te same funkcje buforowania ciągu zapytania, ale interfejs użytkownika różni się. W tym artykule opisano Interfejs **Azure CDN Premium z Verizon**. W przypadku buforowania ciągu zapytania z produktami Azure CDN Standard, zobacz [kontrola Azure CDN zachowanie buforowania za pomocą ciągów zapytań — warstwa standardowa](cdn-query-string.md).
>


Dostępne są trzy tryby ciągu zapytania:

- **pamięć podręczna**: tryb domyślny. W tym trybie węzeł punktu obecności (POP) usługi CDN przekazuje ciągi zapytania od osoby żądającej do serwera pochodzenia przy pierwszym żądaniu i buforuje element zawartości. Wszystkie kolejne żądania dla elementu zawartości, które są obsługiwane przez serwer POP, ignorują ciągi zapytania do momentu wygaśnięcia pamięci podręcznej.

    >[!IMPORTANT] 
    > Jeśli autoryzacja tokenu jest włączona dla dowolnej ścieżki na tym koncie, tryb pamięci podręcznej jest jedynym trybem, który może być używany. 

- **Brak pamięci podręcznej**: w tym trybie żądania z ciągami zapytań nie są buforowane w węźle pop usługi CDN. Węzeł POP pobiera zasób bezpośrednio z serwera pochodzenia i przekazuje go do obiektu żądającego za pomocą każdego żądania.

- **unikatowa pamięć podręczna**: w tym trybie każde żądanie z unikatowym adresem URL, w tym ciąg zapytania, jest traktowane jako unikatowy element zawartości z własną pamięcią podręczną. Na przykład odpowiedź z serwera pochodzenia dla żądania na przykład. ashx? q = TEST1 jest buforowana w węźle POP i zwracana dla kolejnych pamięci podręcznych z tym samym ciągiem zapytania. Żądanie na przykład. ashx? q = Test2 jest zapisywane w pamięci podręcznej jako osobny zasób z własnym ustawieniem czasu wygaśnięcia.
   
    >[!IMPORTANT] 
    > Nie używaj tego trybu, gdy ciąg zapytania zawiera parametry, które zmienią się przy każdym żądaniu, takie jak identyfikator sesji lub nazwa użytkownika, ponieważ spowoduje to niską wartość współczynnika trafień w pamięci podręcznej.

## <a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Zmiana ustawień buforowania ciągu zapytania dla profilów usługi CDN w warstwie Premium
1. Otwórz profil usługi CDN, a następnie kliknij pozycję **Zarządzaj**.
   
    ![Przycisk zarządzania profilem CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)
   
    Zostanie otwarty portal zarządzania sieci CDN.
2. Umieść kursor na **duże karty http** , a następnie umieść wskaźnik myszy nad menu wysuwanym **Ustawienia pamięci podręcznej** . Kliknij opcję **buforowanie ciągu zapytania**.
   
    Wyświetlane są opcje buforowania ciągu zapytania.
   
    ![Opcje buforowania ciągu zapytania usługi CDN](./media/cdn-query-string-premium/cdn-query-string.png)
3. Wybierz tryb ciągu zapytania, a następnie kliknij przycisk **Aktualizuj**.

> [!IMPORTANT]
> Ponieważ trwa czas na propagację rejestracji w usłudze CDN, zmiany ustawień ciągu pamięci podręcznej mogą nie być natychmiast widoczne. Propagacja jest zwykle zakończona w ciągu 10 minut.
 

