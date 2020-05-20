---
title: Zabezpiecz ocenę w Azure Security Center
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
ms.date: 03/10/2020
ms.author: memildin
ms.openlocfilehash: 1476e00386f95e1c86630a0aa0ea7e4bb4180307
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684693"
---
# <a name="enhanced-secure-score-preview-in-azure-security-center"></a>Ulepszony bezpieczny wynik (wersja zapoznawcza) w Azure Security Center

## <a name="introduction-to-secure-score"></a>Wprowadzenie do zabezpieczenia oceny

Azure Security Center ma dwa główne cele: aby pomóc Ci zrozumieć bieżącą sytuację bezpieczeństwa i pomóc efektywnie i efektywnie ulepszyć zabezpieczenia. Centralny aspekt Security Center, który umożliwia osiągnięcie tych celów, jest bezpiecznym wynikiem.

Security Center stale ocenia zasoby, subskrypcje i organizację pod kątem problemów z zabezpieczeniami. Następnie agreguje wszystkie wyniki w postaci pojedynczego wyniku, dzięki czemu można szybko powiedzieć, że aktualna sytuacja zabezpieczeń: wyższy wynik, tym niższy poziom ryzyka. Wynik służy do śledzenia wysiłków w zakresie zabezpieczeń i projektów w organizacji. 

Na stronie "zabezpieczenia oceny" Security Center należą:

- **Wynik** — wynik zabezpieczony jest pokazywany jako wartość procentowa, ale wartości bazowe są również jasne:

    [![W przypadku wartości procentowej z liczbami, które są czyste](media/secure-score-security-controls/secure-score-with-percentage.png)](media/secure-score-security-controls/secure-score-with-percentage.png#lightbox)

- **Kontrolki zabezpieczeń** — każda kontrolka jest logiczną grupą powiązanych zaleceń dotyczących zabezpieczeń i odzwierciedla zagrożone powierzchnie ataków. Kontrolka jest zestawem zaleceń dotyczących zabezpieczeń, z instrukcjami, które ułatwiają zaimplementowanie tych zaleceń. Wynik jest ulepszany tylko wtedy, gdy koryguje *wszystkie* zalecenia dotyczące pojedynczego zasobu w formancie.

    Aby od razu sprawdzić, jak dobrze organizacja zabezpiecza poszczególne osoby atakujące, zapoznaj się z wynikami każdej kontroli zabezpieczeń.

    Aby uzyskać więcej informacji, zobacz [jak poniżej jest obliczany bezpieczny wynik](secure-score-security-controls.md#how-the-secure-score-is-calculated) . 


>[!TIP]
> Wcześniejsze wersje Security Center przyznanych punktów na poziomie zalecenia: po skorygowaniu zalecenia dotyczącego pojedynczego zasobu Ulepszono bezpieczny wynik. Dzisiaj wynik poprawia się tylko wtedy, gdy korygujesz *wszystkie* zalecenia dotyczące pojedynczego zasobu w formancie. Dzięki temu wynik jest ulepszany tylko wtedy, gdy Ulepszono zabezpieczenia zasobu.
> Mimo że ta rozszerzona wersja jest nadal dostępna w wersji zapoznawczej, wcześniejsze bezpieczne środowisko oceny jest dostępne jako opcja w witrynie Azure Portal. 


## <a name="locating-your-secure-score"></a>Lokalizowanie bezpiecznego wyniku

Security Center wyświetla wynik w widocznym miejscu: jest to pierwsza czynność wyświetlana na stronie Przegląd. Jeśli klikniesz pozycję przez użytkownika na stronie dedykowany zabezpieczony wskaźnik, zobaczysz wynik podzielony przez subskrypcję. Kliknij jedną subskrypcję, aby wyświetlić szczegółową listę zaleceń z priorytetami i potencjalny wpływ, który korygowaniem ich na ocenę subskrypcji.

## <a name="how-the-secure-score-is-calculated"></a>Sposób obliczania bezpiecznego wyniku 

Udział każdej kontroli zabezpieczeń w odniesieniu do ogólnej bezpiecznego wyniku jest widoczny na stronie zalecenia.

[![Ulepszony bezpieczny wynik wprowadza kontrolę zabezpieczeń](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Aby uzyskać wszystkie możliwe punkty kontroli zabezpieczeń, wszystkie zasoby muszą być zgodne ze wszystkimi zaleceniami dotyczącymi zabezpieczeń w ramach kontroli zabezpieczeń. Na przykład Security Center ma wiele zaleceń dotyczących zabezpieczania portów zarządzania. W przeszłości można było skorygować niektóre z tych powiązanych i zależnych zaleceń, pozostawiając inne nierozwiązane i bezpieczny wynik. Po zapoznaniu się z obiektywem, można łatwo zatwierdzić, że nie zabezpieczeń zostały ulepszone do momentu ich rozwiązania. Teraz należy skorygować wszystkie te elementy, aby wprowadzić różnicę dla bezpiecznego wyniku.

Na przykład kontrola zabezpieczeń o nazwie "Zastosuj aktualizacje systemu" ma maksymalny wynik sześciu punktów, który można zobaczyć w etykietce narzędzia dla potencjalnej podwyżki wartości formantu:

[![Kontrola zabezpieczeń "Zastosuj aktualizacje systemu"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Możliwość kontroli zabezpieczeń "Zastosuj aktualizacje systemu" na powyższym zrzucie ekranu pokazuje "2% (1 punkt)". Oznacza to, że w przypadku skorygowania wszystkich zaleceń w tym formancie wynik zostanie zwiększony o 2% (w tym przypadku jeden punkt). Dla uproszczenia wartości kolumny "potencjalne zwiększenie" listy rekomendacji są zaokrąglane do liczb całkowitych. Etykietki narzędzi pokazują dokładne wartości:

* **Maksymalny wynik** — Maksymalna liczba punktów, które można uzyskać, wykonując wszystkie zalecenia w formancie. Maksymalna ocena dla kontrolki wskazuje względne znaczenie tego formantu. Użyj wartości maksymalnego wyniku, aby klasyfikacja, które problemy mają być wykonywane w pierwszej kolejności. 
* **Potencjalny wzrost** — pozostałe punkty dostępne dla Ciebie w obrębie formantu. Aby dodać te punkty do bezpiecznego wyniku, skoryguj wszystkie zalecenia dotyczące kontroli. W powyższym przykładzie, jeden punkt pokazywany dla kontrolki jest faktycznie 0,96 punktów.
* **Bieżący wynik** — bieżący wynik dla tego formantu. Każda kontrolka przyczynia się do osiągnięcia łącznego wyniku. W tym przykładzie Kontrola ma wpływ na 5,04 punktów na sumę. 

### <a name="calculations---understanding-your-score"></a>Obliczenia — zrozumienie oceny

|Metric|Formuła i przykład|
|-|-|
|**Bieżący wynik kontroli zabezpieczeń**|<br>![Równanie obliczania bieżącego wyniku kontroli zabezpieczeń](media/secure-score-security-controls/security-control-scoring-equation.png)<br><br>Każda indywidualna kontrola zabezpieczeń przyczynia się do osiągnięcia oceny zabezpieczeń. Każdy zasób, na który ma wpływ zalecenie w ramach kontroli, przyczynia się do bieżącego wyniku kontrolki. Bieżący wynik dla każdej kontrolki jest miarą stanu zasobów *w* kontrolce.<br>![Etykietki narzędzi pokazujące wartości używane podczas obliczania bieżącego wyniku kontroli zabezpieczeń](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>W tym przykładzie maksymalny wynik 6 zostałby podzielony przez 78, ponieważ to jest suma zasobów w dobrej kondycji i w nieprawidłowych Stanach.<br>6/78 = 0,0769<br>Mnożenie tego przez liczbę zasobów w dobrej kondycji (4) skutkuje bieżącym wynikiem:<br>0,0769 * 4 = **0,31**<br><br>|
|**Wskaźnik bezpieczeństwa**<br>Subskrypcja pojedyncza|<br>![Równanie do obliczania bieżącego wyniku bezpiecznego](media/secure-score-security-controls/secure-score-equation.png)<br><br>![Jeden bezpieczny wynik subskrypcji z włączonymi wszystkimi kontrolkami](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>W tym przykładzie istnieje jedna subskrypcja z wszystkimi dostępnymi wszystkimi kontrolami zabezpieczeń (potencjalną maksymalną wartością wyniku 60 punktów). Wynik pokazuje 28 punktów z możliwego 60, a pozostałe 32 punkty są odzwierciedlone w postaci "potencjalne zwiększenie wyniku" w zakresie kontroli zabezpieczeń.<br>![Lista kontrolek i zwiększenie potencjalnego wyniku](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Wskaźnik bezpieczeństwa**<br>Wiele subskrypcji|<br>Bieżący wynik dla wszystkich zasobów we wszystkich subskrypcjach zostanie dodany, a obliczenia są takie same jak w przypadku pojedynczej subskrypcji<br><br>Podczas przeglądania wielu subskrypcji, funkcja Secure Score szacuje wszystkie zasoby we wszystkich włączonych zasadach i grupuje ich łączny wpływ na maksymalny wynik kontroli zabezpieczeń.<br>![Zabezpieczony wynik dla wielu subskrypcji z włączonymi wszystkimi kontrolkami](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Połączony wynik **nie** jest średni; jest to raczej oceniane stan stanu wszystkich zasobów we wszystkich subskrypcjach.<br>Tutaj, jeśli przejdziesz do strony rekomendacje i dodasz dostępne punkty, zobaczysz, że jest to różnica między bieżącym wynikiem (24) i maksymalnym dostępnym wynikiem (60).|
||||

## <a name="improving-your-secure-score"></a>Ulepszanie bezpiecznego wyniku

Aby ulepszyć bezpieczny wynik, skoryguj zalecenia dotyczące zabezpieczeń z listy rekomendacji. Wszystkie zalecenia można skorygować ręcznie dla każdego zasobu lub przy użyciu **szybkiej poprawki** . Opcja (jeśli jest dostępna) w celu szybkiego zastosowania skorygowania zalecenia do grupy zasobów. Aby uzyskać więcej informacji, zobacz temat [korygowanie zaleceń](security-center-remediate-recommendations.md).

>[!IMPORTANT]
> Tylko wbudowane zalecenia mają wpływ na bezpieczny wynik.

## <a name="security-controls-and-their-recommendations"></a>Kontrola zabezpieczeń i ich zalecenia

W poniższej tabeli wymieniono kontrolki zabezpieczeń w Azure Security Center. Dla każdej kontrolki można zobaczyć maksymalną liczbę punktów, które można dodać do swojego bezpiecznego wyniku, jeśli korygujesz *wszystkie* zalecenia wymienione w formancie dla *wszystkich* zasobów. 

> [!TIP]
> Jeśli chcesz odfiltrować lub posortować tę listę inaczej, skopiuj ją i wklej do programu Excel.

|Kontrola zabezpieczeń|Maksymalna liczba bezpiecznych punktów punktacji|Zalecenia|
|----------------|:-------------------:|---------------|
|**Włączanie usługi MFA**|10|-Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami właściciela w ramach subskrypcji<br>-MFA należy włączyć konta z uprawnieniami do zapisu w Twojej subskrypcji|
|**Bezpieczne porty zarządzania**|8|— Kontrola dostępu do sieci just in Time powinna być stosowana na maszynach wirtualnych<br>-Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń<br>-Należy zamknąć porty zarządzania na maszynach wirtualnych|
|**Zastosuj aktualizacje systemu**|6|-Monitorowanie problemów z kondycją agenta na maszynach<br>-Należy zainstalować agenta monitorowania w zestawach skalowania maszyn wirtualnych<br>-Należy zainstalować agenta monitorowania na swoich maszynach<br>-Należy zaktualizować wersję systemu operacyjnego dla ról usługi w chmurze<br>-Należy zainstalować aktualizacje systemu dla zestawów skalowania maszyn wirtualnych<br>-Aktualizacje systemu powinny być zainstalowane na maszynach<br>-Należy ponownie uruchomić maszyny, aby zastosować aktualizacje systemu<br>-Kubernetes Services należy uaktualnić do wersji Kubernetes, która nie jest zagrożona<br>-Agent monitorowania powinien być zainstalowany na maszynach wirtualnych|
|**Korygowanie luk w zabezpieczeniach**|6|-Zaawansowane zabezpieczenia danych powinny być włączone na serwerach SQL<br>-Luki w zabezpieczeniach Azure Container Registry obrazów należy skorygować<br>— Luki w zabezpieczeniach baz danych SQL należy skorygować<br>-Luki w zabezpieczeniach należy skorygować przez rozwiązanie do oceny luk w zabezpieczeniach<br>-Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL<br>-Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL<br>-Rozwiązanie do oceny luk w zabezpieczeniach powinno być zainstalowane na maszynach wirtualnych|
|**Włącz szyfrowanie w spoczynku**|4|-Szyfrowanie dysków powinno być stosowane na maszynach wirtualnych<br>-Należy włączyć Transparent Data Encryption baz danych SQL<br>-Zmienne konta usługi Automation powinny być szyfrowane<br>-Service Fabric klastrów powinna mieć Właściwość ClusterProtectionLevel ustawioną na wartość EncryptAndSign<br>-Funkcja ochrony SQL Server TDE powinna być szyfrowana przy użyciu własnego klucza|
|**Szyfruj dane podczas przesyłania**|4|— Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS<br>-Aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS<br>-Należy włączyć tylko bezpieczne połączenia z Redis Cache<br>-Należy włączyć bezpieczny transfer do kont magazynu<br>-Aplikacja internetowa powinna być dostępna tylko za pośrednictwem protokołu HTTPS|
|**Zarządzanie dostępem i uprawnieniami**|4|-Przestarzałe konta powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>-Przestarzałe konta z uprawnieniami właściciela powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>— Konta zewnętrzne z uprawnieniami właściciela powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>— Konta zewnętrzne z uprawnieniami do zapisu powinny zostać usunięte z subskrypcji (wersja zapoznawcza)<br>-Do subskrypcji powinien być przypisany co najmniej jeden właściciel<br>-Oparta na rolach Access Control (RBAC) powinna być używana w usługach Kubernetes Services (wersja zapoznawcza)<br>— Klastry Service Fabric powinny używać tylko Azure Active Directory do uwierzytelniania klientów|
|**Korygowanie konfiguracji zabezpieczeń**|4|-Pod należy zdefiniować zasady zabezpieczeń w usługach Kubernetes Services<br>— Luki w zabezpieczeniach w konfiguracjach zabezpieczeń kontenerów należy skorygować<br>-Luki w zabezpieczeniach na maszynach należy skorygować<br>— Luki w zabezpieczeniach w konfiguracji zabezpieczeń w zestawach skalowania maszyn wirtualnych należy skorygować<br>-Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>-Należy zainstalować agenta monitorowania na swoich maszynach<br>-Należy zainstalować agenta monitorowania w zestawach skalowania maszyn wirtualnych<br>-Monitorowanie problemów z kondycją agenta na maszynach|
|**Ogranicz nieautoryzowany dostęp do sieci**|4|-Przekazywanie adresów IP na maszynie wirtualnej powinno być wyłączone<br>-Autoryzowane zakresy adresów IP powinny być zdefiniowane w usługach Kubernetes Services (wersja zapoznawcza)<br>-(PRZESTARZAŁy) dostęp do App Services powinien być ograniczony (wersja zapoznawcza)<br>— (PRZESTARZAŁe) reguły dla aplikacji sieci Web na IaaS sieciowych grup zabezpieczeń powinny mieć zabezpieczenia<br>-Maszyny wirtualne powinny być skojarzone z sieciową grupą zabezpieczeń<br>— Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji interfejsu API<br>— Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacja funkcji<br>— Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web<br>-Zdalne debugowanie powinno zostać wyłączone dla aplikacji interfejsu API<br>-Zdalne debugowanie powinno zostać wyłączone dla aplikacja funkcji<br>-Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web<br>-Dostęp powinien być ograniczony dla dozwolonych sieciowych grup zabezpieczeń z maszynami wirtualnymi z Internetu<br>-Reguły sieciowej grupy zabezpieczeń dla maszyn wirtualnych mających dostęp do Internetu powinny być zaostrzone|
|**Zastosuj adaptacyjną kontrolę aplikacji**|3|-Adaptacyjne kontrolki aplikacji powinny być włączone na maszynach wirtualnych<br>-Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>-Należy zainstalować agenta monitorowania na swoich maszynach<br>-Monitorowanie problemów z kondycją agenta na maszynach|
|**Zastosuj klasyfikację danych**|2|Dane poufne w bazach danych SQL powinny zostać sklasyfikowane (wersja zapoznawcza)|
|**Ochrona aplikacji przed atakami DDoS**|2|-DDoS Protection powinien być włączony Standard|
|**Włącz program Endpoint Protection**|2|-Błędy kondycji programu Endpoint Protection należy skorygować w przypadku zestawów skalowania maszyn wirtualnych<br>-Na maszynach należy rozwiązać problemy dotyczące kondycji programu Endpoint Protection<br>-Należy zainstalować rozwiązanie Endpoint Protection w zestawach skalowania maszyn wirtualnych<br>-Zainstaluj rozwiązanie Endpoint Protection na maszynach wirtualnych<br>-Monitorowanie problemów z kondycją agenta na maszynach<br>-Należy zainstalować agenta monitorowania w zestawach skalowania maszyn wirtualnych<br>-Należy zainstalować agenta monitorowania na swoich maszynach<br>-Agent monitorowania powinien być zainstalowany na maszynach wirtualnych<br>— Zainstaluj rozwiązanie Endpoint Protection na swoich maszynach|
|**Włącz inspekcję i rejestrowanie**|1|-Inspekcja w programie SQL Server powinna być włączona<br>-Należy włączyć dzienniki diagnostyczne w App Services<br>-Należy włączyć dzienniki diagnostyczne w Azure Data Lake Store<br>-Należy włączyć dzienniki diagnostyczne w Azure Stream Analytics<br>-Należy włączyć dzienniki diagnostyczne na kontach wsadowych<br>-Należy włączyć dzienniki diagnostyczne w Data Lake Analytics<br>-Należy włączyć dzienniki diagnostyczne w centrum zdarzeń<br>-Należy włączyć dzienniki diagnostyczne w IoT Hub<br>-Należy włączyć dzienniki diagnostyczne w Key Vault<br>-Należy włączyć dzienniki diagnostyczne w Logic Apps<br>-Należy włączyć dzienniki diagnostyczne w usłudze wyszukiwania<br>-Należy włączyć dzienniki diagnostyczne w Service Bus<br>-Należy włączyć dzienniki diagnostyczne w Virtual Machine Scale Sets<br>-Dla kont wsadowych należy skonfigurować reguły alertów metryk<br>-Ustawienia inspekcji SQL powinny mieć skonfigurowane grupy akcji do przechwytywania działań krytycznych<br>— Serwery SQL powinny być skonfigurowane z okresem przechowywania inspekcji większym niż 90 dni.|
|**Implementowanie najlepszych rozwiązań w zakresie zabezpieczeń**|0|-Dla subskrypcji należy wyznaczyć maksymalnie 3 właścicieli<br>-Konta zewnętrzne z uprawnieniami do odczytu powinny zostać usunięte z subskrypcji<br>-Uwierzytelnianie wieloskładnikowe powinno być włączone na kontach z uprawnieniami do odczytu w ramach subskrypcji<br>-Dostęp do kont magazynu z konfiguracją zapory i sieci wirtualnej należy ograniczyć<br>-Wszystkie reguły autoryzacji z wyjątkiem RootManageSharedAccessKey powinny zostać usunięte z przestrzeni nazw centrum zdarzeń<br>-Administrator Azure Active Directory powinien być zainicjowany dla serwerów SQL<br>-Należy zdefiniować reguły autoryzacji w wystąpieniu centrum zdarzeń<br>-Konta magazynu należy migrować do nowych zasobów Azure Resource Manager<br>-Maszyny wirtualne należy migrować do nowych zasobów Azure Resource Manager<br>— Zaawansowane ustawienia zabezpieczeń danych dla programu SQL Server powinny zawierać adres e-mail, aby otrzymywać alerty zabezpieczeń<br>-Zaawansowane zabezpieczenia danych powinny być włączone w zarządzanych wystąpieniach<br>-Wszystkie typy zaawansowanej ochrony przed zagrożeniami powinny być włączone w ustawieniach zaawansowanych danych wystąpienia zarządzanego SQL<br>— Powiadomienia e-mail dla administratorów i właścicieli subskrypcji powinny być włączone w zaawansowanych ustawieniach zabezpieczeń danych programu SQL Server<br>-Zaawansowane typy ochrony przed zagrożeniami powinny mieć ustawioną wartość "wszystkie" w ustawieniach zaawansowanych zabezpieczeń danych programu SQL Server<br>-Podsieci powinny być skojarzone z sieciową grupą zabezpieczeń<br>-Wszystkie typy zaawansowanej ochrony przed zagrożeniami powinny być włączone w ustawieniach zaawansowanych danych programu SQL Server<br>-[Wersja zapoznawcza] należy włączyć funkcję Windows Exploit Guard <br>-[Wersja zapoznawcza] Agent konfiguracji gościa powinien być zainstalowany|
||||

## <a name="secure-score-faq"></a>Bezpieczna ocena — często zadawane pytania

### <a name="why-has-my-secure-score-gone-down"></a>Dlaczego mój bezpieczny wskaźnik został usunięty?
Security Center przełączono do rozszerzonego bezpiecznego wyniku (obecnie w stanie wersji zapoznawczej), który zawiera zmiany w sposobie obliczania wyniku. Teraz należy rozwiązać wszystkie zalecenia dotyczące zasobu, aby otrzymywać punkty. Wyniki również zostały zmienione na skalę 0-10.

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Czy w ramach kontroli zabezpieczeń są adresowane tylko trzy z czterech zaleceń, czy mój bezpieczny wynik zmieni się?
Nie. Nie ulegnie zmianie, dopóki nie zostaną skorygowane wszystkie zalecenia dotyczące pojedynczego zasobu. Aby uzyskać maksymalny wynik kontrolki, należy skorygować wszystkie zalecenia dla wszystkich zasobów.

### <a name="is-the-previous-experience-of-the-secure-score-still-available"></a>Czy poprzednie środowisko bezpiecznego wyniku jest nadal dostępne? 
Tak. Przez czas, gdy będą działały obok siebie, aby ułatwić przejście. Oczekiwano, że poprzedni model zostanie rozmieszczony w czasie. 

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Jeśli zalecenie nie dotyczy mnie i nie zostało wyłączone w zasadach, czy moja kontrola zabezpieczeń zostanie zrealizowana, a my zabezpieczony?
Tak. Zalecamy wyłączenie zaleceń, gdy nie są one stosowane w danym środowisku. Aby uzyskać instrukcje dotyczące sposobu wyłączania określonego zalecenia, zobacz temat [wyłączanie zasad zabezpieczeń](https://docs.microsoft.com/azure/security-center/tutorial-security-policy#disable-security-policies).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Jeśli kontrola zabezpieczeń oferuje mi zero punktów na rzecz mojego bezpiecznego oceny, należy ją zignorować?
W niektórych przypadkach zobaczysz maksymalny wynik kontrolki większy od zera, ale wpływ wynosi zero. Gdy przyrostowy wynik naprawiania zasobów jest nieznaczny, jest on zaokrąglony do zera. Nie należy ignorować tych zaleceń, ponieważ nadal wprowadzają ulepszenia zabezpieczeń. Jedynym wyjątkiem jest "dodatkowa kontrola najlepszych rozwiązań". Korygowaniem te zalecenia nie spowodują zwiększenia wyniku, ale zwiększy swoje ogólne zabezpieczenia.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera opis bezpiecznego wyniku i kontroli zabezpieczeń, które wprowadza. W przypadku pokrewnego materiału zapoznaj się z następującymi artykułami:

- [Poznaj różne elementy zalecenia](security-center-recommendations.md)
- [Dowiedz się, jak skorygować zalecenia](security-center-remediate-recommendations.md)