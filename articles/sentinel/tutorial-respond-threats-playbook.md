---
title: 'Samouczek: używanie elementy PlayBook z regułami automatyzacji na platformie Azure — wskaźnik'
description: Skorzystaj z tego samouczka, aby ułatwić korzystanie z elementy PlayBook wraz z regułami automatyzacji na platformie Azure, aby zautomatyzować reagowanie na zdarzenia i skorygować zagrożenia bezpieczeństwa.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2021
ms.author: yelevin
ms.openlocfilehash: 365ba9df39b4b3bd7397e86e6a51b285bf049242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600630"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-azure-sentinel"></a>Samouczek: używanie elementy PlayBook z regułami automatyzacji na platformie Azure — wskaźnik

W tym samouczku pokazano, jak za pomocą elementy PlayBook z regułami automatyzacji zautomatyzować reagowanie na zdarzenia i skorygować zagrożenia bezpieczeństwa wykryte przez wskaźnik na platformie Azure. Po ukończeniu tego samouczka będziesz mieć możliwość:

> [!div class="checklist"]
>
> * Tworzenie reguły automatyzacji
> * Utwórz element PlayBook
> * Dodawanie akcji do element PlayBook
> * Dołączanie element PlayBook do reguły automatyzacji lub reguły analizy w celu zautomatyzowania odpowiedzi na zagrożenia

## <a name="what-are-automation-rules-and-playbooks"></a>Co to są reguły automatyzacji i elementy PlayBook?

Reguły automatyzacji ułatwiają Klasyfikacja zdarzeń na platformie Azure — wskaźnik. Można ich używać do automatycznego przypisywania incydentów do odpowiednich pracowników, zamykania zdarzeń z zakłóceniami lub znanych fałszywych pozytywów, zmiany ich ważności i dodawania tagów. Są one również mechanizmem, za pomocą którego można uruchamiać elementy PlayBook w odpowiedzi na zdarzenia.

Elementy PlayBook to kolekcje procedur, które mogą być uruchamiane z obszaru danych platformy Azure w odpowiedzi na alert lub zdarzenie. Element PlayBook może pomóc zautomatyzować i zorganizować odpowiedź i można ją skonfigurować do automatycznego uruchamiania w przypadku wygenerowania określonych alertów lub zdarzeń przez dołączenie ich do reguły analizy lub reguły automatyzacji. Można go również uruchamiać ręcznie na żądanie.

Elementy PlayBook na platformie Azure — wskaźnik na podstawie przepływów pracy wbudowanych w [Azure Logic Apps](../logic-apps/logic-apps-overview.md), co oznacza, że otrzymujesz wszystkie funkcje, szerszym i wbudowane szablony Logic Apps. Każdy element PlayBook jest tworzony dla określonej subskrypcji, do której należy, ale **na ekranie wyświetlane** są wszystkie elementy PlayBook dostępne dla wybranych subskrypcji.

> [!NOTE]
> Ponieważ elementy PlayBook używają Azure Logic Apps, mogą zostać naliczone dodatkowe opłaty. Aby uzyskać więcej informacji, odwiedź stronę z cennikiem [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) .

Na przykład jeśli chcesz, aby użytkownicy mogli zrezygnować z poruszania się po sieci i kradzieży informacji, możesz utworzyć zautomatyzowaną, wieloaspektoweą odpowiedź na zdarzenia generowane przez zasady wykrywające zagrożonych użytkowników. Zacznij od utworzenia elementu element PlayBook, który podejmuje następujące działania:

1. Gdy element PlayBook jest wywoływana przez regułę usługi Automation przekazującą to zdarzenie, element PlayBook otwiera bilet w [usługi ServiceNow](/connectors/service-now/) lub innym systemie biletów IT.

1. Wysyła komunikat do kanału operacji zabezpieczeń w usłudze [Microsoft Teams](/connectors/teams/) lub [zapasu czasu](/connectors/slack/) , aby upewnić się, że analitycy zabezpieczeń wiedzą o zdarzeniu.

1. Wysyła także wszystkie informacje w zdarzeniu w wiadomości e-mail do administratora sieci w starszej firmie i administratora zabezpieczeń. Wiadomość e-mail będzie zawierać przyciski opcji **Blokuj** i **Ignoruj** użytkownika.

1. Element PlayBook czeka do momentu otrzymania odpowiedzi od administratorów, a następnie kontynuuje wykonanie kolejnych kroków.

1. Jeśli administratorzy wybierzą **blok**, wysyła polecenie do usługi Azure AD, aby wyłączyć użytkownika, a drugi w zaporze w celu zablokowania adresu IP.

1. Jeśli administratorzy wybierzą opcję **Ignoruj**, element PlayBook zamyka zdarzenie na platformie Azure, a bilet w usługi ServiceNow.

Aby wyzwolić element PlayBook, należy utworzyć regułę automatyzacji, która będzie uruchamiana po wygenerowaniu tych zdarzeń. Ta zasada spowoduje wykonanie następujących czynności:

1. Reguła zmienia stan zdarzenia na **aktywny**.

1. Przypisuje zdarzenie do analityka zadania związanego z zarządzaniem zdarzeniem tego typu.

1. Dodaje tag "złamany użytkownik".

1. Na koniec wywołuje właśnie utworzoną element PlayBook. ([W tym kroku są wymagane specjalne uprawnienia](automate-responses-with-playbooks.md#incident-creation-automated-response)).

Elementy PlayBook można uruchamiać automatycznie w odpowiedzi na zdarzenia, tworząc reguły automatyzacji, które wywołują elementy PlayBook jako akcje, jak w powyższym przykładzie. Mogą być również uruchamiane automatycznie w odpowiedzi na alerty, przez poinformowanie reguły analizy, aby automatycznie uruchamiała co najmniej jeden elementy PlayBook podczas generowania alertu. 

Możesz również ręcznie uruchomić element PlayBook na żądanie, jako odpowiedź na wybrany alert.

Uzyskaj pełniejsze i szczegółowe wprowadzenie do automatyzacji odpowiedzi na zagrożenia przy użyciu [reguł automatyzacji](automate-incident-handling-with-automation-rules.md) i [elementy PlayBook](automate-responses-with-playbooks.md) na platformie Azure.

> [!IMPORTANT]
>
> - **Reguły automatyzacji** i użycie **wyzwalacza zdarzenia** dla elementy PlayBook są obecnie dostępne w **wersji zapoznawczej**. Zapoznaj się z dodatkowymi [warunkami użytkowania Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych, aby uzyskać dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, Preview lub w inny sposób nie zostały jeszcze udostępnione publicznie.

## <a name="create-a-playbook"></a>Utwórz element PlayBook

Wykonaj następujące kroki, aby utworzyć nowy element PlayBook na platformie Azure — wskaźnik:

### <a name="prepare-the-playbook-and-logic-app"></a>Przygotowywanie aplikacji element playbook i logiki

1. Z menu nawigacyjnego **wskaźnikowego platformy Azure** wybierz pozycję **Automatyzacja**.

1. W górnym menu wybierz pozycję **Utwórz** i **Dodaj nowe element PlayBook**.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="Dodaj nowy element PlayBook":::

    Zostanie otwarta nowa karta przeglądarki i przejdziesz do kreatora **tworzenia aplikacji logiki** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="Tworzenie aplikacji logiki":::

1. Wprowadź swoją **subskrypcję** i **grupę zasobów**, a następnie nadaj element PlayBook nazwę w obszarze **Nazwa aplikacji logiki**.

1. W polu **region** wybierz region platformy Azure, w którym mają być przechowywane informacje o aplikacji logiki.

1. Jeśli chcesz monitorować działanie tego element PlayBook na potrzeby diagnostyki, zaznacz pole wyboru Włącz usługę **log Analytics** i wprowadź nazwę **obszaru roboczego log Analytics** .

1. Jeśli chcesz zastosować Tagi do element PlayBook, kliknij przycisk **Dalej: tagi >** (niepołączony ze znacznikami zastosowanymi przez reguły automatyzacji. [Dowiedz się więcej o tagach](../azure-resource-manager/management/tag-resources.md)). W przeciwnym razie kliknij przycisk **Przegląd + Utwórz**. Potwierdź podane szczegóły, a następnie kliknij przycisk **Utwórz**.

1. Podczas tworzenia i wdrażania element PlayBook (trwa to kilka minut) nastąpi przekierowanie do ekranu o nazwie **Microsoft. EmptyWorkflow**. Po wyświetleniu komunikatu "wdrożenie zostało zakończone" kliknij pozycję **Przejdź do zasobu.**

1. Nastąpi przekierowanie do element PlayBook [projektanta Logic Apps](../logic-apps/logic-apps-overview.md), w którym można rozpocząć projektowanie przepływu pracy. Zobaczysz ekran z krótkim wstępnym wideo, a kilka często używanymi wyzwalaczami i szablonami aplikacji logiki. [Dowiedz się więcej](../logic-apps/logic-apps-create-logic-apps-from-templates.md) na temat tworzenia element playbook z Logic Apps.

1. Wybierz szablon **pustej aplikacji logiki** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Galeria szablonów Logic Apps Designer":::

### <a name="choose-the-trigger"></a>Wybierz wyzwalacz

Każdy element PlayBook musi rozpoczynać się od wyzwalacza. Wyzwalacz definiuje akcję, która uruchomi element playbook i schemat, który będzie oczekiwać na otrzymanie element PlayBook.

1. Na pasku wyszukiwania Znajdź pozycję wskaźnik platformy Azure. Wybierz pozycję **wskaźnik na platformie Azure** , gdy zostanie ona wyświetlona w wynikach.

1. Na karcie **wyzwalacze** powstają widoczne są dwa wyzwalacze oferowane przez wskaźnik platformy Azure:
    - Gdy zostanie wyzwolona odpowiedź na alert dotyczący systemu Azure wskaźnikowego
    - Gdy jest wyzwalana reguła tworzenia incydentu usługi Azure Sentinel

   Wybierz wyzwalacz, który jest zgodny z typem tworzonego element PlayBook.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="Wybierz wyzwalacz dla element PlayBook":::

### <a name="add-actions"></a>Dodaj akcje

Teraz można zdefiniować, co się dzieje po wywołaniu element PlayBook. Możesz dodawać akcje, warunki logiczne, pętle lub przełączać warunki przypadków, wybierając pozycję **nowy krok**. Wybranie tej opcji spowoduje otwarcie nowej ramki w projektancie, w której można wybrać system lub aplikację do współpracy z lub warunek, który ma zostać ustawiony. Wprowadź nazwę systemu lub aplikacji na pasku wyszukiwania u góry ramki, a następnie wybierz spośród dostępnych wyników.

W każdym z tych kroków kliknięcie dowolnego pola powoduje wyświetlenie panelu z dwoma menu: **zawartość dynamiczna** i **wyrażenie**. Z menu **zawartość dynamiczna** można dodać odwołania do atrybutów alertu lub zdarzenia, które zostały przesłane do element PlayBook, w tym wartości i atrybuty wszystkich jednostek, których to dotyczy. Z menu **wyrażenie** możesz wybrać z dużej biblioteki funkcji, aby dodać do nich dodatkową logikę.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="Projektant aplikacji logiki":::

Ten zrzut ekranu przedstawia akcje i warunki, które należy dodać podczas tworzenia element PlayBook opisanego w przykładzie na początku tego dokumentu. Jedyną różnicą jest to, że w element PlayBook pokazanym tutaj jest używany **wyzwalacz alertu** zamiast **wyzwalacza zdarzenia**. Oznacza to, że użytkownik będzie wywoływać ten element PlayBook z reguły analizy bezpośrednio, a nie z poziomu reguły automatyzacji. Poniżej opisano metody wywoływania element PlayBook.

## <a name="automate-threat-responses"></a>Automatyzowanie odpowiedzi na zagrożenia

Utworzono element playbook i zdefiniowano wyzwalacz, ustawisz warunki i wydano akcje, które zostaną wykonane, oraz dane wyjściowe, które zostaną wygenerowane. Teraz należy określić kryteria, w których zostanie ono uruchomione, i skonfigurować mechanizm automatyzacji, który zostanie uruchomiony po spełnieniu tych kryteriów.

### <a name="respond-to-incidents"></a>Reagowanie na zdarzenia

Aby odpowiedzieć na **zdarzenie** , należy utworzyć [regułę automatyzacji](automate-incident-handling-with-automation-rules.md) , która będzie uruchamiana w momencie wygenerowania zdarzenia, a następnie wywoła element PlayBook.

Aby utworzyć regułę automatyzacji:

1. W bloku **Automatyzacja** w menu nawigacji wskaźnikowej platformy Azure wybierz pozycję **Utwórz** z górnego menu, a następnie **Dodaj nową regułę**.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="Dodaj nową regułę":::

1. Zostanie otwarty panel **Utwórz nową regułę automatyzacji** . Wprowadź nazwę reguły.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="Tworzenie reguły automatyzacji":::

1. Jeśli chcesz, aby reguła automatyzacji zaczęła obowiązywać tylko dla niektórych reguł analizy, określ, które z nich modyfikują warunek **nazwy reguły analizy** .

1. Dodaj inne warunki, od których ta reguła automatyzacji ma być zależna. Kliknij pozycję **Dodaj warunek** , a następnie wybierz pozycję warunki z listy rozwijanej. Lista warunków jest wypełniana przez szczegóły alertu i pola identyfikatora jednostki.

1. Wybierz akcje, które mają zostać wykonane przez tę regułę automatyzacji. Dostępne akcje to **przypisanie właściciela**, **zmiana stanu**, **zmiana ważności**, **dodanie tagów** i **uruchomienie element PlayBook**. Możesz dodać dowolną liczbę akcji.

1. Po dodaniu akcji **Uruchom element PlayBook** zostanie wyświetlony monit o wybranie z listy rozwijanej dostępne elementy PlayBook. Tylko elementy PlayBook, które rozpoczynają się od **wyzwalacza zdarzenia** mogą być uruchamiane z reguł automatyzacji, dlatego tylko zostaną wyświetlone na liście.

    > [!IMPORTANT]
    > Aby można było uruchamiać elementy PlayBook z reguł automatyzacji, należy przyznać wskaźnikom dostępu do systemu Azure. Jeśli element PlayBook pojawia się na liście rozwijanej "wygaszone", oznacza to, że wskaźnik nie ma uprawnień do grupy zasobów tej element PlayBook. Kliknij link **Zarządzaj uprawnieniami element PlayBook** , aby przypisać uprawnienia.
    > W otwartym okienku **Zarządzaj uprawnieniami** zaznacz pola wyboru grup zasobów zawierających elementy PlayBook, które chcesz uruchomić, a następnie kliknij przycisk **Zastosuj**.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="Zarządzanie uprawnieniami":::
    > - Użytkownik musi mieć uprawnienia **właściciela** do każdej grupy zasobów, do której chcesz przyznać uprawnienia do usługi Azure, i musi mieć rolę **współautor aplikacji logiki** w dowolnej grupie zasobów zawierającej elementy PlayBook, które mają być uruchamiane.
    > - W przypadku wdrożenia wielodostępnego, jeśli element PlayBook, które chcesz uruchomić, znajduje się w innej dzierżawie, musisz udzielić uprawnienia do działania wskaźnikowego platformy Azure, aby uruchomić element PlayBook w dzierżawie element PlayBook.
    >    1. Z menu nawigacyjnego wskaźnikowego platformy Azure w dzierżawie elementy PlayBook wybierz pozycję **Ustawienia**.
    >    1. W bloku **Ustawienia** wybierz kartę **Ustawienia** , a następnie pozycję Ekspander **element PlayBook** .
    >    1. Kliknij przycisk **Konfiguruj uprawnienia** , aby otworzyć panel **Zarządzaj uprawnieniami** wymieniony powyżej, i Kontynuuj zgodnie z opisem.

1. Ustaw datę wygaśnięcia reguły automatyzacji, jeśli ma ona mieć taką funkcję.

1. Wprowadź liczbę w obszarze **kolejność** , aby określić, gdzie w sekwencji reguł automatyzacji zostanie uruchomiona ta reguła.

1. Kliknij pozycję **Zastosuj**. Wszystko gotowe!

[Odkryj inne sposoby](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules) tworzenia reguł automatyzacji.

### <a name="respond-to-alerts"></a>Reagowanie na alerty

Aby odpowiedzieć na **alert** przy użyciu element PlayBook, można utworzyć **regułę analizy** lub edytować istniejącą, która jest uruchamiana po wygenerowaniu alertu, a następnie wybrać element PlayBook jako automatyczną odpowiedź w [Kreatorze reguł analizy](tutorial-detect-threats-custom.md).

1. W bloku **Analiza** w menu nawigacji wskaźnikowej platformy Azure wybierz regułę analizy, dla której chcesz zautomatyzować odpowiedź, a następnie kliknij pozycję **Edytuj** w okienku szczegółów.

1. Na stronie **kreatora reguły analizy — Edycja istniejącej reguły** wybierz kartę **automatyczna odpowiedź** .

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="Karta automatyczna odpowiedź":::

1. Z listy rozwijanej wybierz pozycję element PlayBook. Można wybrać więcej niż jeden element PlayBook, ale będzie dostępny tylko elementy PlayBook z **wyzwalaczem alertu** .

1. Na karcie **Przegląd i tworzenie** wybierz pozycję **Zapisz**.

## <a name="run-a-playbook-on-demand"></a>Uruchamianie podręcznika na żądanie

Możesz również uruchomić element PlayBook na żądanie.

 > [!NOTE]
 > Tylko elementy PlayBook przy użyciu **wyzwalacza alertu** można uruchomić na żądanie.

Aby uruchomić element PlayBook na żądanie:

1. Na stronie **incydenty** wybierz zdarzenie i kliknij pozycję **Wyświetl pełne szczegóły**.

2. Na karcie **alerty** Kliknij alert, na którym chcesz uruchomić element PlayBook, a następnie przewiń w prawo, a następnie kliknij pozycję **Wyświetl elementy PlayBook** i wybierz element PlayBook do **uruchomienia** z listy dostępnych elementy PlayBook w subskrypcji.

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się korzystać z reguł elementy playbook i automatyzacji w obszarze wskaźnikowym platformy Azure do reagowania na zagrożenia. 
- Dowiedz się, jak [aktywnie odszukiwać zagrożenia](hunting.md) za pomocą platformy Azure.
