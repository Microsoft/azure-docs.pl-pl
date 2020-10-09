---
title: 'Samouczek: uruchamianie element PlayBook na platformie Azure — wskaźnik'
description: Skorzystaj z tego samouczka, aby ułatwić korzystanie z usługi Security elementy PlayBook w wskaźniku kontrolnym platformy Azure, aby ustawiać automatyczne reagowanie na zagrożenia związane z zabezpieczeniami.
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
ms.date: 02/18/2019
ms.author: yelevin
ms.openlocfilehash: 0e7d790fa9c5d4052ddb1e20defed1d1cc457a2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840204"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Samouczek: Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia na platformie Azure — wskaźnik



Ten samouczek ułatwia korzystanie z usługi Security elementy PlayBook w wskaźniku kontrolnym platformy Azure w celu automatycznego reagowania na problemy związane z zabezpieczeniami wykryte przez wskaźnik na platformie Azure.


> [!div class="checklist"]
> * Zrozumienie elementy PlayBook
> * Utwórz element PlayBook
> * Uruchamianie element PlayBook
> * Automatyzowanie odpowiedzi na zagrożenia


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Co to jest element PlayBook zabezpieczeń na platformie Azure — wskaźnik?

Element PlayBook zabezpieczeń to zbiór procedur, które mogą być uruchamiane z obszaru wskaźnikowego platformy Azure w odpowiedzi na alert. Element PlayBook zabezpieczeń może pomóc zautomatyzować i zorganizować odpowiedź i można ją uruchomić ręcznie lub skonfigurować do automatycznego uruchamiania po wyzwoleniu określonych alertów. Zabezpieczenia elementy PlayBook na platformie Azure wskazują na [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps), co oznacza, że otrzymujesz wszystkie funkcje, szerszym i wbudowane szablony Logic Apps. Każdy element PlayBook jest tworzony dla wybranej subskrypcji, ale po przeszukaniu na stronie elementy PlayBook zobaczysz wszystkie elementy PlayBook w ramach dowolnych subskrypcji.

> [!NOTE]
> Elementy PlayBook wykorzystać Azure Logic Apps, dlatego opłaty są naliczane. Odwiedź stronę cennika usługi [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/), aby zapoznać się ze szczegółami.

Na przykład jeśli martwisz się o złośliwych osobach atakujących uzyskujących dostęp do zasobów sieciowych, można ustawić alert, który szuka złośliwych adresów IP uzyskujących dostęp do sieci. Następnie można utworzyć element PlayBook, który wykonuje następujące czynności:
1. Po wyzwoleniu alertu Otwórz bilet w usługi ServiceNow lub dowolnym systemie biletów IT.
2. Wyślij wiadomość do kanału operacji zabezpieczeń w usłudze Microsoft Teams lub zapasu czasu, aby upewnić się, że analitycy zabezpieczeń wiedzą o zdarzeniu.
3. Wyślij wszystkie informacje z alertu do administratora sieci w starszej firmie i administratora zabezpieczeń. Wiadomość e-mail zawiera również dwa przyciski opcji użytkownika **blok** lub **Ignoruj**.
4. Element PlayBook będzie nadal działać po odebraniu odpowiedzi od administratorów.
5. Jeśli administratorzy wybierzą opcję **Blokuj**, adres IP jest blokowany w zaporze, a użytkownik jest wyłączony w usłudze Azure AD.
6. Jeśli administratorzy wybierzą opcję **Ignoruj**, alert zostanie zamknięty w obszarze Azure — wskaźnik i zdarzenie zostanie zamknięte w usługi ServiceNow.

Elementy PlayBook zabezpieczeń można uruchomić ręcznie lub automatycznie. Uruchamianie ich ręcznie oznacza, że po otrzymaniu alertu można uruchomić element PlayBook na żądanie jako odpowiedź na wybrany alert. Uruchamianie ich automatycznie oznacza, że podczas tworzenia reguły korelacji ustawia się ją w taki sposób, aby automatycznie uruchamiała co najmniej jeden elementy PlayBook, gdy zostanie wyzwolony alert.


## <a name="create-a-security-playbook"></a>Tworzenie element PlayBook zabezpieczeń

Wykonaj następujące kroki, aby utworzyć nowy element PlayBook zabezpieczeń na platformie Azure:

1. Otwórz pulpit nawigacyjny wskaźników **platformy Azure** .
2. W obszarze **Konfiguracja**wybierz pozycję **elementy PlayBook**.

   ![Aplikacja logiki](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Na stronie **wskaźnik na platformie Azure — elementy PlayBook** kliknij przycisk **Dodaj** .

   ![Tworzenie aplikacji logiki](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Na stronie **Tworzenie aplikacji logiki** wpisz żądane informacje, aby utworzyć nową aplikację logiki, a następnie kliknij przycisk **Utwórz**. 

5. W [**Projektancie aplikacji logiki**](../logic-apps/logic-apps-overview.md)wybierz szablon, którego chcesz użyć. W przypadku wybrania szablonu wymagającego poświadczeń należy je udostępnić. Alternatywnie można utworzyć nową pustą element PlayBook od podstaw. Wybierz pozycję **pusta aplikacja logiki**. 

   ![Zrzut ekranu przedstawiający panel pustej aplikacji logiki.](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. Nastąpi przekierowanie do projektanta aplikacji logiki, w którym można utworzyć nowy lub edytować szablon. Aby uzyskać więcej informacji na temat tworzenia element PlayBook z [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Jeśli tworzysz puste element PlayBook, w polu **Przeszukaj wszystkie łączniki i wyzwalacze** wpisz *Azure*, a następnie wybierz, **kiedy zostanie wyzwolona odpowiedź na alert na platformie Azure**. <br>Po utworzeniu nowego element PlayBook pojawia się na liście **elementy PlayBook** . Jeśli nie jest wyświetlany, kliknij przycisk **Odśwież**.

1. Użyj funkcji **Pobierz jednostki** , które umożliwiają uzyskanie odpowiednich jednostek z listy **jednostek** , takich jak konta, adresy IP i hosty. Umożliwi to uruchamianie akcji na określonych jednostkach.

7. Teraz możesz zdefiniować zachowanie po wyzwoleniu elementu playbook. Można dodać akcję, warunek logiczny, warunki przełączenia lub pętle.

   ![Projektant aplikacji logiki](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Jak uruchomić element PlayBook zabezpieczeń

Można uruchomić element PlayBook na żądanie.

Aby uruchomić element PlayBook na żądanie:

1. Na stronie **incydenty** wybierz zdarzenie i kliknij pozycję **Wyświetl pełne szczegóły**.

2. Na karcie **alerty** Kliknij alert, na którym chcesz uruchomić element PlayBook, a następnie przewiń w prawo, a następnie kliknij pozycję **Wyświetl elementy PlayBook** i wybierz element PlayBook do **uruchomienia** z listy dostępnych elementy PlayBook w subskrypcji. 



## <a name="automate-threat-responses"></a>Automatyzowanie odpowiedzi na zagrożenia

Zespoły SIEM/SOC mogą regularnie zasypaniu z alertami zabezpieczeń. Wygenerowane alerty są bardzo ogromne, a dostępni administratorzy zabezpieczeń są zapychani. W wyniku tego często zdarza się, że nie można zbadać wielu alertów, pozostawiając organizację narażoną na ataki, które nie są zauważalne. 

Wiele, jeśli nie większość z tych alertów, jest zgodny ze wzorcami cyklicznymi, które mogą być rozkierowane przez określone i zdefiniowane akcje naprawcze. Na platformie Azure wskaźnikiem można już definiować korygowanie w elementy PlayBook. Istnieje również możliwość ustawienia automatyzacji w czasie rzeczywistym jako części definicji element PlayBook, aby umożliwić pełne Automatyzowanie zdefiniowanej odpowiedzi dla konkretnych alertów zabezpieczeń. Korzystając z automatyzacji w czasie rzeczywistym, zespoły odpowiedzi mogą znacząco ograniczyć obciążenie, w pełni automatyzując rutynowe odpowiedzi na cykliczne typy alertów, co pozwala skoncentrować się na unikatowych alertach, analizowanie wzorców, łowiectwu zagrożeń i nie tylko.

Aby zautomatyzować odpowiedzi:

1. Wybierz Alert, dla którego chcesz zautomatyzować odpowiedź.
1. Na stronie **Edytowanie reguły alertu** w obszarze **Automatyzacja w czasie rzeczywistym**wybierz **wyzwolone element PlayBook** , które chcesz uruchomić po dopasowaniu tej reguły alertu.
1. Wybierz pozycję **Zapisz**.

   ![Automatyzacja w czasie rzeczywistym](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób uruchamiania element PlayBook na platformie Azure. Przejdź do strony z [możliwością aktywnego wyszukiwania w poszukiwaniu zagrożeń](hunting.md) przy użyciu platformy Azure.


