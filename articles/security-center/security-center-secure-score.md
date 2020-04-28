---
title: Zabezpiecz ocenę w Azure Security Center | Microsoft Docs
description: " Ustaw priorytety zaleceń dotyczących zabezpieczeń, korzystając z bezpiecznego wyniku w Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79415764"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Popraw swój Bezpieczny wynik w Azure Security Center

> [!NOTE]
> W tym artykule znajduje się Poprzednia wersja bezpiecznego wyniku. Ten bezpieczny wynik oceny jest nadal dostępny w interfejsie użytkownika, ale zostanie on rozliczony z upływem czasu. Dwa bezpieczne środowiska oceny działają równolegle, aby umożliwić płynne przejście.
>
> Aby uzyskać szczegółowe informacje na temat nowszego, bezpiecznego wyniku, zobacz [tutaj](secure-score-security-controls.md).
>

Dzięki temu wielu usługom, które oferują korzyści z zabezpieczeń, często trudno jest wiedzieć, jakie kroki należy podjąć najpierw w celu zabezpieczenia i ograniczenia obciążenia. W ramach bezpiecznego oceny zawarto informacje o zaleceń dotyczących zabezpieczeń, dlatego należy wiedzieć, które zalecenia należy wykonać w pierwszej kolejności. Ułatwia to znalezienie najważniejszych luk w zabezpieczeniach, dzięki czemu można określić priorytety badania. Security score to narzędzie, które pomaga ocenić stan bezpieczeństwa obciążeń.

## <a name="secure-score-calculation"></a>Bezpieczne Obliczanie wyniku

Security Center naśladuje pracy analityka zabezpieczeń, przeglądanie zaleceń dotyczących zabezpieczeń i stosowanie zaawansowanych algorytmów w celu określenia, jak kluczowe są poszczególne zalecenia.
Usługa Azure Security Center stale przegląda Twoje aktywne rekomendacje i oblicza swój Bezpieczny wynik na podstawie ich, więc wynik zalecenia pochodzi od jego ważności i najlepszych rozwiązań w zakresie zabezpieczeń, które mają wpływ na bezpieczeństwo obciążeń.

Security Center zapewnia również **ogólny, bezpieczny wynik**. 

**Ogólnie bezpieczny wynik** to akumulacja wszystkich ocen rekomendacji. W zależności od wybranych opcji można wyświetlić ogólny, bezpieczny wynik w ramach subskrypcji lub grup zarządzania. Wyniki będą się różnić w zależności od wybranej subskrypcji i aktywnych zaleceń w tych subskrypcjach.

Aby sprawdzić, które zalecenia mają największy wpływ na bezpieczeństwo, możesz wyświetlić trzy najważniejsze zalecenia na pulpicie nawigacyjnym Security Center lub sortować zalecenia w bloku listy rekomendacji przy użyciu kolumny " **bezpieczeństwo oceny** ".

Aby wyświetlić ogólny, bezpieczny wynik:

1. Na pulpicie nawigacyjnym platformy Azure kliknij **Security Center** a następnie kliknij przycisk **Zabezpiecz ocenę**.

2. W górnej części można zobaczyć bezpieczny wynik:
   - **Całkowity łączny wynik** reprezentuje wynik na zasady, na wybraną subskrypcję
   - **Zabezpieczenia oceny według kategorii** pokazują, które zasoby wymagają największej uwagi
   - **Najważniejsze zalecenia według bezpiecznego wpływu na ocenę** zapewniają listę zaleceń, które pomogą ulepszyć bezpieczny wynik w większości sytuacji, gdy zostaną zaimplementowane.
 
   ![Wynik zabezpieczony](./media/security-center-secure-score/secure-score-dashboard.png)

3. W poniższej tabeli można zobaczyć każdą z Twoich subskrypcji i ogólny łączny wynik dla każdego z nich.

   > [!NOTE]
   > Suma bezpiecznego wyniku każdej subskrypcji nie jest równa ogólnej łącznej wartości. Wynik zabezpieczony jest obliczany na podstawie współczynnika zasobów w dobrej kondycji i łącznej liczby zasobów według rekomendacji, a nie sumy zabezpieczonych ocen w ramach subskrypcji. 
   >
4. Kliknij pozycję **Wyświetl zalecenia** , aby zobaczyć zalecenia dla tej subskrypcji, które możesz skorygować, aby poprawić swój Bezpieczny wynik.
4. Na liście zaleceń można zobaczyć, że dla każdego zalecenia istnieje kolumna, która reprezentuje **bezpieczny wpływ na ocenę**. Ta liczba przedstawia informacje o tym, ile ogólnego stanu bezpiecznego poprawi się, jeśli wykonasz zalecenia. Na przykład na poniższym ekranie, jeśli **korygujesz luki w zabezpieczeniach, konfiguracje zabezpieczeń kontenerów**, bezpieczny wynik zostanie zwiększony o 35 punktów.

   ![Wynik zabezpieczony](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Indywidualny wskaźnik zabezpieczeń

Ponadto, aby wyświetlać pojedyncze, bezpieczne oceny, można je znaleźć w bloku poszczególnych rekomendacji.  

Propozycja **Secure Score** to obliczenie oparte na współczynniku zasobów w dobrej kondycji i całkowitej liczbie zasobów. Jeśli liczba zasobów w dobrej kondycji jest równa całkowitej liczbie zasobów, uzyskasz maksymalny bezpieczny wynik zalecenia 50. Aby spróbować uzyskać bezpieczny wynik bliżej maksymalnego wyniku, napraw zasoby w złej kondycji, postępując zgodnie z zaleceniami.

**Problem z zaleceniem** pozwala poznać, jak dużo Twojego bezpiecznego wyniku jest lepszy w przypadku zastosowania kroków rekomendacji. Na przykład jeśli bezpiecznym wynikiem jest 42, a **zaleceń ma wpływ** + 3, wykonanie kroków opisanych w rekomendacji poprawi wynik, aby przejść do 45.

Zalecenie wskazuje, które zagrożenia są narażone na Twoje obciążenie, jeśli nie zostaną podjęte czynności zaradcze.

![indywidualny rekomendacja bezpieczna Ocena](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Następne kroki
W tym artykule pokazano, jak ulepszyć stan zabezpieczeń przy użyciu funkcji **bezpiecznego oceny** w Azure Security Center. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz:

* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
