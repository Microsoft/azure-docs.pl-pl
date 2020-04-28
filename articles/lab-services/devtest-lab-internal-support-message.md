---
title: Dodaj wewnętrzną instrukcję obsługi do laboratorium w Azure DevTest Labs
description: Dowiedz się, jak opublikować wewnętrzną instrukcję obsługi w laboratorium w Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 591dcec36dc62143901d3b49db24196e84d58c29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76170352"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Dodaj wewnętrzną instrukcję obsługi do laboratorium w Azure DevTest Labs

Azure DevTest Labs umożliwia dostosowanie laboratorium przy użyciu wewnętrznej instrukcji obsługi, która zapewnia użytkownikom informacje o pomocy technicznej dotyczące laboratorium. Można na przykład podać informacje kontaktowe, aby użytkownik wie, jak uzyskać pomoc techniczną wewnętrzną, gdy potrzebują pomocy przy rozwiązywaniu problemów lub uzyskaniu dostępu do zasobów w laboratorium. Możesz również udostępniać linki do wewnętrznych witryn sieci Web lub często zadawane pytania, do których Twój zespół może uzyskać dostęp przed skontaktowaniem się z pomocą techniczną.

Wewnętrzna instrukcja obsługi ma na celu umożliwienie ogłaszania informacji laboratoryjnych, które zwykle nie zmieniają się zbyt często. Aby poinformować użytkowników o tym, że informacje dotyczące laboratorium mają więcej charakter tymczasowy, na przykład w przypadku najnowszych aktualizacji zasad laboratorium — zobacz [Publikowanie ogłoszeń w laboratorium](devtest-lab-announcements.md).

Możesz łatwo wyłączyć lub edytować instrukcję pomocy technicznej, gdy nie ma już zastosowania.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Procedura dodawania instrukcji obsługi do istniejącego laboratorium

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. W razie potrzeby wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** . (Laboratorium może być już wyświetlane na pulpicie nawigacyjnym w obszarze **wszystkie zasoby**).
1. Z listy laboratoriów wybierz laboratorium, w którym chcesz dodać instrukcję obsługi.  
1. W obszarze **Przegląd** laboratorium wybierz pozycję **Konfiguracja i zasady**.  

    ![Przycisk konfiguracji i zasad](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. W obszarze **Ustawienia**wybierz pozycję **Obsługa wewnętrzna**.

    ![Przycisk obsługi wewnętrznej](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Aby utworzyć wewnętrzny komunikat pomocy technicznej dla użytkowników w tym laboratorium, ustaw wartość włączone na **tak**.

1. W polu **komunikat pomocy technicznej** wprowadź wewnętrzną deklarację pomocy technicznej, którą chcesz przedstawić użytkownikom laboratorium. Komunikat pomocy technicznej akceptuje wartość z przedziału. Podczas wprowadzania tekstu wiadomości można wyświetlić obszar **podglądu** u dołu ekranu, aby zobaczyć, jak wiadomość jest widoczna dla użytkowników.

    ![Ekran obsługi wewnętrznej w celu utworzenia wiadomości.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Wybierz pozycję **Zapisz** , gdy instrukcja pomocy technicznej będzie gotowa do opublikowania.

Gdy nie chcesz już wyświetlać tego komunikatu o pomocy technicznej dla użytkowników laboratorium, Wróć do strony **pomocy technicznej wewnętrznej** **i ustaw wartość** **nie**.

## <a name="steps-for-users-to-view-the-support-message"></a>Kroki dla użytkowników umożliwiające wyświetlenie komunikatu o pomocy technicznej

1. Z [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)wybierz laboratorium.

1. W obszarze **Przegląd** laboratorium wybierz pozycję **Obsługa wewnętrzna**.  

    ![Przycisk obsługi wewnętrznej](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Jeśli zostanie ogłoszony komunikat pomocy technicznej, użytkownik może go wyświetlić w okienku pomocy technicznej wewnętrznej.

    ![Okienko obsługi wewnętrznej pokazujące ogłoszony komunikat pomocy technicznej](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
* Wewnętrzne instrukcje obsługi są zwykle używane do zapewnienia informacji pomocy technicznej, które często nie zmieniają się. Możesz również dowiedzieć się, jak [ogłosić ogłoszenie w laboratorium](devtest-lab-announcements.md) , aby poinformować użytkowników o tymczasowych zmianach lub aktualizacjach laboratorium.
* [Ustawianie zasad i harmonogramów](devtest-lab-set-lab-policy.md) zawiera informacje na temat sposobu stosowania innych ograniczeń i Konwencji w ramach subskrypcji przy użyciu zasad niestandardowych.