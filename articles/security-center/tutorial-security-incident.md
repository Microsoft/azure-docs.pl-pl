---
title: Samouczek odpowiedzi na alerty — Azure Security Center
description: W tym samouczku dowiesz się, jak Klasyfikacja alerty zabezpieczeń i określić główną przyczynę & zakresu alertu.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 41f96202bffc0169be9705cd7e67c1eedbecbc05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453448"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Samouczek: Klasyfikowanie i badanie alertów zabezpieczeń oraz odpowiadanie na nie
Security Center stale analizuje obciążenia chmury hybrydowej dzięki zaawansowanej analizie i analizie zagrożeń, aby otrzymywać alerty o potencjalnie złośliwych działaniach w zasobach w chmurze. Możesz także zintegrować alerty z innych produktów i usług zabezpieczeń w Security Center. Po podniesieniu alertu w celu zbadania i skorygowania potencjalnego problemu z zabezpieczeniami jest wymagana akcja Swift. 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Klasyfikacja alertów zabezpieczeń
> * Zbadaj alert zabezpieczeń, aby określić główną przyczynę
> * Odpowiadanie na alert zabezpieczeń i eliminowanie przyczyny głównej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne
Aby przekroczyć funkcje omówione w tym samouczku, musisz mieć włączoną usługę Azure Defender. Możesz bezpłatnie wypróbować usługę Azure Defender. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). Przewodnik Szybki Start [dotyczący rozpoczynania pracy z Security Center](security-center-get-started.md) przeprowadzi Cię przez proces uaktualniania.


## <a name="triage-security-alerts"></a>Klasyfikacja alertów zabezpieczeń
Usługa Security Center zapewnia spójny widok wszystkich alertów zabezpieczeń. Alerty zabezpieczeń są klasyfikowane w oparciu o ważność wykrytego działania. 

Klasyfikacja swoje alerty na stronie **alertów zabezpieczeń** :

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista alertów zabezpieczeń Azure Security Center":::

Ta strona służy do przeglądania aktywnych alertów zabezpieczeń w środowisku, aby zdecydować, który alert należy zbadać jako pierwszy.

W przypadku alertów zabezpieczeń segregowania należy określić priorytety alertów na podstawie ważności alertu przez adresowanie alertów o wyższej ważności. Dowiedz się więcej o ważności alertów w [przypadku sklasyfikowania alertów?](security-center-alerts-overview.md#how-are-alerts-classified)

> [!TIP]
> Możesz połączyć Azure Security Center z najpopularniejszymi rozwiązaniami SIEM, w tym z platformą Azure, i korzystać z alertów z wybranego narzędzia. Dowiedz się więcej w temacie [alerty przesyłania strumieniowego do rozwiązania do zarządzania usługami Siem, o i IT](export-to-siem.md).


## <a name="investigate-a-security-alert"></a>Badanie alertu zabezpieczeń

Po podjęciu decyzji o tym, który alert należy zbadać jako pierwszy:

1. Wybierz żądany alert.
1. Na stronie Przegląd alertów wybierz zasób do zbadania jako pierwszy.
1. Rozpocznij badanie z okienka po lewej stronie, które pokazuje ogólne informacje dotyczące alertu zabezpieczeń.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="W lewym okienku strony szczegółów alertu wyróżniono informacje wysokiego poziomu":::

    W tym okienku są wyświetlane:
    - Ważność alertu, stan i czas działania
    - Opis objaśniający dokładną aktywność, która została wykryta
    - Zasoby, których to dotyczy
    - Zabicia przeznaczenie łańcucha działania w macierzy MITRE ATT&

1. Aby uzyskać bardziej szczegółowe informacje, które mogą pomóc w zbadaniu podejrzanych działań, sprawdź kartę **szczegóły alertu** .

1. Po przejrzeniu informacji na tej stronie może być wystarczające przeprowadzenie odpowiedzi. Jeśli potrzebujesz dalszych szczegółów:

    - Skontaktuj się z właścicielem zasobu, aby sprawdzić, czy wykryta czynność jest fałszywie dodatnia.
    - Zbadaj dzienniki pierwotne wygenerowane przez zaatakowany zasób

## <a name="respond-to-a-security-alert"></a>Odpowiedz na alert zabezpieczeń
Po zbadaniu alertu i zrozumieniu jego zakresu można odpowiedzieć na alert zabezpieczeń z poziomu Azure Security Center:

1.  Otwórz kartę **podejmij działanie** , aby wyświetlić zalecane odpowiedzi.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Karta akcja z alertami zabezpieczeń" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Zapoznaj się z sekcją **ograniczanie zagrożenia** , aby zapoznać się z ręcznymi krokami w celu rozwiązania problemu.
1.  W celu zabezpieczenia zasobów i zapobiegania przyszłym atakom tego rodzaju należy skorygować zalecenia dotyczące zabezpieczeń w sekcji **zapobieganie atakom w przyszłości** .
1.  Aby wyzwolić aplikację logiki z zautomatyzowanymi krokami odpowiedzi, użyj sekcji **wyzwalanie automatycznej odpowiedzi** .
1.  Jeśli wykryte działanie *nie jest* złośliwe, możesz pominąć przyszłe alerty tego rodzaju za pomocą sekcji **Pomiń podobne alerty** .

1.  Po zakończeniu badania do alertu i odpowiedziano w odpowiedni sposób, Zmień stan na **odrzucony**.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Ustawianie stanu alertu":::

    Spowoduje to usunięcie alertu z głównej listy alertów. Możesz użyć filtru na stronie listy alertów, aby wyświetlić wszystkie alerty z **odrzuconym** stanem.

1.  Zachęcamy do przekazywania informacji zwrotnych o alercie do firmy Microsoft:
    1. Oznaczanie alertu jako **przydatnego** lub **nieprzydatnego**.
    1. Wybierz przyczynę i Dodaj komentarz.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Prześlij opinię do firmy Microsoft na temat użyteczności alertu":::

    > [!TIP]
    > Przeglądamy Twoją opinię, aby ulepszyć nasze algorytmy i zapewnić lepsze alerty zabezpieczeń.

## <a name="end-the-tutorial"></a>Zakończ samouczek

Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, Zachowaj automatyczną obsługę administracyjną i usługę Azure Defender. 

Jeśli nie planujesz kontynuować pracy lub chcesz wyłączyć jedną z następujących funkcji:

1. Wróć do menu głównego Security Center i wybierz pozycję **Cennik i ustawienia**.
1. Wybierz odpowiednią subskrypcję.
1. Aby zmienić wersję, wybierz pozycję **Azure Defender off**.
1. Aby **wyłączyć automatyczne** Inicjowanie obsługi, Otwórz stronę **zbieranie danych** i ustaw **Automatyczne Inicjowanie obsługi** .
1. Wybierz pozycję **Zapisz**.

>[!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia agenta Log Analytics z maszyn wirtualnych platformy Azure, które już mają agenta. Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów.
>

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono informacje o Security Center funkcjach, które będą używane podczas odpowiadania na alert zabezpieczeń. W przypadku pokrewnego materiału Zobacz:

- [Reagowanie na alerty usługi Azure Defender dla usługi Key Vault](defender-for-key-vault-usage.md)
- [Alerty zabezpieczeń — przewodnik informacyjny](alerts-reference.md)
- [Wprowadzenie do usługi Azure Defender](azure-defender.md)
