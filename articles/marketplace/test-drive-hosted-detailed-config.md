---
title: Szczegółowa konfiguracja hostowanego dysku testowego w portalu Azure Marketplace
description: Wyjaśnienie czynności konfiguracyjnych dla hostowanego dysku testowego w komercyjnej witrynie Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/06/2020
ms.openlocfilehash: 88779f67a2fa9b18f0177a1459b32c672343bb57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462955"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>Szczegółowa konfiguracja dla hostowanych dysków testowych

W tym artykule opisano sposób konfigurowania hostowanego dysku testowego dla programu Dynamics 365 na potrzeby współpracy klienta lub usługi Dynamics 365 dla operacji.

## <a name="configure-for-dynamics-365-customer-engagement"></a>Konfigurowanie dla programu Dynamics 365 Customer Engagement

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/).
2. Jeśli nie możesz uzyskać dostępu do powyższego linku, musisz przesłać żądanie [tutaj](https://appsource.microsoft.com/partners/list-an-app) , aby opublikować aplikację. Po przejrzeniu żądania zostanie udzielony dostęp umożliwiający rozpoczęcie procesu publikowania.
3. Znajdź istniejącą ofertę **dynamics 365 dla usługi Customer Engagement** lub Utwórz nową ofertę **Dynamics 365 for Customer Engagement** .
4. Zaznacz pole wyboru **Włącz stację testową** i wybierz **typ dysku testowego** (zobacz punktor poniżej), a następnie wybierz pozycję **Zapisz**.

    [![Zaznacz pole wyboru "Włącz stację testową".](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **Typ dysku testowego** — wybierz **firmę Microsoft hostowaną (Dynamics 365 for Customer Engagement & powerapps)**. Oznacza to, że firma Microsoft będzie hostować i obsługiwać usługę, która przeprowadza testowanie i cofa obsługę administracyjną użytkowników.

5. Przyznaj Microsoft AppSource uprawnienia do aprowizacji i anulowania aprowizacji użytkowników w dzierżawie przy użyciu [tych instrukcji](./test-drive-azure-subscription-setup.md). W tym kroku zostanie wygenerowany **aplikacja usługi Azure AD identyfikator** i **aplikacja usługi Azure AD wartości klucza** wymienione poniżej.
6. Wypełnij te pola na stronie **konfiguracja techniczna na dysku testowym** .

    [![Strona konfiguracja techniczna na dysku testowym.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maksymalne współbieżne dyski testowe** — liczba równoczesnych użytkowników, którzy mogą korzystać z aktywnego dysku testowego w tym samym czasie. Każdy użytkownik będzie korzystał z licencji usługi Dynamics, gdy ich dysk testowy jest aktywny, więc upewnij się, że masz co najmniej następującą liczbę dostępnych licencji Dynamics dla użytkowników wersji testowej. Zalecamy od 3 do 5.
    - **Czas trwania dysku testowego** — liczba godzin aktywności dysku testowego użytkownika. Po upływie tego czasu użytkownik zostanie wydzierżawiony z dzierżawy. Zalecamy 2-24 godzin w zależności od złożoności aplikacji. Użytkownik może zawsze zażądać innego dysku testowego, jeśli są one nieaktualne i chcą ponownie uzyskać dostęp do dysku testowego.
    - **Adres URL wystąpienia** — adres URL, do którego zostanie wysłany użytkownik dysku testowego po uruchomieniu testu. Jest to zazwyczaj adres URL wystąpienia programu Dynamics 365, na którym zainstalowano aplikację i przykładowe dane. Przykładowa wartość: `https://testdrive.crm.dynamics.com` .
    - **Adres URL internetowego interfejsu API wystąpienia** — adres URL internetowego interfejsu API dla wystąpienia usługi Dynamics 365. Pobierz tę wartość, logując się do wystąpienia systemu Microsoft Dynamics 365 i przechodząc do **ustawień ustawienia** internetowego  >    >  interfejsu API wystąpienia **zasobów deweloperskich**  >   i skopiuj adres (URL). Przykład wartość:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="Przykład interfejsu API sieci Web wystąpienia.":::

    - **Nazwa roli** — nazwa niestandardowej roli zabezpieczeń Dynamics 365 utworzonej dla programu Test Drive lub można użyć istniejącej roli. Nowa rola powinna mieć minimalne wymagane uprawnienia dodane do roli w celu zalogowania się do wystąpienia zaangażowania klienta. Zapoznaj się z [minimalnymi uprawnieniami wymaganymi do zalogowania się do systemu Microsoft Dynamics 365](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365). Jest to rola, która zostanie przypisana do użytkowników na ich dysku testowym. Przykładowa wartość: `testdriverole` .
    
        > [!IMPORTANT]
        > Upewnij się, że kontrola grupy zabezpieczeń nie została dodana. Pozwala to na synchronizację użytkownika z wystąpieniem zaangażowania klienta.

    - **Azure Active Directory identyfikator dzierżawy** — identyfikator dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Aby pobrać tę wartość, zaloguj się do Azure Portal i przejdź do **Azure Active Directory**  >  **Właściwości** i skopiuj identyfikator katalogu. Przykładowa wartość: 172f988bf-86f1-41AF-91ab-2d7cd01112341.
    - **Azure Active Directory nazwę dzierżawy** — nazwa dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Użyj formatu `<tenantname>.onmicrosoft.com`. Przykładowa wartość: `testdrive.onmicrosoft.com` .
    - **Identyfikator aplikacji Azure Active Directory** — identyfikator aplikacji Azure Active Directory (AD) utworzonej w kroku 5. Przykładowa wartość: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory App Client Secret** — Secret dla aplikacji usługi Azure AD utworzonej w kroku 5. Przykładowa wartość: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .

7. Podaj szczegóły dotyczące aukcji portalu Marketplace. Wybierz **Język** , aby wyświetlić więcej wymaganych pól.

    [![Strona szczegółów listy witryny Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Opis** — przegląd dysku testowego. Ten tekst będzie widoczny dla użytkownika podczas aprowizacji dysku testowego. To pole obsługuje kod HTML, jeśli chcesz wprowadzić sformatowaną zawartość.
    - **Podręcznik użytkownika** — Podręcznik użytkownika PDF, który pomaga testować, jak korzystać z aplikacji.
    - **Wideo demonstracyjne dla wersji testowej** — wideo, które prezentuje aplikację (opcjonalnie).

## <a name="configure-for-dynamics-365-operations"></a>Konfigurowanie dla operacji systemu Dynamics 365

2. Jeśli nie możesz uzyskać dostępu do powyższego linku, musisz przesłać żądanie [tutaj](https://appsource.microsoft.com/partners/list-an-app) , aby opublikować aplikację. Po przejrzeniu żądania zostanie udzielony dostęp umożliwiający rozpoczęcie procesu publikowania.
3. Znajdź istniejącą ofertę **dynamics 365 for Operations** lub Utwórz nową ofertę **Dynamics 365 dla operacji** .
4. Zaznacz pole wyboru **Włącz stację testową** i wybierz **typ dysku testowego** (zobacz punktor poniżej), a następnie wybierz pozycję **Zapisz**.

    [![Zaznacz pole wyboru "Włącz stację testową".](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **Typ dysku testowego** — wybierz opcję **Dynamics 365 dla operacji** . Oznacza to, że firma Microsoft będzie hostować i obsługiwać usługę, która przeprowadza testowanie i cofa obsługę administracyjną użytkowników.

5. Przyznaj Microsoft AppSource uprawnienia do aprowizacji i anulowania aprowizacji użytkowników w dzierżawie przy użyciu [tych instrukcji](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md). W tym kroku zostanie wygenerowany **aplikacja usługi Azure AD identyfikator** i **aplikacja usługi Azure AD wartości klucza** wymienione poniżej.
6. Wypełnij te pola na stronie **konfiguracja techniczna na dysku testowym** .

    [![Strona konfiguracja techniczna portalu Marketplace.](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **Maksymalne współbieżne dyski testowe** — liczba równoczesnych użytkowników, którzy mogą korzystać z aktywnego dysku testowego w tym samym czasie. Każdy użytkownik będzie korzystał z licencji usługi Dynamics, gdy ich dysk testowy jest aktywny, więc upewnij się, że masz co najmniej następującą liczbę dostępnych licencji Dynamics dla użytkowników wersji testowej. Zalecamy od 3 do 5.
    - **Czas trwania dysku testowego** — liczba godzin aktywności dysku testowego użytkownika. Po upływie tego czasu użytkownik zostanie wydzierżawiony z dzierżawy. Zalecamy 2-24 godzin w zależności od złożoności aplikacji. Użytkownik może zawsze zażądać innego dysku testowego, jeśli są one nieaktualne i chcą ponownie uzyskać dostęp do dysku testowego.
    - **Adres URL wystąpienia** — adres URL, do którego zostanie wysłany użytkownik dysku testowego po uruchomieniu testu. Jest to zazwyczaj adres URL wystąpienia programu Dynamics 365, na którym zainstalowano aplikację i przykładowe dane. Przykładowa wartość: `https://testdrive.crm.dynamics.com` .
    - **Azure Active Directory identyfikator dzierżawy** — identyfikator dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Aby pobrać tę wartość, zaloguj się do Azure Portal i przejdź do **Azure Active Directory**  >  **Właściwości** i skopiuj identyfikator katalogu. Przykładowa wartość: 172f988bf-86f1-41AF-91ab-2d7cd01112341.
    - **Azure Active Directory nazwę dzierżawy** — nazwa dzierżawy platformy Azure dla wystąpienia usługi Dynamics 365. Użyj formatu `<tenantname>.onmicrosoft.com`. Przykładowa wartość: `testdrive.onmicrosoft.com` .
    - **Identyfikator aplikacji Azure Active Directory** — identyfikator aplikacji Azure Active Directory (AD) utworzonej w kroku 5. Przykładowa wartość: `53852862-a2ae-4e43-9461-faa49650a096` .
    - **Azure Active Directory App Client Secret** — Secret dla aplikacji usługi Azure AD utworzonej w kroku 5. Przykładowa wartość: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=` .
    - **Wersja próbna podmiotu prawnego** — umożliwia podmiotowi prawnemu przypisanie użytkownika wersji próbnej. Nową [jednostkę](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)można utworzyć przy użyciu programu.
    - **Nazwa roli** — nazwa AOT (drzewo obiektów aplikacji) dla niestandardowej roli zabezpieczeń Dynamics 365 utworzonej dla dysku testowego. Jest to rola, która zostanie przypisana do użytkowników na ich dysku testowym.

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="Na stronie Konfiguracja zabezpieczeń.":::

7. Podaj szczegóły dotyczące aukcji portalu Marketplace. Wybierz **Język** , aby wyświetlić więcej wymaganych pól.

    [![Strona szczegółów listy witryny Marketplace.](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **Opis** — przegląd dysku testowego. Ten tekst będzie widoczny dla użytkownika podczas aprowizacji dysku testowego. To pole obsługuje kod HTML, jeśli chcesz wprowadzić sformatowaną zawartość.
    - **Podręcznik użytkownika** — Podręcznik użytkownika PDF, który pomaga testować, jak korzystać z aplikacji.
    - **Wideo demonstracyjne dla wersji testowej** — wideo, które prezentuje aplikację (opcjonalnie).

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->