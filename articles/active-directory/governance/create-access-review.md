---
title: Tworzenie przeglądu dostępu do grup & aplikacji — Azure AD
description: Dowiedz się, jak utworzyć przegląd dostępu dla członków grupy lub dostępu do aplikacji w Azure Active Directory przeglądy dostępu.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661339545f2c3f01ffd2e07b0fad08de8ac90278
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078875"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu do grup i aplikacji w przeglądach dostępu usługi Azure AD

Dostęp do grup i aplikacji dla pracowników i Gości zmienia się wraz z upływem czasu. Aby zmniejszyć ryzyko związane ze starymi przypisaniami dostępu, Administratorzy mogą używać Azure Active Directory (Azure AD) do tworzenia przeglądów dostępu dla członków grupy lub dostępu do aplikacji. Jeśli musisz rutynowo przeglądać dostęp, możesz również utworzyć cykliczne przeglądy dostępu. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [Zarządzanie dostępem użytkowników](manage-user-access-with-access-reviews.md) i [Zarządzanie dostępem gościa](manage-guest-access-with-access-reviews.md).

W tym artykule opisano sposób tworzenia jednej lub kilku przeglądów dostępu dla członków grupy lub dostępu do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — warstwa Premium P2
- Administrator globalny lub administrator użytkowników

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące licencji](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Utwórz co najmniej jedną weryfikację dostępu

1. Zaloguj się do Azure Portal i Otwórz [stronę zarządzania tożsamościami](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. W menu po lewej stronie kliknij pozycję **przeglądy dostępu**.

1. Kliknij kolejno pozycje **nowy przegląd dostępu** , aby utworzyć nowy przegląd dostępu.

    ![Okienko przeglądy dostępu w programie Zarządzanie tożsamościami](./media/create-access-review/access-reviews.png)

1. Nadaj nazwę przeglądowi dostępu. Opcjonalnie nadaj przeglądowi opis. Nazwa i opis są widoczne dla recenzentów.

    ![Tworzenie przeglądu dostępu — nazwa i opis przeglądu](./media/create-access-review/name-description.png)

1. Ustaw **datę początkową**. Domyślnie przegląd dostępu odbywa się raz, uruchamia się w tym samym czasie, który jest tworzony i upływa w ciągu miesiąca. Można zmienić datę początkową i końcową w taki sposób, aby przegląd dostępu został uruchomiony w przyszłości, a ostatni w ciągu kilku dni.

    ![Tworzenie przeglądu dostępu — daty rozpoczęcia i zakończenia](./media/create-access-review/start-end-dates.png)

1. Aby umożliwić cykliczne przeglądy dostępu, Zmień **ustawienie częstotliwości** z **jednego czasu** na **co tydzień**, **co miesiąc**, co **kwartał**, co **pół roku**lub **co rok**. Za pomocą suwaka **czas trwania** lub pola tekstowego Zdefiniuj, ile dni każdy przegląd cyklicznej serii będzie otwarty do wprowadzania danych od recenzentów. Na przykład maksymalny czas, który można ustawić dla comiesięcznego przeglądu wynosi 27 dni, aby uniknąć nakładających się przeglądów.

1. Użyj ustawienia **End** , aby określić, jak zakończyć cykliczne serie przeglądu dostępu. Seria może kończyć się na trzy sposoby: działa stale, aby rozpocząć przeglądy w nieskończoność, aż do określonej daty lub po zakończeniu zdefiniowanej liczby wystąpień. Po utworzeniu seria może zostać zatrzymana przez innego administratora lub innego administratora globalnego, zmieniając datę w **ustawieniach**, aby zakończyć działanie.

1. W sekcji **Użytkownicy** Określ użytkowników, których dotyczy przegląd dostępu. Przeglądy dostępu mogą dotyczyć członków grupy lub użytkowników, którzy zostali przypisani do aplikacji. Można dodatkowo określić zakres przeglądu dostępu, aby przeglądać tylko użytkowników-Gości, którzy są członkami (lub przypisani do aplikacji), a nie przeglądać wszystkich użytkowników, którzy są członkami lub którzy mają dostęp do aplikacji.

    ![Tworzenie przeglądu dostępu — użytkownicy](./media/create-access-review/users.png)

1. W sekcji **Grupa** wybierz co najmniej jedną grupę, do której chcesz przejrzeć członkostwo.

    > [!NOTE]
    > Wybranie więcej niż jednej grupy spowoduje utworzenie wielu przeglądów dostępu. Na przykład wybranie pięciu grup spowoduje utworzenie pięciu odrębnych przeglądów dostępu.
    
    ![Tworzenie przeglądu dostępu — Wybieranie grupy](./media/create-access-review/select-group.png)

1. W sekcji **aplikacje** (w przypadku wybrania opcji **przypisane do aplikacji** w kroku 8) wybierz aplikacje, do których chcesz przejrzeć dostęp.

    > [!NOTE]
    > Wybranie więcej niż jednej aplikacji spowoduje utworzenie wielu przeglądów dostępu. Na przykład wybranie pięciu aplikacji spowoduje utworzenie pięciu odrębnych przeglądów dostępu.
    
    ![Tworzenie przeglądu dostępu — Wybieranie aplikacji](./media/create-access-review/select-application.png)

1. W sekcji **recenzenci** wybierz co najmniej jedną osobę, aby przejrzeć wszystkich użytkowników w zakresie. Możesz również wybrać, aby członkowie mogli przeglądać swój własny dostęp. Jeśli zasób jest grupą, możesz poproszony właściciele grupy o przeglądanie. Można również wymagać od recenzentów podania przyczyny zatwierdzenia dostępu.

    ![Tworzenie przeglądu dostępu — recenzenci](./media/create-access-review/reviewers.png)

1. W sekcji **programy** wybierz program, którego chcesz użyć. **Program domyślny** jest zawsze obecny.

    ![Tworzenie przeglądu dostępu — programy](./media/create-access-review/programs.png)

    Można uprościć śledzenie i zbieranie przeglądów dostępu do różnych celów, organizując je w programy. Poszczególne przeglądy dostępu mogą być połączone z programem. Następnie podczas przygotowywania raportów dla audytora można skupić się na przeglądach dostępu w zakresie dla konkretnej inicjatywy. Wyniki przeglądu programów i dostępu są widoczne dla użytkowników w roli administratora globalnego, administratora użytkowników, administratora zabezpieczeń lub czytelnika zabezpieczeń.

    Aby wyświetlić listę programów, przejdź do strony przeglądy dostępu i wybierz pozycję **programy**. Jeśli jesteś administratorem globalnym lub rolą administratora użytkownika, możesz utworzyć dodatkowe programy. Na przykład można wybrać opcję posiadania jednego programu dla każdej inicjatywy zgodności lub celu prowadzenia działalności biznesowej. Jeśli program nie jest już potrzebny i nie ma żadnych kontrolek, można go usunąć.

### <a name="upon-completion-settings"></a>Po zakończeniu ustawień

1. Aby określić, co się stanie po zakończeniu przeglądu, rozwiń sekcję **po zakończeniu ustawień** .

    ![Tworzenie przeglądu dostępu — po zakończeniu ustawień](./media/create-access-review/upon-completion-settings.png)

1. Jeśli chcesz automatycznie usunąć, dostęp dla użytkowników, którym odmówiono, ustaw **Automatyczne stosowanie wyników do zasobu** do **włączenia**. Jeśli chcesz ręcznie zastosować wyniki po zakończeniu przeglądu, ustaw przełącznik do **wyłączenia**.

1. Użyj listy **powinien nie odpowiadać** , aby określić, co się dzieje w przypadku użytkowników, którzy nie są recenzowani przez recenzenta w okresie przeglądu. To ustawienie nie ma wpływu na użytkowników, którzy zostali ręcznie przejrzał przez recenzentów. Jeśli ostateczna decyzja recenzenta jest odmowa, dostęp użytkownika zostanie usunięty.

    - **Bez zmian — nie** zmieniaj dostępu użytkownika
    - **Usuwanie dostępu** — Usuwanie dostępu użytkownika
    - **Zatwierdzanie dostępu** — zatwierdzanie dostępu użytkownika
    - Zapoznaj się z **zaleceniami** — zapoznaj się z zaleceniami systemu dotyczącymi odmowy lub zatwierdzenia ciągłego dostępu użytkownika

### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. Aby określić dodatkowe ustawienia, rozwiń sekcję **Ustawienia zaawansowane** .

    ![Tworzenie przeglądu dostępu — Ustawienia zaawansowane](./media/create-access-review/advanced-settings.png)

1. Ustaw opcję **Pokaż zalecenia** do **włączenia** , aby wyświetlić recenzentów zalecenia systemowe na podstawie informacji o dostępie użytkownika.

1. Ustaw **przyczynę Wymagaj przy zatwierdzeniu** , aby **umożliwić** recenzentowi podanie przyczyny zatwierdzenia.

1. Ustaw **powiadomienia pocztą** , aby **umożliwić** usłudze Azure AD wysyłanie powiadomień e-mail do recenzentów po rozpoczęciu przeglądu dostępu oraz do administratorów po zakończeniu przeglądu.

1. Ustaw **przypomnienia** , aby **umożliwić** usłudze Azure AD wysyłanie przypomnień o przeglądach dostępu w toku do recenzentów, którzy nie ukończyli swojego przeglądu.

    Domyślnie usługa Azure AD automatycznie wysyła przypomnienia recenzentom, którzy jeszcze nie odpowiedzieli, po upływie połowy czasu przeznaczonego na przekazanie opinii.

## <a name="start-the-access-review"></a>Rozpocznij przegląd dostępu

Po określeniu ustawień przeglądu dostępu kliknij przycisk **Uruchom**. Przegląd dostępu zostanie wyświetlony na liście ze wskaźnikiem jego stanu.

![Lista przeglądów dostępu i ich stan](./media/create-access-review/access-reviews-list.png)

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów wkrótce po rozpoczęciu przeglądu. Jeśli nie chcesz, aby usługa Azure AD wysłała wiadomość e-mail, pamiętaj, aby poinformować recenzentów, że przegląd dostępu oczekuje na jego zakończenie. Można wyświetlić instrukcje dotyczące sposobu [przeglądania dostępu do grup lub aplikacji](perform-access-review.md). Jeśli Twoje przeglądy są przeznaczone dla Gości, aby zapoznać się z własnym dostępem, Pokaż im instrukcje dotyczące sposobu [przeglądania dostępu do grup lub aplikacji](review-your-access.md).

Jeśli przypisano Gości jako recenzentów, którzy nie zaakceptowali zaproszenia, nie otrzymają wiadomości e-mail od przeglądów dostępu, ponieważ najpierw muszą zaakceptować zaproszenie przed rozpoczęciem przeglądu.

## <a name="access-review-status-table"></a>Tabela stanu przeglądu dostępu

| Stan | Definicja |
|--------|------------|
|NotStarted | Przegląd został utworzony, odnajdywanie użytkowników oczekuje na uruchomienie. |
|Inicjowanie   | Odnajdywanie użytkowników jest w toku, aby zidentyfikować wszystkich użytkowników, którzy są częścią przeglądu. |
|Uruchamianie | Przegląd rozpoczyna się. Jeśli powiadomienia e-mail są włączone, wiadomości e-mail są wysyłane do recenzentów. |
|Toku | Przegląd został rozpoczęty. Powiadomienia e-mail, które zostały włączone, są wysyłane do recenzentów. Recenzenci mogą przesyłać decyzje do daty ukończenia. |
|Podając | Przegląd jest wykonywany i wiadomości e-mail są wysyłane do właściciela przeglądu. |
|Autoprzegląd | Przegląd znajduje się na etapie recenzowania systemu. System rejestruje decyzje dla użytkowników, którzy nie zostali zrecenzowani w oparciu o zalecenia lub wstępnie skonfigurowane decyzje. |
|Przejrzane ponownie | Decyzje zostały zarejestrowane przez system dla wszystkich użytkowników, którzy nie zostali zrecenzowani. Przegląd jest gotowy do **dalszego zastosowania, jeśli włączono** funkcję autoapply. |
|Stosuje | Dostęp do użytkowników, którzy zostali zatwierdzeni, nie zmieni się. |
|Zastosowano | Odmowa dostępu użytkowników, jeśli istnieją, zostały usunięte z zasobu lub katalogu. |

## <a name="create-reviews-via-apis"></a>Tworzenie przeglądów za pośrednictwem interfejsów API

Możesz również tworzyć przeglądy dostępu za pomocą interfejsów API. Aby zarządzać przeglądami dostępu dla grup i użytkowników aplikacji w Azure Portal, można również wykonać Microsoft Graph interfejsów API. Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API przeglądów dostępu usługi Azure AD](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). Aby uzyskać przykład kodu, zobacz [przykład pobierania przeglądów dostępu do usługi Azure AD za pośrednictwem Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie dostępu do grup lub aplikacji](perform-access-review.md)
- [Przejrzyj dostęp dla siebie do grup lub aplikacji](review-your-access.md)
- [Ukończ przegląd dostępu do grup lub aplikacji](complete-access-review.md)
