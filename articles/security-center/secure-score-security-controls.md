---
title: Wskaźnik bezpieczeństwa w usłudze Azure Security Center
description: Opis bezpiecznego wyniku Azure Security Center i jego kontroli zabezpieczeń
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: b19a7c156abf32e2a0f6d70717145a6ed5ab42ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099679"
---
# <a name="secure-score-in-azure-security-center"></a>Wskaźnik bezpieczeństwa w usłudze Azure Security Center

## <a name="introduction-to-secure-score"></a>Wprowadzenie do zabezpieczenia oceny

Azure Security Center ma dwa główne cele: 

- Aby ułatwić zapoznanie się z bieżącą sytuacją bezpieczeństwa
- Aby ułatwić wydajne i efektywne ulepszanie zabezpieczeń

Centralna funkcja w Security Center, która umożliwia osiąganie tych celów, jest **bezpieczną oceną**.

Security Center stale ocenia zasoby, subskrypcje i organizację pod kątem problemów z zabezpieczeniami. Następnie agreguje wszystkie wyniki w postaci pojedynczego wyniku, dzięki czemu można szybko powiedzieć, że aktualna sytuacja zabezpieczeń: wyższy wynik, tym niższy poziom ryzyka.

Na Azure Portal stronach jest pokazywany wynik z wartością procentową, ale podstawowe wartości są również jasno podane:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Ogólny bezpieczny wynik, jak pokazano w portalu":::

Aby zwiększyć bezpieczeństwo, zapoznaj się z tematem zalecenia dotyczące Security Centerych akcji niezbędnych do podniesienia wyniku. Każde zalecenie zawiera instrukcje, które ułatwiają skorygowanie konkretnego problemu.

Zalecenia są pogrupowane w **zabezpieczeniach**. Każda kontrolka jest logiczną grupą powiązanych zaleceń dotyczących zabezpieczeń i odzwierciedla zagrożone powierzchnie ataków. Wynik jest ulepszany tylko wtedy, gdy koryguje *wszystkie* zalecenia dotyczące pojedynczego zasobu w formancie. Aby dowiedzieć się, jak dobrze organizacja zabezpiecza poszczególne osoby atakujące, zapoznaj się z wynikami każdej kontroli zabezpieczeń.

Aby uzyskać więcej informacji, zobacz [jak poniżej jest obliczany bezpieczny wynik](secure-score-security-controls.md#how-your-secure-score-is-calculated) . 

## <a name="how-your-secure-score-is-calculated"></a>Jak jest obliczany bezpieczny wynik 

Udział każdej kontroli zabezpieczeń w odniesieniu do ogólnej bezpiecznego wyniku jest widoczny na stronie zalecenia.

[![Ulepszony bezpieczny wynik wprowadza kontrolę zabezpieczeń](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Aby uzyskać wszystkie możliwe punkty kontroli zabezpieczeń, wszystkie zasoby muszą być zgodne ze wszystkimi zaleceniami dotyczącymi zabezpieczeń w ramach kontroli zabezpieczeń. Na przykład Security Center ma wiele zaleceń dotyczących zabezpieczania portów zarządzania. Należy skorygować wszystkie te elementy, aby zwiększyć poziom bezpieczeństwa.

Na przykład kontrola zabezpieczeń o nazwie "Zastosuj aktualizacje systemu" ma maksymalny wynik sześciu punktów, który można zobaczyć w etykietce narzędzia dla potencjalnej podwyżki wartości formantu:

[![Kontrola zabezpieczeń "Zastosuj aktualizacje systemu"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Maksymalny wynik tej kontrolki, zastosowanie aktualizacji systemu, to zawsze 6. W tym przykładzie wykorzystano 50 zasobów. Dzięki temu Podzielmy maksymalny wynik o 50, a wynikiem jest to, że każdy zasób składa się z 0,12 punktów. 

* **Potencjalny wzrost** (0,12 x 8 niezdrowych zasobów = 0,96) — pozostałe punkty dostępne dla Ciebie w obrębie formantu. W przypadku skorygowania wszystkich zaleceń w tym formancie wynik zostanie zwiększony o 2% (w tym przypadku 0,96 punktów zaokrąglonych do 1 punktu). 
* **Bieżący wynik** (0,12 x 42 w dobrej kondycji = 5,04) — bieżący wynik dla tego formantu. Każda kontrolka przyczynia się do osiągnięcia łącznego wyniku. W tym przykładzie Kontrola ma wpływ na 5,04 punktów na bieżącą łączną sumę.
* **Maksymalny wynik** — Maksymalna liczba punktów, które można uzyskać, wykonując wszystkie zalecenia w formancie. Maksymalna ocena dla kontrolki wskazuje względne znaczenie tego formantu. Użyj wartości maksymalnego wyniku, aby klasyfikacja te problemy, aby działały w pierwszej kolejności. 


### <a name="calculations---understanding-your-score"></a>Obliczenia — zrozumienie oceny

|Metric|Formuła i przykład|
|-|-|
|**Bieżący wynik kontroli zabezpieczeń**|<br>![Równanie obliczające wynik kontroli zabezpieczeń](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Każda indywidualna kontrola zabezpieczeń przyczynia się do osiągnięcia oceny zabezpieczeń. Każdy zasób, na który ma wpływ zalecenie w ramach kontroli, przyczynia się do bieżącego wyniku kontrolki. Bieżący wynik dla każdej kontrolki jest miarą stanu zasobów *w* kontrolce.<br>![Etykietki narzędzi pokazujące wartości używane podczas obliczania bieżącego wyniku kontroli zabezpieczeń](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>W tym przykładzie maksymalny wynik 6 zostałby podzielony przez 78, ponieważ to jest suma zasobów w dobrej kondycji i w nieprawidłowych Stanach.<br>6/78 = 0,0769<br>Mnożenie tego przez liczbę zasobów w dobrej kondycji (4) skutkuje bieżącym wynikiem:<br>0,0769 * 4 = **0,31**<br><br>|
|**Wskaźnik bezpieczeństwa**<br>Subskrypcja pojedyncza|<br>![Równanie obliczania bezpiecznego wyniku subskrypcji](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Jeden bezpieczny wynik subskrypcji z włączonymi wszystkimi kontrolkami](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>W tym przykładzie istnieje jedna subskrypcja z wszystkimi dostępnymi wszystkimi kontrolami zabezpieczeń (potencjalną maksymalną wartością wyniku 60 punktów). Wynik pokazuje 28 punktów z możliwego 60, a pozostałe 32 punkty są odzwierciedlone w postaci "potencjalne zwiększenie wyniku" w zakresie kontroli zabezpieczeń.<br>![Lista kontrolek i zwiększenie potencjalnego wyniku](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Wskaźnik bezpieczeństwa**<br>Wiele subskrypcji|<br>![Równanie obliczania bezpiecznego wyniku dla wielu subskrypcji](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Podczas obliczania połączonego wyniku dla wielu subskrypcji Security Center obejmuje *wagę* dla każdej subskrypcji. Wagi względne dla subskrypcji są określane przez Security Center w oparciu o takie czynniki jak liczba zasobów.<br>Bieżący wynik dla każdej subskrypcji jest obliczany w taki sam sposób jak w przypadku pojedynczej subskrypcji, ale waga jest stosowana, jak pokazano w równaniu.<br>Podczas przeglądania wielu subskrypcji, funkcja Secure Score szacuje wszystkie zasoby we wszystkich włączonych zasadach i grupuje ich łączny wpływ na maksymalny wynik kontroli zabezpieczeń.<br>![Zabezpieczony wynik dla wielu subskrypcji z włączonymi wszystkimi kontrolkami](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Połączony wynik **nie** jest średni; jest to raczej oceniane stan stanu wszystkich zasobów we wszystkich subskrypcjach.<br>Tutaj, jeśli przejdziesz do strony rekomendacje i dodasz dostępne punkty, zobaczysz, że jest to różnica między bieżącym wynikiem (24) i maksymalnym dostępnym wynikiem (60).|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Jakie zalecenia są zawarte w zabezpieczonych obliczeniach oceny?

Tylko wbudowane zalecenia mają wpływ na bezpieczny wynik.

Zalecenia oflagowane jako **wersja zapoznawcza** nie są uwzględniane w obliczeniach bezpiecznego wyniku. Powinny być nadal korygowane wszędzie tam, gdzie jest to możliwe, więc po zakończeniu okresu korzystania z wersji zapoznawczej będą one wchodzić w skład Twojego wyniku.

Przykład zalecenia dotyczącego wersji zapoznawczej:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Zalecenie z flagą wersji zapoznawczej":::

## <a name="improve-your-secure-score"></a>Poprawianie oceny bezpieczeństwa

Aby ulepszyć bezpieczny wynik, skoryguj zalecenia dotyczące zabezpieczeń z listy rekomendacji. Wszystkie zalecenia można skorygować ręcznie dla każdego zasobu lub przy użyciu **szybkiej poprawki** . Opcja (jeśli jest dostępna) w celu szybkiego zastosowania skorygowania zalecenia do grupy zasobów. Aby uzyskać więcej informacji, zobacz temat [korygowanie zaleceń](security-center-remediate-recommendations.md).

Innym sposobem ulepszania oceny i upewnienia się, że użytkownicy nie tworzą zasobów, które nie mają negatywnego wpływu na ocenę, to skonfigurowanie opcji Wymuszaj i Odmów na odpowiednich zaleceniach. Dowiedz się więcej w temacie [zapobieganie błędom konfiguracji z zaleceń Wymuszaj/Odmów](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Kontrola zabezpieczeń i ich zalecenia

W poniższej tabeli wymieniono kontrolki zabezpieczeń w Azure Security Center. Dla każdej kontrolki można zobaczyć maksymalną liczbę punktów, które można dodać do swojego bezpiecznego wyniku, jeśli korygujesz *wszystkie* zalecenia wymienione w formancie dla *wszystkich* zasobów. 

Zestaw zaleceń dotyczących zabezpieczeń zapewnianych w Security Center jest dostosowany do dostępnych zasobów w środowisku każdej organizacji. Zalecenia mogą być bardziej dostosowane przez [wyłączenie zasad](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) i [Wykluczanie określonych zasobów z rekomendacji](exempt-resource.md). 
 
Zaleca się, aby każda organizacja uważnie zapisała swoje przypisane Azure Policy inicjatywy. 

> [!TIP]
> Aby uzyskać szczegółowe informacje na temat przeglądania i edytowania inicjatyw, zobacz [Praca z zasadami zabezpieczeń](tutorial-security-policy.md). 

Mimo że Security Center domyślna inicjatywa w zakresie zabezpieczeń jest oparta na najlepszych rozwiązaniach i standardach branżowych, istnieją scenariusze, w których wbudowane zalecenia wymienione poniżej mogą nie być całkowicie zgodne z Twoją organizacją. W związku z tym czasami konieczne jest dostosowanie inicjatywy domyślnej — bez naruszania zabezpieczeń — w celu zapewnienia ich zgodności z zasadami obowiązującymi w organizacji. standardy branżowe, standardy prawne i wzorce, które są zobowiązane do osiągnięcia.<br><br>
<div class="foo">

<style type="text/css"> . TG {Border-zwijania: Zwiń; odstępy między obramowaniem: 0;}. TG TD {border-color: Black; obramowanie: Solid; border-width: 1px; Font-Family: Arial, sans-serif; font-size: 14px; overflow: Hidden; dopełnienie: 10px 5px; słowo-Break: Normal;}. TG; border-width: 1px; Font-Family: Arial, sans-serif; font-size: 18px; Font-grubości: Normal; overflow: Hidden; uzupełnienie: 10px 5px; słowo-Break: Normal;}. TG. TG-cly1 {text-align: Left; pionowa-align: Middle}. TG. WT-lboi {border-color: inherit; text-align: Left; pionowa-align: Middle} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Bezpieczna ocena — często zadawane pytania

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Czy w ramach kontroli zabezpieczeń są adresowane tylko trzy z czterech zaleceń, czy mój bezpieczny wynik zmieni się?
Nie. Nie ulegnie zmianie, dopóki nie zostaną skorygowane wszystkie zalecenia dotyczące pojedynczego zasobu. Aby uzyskać maksymalny wynik kontrolki, należy skorygować wszystkie zalecenia dla wszystkich zasobów.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Jeśli zalecenie nie dotyczy mnie i nie zostało wyłączone w zasadach, czy moja kontrola zabezpieczeń zostanie zrealizowana, a my zabezpieczony?
Tak. Zalecamy wyłączenie zaleceń, gdy nie są one stosowane w danym środowisku. Aby uzyskać instrukcje dotyczące sposobu wyłączania określonego zalecenia, zobacz temat [wyłączanie zasad zabezpieczeń](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Jeśli kontrola zabezpieczeń oferuje mi zero punktów na rzecz mojego bezpiecznego oceny, należy ją zignorować?
W niektórych przypadkach zobaczysz maksymalny wynik kontrolki większy od zera, ale wpływ wynosi zero. Gdy przyrostowy wynik naprawiania zasobów jest nieznaczny, jest on zaokrąglony do zera. Nie należy ignorować tych zaleceń, ponieważ nadal wprowadzają ulepszenia zabezpieczeń. Jedynym wyjątkiem jest "dodatkowa kontrola najlepszych rozwiązań". Korygowaniem te zalecenia nie spowodują zwiększenia wyniku, ale zwiększy swoje ogólne zabezpieczenia.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera opis bezpiecznego wyniku i kontroli zabezpieczeń, które wprowadza. W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami:

- [Poznaj różne elementy zalecenia](security-center-recommendations.md)
- [Dowiedz się, jak skorygować zalecenia](security-center-remediate-recommendations.md)
- [Wyświetlanie narzędzi opartych na usłudze GitHub w celu programistycznego pracy z bezpiecznym wynikiem](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)


> [!div class="nextstepaction"]
> [Dostęp i śledzenie bezpiecznego wyniku](secure-score-access-and-track.md)