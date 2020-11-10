---
title: Skonfiguruj subskrypcję portalu Azure Marketplace dla hostowanych dysków testowych
description: W tym artykule wyjaśniono, jak skonfigurować subskrypcję portalu Azure Marketplace dla programu Dynamics 365 na potrzeby współdziałania z klientami i Dynamics 365 dla dysków testowych operacji
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 11/09/2020
ms.openlocfilehash: 8e77c21c6d776aef20fa3ca71ad12fdb424b07ab
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94415019"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>Skonfiguruj subskrypcję portalu Azure Marketplace dla hostowanych dysków testowych

W tym artykule wyjaśniono, jak skonfigurować subskrypcję portalu Azure Marketplace i **dynamics 365 dla środowiska klienta** lub **Dynamics 365 for Operations** dla dysków testowych.

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>Konfiguracja programu Dynamics 365 dla zaangażowania klienta

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora.
2. Upewnij się, że jesteś w dzierżawie skojarzonej z wystąpieniem dysku testowego Dynamics 365, umieszczając kursor na ikonie konta w prawym górnym rogu. Jeśli nie jesteś w poprawnej dzierżawie, wybierz ikonę konta, aby przełączyć się do odpowiedniej dzierżawy.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Wybór odpowiedniej dzierżawy.":::

3. Sprawdź, czy licencja na **Plan zaangażowania klienta na Dynamics 365** jest dostępna.

    [![Sprawdzanie licencji planu.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Tworzenie aplikacji Azure Active Directory (AD) na platformie Azure. AppSource będzie używać tej aplikacji do aprowizacji i anulowania aprowizacji użytkownika testowego w dzierżawie.
    1. W okienku Filtr Wybierz pozycję **Azure Active Directory**.
    2. Wybierz pozycję **Rejestracje aplikacji**.

        [![Wybieranie pozycji rejestracje aplikacji.](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. Wybierz pozycję **Nowa rejestracja**.
    4. Podaj odpowiednią nazwę aplikacji.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Rejestrowanie aplikacji.":::

    5. W obszarze obsługiwane typy kont wybierz pozycję **konto w dowolnym katalogu organizacji i osobiste konta Microsoft**.
    6. Wybierz pozycję **Utwórz** i poczekaj na utworzenie aplikacji.
    7. Po utworzeniu aplikacji należy zwrócić uwagę na **Identyfikator aplikacji** wyświetlany na ekranie przegląd. Ta wartość będzie potrzebna później podczas konfigurowania dysku testowego.
    8. Aby dodać identyfikator URI przekierowania nativeclient, wybierz blok **uwierzytelnianie** . W obszarze **Konfiguracja platformy** wybierz opcję **Dodaj**  >  **Mobile**  >  kafelek aplikacji **klasycznej** platformy mobilnej. Wybierz identyfikator URI przekierowania **nativeclient** i wybierz pozycję **Konfiguruj**.

        :::image type="content" source="./media/test-drive/configure-desktop-devices.png" alt-text="Dodawanie identyfikatora URI przekierowania nativeclient.":::

    9. W obszarze **Zarządzanie aplikacją** wybierz pozycję **uprawnienia interfejsu API**.
    10. Wybierz pozycję **Dodaj uprawnienie** , a następnie **Microsoft Graph interfejs API**.
    11. Wybierz kategorię uprawnienia **aplikacji** , a następnie **katalog. odczyt. wszystkie** i katalog. **ReadWrite. wszystkie** uprawnienia.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Ustawianie uprawnień aplikacji.":::

    12. Aby dodać dostęp do **personifikacji użytkownika w programie Dynamics CRM** dla listy dozwolonych aplikacji usługi Azure AD, wybierz pozycję **Dodaj uprawnienie** ponownie.

        :::image type="content" source="./media/test-drive/request-api-permissions.png" alt-text="Żądanie uprawnień aplikacji.":::

    13. Po dodaniu uprawnienia wybierz pozycję **Udziel zgody administrator na firmę Microsoft**.
    14. Na stronie alertu wiadomości wybierz pozycję **tak**.

        [![Pokazuje, że uprawnienia aplikacji zostały pomyślnie przyznane.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    15. Aby wygenerować klucz tajny dla aplikacja usługi Azure AD:
        1. W obszarze **Zarządzanie aplikacją** wybierz pozycję **certyfikat i wpisy tajne**.
        2. W obszarze wpisy tajne klienta wybierz pozycję **nowy klucz tajny klienta**.
        3. Wprowadź opis, taki jak *dysk testowy* , i wybierz odpowiedni czas trwania. Po upływie tego klucza dysk testowy zostanie przerwany, w którym momencie trzeba będzie wygenerować i udostępnić nowy klucz AppSource.
        4. Wybierz pozycję **Dodaj** , aby wygenerować klucz tajny aplikacji platformy Azure. Skopiuj tę wartość, ponieważ wkrótce zostanie ona ukryta po Lave tego bloku. Ta wartość będzie potrzebna później podczas konfigurowania dysku testowego.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Dodawanie klucza tajnego klienta.":::

5. Czasami Synchronizacja użytkownika z usługi Azure AD z wystąpieniem programu CRM trwa dłużej niż oczekiwano. Aby uzyskać pomoc w tym, dodaliśmy proces wymuszania użytkownika synchronizacji, ale wymaga to, aby aplikacja usługi Azure AD była listy dozwolonych przez centrum partnerskie. Aby to zrobić, zobacz temat [Synchronizacja użytkownika z wystąpieniem zaangażowanie klienta](https://github.com/microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/CDS_Utility_to_ForceUserSync_in_CRM_Instance.md).
6. Dodaj rolę jednostki usługi do aplikacji, aby umożliwić aplikacji usługi Azure AD usuwanie użytkowników z dzierżawy platformy Azure.
    1. Otwórz wiersz polecenia programu PowerShell na poziomie administracyjnym.
    2. Install-Module MSOnline (Uruchom to polecenie, jeśli MSOnline nie jest zainstalowana).
    3. Connect-MsolService (spowoduje to wyświetlenie okna podręcznego; Zaloguj się przy użyciu nowo utworzonej dzierżawy organizacji).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-identyfikatora appprincipalid $applicationId.
    6. $Sp Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47dB-91af-98c3a49a38b1-RoleMemberObjectId. ObjectId-RoleMemberType serviceprincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Logowanie się do konta.":::

7. Dodaj powyżej utworzoną aplikację platformy Azure jako użytkownika aplikacji do wystąpienia testowego programu CRM.
    1. Dodaj nowego użytkownika w **Azure Active Directory**. Tylko wartości **name** i **username** (należące do tej samej dzierżawy) są wymagane do utworzenia tego użytkownika, pozostałe pola należy pozostawić jako domyślne. Skopiuj wartość Nazwa użytkownika.
    2. Zaloguj się do **wystąpienia programu CRM** i wybierz pozycję **Ustawianie**  >  **Security**  >  **użytkowników** zabezpieczeń.
    3. Zmień widok na **użytkowników aplikacji**.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="Ustawianie informacji o koncie dla użytkownika.":::

    4. Dodaj nowego użytkownika (Upewnij się, że formularz dotyczy użytkownika aplikacji).
    5. Wprowadź tę samą nazwę użytkownika w polach **podstawowy adres e-mail** i **Nazwa użytkownika** . Dodaj **Identyfikator aplikacji** **platformy Azure w usłudze site** ID.
    6. Podaj **pełną nazwę**.
    7. Wybierz pozycję **Zapisz**.
    8. Wybierz pozycję **Zarządzaj rolami**.
    9. Przypisz niestandardową lub OOB rolę zabezpieczeń, która zawiera uprawnienia do odczytu, zapisu i przypisywania ról, takie jak *administrator systemu*.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="Wybieranie uprawnień roli.":::

    10. Przypisz użytkownika aplikacji do niestandardowej roli zabezpieczeń utworzonej dla danego dysku testowego.

## <a name="set-up-for-dynamics-365-for-operations"></a>Konfiguracja programu Dynamics 365 dla operacji

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta administratora.
2. Upewnij się, że jesteś w dzierżawie skojarzonej z wystąpieniem dysku testowego Dynamics 365, umieszczając kursor na ikonie konta w prawym górnym rogu. Jeśli nie jesteś w poprawnej dzierżawie, wybierz ikonę konta, aby przełączyć się do odpowiedniej dzierżawy.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Wybór odpowiedniej dzierżawy.":::

3. Utwórz aplikacja usługi Azure AD na platformie Azure. AppSource będzie używać tej aplikacji do aprowizacji i anulowania aprowizacji użytkownika testowego w dzierżawie.
    1. W okienku Filtr Wybierz pozycję **Azure Active Directory**.
    2. Wybierz pozycję **Rejestracje aplikacji**.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="Wybieranie rejestracji aplikacji.":::

    3. Wybierz pozycję **Nowa rejestracja**.
    4. Podaj odpowiednią nazwę aplikacji.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Rejestrowanie aplikacji.":::

    5. W obszarze obsługiwane typy kont wybierz pozycję **konto w dowolnym katalogu organizacji i osobiste konta Microsoft**.
    6. Wybierz pozycję **Utwórz** i poczekaj na utworzenie aplikacji.
    7. Po utworzeniu aplikacji należy zwrócić uwagę na **Identyfikator aplikacji** wyświetlany na ekranie przegląd. Ta wartość będzie potrzebna później podczas konfigurowania dysku testowego.
    8. W obszarze **Zarządzanie aplikacją** wybierz pozycję **uprawnienia interfejsu API**.
    9. Wybierz pozycję **Dodaj uprawnienie** , a następnie **Microsoft Graph interfejs API**.
    10. Wybierz kategorię uprawnienia **aplikacji** , a następnie **katalog. odczyt. wszystkie** i katalog. **ReadWrite. wszystkie** uprawnienia.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Ustawianie uprawnień aplikacji.":::

    11. Wybierz pozycję **Dodaj uprawnienie**.
    12. Po dodaniu uprawnienia wybierz pozycję **Udziel zgody administrator na firmę Microsoft**.
    13. Na stronie alertu wiadomości wybierz pozycję **tak**.

        [![Pokazuje, że uprawnienia aplikacji zostały pomyślnie przyznane.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Aby wygenerować klucz tajny dla aplikacja usługi Azure AD:
        1. W obszarze **Zarządzanie aplikacją** wybierz pozycję **certyfikat i wpisy tajne**.
        2. W obszarze wpisy tajne klienta wybierz pozycję **nowy klucz tajny klienta**.
        3. Wprowadź opis, taki jak *dysk testowy* , i wybierz odpowiedni czas trwania. Po upływie tego klucza dysk testowy zostanie przerwany, w którym momencie trzeba będzie wygenerować i udostępnić nowy klucz AppSource.
        4. Wybierz pozycję **Dodaj** , aby wygenerować klucz tajny aplikacji platformy Azure. Skopiuj tę wartość, ponieważ wkrótce zostanie ona ukryta po Lave tego bloku. Ta wartość będzie potrzebna później podczas konfigurowania dysku testowego.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Dodawanie klucza tajnego klienta.":::

4. Dodaj rolę jednostki usługi do aplikacji, aby umożliwić aplikacji usługi Azure AD usuwanie użytkowników z dzierżawy platformy Azure.
    1. Otwórz wiersz polecenia programu PowerShell na poziomie administracyjnym.
    2. Install-Module MSOnline (Uruchom to polecenie, jeśli MSOnline nie jest zainstalowana).
    3. Connect-MsolService (spowoduje to wyświetlenie okna podręcznego; Zaloguj się przy użyciu nowo utworzonej dzierżawy organizacji).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-identyfikatora appprincipalid $applicationId.
    6. $Sp Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47dB-91af-98c3a49a38b1-RoleMemberObjectId. ObjectId-RoleMemberType serviceprincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Logowanie się do konta.":::

5. Teraz Dodaj powyższą aplikację do usługi **Dynamics 365 dla operacji** , aby umożliwić aplikacji Zarządzanie użytkownikami.
    1. Znajdź swoje wystąpienie usługi **Dynamics 365 for Operations** .
    2. W lewym górnym rogu kliknij menu z trzema wierszami (Hamburger).
    3. Wybierz pozycję **Administracja systemu**.
    4. Wybierz pozycję **Azure Active Directory aplikacje**.
    5. Wybierz pozycję **+ Nowe**.
    6. Wprowadź **Identyfikator klienta aplikacji usługi Azure AD** , która ma wykonywać działania w imieniu użytkownika.

    > [!NOTE]
    > Identyfikator użytkownika, w którym imieniu akcje zostaną wykonane (zazwyczaj administrator systemu wystąpienia lub użytkownika, który ma uprawnienia do dodawania innych użytkowników).

    [![Identyfikator użytkownika, w którym imieniu akcje zostaną wykonane (zazwyczaj administrator systemu wystąpienia lub użytkownika, który ma uprawnienia do dodawania innych użytkowników).](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
