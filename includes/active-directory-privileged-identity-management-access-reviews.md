---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
author: ajburnle
ms.service: active-directory
ms.topic: include
ms.date: 12/07/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 9c91eb6cfa18c5302a83347f671e4552befcf3e2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623345"
---
## <a name="create-one-or-more-access-reviews"></a>Utwórz co najmniej jedną weryfikację dostępu

1. Kliknij pozycję **Nowy** , aby utworzyć nowy przegląd dostępu.

1. Nadaj nazwę przeglądowi dostępu. Opcjonalnie nadaj przeglądowi opis. Nazwa i opis są widoczne dla recenzentów.

    ![Tworzenie przeglądu dostępu — nazwa i opis przeglądu](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Ustaw **datę początkową**. Domyślnie przegląd dostępu odbywa się raz, uruchamia się w tym samym czasie, który jest tworzony i upływa w ciągu miesiąca. Można zmienić datę początkową i końcową w taki sposób, aby przegląd dostępu został uruchomiony w przyszłości, a ostatni w ciągu kilku dni.

    ![Data rozpoczęcia, częstotliwość, czas trwania, koniec, liczba godzin i Data zakończenia](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Aby umożliwić cykliczne przeglądy dostępu, Zmień **ustawienie częstotliwości** z **jednego czasu** na **co tydzień**, **co miesiąc**, co **kwartał**, co **rok, rocznie** lub **półroczj**. Za pomocą suwaka **czas trwania** lub pola tekstowego Zdefiniuj, ile dni każdy przegląd cyklicznej serii będzie otwarty do wprowadzania danych od recenzentów. Na przykład maksymalny czas, który można ustawić dla comiesięcznego przeglądu wynosi 27 dni, aby uniknąć nakładających się przeglądów.

1. Użyj ustawienia **End** , aby określić, jak zakończyć cykliczne serie przeglądu dostępu. Seria może kończyć się na trzy sposoby: działa stale, aby rozpocząć przeglądy w nieskończoność, aż do określonej daty lub po zakończeniu zdefiniowanej liczby wystąpień. Po utworzeniu seria może zostać zatrzymana przez innego administratora lub innego administratora globalnego, zmieniając datę w **ustawieniach**, aby zakończyć działanie.

1. W sekcji **Użytkownicy** wybierz co najmniej jedną rolę, w której chcesz przejrzeć członkostwo.

    ![Zakres użytkowników do przeglądania członkostwa w roli](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > - Role wybrane w tym miejscu obejmują zarówno [role trwałe, jak i kwalifikujące się](../articles/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Wybranie więcej niż jednej roli spowoduje utworzenie wielu przeglądów dostępu. Na przykład wybranie pięciu ról spowoduje utworzenie pięciu odrębnych przeglądów dostępu.
    > - W przypadku ról z przypisanymi do nich grupami dostęp do każdej grupy połączonej z rolą objętą przeglądem zostanie przejrzany jako część przeglądu dostępu.

    Jeśli tworzysz przegląd dostępu **ról usługi Azure AD**, poniżej przedstawiono przykład listy członkostwo.

    ![Przeglądanie okienka członkostwa lista ról usługi Azure AD, które można wybrać](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Jeśli tworzysz przegląd dostępu do **ról zasobów platformy Azure**, na poniższej ilustracji przedstawiono przykład listy członkostwo na stronie Przegląd.

    ![Przeglądanie okienka członkostwa — lista ról zasobów platformy Azure, które można wybrać](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. W sekcji **recenzenci** wybierz co najmniej jedną osobę, aby przejrzeć wszystkich użytkowników. Możesz również wybrać, aby członkowie mogli przeglądać swój własny dostęp.

    ![Lista recenzentów wybranych użytkowników lub członków (własnych)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Wybrani użytkownicy** — Użyj tej opcji, jeśli nie wiesz, kto potrzebuje dostępu. Po wybraniu tej opcji można przypisać przegląd do właściciela zasobu lub Menedżera grupy, aby zakończyć.
    - **Członkowie (własne)** — ta opcja umożliwia użytkownikom przeglądanie własnych przypisań ról. Grupy przypisane do roli nie będą częścią przeglądu, gdy ta opcja jest zaznaczona.
    - **Manager** — Użyj tej opcji, aby Menedżer użytkownika mógł sprawdzić przypisanie roli. Po wybraniu Menedżera będzie również dostępna opcja określania recenzenta powrotu. Osoby dokonujące przeglądu awaryjnego są proszeni o sprawdzenie użytkownika, gdy użytkownik nie ma Menedżera określonego w katalogu. Grupy przypisane do roli będą przeglądane przez recenzenta powrotu, jeśli został wybrany. 

### <a name="upon-completion-settings"></a>Po zakończeniu ustawień

1. Aby określić, co się stanie po zakończeniu przeglądu, rozwiń sekcję **po zakończeniu ustawień** .

    ![Po zakończeniu ustawień do automatycznego zastosowania](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Jeśli chcesz automatycznie usunąć dostęp dla użytkowników, których odmówiono, ustaw opcję **automatycznie Zastosuj wyniki do zasobu** do **włączenia**. Jeśli chcesz ręcznie zastosować wyniki po zakończeniu przeglądu, ustaw przełącznik do **wyłączenia**.

1. Użyj listy **powinien nie odpowiadać** , aby określić, co się dzieje w przypadku użytkowników, którzy nie są recenzowani przez recenzenta w okresie przeglądu. To ustawienie nie ma wpływu na użytkowników, którzy zostali ręcznie przejrzał przez recenzentów. Jeśli ostateczna decyzja recenzenta jest odmowa, dostęp użytkownika zostanie usunięty.

    - **Bez zmian — nie** zmieniaj dostępu użytkownika
    - **Usuwanie dostępu** — Usuwanie dostępu użytkownika
    - **Zatwierdzanie dostępu** — zatwierdzanie dostępu użytkownika
    - Zapoznaj się z **zaleceniami** — zapoznaj się z zaleceniami systemu dotyczącymi odmowy lub zatwierdzenia ciągłego dostępu użytkownika

### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. Aby określić dodatkowe ustawienia, rozwiń sekcję **Ustawienia zaawansowane** .

    ![Zaawansowane ustawienia dotyczące wyświetlania zaleceń, wymagania przyczyny dotyczącej zatwierdzenia, powiadomień pocztą i przypomnień](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Ustaw opcję **Pokaż zalecenia** do **włączenia** , aby wyświetlić recenzentów zalecenia systemowe na podstawie informacji o dostępie użytkownika.

1. Ustaw **przyczynę Wymagaj przy zatwierdzeniu** , aby **umożliwić** recenzentowi podanie przyczyny zatwierdzenia.

1. Ustaw **powiadomienia pocztą** , aby **umożliwić** usłudze Azure AD wysyłanie powiadomień e-mail do recenzentów po rozpoczęciu przeglądu dostępu oraz do administratorów po zakończeniu przeglądu.

1. Ustaw **przypomnienia** , aby **umożliwić** usłudze Azure AD wysyłanie przypomnień o przeglądach dostępu w toku do recenzentów, którzy nie ukończyli swojego przeglądu.
1. Zawartość wiadomości e-mail wysyłanej do recenzentów jest generowana automatycznie na podstawie szczegółów przeglądu, takich jak nazwa przeglądu, nazwa zasobu, Data ukończenia itd. Jeśli potrzebujesz sposobu na przekazanie dodatkowych informacji, takich jak dodatkowe instrukcje lub informacje kontaktowe, możesz określić te szczegóły w **dodatkowej zawartości dla wiadomości e-mail recenzenta** , która zostanie uwzględniona w wiadomości e-mail z zaproszeniem i przypomnieniem wysyłanym do przypisanych recenzentów. W wyróżnionej sekcji poniżej znajduje się informacja, gdzie te informacje będą wyświetlane.

    ![Zawartość wiadomości e-mail wysyłanych do recenzentów z wyróżnionymi wyróżnieniami](./media/active-directory-privileged-identity-management-access-reviews/email-info.png)