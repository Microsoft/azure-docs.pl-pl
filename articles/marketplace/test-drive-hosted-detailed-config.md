---
title: Szczegółowa konfiguracja hostowanej dysku testowego w programie Azure Marketplace
description: Wyjaśnienie kroków konfiguracji hostowanej dysku testowego na platformie handlowej
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 04/20/2021
ms.openlocfilehash: f11f1d5601a61bbd9b8729b478c278db8d3e73dc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812424"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Szczegółowa konfiguracja hostowanych dysków testowych

W tym artykule opisano sposób konfigurowania hostowanej aplikacji testowej dla usługi Dynamics 365 for Customer Engagement lub Dynamics 365 for Operations.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Konfigurowanie usługi Dynamics 365 Customer Engagement

1. Zaloguj się do [Partner Center](https://partner.microsoft.com/).
2. Jeśli nie możesz uzyskać dostępu do powyższego linku, musisz przesłać tutaj żądanie opublikowania aplikacji. [](https://appsource.microsoft.com/partners/list-an-app) Po przejrzeniu żądania użytkownik będzie miał dostęp do rozpoczęcia procesu publikowania.
3. Znajdź istniejącą **ofertę usługi Dynamics 365 for Customer Engagement** lub utwórz nową ofertę **usługi Dynamics 365 for Customer** Engagement.
4. Zaznacz pole **wyboru Włącz dysk testowy** i wybierz typ dysku **testowego** (zobacz punkt poniżej), a następnie wybierz pozycję **Zapisz**.

    [![Zaznaczenie pola wyboru "Włącz dysk testowy".](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Typ dysku testowego** — wybierz **pozycję Hostowane przez firmę Microsoft (Dynamics 365 for Customer Engagement & PowerApps).** Oznacza to, że firma Microsoft będzie hostować i utrzymywać usługę, która wykonuje testową aprowizowanie i coprowizowanie użytkowników.

5. Przyznaj Microsoft AppSource aprowizację i coprowizację użytkowników testowych w dzierżawie, korzystając [z tych instrukcji.](./test-drive-azure-subscription-setup.md) W tym kroku zostanie wygenerowany identyfikator **aplikacja usługi Azure AD klucz** aplikacja usługi Azure AD **wartości** klucza wymienione poniżej.
6. Wypełnij te pola na stronie **Konfiguracja techniczna dysku testowego.**

    [![Strona konfiguracji technicznej dysku testowego.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maksymalna liczba współbieżnych** dysków testowych — liczba równoczesnych użytkowników, którzy mogą mieć aktywny dysk testowy uruchomiony w tym samym czasie. Każdy użytkownik będzie korzystać z licencji usługi Dynamics, gdy jego dysk testowy jest aktywny, dlatego upewnij się, że masz co najmniej tyle licencji usługi Dynamics dostępnych dla użytkowników wersji testowej. Zalecamy użycie od 3 do 5.
    - **Czas trwania dysku testowego** — liczba godzin, przez które dysk testowy użytkownika będzie aktywny. Po upływie tego czasu aprowizę użytkownika zostanie coprowizowana w dzierżawie. Zalecamy 2–24 godziny w zależności od złożoności aplikacji. Użytkownik może zawsze zażądać kolejnego dysku testowego, jeśli zabraknie czasu i chce ponownie uzyskać dostęp do dysku testowego.
    - **Adres URL wystąpienia** — adres URL, na który użytkownik testu będzie wysyłany po uruchomieniu dysku testowego. Zazwyczaj jest to adres URL wystąpienia usługi Dynamics 365, na którym zainstalowano aplikację i przykładowe dane. Przykładowa wartość: `https://testdrive.crm.dynamics.com` .
    - **Adres URL internetowego interfejsu API** wystąpienia — adres URL internetowego interfejsu API dla wystąpienia usługi Dynamics 365. Pobierz tę wartość, logując się do wystąpienia usługi Microsoft Dynamics 365 i przechodząc do internetowego interfejsu API ustawiania zasobów dla deweloperów dostosowywania i  >    >    >   kopiując adres (URL). Przykład wartość:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Przykład internetowego interfejsu API wystąpienia.":::

    - **Nazwa roli** — nazwa niestandardowej roli zabezpieczeń usługi Dynamics 365 utworzonej dla dysku testowego lub można użyć istniejącej roli. Nowa rola powinna mieć minimalne wymagane uprawnienia dodane do roli w celu zalogowania się do wystąpienia usługi Customer Engagement. Zapoznaj się [z tematem Minimum privileges required to sign into Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365)(Minimalne uprawnienia wymagane do logowania się do usługi Microsoft Dynamics 365). Jest to rola, która zostanie przypisana do użytkowników podczas ich dysku testowego. Przykładowa wartość: `testdriverole` .
    
        > [!IMPORTANT]
        > Upewnij się, że nie dodano kontroli grupy zabezpieczeń. Dzięki temu użytkownik może być synchronizowany z wystąpieniem customer engagement.

    - **Azure Active Directory dzierżawy** — identyfikator dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Aby pobrać tę wartość, zaloguj się do Azure Portal i przejdź do Azure Active Directory Właściwości i  >   skopiuj identyfikator katalogu. Przykładowa wartość: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory dzierżawy** — nazwa dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Użyj formatu `<tenantname>.onmicrosoft.com`. Przykładowa wartość: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory aplikacji —** identyfikator aplikacji Azure Active Directory (AD) utworzonej w kroku 5. Przykładowa wartość: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory tajny klienta aplikacji** — klucz tajny dla aplikacji usługi Azure AD utworzonej w kroku 5. Przykładowa wartość: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Podaj szczegóły listy na platformie handlowej. Wybierz **pozycję Język,** aby wyświetlić dodatkowe wymagane pola.

    [![Strona szczegółów listy w witrynie Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Opis** — omówienie dysku testowego. Ten tekst będzie wyświetlany użytkownikowi podczas aprowizowanych dysków testowych. To pole obsługuje kod HTML, jeśli chcesz podać sformatowaną zawartość.
    - **Podręcznik użytkownika —** podręcznik użytkownika w formacie PDF, który pomaga użytkownikom testowym zrozumieć sposób korzystania z aplikacji.
    - **Pokaz wideo na dysku testowym** — film, który pokazuje aplikację (opcjonalnie).

## <a name="configure-for-dynamics-365-operations"></a>Konfigurowanie dla operacji usługi Dynamics 365

2. Jeśli nie możesz uzyskać dostępu do powyższego linku, musisz przesłać tutaj żądanie opublikowania aplikacji. [](https://appsource.microsoft.com/partners/list-an-app) Po przejrzeniu żądania użytkownik będzie miał dostęp do rozpoczęcia procesu publikowania.
3. Znajdź istniejącą **ofertę usługi Dynamics 365 for Operations** lub utwórz nową ofertę **usługi Dynamics 365 for** Operations.
4. Zaznacz pole **wyboru Włącz dysk testowy** i wybierz typ dysku **testowego** (zobacz punkt poniżej), a następnie wybierz pozycję **Zapisz**.

    [![Zaznacz pole wyboru "Włącz dysk testowy".](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Typ dysku testowego** — wybierz **opcję Dynamics 365 for Operations.** Oznacza to, że firma Microsoft będzie hostować i utrzymywać usługę, która wykonuje aprowizowanie i cokaprowizowanie użytkowników na dysku testowym.

5. Przyznaj Microsoft AppSource aprowizowania i coprowizowania użytkowników testowych w dzierżawie, korzystając [z tych instrukcji.](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md) W tym kroku wygeneruje się identyfikator **aplikacja usługi Azure AD i** **aplikacja usługi Azure AD klucz** wymienione poniżej.
6. Wypełnij te pola na stronie **Konfiguracja techniczna dysku testowego.**

    [![Strona konfiguracji technicznej witryny Marketplace.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maksymalna liczba współbieżnych** dysków testowych — liczba równoczesnych użytkowników, którzy mogą mieć uruchomiony aktywny dysk testowy w tym samym czasie. Każdy użytkownik będzie korzystać z licencji usługi Dynamics, gdy jego dysk testowy jest aktywny, dlatego upewnij się, że masz co najmniej tyle licencji usługi Dynamics dostępnych dla użytkowników wersji testowej. Zalecamy od 3 do 5.
    - **Czas trwania dysku testowego** — liczba godzin, przez które dysk testowy użytkownika będzie aktywny. Po upływie tego czasu użytkownik zostanie coprowizowany z dzierżawy. Zalecamy 2–24 godziny w zależności od złożoności aplikacji. Użytkownik może zawsze zażądać innego dysku testowego, jeśli zabraknie czasu i ponownie chce uzyskać dostęp do dysku testowego.
    - **Adres URL wystąpienia** — adres URL, na który użytkownik dysku testowego zostanie wysłany po uruchomieniu dysku testowego. Zazwyczaj jest to adres URL wystąpienia usługi Dynamics 365, na którym zainstalowano aplikację i przykładowe dane. Przykładowa wartość: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory dzierżawy** — identyfikator dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Aby pobrać tę wartość, zaloguj się do Azure Portal i przejdź do Azure Active Directory właściwości i  >   skopiuj identyfikator katalogu. Przykładowa wartość: 172f988bf-86f1-41af-91ab-2d7cd01112341.
    - **Azure Active Directory dzierżawy** — nazwa dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Użyj formatu `<tenantname>.onmicrosoft.com`. Przykładowa wartość: `testdrive.onmicrosoft.com` .
    - **Azure Active Directory aplikacji —** identyfikator aplikacji Azure Active Directory (AD) utworzonej w kroku 5. Przykładowa wartość: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory tajny klienta aplikacji** — klucz tajny dla aplikacji usługi Azure AD utworzonej w kroku 5. Przykładowa wartość: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Jednostka prawna wersji** próbnej — podaj jednostkę prawna, aby przypisać użytkownika wersji próbnej. Możesz utworzyć nową jednostkę na stronie [Create or modify a legal entity (Tworzenie lub modyfikowanie jednostki prawnej).](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)
    - **Nazwa roli** — nazwa AOT (drzewo obiektów aplikacji) niestandardowej roli zabezpieczeń usługi Dynamics 365 utworzonej dla dysku testowego. Jest to rola, która zostanie przypisana do użytkowników podczas ich dysku testowego.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="Strona konfiguracji zabezpieczeń.":::

7. Podaj szczegóły listy na platformie handlowej. Wybierz **pozycję Język,** aby wyświetlić dodatkowe wymagane pola.

    [![Strona szczegółów listy w witrynie Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Opis** — omówienie dysku testowego. Ten tekst będzie wyświetlany użytkownikowi podczas aprowizowanych dysków testowych. To pole obsługuje kod HTML, jeśli chcesz podać sformatowaną zawartość.
    - **Podręcznik użytkownika —** podręcznik użytkownika w formacie PDF, który pomaga użytkownikom testowym zrozumieć sposób korzystania z aplikacji.
    - **Pokaz wideo na dysku testowym** — film, który pokazuje aplikację (opcjonalnie).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->