---
title: Tworzenie przeglądu dostępu do ról zasobów platformy Azure w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak utworzyć przegląd dostępu ról zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c0ce72348f67c22759915a3a15c69193ad2f60
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552799"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Tworzenie przeglądu dostępu do ról zasobów platformy Azure w Privileged Identity Management

Konieczność uzyskiwania dostępu do uprzywilejowanych ról zasobów platformy Azure przez pracowników zmienia się w czasie. Aby zmniejszyć ryzyko związane ze starymi przypisaniami ról, należy regularnie przeglądać dostęp. Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można tworzyć przeglądy dostępu dla uprzywilejowanego dostępu do ról zasobów platformy Azure. Istnieje również możliwość skonfigurowania cyklicznych przeglądów dostępu, które są wykonywane automatycznie. W tym artykule opisano sposób tworzenia jednej lub kilku przeglądów dostępu.

## <a name="prerequisite-license"></a>Wstępnie wymagana licencja

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]. Aby uzyskać więcej informacji o licencjach dla usługi PIM, zapoznaj się z [wymaganiami dotyczącymi licencji, aby użyć Privileged Identity Management](subscription-requirements.md).

> [!Note]
>  Obecnie można ograniczyć przegląd dostępu do jednostek usługi przy użyciu dostępu do usługi Azure AD i ról zasobów platformy Azure (wersja zapoznawcza) z aktywną Azure Active Directory — wersja Premium P2 w dzierżawie. Model licencjonowania dla jednostek usługi zostanie sfinalizowany do ogólnej dostępności tej funkcji, a dodatkowe licencje mogą być wymagane.

## <a name="prerequisite-role"></a>Rola wymagana wstępnie

 Aby utworzyć przeglądy dostępu, musisz być przypisany do roli platformy Azure [właściciela](../../role-based-access-control/built-in-roles.md#owner) lub [dostępu użytkownika](../../role-based-access-control/built-in-roles.md#user-access-administrator) dla zasobu.

## <a name="open-access-reviews"></a>Otwarte przeglądy dostępu

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest przypisany do jednej z ról wymaganych wstępnie.

1. Wybierz **Zarządzanie tożsamościami**
 
1. W menu po lewej stronie wybierz pozycję **zasoby platformy Azure** w obszarze **Azure AD Privileged Identity Management**.

1. Wybierz zasób, którym chcesz zarządzać, na przykład subskrypcję.

1. W obszarze Zarządzaj wybierz pozycję **przeglądy dostępu**.

    ![Zasoby platformy Azure — lista przeglądów dostępu pokazująca stan wszystkich przeglądów](./media/pim-resource-roles-start-access-review/access-reviews.png)

1. Kliknij pozycję **Nowy** , aby utworzyć nowy przegląd dostępu.

1. Nadaj nazwę przeglądowi dostępu. Opcjonalnie nadaj przeglądowi opis. Nazwa i opis są widoczne dla recenzentów.

    ![Tworzenie przeglądu dostępu — nazwa i opis przeglądu](./media/pim-resource-roles-start-access-review/name-description.png)

1. Ustaw **datę początkową**. Domyślnie przegląd dostępu odbywa się raz, uruchamia się w tym samym czasie, który jest tworzony i upływa w ciągu miesiąca. Można zmienić datę początkową i końcową w taki sposób, aby przegląd dostępu został uruchomiony w przyszłości, a ostatni w ciągu kilku dni.

    ![Data rozpoczęcia, częstotliwość, czas trwania, koniec, liczba godzin i Data zakończenia](./media/pim-resource-roles-start-access-review/start-end-dates.png)

1. Aby umożliwić cykliczne przeglądy dostępu, Zmień **ustawienie częstotliwości** z **jednego czasu** na **co tydzień**, **co miesiąc**, co **kwartał**, co **rok, rocznie** lub **półroczj**. Za pomocą suwaka **czas trwania** lub pola tekstowego Zdefiniuj, ile dni każdy przegląd cyklicznej serii będzie otwarty do wprowadzania danych od recenzentów. Na przykład maksymalny czas, który można ustawić dla comiesięcznego przeglądu wynosi 27 dni, aby uniknąć nakładających się przeglądów.

1. Użyj ustawienia **End** , aby określić, jak zakończyć cykliczne serie przeglądu dostępu. Seria może kończyć się na trzy sposoby: działa stale, aby rozpocząć przeglądy w nieskończoność, aż do określonej daty lub po zakończeniu zdefiniowanej liczby wystąpień. Po utworzeniu seria może zostać zatrzymana przez innego administratora lub innego administratora globalnego, zmieniając datę w **ustawieniach**, aby zakończyć działanie.

1. W sekcji **Użytkownicy** wybierz zakres przeglądu. Aby przejrzeć użytkowników, wybierz opcję **Użytkownicy lub wybierz (wersja zapoznawcza) jednostki usługi** , aby przejrzeć konta maszyn z dostępem do roli platformy Azure.   

    ![Zakres użytkowników do przeglądania członkostwa w roli](./media/pim-resource-roles-start-access-review/users.png)


1. W obszarze **Przegląd członkostwa w roli** wybierz role platformy Azure, które mają być przeglądane. 

    > [!NOTE]
    > - Role wybrane w tym miejscu obejmują zarówno [role trwałe, jak i kwalifikujące się](../privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Wybranie więcej niż jednej roli spowoduje utworzenie wielu przeglądów dostępu. Na przykład wybranie pięciu ról spowoduje utworzenie pięciu odrębnych przeglądów dostępu.
    Jeśli tworzysz przegląd dostępu **ról usługi Azure AD**, poniżej przedstawiono przykład listy członkostwo.

    ![Przeglądanie okienka członkostwa lista ról usługi Azure AD, które można wybrać](./media/pim-resource-roles-start-access-review/review-membership.png)

    Jeśli tworzysz przegląd dostępu do **ról zasobów platformy Azure**, na poniższej ilustracji przedstawiono przykład listy członkostwo na stronie Przegląd.

    ![Przeglądanie okienka członkostwa — lista ról zasobów platformy Azure, które można wybrać](./media/pim-resource-roles-start-access-review/review-membership-azure-resource-roles.png)

1. W sekcji **recenzenci** wybierz co najmniej jedną osobę, aby przejrzeć wszystkich użytkowników. Możesz również wybrać, aby członkowie mogli przeglądać swój własny dostęp.

    ![Lista recenzentów wybranych użytkowników lub członków (własnych)](./media/pim-resource-roles-start-access-review/reviewers.png)

    - **Wybrani użytkownicy** — Użyj tej opcji, aby wyznaczyć określonego użytkownika w celu ukończenia przeglądu. Ta opcja jest dostępna niezależnie od zakresu przeglądu, a wybrani Recenzenci mogą przeglądać użytkowników i podmioty usługi. 
    - **Członkowie (własne)** — ta opcja umożliwia użytkownikom przeglądanie własnych przypisań ról. Ta opcja jest dostępna tylko wtedy, gdy przegląd został objęty zakresem **użytkownikom**.
    - **Manager** — Użyj tej opcji, aby Menedżer użytkownika mógł sprawdzić przypisanie roli. Ta opcja jest dostępna tylko wtedy, gdy przegląd został objęty zakresem **użytkownikom**. Po wybraniu Menedżera będzie również dostępna opcja określania recenzenta powrotu. Osoby dokonujące przeglądu awaryjnego są proszeni o sprawdzenie użytkownika, gdy użytkownik nie ma Menedżera określonego w katalogu. 

### <a name="upon-completion-settings"></a>Po zakończeniu ustawień

1. Aby określić, co się stanie po zakończeniu przeglądu, rozwiń sekcję **po zakończeniu ustawień** .

    ![Po zakończeniu ustawień do automatycznego zastosowania](./media/pim-resource-roles-start-access-review/upon-completion-settings.png)

1. Jeśli chcesz automatycznie usunąć dostęp dla użytkowników, których odmówiono, ustaw opcję **automatycznie Zastosuj wyniki do zasobu** do **włączenia**. Jeśli chcesz ręcznie zastosować wyniki po zakończeniu przeglądu, ustaw przełącznik do **wyłączenia**.

1. Użyj listy **powinien nie odpowiadać** , aby określić, co się dzieje w przypadku użytkowników, którzy nie są recenzowani przez recenzenta w okresie przeglądu. To ustawienie nie ma wpływu na użytkowników, którzy zostali ręcznie przejrzał przez recenzentów. Jeśli ostateczna decyzja recenzenta jest odmowa, dostęp użytkownika zostanie usunięty.

    - **Bez zmian — nie** zmieniaj dostępu użytkownika
    - **Usuwanie dostępu** — Usuwanie dostępu użytkownika
    - **Zatwierdzanie dostępu** — zatwierdzanie dostępu użytkownika
    - Zapoznaj się z **zaleceniami** — zapoznaj się z zaleceniami systemu dotyczącymi odmowy lub zatwierdzenia ciągłego dostępu użytkownika

### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. Aby określić dodatkowe ustawienia, rozwiń sekcję **Ustawienia zaawansowane** .

    ![Zaawansowane ustawienia dotyczące wyświetlania zaleceń, wymagania przyczyny dotyczącej zatwierdzenia, powiadomień pocztą i przypomnień](./media/pim-resource-roles-start-access-review/advanced-settings.png)

1. Ustaw opcję **Pokaż zalecenia** do **włączenia** , aby wyświetlić recenzentów zalecenia systemowe na podstawie informacji o dostępie użytkownika.

1. Ustaw **przyczynę Wymagaj przy zatwierdzeniu** , aby **umożliwić** recenzentowi podanie przyczyny zatwierdzenia.

1. Ustaw **powiadomienia pocztą** , aby **umożliwić** usłudze Azure AD wysyłanie powiadomień e-mail do recenzentów po rozpoczęciu przeglądu dostępu oraz do administratorów po zakończeniu przeglądu.

1. Ustaw **przypomnienia** , aby **umożliwić** usłudze Azure AD wysyłanie przypomnień o przeglądach dostępu w toku do recenzentów, którzy nie ukończyli swojego przeglądu.
1. Zawartość wiadomości e-mail wysyłanej do recenzentów jest generowana automatycznie na podstawie szczegółów przeglądu, takich jak nazwa przeglądu, nazwa zasobu, Data ukończenia itd. Jeśli potrzebujesz sposobu na przekazanie dodatkowych informacji, takich jak dodatkowe instrukcje lub informacje kontaktowe, możesz określić te szczegóły w **dodatkowej zawartości dla wiadomości e-mail recenzenta** , która zostanie uwzględniona w wiadomości e-mail z zaproszeniem i przypomnieniem wysyłanym do przypisanych recenzentów. W wyróżnionej sekcji poniżej znajduje się informacja, gdzie te informacje będą wyświetlane.

    ![Zawartość wiadomości e-mail wysyłanych do recenzentów z wyróżnionymi wyróżnieniami](./media/pim-resource-roles-start-access-review/email-info.png)

## <a name="start-the-access-review"></a>Rozpocznij przegląd dostępu

Po określeniu ustawień przeglądu dostępu kliknij przycisk **Uruchom**. Przegląd dostępu zostanie wyświetlony na liście ze wskaźnikiem jego stanu.

![Lista przeglądów dostępu pokazująca stan rozpoczętego przeglądu](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów wkrótce po rozpoczęciu przeglądu. Jeśli nie chcesz, aby usługa Azure AD wysłała wiadomość e-mail, pamiętaj, aby poinformować recenzentów, że przegląd dostępu oczekuje na jego zakończenie. Można wyświetlić instrukcje dotyczące sposobu [przeglądania dostępu do ról zasobów platformy Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Zarządzanie przeglądem dostępu

Postęp można śledzić, ponieważ recenzenci ukończyją przeglądy na stronie **Przegląd** przeglądu dostępu. Żadne prawa dostępu nie są zmieniane w katalogu, dopóki nie [zostanie ukończona Recenzja](pim-resource-roles-complete-access-review.md).

![Strona przeglądu dostępu przegląd przedstawia szczegóły przeglądu](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Jeśli jest to jednorazowe przegląd, po przekroczeniu okresu kontroli dostępu lub przejściu przez administratora do przeglądu dostępu wykonaj kroki opisane w temacie [Kończenie przeglądu dostępu ról zasobów platformy Azure](pim-resource-roles-complete-access-review.md) , aby zobaczyć i zastosować wyniki.  

Aby zarządzać serią przeglądów dostępu, przejdź do przeglądu dostępu i znajdziesz nadchodzące wystąpienia w zaplanowanych recenzjach, a następnie edytuj datę końcową lub Dodaj/Usuń recenzentów.

Po dokonaniu wyboru w **ustawieniach ukończenia** funkcja automatycznego zastosowania zostanie wykonana po zakończeniu przeglądu lub po ręcznym zatrzymaniu przeglądu. Stan przeglądu zmieni się z **zakończone** przez Stany pośrednie, takie jak **stosowanie** i ostatecznie do stanu **zastosowania**. Należy się spodziewać, że w ciągu kilku minut będzie widocznych odrzuconych użytkowników (jeśli istnieją).

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie dostępu do ról zasobów platformy Azure](pim-resource-roles-perform-access-review.md)
- [Ukończ przegląd dostępu do ról zasobów platformy Azure](pim-resource-roles-complete-access-review.md)
- [Tworzenie przeglądu dostępu do ról usługi Azure AD](pim-how-to-start-security-review.md)
