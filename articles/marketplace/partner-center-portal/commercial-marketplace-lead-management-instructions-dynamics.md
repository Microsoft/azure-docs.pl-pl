---
title: Zarządzanie potencjalnymi klientami w programie Dynamics 365 Customer Engagement — Microsoft Commercial Marketplace
description: Dowiedz się, jak skonfigurować usługę Dynamics 365 Customer Engagement w celu zarządzania potencjalnymi klientami z poziomu Microsoft AppSource i witryny Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: d996a2e0732ad9b4da104265710970e04cdbd368
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98199782"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Konfigurowanie zarządzania liderem dla usługi Dynamics 365 Customer Engagement

W tym artykule opisano sposób konfigurowania zaangażowania klienta Dynamics 365 (wcześniej o nazwie Dynamics CRM Online). Przeczytaj więcej na temat zmiany w temacie [Konfigurowanie uwierzytelniania opartego na serwerze z zaangażowaniem klientów i SharePoint Online](/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) , aby przetwarzać potencjalni klienci z komercyjnej oferty portalu Marketplace.

>[!NOTE]
>Te instrukcje są specyficzne dla środowiska chmury hostowanej przez firmę Microsoft dla programu Dynamics 365 Customer Engagement. Bezpośrednie łączenie z lokalnym środowiskiem Dynamics nie jest obecnie obsługiwane. Dostępne są inne opcje umożliwiające otrzymywanie potencjalnych klientów, takie jak konfigurowanie [punktu końcowego HTTPS](./commercial-marketplace-lead-management-instructions-https.md) lub [tabeli platformy Azure](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Wymagania wstępne

Następujące uprawnienia użytkownika są niezbędne do wykonania kroków opisanych w tym artykule:

* Uprawnienia administratora w wystąpieniu pakietu zaangażowania klienta w systemie Dynamics 365, aby można było zainstalować rozwiązanie.
* Uprawnienia administratora dzierżawy do tworzenia nowego konta usługi dla usługi lidera używanej do wysyłania potencjalnych klientów z komercyjnych ofert portalu Marketplace.
* Dostęp do portalu administracyjnego.
* Dostęp do Azure Portal.

## <a name="install-the-solution"></a>Instalowanie rozwiązania

1. Pobierz [rozwiązanie Microsoft Marketplaceego modułu zapisywania](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)i Zapisz je lokalnie na komputerze.

1. Otwórz program Dynamics 365 Customer Engagement, przechodząc do adresu URL dla wystąpienia usługi Dynamics, takiego jak `https://tenant.crm.dynamics.com` .

1. Wybierz ikonę koła zębatego na górnym pasku, a następnie wybierz pozycję **Ustawienia zaawansowane**.

    ![Element menu ustawień zaawansowanych systemu Dynamics 365](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. Na stronie **Ustawienia** Otwórz menu **Ustawienia** na górnym pasku i wybierz pozycję **rozwiązania**.

    >[!NOTE]
    >Jeśli nie widzisz opcji na poniższym ekranie, nie masz uprawnień, które należy wykonać. Skontaktuj się z administratorem w wystąpieniu programu Dynamics 365 Customer Engagement.

    ![Dynamics 365 — opcja rozwiązań](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. Na stronie **rozwiązania** wybierz pozycję **Importuj** i przejdź do lokalizacji, w której zapisano **Microsoft Marketplace rozwiązanie do zapisywania potencjalnych klientów** pobrane w kroku 1.

    ![Przycisk Importuj](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Zaimportuj rozwiązanie przy użyciu Kreatora importu rozwiązania.

## <a name="configure-user-permissions"></a>Konfigurowanie uprawnień użytkownika

Aby napisać potencjalni klienci do wystąpienia zaangażowania klienta Dynamics 365, należy udostępnić konto usługi z firmą Microsoft i skonfigurować uprawnienia dla konta.

Wykonaj następujące kroki, aby utworzyć konto usługi i przypisać uprawnienia. Możesz użyć Azure Active Directory lub pakietu Office 365.

>[!NOTE]
>Przejdź do odpowiednich instrukcji w zależności od wybranej opcji uwierzytelniania. Zobacz [Azure Active Directory](#azure-active-directory) lub [Office 365](#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Zalecamy korzystanie z tej opcji, ponieważ nie trzeba aktualizować nazwy użytkownika ani hasła, aby nadal otrzymywać potencjalnych klientów. Aby użyć opcji Azure Active Directory, należy podać identyfikator aplikacji, klucz aplikacji i identyfikator katalogu w aplikacji Active Directory.

Aby skonfigurować Azure Active Directory dla usługi Dynamics 365 Customer Engagement:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**.

1. Wybierz pozycję **Właściwości** i skopiuj wartość **Identyfikator katalogu** na stronie **Właściwości katalogu** . Zapisz tę wartość, ponieważ musisz podać ją w portalu wydawców, aby otrzymywać potencjalnych klientów na potrzeby oferty portalu Marketplace.

    ![Element menu właściwości Azure Active Directory](media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Wybierz pozycję **rejestracje aplikacji** w lewym okienku Azure Active Directory, a następnie wybierz pozycję **Nowa rejestracja** na tej stronie.
1. Wprowadź opisową nazwę aplikacji.
1. W obszarze **obsługiwane typy kont** wybierz pozycję **konta w dowolnym katalogu organizacyjnym**.
1. W obszarze **URI przekierowania (opcjonalnie)** wybierz pozycję **Sieć Web** i wprowadź identyfikator URI, na przykład `https://contosoapp1/auth` .
1. Wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie strony aplikacji](media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Teraz, gdy aplikacja jest zarejestrowana, uzyskaj dostęp do strony przegląd aplikacji. Skopiuj wartość **identyfikatora aplikacji (klienta)** na tej stronie. Zapisz tę wartość, ponieważ musisz ją udostępnić w portalu wydawców i w usłudze Dynamics 365, aby otrzymywać potencjalnych klientów dla oferty portalu Marketplace.

    ![Pole identyfikatora aplikacji (klienta)](media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Wybierz pozycję **certyfikaty & wpisy tajne** w lewym okienku aplikacji, a następnie wybierz przycisk **nowy klucz tajny klienta** . Wprowadź Opisowy wpis tajny klienta i wybierz opcję **nigdy nie** w obszarze **wygaśnięcie**. Wybierz pozycję **Dodaj** , aby utworzyć klucz tajny klienta.

    ![Element menu & certyfikatów](media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Po pomyślnym utworzeniu wpisu tajnego klienta skopiuj wartość **klucz tajny klienta** . Nie będzie można pobrać wartości po opuszczeniu strony. Zapisz tę wartość, ponieważ musisz podać ją w portalu wydawców, aby otrzymywać potencjalnych klientów na potrzeby oferty portalu Marketplace. 
1. Wybierz pozycję **uprawnienia interfejsu API** w lewym okienku aplikacji, a następnie wybierz pozycję **+ Dodaj uprawnienie**.
1. Wybierz pozycję **Microsoft API**, a następnie wybierz pozycję **Dynamics CRM** jako interfejs API.
1. W obszarze **jakiego typu uprawnienia aplikacja jest wymagana?** upewnij się, że wybrano **uprawnienia delegowane** .
1. W obszarze **uprawnienia** zaznacz pole wyboru **user_impersonation** **dostępu Common Data Service jako użytkownicy organizacji**. Następnie wybierz pozycję **Dodaj uprawnienia**.

    ![Przycisk Dodaj uprawnienia](media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Po wykonaniu kroków od 1 do 14 w Azure Portal przejdź do wystąpienia klienta Dynamics 365, przechodząc do adresu URL, na przykład `https://tenant.crm.dynamics.com` .
1. Wybierz ikonę koła zębatego na górnym pasku, a następnie wybierz pozycję **Ustawienia zaawansowane**.
1. Na stronie **Ustawienia** Otwórz menu **Ustawienia** na górnym pasku i wybierz pozycję **zabezpieczenia**.
1. Na stronie **zabezpieczenia** wybierz pozycję **Użytkownicy**. Na stronie **Użytkownicy** wybierz listę lista **włączonych użytkowników** , a następnie wybierz pozycję **Użytkownicy aplikacji**.
1. Wybierz pozycję **Nowy** , aby utworzyć nowego użytkownika.

    ![Tworzenie konta nowego użytkownika](media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. W okienku **nowy użytkownik** upewnij się, że użytkownik **: aplikacja** jest zaznaczona. Wprowadź nazwę użytkownika, pełną nazwę i adres e-mail użytkownika, który ma być używany z tym połączeniem. Ponadto wklej **Identyfikator aplikacji** dla aplikacji utworzonej w Azure Portal z kroku 8. Wybierz pozycję **zapisz & Zamknij** , aby zakończyć Dodawanie użytkownika.

    ![Okienko nowego użytkownika](media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Przejdź do sekcji "Ustawienia zabezpieczeń" w tym artykule, aby zakończyć konfigurowanie połączenia dla tego użytkownika.

### <a name="office-365"></a>Office 365

Jeśli nie chcesz używać Azure Active Directory, możesz zarejestrować nowego użytkownika w centrum administracyjnym Microsoft 365. Aby nadal otrzymywać potencjalni klienci, musisz zaktualizować nazwę użytkownika i hasło co 90 dni.

Aby skonfigurować pakiet Office 365 dla programu Dynamics 365 Customer Engagement:

1. Zaloguj się do [centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com).

1. Wybierz pozycję **Dodaj użytkownika**.

    ![Microsoft 365 centrum administracyjnego Dodaj opcję użytkownika](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Utwórz nowego użytkownika dla usługi składnika zapisywania potencjalnych klientów. Skonfiguruj następujące ustawienia:

    * Wprowadź nazwę użytkownika.
    * Wprowadź hasło, a następnie usuń zaznaczenie pola **Uczyń ten użytkownik zmianami hasła podczas pierwszego logowania** .
    * Wybierz pozycję **użytkownik (brak dostępu administratora)** jako rolę dla użytkownika.
    * Wybierz pozycję **Dynamics 365 Customer Engagement plan** jako licencję produktu, jak pokazano na poniższym ekranie. Opłata zostanie naliczona za licencję, którą wybierzesz. 

Zapisz te wartości, ponieważ musisz podać wartości **nazwy użytkownika** i **hasła** w portalu wydawców, aby otrzymywać potencjalni klienci na potrzeby oferty w witrynie Marketplace.

![Microsoft 365 okienku Centrum administracyjnego](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Ustawienia zabezpieczeń

Ostatnim krokiem jest umożliwienie użytkownikowi utworzonego w celu zapisania potencjalnych klientów.

1. Otwórz program Dynamics 365 Customer Engagement, przechodząc do adresu URL dla wystąpienia usługi Dynamics, takiego jak `https://tenant.crm.dynamics.com` .
1. Wybierz ikonę koła zębatego na górnym pasku, a następnie wybierz pozycję **Ustawienia zaawansowane**.
1. Na stronie **Ustawienia** Otwórz menu **Ustawienia** na górnym pasku i wybierz pozycję **zabezpieczenia**.
1. Na stronie **zabezpieczenia** wybierz pozycję **Użytkownicy** , a następnie wybierz użytkownika, który został utworzony w sekcji "Konfigurowanie uprawnień użytkownika" tego dokumentu. Następnie wybierz pozycję **Zarządzaj rolami**.

    ![Karta Zarządzanie rolami](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Wyszukaj nazwę roli **Microsoft Marketplace składnik zapisywania klienta** i wybierz ją, aby przypisać użytkownika do roli.

    ![Okienko Zarządzanie rolami użytkowników](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Ta rola jest tworzona przez zaimportowane rozwiązanie i ma uprawnienia do pisania potencjalnych klientów i śledzenia wersji rozwiązania w celu zapewnienia zgodności.

1. Wróć do strony **zabezpieczenia** i wybierz pozycję **role zabezpieczeń**. Wyszukaj rolę **Microsoft Marketplace autorem potencjalnego klienta** i wybierz ją.

    ![Okienko role zabezpieczeń](media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. W roli zabezpieczeń wybierz kartę **rekordy podstawowe** . Wyszukaj element **ustawień interfejsu użytkownika jednostki User** . Włącz dla tej jednostki uprawnienia do tworzenia, odczytu i zapisu dla użytkownika (1/4 żółtego kółka), wybierając odpowiednie przyciski radiowe.

    ![Karta rekordów podstawowych składnika zapisywania klienta Microsoft Marketplace](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. Na karcie **dostosowanie** Wyszukaj element **zadanie systemowe** . Włącz uprawnienia Odczyt, zapis i AppendTo w organizacji (pełne zielone przyciski radiowe) dla tej jednostki, wybierając odpowiednie przyciski radiowe.

    ![Karta dostosowania modułu zapisywania klienta Microsoft Marketplace](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Wybierz pozycję **Zapisz i zamknij**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Skonfiguruj ofertę do wysyłania potencjalnych klientów do programu Dynamics 365 Customer Engagement

Aby skonfigurować informacje dotyczące zarządzania potencjalnymi klientami dla oferty w portalu wydawców:

1. Przejdź do strony **konfiguracji oferty** oferty.
1. W sekcji **potencjalni klienci** wybierz pozycję **Połącz**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="Potencjalni klienci":::

1. W oknie podręcznym szczegóły połączenia wybierz pozycję **Dynamics 365 Customer Engagement** dla miejsca docelowego potencjalnego klienta.

    ![Pole docelowe potencjalnego klienta](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Wprowadź **adres URL** wystąpienia usługi Dynamics 365, na przykład `https://contoso.crm4.dynamics.com` .

1. Wybierz metodę **uwierzytelniania**, Azure Active Directory lub Office 365.
1. W przypadku wybrania **Azure Active Directory** wprowadź **Identyfikator aplikacji (** na przykład `23456052-aaaa-bbbb-8662-1234df56788f` ), **Identyfikator katalogu** (na przykład `12345678-8af1-4asf-1234-12234d01db47` ) i **klucz tajny klienta** (na przykład `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=` ).

    ![Uwierzytelnianie z wybranym Azure Active Directory](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. W przypadku wybrania **pakietu Office 365** wprowadź **nazwę użytkownika** (na przykład `contoso@contoso.onmicrosoft.com` ) i **hasło** (na przykład `P@ssw0rd` ).

    ![Pole nazwy użytkownika pakietu Office 365](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. W polu **kontaktowy adres e-mail** wprowadź adresy e-mail osób w firmie, które powinny otrzymywać powiadomienia e-mail po odebraniu nowego potencjalnego klienta. Możesz wprowadzić wiele adresów e-mail, rozdzielając je średnikami.
1. Wybierz przycisk **OK**.

Aby upewnić się, że pomyślnie nawiązano połączenie z miejscem docelowym potencjalnego klienta, wybierz przycisk **Weryfikuj** . Jeśli się powiedzie, będziesz mieć test w miejscu docelowym potencjalnego klienta.

![Pole kontaktowego adresu e-mail](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>Musisz zakończyć konfigurowanie reszty oferty i opublikować ją przed odebraniem potencjalnych klientów do oferty.
