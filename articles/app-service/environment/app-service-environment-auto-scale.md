---
title: Skalowanie automatyczne w wersji 1
description: Skalowanie automatyczne i App Service Environment v1. Ten dokument jest dostępny tylko dla klientów korzystających ze starszej wersji V1 ASE.
author: btardif
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4f071c0d09fc2fa97eeea45bd82228b7eb8434a2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021660"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>Skalowanie automatyczne i App Service Environment v1

> [!NOTE]
> Ten artykuł dotyczy App Service Environment v1.  Istnieje nowsza wersja App Service Environment ułatwiająca korzystanie z bardziej wydajną infrastrukturą. Aby dowiedzieć się więcej o nowej wersji, Zacznij od [wprowadzenia do App Service Environment](intro.md).
> 

Środowiska Azure App Service obsługują *skalowanie* automatyczne. Można automatycznie skalować poszczególne pule procesów roboczych na podstawie metryk lub harmonogramu.

![Opcje skalowania automatycznego dla puli procesów roboczych.][intro]

Skalowanie automatyczne optymalizuje wykorzystanie zasobów, automatycznie zwiększając i zmniejszając środowisko App Service, aby dopasować je do budżetu i lub załadować profil.

## <a name="configure-worker-pool-autoscale"></a>Konfigurowanie automatycznego skalowania puli procesów roboczych
Możesz uzyskać dostęp do funkcji skalowania automatycznego z karty **Ustawienia** w puli procesów roboczych.

![Karta Ustawienia puli procesów roboczych.][settings-scale]

Z tego miejsca interfejs powinien być dobrze zaznajomiony, ponieważ jest to to samo środowisko, które jest widoczne podczas skalowania planu App Service. 

![Ustawienia skalowania ręcznego.][scale-manual]

Możesz również skonfigurować profil skalowania automatycznego.

![Ustawienia skalowania automatycznego.][scale-profile]

Profile automatycznego skalowania są przydatne do ustawiania limitów skali. W ten sposób można mieć spójną wydajność, ustawiając dolną granicę wartości skali (1) i przewidywalny limit wydatków, ustawiając górną granicę (2).

![Ustawienia skalowania w profilu.][scale-profile2]

Po zdefiniowaniu profilu można dodać reguły skalowania automatycznego w celu skalowania w górę lub w dół liczby wystąpień w puli procesów roboczych w granicach zdefiniowanych przez profil. Reguły automatycznego skalowania są oparte na metrykach.

![Reguła skalowania.][scale-rule]

 Do definiowania reguł skalowania automatycznego można użyć dowolnej puli procesów roboczych lub metryk frontonu. Te metryki są tymi samymi metrykami, które można monitorować w wykresach bloku zasobów lub ustawiać dla nich alerty.

## <a name="autoscale-example"></a>Przykład skalowania automatycznego
Automatyczne skalowanie środowiska App Service można uzyskać, przechodząc przez scenariusz.

W tym artykule objaśniono wszystkie niezbędne uwagi dotyczące konfigurowania automatycznego skalowania. Artykuł przeprowadzi Cię przez interakcje, które są odtwarzane w przypadku automatycznego skalowania App Service środowiska hostowane w App Service Environment.

### <a name="scenario-introduction"></a>Wprowadzenie scenariusza
Piotr jest administratorem systemu dla przedsiębiorstwa, który przeprowadził migrację części obciążeń zarządzanych przez nich do środowiska App Serviceowego.

Środowisko App Service jest skonfigurowane do ręcznego skalowania w następujący sposób:

* **Frontony:** 3
* **Pula procesów roboczych 1**: 10
* **Pula procesów roboczych 2**: 5
* **Pula procesów roboczych 3**: 5

Pula procesów roboczych 1 jest używana w przypadku obciążeń produkcyjnych, natomiast Pula procesów roboczych 2 i Pula procesów roboczych 3 są używane w celu zapewnienia jakości i obciążeń programistycznych.

Plany App Service dla pytań i odpowiedzi są skonfigurowane do skalowania ręcznego. Plan App Service produkcji jest ustawiany na automatyczne skalowanie w celu zajmowania się wahaniami obciążenia i ruchu.

Piotr jest bardzo zaznajomiony z aplikacją. Wiemy, że szczytowe godziny ładowania są z zakresu od 9:00 do 6:00 PM, ponieważ jest to aplikacja biznesowa (LOB), której pracownicy używają w biurze. Użycie spadnie po tym, gdy użytkownicy ukończyją ten dzień. Poza godzinami szczytu wciąż występuje pewne obciążenie, ponieważ użytkownicy mogą zdalnie uzyskiwać dostęp do aplikacji przy użyciu urządzeń przenośnych lub komputerów domowych. Plan App Service produkcji jest już skonfigurowany do automatycznego skalowania na podstawie użycia procesora CPU z następującymi regułami:

![Określone ustawienia dla aplikacji LOB.][asp-scale]

| **Profil skalowania automatycznego — dni tygodnia — plan App Service** | **Profil skalowania automatycznego — weekendy — plan App Service** |
| --- | --- |
| **Nazwa:** Profil dnia tygodnia |**Nazwa:** Profil weekendu |
| **Skaluj według:** Reguły harmonogramu i wydajności |**Skaluj według:** Reguły harmonogramu i wydajności |
| **Profil:** Dni robocze |**Profil:** Weekend |
| **Typ:** Wystąpieniu |**Typ:** Wystąpieniu |
| **Zakres docelowy:** od 5 do 20 wystąpień |**Zakres docelowy:** od 3 do 10 wystąpień |
| **Dni:** Poniedziałek, wtorek, środa, czwartek, piątek |**Dni:** Sobota, niedziela |
| **Godzina rozpoczęcia:** 9:00 am |**Godzina rozpoczęcia:** 9:00 am |
| **Strefa czasowa:** UTC — 08 |**Strefa czasowa:** UTC — 08 |
|  | |
| **Reguła automatycznego skalowania (skalowanie w górę)** |**Reguła automatycznego skalowania (skalowanie w górę)** |
| **Zasób:** Produkcja (App Service Environment) |**Zasób:** Produkcja (App Service Environment) |
| **Metryka:** TESTY |**Metryka:** TESTY |
| **Operacja:** Ponad 60% |**Operacja:** Ponad 80% |
| **Czas trwania:** 5 minut |**Czas trwania:** 10 minut |
| **Agregacja czasu:** Obliczon |**Agregacja czasu:** Obliczon |
| **Akcja:** Zwiększ liczbę o 2 |**Akcja:** Zwiększ liczbę o 1 |
| **Chłodna (minuty):** 15 |**Chłodna (minuty):** 20 |
|  | |
| **Reguła automatycznego skalowania (skalowanie w dół)** |**Reguła automatycznego skalowania (skalowanie w dół)** |
| **Zasób:** Produkcja (App Service Environment) |**Zasób:** Produkcja (App Service Environment) |
| **Metryka:** TESTY |**Metryka:** TESTY |
| **Operacja:** Mniej niż 30% |**Operacja:** Mniej niż 20% |
| **Czas trwania:** 10 minut |**Czas trwania:** 15 minut |
| **Agregacja czasu:** Obliczon |**Agregacja czasu:** Obliczon |
| **Akcja:** Zmniejsz liczbę o 1 |**Akcja:** Zmniejsz liczbę o 1 |
| **Chłodna (minuty):** 20 |**Chłodna (minuty):** 10 |

### <a name="app-service-plan-inflation-rate"></a>Częstotliwość inflacji planu App Service
Plany App Service, które są skonfigurowane do automatycznego skalowania, to w maksymalnej stawce za godzinę. Tę stawkę można obliczyć na podstawie wartości podanych w regule skalowania automatycznego.

Interpretacja i obliczanie *współczynnika inflacji planu App Service* jest ważne dla skalowania App Service środowiska, ponieważ zmiany skalowania do puli procesów roboczych nie są natychmiastowe.

Współczynnik inflacji planu App Service jest obliczany w następujący sposób:

![Obliczanie współczynnika inflacji App Service planu.][ASP-Inflation]

Na podstawie reguły skalowania automatycznego — Skaluj w górę dla profilu dnia tygodnia planu App Service produkcji:

![App Service planowanie odsetek dla dni tygodnia na podstawie reguły skalowania automatycznego.][Equation1]

W przypadku reguły automatycznego skalowania — skalowanie w górę dla profilu weekendowego planu App Service produkcji, formuła zostanie rozwiązana:

![App Service planowanie współczynnika inflacji dla weekendów na podstawie reguły skalowania automatycznego.][Equation2]

Tę wartość można również obliczyć dla operacji skalowania w dół.

W oparciu o regułę automatycznego skalowania (skalowanie w dół) dla profilu dnia roboczego planu App Service produkcji będzie to wyglądać następująco:

![App Service planowanie odsetek dla dni tygodnia na podstawie reguły skalowania automatycznego.][Equation3]

W przypadku reguły automatycznego skalowania — skalowanie w dół dla profilu weekendowego planu App Service produkcji, formuła zostanie rozpoznana jako:  

![App Service planowanie współczynnika inflacji dla weekendów na podstawie reguły skalowania automatycznego.][Equation4]

Plan App Service produkcyjnych może wzrosnąć o maksymalnie osiem wystąpień na godzinę w tygodniu i czterech wystąpieniach na godzinę w weekendie. Może zwolnić wystąpienia z maksymalną częstotliwością czterech wystąpień na godzinę w tygodniu i sześciu wystąpieniami/godz. w weekendy.

Jeśli wiele planów App Service jest obsługiwanych w puli procesów roboczych, należy obliczyć *łączną stawkę za inflację* jako sumę współczynnika inflacji dla wszystkich planów App Service, które są hostowane w tej puli procesów roboczych.

![Łączna liczba obliczeń dla wielu planów App Service hostowanych w puli procesów roboczych.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Definiowanie reguł automatycznego skalowania puli procesów roboczych przy użyciu planu App Service
Pule procesów roboczych, które obsługują plany App Service, które są skonfigurowane do automatycznego skalowania, muszą mieć przydzieloną bufor pojemności. Bufor umożliwia rozszerzanie i zmniejszanie planu App Service w miarę potrzeb. Minimalny bufor to obliczona suma App Service planu inflacji.

Ze względu na to, że zastosowanie App Service operacji skalowania środowiska wymaga pewnego czasu, każda zmiana powinna uwzględniać dalsze zmiany zapotrzebowania, które mogą wystąpić podczas wykonywania operacji skalowania. W celu uwzględnienia tego opóźnienia zalecamy użycie obliczonej sumy App Service planowanie inflacji jako minimalnej liczby wystąpień, które są dodawane dla każdej operacji automatycznego skalowania.

Dzięki tym informacjom Piotr może zdefiniować następujący profil i reguły automatycznego skalowania:

![Automatyczne skalowanie reguł profilu dla przykładu LOB.][Worker-Pool-Scale]

| **Profil skalowania automatycznego — dni tygodnia** | **Profil skalowania automatycznego — weekendy** |
| --- | --- |
| **Nazwa:** Profil dnia tygodnia |**Nazwa:** Profil weekendu |
| **Skaluj według:** Reguły harmonogramu i wydajności |**Skaluj według:** Reguły harmonogramu i wydajności |
| **Profil:** Dni robocze |**Profil:** Weekend |
| **Typ:** Wystąpieniu |**Typ:** Wystąpieniu |
| **Zakres docelowy:** od 13 do 25 wystąpień |**Zakres docelowy:** od 6 do 15 wystąpień |
| **Dni:** Poniedziałek, wtorek, środa, czwartek, piątek |**Dni:** Sobota, niedziela |
| **Godzina rozpoczęcia:** 7:00 am |**Godzina rozpoczęcia:** 9:00 am |
| **Strefa czasowa:** UTC — 08 |**Strefa czasowa:** UTC — 08 |
|  | |
| **Reguła automatycznego skalowania (skalowanie w górę)** |**Reguła automatycznego skalowania (skalowanie w górę)** |
| **Zasób:** Pula procesów roboczych 1 |**Zasób:** Pula procesów roboczych 1 |
| **Metryka:** WorkersAvailable |**Metryka:** WorkersAvailable |
| **Operacja:** Mniej niż 8 |**Operacja:** Mniej niż 3 |
| **Czas trwania:** 20 minut |**Czas trwania:** 30 minut |
| **Agregacja czasu:** Obliczon |**Agregacja czasu:** Obliczon |
| **Akcja:** Zwiększ liczbę o 8 |**Akcja:** Zwiększ liczbę o 3 |
| **Chłodna (minuty):** 180 |**Chłodna (minuty):** 180 |
|  | |
| **Reguła automatycznego skalowania (skalowanie w dół)** |**Reguła automatycznego skalowania (skalowanie w dół)** |
| **Zasób:** Pula procesów roboczych 1 |**Zasób:** Pula procesów roboczych 1 |
| **Metryka:** WorkersAvailable |**Metryka:** WorkersAvailable |
| **Operacja:** Większy niż 8 |**Operacja:** Większe niż 3 |
| **Czas trwania:** 20 minut |**Czas trwania:** 15 minut |
| **Agregacja czasu:** Obliczon |**Agregacja czasu:** Obliczon |
| **Akcja:** Zmniejsz liczbę o 2 |**Akcja:** Zmniejsz liczbę o 3 |
| **Chłodna (minuty):** 120 |**Chłodna (minuty):** 120 |

Zakres docelowy zdefiniowany w profilu jest obliczany przez minimalne wystąpienia zdefiniowane w profilu dla buforu App Service plan +.

Maksymalny zakres to suma wszystkich zakresów App Service obsługiwanych w puli procesów roboczych.

W przypadku skalowania w górę dla reguł skalowania w górę należy ustawić co najmniej wartość 1X App Service plan o inflację w górę.

Licznik Zmniejsz liczbę może zostać dostosowany do wartości z zakresu od 1 do 2 lub 1X współczynnika inflacji App Service planu dla skalowania w dół.

### <a name="autoscale-for-front-end-pool"></a>Skalowanie automatyczne dla puli frontonu
Reguły automatycznego skalowania frontonu są prostsze niż w przypadku pul procesów roboczych. Przede wszystkim należy  
Upewnij się, że czas trwania pomiaru i czasomierze cooldownu uwzględniają, że operacje skalowania w planie App Service nie są chwilowo.

W tym scenariuszu Piotr wie, że współczynnik błędów wzrasta po zakończeniu frontonu osiągnie 80% użycia procesora CPU i ustawia regułę automatycznego skalowania, aby zwiększyć wystąpienia w następujący sposób:

![Ustawienia automatycznego skalowania dla puli frontonu.][Front-End-Scale]

| **Profil skalowania automatycznego — frontony** |
| --- |
| **Nazwa:** Skalowanie automatyczne — frontony |
| **Skaluj według:** Reguły harmonogramu i wydajności |
| **Profil:** Używany |
| **Typ:** Wystąpieniu |
| **Zakres docelowy:** od 3 do 10 wystąpień |
| **Dni:** Używany |
| **Godzina rozpoczęcia:** 9:00 am |
| **Strefa czasowa:** UTC — 08 |
|  |
| **Reguła automatycznego skalowania (skalowanie w górę)** |
| **Zasób:** Pula frontonu |
| **Metryka:** TESTY |
| **Operacja:** Ponad 60% |
| **Czas trwania:** 20 minut |
| **Agregacja czasu:** Obliczon |
| **Akcja:** Zwiększ liczbę o 3 |
| **Chłodna (minuty):** 120 |
|  |
| **Reguła automatycznego skalowania (skalowanie w dół)** |
| **Zasób:** Pula procesów roboczych 1 |
| **Metryka:** TESTY |
| **Operacja:** Mniej niż 30% |
| **Czas trwania:** 20 minut |
| **Agregacja czasu:** Obliczon |
| **Akcja:** Zmniejsz liczbę o 3 |
| **Chłodna (minuty):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
