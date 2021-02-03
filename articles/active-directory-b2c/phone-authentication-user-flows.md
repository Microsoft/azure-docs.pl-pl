---
title: Konfigurowanie dostawcy tożsamości konta lokalnego
titleSuffix: Azure AD B2C
description: Zdefiniuj typy tożsamości, których możesz użyć (adres e-mail, nazwa użytkownika, numer telefonu) do uwierzytelniania konta lokalnego podczas konfigurowania przepływów użytkowników w dzierżawie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1ed0ca069f29413083356a3147d4b97f1b6ed981
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525199"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>Skonfiguruj rejestrację i logowanie za telefon na potrzeby przepływów użytkowników (wersja zapoznawcza)

> [!NOTE]
> Funkcje rejestracji i logowania na telefonie oraz w przypadku przepływów użytkowników są dostępne w publicznej wersji zapoznawczej.

Oprócz poczty e-mail i nazwy użytkownika można włączyć numer telefonu jako opcję rejestracji dla dzierżawy, dodając konto i logując się do lokalnego dostawcy tożsamości. Po włączeniu rejestracji i zalogowaniu się na kontach lokalnych możesz dodać do nich przepływy telefoniczne.

Konfigurowanie rejestracji i logowania za pomocą telefonu w przepływie użytkownika obejmuje następujące kroki:

- [Skonfiguruj dzierżawę i zaloguj](#configure-phone-sign-up-and-sign-in-tenant-wide) się na koncie w ramach dostawcy tożsamości konta lokalnego, aby akceptować numer telefonu jako tożsamość użytkownika. 

- [Dodaj konto telefoniczne do przepływu użytkownika](#add-phone-sign-up-to-a-user-flow) , aby umożliwić użytkownikom rejestrowanie się w aplikacji przy użyciu numeru telefonu.

- [Włącz monit dotyczący wiadomości e-mail z odzyskiwaniem (wersja zapoznawcza)](#enable-the-recovery-email-prompt-preview) , aby umożliwić użytkownikom określenie wiadomości e-mail, której można użyć do odzyskania konta, jeśli nie mają swojego telefonu.

Uwierzytelnianie wieloskładnikowe (MFA) jest domyślnie wyłączone podczas konfigurowania przepływu użytkownika przy użyciu usługi rejestracji na telefonie. Uwierzytelnianie wieloskładnikowe można włączyć w przepływach użytkownika przy użyciu rejestracji w telefonie, ale ponieważ numer telefonu jest używany jako identyfikator podstawowy, jedyną opcją dostępną dla drugiego czynnika uwierzytelniania jest adres e-mail.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>Skonfiguruj rejestrację i logowanie na telefonie — szerokie

Rejestracja w wiadomościach e-mail jest domyślnie włączona w ustawieniach dostawcy tożsamości konta lokalnego. Typy tożsamości, które będą obsługiwane w dzierżawie, można zmienić, zaznaczając lub usuwając zaznaczenie opcji Rejestracja w wiadomości e-mail, nazwa użytkownika lub numer telefonu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.

4. W obszarze **Zarządzaj** wybierz pozycję **dostawcy tożsamości**.

5. Na liście dostawca tożsamości wybierz pozycję **konto lokalne**.

   ![Dostawcy tożsamości wybierają konto lokalne](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. Na stronie **Konfiguruj lokalną dostawcy tożsamości** upewnij się, że wybrano pozycję **telefon** jako jeden z dozwolonych typów tożsamości, których użytkownicy mogą używać do tworzenia kont lokalnych w dzierżawie Azure AD B2C.

   ![Wybierz dozwolone typy tożsamości](media/phone-authentication-user-flows/configure-local-idp.png)

1. Wybierz pozycję **Zapisz**.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Dodawanie konta służbowego do przepływu użytkownika

Po dodaniu rejestracji za pomocą telefonu jako opcji tożsamości dla kont lokalnych możesz dodać ją do przepływów użytkowników, o ile są one najnowszymi **zalecanymi** wersjami przepływu użytkownika. Poniżej znajduje się przykład przedstawiający sposób dodawania rejestracji do nowego przepływu użytkownika. Można jednak również dodać rejestrację telefonu do istniejących, zalecanych przepływów użytkowników (wybierz **przepływy użytkownika**  >  *Nazwa przepływu użytkownika*  >    >  **konta lokalnego Rejestracja** dostawcy). 

Oto przykład przedstawiający sposób dodawania rejestracji do nowego przepływu użytkownika.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.

    ![B2C dzierżawy, katalogu i subskrypcji, Azure Portal](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
4. W obszarze **zasady** wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.

    ![Strona przepływy użytkownika w portalu z wyróżnionym przyciskiem nowy przepływ użytkownika](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. Na stronie **Tworzenie przepływu użytkownika** wybierz pozycję **zarejestruj się i zaloguj się** .

    ![Wybierz stronę przepływu użytkownika z wyróżnioną pozycją Rejestracja i przepływ logowania](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. W obszarze **Wybierz wersję** wybierz pozycję **zalecane**, a następnie wybierz pozycję **Utwórz**. ([Dowiedz się więcej](user-flow-versions.md) o wersjach przepływu użytkowników).

    ![Przycisk tworzenia przepływu użytkownika](./media/phone-authentication-user-flows/select-version.png)

7. Wprowadź **nazwę** przepływu użytkownika. Na przykład *signupsignin1*.
8. W sekcji **dostawcy tożsamości** w obszarze **konta lokalne** wybierz pozycję **rejestracja na telefonie**.

   ![Wybrana opcja rejestrowania przepływu użytkownika](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. W obszarze **dostawcy tożsamości społecznościowej** wybierz innych dostawców tożsamości, których chcesz zezwolić na ten przepływ użytkownika.

   > [!NOTE]
   > Uwierzytelnianie wieloskładnikowe (MFA) jest domyślnie wyłączone dla przepływów użytkowników logujących się. Usługę MFA można włączyć dla przepływu użytkownika w celu rejestracji telefonu, ale ponieważ numer telefonu jest używany jako identyfikator podstawowy, to jedyną opcją dostępną dla drugiego czynnika uwierzytelniania.

1. W sekcji **atrybuty użytkownika i oświadczenia tokenu** wybierz oświadczenia i atrybuty, które mają być zbierane i wysyłane przez użytkownika podczas tworzenia konta. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz atrybuty i oświadczenia dla **kraju/regionu**, **Nazwa wyświetlana** i **Kod pocztowy**. Wybierz przycisk **OK**.

1. Wybierz pozycję **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

## <a name="enable-the-recovery-email-prompt-preview"></a>Włącz monit dotyczący odzyskiwania wiadomości e-mail (wersja zapoznawcza)

Po włączeniu rejestracji i zalogowaniu się w telefonie dla użytkowników warto również włączyć funkcję poczty e-mail do odzyskiwania. Korzystając z tej funkcji, użytkownik może podać adres e-mail, którego można użyć do odzyskania konta, jeśli nie mają telefonu. Ten adres e-mail służy tylko do odzyskiwania konta. Nie można jej użyć do zalogowania się.

- Po wyświetleniu monitu **dotyczącego** wiadomości e-mail z odzyskiwaniem zostanie wyświetlony monit o zweryfikowanie wiadomości e-mail z kopią zapasową. Użytkownik, który nie dostarczył wiadomości e-mail z odzyskiwaniem, przed wyświetleniem monitu o zweryfikowanie wiadomości e-mail dotyczącej kopii zapasowej podczas kolejnego logowania.

- Po **wyłączeniu** wiadomości e-mail dotyczącej odzyskiwania nie jest wyświetlany monit dotyczący odzyskiwania wiadomości e-mail.
 
Możesz włączyć monit dotyczący odzyskiwania wiadomości e-mail we właściwościach przepływu użytkownika.

> [!NOTE]
> Przed rozpoczęciem upewnij się, że [dodano konto telefonu do przepływu użytkownika](#add-phone-sign-up-to-a-user-flow) zgodnie z powyższym opisem.

### <a name="to-enable-the-recovery-email-prompt"></a>Aby włączyć monit dotyczący odzyskiwania wiadomości e-mail

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
3. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
4. W Azure AD B2C w obszarze **zasady** wybierz pozycję **przepływy użytkownika**.
5. Wybierz przepływ użytkownika z listy.
6. W obszarze **Ustawienia** wybierz pozycję **Właściwości**.
7. Obok pozycji **Włącz monit e-mail dotyczącej odzyskiwania numeru telefonu i logowania (wersja zapoznawcza)** wybierz pozycję:

   - **W** celu wyświetlenia monitu o wiadomość e-mail z odzyskiwaniem podczas rejestracji i logowania.
   - **Aby ukryć** monit dotyczący odzyskiwania wiadomości e-mail.

    ![Właściwości przepływów użytkownika z włączoną opcją Włącz odzyskiwanie poczty e-mail](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. Wybierz pozycję **Zapisz**.

### <a name="to-test-the-recovery-email-prompt"></a>Aby przetestować monit dotyczący odzyskiwania wiadomości e-mail

Po włączeniu funkcji rejestracji i logowania za pomocą telefonu oraz monitu o odzyskanie wiadomości e-mail w przepływie użytkownika można testować środowisko użytkownika przy użyciu **przepływu pracy** .

1. Wybierz pozycję **zasady**  >  **przepływy użytkownika**, a następnie wybierz utworzony przepływ użytkownika. Na stronie Przegląd przepływu użytkownika wybierz pozycję **Uruchom przepływ użytkownika**.

2. W przypadku **aplikacji** wybierz aplikację sieci Web, która została zarejestrowana w kroku 1. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .

3. Wybierz pozycję **Uruchom przepływ użytkownika** i sprawdź następujące zachowanie:

   - Użytkownik, który zarejestruje się po raz pierwszy, zostanie poproszony o podanie wiadomości e-mail dotyczącej odzyskiwania. 
   - Użytkownik, który został już zarejestrowany, ale nie dostarczył wiadomości e-mail z odzyskiwaniem, jest proszony o podanie go podczas logowania.

4. Wprowadź adres e-mail, a następnie wybierz pozycję **Wyślij kod weryfikacyjny**. Sprawdź, czy kod jest wysyłany do podanej skrzynki odbiorczej poczty e-mail. Pobierz kod i wprowadź go w polu **kod weryfikacyjny** . Następnie wybierz pozycję **Weryfikuj kod**.

## <a name="next-steps"></a>Następne kroki

- [Dodawanie zewnętrznych dostawców tożsamości](tutorial-add-identity-providers.md)
- [Tworzenie przepływu użytkownika](tutorial-create-user-flows.md)