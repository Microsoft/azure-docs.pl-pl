---
title: Tworzenie przeglądu dostępu grup w & — Azure AD
description: Dowiedz się, jak utworzyć przegląd dostępu członków grupy lub dostępu do aplikacji w Azure Active Directory przeglądu dostępu.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 3/3/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4d6502ffdd13272d396852b11a11d13f929b11b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532267"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Tworzenie przeglądu dostępu grup i aplikacji w przeglądach dostępu usługi Azure AD

Dostęp do grup i aplikacji dla pracowników i gości zmienia się w czasie. Aby zmniejszyć ryzyko związane z nieodświeżonymi przypisaniami dostępu, administratorzy mogą używać usługi Azure Active Directory (Azure AD) do tworzenia przeglądów dostępu dla członków grupy lub dostępu do aplikacji. Jeśli musisz regularnie przeglądać dostęp, możesz również utworzyć cykliczne przeglądy dostępu. Aby uzyskać więcej informacji na temat tych scenariuszy, zobacz [Zarządzanie dostępem użytkowników](manage-user-access-with-access-reviews.md) i [Zarządzanie dostępem gości.](manage-guest-access-with-access-reviews.md)

Możesz obejrzeć krótkie wideo dotyczące włączania przeglądów dostępu:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

W tym artykule opisano sposób tworzenia jednego lub większej liczby przeglądów dostępu dla członków grupy lub dostępu do aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Usługa Azure AD — wersja Premium P2
- administrator globalny lub administrator użytkowników

Aby uzyskać więcej informacji, zobacz [Wymagania licencyjne.](access-reviews-overview.md#license-requirements)

## <a name="create-one-or-more-access-reviews"></a>Tworzenie co najmniej jednego przeglądu dostępu

1. Zaloguj się do witryny Azure Portal i otwórz stronę [Identity Governance (Nadzór nad tożsamościami).](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)

2. W menu po lewej stronie kliknij pozycję **Przeglądy dostępu.**

3. Kliknij **pozycję Nowy przegląd dostępu,** aby utworzyć nowy przegląd dostępu.

    ![Okienko przeglądów dostępu w łasuchach zarządzania tożsamościami](./media/create-access-review/access-reviews.png)

4. W **kroku 1. Wybierz, co należy przejrzeć,** aby wybrać zasób, który chcesz przejrzeć.

    ![Tworzenie przeglądu dostępu — nazwa i opis przeglądu](./media/create-access-review/select-what-review.png)

5. Jeśli w kroku 1 **wybrano** opcję Zespoły i grupy, w kroku 2 dostępne są dwie opcje
   - **Wszystkie Microsoft 365 grup z użytkownikami-gośćmi.** Wybierz tę opcję, jeśli chcesz tworzyć cykliczne przeglądy dla wszystkich użytkowników-gości we wszystkich grupach aplikacji Microsoft Teams i M365 w organizacji. Możesz wykluczyć niektóre grupy, klikając pozycję "Wybierz grupy do wykluczenia".
   - **Wybierz pozycję Zespoły i grupy.** Wybierz tę opcję, jeśli chcesz określić skończony zestaw zespołów i/lub grup do przejrzenia. Po kliknięciu tej opcji zostanie wyświetlona lista grup po prawej stronie do wyboru.

     ![Zespoły i grupy](./media/create-access-review/teams-groups.png)

     ![Zespoły i grupy wybrane w interfejsie użytkownika](./media/create-access-review/teams-groups-detailed.png)

6. Jeśli w **kroku** 1 wybrano pozycję Aplikacje, w kroku 2 można wybrać jedną lub więcej aplikacji.

    >[!NOTE]
    > Wybranie wielu grup i/lub aplikacji spowoduje, że zostanie utworzonych wiele przeglądów dostępu. Jeśli na przykład wybierzesz 5 grup do przejrzenia, spowoduje to 5 oddzielnych przeglądów dostępu

   ![Interfejs wyświetlany, jeśli wybrano aplikacje, a nie grupy](./media/create-access-review/select-application-detailed.png)

7. Następnie w kroku 3 możesz wybrać zakres do przeglądu. Dostępne opcje to:
   - **Tylko użytkownicy-goście.** Wybranie tej opcji ogranicza przegląd dostępu tylko do użytkowników-gości usługi Azure AD B2B w katalogu.
   - **Wszyscy.** Wybranie tej opcji powoduje zakres przeglądu dostępu do wszystkich obiektów użytkownika skojarzonych z zasobem.

    >[!NOTE]
    > Jeśli w kroku 2 wybrano opcję Wszystkie Microsoft 365 z użytkownikami-gośćmi, jedyną opcją jest przejrzenie użytkowników-gości w kroku 3

8. Kliknij pozycję Dalej: Przeglądy
9. W sekcji **Wybieranie recenzentów** wybierz jedną lub więcej osób, aby przeprowadzić przeglądy dostępu. Można wybrać jedną z następujących opcji:
    - **Właściciele grupy (dostępni** tylko podczas przeprowadzania przeglądu dla zespołu lub grupy)
    - **Wybrani użytkownik lub grupy**
    - **Użytkownicy przeglądają własny dostęp**
    - **Menedżerowie użytkowników.**
    Jeśli wybierzesz **opcję Menedżerowie użytkowników lub** **Właściciele**  grupy, możesz również określić rezerwowego recenzenta. Recenzentzy rezerwowi są proszeni o przejrzenie, gdy użytkownik nie ma określonego menedżera w katalogu lub grupa nie ma właściciela.

    ![nowy przegląd dostępu](./media/create-access-review/new-access-review.png)

10. W **sekcji Określ cykl przeglądu** można określić częstotliwość, taką jak Co tydzień, Co miesiąc, Co **kwartał, Półroczny, Rocznie.** Następnie należy określić czas **trwania**, który definiuje, jak długo będzie można otworzyć przegląd dla danych wejściowych od recenzentów. Na przykład maksymalny czas trwania, który można ustawić dla comiesięcznego przeglądu, wynosi 27 dni, aby uniknąć nakładających się przeglądów. Możesz skrócić czas trwania, aby zapewnić wcześniejsze zastosowanie danych wejściowych recenzentów. Następnie możesz wybrać **datę rozpoczęcia i** **datę zakończenia**.

    ![Wybierz, jak często ma nastąpić przegląd](./media/create-access-review/frequency.png)

11. Kliknij przycisk **Dalej: Ustawienia** w dolnej części strony.
12. W **ustawieniach Po zakończeniu** możesz określić, co się stanie po zakończeniu przeglądu

    ![Tworzenie przeglądu dostępu — ustawienia po zakończeniu](./media/create-access-review/upon-completion-settings-new.png)

Jeśli chcesz automatycznie usunąć dostęp dla użytkowników, którym odmówiono dostępu, ustaw opcję Automatycznie zastosuj wyniki do zasobu na wartość Włącz. Jeśli chcesz ręcznie zastosować wyniki po zakończeniu przeglądu, ustaw przełącznik na wartość Wyłącz.
Użyj listy Jeśli recenzentzy nie odpowiadają, aby określić, co się dzieje z użytkownikami, którzy nie są przeglądani przez recenzenta w okresie przeglądu. To ustawienie nie ma wpływu na użytkowników przeglądanych ręcznie przez recenzentów. Jeśli ostateczna decyzja recenzenta to Odmów, dostęp użytkownika zostanie usunięty.

- **Brak zmiany** — nie zmieniaj dostępu użytkownika
- **Usuwanie dostępu** — usuwanie dostępu użytkownika
- **Zatwierdzanie dostępu** — zatwierdzanie dostępu użytkownika
- **Korzystanie z** zaleceń — skorzystaj z zalecenia systemu dotyczącego odmowy lub zatwierdzenia dalszego dostępu użytkownika

    ![Opcje ustawień po zakończeniu](./media/create-access-review/upon-completion-settings-new.png)

Użyj akcji do zastosowania w przypadku odrzuconych **użytkowników-gości,** aby określić, co się stanie z użytkownikami-gośćmi w przypadku odmowy dostępu.
- Usunięcie członkostwa użytkownika z zasobu spowoduje usunięcie odrzuconego dostępu użytkownika do przeglądanych grup lub aplikacji. Będzie on nadal mógł zalogować się do dzierżawy.
- Zablokuj logowanie użytkownika przez 30 dni, a następnie usuń użytkownika z dzierżawy, aby zablokować odmówienie użytkownikom logowania się do dzierżawy, niezależnie od tego, czy mają dostęp do innych zasobów. Jeśli popełniono błąd lub jeśli administrator zdecyduje się ponownie włączyć dostęp, może to zrobić w ciągu 30 dni od wyłączenia użytkownika. Jeśli na wyłączonych użytkownikach nie zostaną wykonane żadne działania, zostaną oni usunięci z dzierżawy.

Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących usuwania użytkowników-gości, którzy nie mają już dostępu do zasobów w organizacji, przeczytaj artykuł zatytułowany Używanie usługi Azure AD Identity Governance do przeglądania i usuwania użytkowników zewnętrznych, którzy już nie mają dostępu do [zasobów.](access-reviews-external-users.md)

   >[!NOTE]
   >Akcji do zastosowania w przypadku odrzuconych użytkowników-gości nie można konfigurować dla przeglądów większej niż liczby użytkowników-gości. Nie można go również konfigurować w przypadku przeglądów **wszystkich grup M365 z użytkownikami-gośćmi.** Jeśli nie można skonfigurować, opcja domyślna usuwania członkostwa użytkownika z zasobu jest używana dla odrzuconych użytkowników.

13. W **pomocnikach dotyczących włączania weryfikacji** zdecyduj, czy recenzent ma otrzymywać rekomendacje podczas procesu przeglądu.

    ![Włączanie opcji pomocników podejmowania decyzji](./media/create-access-review/helpers.png)

14. W **sekcji Ustawienia zaawansowane** możesz wybrać następujące opcje:
    - Ustaw **wartość Wymagane uzasadnienie** na wartość **Włącz,** aby wymagać od recenzenta uzasadnienia zatwierdzenia.
    - Ustaw opcję  **Powiadomienia e-mail** na wartość Włącz, aby usługa Azure AD wysyłała powiadomienia e-mail do recenzentów po zakończeniu przeglądu dostępu oraz administratorom po zakończeniu przeglądu.
    - Ustaw **opcję Przypomnienia na** wartość **Włącz,** aby usługa Azure AD wysyłała przypomnienia o przeglądach dostępu w toku do recenzentów, którzy nie ukończyli przeglądu. Przypomnienia te będą samodzielne w połowie czasu trwania przeglądu.
    - Zawartość wiadomości e-mail wysyłanych do recenzentów jest generowana automatycznie na podstawie szczegółów recenzji, takich jak nazwa recenzji, nazwa zasobu, termin itp. Jeśli potrzebujesz sposobu przekazywania dodatkowych informacji, takich jak dodatkowe instrukcje lub  informacje kontaktowe, możesz określić te szczegóły w sekcji Dodatkowa zawartość dla poczty e-mail recenzenta. Podane informacje są zawarte w wiadomościach e-mail z zaproszeniem i przypomnieniami wysyłanych do przypisanych recenzentów. Sekcja wyróżniona na poniższej ilustracji pokazuje, gdzie są wyświetlane te informacje.


      ![dodatkowa zawartość dla recenzenta](./media/create-access-review/additional-content-reviewer.png)

15. Kliknij pozycję **Dalej: Przeglądanie + tworzenie,** aby przejść do następnej strony
16. Nazwij przegląd dostępu. Opcjonalnie nadaj przeglądowi opis. Nazwa i opis są wyświetlane recenzentom.
17. Przejrzyj informacje i wybierz pozycję **Utwórz**

       ![ekran tworzenia przeglądu](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>Rozpoczynanie przeglądu dostępu

Po podano ustawienia przeglądu dostępu, kliknij przycisk **Uruchom**. Przegląd dostępu zostanie wyświetlony na liście ze wskaźnikiem jej stanu.

![Lista przeglądów dostępu i ich stanu](./media/create-access-review/access-reviews-list.png)

Domyślnie usługa Azure AD wysyła wiadomość e-mail do recenzentów wkrótce po zakończeniu przeglądu. Jeśli nie chcesz, aby usługa Azure AD wysyłała wiadomość e-mail, pamiętaj, aby poinformować recenzentów, że przegląd dostępu oczekuje na ukończenie. Możesz wyświetlić im instrukcje dotyczące sposobu [przeglądania dostępu do grup lub aplikacji.](perform-access-review.md) Jeśli Twoja recenzja jest dla gości, aby przejrzeli własny dostęp, pokaż im instrukcje dotyczące sposobu przeglądania dostępu dla siebie [do grup lub aplikacji.](review-your-access.md)

Jeśli przypisano gości jako recenzentów i nie zaakceptowali oni zaproszenia, nie otrzymają oni wiadomości e-mail z przeglądów dostępu, ponieważ muszą najpierw zaakceptować zaproszenie przed sprawdzeniem.

## <a name="access-review-status-table"></a>Tabela stanu przeglądu dostępu

| Stan | Definicja |
|--------|------------|
|NotStarted | Przegląd został utworzony, odnajdywanie użytkowników oczekuje na rozpoczęcie. |
|Inicjowanie   | Trwa odnajdywanie użytkowników w celu zidentyfikowania wszystkich użytkowników, którzy są częścią przeglądu. |
|Uruchamianie | Przegląd jest rozpoczynany. Jeśli powiadomienia e-mail są włączone, wiadomości e-mail są wysyłane do recenzentów. |
|Ruch przychodzący | Przegląd został rozpoczęty. Jeśli powiadomienia e-mail są włączone, wiadomości e-mail zostały wysłane do recenzentów. Recenzentzy mogą przesyłać decyzje do terminu płatności. |
|Zakończeniu | Przegląd jest ukończony, a wiadomości e-mail są wysyłane do właściciela przeglądu. |
|Przeglądanie automatyczne | Przegląd jest na etapie przeglądu systemu. System rejestrowania decyzji dla użytkowników, którzy nie zostały przejmowane na podstawie zaleceń lub wstępnie skonfigurowanych decyzji. |
|Automatycznie przeglądane | Decyzje zostały zarejestrowane przez system dla wszystkich użytkowników, którzy nie zostali przejrzeni. Przegląd jest gotowy do kontynuowania **stosowania,** jeśli automatyczne stosowanie jest włączone. |
|Stosowania | Dostęp użytkowników zatwierdzonych nie zmieni się. |
|Zastosowano | Odmówiono dostępu użytkownikom, jeśli są, którzy są usunięci z zasobu lub katalogu. |
|Niepowodzenie | Nie można przejść przeglądu. Ten błąd może być związany z usunięciem dzierżawy, zmianą licencji lub innymi wewnętrznymi zmianami dzierżawy. |

## <a name="create-reviews-via-apis"></a>Tworzenie przeglądów za pośrednictwem interfejsów API

Przeglądy dostępu można również tworzyć przy użyciu interfejsów API. To, co należy zrobić, aby zarządzać przeglądami dostępu grup i użytkowników aplikacji w Azure Portal, można również wykonać przy użyciu Microsoft Graph API. Aby uzyskać więcej informacji, zobacz Azure [AD access reviews API reference (Dokumentacja](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)interfejsu API przeglądów dostępu usługi Azure AD). Aby uzyskać przykładowy kod, zobacz [Przykład pobierania przeglądów dostępu usługi Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)za pośrednictwem Microsoft Graph .

## <a name="next-steps"></a>Następne kroki

- [Przeglądanie dostępu do grup lub aplikacji](perform-access-review.md)
- [Przeglądanie dostępu dla siebie do grup lub aplikacji](review-your-access.md)
- [Ukończenie przeglądu dostępu grup lub aplikacji](complete-access-review.md)