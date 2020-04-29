---
title: Dynamics CRM | Portal Azure Marketplace
description: Skonfiguruj zarządzanie potencjalnymi klientami dla programu Dynamics CRM.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: d64c8460f5653f28b96396025f29ea13af15c8c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416320"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurowanie zarządzania liderem w usłudze Dynamics CRM Online

W tym artykule opisano sposób konfigurowania usługi Dynamics CRM Online do przetwarzania potencjalnych klientów.

## <a name="prerequisites"></a>Wymagania wstępne

Następujące uprawnienia użytkownika są potrzebne do wykonania kroków opisanych w tym artykule:
- Aby zainstalować rozwiązanie, musisz być administratorem wystąpienia usługi Dynamics CRM Online.
- Musisz być administratorem dzierżawy, aby utworzyć nowe konto usługi dla usługi lidera.

<a name="install-the-solution"></a>Instalowanie rozwiązania
--------------------

1.  Pobierz [rozwiązanie Microsoft Marketplaceego modułu zapisywania](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) i Zapisz je lokalnie.

2.  Otwórz usługi Dynamics CRM Online i przejdź do pozycji Ustawienia.
    >[!NOTE]
    >Jeśli nie widzisz opcji w następnym przechwyceniu ekranu, nie masz wymaganych uprawnień.
 
       ![Widok instalacji systemu Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Wybierz pozycję **Importuj**, a następnie wybierz rozwiązanie pobrane w kroku 1.
 
    ![Opcja importowania systemu Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Zakończ Instalowanie rozwiązania.

## <a name="configure-user-permissions"></a>Konfigurowanie uprawnień użytkownika

Aby napisać potencjalnych klientów do wystąpienia programu Dynamics CRM, musisz udostępnić konto usługi z nami i skonfigurować uprawnienia dla konta.

Wykonaj następujące kroki, aby utworzyć konto usługi i przypisać uprawnienia. Możesz użyć **Azure Active Directory** lub **pakietu Office 365**.

### <a name="azure-active-directory"></a>Azure Active Directory

Zalecamy korzystanie z tej opcji, ponieważ w celu uzyskania potencjalnych klientów nie trzeba aktualizować nazwy użytkownika ani hasła. Aby użyć opcji Azure Active Directory, podaj identyfikator aplikacji, klucz aplikacji i identyfikator katalogu z aplikacji Active Directory.

Wykonaj następujące kroki, aby skonfigurować Azure Active Directory programu Dynamics CRM.

1.  Zaloguj się do [Azure Portal](https://portal.azure.com/) a następnie wybierz usługę Azure Active Directory.

2.  Wybierz pozycję **Właściwości** , a następnie skopiuj **Identyfikator katalogu**. Jest to identyfikator konta dzierżawy, którego potrzebujesz użyć w portal Cloud Partner.

    ![Pobierz identyfikator katalogu](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **rejestracja nowej aplikacji**.
4.  Wpisz nazwę aplikacji.
5.  W obszarze Typ wybierz pozycję **aplikacja sieci Web/interfejs API**.
6.  Podaj adres URL. To pole nie jest wymagane w przypadku potencjalnych klientów, ale jest wymagane do utworzenia aplikacji.
7. Wybierz przycisk **Utwórz**.
8.  Teraz, gdy aplikacja jest zarejestrowana, wybierz pozycję **Właściwości** , a następnie wybierz pozycję **Kopiuj identyfikator aplikacji**. Te informacje o połączeniu zostaną użyte w portal Cloud Partner.
9.  We właściwościach ustaw aplikację jako wielodostępną, a następnie wybierz pozycję **Zapisz**.

10. Wybierz pozycję **klucze** i Utwórz nowy klucz z czasem trwania ustawionym na *nigdy nie wygasa*. Wybierz pozycję **Zapisz** , aby utworzyć klucz. 
11. W menu klucze wybierz opcję **Kopiuj wartość klucza.** Zapisz kopię tej wartości, ponieważ będzie ona potrzebna dla portal Cloud Partner.
    
    ![Dynamics Get — zarejestrowano klucz](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Wybierz pozycję **wymagane uprawnienia** , a następnie wybierz pozycję **Dodaj**. 
13. Wybierz pozycję **Dynamics CRM Online** jako nowy interfejs API i sprawdź uprawnienia dostępu do usługi *CRM Online jako użytkownicy organizacji*.

14. W programie Dynamics CRM przejdź do pozycji Użytkownicy i wybierz listę rozwijaną "włączonych użytkowników", aby przełączyć się do opcji **Użytkownicy aplikacji**.
    
    ![Użytkownicy aplikacji](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Wybierz pozycję **Nowy** , aby utworzyć nowego użytkownika. Wybierz użytkownika: Lista rozwijana **użytkownika aplikacji** .
    
    ![Dodaj nowego użytkownika aplikacji](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. W polu **nowy użytkownik**Podaj nazwę i adres e-mail, które mają być używane z tym połączeniem. Wklej **Identyfikator aplikacji** dla aplikacji utworzonej w Azure Portal.

     ![Konfiguruj nowego użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Przejdź do pozycji "Ustawienia zabezpieczeń" w tym artykule, aby zakończyć konfigurowanie połączenia dla tego użytkownika.

### <a name="office-365"></a>Office 365

Jeśli nie chcesz używać Azure Active Directory, możesz zarejestrować nowego użytkownika w *centrum administracyjnym Microsoft 365*. Aby nadal otrzymywać potencjalni klienci, musisz zaktualizować nazwę użytkownika/hasło co 90 dni.

Wykonaj następujące kroki, aby skonfigurować pakiet Office 365 dla programu Dynamics CRM.

1. Zaloguj się do [centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com).

2. Wybierz kafelek **administrator** .

    ![Administrator usługi Office Online](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Wybierz pozycję **Dodaj użytkownika**.

    ![Dodawanie użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Utwórz nowego użytkownika dla usługi składnika zapisywania potencjalnych klientów. Skonfiguruj następujące ustawienia:

    -   Podaj hasło i usuń zaznaczenie opcji "Uczyń ten użytkownik zmianami hasła podczas pierwszego logowania".
    -   Wybierz pozycję "użytkownik (brak dostępu administratora)" jako rolę dla użytkownika.
    -   Wybierz licencję produktu pokazaną w następnym przechwyceniu ekranu. Opłata zostanie naliczona za licencję, którą wybierzesz. Rozwiązanie będzie również współpracowało z licencją usługi Dynamics CRM Online w warstwie Podstawowa.
    
    ![Konfigurowanie uprawnień użytkownika i licencji](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Ustawienia zabezpieczeń

Ostatnim krokiem jest umożliwienie użytkownikowi utworzonego w celu zapisania potencjalnych klientów.

1.  Zaloguj się do usługi Dynamics CRM Online.
2.  W obszarze **Ustawienia**wybierz pozycję **zabezpieczenia**.
    
    ![Ustawienia zabezpieczeń](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Wybierz użytkownika utworzonego w obszarze **uprawnienia użytkownika**, a następnie wybierz pozycję **Zarządzaj rolami użytkowników**. Aby przypisać rolę, zaznacz opcję **moduł zapisujący klienta Microsoft Marketplace** .

    ![Przypisywanie roli użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Ta rola jest tworzona przez zaimportowane rozwiązanie i ma uprawnienia do pisania potencjalnych klientów i śledzenia wersji rozwiązania w celu zapewnienia zgodności.

4.  W obszarze Zabezpieczenia wybierz pozycję **role zabezpieczeń** i znajdź rolę Microsoft Marketplaceego składnika zapisywania klienta.
    
    ![Konfigurowanie składnika zapisywania klientów zabezpieczeń](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Wybierz kartę **rekordy podstawowe** . Włącz opcję Utwórz/Odczytaj/Zapisz dla interfejsu użytkownika obiektu.

    ![Włącz tworzenie/odczyt/zapis dla użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Zawijaj w górę

Zakończ Konfigurowanie zarządzania programu Dynamics CRM dla lidera, dodając do portal Cloud Partner informacje o wygenerowanym koncie. Przykład:

-   **Azure Active Directory** - **Identyfikator aplikacji** Azure Active Directory (przykład: *23456052-AAAA-bbbb-8662-1234df56788f*), **Identyfikator katalogu** (przykład: *12345678-8af1-4asf-1234-12234d01db47*) i **klucz aplikacji** (przykład: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc =*).
-   **Adres URL** **pakietu Office 365** - ( **`https://contoso.crm4.dynamics.com`** przykład:), **Nazwa użytkownika** ( **`contoso\@contoso.onmicrosoft.com`** przykład:) i **hasło** (przykład *:\@P ssw0rd*).
