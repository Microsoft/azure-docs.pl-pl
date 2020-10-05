---
title: Analizator Internetu platformy Azure | Microsoft Docs
description: Dowiedz się więcej o usłudze Azure Internet Analyzer
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: overview
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 2512092c69390ce604c26ab6c5dd000afafc7c05
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91360892"
---
# <a name="what-is-internet-analyzer-preview"></a>Co to jest Analizator Internetu? (Wersja zapoznawcza)

Internet Analyzer to platforma pomiarowa po stronie klienta do testowania, w jaki sposób zmiany infrastruktury sieci wpływają na wydajność klientów. Niezależnie od tego, czy migrujesz z infrastruktury lokalnej na platformę Azure, czy oceniasz nową usługę platformy Azure, usługa Internet Analyzer pozwala Ci uczyć się na podstawie danych użytkowników i bogatych danych analitycznych firmy Microsoft w celu lepszego zrozumienia i zoptymalizowania architektury sieci za pomocą platformy Azure — przed migracją.

Internet Analyzer używa małego klienta JavaScript osadzonego w aplikacji sieci Web, aby mierzyć opóźnienie od użytkowników końcowych do wybranego zestawu miejsc docelowych sieci, a my nazywamy _punkty końcowe_. Program Internet Analyzer umożliwia skonfigurowanie wielu testów równoległych, co pozwala na ocenę różnorodnych scenariuszy w miarę rozwoju infrastruktury i klienta. Program Internet Analyzer udostępnia niestandardowe i wstępnie skonfigurowane punkty końcowe, co zapewnia wygodę i elastyczność podejmowanie zaufanych decyzji dotyczących wydajności dla użytkowników końcowych. 


> [!IMPORTANT]
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="quick--customizable-tests"></a>Szybkie & dostosowywalne testy

Analizator Internetu rozwiązuje pytania dotyczące wydajności migracji do chmury, wdrażając je w nowych lub dodatkowych regionach platformy Azure, lub testując nowe aplikacje i platformy dostarczania zawartości na platformie Azure, takie jak [Azure Front drzwiczki](https://azure.microsoft.com/services/frontdoor/) i [Microsoft Azure sieci CDN](https://azure.microsoft.com/services/cdn/). 

Każdy test tworzony w programie Internet Analyzer obejmuje dwa punkty końcowe — punkt końcowy a i punkt końcowy B. wydajność punktu końcowego B jest analizowana względem punktu końcowego A. 

Możesz skonfigurować własny niestandardowy punkt końcowy lub wybrać jedną z wielu wstępnie skonfigurowanych punktów końcowych platformy Azure. Niestandardowe punkty końcowe powinny być używane do obliczania obciążeń lokalnych, wystąpień innych dostawców chmury lub niestandardowych konfiguracji platformy Azure. Testy mogą składać się z dwóch niestandardowych punktów końcowych; Jednak co najmniej jeden niestandardowy punkt końcowy musi być hostowany na platformie Azure. Wstępnie skonfigurowane punkty końcowe platformy Azure są szybkim i łatwym sposobem oceny wydajności popularnych platform sieciowych platformy Azure, takich jak Azure front-drzwi, Azure Traffic Manager i Azure CDN. 

W trakcie okresu zapoznawczego dostępne są następujące wstępnie skonfigurowane punkty końcowe: 

* **Regiony platformy Azure**
    * Brazil South
    * Indie Środkowe
    * Środkowe stany USA
    * Azja Wschodnia
    * East US
    * Japonia Zachodnia
    * Europa Północna
    * Północna Republika Południowej Afryki
    * Southeast Asia 
    * Północne Zjednoczone Emiraty Arabskie
    * Zachodnie Zjednoczone Królestwo  
    * West Europe
    * Zachodnie stany USA 
    * Zachodnie stany USA 2
* **Wiele kombinacji regionów platformy Azure** 
    * Wschodnie stany USA, Brazylia Południowa 
    * Wschodnie stany USA, Azja Wschodnia 
    * Europa Zachodnia, Brazylia Południowa
    * Europa Zachodnia, Azja Południowo-Wschodnia
    * Europa Zachodnia, Zjednoczone Emiraty Arabskie
    * Zachodnie stany USA, Wschodnie stany USA 
    * Zachodnie stany USA, Europa Zachodnia
    * Zachodnie stany USA, Zjednoczone Emiraty Arabskie
    * Europa Zachodnia, Zjednoczone Emiraty Arabskie, Azja Południowo-Wschodnia
    * Zachodnie stany USA, Europa Zachodnia, Azja Wschodnia
    * Zachodnie stany USA, Europa Północna, Azja Południowo-Wschodnia, Zjednoczone Emiraty Arabskie, Północna Republika Południowej Afryki 
* **Platforma Azure + platformy Azure z góry** — wdrożone na jednej lub wielu kombinacjach regionów platformy Azure wymienionych powyżej
* **Platforma Azure i Azure CDN od firmy Microsoft** — wdrożone na jednej kombinacji regionu platformy Azure wymienionej powyżej
* **Azure + azure Traffic Manager** — wdrożone na dowolnej kombinacji regionu platformy Azure wymienionej powyżej

## <a name="suggested-test-scenarios"></a>Sugerowane scenariusze testowe 

Aby ułatwić klientom podejmowanie najlepszych decyzji dotyczących wydajności, Analizator Internetu umożliwia ocenę dwóch punktów końcowych dla określonej populacji użytkowników końcowych. 

Usługa Internet Analyzer może odpowiedzieć na wiele pytań, a niektóre z nich są następujące: 
* Jaki jest wpływ na wydajność migracji do chmury? 
    * *Sugerowany test: niestandardowy (Bieżąca infrastruktura lokalna) a platforma Azure (wszystkie wstępnie skonfigurowane punkty końcowe)*
* Jaka jest wartość umieszczania moich danych na krawędzi zamiast w centrum danych? 
    *  *Sugerowany test: Azure a platformy Azure z przodu, platforma Azure a Azure CDN firmy Microsoft*
* Jakie są korzyści z korzystania z platformy Azure w przód?
    *  *Sugerowany test: niestandardowy/Azure/CDN a drzwi frontonu platformy Azure*
* Jakie korzyści przynosi Azure CDN od firmy Microsoft? 
    *  *Sugerowany test: Custom/Azure/AFD vs. Azure CDN od firmy Microsoft*
* Jak Azure CDN ze stosu Microsoft? 
    *  *Sugerowany test: niestandardowy (inny punkt końcowy usługi CDN) a Azure CDN firmy Microsoft*
* Co to jest Najlepsza chmura dla ludności użytkowników końcowych w każdym regionie? 
    *  *Sugerowany test: niestandardowy (inna usługa w chmurze) a platforma Azure (wszystkie wstępnie skonfigurowane punkty końcowe)*

## <a name="how-it-works"></a>Jak to działa

Aby użyć narzędzia Internet Analyzer, skonfiguruj zasób analizatora internetowego w Microsoft Azure Portal i zainstaluj w aplikacji mały klient JavaScript. Klient mierzy opóźnienie od użytkowników końcowych do wybranych punktów końcowych przez pobranie obrazu z jednym pikselem za pośrednictwem protokołu HTTPS. Po zebraniu pomiarów opóźnienia klient wysyła dane pomiarowe do analizatora Internetu.

Gdy użytkownik odwiedza aplikację sieci Web, klient JavaScript wybiera dwa punkty końcowe do mierzenia we wszystkich skonfigurowanych testach. Dla każdego punktu końcowego klient wykonuje pomiary _zimnej_ i _ciepłej_ . _Zimna_ miara wiąże się z dodatkowym opóźnieniem obok czystego opóźnienia sieci między użytkownikiem a punktem końcowym, takim jak rozpoznawanie nazw DNS, uzgadnianie połączenia TCP i negocjowanie protokołu SSL/TLS. Pomiar _ciepły_ następuje tuż po zakończeniu _zimnej_ pomiarów i korzystaniu z funkcji zarządzania połączeniami za pomocą nowoczesnej przeglądarki w celu uzyskania dokładnej miary opóźnienia. W przypadku obsługi przez przeglądarkę użytkownika interfejs API czasu zasobów W3C jest używany do dokładnego chronometrażu pomiaru. Obecnie do analizy są używane tylko miary ciepłego opóźnienia.

![Diagram przedstawia użytkownika końcowego łączącego się z serwerem aplikacji z osadzonym klientem i dwoma punktami końcowymi w Internecie z kilku opcji. Użytkownik przekazuje pomiary do analizatora internetowego.](./media/ia-overview/architecture.png)


## <a name="scorecards"></a>Karty wyników 

Po uruchomieniu testu dane telemetryczne są widoczne w Twoim zasobie analizatora Internetu na karcie Karta wyników. Te dane są zawsze agregowane. Użyj następujących filtrów, aby zmienić widok danych, które widzisz: 

* **Test:** Wybierz test, dla którego chcesz wyświetlić wyniki. Dane testowe pojawiają się, gdy jest wystarczająco dużo danych, aby zakończyć analizę — w większości przypadków, w ciągu 24 godzin. 
* **Okres & daty zakończenia:** Program Internet Analyzer codziennie generuje trzy karty wyników — każda karta wyników odzwierciedla inny okres agregacji — 24 godziny poprzedzające (dzień), siedem dni przed (tydzień) i 30 dni przed (miesiąc). Użyj filtru "Data końcowa", aby wybrać okres, który ma zostać wyświetlony. 
* **Kraj:** Ten filtr służy do wyświetlania danych specyficznych dla użytkowników końcowych znajdujących się w danym kraju. Filtr globalny pokazuje dane we wszystkich lokalizacje geograficzne.  

Więcej informacji na temat kart wyników można znaleźć na stronie [interpretacji karty wyników](internet-analyzer-scorecard.md) . 


## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć pierwszy zasób analizatora internetowego](internet-analyzer-create-test-portal.md).
* Przeczytaj [często zadawane pytania dotyczące analizatora Internetu](internet-analyzer-faq.md). 
