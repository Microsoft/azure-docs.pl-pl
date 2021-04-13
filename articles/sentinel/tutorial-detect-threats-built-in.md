---
title: Wykrywaj zagrożenia dzięki wbudowanym regułom analizy na platformie Azure — wskaźnik | Microsoft Docs
description: Dowiedz się, jak używać wbudowanych reguł wykrywania zagrożeń opartych na gotowych szablonach, które powiadamiają o wystąpieniu podejrzanej sytuacji.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: yelevin
ms.openlocfilehash: ba757474ab24006b7b8b514bda085522bd353ea8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312675"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Samouczek: wykrywanie zagrożeń przy użyciu wbudowanych funkcji

Po [nawiązaniu połączenia ze źródłami danych](quickstart-onboard.md) z platformą Azure wskaźnikiem, użytkownik chce otrzymywać powiadomienia o wystąpieniu podejrzanych wystąpień. Z tego względu usługa Azure — wskaźnik przedstawia wbudowane szablony, które ułatwiają tworzenie reguł wykrywania zagrożeń. Te szablony zostały zaprojektowane przez zespół ekspertów ds. zabezpieczeń i analityków firmy Microsoft na podstawie znanych zagrożeń, typowych wektorów ataków oraz podejrzanych łańcuchów eskalacji działań. Reguły utworzone na podstawie tych szablonów będą automatycznie przeszukiwać środowisko w celu wyszukania wszystkich działań, które wyglądają podejrzanie. Wiele szablonów można dostosować, aby wyszukiwać działania lub odfiltrować je zgodnie z potrzebami. Alerty wygenerowane przez te reguły spowodują utworzenie zdarzeń, które można przypisać i zbadać w danym środowisku.

Ten samouczek ułatwia wykrywanie zagrożeń przy użyciu platformy Azure — wskaźnik:

> [!div class="checklist"]
> * Korzystanie z wbudowanych wykryć zagrożeń
> * Automatyzowanie odpowiedzi na zagrożenia

## <a name="about-out-of-the-box-detections"></a>Informacje o gotowych funkcjach wykrywania

Aby wyświetlić wszystkie gotowe do użycia wykrycia, przejdź do pozycji **Analiza** , a następnie pozycję **Szablony reguł**. Ta karta zawiera wszystkie wbudowane reguły kontrolki Azure — wskaźnik.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Używanie wbudowanych wykryć do znajdowania zagrożeń przy użyciu platformy Azure — wskaźnik":::

W poniższych sekcjach opisano typy wbudowanych szablonów dostępnych:

### <a name="microsoft-security"></a>Zabezpieczenia firmy Microsoft

Szablony zabezpieczeń firmy Microsoft automatycznie tworzą zdarzenia wskaźnikowe platformy Azure na podstawie alertów wygenerowanych w innych rozwiązaniach zabezpieczeń firmy Microsoft w czasie rzeczywistym. Możesz użyć reguł zabezpieczeń firmy Microsoft jako szablonu, aby utworzyć nowe reguły z podobną logiką.

Aby uzyskać więcej informacji na temat reguł zabezpieczeń, zobacz [Automatyczne tworzenie zdarzeń z alertów zabezpieczeń firmy Microsoft](create-incidents-from-alerts.md).

### <a name="fusion"></a>Łączenie

W oparciu o technologię syntezy zaawansowane wykrywanie ataków potokach wieloetapowych na platformie Azure Wskaźnikowo używa skalowalnych algorytmów uczenia maszynowego, które mogą skorelować wiele alertów o niskiej wierności i zdarzeń dla wielu produktów w zdarzeniach o wysokiej wierności i akcji. Funkcja Fusion jest domyślnie włączona. Ponieważ logika jest ukryta i w związku z tym nie można jej dostosowywać, możesz utworzyć tylko jedną regułę z tym szablonem.

> [!IMPORTANT]
> Niektóre wykrycia w szablonie reguły Fusion są obecnie dostępne w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.
>
> Aby zobaczyć, które wykrycia są w wersji zapoznawczej, zobacz [Zaawansowane wykrywanie ataków potokach wieloetapowych na platformie Azure](fusion.md).

### <a name="machine-learning-behavioral-analytics"></a>Analiza zachowań uczenia maszynowego

Te szablony są oparte na własnościowych algorytmach uczenia maszynowego firmy Microsoft, dlatego nie można zobaczyć wewnętrznej logiki sposobu działania i uruchamiania programu. Ponieważ logika jest ukryta i w związku z tym nie można jej dostosowywać, możesz utworzyć tylko jedną regułę z każdym szablonem tego typu.

> [!IMPORTANT]
> - Szablony reguł analizy zachowań uczenia maszynowego są obecnie dostępne w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.
>
> - Dzięki utworzeniu i włączeniu wszelkich reguł opartych na szablonach analizy zachowań ML **firma Microsoft uprawnia do kopiowania pozyskanych danych poza obszar roboczy obszaru roboczego wskaźnikowego platformy Azure** zgodnie z potrzebami do przetwarzania przez aparaty i modele uczenia maszynowego.

### <a name="scheduled"></a>Zaplanowana

Zaplanowane reguły analizy opierają się na wbudowanych zapytaniach pisanych przez ekspertów zabezpieczeń firmy Microsoft. Można wyświetlić logikę zapytań i wprowadzić w niej zmiany. Można użyć szablonu reguł zaplanowanych i dostosować logikę zapytania i ustawienia planowania w celu utworzenia nowych reguł.

> [!TIP]
> Opcje planowania reguł obejmują skonfigurowanie reguły do uruchamiania co określoną liczbę minut, godzin lub dni, z zegarem rozpoczynającym się po włączeniu reguły.
>
> Zalecamy zapoznanie się z tematem po włączeniu nowej lub edytowanej reguły analizy, aby upewnić się, że reguły będą uzyskiwać nowy stos zdarzeń w czasie. Na przykład możesz chcieć uruchomić regułę w synchronizacji z czasem, gdy analitycy SOC rozpoczną swoją pracę, a następnie włączą reguły.
>

## <a name="use-out-of-the-box-detections"></a>Korzystanie z wbudowanych wykryć

1. Aby użyć wbudowanego szablonu, kliknij nazwę szablonu, a następnie kliknij przycisk **Utwórz regułę** w okienku szczegółów, aby utworzyć nową aktywną regułę opartą na tym szablonie. Każdy szablon zawiera listę wymaganych źródeł danych. Po otwarciu szablonu źródła danych są automatycznie sprawdzane pod kątem dostępności. Jeśli występuje problem z dostępnością, przycisk **Utwórz regułę** może być wyłączony lub może zostać wyświetlone ostrzeżenie.

    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Panel podglądu reguły wykrywania":::

1. Kliknięcie przycisku **Utwórz regułę** spowoduje otwarcie Kreatora tworzenia reguły w oparciu o wybrany szablon. Wszystkie szczegóły są wypełniane przy użyciu szablonów zabezpieczeń **zaplanowanych** lub **Microsoft** , można dostosować logikę i inne ustawienia reguły, aby lepiej odpowiadały konkretnym potrzebom. Możesz powtórzyć ten proces, aby utworzyć dodatkowe reguły na podstawie szablonu wbudowanego. Po wykonaniu kroków w Kreatorze tworzenia reguły na końcu zakończono Tworzenie reguły na podstawie szablonu. Nowe reguły pojawią się na karcie **aktywne reguły** .

    Aby uzyskać więcej informacji na temat dostosowywania reguł w Kreatorze tworzenia reguł, zobacz [Samouczek: Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób rozpoczynania wykrywania zagrożeń przy użyciu platformy Azure.

Aby dowiedzieć się, jak zautomatyzować odpowiedzi na zagrożenia, [Skonfiguruj automatyczne reagowanie na zagrożenia na platformie Azure](tutorial-respond-threats-playbook.md).
