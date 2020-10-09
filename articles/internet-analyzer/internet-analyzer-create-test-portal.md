---
title: Tworzenie testu analizatora internetowego przy użyciu portalu | Microsoft Docs
description: W tym artykule dowiesz się, jak utworzyć pierwszy test analizatora internetowego.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "73501771"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Tworzenie testu analizatora internetowego przy użyciu portalu (wersja zapoznawcza)

Istnieją dwa sposoby tworzenia zasobów analizatora internetowego — przy użyciu Azure Portal lub [interfejsu wiersza polecenia](internet-analyzer-cli.md). Ta sekcja pomaga utworzyć nowy zasób usługi Azure Internet Analyzer przy użyciu naszego środowiska portalu.

> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

Publiczna wersja zapoznawcza jest dostępna do użycia globalnie; jednak magazyn danych jest ograniczony do *zachodnich Stanów Zjednoczonych 2* w trakcie okresu zapoznawczego.

## <a name="basics"></a>Podstawy

1. Uzyskaj dostęp do usługi Internet Analyzer w wersji zapoznawczej, wykonując **Jak mogę uczestnictwo w wersji zapoznawczej** [usługi Azure Internet Analyzer — często zadawane pytania](internet-analyzer-faq.md).
2. Na stronie głównej w [Azure Portal](https://preview.portal.azure.com)kliknij pozycję **+ Utwórz zasób**. Analizator Internetu jest obecnie dostępny tylko w wersji zapoznawczej Azure Portal.
3. Na **nowej** stronie Wyszukaj ciąg "Analizator Internetu" w polu Wyszukaj w *witrynie Marketplace* .
4. Kliknij pozycję **Internet Analyzer (wersja zapoznawcza)**. Upewnij się, że Wydawca jest *firmą Microsoft* , a kategoria to *Sieć*.
5. Na stronie **Analizator Internetu (wersja zapoznawcza)** kliknij pozycję **Utwórz** , aby otworzyć stronę **Tworzenie analizatora internetowego** .
6. Określ następujące ustawienia konfiguracji zasobu usługi Internet Analyzer:

    * **Subskrypcja:** Subskrypcja platformy Azure do hostowania nowego zasobu analizatora internetowego. ***Użyj tego samego identyfikatora subskrypcji, który jest używany do żądania dostępu do wersji zapoznawczej.***
    * **Grupa zasobów:** Grupa zasobów platformy Azure, w której zostanie utworzony nowy zasób analizatora internetowego. Jeśli nie masz istniejącej grupy zasobów, możesz utworzyć nową.
    * **Nazwa:** Nazwa nowego profilu zasobów analizatora Internetu.
    * **Region:** Region publiczny platformy Azure, w którym zostanie utworzony zasób. W trakcie okresu zapoznawczego dostępna jest tylko *zachodnie stany USA 2* .

7. Po zakończeniu określania ustawień profilu kliknij pozycję **Przegląd + Utwórz**.

## <a name="configuration"></a>Konfiguracja

Wykonanie podstawowych kroków jest wymaganiem wstępnym do konfigurowania testu i osadzania klienta JavaScript. Po utworzeniu profilu przejdź do pozycji **ustawienia > konfiguracja** , aby skonfigurować pierwszy test.

1. Nadaj testowi nazwę w polu **nazwa testu** .
2. Dodaj opis testu w polu **Opis** .
3. Kliknij pozycję **Konfiguruj punkt końcowy** — po prawej stronie zostanie wyświetlona karta. Wybierz typ punktu końcowego, który chcesz skonfigurować — pojedynczy region świadczenia usługi Azure, wiele regionów platformy Azure lub niestandardowy punkt końcowy.

    >
    ***Wstępnie skonfigurowane punkty końcowe: jedna i wiele kombinacji regionów platformy Azure***
    * Wybierz region lub zestaw regionów ze [wstępnie skonfigurowanej listy punktów końcowych platformy Azure](internet-analyzer-faq.md).
    * Następnie wybierz typ aplikacji lub architektury dostarczania zawartości, które chcesz oszacować.
        * Pojedynczy region platformy Azure: przyspieszanie witryn ([drzwi platformy Azure](https://azure.microsoft.com/services/frontdoor/)), statyczne buforowanie zawartości ([Azure CDN dla firmy Microsoft](https://azure.microsoft.com/services/cdn/)) lub brak.
        * Wiele regionów platformy Azure: przyspieszenie witryny ([Azure front-drzwi](https://azure.microsoft.com/services/frontdoor/)), kierowanie DNS ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Niestandardowe punkty końcowe***
    * Postępuj zgodnie z instrukcjami na stronie [Tworzenie niestandardowego punktu końcowego](internet-analyzer-custom-endpoint.md) .
    * Wklej lokalizację adresu URL protokołu HTTPS obrazu z pikselami w portalu.
    >

4. Kliknij przycisk **Dodaj** , aby dodać punkt końcowy do testu.
5. Powtórz kroki 1-4, aby skonfigurować drugi punkt końcowy. Punkt końcowy B jest zawsze mierzony względem punktu końcowego A — podczas konfigurowania punktów końcowych należy wziąć pod uwagę, który punkt końcowy powinien być formantem testowym.
6. Kliknij przycisk **Zapisz** , aby zapisać test. Po zapisaniu testu nie można już edytować punktów końcowych określonego testu.
7. Wybierz testy, które chcesz uruchomić, a następnie kliknij przycisk **Rozpocznij Test**. Spowoduje to zmianę ***stanu*** testów do ***uruchomienia***. Testy można uruchomić w dowolnym momencie, ale klient JavaScript musi być osadzony, aby test mógł rozpocząć zbieranie pomiarów.
8. Dodaj więcej testów w dowolnym momencie. Należy pamiętać, że unikatowy klient JavaScript nie zostanie wygenerowany do momentu utworzenia jednego testu.

## <a name="embed-client"></a>Osadź klienta

Aby rozpocząć dowolny test, klient JavaScript musi być osadzony w aplikacji sieci Web. Po skonfigurowaniu co najmniej jednego testu kliknij pozycję **Przegląd + Utwórz**, przejdź do pozycji **Ustawienia > konfiguracja**i skopiuj klienta JavaScript. Szczegółowe instrukcje można znaleźć na stronie [klienta Osadź Internet Analyzer Client](internet-analyzer-embed-client.md) .  

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [często zadawane pytania dotyczące narzędzia Internet Analyzer](internet-analyzer-faq.md)
* Dowiedz się więcej o osadzaniu [klienta analizatora Internetu](internet-analyzer-embed-client.md) i tworzeniu [niestandardowego punktu końcowego](internet-analyzer-custom-endpoint.md).
