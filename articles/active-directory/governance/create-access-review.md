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
ms.date: 12/07/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b12eb95a7840bdbb902701fc644eee30ffe9900f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778593"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu do grup i aplikacji w przeglądach dostępu usługi Azure AD

Dostęp do grup i aplikacji dla pracowników i Gości zmienia się wraz z upływem czasu. Aby zmniejszyć ryzyko związane ze starymi przypisaniami dostępu, Administratorzy mogą używać Azure Active Directory (Azure AD) do tworzenia przeglądów dostępu dla członków grupy lub dostępu do aplikacji. Jeśli musisz rutynowo przeglądać dostęp, możesz również utworzyć cykliczne przeglądy dostępu. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [Zarządzanie dostępem użytkowników](manage-user-access-with-access-reviews.md) i [Zarządzanie dostępem gościa](manage-guest-access-with-access-reviews.md).

Możesz obejrzeć szybkie rozmowy wideo dotyczące włączania przeglądów dostępu:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

W tym artykule opisano sposób tworzenia jednej lub kilku przeglądów dostępu dla członków grupy lub dostępu do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — wersja Premium P2
- Administrator globalny lub administrator użytkowników

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące licencji](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Utwórz co najmniej jedną weryfikację dostępu

1. Zaloguj się do Azure Portal i Otwórz [stronę zarządzania tożsamościami](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

2. W menu po lewej stronie kliknij pozycję **przeglądy dostępu**.

3. Kliknij kolejno pozycje **nowy przegląd dostępu** , aby utworzyć nowy przegląd dostępu.

    ![Okienko przeglądy dostępu w programie Zarządzanie tożsamościami](./media/create-access-review/access-reviews.png)

4. W **kroku 1: Wybierz, co chcesz przejrzeć** wybierz zasób, który chcesz przejrzeć.

    ![Tworzenie przeglądu dostępu — nazwa i opis przeglądu](./media/create-access-review/select-what-review.png)

5. Jeśli wybrano pozycję **zespoły + grupy** w kroku 1, dostępne są dwie opcje w kroku 2
   - **Wszystkie grupy Microsoft 365 z użytkownikami-gośćmi.** Wybierz tę opcję, jeśli chcesz utworzyć cykliczne przeglądy dla wszystkich użytkowników-Gości w ramach wszystkich zespołów firmy Microsoft i grup M365 w organizacji. Niektóre grupy można wykluczać, klikając pozycję "Wybierz grupy do wykluczenia".
   - **Wybierz pozycję zespoły + grupy.** Wybierz tę opcję, jeśli chcesz określić skończoną zbiór zespołów i/lub grup do przejrzenia. Po kliknięciu tej opcji zostanie wyświetlona lista grup z prawej strony do wybrania.

     ![Zespoły i grupy](./media/create-access-review/teams-groups.png)

     ![Zespoły i grupy wybrane w interfejsie użytkownika](./media/create-access-review/teams-groups-detailed.png)

6. W przypadku wybrania **aplikacji** w kroku 1 można wybrać jedną lub więcej aplikacji w kroku 2.

    >[!NOTE]
    > Wybranie wielu grup i/lub aplikacji spowoduje utworzenie wielu przeglądów dostępu. Na przykład, jeśli wybierzesz 5 grup do przejrzenia, spowoduje to 5 oddzielnych przeglądów dostępu

   ![Interfejs wyświetlany w przypadku wybrania aplikacji zamiast grup](./media/create-access-review/select-application-detailed.png)

7. Następnie w kroku 3 można wybrać zakres do przeglądu. Dostępne opcje to
   - **Tylko użytkownicy-Goście.** Wybranie tej opcji ogranicza dostęp do przeglądu dostępu tylko do użytkowników Gości usługi Azure AD w katalogu.
   - **Widzieć.** Wybranie tej opcji spowoduje przeprowadzenie przeglądu dostępu do wszystkich obiektów użytkownika skojarzonych z zasobem.

    >[!NOTE]
    > W przypadku wybrania opcji wszystkie grupy Microsoft 365 z użytkownikami Gości w kroku 2, jedyną opcją jest przeglądanie użytkowników-Gości w kroku 3.

8. Kliknij kolejno pozycje dalej: Recenzje
9. W sekcji **Wybieranie recenzentów** wybierz co najmniej jedną osobę, która ma wykonać przeglądy dostępu. Można wybrać jedną z następujących opcji:
    - **Właściciele grupy** (dostępne tylko podczas przeprowadzania przeglądu w zespole lub grupie)
    - **Wybrani użytkownicy lub grupy**
    - **Użytkownicy przeglądają własny dostęp**
    - **Przeglądania Menedżerowie użytkowników.**
    W przypadku wybrania opcji **menedżerowie użytkowników** lub **grup**  można także określić recenzenta powrotu. Recenzenci powrotu są proszeni o przeprowadzenie przeglądu, gdy użytkownik nie ma Menedżera określonego w katalogu lub grupa nie ma właściciela.

    ![nowy przegląd dostępu](./media/create-access-review/new-access-review.png)

10. W sekcji **Określ cykl przeglądu** można określić częstotliwość, na przykład **co tydzień, co miesiąc, co kwartał, co pół roku**. Następnie należy określić **czas trwania**, który definiuje, jak długo będzie otwarta Recenzja dla danych wejściowych od recenzentów. Na przykład maksymalny czas, który można ustawić dla comiesięcznego przeglądu wynosi 27 dni, aby uniknąć nakładających się przeglądów. Możesz skrócić czas trwania, aby upewnić się, że dane wejściowe recenzentów zostaną zastosowane wcześniej. Następnie można wybrać **datę początkową** i **datę końcową**.

    ![Określ, jak często ma być wykonywane przegląd](./media/create-access-review/frequency.png)

11. Kliknij przycisk **Dalej: ustawienia** w dolnej części strony.
12. W obszarze **Ustawienia ukończenia** możesz określić, co się dzieje po zakończeniu przeglądu

    ![Tworzenie przeglądu dostępu — po zakończeniu ustawień](./media/create-access-review/upon-completion-settings-new.png)

Jeśli chcesz automatycznie usunąć dostęp dla niedozwolonych użytkowników, ustaw opcję automatycznie Zastosuj wyniki do zasobu do włączenia. Jeśli chcesz ręcznie zastosować wyniki po zakończeniu przeglądu, ustaw przełącznik do wyłączenia.
Użyj listy if recenzenci nie odpowiadaj, aby określić, co się dzieje w przypadku użytkowników, którzy nie są recenzowani przez recenzenta w okresie przeglądu. To ustawienie nie ma wpływu na użytkowników, którzy zostali ręcznie przejrzał przez recenzentów. Jeśli ostateczna decyzja recenzenta jest odmowa, dostęp użytkownika zostanie usunięty.

- **Bez zmian — nie** zmieniaj dostępu użytkownika
- **Usuwanie dostępu** — Usuwanie dostępu użytkownika
- **Zatwierdzanie dostępu** — zatwierdzanie dostępu użytkownika
- Zapoznaj się z **zaleceniami** — zapoznaj się z zaleceniami systemu dotyczącymi odmowy lub zatwierdzenia ciągłego dostępu użytkownika

    ![Po zakończeniu opcji ustawień](./media/create-access-review/upon-completion-settings-new.png)

Użyj akcji, aby zastosować odrzuconych użytkowników- **Gości** , aby określić, co się dzieje z użytkownikami-Gośćmi, jeśli są odrzucane.
- Usunięcie członkostwa użytkownika z zasobu spowoduje usunięcie odmowy dostępu użytkownika do przeglądanej grupy lub aplikacji, nadal będzie można zalogować się do dzierżawy.
- Zablokuj użytkownikom możliwość logowania przez 30 dni, a następnie usunięcie użytkownika z dzierżawy spowoduje zablokowanie użytkownikom niezalogowanych użytkowników w dzierżawie, niezależnie od tego, czy mają dostęp do innych zasobów. Jeśli wystąpił błąd lub jeśli administrator zdecyduje się ponownie włączyć dostęp, może to zrobić w ciągu 30 dni od wyłączenia użytkownika. Jeśli nie wykonano żadnych akcji dla wyłączonych użytkowników, zostaną one usunięte z dzierżawy.

Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących usuwania użytkowników-Gości, którzy nie mają już dostępu do zasobów w organizacji, przeczytaj artykuł zatytułowany [używanie Azure AD Identity Governance do przeglądania i usuwania użytkowników zewnętrznych, którzy nie mają już dostępu do zasobów.](access-reviews-external-users.md)

   >[!NOTE]
   >Akcja, która ma zostać zastosowana w odniesieniu do użytkowników, których nie można skonfigurować, nie jest konfigurowalna na przeglądach należących do użytkowników Nie można również konfigurować dla przeglądów **wszystkich grup M365 z użytkownikami Gości.** Gdy nie jest możliwe do skonfigurowania, domyślna opcja usuwania członkostwa użytkownika z zasobu jest używana w przypadku zabronionych użytkowników.

13. W oknie **Włączanie decyzji dotyczącej przeglądu** wybierz, czy chcesz, aby recenzent otrzymał rekomendacje podczas procesu recenzowania.

    ![Włącz opcje pomocników decyzyjnych](./media/create-access-review/helpers.png)

14. W sekcji **Ustawienia zaawansowane** możesz wybrać następujące opcje:
    - Ustaw **uzasadnienie wymagane** w celu **umożliwienia** recenzentowi podania przyczyny zatwierdzenia.
    - Ustaw **powiadomienia e-mail** , aby **umożliwić** usłudze Azure AD wysyłanie powiadomień e-mail do recenzentów po rozpoczęciu przeglądu dostępu oraz do administratorów po zakończeniu przeglądu.
    - Ustaw **przypomnienia** , aby **umożliwić** usłudze Azure AD wysyłanie przypomnień o przeglądach dostępu w toku do recenzentów, którzy nie ukończyli swojego przeglądu. Te przypomnienia będą samoczynne w czasie trwania przeglądu.
    - Zawartość wiadomości e-mail wysyłanej do recenzentów jest generowana automatycznie na podstawie szczegółów przeglądu, takich jak nazwa przeglądu, nazwa zasobu, Data ukończenia itd. Jeśli potrzebujesz sposobu przekazywania dodatkowych informacji, takich jak dodatkowe instrukcje lub informacje kontaktowe, możesz określić te szczegóły w sekcji **dodatkowa zawartość dla recenzentów** . Wprowadzane informacje są zawarte w wiadomościach e-mail z zaproszeniem i przypomnieniem wysyłanym do przypisanych recenzentów. Sekcja wyróżniona na poniższej ilustracji pokazuje, gdzie są wyświetlane te informacje.


      ![Dodatkowa zawartość dla recenzenta](./media/create-access-review/additional-content-reviewer.png)

15. Kliknij przycisk **Dalej: Przejrzyj i Utwórz** , aby przejść do następnej strony
16. Nadaj nazwę przeglądowi dostępu. Opcjonalnie nadaj przeglądowi opis. Nazwa i opis są widoczne dla recenzentów.
17. Przejrzyj informacje i wybierz pozycję **Utwórz** .

       ![Utwórz ekran przeglądu](./media/create-access-review/create-review.png)

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
|Przeglądanie podwójne | Przegląd znajduje się na etapie recenzowania systemu. System rejestruje decyzje dla użytkowników, którzy nie zostali zrecenzowani w oparciu o zalecenia lub wstępnie skonfigurowane decyzje. |
|Zrecenzowane ponownie | Decyzje zostały zarejestrowane przez system dla wszystkich użytkowników, którzy nie zostali zrecenzowani. Przegląd jest gotowy do **dalszego zastosowania, jeśli włączono** funkcję autoapply. |
|Stosuje | Dostęp do użytkowników, którzy zostali zatwierdzeni, nie zmieni się. |
|Zastosowano | Odmowa dostępu użytkowników, jeśli istnieją, zostały usunięte z zasobu lub katalogu. |
|Niepowodzenie | Przeglądanie nie powiodło się. Ten błąd może być związany z usunięciem dzierżawy, zmianą licencji lub innymi wewnętrznymi zmianami dzierżawy. |

## <a name="create-reviews-via-apis"></a>Tworzenie przeglądów za pośrednictwem interfejsów API

Możesz również tworzyć przeglądy dostępu za pomocą interfejsów API. Aby zarządzać przeglądami dostępu dla grup i użytkowników aplikacji w Azure Portal, można również wykonać Microsoft Graph interfejsów API. Aby uzyskać więcej informacji, zobacz [Dokumentacja interfejsu API przeglądów dostępu usługi Azure AD](/graph/api/resources/accessreviews-root?view=graph-rest-beta). Aby uzyskać przykład kodu, zobacz [przykład pobierania przeglądów dostępu do usługi Azure AD za pośrednictwem Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie dostępu do grup lub aplikacji](perform-access-review.md)
- [Przejrzyj dostęp dla siebie do grup lub aplikacji](review-your-access.md)
- [Ukończ przegląd dostępu do grup lub aplikacji](complete-access-review.md)