---
title: Badanie zdarzeń za pomocą Azure Sentinel| Microsoft Docs
description: Z tego samouczka dowiesz się, jak za pomocą Azure Sentinel tworzyć zaawansowane reguły alertów generujące zdarzenia, które można przypisywać i badać.
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
ms.date: 04/08/2021
ms.author: yelevin
ms.openlocfilehash: 8980a8920b4f41f5a8e6afe106415032eef2055b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375846"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Samouczek: badanie zdarzeń za pomocą Azure Sentinel

> [!IMPORTANT]
> Wykres badania jest teraz ogólnie **dostępny.** 

Ten samouczek ułatwia badanie zdarzeń za pomocą Azure Sentinel. Po połączeniu źródeł danych z Azure Sentinel chcesz być powiadamiany, gdy zdarzy się coś podejrzanego. Aby to umożliwić, można Azure Sentinel zaawansowane reguły alertów, które generują zdarzenia, które można przypisać i zbadać.

Ten artykuł obejmuje:
> [!div class="checklist"]
> * Badanie zdarzeń
> * Korzystanie z wykresu badania
> * Reagowanie na zagrożenia

Zdarzenie może obejmować wiele alertów. Jest to agregacja wszystkich istotnych dowodów dla konkretnego badania. Zdarzenie jest tworzone na podstawie reguł analizy utworzonych na **stronie Analiza.** Właściwości związane z alertami, takie jak ważność i stan, są ustawiane na poziomie zdarzenia. Po Azure Sentinel, jakich zagrożeń szukasz i jak je znaleźć, możesz monitorować wykryte zagrożenia, badając zdarzenia.

## <a name="prerequisites"></a>Wymagania wstępne
- Badanie zdarzenia będzie możliwe tylko wtedy, gdy podczas konfigurowanie reguły analizy używane są pola mapowania jednostek. Graf badania wymaga, aby oryginalny incydent zawierał jednostki.

- Jeśli masz użytkownika-gościa, który musi przypisać zdarzenia, musisz [](../active-directory/roles/permissions-reference.md#directory-readers) mieć przypisaną rolę Czytelnik katalogu w dzierżawie usługi Azure AD. Do zwykłych użytkowników (niebędących gośćmi) ta rola jest domyślnie przypisana.

## <a name="how-to-investigate-incidents"></a>Jak badać zdarzenia

1. Wybierz **pozycję Zdarzenia.** Strona **Zdarzenia informuje** o tym, ile masz zdarzeń, ile jest otwartych, ile zdarzeń zostało ustawionych na W toku i ile zostało zamkniętych.  Dla każdego zdarzenia można zobaczyć czas jego zdarzenia oraz stan zdarzenia. Przyjrzyj się ważności, aby zdecydować, które zdarzenia mają być obsługiwane jako pierwsze.

    ![Wyświetlanie ważności zdarzenia](media/tutorial-investigate-cases/incident-severity.png)

1. W razie potrzeby można filtrować zdarzenia, na przykład według stanu lub ważności.

1. Aby rozpocząć badanie, wybierz konkretne zdarzenie. Po prawej stronie można zobaczyć szczegółowe informacje o zdarzeniu, w tym jego ważność, podsumowanie liczby jednostek, których dotyczy zdarzenie, nieprzetworzone zdarzenia, które wyzwoliły to zdarzenie, oraz unikatowy identyfikator zdarzenia.

1. Aby wyświetlić więcej szczegółów na temat alertów  i jednostek w zdarzeniu, wybierz pozycję Wyświetl pełne szczegóły na stronie zdarzenia i przejrzyj odpowiednie karty, które podsumowują informacje o zdarzeniu. 

    ![Wyświetlanie szczegółów alertu](media/tutorial-investigate-cases/incident-timeline.png)

    Na przykład:

    - Na karcie **Oś** czasu przejrzyj oś czasu alertów i zakładek w zdarzeniu, co może ułatwić odtworzenie osi czasu działań osoby atakującej.
    - Na karcie **Alerty** przejrzyj sam alert. Możesz wyświetlić wszystkie istotne informacje o alercie — zapytanie, które wyzwoliło alert, liczbę zwróconych wyników na zapytanie oraz możliwość uruchamiania podręczników w alertach. Aby jeszcze bardziej przejść do szczegółów zdarzenia, wybierz liczbę **zdarzeń**. Spowoduje to otwarcie zapytania, które wygenerowała wyniki i zdarzenia, które wyzwoliły alert w u usługi Log Analytics. 
    - Na **karcie Jednostki** można zobaczyć wszystkie jednostki zamapowane w ramach definicji reguły alertu.

1. Jeśli aktywnie badasz zdarzenie, dobrym pomysłem jest ustawienie stanu zdarzenia na  W toku do momentu jego zamknięcia.

1. Zdarzenia można przypisać do określonego użytkownika. Dla każdego zdarzenia można przypisać właściciela, ustawiając pole **Właściciel** zdarzenia. Wszystkie zdarzenia rozpoczynają się jako nieprzypisane. Możesz również dodać komentarze, aby inni analitycy mogli zrozumieć, co zostało zbadane i czego dotyczą Twoje obawy związane ze zdarzeniem.

    ![Przypisywanie zdarzenia do użytkownika](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Wybierz **pozycję Zbadaj,** aby wyświetlić mapę badania.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Korzystanie z wykresu badania w celu dogłębnego zgłębniania

Wykres badania umożliwia analitykom zadawanie właściwych pytań dla każdego badania. Wykres badania pomaga zrozumieć zakres potencjalnego zagrożenia bezpieczeństwa i zidentyfikować jego główną przyczynę przez skorelowanie odpowiednich danych z dowolną jednostką. Możesz dokładniej zbadać każdą jednostkę przedstawioną na grafie, wybierając ją i wybierając między różnymi opcjami rozszerzania.  
  
Wykres badania zawiera:

- **Kontekst wizualny z danych pierwotnych:** wykres wizualny na żywo wyświetla relacje między jednostkami wyodrębnione automatycznie z danych pierwotnych. Dzięki temu można łatwo zobaczyć połączenia między różnymi źródłami danych.

- **Odnajdywanie pełnego zakresu badania:** rozszerzanie zakresu badania przy użyciu wbudowanych zapytań eksploracji w celu wyekslorowania pełnego zakresu naruszenia.

- **Wbudowane kroki badania:** użyj wstępnie zdefiniowanych opcji eksploracji, aby upewnić się, że zadawaj odpowiednie pytania w przypadku zagrożenia.

Aby użyć wykresu badania:

1. Wybierz zdarzenie, a następnie wybierz pozycję **Zbadaj**. W ten sposób zostaniemy przeszli do wykresu badania. Wykres zawiera ilustracyjną mapę jednostek połączonych bezpośrednio z alertem i każdego zasobu połączonego dalej.

   > [!IMPORTANT] 
   > - Badanie zdarzenia będzie możliwe tylko wtedy, gdy podczas konfigurowanie reguły analizy używane są pola mapowania jednostek. Graf badania wymaga, aby oryginalny incydent zawierał jednostki.
   >
   > - Azure Sentinel obsługuje obecnie badanie **zdarzeń do 30 dni.**

   ![Wyświetlanie mapy](media/tutorial-investigate-cases/map1.png)

1. Wybierz jednostkę, aby otworzyć **okienko Jednostki,** aby przejrzeć informacje dotyczące tej jednostki.

    ![Wyświetlanie jednostek na mapie](media/tutorial-investigate-cases/map-entities.png)
  
1. Rozwiń swoje badanie, umieszczając wskaźnik myszy na każdej jednostce, aby wyświetlić listę pytań, które zostały zaprojektowane przez naszych ekspertów ds. zabezpieczeń i analityków dla poszczególnych typów jednostek w celu pogłębiania badania. Nazywamy te opcje **zapytaniami eksploracji**.

    ![Poznaj więcej szczegółów](media/tutorial-investigate-cases/exploration-cases.png)

   Na przykład na komputerze można zażądać powiązanych alertów. Jeśli wybierzesz zapytanie eksploracji, wynikowe upoważnień zostaną dodane z powrotem do grafu. W tym przykładzie wybranie opcji **Powiązane alerty** zwróciło następujące alerty do wykresu:

    ![Wyświetlanie powiązanych alertów](media/tutorial-investigate-cases/related-alerts.png)

1. Dla każdego zapytania eksploracji możesz wybrać opcję otwierania nieprzetworzonych wyników zdarzenia i zapytania używanego w umacie Log Analytics, wybierając pozycję **Zdarzenia \>**.

1. Aby zrozumieć zdarzenie, wykres zawiera równoległą oś czasu.

    ![Wyświetlanie osi czasu na mapie](media/tutorial-investigate-cases/map-timeline.png)

1. Umieść kursor na osi czasu, aby zobaczyć, które rzeczy na grafie wystąpiły w jakim momencie.

    ![Używanie osi czasu na mapie do badania alertów](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>Zamykanie zdarzenia

Po zakończeniu rozwiązania określonego zdarzenia (na przykład gdy badanie doszło do wniosku), należy ustawić stan zdarzenia **na Zamknięty.** Gdy to zrobisz, zostaniesz poproszony o sklasyfikowanie zdarzenia przez określenie przyczyny jego zamknięcia. Ten krok jest obowiązkowy. Kliknij **pozycję Wybierz** klasyfikację i z listy rozwijanej wybierz jedną z następujących opcji:

- Wynik prawdziwie dodatni — podejrzana aktywność
- Wynik nieszkodliwie dodatni — podejrzane, ale oczekiwane
- Wynik fałszywie dodatni — nieprawidłowa logika alertu
- Wynik fałszywie dodatni — nieprawidłowe dane
- Nieokreślone

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="Zrzut ekranu przedstawiający klasyfikacje dostępne na liście Wybierz klasyfikację.":::

Po wybraniu odpowiedniej klasyfikacji dodaj opisowy tekst w polu **Komentarz.** Będzie to przydatne w przypadku, gdy konieczne będzie powrót do tego zdarzenia. Gdy **wszystko** będzie gotowe, kliknij przycisk Zastosuj. Zdarzenie zostanie zamknięte.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{alt-text}":::

## <a name="next-steps"></a>Następne kroki
W tym samouczku opisano, jak rozpocząć badanie zdarzeń przy użyciu Azure Sentinel. Przejdź do samouczka, aby [dowiedzieć się, jak reagować na zagrożenia przy użyciu zautomatyzowanych podręczników.](tutorial-respond-threats-playbook.md)
> [!div class="nextstepaction"]
> [Reagowanie na zagrożenia](tutorial-respond-threats-playbook.md) w celu zautomatyzowania reakcji na zagrożenia.

