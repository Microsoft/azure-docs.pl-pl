---
title: Model danych telemetrii żądania — Application Insights platformy Azure
description: Application Insights model danych dla telemetrii żądania
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 7a352f4ce3528d395599a91b53031c74b0873152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320565"
---
# <a name="request-telemetry-application-insights-data-model"></a>Dane telemetryczne żądania: Application Insights model danych

Element telemetrii żądania (w [Application Insights](./app-insights-overview.md)) reprezentuje sekwencję logiczną wykonywania wyzwalaną przez zewnętrzne żądanie do aplikacji. Każde wykonanie żądania jest identyfikowane przez unikatową `ID` i `url` zawierającą wszystkie parametry wykonania. Żądania można grupować według logicznej `name` i zdefiniować dla `source` tego żądania. Wykonanie kodu może skutkować `success` lub `fail` i mieć pewne `duration` . Zarówno wykonanie sukcesu, jak i niepowodzenie może być pogrupowane w dalszej postaci `resultCode` . Godzina rozpoczęcia dla danych telemetrycznych żądania zdefiniowanej na poziomie koperty.

Dane telemetryczne żądania obsługują standardowy model rozszerzalności przy użyciu niestandardowych `properties` i `measurements` .

## <a name="name"></a>Nazwa

Nazwa żądania reprezentuje ścieżkę kodu wykonywaną w celu przetworzenia żądania. Niska wartość kardynalności umożliwiająca lepsze grupowanie żądań. W przypadku żądań HTTP reprezentuje ona metodę HTTP i szablon ścieżki adresu URL, takie jak `GET /values/{id}` bez wartości rzeczywistej `id` .

Application Insights Web SDK wysyła nazwę żądania "AS IS" w odniesieniu do wielkości liter. Grupowanie w interfejsie użytkownika jest rozróżniana wielkość liter, więc `GET /Home/Index` jest zliczane oddzielnie od `GET /home/INDEX` nawet wtedy, gdy często powodują to ten sam kontroler i wykonywanie akcji. Przyczyną tego jest to, że adresy URL w ogólności uwzględnia [wielkość](https://www.w3.org/TR/WD-html40-970708/htmlweb.html)liter. Możesz chcieć zobaczyć, czy dotyczy `404` to adresów URL wpisanych wielką literą. Więcej informacji na temat kolekcji nazw żądań można znaleźć przez ASP.NET Web SDK w [wpisie w blogu](https://apmtips.com/posts/2015-02-23-request-name-and-url/).

Maksymalna długość: 1024 znaków

## <a name="id"></a>ID (Identyfikator)

Identyfikator wystąpienia wywołania żądania. Służy do korelacji między żądaniem a innymi elementami telemetrii. Identyfikator powinien być globalnie unikatowy. Aby uzyskać więcej informacji, zobacz stronę [korelacji](./correlation.md) .

Maksymalna długość: 128 znaków

## <a name="url"></a>Url

Adres URL żądania ze wszystkimi parametrami ciągu zapytania.

Maksymalna długość: 2048 znaków

## <a name="source"></a>Element źródłowy

Źródło żądania. Przykłady to klucz Instrumentacji obiektu wywołującego lub adres IP obiektu wywołującego. Aby uzyskać więcej informacji, zobacz stronę [korelacji](./correlation.md) .

Maksymalna długość: 1024 znaków

## <a name="duration"></a>Czas trwania

Czas trwania żądania w formacie: `DD.HH:MM:SS.MMMMMM` . Wartość musi być dodatnia i mniejsza niż `1000` Liczba dni. To pole jest wymagane jako dane telemetryczne żądania reprezentuje operację z początkiem i końcem.

## <a name="response-code"></a>Kod odpowiedzi

Wynik wykonania żądania. Kod stanu HTTP dla żądań HTTP. Może być `HRESULT` wartością lub typem wyjątku dla innych typów żądań.

Maksymalna długość: 1024 znaków

## <a name="success"></a>Powodzenie

Wskazanie powodzenia lub nieudanych wywołań. To pole jest wymagane. Gdy nie ustawiono jawnie opcji `false` -żądanie jest uznawane za pomyślne. Ustaw tę wartość na `false` Jeśli operacja została przerwana przez wyjątek lub zwrócony kod wyniku błędu.

W przypadku aplikacji sieci Web Application Insights zdefiniować żądanie jako pomyślne, gdy kod odpowiedzi jest mniejszy `400` lub równy `401` . Istnieją jednak przypadki, gdy to domyślne mapowanie nie jest zgodne z semantyką aplikacji. Kod odpowiedzi `404` może wskazywać na brak rekordów, które mogą być częścią regularnego przepływu. Może także wskazywać na przerwane łącze. W przypadku uszkodzonych linków można nawet zaimplementować bardziej zaawansowaną logikę. Przerwane linki można oznaczyć jako błędy tylko wtedy, gdy te linki znajdują się w tej samej witrynie przez analizowanie odwołującego adresu URL. Lub Oznacz je jako błędy podczas uzyskiwania dostępu z firmowej aplikacji mobilnej. Analogicznie `301` i `302` oznacza niepowodzenie podczas uzyskiwania dostępu z klienta, który nie obsługuje przekierowania.

Częściowo zaakceptowana zawartość `206` może wskazywać na niepowodzenie żądania ogólnego. Na przykład, Application Insights punkt końcowy odbiera partie elementów telemetrii jako pojedyncze żądanie. Zwraca, `206` gdy niektóre elementy w partii nie zostały pomyślnie przetworzone. Zwiększona stawka `206` wskazuje problem, który należy zbadać. Podobna logika ma zastosowanie do `207` wielostanowych, w których sukces może być najgorszeniem oddzielnych kodów odpowiedzi.

Więcej informacji na temat kodu wyniku żądania i kodu stanu można znaleźć w [wpisie w blogu](https://apmtips.com/posts/2016-12-03-request-success-and-response-code/).

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Następne kroki

- [Zapisz dane telemetryczne żądania niestandardowego](./api-custom-events-metrics.md#trackrequest)
- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- Dowiedz się, jak [skonfigurować aplikację ASP.NET Core](./asp-net.md) przy użyciu Application Insights.
- Sprawdź [platformy](./platforms.md) obsługiwane przez Application Insights.

