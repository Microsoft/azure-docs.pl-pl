---
title: Sterowanie zachowaniem Azure CDN buforowania za pomocą ciągów zapytań — warstwa standardowa
description: Azure CDN buforowanie ciągu zapytania kontroluje, jak pliki są buforowane, gdy żądanie sieci Web zawiera ciąg zapytania. W tym artykule opisano buforowanie ciągów zapytań w produktach Azure CDN Standard.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 1521d08ef9d431bbe8b3fd3a578297d440ed56b3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018583"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Sterowanie zachowaniem Azure CDN buforowania za pomocą ciągów zapytań — warstwa standardowa
> [!div class="op_single_selector"]
> * [Warstwa Standardowa](cdn-query-string.md)
> * [Warstwa Premium](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Omówienie
Za pomocą usługi Azure Content Delivery Network (CDN) można kontrolować sposób, w jaki pliki są buforowane dla żądania sieci Web, które zawiera ciąg zapytania. W żądaniu sieci Web za pomocą ciągu zapytania ciąg zapytania jest częścią żądania, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać co najmniej jedną parę klucz-wartość, w której nazwa pola i jego wartość są oddzielone znakiem równości (=). Każda para klucz-wartość jest oddzielona znakiem handlowego "i" (&). Na przykład http: \/ /www.contoso.com/content.mov?field1=value1&Pole2 = wartość2. Jeśli w ciągu zapytania żądania występuje więcej niż jedna para klucz-wartość, ich kolejność nie ma znaczenia. 

> [!IMPORTANT]
> Produkty Azure CDN Standard i Premium zapewniają te same funkcje buforowania ciągu zapytania, ale interfejs użytkownika różni się. W tym artykule opisano interfejs dla **Azure CDN standard firmy Microsoft**, **Azure CDN Standard from Akamai** i **Azure CDN Standard from Verizon**. W przypadku buforowania ciągu zapytania z użyciem **Azure CDN Premium z Verizon**, zobacz [Azure CDN sterowania buforowaniem z ciągami zapytań — warstwa Premium](cdn-query-string-premium.md).

Dostępne są trzy tryby ciągu zapytania:

- **Ignoruj ciągi zapytań**: tryb domyślny. W tym trybie węzeł punktu obecności (POP) usługi CDN przekazuje ciągi zapytania od osoby żądającej do serwera pochodzenia przy pierwszym żądaniu i buforuje element zawartości. Wszystkie kolejne żądania dla elementu zawartości, które są obsługiwane z punktu POP, ignorują ciągi zapytania do momentu wygaśnięcia pamięci podręcznej.

- **Obejście buforowania dla ciągów zapytań**: w tym trybie żądania z ciągami zapytań nie są buforowane w węźle pop usługi CDN. Węzeł POP pobiera zasób bezpośrednio z serwera pochodzenia i przekazuje go do obiektu żądającego za pomocą każdego żądania.

- **Buforuj każdy unikatowy adres URL**: w tym trybie każde żądanie z unikatowym adresem URL, łącznie z ciągiem zapytania, jest traktowane jako unikatowy element zawartości z własną pamięcią podręczną. Na przykład odpowiedź z serwera pochodzenia dla żądania na przykład. ashx? q = TEST1 jest buforowana w węźle POP i zwracana dla kolejnych pamięci podręcznych z tym samym ciągiem zapytania. Żądanie na przykład. ashx? q = Test2 jest zapisywane w pamięci podręcznej jako osobny zasób z własnym ustawieniem czasu wygaśnięcia.
   
    >[!IMPORTANT] 
    > Nie używaj tego trybu, gdy ciąg zapytania zawiera parametry, które zmienią się przy każdym żądaniu, takie jak identyfikator sesji lub nazwa użytkownika, ponieważ spowoduje to niską wartość współczynnika trafień w pamięci podręcznej.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Zmiana ustawień buforowania ciągu zapytania dla standardowych profilów usługi CDN
1. Otwórz profil usługi CDN, a następnie wybierz punkt końcowy usługi CDN, którym chcesz zarządzać.
   
   ![Punkty końcowe profilu usługi CDN](./media/cdn-query-string/cdn-endpoints.png)
   
2. W lewym okienku w obszarze Ustawienia kliknij pozycję **reguły buforowania**.
   
    ![Przycisk Reguły buforowania usługi CDN](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Na liście **zachowanie buforowania ciągu zapytania** wybierz tryb ciągu zapytania, a następnie kliknij przycisk **Zapisz**.
   
   ![Opcje buforowania ciągu zapytania usługi CDN](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Ponieważ trwa czas, aby rejestracja była propagowana za pomocą Azure CDN, zmiany ustawień ciągu pamięci podręcznej mogą nie być od razu widoczne:
> - W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
> - W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
> - W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut. 



