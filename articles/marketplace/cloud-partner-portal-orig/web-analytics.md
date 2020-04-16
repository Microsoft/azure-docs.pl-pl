---
title: Analityka internetowa
description: Z tego artykułu dowiesz się, jak uczyć się i korzystać z usługi Web Analytics, aby jak najlepiej rozwijać swoją firmę.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bd6d6375b19689c582c0f129378c76936e624e04
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416280"
---
<a name="web-analytics"></a>Analityka internetowa
=============

Z tego artykułu dowiesz się, jak uczyć się i korzystać z usługi Web Analytics, aby jak najlepiej rozwijać swoją firmę. Obecnie ta karta Statystyki jest dostępna dla wszystkich ofert usługi AppSource.

Teraz, gdy już zbudowałeś i opublikowałeś swoją ofertę, następną częścią\' twojej podróży jest śledzenie i mierzenie jej sukcesu. Dzięki **Web Analytics**dodaliśmy możliwość, aby zobaczyć dokładnie, jak dobrze każda z Twoich ofert radzi sobie na rynku. Aby rozpocząć podróż, przejdź do strony Statystyki po lewej stronie portalu Cloud Partner Portal, aby wyświetlić nową kartę Analytics.

![Strona WebAnalytics](./media/si-getting-started/WebAnalytics1.png)

Zostanie wyświetlony rozbudowany pulpit nawigacyjny dla identyfikatora wydawcy, który został zbudowany za\' pomocą usługi Microsoft Power BI i umożliwia wyświetlanie każdego z danych ofert, który jest codziennie odświeżany.

<a name="microsoft-campaigns"></a>**Kampanie firmy Microsoft**
-----------------------

Aby rozwijać oferty i śledzić rozwój ofert, umożliwiliśmy korzystanie z **kampanii firmy Microsoft** w portalu Cloud Partner Portal. Kampanie to nowo obsługiwana funkcja w portalu marketplace, która umożliwia śledzenie różnych kanałów, które wysyłają klientów na stronę szczegółów aplikacji.

### <a name="how-to-make-a-campaign"></a>**Jak zrobić kampanię**

Najprostszym sposobem opisania kampanii jest dodanie niestandardowego słowa/terminu do adresu URL, który trafia na stronę szczegółów aplikacji w portalu marketplace. Google, Bing, LinkedIn i wiele innych witryn zachęca do tworzenia reklam, link z ich witryny do żądanej witryny.

Ogólnie rzecz biorąc, te wysiłki mają na celu pomóc zwiększyć liczbę nowych klientów w twoim produkcie i konieczne jest zmierzenie sukcesu każdego z twoich kanałów. W tym miejscu pojawiają się kampanie.

Istnieją dwa sposoby generowania własnej kampanii.

1. Dodaj do adresu URL parametr zapytania **mktcmpid,** który opisuje, czym jest kampania i z jakiej strony/zdarzenia pochodzą ci klienci.

Na przykład można użyć:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign```

1. (Zaawansowane): użyj jednego z naszych obsługiwanych, ogólnych identyfikatorów kampanii w adresie URL. Chcemy być przychylne z dodatkowych tagów ref, które należy użyć, więc obsługujemy konwencji, aby automatycznie rozpoznawać te dodatkowe tagi:
    
    1. **kampania\_utm**
    2. **źródło\_utm**
    3. **ref**
    4. **src**

Na przykład można użyć:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign```

Możesz wybrać kombinację wielu tych identyfikatorów kampanii, aby dokładniej zidentyfikować wiele źródeł napędzania ruchu w kampanii, takich jak miejsce, z którego pochodzi klient (poczta e-mail, blog, źródło mediów społecznościowych itp.).

Przykład:

1. Odsyłacz do newslettera:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter```
2. LinkedIn odsyłający:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn```

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Zapewnienie, że kampanie przechodzą przez wszystkie twoje strony**

Może istnieć scenariusz, w którym twoje kampanie mają stronę pośrednią, do której prowadzisz ruch, a następnie przystępuje do wysyłania klientów do rynku. Ważne jest, aby przejść przez początkowe identyfikatory kampanii do końcowego adresu URL wysyłanego do portalu Marketplace.

Oto przykład:

1. Pracownik marketingu kupuje reklamy od Google, aby ```https://contoso.com```zwiększyć ruch na stronie docelowej firmy.\' Ta strona docelowa \"zawiera\" link do ```https://appsource.com```wypróbowania produktu, który przechodzi do .
2. Użytkownik klika reklamę i ląduje na stronie docelowej swojej firmy.\'
    1.  Adres URL odwołania = google.com
    2.  Adres URL strony docelowej =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
3. Użytkownik kliknie \"link\" wypróbuj mój produkt i przejdzie do źródła aplikacji.
    1. Adres URL odwołania =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
    2. Adres URL strony docelowej (**Upewnij się, że ten adres URL ma utm\_kampanii i źródło\_utm dodane do tego adresu URL**) =```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**```

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Jak ocenić sukces kampanii
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Wizyty na stronie według kampanii**

![Analityka sieci Web2](./media/si-getting-started/WebAnalytics2.png)

Jest to podział każdej z codziennych wizyt na stronie według kampanii, z których pochodzi.

### <a name="conversion-rate-by-campaign"></a>**Współczynnik konwersji według kampanii**

![Analityka sieci Web3](./media/si-getting-started/WebAnalytics3.png)

Podobnie jak pokazujemy współczynnik konwersji całej oferty, na tym wykresie możesz zobaczyć podział na to, jak radzą sobie twoje różne kampanie. Ten wykres pomoże Ci określić, skąd pochodzą kampanie o wyższym współczynniku konwersji.

### <a name="distribution-by-campaign"></a>**Dystrybucja według kampanii**

![Analityka sieci Web4](./media/si-getting-started/WebAnalytics4.png)

Podobnie jak patrzymy na domeny twoich klientów, ten wykres pozwala zobaczyć rozkład danych na kampanię, pod którą użytkownicy przychodzą do rynku. \_NoCampaign oznacza, że klient nie miał identyfikatora kampanii w adresie URL, gdy przechodził do rynku.

<a name="next-steps"></a>**Kolejne kroki**
--------------

Teraz, gdy masz możliwość śledzenia sukcesu swoich ofert, chcemy zachęcić Cię do tworzenia własnych kampanii.

Jeśli masz pytania/prośby o funkcje, podziel się nimi za pośrednictwem opinii, znajdującej się w prawym górnym rogu.

![Opinie w portalu cloud partnerów](./media/si-getting-started/WebAnalytics5.png)
