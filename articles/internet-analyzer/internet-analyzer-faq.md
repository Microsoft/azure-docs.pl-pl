---
title: Analizator Internetu — często zadawane pytania | Microsoft Docs
description: Często zadawane pytania dotyczące usługi Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74184251"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure Internet Analyzer — często zadawane pytania (wersja zapoznawcza)

Często zadawane pytania dotyczące usługi Azure Internet Analyzer — Jeśli masz dodatkowe pytania, przejdź do [forum opinii](https://aka.ms/internetAnalyzerFeedbackForum) i Opublikuj swoje pytanie. Gdy pytanie jest często zadawane, dodamy je do tego artykułu, aby można je było szybko i łatwo znaleźć.

## <a name="how-do-i-participate-in-the-preview"></a>Jak mogę uczestniczyć w wersji zapoznawczej?

Wersja zapoznawcza jest dostępna dla wybranych klientów. Jeśli chcesz dołączyć do wersji zapoznawczej, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://ms.portal.azure.com).
2. Przejdź do strony **subskrypcje** .
3. Kliknij subskrypcję platformy Azure, której planujesz używać analizatora internetowego.
4. Przejdź do ustawień **dostawcy zasobów** dla subskrypcji.
5. Wyszukaj pozycję **Microsoft. Network** i kliknij przycisk **zarejestruj** (lub **zarejestruj ponownie**).
![żądanie dostępu](./media/ia-faq/request-preview-access.png)

6. [Zażądaj zatwierdzenia](https://aka.ms/internetAnalyzerContact) , podając nam swój adres E-mail i Identyfikator subskrypcji platformy Azure, który został użyty do uzyskania żądania dostępu.
7. Po zatwierdzeniu żądania otrzymasz wiadomość e-mail z potwierdzeniem i będzie można utworzyć/zaktualizować/zmodyfikować zasoby analizatora internetowego z nowo dozwolonej subskrypcji platformy Azure.

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>Czy muszę osadzić klienta w celu uruchomienia testu?

Tak, klient programu Internet Analyzer musi być zainstalowany w aplikacji, aby zbierać metryki specyficzne dla użytkowników. [Dowiedz się, jak zainstalować klienta programu.](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>Czy jest naliczana opłata za uczestnictwo w wersji zapoznawczej?
Nie, usługa Azure Internet Analyzer jest bezpłatna do użycia w wersji zapoznawczej. W trakcie okresu zapoznawczego nie ma umowy dotyczącej poziomu usług.

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Jakie scenariusze jest zaprojektowana dla programu Internet Analyzer?

Analizator Internetu został zaprojektowany w celu zapewnienia szczegółowych informacji o wydajności sieci opartych na populacji użytkownika. Aby pomóc użytkownikom w podejmowaniu najlepszych decyzji dotyczących wydajności, Analizator Internetu porównuje wydajność dwóch punktów końcowych internetowych przy użyciu odrębnej populacji użytkownika. Usługa Internet Analyzer może odpowiedzieć na wiele pytań, a niektóre z nich są następujące:

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

## <a name="which-tests-can-i-run-in-preview"></a>Które testy można uruchomić w wersji zapoznawczej?

Każdy test tworzony w programie Internet Analyzer obejmuje dwa punkty końcowe — punkt końcowy A i punkt końcowy B. Wszystkie następujące kombinacje mogą być uruchamiane jako testy:  
* Dwa wstępnie skonfigurowane punkty końcowe,
* Jeden niestandardowy i jeden wstępnie skonfigurowany punkt końcowy lub
* Dwa [niestandardowe punkty końcowe](internet-analyzer-custom-endpoint.md) (jeden niestandardowy punkt końcowy musi znajdować się na platformie Azure).

Następujące wstępnie skonfigurowane punkty końcowe są dostępne w trakcie okresu zapoznawczego:
* **Regiony platformy Azure**
    * Brazylia Południowa
    * Indie Środkowe
    * Central US
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

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Czym różni się Internet Analyzer od innych usług monitorowania udostępnianych przez platformę Azure?

Analizator Internetu ułatwia zrozumienie wydajności użytkowników końcowych i ułatwia podejmowanie decyzji w celu usprawnienia ich wydajności. Chociaż inne narzędzia do monitorowania platformy Azure zapewniają wgląd w usługi platformy Azure, Analizator Internetu koncentruje się na mierze kompleksowej wydajności Internetu dla użytkowników.

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Jak dane pomiarów są obsługiwane przez analizator Internetu?

Platforma Azure ma [silne procesy zabezpieczeń i spełnia szeroki zakres standardów zgodności](https://azure.microsoft.com/support/trust-center/). Tylko ty i Twój Wyznaczeni zespół mają dostęp do danych. Pracownicy firmy Microsoft mogą mieć ograniczony dostęp do niego tylko w określonych ograniczonych okolicznościach z Twoją wiedzą. Jest on szyfrowany podczas przesyłania i przechowywania.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz [Omówienie analizatora Internetu](internet-analyzer-overview.md).
