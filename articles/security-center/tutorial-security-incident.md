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
ms.date: 09/30/2020
ms.author: memildin
ms.openlocfilehash: a04f94f5ebc7c1fdaf7b95e71dc8549e19863b39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614166"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Samouczek: Klasyfikacja, badanie i reagowanie na alerty zabezpieczeń
Usługa Security Center w sposób ciągły analizuje obciążenia chmury hybrydowej i ostrzega o złośliwych działaniach, używając zaawansowanych funkcji analitycznych oraz funkcji analizy zagrożeń. Możesz także zintegrować alerty z innych produktów i usług zabezpieczeń w Security Center i tworzyć niestandardowe alerty na podstawie własnych wskaźników lub źródeł analizy. Po wygenerowaniu alertu potrzebne jest sprawne działanie w celu zbadania i skorygowania jego przyczyny. 

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

:::image type="content" source="./media/tutorial-security-incident/alerts-list.png" alt-text="Strona listy alertów zabezpieczeń" lightbox="./media/tutorial-security-incident/alerts-list.png":::

Ta strona służy do przeglądania aktywnych alertów zabezpieczeń w środowisku, aby zdecydować, który alert należy zbadać jako pierwszy.

W przypadku alertów zabezpieczeń segregowania należy określić priorytety alertów na podstawie ważności alertu przez adresowanie alertów o wyższej ważności. Dowiedz się więcej o ważności alertów w [przypadku sklasyfikowania alertów?](security-center-alerts-overview.md#how-are-alerts-classified)

> [!TIP]
> Możesz połączyć Azure Security Center z najpopularniejszymi rozwiązaniami SIEM, w tym z platformą Azure, i korzystać z alertów z wybranego narzędzia. Dowiedz się więcej w temacie [Eksportowanie alertów do Siem](continuous-export.md).


## <a name="investigate-a-security-alert"></a>Badanie alertu zabezpieczeń

Po podjęciu decyzji o tym, który alert należy zbadać jako pierwszy:

1. Wybierz żądany alert.
1. Na stronie Przegląd alertów wybierz zasób do zbadania jako pierwszy.
1. Rozpocznij badanie z okienka po lewej stronie, które pokazuje ogólne informacje dotyczące alertu zabezpieczeń.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="Strona listy alertów zabezpieczeń":::

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

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Strona listy alertów zabezpieczeń" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Zapoznaj się z sekcją **ograniczanie zagrożenia** , aby zapoznać się z ręcznymi krokami w celu rozwiązania problemu.
1.  W celu zabezpieczenia zasobów i zapobiegania przyszłym atakom tego rodzaju należy skorygować zalecenia dotyczące zabezpieczeń w sekcji **zapobieganie atakom w przyszłości** .
1.  Aby wyzwolić aplikację logiki z zautomatyzowanymi krokami odpowiedzi, użyj sekcji **wyzwalanie automatycznej odpowiedzi** .
1.  Jeśli wykryte działanie *nie jest* złośliwe, możesz pominąć przyszłe alerty tego rodzaju za pomocą sekcji **Pomiń podobne alerty** .

1.  Po zakończeniu badania do alertu i odpowiedziano w odpowiedni sposób, Zmień stan na **odrzucony**.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Strona listy alertów zabezpieczeń":::

    Spowoduje to usunięcie alertu z głównej listy alertów. Możesz użyć filtru na stronie listy alertów, aby wyświetlić wszystkie alerty z **odrzuconym** stanem.

1.  Opcjonalnie Prześlij opinię na temat alertu do firmy Microsoft:
    1. Oznaczanie alertu jako **przydatnego** lub **nieprzydatnego** i dostarczającego
    1. Wybierz przyczynę i Dodaj komentarz.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Strona listy alertów zabezpieczeń":::

    > [!TIP]
    > Przeglądamy Twoją opinię, aby ulepszyć nasze algorytmy i zapewnić lepsze alerty zabezpieczeń.

## <a name="end-the-tutorial"></a>Zakończ samouczek

Inne przewodniki szybkiego startu i samouczki w tej kolekcji bazują na tym przewodniku. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, Zachowaj automatyczną obsługę administracyjną i usługę Azure Defender. 

Jeśli nie planujesz kontynuować pracy lub chcesz wyłączyć jedną z następujących funkcji:

1. Wróć do menu głównego Security Center i wybierz pozycję **Cennik i ustawienia**.
1. Wybierz odpowiednią subskrypcję.
1. Aby zmienić wersję, wybierz pozycję **Azure Defender off**.
1. Aby **wyłączyć automatyczne**Inicjowanie obsługi, Otwórz stronę **zbieranie danych** i ustaw **Automatyczne Inicjowanie obsługi** .
4. Wybierz pozycję **Zapisz**.

>[!NOTE]
> Wyłączenie automatycznej aprowizacji nie powoduje usunięcia agenta Log Analytics z maszyn wirtualnych platformy Azure, które już mają agenta. Wyłączenie automatycznej aprowizacji powoduje ograniczenie monitorowania zabezpieczeń dla zasobów.
>

## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono informacje o Security Center funkcjach, które będą używane podczas odpowiadania na alert zabezpieczeń. W przypadku pokrewnego materiału Zobacz:

- [Reagowanie na alerty usługi Azure Defender dla usługi Key Vault](defender-for-key-vault-usage.md)
- [Alerty zabezpieczeń — Podręcznik referencyjny](alerts-reference.md)
- [Wprowadzenie do usługi Azure Defender](azure-defender.md)