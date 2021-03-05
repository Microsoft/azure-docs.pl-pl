---
title: Dokończ przegląd dostępu grup & aplikacji — Azure AD
description: Dowiedz się, jak wykonać przegląd dostępu członków grupy lub dostępu do aplikacji w Azure Active Directory przeglądy dostępu.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 02/08/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e31b9ce115ebe0723a9d082aaddd8056e486ed27
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176981"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Dokończ przegląd dostępu grup i aplikacji w przeglądach dostępu usługi Azure AD

Jako administrator [utworzysz przegląd dostępu do grup lub aplikacji](create-access-review.md) , a recenzenci [przeprowadzili przegląd dostępu](perform-access-review.md). W tym artykule opisano, jak wyświetlić wyniki przeglądu dostępu i zastosować wyniki.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — wersja Premium P2
- Administrator globalny, administrator użytkowników, administrator zabezpieczeń lub czytelnik zabezpieczeń

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące licencji](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Wyświetl przegląd dostępu

Postęp można śledzić, ponieważ recenzenci ukończyją swoje przeglądy.

1. Zaloguj się do Azure Portal i Otwórz [stronę zarządzania tożsamościami](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. W menu po lewej stronie kliknij pozycję **przeglądy dostępu**.

1. Na liście kliknij przegląd dostępu.

    Aby wyświetlić przyszłe wystąpienia przeglądów dostępu, przejdź do przeglądu dostępu i wybierz pozycję zaplanowane Recenzje.

    Na stronie **Przegląd** można zobaczyć postęp bieżącego wystąpienia. Żadne prawa dostępu nie są zmieniane w katalogu, dopóki nie zostanie ukończona Recenzja.

     ![Przegląd wszystkich grup firmy](./media/complete-access-review/all-company-group.png)

    Wszystkie bloki w obszarze Current są widoczne tylko w czasie trwania każdego wystąpienia przeglądu. 

    Strona wyniki zawiera więcej informacji na temat poszczególnych użytkowników w ramach przeglądu wystąpienia, w tym możliwość zatrzymywania, resetowania i pobierania wyników.

    ![Przeglądanie dostępu gościa w grupach Microsoft 365](./media/complete-access-review/all-company-group-results.png)


    Jeśli przeglądasz przegląd dostępu, który przegląda dostęp gościa w grupach Microsoft 365, w bloku przegląd zostanie wyświetlona lista wszystkich grup w przeglądzie. 
   
    ![Przeglądanie dostępu gościa w grupach Microsoft 365](./media/complete-access-review/review-guest-access-across-365-groups.png)

    Kliknij grupę, aby zobaczyć postęp przeglądu tej grupy, a także zatrzymywać, resetować, stosować i usuwać.

   ![szczegółowe przeglądanie dostępu gościa w grupach Microsoft 365](./media/complete-access-review/progress-group-review.png)

1. Jeśli chcesz zatrzymać przegląd dostępu przed osiągnięciem zaplanowanej daty zakończenia, kliknij przycisk **Zatrzymaj** .

    Po zatrzymaniu przeglądu recenzenci nie będą już mogli udzielić odpowiedzi. Po zatrzymaniu nie można ponownie uruchomić recenzji.

1. Jeśli nie interesuje Cię przegląd dostępu, możesz go usunąć, klikając przycisk **Usuń** .

## <a name="apply-the-changes"></a>Zastosuj zmiany

Jeśli **Funkcja automatycznego zastosowania do zasobu** została włączona na podstawie wybranych opcji w **ustawieniach ukończenia**, funkcja automatycznego zastosowania zostanie wykonana po dacie zakończenia przeglądu lub w przypadku ręcznego zatrzymania przeglądu.

Jeśli **Funkcja autoapply** nie została włączona na potrzeby przeglądu, przejdź do pozycji **Przejrzyj historię** w obszarze **Seria** po zakończeniu okresu przeglądu lub przegląd został zatrzymany wcześniej, a następnie kliknij wystąpienie przeglądu, które chcesz zastosować.

![Zastosuj zmiany przeglądu dostępu](./media/complete-access-review/apply-changes.png)

Kliknij przycisk **Zastosuj** , aby ręcznie zastosować zmiany. Jeśli w przeglądzie odmówiono dostępu użytkownikowi, po kliknięciu przycisku **Zastosuj** usługa Azure AD usunie przynależność lub przypisanie aplikacji.

![Przycisk Zastosuj zmiany przeglądu dostępu](./media/complete-access-review/apply-changes-button.png)


Stan przeglądu zostanie zmieniony z **ukończonej** przez Stany pośrednie, takie jak **stosowanie** i zakończenie do stanu **wyniku**. Należy się spodziewać, że w ciągu kilku minut będzie widocznych odrzuconych użytkowników (jeśli istnieją) z członkostwa w grupie lub przypisywania aplikacji.

Ręczne lub automatyczne stosowanie wyników nie ma wpływu na grupę, która pochodzi z katalogu lokalnego lub grupy dynamicznej. Jeśli chcesz zmienić grupę, która pochodzi z lokalizacji lokalnej, Pobierz wyniki i Zastosuj te zmiany do reprezentacji grupy w tym katalogu.

## <a name="retrieve-the-results"></a>Pobieranie wyników

Aby wyświetlić wyniki przeglądu dostępu jednorazowego, kliknij stronę **wyniki** . Aby wyświetlić tylko dostęp użytkownika, w polu wyszukiwania wpisz nazwę wyświetlaną lub nazwę główną użytkownika użytkownika, którego dostęp został sprawdzony.

![Pobierz wyniki przeglądu dostępu](./media/complete-access-review/retrieve-results.png) 

Aby wyświetlić postęp aktywnego przeglądu dostępu, który jest cykliczny, kliknij stronę **wyniki** .

Aby wyświetlić wyniki ukończonego wystąpienia przeglądu dostępu, który jest cykliczny, kliknij pozycję **Przejrzyj historię**, a następnie wybierz określone wystąpienie z listy zakończonych wystąpień przeglądu dostępu na podstawie daty rozpoczęcia i zakończenia wystąpienia. Wyniki tego wystąpienia można uzyskać ze strony **wyników** .

Aby pobrać wszystkie wyniki przeglądu dostępu, kliknij przycisk **Pobierz** . Otrzymany plik CSV można wyświetlić w programie Excel lub w innych programach, które otwierają pliki CSV kodowane w formacie UTF-8.

## <a name="remove-users-from-an-access-review"></a>Usuwanie użytkowników z przeglądu dostępu

 Domyślnie usunięty użytkownik pozostaje usunięty w usłudze Azure AD przez 30 dni i w tym okresie administrator może przywrócić go w razie potrzeby.  Po 30 dniach użytkownik jest trwale usuwany.  Ponadto przy użyciu portalu Azure Active Directory Administrator globalny może jawnie [usunąć niedawno usuniętego użytkownika](../fundamentals/active-directory-users-restore.md) przed osiągnięciem tego okresu.  Gdy użytkownik zostanie trwale usunięty, nowe dane dotyczące tego użytkownika będą usuwane z aktywnych przeglądów dostępu.  Informacje inspekcji dotyczące usuniętych użytkowników pozostają w dzienniku inspekcji.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem użytkowników za pomocą przeglądów dostępu w usłudze Azure AD](manage-user-access-with-access-reviews.md)
- [Zarządzanie dostępem gości za pomocą przeglądów dostępu w usłudze Azure AD](manage-guest-access-with-access-reviews.md)
- [Tworzenie przeglądu dostępu do grup lub aplikacji](create-access-review.md)
- [Tworzenie przeglądu dostępu użytkowników ról administracyjnych usługi Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
