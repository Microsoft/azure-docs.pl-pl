---
title: Dynamics CRM | Azure Marketplace
description: Konfigurowanie zarządzania potencjalnymi klientami dla programu Dynamics CRM.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: d64c8460f5653f28b96396025f29ea13af15c8c3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416320"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Konfigurowanie zarządzania potencjalnymi klientami dla programu Dynamics CRM w trybie online

W tym artykule opisano sposób konfigurowania programu Dynamics CRM Online do przetwarzania potencjalnych klientów sprzedaży.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania czynności określonych w tym artykule potrzebne są następujące uprawnienia użytkownika:
- Aby zainstalować rozwiązanie, musisz być administratorem w wystąpieniu programu Dynamics CRM Online.
- Musisz być administratorem dzierżawy, aby utworzyć nowe konto usługi dla usługi potencjalnego klienta.

<a name="install-the-solution"></a>Zainstaluj rozwiązanie
--------------------

1.  Pobierz [rozwiązanie Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) i zapisz je lokalnie.

2.  Otwórz program Dynamics CRM Online i przejdź do ustawień.
    >[!NOTE]
    >Jeśli nie widzisz opcji w następnym przechwytywaniu ekranu, nie masz uprawnień, których potrzebujesz.
 
       ![Widok konfiguracji dynamiki](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  Wybierz **polecenie Importuj**, a następnie wybierz rozwiązanie pobrane w kroku 1.
 
    ![Opcja importu dynamiki](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  Zakończ instalację rozwiązania.

## <a name="configure-user-permissions"></a>Konfigurowanie uprawnień użytkowników

Aby zapisać potencjalnych klientów w wystąpieniu programu Dynamics CRM, musisz udostępnić nam konto usługi i skonfigurować uprawnienia do konta.

Aby utworzyć konto usługi i przypisać uprawnienia, należy wykonać następujące czynności. Możesz użyć **usługi Azure Active Directory** lub usługi Office **365**.

### <a name="azure-active-directory"></a>Usługa Azure Active Directory

Zalecamy tę opcję, ponieważ otrzymujesz dodatkową korzyść, ponieważ nigdy nie musisz aktualizować nazwy użytkownika / hasła, aby nadal otrzymywać potencjalnych klientów. Aby użyć opcji Usługi Azure Active Directory, należy podać identyfikator aplikacji, klucz aplikacji i identyfikator katalogu z aplikacji usługi Active Directory.

Aby skonfigurować usługę Azure Active Directory for Dynamics CRM, należy wykonać następujące kroki.

1.  Zaloguj się do [witryny Azure Portal,](https://portal.azure.com/) a następnie wybierz usługę Azure Active Directory.

2.  Wybierz **pozycję Właściwości,** a następnie skopiuj **identyfikator katalogu**. Jest to identyfikacja konta dzierżawy, której potrzebujesz w portalu cloud partner.

    ![Pobierz identyfikator katalogu](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  Wybierz **pozycję Rejestracje aplikacji**, a następnie wybierz pozycję Nowa **rejestracja aplikacji**.
4.  Wpisz nazwę aplikacji.
5.  W obszarze Tekst wybierz pozycję **Aplikacja sieci Web / INTERFEJS API**.
6.  Podaj adres URL. To pole nie jest potrzebne dla potencjalnych klientów, ale jest wymagane do utworzenia aplikacji.
7. Wybierz pozycję **Utwórz**.
8.  Teraz, gdy aplikacja jest zarejestrowana, wybierz **właściwości,** a następnie wybierz **skopiuj identyfikator aplikacji**. Te informacje o połączeniu będą używane w portalu cloud partnerów.
9.  W obszarze Właściwości ustaw aplikację jako Wielodostępną, a następnie wybierz pozycję **Zapisz**.

10. Wybierz **klawisze** i utwórz nowy klucz z ustawioną wartością Czas trwania *Nigdy nie wygaśnie*. Wybierz **pozycję Zapisz,** aby utworzyć klucz. 
11. W menu Klawisze wybierz polecenie **Kopiuj wartość klucza.** Zapisz kopię tej wartości, ponieważ będzie jej potrzebować dla portalu Cloud Partner Portal.
    
    ![Dynamics otrzymuje zarejestrowany klucz](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. Wybierz **pozycję Wymagane uprawnienia,** a następnie wybierz pozycję **Dodaj**. 
13. Wybierz **dynamics CRM Online** jako nowy interfejs API i sprawdź uprawnienia *do programu Access CRM Online jako użytkowników organizacji*.

14. W programie Dynamics CRM przejdź do listy Użytkownicy i wybierz pozycję rozwijana "Włączeni użytkownicy", aby przełączyć się na **użytkowników aplikacji.**
    
    ![Użytkownicy aplikacji](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. Wybierz **pozycję Nowy,** aby utworzyć nowego użytkownika. Wybierz pozycję rozwijana **USER: APPLICATION USER.**
    
    ![Dodaj nowego użytkownika aplikacji](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. W **obszarze Nowy użytkownik**podaj nazwę i adres e-mail, których chcesz użyć z tym połączeniem. Wklej w **identyfikatorze aplikacji** dla aplikacji utworzonej w witrynie Azure portal.

     ![Konfigurowanie nowego użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. Przejdź do sekcji "Ustawienia zabezpieczeń" w tym artykule, aby zakończyć konfigurowanie połączenia dla tego użytkownika.

### <a name="office-365"></a>Office 365

Jeśli nie chcesz korzystać z usługi Azure Active Directory, możesz zarejestrować nowego użytkownika w *centrum administracyjnym usługi Microsoft 365*. Aby kontynuować pozyskiwanie potencjalnych klientów, musisz aktualizować nazwę użytkownika/hasło co 90 dni.

Konfigurowanie programu Office 365 for Dynamics CRM za pomocą następujących czynności: Poniższe kroki.

1. Zaloguj się do [centrum administracyjnego usługi Microsoft 365](https://admin.microsoft.com).

2. Wybierz kafelek **Administrator.**

    ![Administrator usługi Office Online](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. Wybierz **pozycję Dodaj użytkownika**.

    ![Dodawanie użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. Utwórz nowego użytkownika dla usługi modułu zapisującego potencjalnych klienty. Skonfiguruj następujące ustawienia:

    -   Podaj hasło i wyeznańcz opcję "Niech ten użytkownik zmieni hasło podczas pierwszego logowania".
    -   Wybierz "Użytkownik (brak dostępu administratora)" jako rolę dla użytkownika.
    -   Wybierz licencję produktu pokazaną w następnym przechwycie ekranu. Zostanie naliczona opłata za wybraną licencję. Rozwiązanie będzie również współpracować z licencją Dynamics CRM Online Basic.
    
    ![Konfigurowanie uprawnień i licencji użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>Ustawienia zabezpieczeń

Ostatnim krokiem jest umożliwienie użytkownikowi utworzonego do pisania potencjalnych klientów.

1.  Zaloguj się do programu Dynamics CRM online.
2.  W **obszarze Ustawienia**wybierz pozycję **Zabezpieczenia**.
    
    ![Ustawienia zabezpieczeń](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  Wybierz użytkownika utworzonego w **obszarze Uprawnienia użytkownika,** a następnie wybierz pozycję **Zarządzaj rolami użytkowników**. Sprawdź, **czy moduł zapisujący potencjalnych klientów w portalu Microsoft Marketplace** przypisze tę rolę.

    ![Przypisywanie roli użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >Ta rola jest tworzona przez zaimportowane rozwiązanie i ma tylko uprawnienia do zapisywania potencjalnych klientów i śledzenia wersji rozwiązania w celu zapewnienia zgodności.

4.  W obszarze Zabezpieczenia wybierz pozycję **Role zabezpieczeń** i znajdź rolę głównego modułu zapisującego w portalu Microsoft Marketplace.
    
    ![Konfigurowanie modułu zapisującego potencjalnych liderów zabezpieczeń](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. Wybierz kartę **Rekordy podstawowe.** Włącz tworzenie/odczytanie/zapis dla interfejsu użytkownika jednostki użytkownika.

    ![Włącz tworzenie/odczytanie/zapis dla użytkownika](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>Zawijanie

Zakończ konfigurowanie programu Dynamics CRM do zarządzania potencjalnymi klientami, dodając wygenerowane informacje o koncie do portalu cloud partnerów. Przykład:

-   **Identyfikator aplikacji** usługi **Azure Active Directory** - (przykład: *23456052-aaaa-bbbb-8662-1234df56788f),* **identyfikator katalogu** (przykład: *12345678-8af1-4 (przykład:* *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*). **Application Key**
-   **Adres URL** usługi **Office 365** - (przykład: **`https://contoso.crm4.dynamics.com`**), **Nazwa użytkownika** (przykład: **`contoso\@contoso.onmicrosoft.com`**) i **Hasło** (przykład: *P\@ssw0rd*).
