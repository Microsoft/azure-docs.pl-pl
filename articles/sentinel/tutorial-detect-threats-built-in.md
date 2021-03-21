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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: b8a304d523052f1fe839f94608806df51d35fdf9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589926"
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

Dostępne są następujące typy szablonów:

- **Zabezpieczenia firmy Microsoft**
   
   Szablony zabezpieczeń firmy Microsoft automatycznie tworzą zdarzenia wskaźnikowe platformy Azure na podstawie alertów wygenerowanych w innych rozwiązaniach zabezpieczeń firmy Microsoft w czasie rzeczywistym. Możesz użyć reguł zabezpieczeń firmy Microsoft jako szablonu, aby utworzyć nowe reguły z podobną logiką. Aby uzyskać więcej informacji na temat reguł zabezpieczeń, zobacz [Automatyczne tworzenie zdarzeń z alertów zabezpieczeń firmy Microsoft](create-incidents-from-alerts.md).

- **Łączenie** 

    W oparciu o technologię syntezy zaawansowane wykrywanie ataków potokach wieloetapowych na platformie Azure Wskaźnikowo używa skalowalnych algorytmów uczenia maszynowego, które mogą skorelować wiele alertów o niskiej wierności i zdarzeń dla wielu produktów w zdarzeniach o wysokiej wierności i akcji. Funkcja Fusion jest domyślnie włączona. Ponieważ logika jest ukryta i w związku z tym nie można jej dostosowywać, możesz utworzyć tylko jedną regułę z tym szablonem.

    > [!IMPORTANT]
    > Niektóre wykrycia w szablonie reguły Fusion są obecnie dostępne w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.
    >
    > Aby zobaczyć, które wykrycia są w wersji zapoznawczej, zobacz [Zaawansowane wykrywanie ataków potokach wieloetapowych na platformie Azure](fusion.md).

- **Analiza zachowań uczenia maszynowego**

    Te szablony są oparte na własnościowych algorytmach uczenia maszynowego firmy Microsoft, dlatego nie można zobaczyć wewnętrznej logiki sposobu działania i uruchamiania programu. Ponieważ logika jest ukryta i w związku z tym nie można jej dostosowywać, możesz utworzyć tylko jedną regułę z każdym szablonem tego typu.

    > [!IMPORTANT]
    > Szablony reguł analizy zachowań uczenia maszynowego są obecnie dostępne w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

- **Zaplanowana**

    Zaplanowane reguły analizy opierają się na wbudowanych zapytaniach pisanych przez ekspertów zabezpieczeń firmy Microsoft. Można wyświetlić logikę zapytań i wprowadzić w niej zmiany. Można użyć szablonu reguł zaplanowanych i dostosować logikę zapytania i ustawienia planowania w celu utworzenia nowych reguł.

## <a name="use-out-of-the-box-detections"></a>Korzystanie z wbudowanych wykryć

1. Aby użyć wbudowanego szablonu, kliknij nazwę szablonu, a następnie kliknij przycisk **Utwórz regułę** w okienku szczegółów, aby utworzyć nową aktywną regułę opartą na tym szablonie. Każdy szablon zawiera listę wymaganych źródeł danych. Po otwarciu szablonu źródła danych są automatycznie sprawdzane pod kątem dostępności. Jeśli występuje problem z dostępnością, przycisk **Utwórz regułę** może być wyłączony lub może zostać wyświetlone ostrzeżenie.
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Panel podglądu reguły wykrywania":::
 
1. Kliknięcie przycisku **Utwórz regułę** spowoduje otwarcie Kreatora tworzenia reguły w oparciu o wybrany szablon. Wszystkie szczegóły są wypełniane przy użyciu szablonów zabezpieczeń **zaplanowanych** lub **Microsoft** , można dostosować logikę i inne ustawienia reguły, aby lepiej odpowiadały konkretnym potrzebom. Możesz powtórzyć ten proces, aby utworzyć dodatkowe reguły na podstawie szablonu wbudowanego. Po wykonaniu kroków w Kreatorze tworzenia reguły na końcu zakończono Tworzenie reguły na podstawie szablonu. Nowe reguły pojawią się na karcie **aktywne reguły** .

    Aby uzyskać więcej informacji na temat dostosowywania reguł w Kreatorze tworzenia reguł, zobacz [Samouczek: Tworzenie niestandardowych reguł analizy w celu wykrywania zagrożeń](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób rozpoczynania wykrywania zagrożeń przy użyciu platformy Azure. 

Aby dowiedzieć się, jak zautomatyzować odpowiedzi na zagrożenia, [Skonfiguruj automatyczne reagowanie na zagrożenia na platformie Azure](tutorial-respond-threats-playbook.md).
