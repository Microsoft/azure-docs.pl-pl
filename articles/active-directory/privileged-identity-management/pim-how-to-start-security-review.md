---
title: Tworzenie przeglądu dostępu ról usługi Azure AD w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak utworzyć przegląd dostępu ról usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 3/16/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 310122177d4bd1603f5f498aa2a51620eeda4a20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592800"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Tworzenie przeglądu dostępu do ról usługi Azure AD w Privileged Identity Management

Aby zmniejszyć ryzyko związane ze starymi przypisaniami ról, należy regularnie przeglądać dostęp. Możesz użyć Azure AD Privileged Identity Management (PIM), aby utworzyć przeglądy dostępu dla ról uprzywilejowanych usługi Azure AD. Istnieje również możliwość skonfigurowania cyklicznych przeglądów dostępu, które są wykonywane automatycznie.

W tym artykule opisano sposób tworzenia co najmniej jednej oceny dostępu dla uprzywilejowanych ról usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

[Administrator ról uprzywilejowanych](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Otwarte przeglądy dostępu

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli administrator ról uprzywilejowanych.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. W obszarze Zarządzaj wybierz pozycję **przeglądy dostępu**, a następnie wybierz pozycję **Nowy**.

    ![Role usługi Azure AD — lista przeglądów dostępu przedstawiających stan wszystkich przeglądów](./media/pim-how-to-start-security-review/access-reviews.png)

Kliknij pozycję **Nowy** , aby utworzyć nowy przegląd dostępu.

1. Nadaj nazwę przeglądowi dostępu. Opcjonalnie nadaj przeglądowi opis. Nazwa i opis są widoczne dla recenzentów.

    ![Tworzenie przeglądu dostępu — nazwa i opis przeglądu](./media/pim-how-to-start-security-review/name-description.png)

1. Ustaw **datę początkową**. Domyślnie przegląd dostępu odbywa się raz, uruchamia się w tym samym czasie, który jest tworzony i upływa w ciągu miesiąca. Można zmienić datę początkową i końcową w taki sposób, aby przegląd dostępu został uruchomiony w przyszłości, a ostatni w ciągu kilku dni.

    ![Data rozpoczęcia, częstotliwość, czas trwania, koniec, liczba godzin i Data zakończenia](./media/pim-how-to-start-security-review/start-end-dates.png)

1. Aby umożliwić cykliczne przeglądy dostępu, Zmień **ustawienie częstotliwości** z **jednego czasu** na **co tydzień**, **co miesiąc**, co **kwartał**, co **rok, rocznie** lub **półroczj**. Za pomocą suwaka **czas trwania** lub pola tekstowego Zdefiniuj, ile dni każdy przegląd cyklicznej serii będzie otwarty do wprowadzania danych od recenzentów. Na przykład maksymalny czas, który można ustawić dla comiesięcznego przeglądu wynosi 27 dni, aby uniknąć nakładających się przeglądów.

1. Użyj ustawienia **End** , aby określić, jak zakończyć cykliczne serie przeglądu dostępu. Seria może kończyć się na trzy sposoby: działa stale, aby rozpocząć przeglądy w nieskończoność, aż do określonej daty lub po zakończeniu zdefiniowanej liczby wystąpień. Po utworzeniu seria może zostać zatrzymana przez innego administratora lub innego administratora globalnego, zmieniając datę w **ustawieniach**, aby zakończyć działanie.

1. W sekcji **Użytkownicy** wybierz co najmniej jedną rolę, w której chcesz przejrzeć członkostwo.

    ![Zakres użytkowników do przeglądania członkostwa w roli](./media/pim-how-to-start-security-review/users.png)

    > [!NOTE]
    > - Role wybrane w tym miejscu obejmują zarówno [role trwałe, jak i kwalifikujące się](../privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Wybranie więcej niż jednej roli spowoduje utworzenie wielu przeglądów dostępu. Na przykład wybranie pięciu ról spowoduje utworzenie pięciu odrębnych przeglądów dostępu.
    > - W przypadku ról z przypisanymi do nich grupami dostęp do każdej grupy połączonej z rolą objętą przeglądem zostanie przejrzany jako część przeglądu dostępu.
    Jeśli tworzysz przegląd dostępu **ról usługi Azure AD**, poniżej przedstawiono przykład listy członkostwo.

    ![Przeglądanie okienka członkostwa lista ról usługi Azure AD, które można wybrać](./media/pim-how-to-start-security-review/review-membership.png)

    Jeśli tworzysz przegląd dostępu do **ról zasobów platformy Azure**, na poniższej ilustracji przedstawiono przykład listy członkostwo na stronie Przegląd.

    ![Przeglądanie okienka członkostwa — lista ról zasobów platformy Azure, które można wybrać](./media/pim-how-to-start-security-review/review-membership-azure-resource-roles.png)

1. W sekcji **recenzenci** wybierz co najmniej jedną osobę, aby przejrzeć wszystkich użytkowników. Możesz również wybrać, aby członkowie mogli przeglądać swój własny dostęp.

    ![Lista recenzentów wybranych użytkowników lub członków (własnych)](./media/pim-how-to-start-security-review/reviewers.png)

    - **Wybrani użytkownicy** — Użyj tej opcji, jeśli nie wiesz, kto potrzebuje dostępu. Po wybraniu tej opcji można przypisać przegląd do właściciela zasobu lub Menedżera grupy, aby zakończyć.
    - **Członkowie (własne)** — ta opcja umożliwia użytkownikom przeglądanie własnych przypisań ról. Grupy przypisane do roli nie będą częścią przeglądu, gdy ta opcja jest zaznaczona.
    - **Manager** — Użyj tej opcji, aby Menedżer użytkownika mógł sprawdzić przypisanie roli. Po wybraniu Menedżera będzie również dostępna opcja określania recenzenta powrotu. Osoby dokonujące przeglądu awaryjnego są proszeni o sprawdzenie użytkownika, gdy użytkownik nie ma Menedżera określonego w katalogu. Grupy przypisane do roli będą przeglądane przez recenzenta powrotu, jeśli został wybrany. 

### <a name="upon-completion-settings"></a>Po zakończeniu ustawień

1. Aby określić, co się stanie po zakończeniu przeglądu, rozwiń sekcję **po zakończeniu ustawień** .

    ![Po zakończeniu ustawień do automatycznego zastosowania](./media/pim-how-to-start-security-review/upon-completion-settings.png)

1. Jeśli chcesz automatycznie usunąć dostęp dla użytkowników, których odmówiono, ustaw opcję **automatycznie Zastosuj wyniki do zasobu** do **włączenia**. Jeśli chcesz ręcznie zastosować wyniki po zakończeniu przeglądu, ustaw przełącznik do **wyłączenia**.

1. Użyj listy **powinien nie odpowiadać** , aby określić, co się dzieje w przypadku użytkowników, którzy nie są recenzowani przez recenzenta w okresie przeglądu. To ustawienie nie ma wpływu na użytkowników, którzy zostali ręcznie przejrzał przez recenzentów. Jeśli ostateczna decyzja recenzenta jest odmowa, dostęp użytkownika zostanie usunięty.

    - **Bez zmian — nie** zmieniaj dostępu użytkownika
    - **Usuwanie dostępu** — Usuwanie dostępu użytkownika
    - **Zatwierdzanie dostępu** — zatwierdzanie dostępu użytkownika
    - Zapoznaj się z **zaleceniami** — zapoznaj się z zaleceniami systemu dotyczącymi odmowy lub zatwierdzenia ciągłego dostępu użytkownika

### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. Aby określić dodatkowe ustawienia, rozwiń sekcję **Ustawienia zaawansowane** .

    ![Zaawansowane ustawienia dotyczące wyświetlania zaleceń, wymagania przyczyny dotyczącej zatwierdzenia, powiadomień pocztą i przypomnień](./media/pim-how-to-start-security-review/advanced-settings.png)

1. Ustaw opcję **Pokaż zalecenia** do **włączenia** , aby wyświetlić recenzentów zalecenia systemowe na podstawie informacji o dostępie użytkownika.

1. Ustaw **przyczynę Wymagaj przy zatwierdzeniu** , aby **umożliwić** recenzentowi podanie przyczyny zatwierdzenia.

1. Ustaw **powiadomienia pocztą** , aby **umożliwić** usłudze Azure AD wysyłanie powiadomień e-mail do recenzentów po rozpoczęciu przeglądu dostępu oraz do administratorów po zakończeniu przeglądu.

1. Ustaw **przypomnienia** , aby **umożliwić** usłudze Azure AD wysyłanie przypomnień o przeglądach dostępu w toku do recenzentów, którzy nie ukończyli swojego przeglądu.
1. Zawartość wiadomości e-mail wysyłanej do recenzentów jest generowana automatycznie na podstawie szczegółów przeglądu, takich jak nazwa przeglądu, nazwa zasobu, Data ukończenia itd. Jeśli potrzebujesz sposobu na przekazanie dodatkowych informacji, takich jak dodatkowe instrukcje lub informacje kontaktowe, możesz określić te szczegóły w **dodatkowej zawartości dla wiadomości e-mail recenzenta** , która zostanie uwzględniona w wiadomości e-mail z zaproszeniem i przypomnieniem wysyłanym do przypisanych recenzentów. W wyróżnionej sekcji poniżej znajduje się informacja, gdzie te informacje będą wyświetlane.

    ![Zawartość wiadomości e-mail wysyłanych do recenzentów z wyróżnionymi wyróżnieniami](./media/pim-how-to-start-security-review/email-info.png)

## <a name="start-the-access-review"></a>Rozpocznij przegląd dostępu

Po określeniu ustawień przeglądu dostępu wybierz pozycję **Rozpocznij**. Przegląd dostępu zostanie wyświetlony na liście ze wskaźnikiem jego stanu.

![Lista przeglądów dostępu pokazująca stan rozpoczęte przeglądy](./media/pim-how-to-start-security-review/access-reviews-list.png)

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów wkrótce po rozpoczęciu przeglądu. Jeśli nie chcesz, aby usługa Azure AD wysłała wiadomość e-mail, pamiętaj, aby poinformować recenzentów, że przegląd dostępu oczekuje na jego zakończenie. Można wyświetlić instrukcje dotyczące sposobu [przeglądania dostępu do ról usługi Azure AD](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Zarządzanie przeglądem dostępu

Postęp można śledzić, ponieważ recenzenci ukończyją przeglądy na stronie **Przegląd** przeglądu dostępu. Żadne prawa dostępu nie są zmieniane w katalogu, dopóki nie [zostanie ukończona Recenzja](pim-how-to-complete-review.md).

![Strona przeglądu dostępu przegląd przedstawia szczegóły przeglądu](./media/pim-how-to-start-security-review/access-review-overview.png)

Jeśli jest to jednorazowe przegląd, po przekroczeniu okresu kontroli dostępu lub przejściu przez administratora do przeglądu dostępu wykonaj kroki opisane w temacie [Kończenie przeglądu dostępu ról usługi Azure AD](pim-how-to-complete-review.md) , aby zobaczyć i zastosować wyniki.  

Aby zarządzać serią przeglądów dostępu, przejdź do przeglądu dostępu i znajdziesz nadchodzące wystąpienia w zaplanowanych recenzjach, a następnie edytuj datę końcową lub Dodaj/Usuń recenzentów.

Po dokonaniu wyboru w **ustawieniach ukończenia** funkcja automatycznego zastosowania zostanie wykonana po zakończeniu przeglądu lub po ręcznym zatrzymaniu przeglądu. Stan przeglądu zmieni się z **zakończone** przez Stany pośrednie, takie jak **stosowanie** i ostatecznie do stanu **zastosowania**. Należy się spodziewać, że w ciągu kilku minut będzie widocznych odrzuconych użytkowników (jeśli istnieją).

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie dostępu do ról usługi Azure AD](pim-how-to-perform-security-review.md)
- [Kończenie przeglądu dostępu do ról usługi Azure AD](pim-how-to-complete-review.md)
- [Tworzenie przeglądu dostępu do ról zasobów platformy Azure](pim-resource-roles-start-access-review.md)
