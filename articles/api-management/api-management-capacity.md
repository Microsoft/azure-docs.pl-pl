---
title: Pojemność wystąpienia usługi Azure API Management | Microsoft Docs
description: W tym artykule wyjaśniono, co to jest Metryka pojemności i jak podejmować świadome decyzje dotyczące skalowania wystąpienia usługi Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: c7c002b57f2220ac0a9fba43a8081b2a4ed800e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93081063"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Pojemność wystąpienia usługi Azure API Management

**Wydajność** to najważniejszy [Azure monitor metryki](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) do podejmowania świadomych decyzji o tym, czy skalować wystąpienie API Management w celu zwiększenia obciążenia. Konstrukcja jest złożona i nakłada pewne zachowanie.

W tym artykule wyjaśniono, co to jest **pojemność** i jak działa. Pokazano, jak uzyskać dostęp do metryk **pojemności** w Azure Portal i sugeruje, kiedy należy rozważyć skalowanie lub uaktualnianie wystąpienia API Management.

> [!IMPORTANT]
> W tym artykule omówiono sposób monitorowania i skalowania wystąpienia usługi Azure API Management na podstawie jego metryki pojemności. Jednak równie ważne jest, aby zrozumieć, co się dzieje w przypadku wystąpienia poszczególnych API Management w rzeczywistości *osiągnęły* swoją wydajność. Usługa Azure API Management nie będzie stosować ograniczania poziomu usług, aby zapobiec fizycznym przeciążeniem wystąpień. Gdy wystąpienie osiągnie swoją pojemność fizyczną, będzie działać podobnie jak każdy przeciążony serwer sieci Web, który nie może przetworzyć żądań przychodzących: opóźnienie zostanie zwiększone, nastąpi odrzucanie błędów, przekroczenia limitu czasu itd. Oznacza to, że klienci interfejsu API powinni być przygotowani do pracy z tą możliwością podobną jak w przypadku każdej innej usługi zewnętrznej (np. stosując zasady ponawiania).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz dysponować:

+ Aktywna subskrypcja platformy Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Wystąpienie APIM. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Co to jest pojemność

![Diagram objaśniający metrykę pojemności.](./media/api-management-capacity/capacity-ingredients.png)

**Pojemność** jest wskaźnikiem obciążenia wystąpienia API Management. Odzwierciedla użycie zasobów (procesor CPU, pamięć) i długość kolejki sieciowej. Użycie procesora CPU i pamięci pokazuje wykorzystanie zasobów przez:

+ API Management usługi płaszczyzny danych, takie jak przetwarzanie żądań, które mogą obejmować przekazywanie żądań lub uruchamianie zasad.
+ API Management usługi płaszczyzny zarządzania, takie jak akcje zarządzania stosowane za pośrednictwem witryny Azure Portal lub ARM, lub obciążenie pochodzące z [portalu dla deweloperów](api-management-howto-developer-portal.md).
+ Wybrane procesy systemu operacyjnego, w tym procesy, które obejmują koszt uzgadniania protokołu TLS dla nowych połączeń.

Całkowita **pojemność** to średnia z poszczególnych jednostek wystąpienia API Management.

Mimo że **Metryka pojemności** została zaprojektowana w celu wypróbowania problemów z wystąpieniem API Management, istnieją przypadki, w których problemy nie zostaną odzwierciedlone w zmianach w **metryce wydajności**.

## <a name="capacity-metric-behavior"></a>Zachowanie metryki pojemności

Ze względu na jego konstrukcję w realnej **wydajności** może mieć wpływ wiele zmiennych, na przykład:

+ wzorce połączeń (nowe połączenie dla żądania a przy użyciu istniejącego połączenia)
+ rozmiar żądania i odpowiedzi
+ Zasady skonfigurowane na każdym interfejsie API lub liczbie klientów wysyłających żądania.

Im bardziej złożone operacje na żądaniach, tym większe zużycie **pojemności** . Na przykład złożone zasady transformacji zużywają znacznie więcej czasu procesora niż proste przekazywanie żądań. Zbyt niska odpowiedź usługi zaplecza zwiększy swój poziom.

> [!IMPORTANT]
> **Pojemność** nie jest bezpośrednią miarą liczby przetwarzanych żądań.

![Skoki metryk pojemności](./media/api-management-capacity/capacity-spikes.png)

**Pojemność** może również gwałtownie wzgwałtownić lub być większa od zera nawet wtedy, gdy nie są przetwarzane żadne żądania. Dzieje się tak z powodu działań specyficznych dla systemu lub platformy i nie należy brać pod uwagę podczas decydowania o tym, czy skalować wystąpienie.

**Metryka** o niskiej pojemności nie musi oznaczać, że wystąpienie API Management nie napotyka żadnych problemów.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Sprawdzanie pojemności przy użyciu witryny Azure Portal
  
![Metryka pojemności](./media/api-management-capacity/capacity-metric.png)  

1. Przejdź do wystąpienia APIM w [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Metryki**.
3. W sekcji purpurowa wybierz pozycję Metryka **pojemności** z dostępnych metryk i Pozostaw domyślną wartość agregacji **średniej** .

    > [!TIP]
    > Należy zawsze przyjrzeć się rozbiciem metryk **pojemności** na lokalizację, aby uniknąć nieprawidłowych interpretacji.

4. W zielonej sekcji Wybierz pozycję **Lokalizacja** , aby podzielić metrykę według wymiaru.
5. Wybierz żądany czas z górnego paska sekcji.

    Można ustawić alert dotyczący metryki, aby poinformować o nieoczekiwanym czasie. Na przykład Otrzymuj powiadomienia, gdy wystąpienie APIM przekracza oczekiwaną pojemność szczytową przez ponad 20 minut.

    >[!TIP]
    > Możesz skonfigurować alerty, aby poinformować o tym, kiedy usługa ma niską wydajność, lub użyj funkcji automatycznego skalowania Azure Monitor, aby automatycznie dodać jednostkę API Management platformy Azure. Operacja skalowania może potrwać około 30 minut, więc należy zaplanować reguły.  
    > Dozwolone jest tylko skalowanie lokalizacji głównej.

## <a name="use-capacity-for-scaling-decisions"></a>Używanie pojemności do podejmowania decyzji dotyczących skalowania

**Pojemność** jest metryką służącą do podejmowania decyzji, czy skalować wystąpienie API Management w celu zwiększenia obciążenia. Rozważ następujące kwestie:

+ Spojrzenie na długoterminową tendencję i średnią.
+ Ignorowanie nagłych skoków, które najprawdopodobniej nie są związane z jakimkolwiek wzrostem obciążenia (zobacz "zachowanie metryki wydajności" w celu wyjaśnienia).
+ Uaktualnienie lub skalowanie wystąpienia, gdy wartość **pojemności** przekracza 60% lub 70% przez dłuższy czas (na przykład 30 minut). Różne wartości mogą wydajniej współpracować z usługą lub scenariuszem.

>[!TIP]  
> Jeśli możesz wcześniej oszacować ruch, przetestuj wystąpienie APIM w oczekiwanych obciążeniach. Możesz stopniowo zwiększyć obciążenie żądaniami dzierżawy i monitorować wartość metryki pojemności odpowiadającą szczytowej obciążeniu. Postępuj zgodnie z instrukcjami z poprzedniej sekcji, Azure Portal aby dowiedzieć się, ile pojemności jest używanych w danym momencie.

## <a name="next-steps"></a>Następne kroki

- [Jak skalować lub uaktualnić wystąpienie usługi Azure API Management](upgrade-and-scale.md)
- [Optymalizowanie i oszczędzanie wydatków w chmurze](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)