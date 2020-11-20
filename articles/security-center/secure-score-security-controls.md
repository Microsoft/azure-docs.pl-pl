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
ms.date: 10/21/2020
ms.author: memildin
ms.openlocfilehash: b7c4c0565d17e62226a518bc443223df8339faec
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949381"
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


## <a name="access-your-secure-score"></a>Uzyskaj dostęp do swojego bezpiecznego wyniku

Możesz znaleźć ogólny, bezpieczny wynik, a także wynik na subskrypcję za pomocą Azure Portal lub programowo zgodnie z opisem w poniższych sekcjach:

- [Pobierz swój Bezpieczny wynik z portalu](#get-your-secure-score-from-the-portal)
- [Uzyskiwanie bezpiecznego wyniku z interfejsu API REST](#get-your-secure-score-from-the-rest-api)
- [Pobierz bezpieczny wynik z grafu zasobów platformy Azure (ARG)](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>Pobierz swój Bezpieczny wynik z portalu

Security Center wyświetla wyniki w portalu w widocznym miejscu: jest to pierwszy główny kafelek na stronie Przegląd Security Center. Wybranie tego kafelka spowoduje przejście na stronę dedykowanego, bezpiecznego oceny, w której zobaczysz wynik podzielony przez subskrypcję. Wybierz jedną subskrypcję, aby wyświetlić szczegółową listę zaleceń z priorytetami i potencjalny wpływ, który korygowaniem ich na ocenę subskrypcji.

Aby podsumowanie, Twój bezpieczny wynik jest wyświetlany w następujących lokalizacjach na stronach portalu Security Center.

- Na kafelku na Security Center **Przegląd** (główny pulpit nawigacyjny):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Bezpieczna Ocena na pulpicie nawigacyjnym Security Center":::

- Na stronie dedykowany **wynik bezpiecznego** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Bezpieczna Ocena na stronie bezpiecznego oceny Security Center":::

- W górnej części strony **zalecenia** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Bezpieczna Ocena na stronie rekomendacji Security Center":::



### <a name="get-your-secure-score-from-the-rest-api"></a>Uzyskiwanie bezpiecznego wyniku z interfejsu API REST

Możesz uzyskać dostęp do oceny za pośrednictwem interfejsu API bezpiecznego oceny (obecnie w wersji zapoznawczej). Metody interfejsu API zapewniają elastyczność umożliwiającą wykonywanie zapytań dotyczących danych i Tworzenie własnego mechanizmu raportowania z bezpiecznymi wynikami w czasie. Na przykład możesz użyć [interfejsu API Secure Scores](/rest/api/securitycenter/securescores) , aby uzyskać ocenę dla określonej subskrypcji. Ponadto można użyć [interfejsu API kontroli](/rest/api/securitycenter/securescorecontrols) zabezpieczeń, aby wyświetlić listę kontrolek bezpieczeństwa i bieżący wynik subskrypcji.

![Pobieranie pojedynczego, bezpiecznego wyniku za pośrednictwem interfejsu API](media/secure-score-security-controls/single-secure-score-via-api.png)

Przykłady narzędzi wbudowanych w interfejsie API oceny zabezpieczeń znajdują się w obszarze "bezpieczeństwo" w [naszej społeczności usługi GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 



### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Pobierz bezpieczny wynik z grafu zasobów platformy Azure (ARG)

Usługa Azure Resource Graph zapewnia błyskawiczny dostęp do informacji o zasobach w środowiskach chmury z niezawodnymi możliwościami filtrowania, grupowania i sortowania. Jest to szybki i wydajny sposób wykonywania zapytań dotyczących informacji w ramach subskrypcji platformy Azure programowo lub z poziomu Azure Portal. [Dowiedz się więcej o usłudze Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/).

Aby uzyskać dostęp do bezpiecznego wyniku dla wielu subskrypcji z ARGUMENTem:

1. W Azure Portal Otwórz **Eksploratora Azure Resource Graph**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Uruchamianie Eksploratora Azure Resource Graph * * zalecenie" :::

1. Wprowadź zapytanie Kusto (korzystając z przykładów poniżej, aby uzyskać wskazówki).

    - To zapytanie zwraca identyfikator subskrypcji, bieżący wynik w punktach i jako wartość procentową oraz maksymalny wynik dla subskrypcji. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - To zapytanie zwraca stan wszystkich formantów zabezpieczeń. Dla każdej kontrolki uzyskasz liczbę zasobów w złej kondycji, bieżącą ocenę i maksymalny wynik. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Wybierz pozycję **Uruchom zapytanie**.

## <a name="how-your-secure-score-is-calculated"></a>Jak jest obliczany bezpieczny wynik 

Udział każdej kontroli zabezpieczeń w odniesieniu do ogólnej bezpiecznego wyniku jest widoczny na stronie zalecenia.

[![Ulepszony bezpieczny wynik wprowadza kontrolę zabezpieczeń](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Aby uzyskać wszystkie możliwe punkty kontroli zabezpieczeń, wszystkie zasoby muszą być zgodne ze wszystkimi zaleceniami dotyczącymi zabezpieczeń w ramach kontroli zabezpieczeń. Na przykład Security Center ma wiele zaleceń dotyczących zabezpieczania portów zarządzania. W przeszłości można było skorygować niektóre z tych powiązanych i zależnych zaleceń, pozostawiając inne nierozwiązane i bezpieczny wynik. Po zapoznaniu się z obiektywem, można łatwo zatwierdzić, że nie zabezpieczeń zostały ulepszone do momentu ich rozwiązania. Teraz należy skorygować wszystkie te elementy, aby wprowadzić różnicę dla bezpiecznego wyniku.

Na przykład kontrola zabezpieczeń o nazwie "Zastosuj aktualizacje systemu" ma maksymalny wynik sześciu punktów, który można zobaczyć w etykietce narzędzia dla potencjalnej podwyżki wartości formantu:

[![Kontrola zabezpieczeń "Zastosuj aktualizacje systemu"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Maksymalny wynik tej kontrolki, zastosowanie aktualizacji systemu, to zawsze 6. W tym przykładzie wykorzystano 50 zasobów. Dzięki temu Podzielmy maksymalny wynik o 50, a wynikiem jest to, że każdy zasób składa się z 0,12 punktów. 

* **Potencjalny wzrost** (0,12 x 8 niezdrowych zasobów = 0,96) — pozostałe punkty dostępne dla Ciebie w obrębie formantu. W przypadku skorygowania wszystkich zaleceń w tym formancie wynik zostanie zwiększony o 2% (w tym przypadku 0,96 punktów zaokrąglonych do 1 punktu). 
* **Bieżący wynik** (0,12 x 42 w dobrej kondycji = 5,04) — bieżący wynik dla tego formantu. Każda kontrolka przyczynia się do osiągnięcia łącznego wyniku. W tym przykładzie Kontrola ma wpływ na 5,04 punktów na bieżącą łączną sumę.
* **Maksymalny wynik** — Maksymalna liczba punktów, które można uzyskać, wykonując wszystkie zalecenia w formancie. Maksymalna ocena dla kontrolki wskazuje względne znaczenie tego formantu. Użyj wartości maksymalnego wyniku, aby klasyfikacja te problemy, aby działały w pierwszej kolejności. 


### <a name="calculations---understanding-your-score"></a>Obliczenia — zrozumienie oceny

|Metryka|Formuła i przykład|
|-|-|
|**Bieżący wynik kontroli zabezpieczeń**|<br>![Równanie obliczania bieżącego wyniku kontroli zabezpieczeń](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Każda indywidualna kontrola zabezpieczeń przyczynia się do osiągnięcia oceny zabezpieczeń. Każdy zasób, na który ma wpływ zalecenie w ramach kontroli, przyczynia się do bieżącego wyniku kontrolki. Bieżący wynik dla każdej kontrolki jest miarą stanu zasobów *w* kontrolce.<br>![Etykietki narzędzi pokazujące wartości używane podczas obliczania bieżącego wyniku kontroli zabezpieczeń](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>W tym przykładzie maksymalny wynik 6 zostałby podzielony przez 78, ponieważ to jest suma zasobów w dobrej kondycji i w nieprawidłowych Stanach.<br>6/78 = 0,0769<br>Mnożenie tego przez liczbę zasobów w dobrej kondycji (4) skutkuje bieżącym wynikiem:<br>0,0769 * 4 = **0,31**<br><br>|
|**Wskaźnik bezpieczeństwa**<br>Subskrypcja pojedyncza|<br>![Równanie do obliczania bieżącego wyniku bezpiecznego](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Jeden bezpieczny wynik subskrypcji z włączonymi wszystkimi kontrolkami](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>W tym przykładzie istnieje jedna subskrypcja z wszystkimi dostępnymi wszystkimi kontrolami zabezpieczeń (potencjalną maksymalną wartością wyniku 60 punktów). Wynik pokazuje 28 punktów z możliwego 60, a pozostałe 32 punkty są odzwierciedlone w postaci "potencjalne zwiększenie wyniku" w zakresie kontroli zabezpieczeń.<br>![Lista kontrolek i zwiększenie potencjalnego wyniku](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Wskaźnik bezpieczeństwa**<br>Wiele subskrypcji|<br>Bieżące wyniki dla wszystkich zasobów we wszystkich subskrypcjach są dodawane, a obliczenia są takie same jak w przypadku pojedynczej subskrypcji<br><br>Podczas przeglądania wielu subskrypcji, funkcja Secure Score szacuje wszystkie zasoby we wszystkich włączonych zasadach i grupuje ich łączny wpływ na maksymalny wynik kontroli zabezpieczeń.<br>![Zabezpieczony wynik dla wielu subskrypcji z włączonymi wszystkimi kontrolkami](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Połączony wynik **nie** jest średni; jest to raczej oceniane stan stanu wszystkich zasobów we wszystkich subskrypcjach.<br>Tutaj, jeśli przejdziesz do strony rekomendacje i dodasz dostępne punkty, zobaczysz, że jest to różnica między bieżącym wynikiem (24) i maksymalnym dostępnym wynikiem (60).|
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
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>Kontrola zabezpieczeń, Ocena i opis</b><br></th>
    <th class="tg-cly1"><b>Zalecenia</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Włącz usługę MFA (max Score 10)</p></strong>Jeśli do uwierzytelnienia użytkownika używasz tylko hasła, spowoduje to pozostawienie otwartego wektora ataku. Jeśli hasło jest słabe lub zostało ujawnione w innym miejscu, czy użytkownik może zalogować się przy użyciu nazwy użytkownika i hasła?<br>Gdy usługa <a href="https://www.microsoft.com/security/business/identity/mfa">MFA</a> jest włączona, Twoje konta są bezpieczniejsze i użytkownicy nadal mogą uwierzytelniać się w prawie każdej aplikacji z logowaniem jednokrotnym (SSO).</td>
    <td class="tg-lboi"; width=55%>- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji<br>- W ramach usługi MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Secure Management Ports (max Score 8)</p></strong>Wymuszanie ataków na docelowe porty zarządzania w celu uzyskania dostępu do maszyny wirtualnej. Ponieważ porty nie zawsze muszą być otwarte, jedną strategią zaradczą jest ograniczenie ekspozycji na porty przy użyciu kontroli dostępu do sieci just in Time, sieciowych grup zabezpieczeń i zarządzania portami maszyn wirtualnych.<br>Ponieważ wiele organizacji IT nie blokuje komunikacji SSH z sieci, osoby atakujące mogą tworzyć zaszyfrowane tunele, które umożliwiają portom RDP w zainfekowanych systemach komunikowanie się z poleceniu atakującego w celu kontroli serwerów. Osoby atakujące mogą korzystać z podsystemu Windows Remote Management w celu późniejszego przechodzenia przez środowisko i korzystania z skradzionych poświadczeń w celu uzyskania dostępu do innych zasobów w sieci.</td>
    <td class="tg-lboi"; width=55%>- Porty zarządzania maszyn wirtualnych powinny być chronione za pomocą kontroli dostępu just in Time do sieci<br>- Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń<br>- Porty zarządzania powinny być zamknięte na maszynach wirtualnych</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Zastosuj aktualizacje systemu (wynik maksymalny 6)</p></strong>Aktualizacje systemu zapewniają organizacjom możliwość utrzymania sprawności działania, zmniejszania luk w zabezpieczeniach i zapewnienia bardziej stabilnego środowiska dla użytkowników końcowych. Niestosowanie aktualizacji pozostawia Niepoprawione luki w zabezpieczeniach i wyniki w środowiskach, które są podatne na ataki. Luki w zabezpieczeniach można wykorzystać i prowadzić do utraty danych, eksfiltracji danych, oprogramowania wymuszającego okup i nadużycia zasobów. Aby wdrożyć aktualizacje systemu, można użyć <a href="/azure/automation/update-management/overview">rozwiązania Update Management do zarządzania poprawkami i aktualizacjami</a> dla maszyn wirtualnych. Zarządzanie aktualizacjami to proces kontroli nad wdrażaniem i konserwacją wydań oprogramowania.</td>
    <td class="tg-lboi"; width=55%>- Na maszynach należy rozwiązać problemy z kondycją agenta monitorowania<br>- Agent monitorowania powinien być zainstalowany w zestawach skalowania maszyn wirtualnych<br>- Agent monitorowania powinien być zainstalowany na swoich maszynach<br>- Należy zaktualizować wersję systemu operacyjnego dla ról usługi w chmurze<br>- Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych<br>- Aktualizacje systemu powinny być zainstalowane na maszynach<br>- Aby zastosować aktualizacje systemu, należy ponownie uruchomić maszyny.<br>- Usługi Kubernetes należy uaktualnić do niezagrożonej wersji Kubernetes<br>- Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>- Agenta Log Analytics należy zainstalować na komputerach z systemem Windows Azure ARC (wersja zapoznawcza)<br>- Agent Log Analytics powinien być zainstalowany na komputerach z systemem Linux Azure ARC (wersja zapoznawcza)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Koryguj luki w zabezpieczeniach (maksymalny wynik 6)</p></strong>Luka w zabezpieczeniach jest przyczyną, że aktor zagrożeń może wykorzystać, aby naruszyć poufność, dostępność lub integralność zasobu. <a href="/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt">Zarządzanie lukami</a> zmniejsza narażenie na awarie organizacji, zwiększa ochronę obszaru punktu końcowego, podnosi odporność organizacji i zmniejsza podatność na ataki zasobów. Zarządzanie zagrożeniami i lukami w zabezpieczeniach zapewnia wgląd w oprogramowanie i konfiguracje zabezpieczeń oraz zapewnia zalecenia dotyczące środków zaradczych.</td>
    <td class="tg-lboi"; width=55%>- Zaawansowana ochrona danych powinna być włączona na SQL Database<br>- Luki w zabezpieczeniach Azure Container Registry obrazów należy skorygować<br>- Luki w zabezpieczeniach baz danych SQL należy skorygować<br>- Usterki należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach<br>- Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniu zarządzanym SQL<br>- Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL<br>- Rozwiązanie do oceny luk w zabezpieczeniach powinno być zainstalowane na maszynach wirtualnych<br>- Obrazy kontenerów powinny być wdrażane tylko z zaufanych rejestrów (wersja zapoznawcza)<br>- Dodatek Azure Policy dla Kubernetes powinien być zainstalowany i włączony w klastrach (wersja zapoznawcza)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Włącz szyfrowanie w spoczynku (maksymalny wynik 4)</p></strong><a href="/azure/security/fundamentals/encryption-atrest">Szyfrowanie w spoczynku</a> zapewnia ochronę danych przechowywanych danych. Ataki na dane przechowywane w spoczynku obejmują próby uzyskania fizycznego dostępu do sprzętu, na którym dane są zapisywane. Platformy Azure używają szyfrowania symetrycznego do szyfrowania i odszyfrowywania dużych ilości danych przechowywanych w stanie spoczynku. Symetryczny klucz szyfrowania jest używany do szyfrowania danych podczas zapisywania w magazynie. Ten klucz szyfrowania jest również używany do odszyfrowywania tych danych, ponieważ jest readied do użycia w pamięci. Klucze muszą być przechowywane w bezpiecznej lokalizacji z kontrolą dostępu opartą na tożsamości i zasadami inspekcji. Jedna taka bezpieczna lokalizacja jest Azure Key Vault. Jeśli osoba atakująca uzyska zaszyfrowane dane, ale nie klucze szyfrowania, osoba atakująca nie może uzyskać dostępu do danych bez przerywania szyfrowania.</td>
    <td class="tg-lboi"; width=55%>- Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych<br>- Transparent Data Encryption na SQL Database powinna być włączona<br>- Zmienne konta usługi Automation powinny być szyfrowane<br>- W klastrach Service Fabric Właściwość ClusterProtectionLevel ma ustawioną wartość EncryptAndSign<br>- Funkcja ochrony programu SQL Server TDE powinna być szyfrowana przy użyciu własnego klucza</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Szyfruj dane podczas przesyłania (max Score 4)</p></strong>Dane są przesyłane podczas przesyłania między składnikami, lokalizacjami lub programami. Organizacje, które nie chronią danych podczas przesyłania, są podatne na ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji. Protokoły SSL/TLS powinny być używane do wymiany danych, a sieć VPN jest zalecana. Podczas wysyłania zaszyfrowanych danych między maszyną wirtualną platformy Azure i lokalizacją lokalną za pośrednictwem Internetu można użyć bramy sieci wirtualnej, takiej jak <a href="/azure/vpn-gateway/vpn-gateway-about-vpngateways">Azure VPN Gateway</a> , aby wysyłać zaszyfrowany ruch sieciowy.</td>
    <td class="tg-lboi"; width=55%>- Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS<br>- aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS<br>- Należy włączyć tylko bezpieczne połączenia z Redis Cache<br>- Należy włączyć bezpieczny transfer do kont magazynu<br>- Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS<br>- Prywatny punkt końcowy powinien być włączony dla serwerów PostgreSQL<br>- Należy włączyć połączenie SSL dla serwerów bazy danych PostgreSQL<br>- Dla serwerów baz danych MySQL powinna być włączona funkcja Wymuszaj połączenie SSL<br>- Protokół TLS należy zaktualizować do najnowszej wersji dla aplikacji interfejsu API<br>- Protokół TLS należy zaktualizować do najnowszej wersji aplikacji funkcji<br>- Protokół TLS należy zaktualizować do najnowszej wersji aplikacji sieci Web<br>- FTPS powinna być wymagana w aplikacji interfejsu API<br>- FTPS powinna być wymagana w aplikacji funkcji<br>- FTPS powinna być wymagana w aplikacji sieci Web</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Zarządzanie dostępem i uprawnieniami (maksymalny wynik 4)</p></strong>Podstawową częścią programu zabezpieczeń jest zapewnienie, że użytkownicy mają wymagany dostęp do wykonywania swoich zadań, ale nie więcej niż to: <a href="/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models">model dostępu o najniższych uprawnieniach</a>.<br>Kontroluj dostęp do zasobów przez tworzenie przypisań ról z <a href="/azure/role-based-access-control/overview">kontrolą dostępu opartą na rolach na platformie Azure (RBAC)</a>. Przypisanie roli składa się z trzech elementów:<br>- <strong>Podmiot zabezpieczeń</strong>: obiekt, do którego użytkownik żąda dostępu<br>- <strong>Definicja roli</strong>: ich uprawnienia<br>- <strong>Zakres</strong>: zestaw zasobów, do których mają zastosowanie uprawnienia</td>
    <td class="tg-lboi"; width=55%>- Przestarzałe konta powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>- Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>- Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>- Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>- Do subskrypcji powinien być przypisany więcej niż jeden właściciel<br>- Kontrola dostępu oparta na rolach na platformie Azure (RBAC) powinna być używana w usługach Kubernetes Services (wersja zapoznawcza)<br>- W klastrach Service Fabric należy używać tylko Azure Active Directory do uwierzytelniania klientów<br>- Nazwy główne usług powinny być używane do ochrony subskrypcji zamiast certyfikatów zarządzania<br>- W przypadku kontenerów (wersja zapoznawcza) powinny być wymuszane najniższe możliwości systemu Linux.<br>- Niemodyfikowalny (tylko do odczytu) główny system plików powinien zostać wymuszony dla kontenerów (wersja zapoznawcza)<br>- Należy unikać kontenera z eskalacją uprawnień (wersja zapoznawcza)<br>- Należy unikać uruchamiania kontenerów jako użytkownik główny (wersja zapoznawcza)<br>- Należy unikać obsługi kontenerów chronionych przestrzeni nazw hosta (wersja zapoznawcza)<br>- Użycie instalacji woluminów HostPath na początku powinno być ograniczone do znanej listy (wersja zapoznawcza)<br>- Należy unikać używania kontenerów uprzywilejowanych (wersja zapoznawcza)<br>- Dodatek Azure Policy dla Kubernetes powinien być zainstalowany i włączony w klastrach (wersja zapoznawcza)<br>- Aplikacje sieci Web powinny zażądać certyfikatu SSL dla wszystkich żądań przychodzących<br>- Tożsamość zarządzana powinna być używana w aplikacji interfejsu API<br>- Tożsamość zarządzana powinna być używana w aplikacji funkcji<br>- Tożsamość zarządzana powinna być używana w aplikacji sieci Web</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Skoryguj konfiguracje zabezpieczeń (maksymalny wynik 4)</p></strong>Nieprawidłowo skonfigurowane zasoby IT mają większe ryzyko ataku. Podstawowe akcje związane z ograniczaniem funkcjonalności są często zapominane, gdy zasoby są wdrażane, a terminy ostateczne muszą zostać spełnione. Konfiguracje zabezpieczeń mogą znajdować się na dowolnym poziomie infrastruktury: od systemów operacyjnych i urządzeń sieciowych do zasobów chmury.<br>Azure Security Center ciągle porównuje konfigurację zasobów z wymaganiami dotyczącymi standardów branżowych, regulacji i testów porównawczych. Po skonfigurowaniu odpowiednich "pakietów zgodności" (standardy i linie bazowe), które są przeznaczone dla organizacji, wszelkie luki spowodują zaleceń w zakresie zabezpieczeń, które obejmują CCEID oraz wyjaśnienie potencjalnego wpływu na bezpieczeństwo.<br>Często używane pakiety to <a href="/azure/security/benchmarks/introduction">testy zabezpieczeń platformy Azure i usługi</a> <a href="https://www.cisecurity.org/benchmark/azure/">CIS Microsoft Azure w wersji testowej 1.1.0</a></td>
    <td class="tg-lboi"; width=55%>- Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować<br>- Luki w zabezpieczeniach konfiguracji zabezpieczeń na maszynach należy skorygować<br>- Luki w zabezpieczeniach konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować<br>- Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>- Agent monitorowania powinien być zainstalowany na swoich maszynach<br>- Agenta Log Analytics należy zainstalować na komputerach z systemem Windows Azure ARC (wersja zapoznawcza)<br>- Agent Log Analytics powinien być zainstalowany na komputerach z systemem Linux Azure ARC (wersja zapoznawcza)<br>- Agent monitorowania powinien być zainstalowany w zestawach skalowania maszyn wirtualnych<br>- Na maszynach należy rozwiązać problemy z kondycją agenta monitorowania<br>- Zastępowanie lub wyłączanie profilu AppArmornia kontenerów powinno być ograniczone (wersja zapoznawcza)<br>- Dodatek Azure Policy dla Kubernetes powinien być zainstalowany i włączony w klastrach (wersja zapoznawcza)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ogranicz nieautoryzowany dostęp do sieci (maksymalny wynik 4)</p></strong>Punkty końcowe w organizacji zapewniają bezpośrednie połączenie z sieci wirtualnej z obsługiwanymi usługami platformy Azure. Maszyny wirtualne w podsieci mogą komunikować się ze wszystkimi zasobami. Aby ograniczyć komunikację do i z zasobów w podsieci, Utwórz sieciową grupę zabezpieczeń i skojarz ją z podsiecią. Organizacje mogą ograniczać i chronić przed nieautoryzowanym ruchem, tworząc reguły ruchu przychodzącego i wychodzącego.</td>
    <td class="tg-lboi"; width=55%>- Przekazywanie adresów IP na maszynie wirtualnej powinno być wyłączone<br>- Dozwolone zakresy adresów IP powinny być zdefiniowane w usługach Kubernetes Services (wersja zapoznawcza)<br>- PRZESTARZAŁE Dostęp do App Services powinien być ograniczony (wersja zapoznawcza)<br>- PRZESTARZAŁE Reguły dla aplikacji sieci Web na IaaS sieciowych grup zabezpieczeń powinny być zaostrzone<br>- Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń<br>- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji interfejsu API<br>- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacja funkcji<br>- Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web<br>- Debugowanie zdalne powinno być wyłączone dla aplikacji interfejsu API<br>- Zdalne debugowanie powinno zostać wyłączone dla aplikacja funkcji<br>- Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web<br>- Dostęp powinien być ograniczony do ograniczeń sieciowych grup zabezpieczeń z maszynami wirtualnymi z Internetu<br>- Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych mających dostęp do Internetu powinny być zaostrzone<br>- Dodatek Azure Policy dla Kubernetes powinien być zainstalowany i włączony w klastrach (wersja zapoznawcza)<br>- Kontenery powinny nasłuchiwać tylko dozwolonych portów (wersja zapoznawcza)<br>- Usługi powinny nasłuchiwać tylko na dozwolonych portach (wersja zapoznawcza)<br>- Użycie sieci i portów hosta powinno być ograniczone (wersja zapoznawcza)<br>- Sieci wirtualne powinny być chronione przez zaporę platformy Azure (wersja zapoznawcza)<br>- Prywatny punkt końcowy powinien być włączony dla serwerów MariaDB<br>- Prywatny punkt końcowy powinien być włączony dla serwerów MySQL<br>- Prywatny punkt końcowy powinien być włączony dla serwerów PostgreSQL</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Zastosuj adaptacyjną kontrolę aplikacji (maksymalny wynik 3)</p></strong>Adaptacyjna kontrola aplikacji (AAC) to inteligentne, zautomatyzowane i kompleksowe rozwiązanie, które umożliwia kontrolowanie, które aplikacje mogą być uruchamiane na maszynach Azure i poza platformą Azure. Pomaga również w zabezpieczaniu maszyn przed złośliwym oprogramowaniem.<br>Security Center używa usługi Machine Learning, aby utworzyć listę znanych bezpiecznych aplikacji dla grupy komputerów.<br>To innowacyjne podejście do zatwierdzonej listy aplikacji zapewnia korzyści związane z bezpieczeństwem bez złożoności zarządzania.<br>AAC jest szczególnie istotny dla serwerów utworzonych specjalnie dla celów, które muszą uruchamiać określony zestaw aplikacji.</td>
    <td class="tg-lboi"; width=55%>- Na maszynach wirtualnych należy włączyć adaptacyjne kontrolki aplikacji<br>- Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>- Agent monitorowania powinien być zainstalowany na swoich maszynach<br>- Agenta Log Analytics należy zainstalować na komputerach z systemem Windows Azure ARC (wersja zapoznawcza)<br>- Agent Log Analytics powinien być zainstalowany na komputerach z systemem Linux Azure ARC (wersja zapoznawcza)<br>- Na maszynach należy rozwiązać problemy z kondycją agenta monitorowania</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Zastosuj klasyfikację danych (max Score 2)</p></strong>Klasyfikowanie danych organizacji według czułości i wpływu na działalność biznesową umożliwia określenie i przypisanie wartości do danych oraz zapewnia strategię i podstawę zarządzania.<br><a href="/azure/information-protection/what-is-information-protection">Azure Information Protection</a> może pomóc w klasyfikacji danych. Używa zasad szyfrowania, tożsamości i autoryzacji do ochrony danych i ograniczania dostępu do danych. Niektóre klasyfikacje, które są używane przez firmę Microsoft, są inne niż firmowe, publiczne, ogólne, poufne i wysoce poufne.</td>
    <td class="tg-lboi"; width=55%>- Poufne dane w bazach danych SQL powinny zostać sklasyfikowane (wersja zapoznawcza)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Ochrona aplikacji przed atakami DDoS (maksymalny wynik 2)</p></strong>Rozproszone ataki typu "odmowa usługi" (DDoS) powodują Przeciążenie zasobów i renderowanie aplikacji jako nieużytecznych. Użyj <a href="/azure/virtual-network/ddos-protection-overview">Azure DDoS Protection Standard</a> , aby chronić organizację przed trzema głównymi atakami DDoS:<br>- <strong>Ataki</strong> w postaci zalewania sieci z dozwolonym ruchem. DDoS Protection Standard ogranicza te ataki przez ich automatyczne przeabsorbowanie lub szybkie przejmowanie.<br>- <strong>Ataki protokołu</strong> powodują, że element docelowy jest niedostępny, wykorzystując słabe luki w stosie protokołu warstwy 3 i 4. DDoS Protection Standard ogranicza te ataki, blokując złośliwy ruch.<br>- <strong>Warstwa zasobów (aplikacji) atakują</strong> docelowe pakiety aplikacji sieci Web. Obrony przed tym typem za pomocą zapory aplikacji sieci Web i standardu DDoS Protection.</td>
    <td class="tg-lboi"; width=55%>- Należy włączyć Standard DDoS Protection<br>- Należy wymusić limity procesora CPU i pamięci kontenera (wersja zapoznawcza)<br>- Dodatek Azure Policy dla Kubernetes powinien być zainstalowany i włączony w klastrach (wersja zapoznawcza)</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Włącz program Endpoint Protection (maksymalny wynik 2)</p></strong>Aby zapewnić ochronę punktów końcowych przed złośliwym oprogramowaniem, czujniki behawioralne zbierają i przetwarzają dane z systemów operacyjnych punktów końcowych i wysyłają te dane do chmury prywatnej na potrzeby analizy. Analiza zabezpieczeń wykorzystuje duże ilości danych, uczenie maszynowe i inne źródła, aby zalecać reagowanie na zagrożenia. Na przykład usługa <a href="/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection">Microsoft Defender ATP</a> używa analizy zagrożeń, aby identyfikować metody ataków i generować alerty zabezpieczeń.<br>Security Center obsługuje następujące rozwiązania programu Endpoint Protection: Windows Defender, System Center Endpoint Protection, Trend Micro, Symantec v 12.1.1.1100, McAfee v10 for Windows, McAfee v10 dla systemów Linux i Sophos V9 dla systemu Linux. Jeśli Security Center wykryje dowolne z tych rozwiązań, zalecenie instalacji programu Endpoint Protection nie będzie już wyświetlane.</td>
    <td class="tg-lboi"; width=55%>- Błędy kondycji programu Endpoint Protection należy skorygować w przypadku zestawów skalowania maszyn wirtualnych<br>- Na maszynach należy rozwiązać problemy dotyczące kondycji programu Endpoint Protection<br>- Rozwiązanie Endpoint Protection powinno być zainstalowane w zestawach skalowania maszyn wirtualnych<br>- Zainstaluj rozwiązanie Endpoint Protection na maszynach wirtualnych<br>- Na maszynach należy rozwiązać problemy z kondycją agenta monitorowania<br>- Agent monitorowania powinien być zainstalowany w zestawach skalowania maszyn wirtualnych<br>- Agent monitorowania powinien być zainstalowany na swoich maszynach<br>- Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>- Agenta Log Analytics należy zainstalować na komputerach z systemem Windows Azure ARC (wersja zapoznawcza)<br>- Agent Log Analytics powinien być zainstalowany na komputerach z systemem Linux Azure ARC (wersja zapoznawcza)<br>- Zainstaluj rozwiązanie Endpoint Protection na swoich maszynach</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Włącz inspekcję i rejestrowanie (maksymalny wynik 1)</p></strong>Dane rejestrowania zawierają informacje o wcześniejszych problemach, które uniemożliwiają potencjalną pracę, mogą zwiększyć wydajność aplikacji i umożliwia automatyzację działań, które w przeciwnym razie byłyby ręczne.<br>- <strong>Dzienniki kontroli i zarządzania</strong> zawierają informacje dotyczące <a href="/azure/azure-resource-manager/management/overview">Azure Resource Manager</a> operacji.<br>- <strong>Dzienniki płaszczyzny danych</strong> zawierają informacje o zdarzeniach wywoływanych w ramach użycia zasobów platformy Azure.<br>- <strong>Przetwarzane zdarzenia</strong> zawierają informacje o przetworzonych zdarzeniach/alertach, które zostały przetworzone.</td>
    <td class="tg-lboi"; width=55%>- Inspekcja w programie SQL Server powinna być włączona<br>- Dzienniki diagnostyczne w App Services powinny być włączone<br>- Dzienniki diagnostyczne w Azure Data Lake Store powinny być włączone<br>- Dzienniki diagnostyczne w Azure Stream Analytics powinny być włączone<br>- Należy włączyć dzienniki diagnostyczne na kontach wsadowych<br>- Dzienniki diagnostyczne w Data Lake Analytics powinny być włączone<br>- Dzienniki diagnostyczne w centrum zdarzeń powinny być włączone<br>- Dzienniki diagnostyczne w IoT Hub powinny być włączone<br>- Dzienniki diagnostyczne w Key Vault powinny być włączone<br>- Dzienniki diagnostyczne w Logic Apps powinny być włączone<br>- Dzienniki diagnostyczne w usłudze wyszukiwania powinny być włączone<br>- Dzienniki diagnostyczne w Service Bus powinny być włączone<br>- Dzienniki diagnostyczne w Virtual Machine Scale Sets powinny być włączone<br>- Reguły alertów metryk powinny być skonfigurowane na kontach wsadowych<br>- Ustawienia inspekcji SQL powinny mieć Action-Groups skonfigurowany do przechwytywania działań krytycznych<br>- Serwery SQL powinny być skonfigurowane z okresem przechowywania inspekcji większym niż 90 dni.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Włącz zaawansowaną ochronę przed zagrożeniami (maksymalny wynik 0)</p></strong>Opcjonalne Azure Security Center plany ochrony przed zagrożeniami w usłudze Azure Defender zapewniają kompleksowe zabezpieczenia dla danego środowiska. Gdy Security Center wykryje zagrożenie w dowolnym obszarze środowiska, generuje alert. Te alerty opisują szczegóły zasobów, których to dotyczy, sugerowane kroki zaradcze, a w niektórych przypadkach opcja wyzwalająca aplikację logiki w odpowiedzi.<br>Każdy plan usługi Azure Defender jest oddzielną, opcjonalną ofertą, którą można włączyć przy użyciu odpowiedniego zalecenia w tej kontroli zabezpieczeń.<br><a href="/azure/security-center/threat-protection">Dowiedz się więcej o ochronie przed zagrożeniami w programie Security Center</a>.</td>
    <td class="tg-lboi"; width=55%>- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach Azure SQL Database<br>- Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL na maszynach<br>- Zaawansowana ochrona przed zagrożeniami powinna być włączona na Virtual Machines<br>- Zaawansowana ochrona przed zagrożeniami powinna być włączona w planach Azure App Service<br>- Zaawansowana ochrona przed zagrożeniami powinna być włączona na kontach usługi Azure Storage<br>- Zaawansowana ochrona przed zagrożeniami powinna być włączona w klastrach usługi Azure Kubernetes<br>- Zaawansowana ochrona przed zagrożeniami powinna być włączona w rejestrach Azure Container Registry<br>- Zaawansowana ochrona przed zagrożeniami powinna być włączona w magazynach Azure Key Vault</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong><p style="font-size: 16px">Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń (maksymalny wynik 0)</p></strong>Nowoczesne praktyki bezpieczeństwa "założono naruszenie" sieci obwodowej. Z tego powodu wiele najlepszych rozwiązań związanych z tym formantem koncentruje się na zarządzaniu tożsamościami.<br>Utrata kluczy i poświadczeń jest typowym problemem. <a href="/azure/key-vault/key-vault-overview">Azure Key Vault</a> chroni klucze i wpisy tajne, szyfrując klucze, pliki PFX i hasła.<br>Wirtualne sieci prywatne (VPN) są bezpiecznym sposobem uzyskiwania dostępu do maszyn wirtualnych. Jeśli sieci VPN nie są dostępne, użyj skomplikowanych hasła i uwierzytelniania dwuskładnikowego, takiego jak <a href="/azure/active-directory/authentication/concept-mfa-howitworks">usługa Azure AD Multi-Factor Authentication</a>. Uwierzytelnianie dwuskładnikowe eliminuje słabe znaczenie związane z używaniem tylko nazw użytkowników i haseł.<br>Korzystanie z mocnych platform uwierzytelniania i autoryzacji to inne najlepsze rozwiązanie. Korzystanie z tożsamości federacyjnych umożliwia organizacjom delegowanie zarządzania autoryzowanymi tożsamościami. Jest to również ważne, gdy pracownicy są przerwani, a ich dostęp musi zostać odwołany.</td>
    <td class="tg-lboi"; width=55%>- Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli<br>- Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji<br>- Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji<br>- Dostęp do kont magazynu z konfiguracją zapory i sieci wirtualnej należy ograniczyć<br>- Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw centrum zdarzeń<br>- Dla serwerów SQL powinien zostać zainicjowany administrator Azure Active Directory<br>- Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych<br>- Należy zdefiniować reguły autoryzacji w wystąpieniu centrum zdarzeń<br>- Konta magazynu należy migrować do nowych zasobów Azure Resource Manager<br>- Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager<br>- Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń<br>- Przeglądania Funkcja Windows Exploit Guard powinna być włączona <br>- Przeglądania Należy zainstalować agenta konfiguracji gościa<br>- Maszyny wirtualne, które nie są dostępne w Internecie, powinny być chronione przy użyciu sieciowych grup zabezpieczeń<br>- Azure Backup powinna być włączona dla maszyn wirtualnych<br>- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MariaDB<br>- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for MySQL<br>- Tworzenie kopii zapasowej nadmiarowej geograficznie należy włączyć dla Azure Database for PostgreSQL<br>- Aplikacja PHP powinna zostać zaktualizowana do najnowszej wersji dla aplikacji interfejsu API<br>- Aplikacja PHP powinna zostać zaktualizowana do najnowszej wersji aplikacji sieci Web<br>- Środowisko Java należy zaktualizować do najnowszej wersji dla aplikacji interfejsu API<br>- Środowisko Java należy zaktualizować do najnowszej wersji aplikacji funkcji<br>- Środowisko Java należy zaktualizować do najnowszej wersji aplikacji sieci Web<br>- Środowisko Python należy zaktualizować do najnowszej wersji dla aplikacji interfejsu API<br>- Środowisko Python należy zaktualizować do najnowszej wersji aplikacji funkcji<br>- Środowisko Python należy zaktualizować do najnowszej wersji aplikacji sieci Web<br>- Wartość przechowywania inspekcji dla serwerów SQL powinna wynosić co najmniej 90 dni</td>
  </tr>
</tbody>
</table>


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