---
title: 'Samouczek: Konfigurowanie zapisywania zwrotnego SAP SuccessFactors w Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować zapisywanie zapisu z powrotem w programie SAP SuccessFactors z usługi Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: d39e00a80ab167936a749c73867b4343e6ed9d76
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358817"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Samouczek: Konfigurowanie zapisywania zwrotnego atrybutów z usługi Azure AD do oprogramowania SAP SuccessFactors
Celem tego samouczka jest przedstawienie kroków związanych z zapisywaniem atrybutów z usługi Azure AD do oprogramowania SAP SuccessFactors Employee Central. 

## <a name="overview"></a>Omówienie

Można skonfigurować aplikację do zapisywania zwrotnego SAP SuccessFactors w celu zapisania określonych atrybutów z Azure Active Directory w programie SAP SuccessFactors Employee Central. Aplikacja inicjowania obsługi stornowania SuccessFactors obsługuje Przypisywanie wartości do następujących atrybutów centralnych pracownika:

* Służbowy adres E-mail
* Nazwa użytkownika
* Numer telefonu służbowego (w tym kod kraju, kod obszaru, numer i rozszerzenie)
* Flaga podstawowa numeru telefonu służbowego
* Numer telefonu komórkowego (w tym kod kraju, kod obszaru, numer)
* Flaga podstawowa telefonu komórkowego 
* User custom01 — atrybuty custom15
* loginMethod — atrybut

> [!NOTE]
> Ta aplikacja nie ma żadnej zależności od SuccessFactors przychodzącej aplikacji integracji użytkownika. Można skonfigurować go niezależnie od [SuccessFactors do lokalnej aplikacji do aprowizacji usługi AD](sap-successfactors-inbound-provisioning-tutorial.md) lub [SuccessFactors w aplikacji Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) Provisioning.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Dla kogo to rozwiązanie do aprowizacji użytkowników jest najlepiej dopasowane?

To rozwiązanie SuccessFactors zapisywania zwrotnego użytkownika jest idealnym rozwiązaniem dla:

* Organizacje korzystające z Microsoft 365, które chcą pisać i przywracać atrybuty autorytatywne, zarządzane przez nią (takie jak adres e-mail, telefon, nazwa użytkownika) z powrotem do SuccessFactors Employee Central.

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurowanie SuccessFactors na potrzeby integracji

Wszystkie łączniki inicjowania obsługi SuccessFactors wymagają poświadczeń konta SuccessFactors z właściwymi uprawnieniami do wywoływania interfejsów API dla pracowników centralnych OData. W tej sekcji opisano kroki tworzenia konta usługi w programie SuccessFactors i udzielania odpowiednich uprawnień. 

* [Utwórz/Zidentyfikuj konto użytkownika interfejsu API w SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Utwórz rolę uprawnień interfejsu API](#create-an-api-permissions-role)
* [Tworzenie grupy uprawnień dla użytkownika interfejsu API](#create-a-permission-group-for-the-api-user)
* [Udziel uprawnienia roli do grupy uprawnień](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Utwórz/Zidentyfikuj konto użytkownika interfejsu API w SuccessFactors
Skontaktuj się z zespołem administracyjnym SuccessFactors lub partnerem implementacji, aby utworzyć lub zidentyfikować konto użytkownika w usłudze SuccessFactors, które będzie używane do wywoływania interfejsów API usługi OData. Poświadczenia nazwy użytkownika i hasła tego konta będą wymagane podczas konfigurowania aplikacji aprowizacji w usłudze Azure AD. 

### <a name="create-an-api-permissions-role"></a>Utwórz rolę uprawnień interfejsu API

1. Zaloguj się do platformy SAP SuccessFactors przy użyciu konta użytkownika, które ma dostęp do centrum administracyjnego.
1. Wyszukaj pozycje *Zarządzaj rolami uprawnień* , a następnie wybierz pozycję **Zarządzaj rolami uprawnień** z wyników wyszukiwania.

   ![Zarządzanie rolami uprawnień](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. Na liście rola uprawnień kliknij pozycję **Utwórz nową**.

   > [!div class="mx-imgBorder"]
   > ![Utwórz nową rolę uprawnienia](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Dodaj nazwę i **Opis** **roli** dla nowej roli uprawnienia. Nazwa i opis powinny wskazywać, że rola ma uprawnienia do użycia interfejsu API.

   > [!div class="mx-imgBorder"]
   > ![Szczegóły roli uprawnień](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. W obszarze Ustawienia uprawnień kliknij pozycję **uprawnienie...** , a następnie przewiń w dół listę uprawnień i kliknij pozycję **Zarządzaj narzędzia integracji**. Zaznacz pole wyboru **Zezwalaj administratorowi na dostęp do interfejsu API OData za pomocą uwierzytelniania podstawowego**.

   > [!div class="mx-imgBorder"]
   > ![Zarządzanie narzędziami integracji](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Przewiń w dół do tego samego pola i wybierz pozycję **Employee Central API**. Dodaj uprawnienia, jak pokazano poniżej, aby czytać przy użyciu interfejsu API ODATA i edytować za pomocą interfejsu API ODATA. Wybierz opcję Edytuj, jeśli planujesz używać tego samego konta do scenariusza zapisywania zwrotnego z SuccessFactors. 

   > [!div class="mx-imgBorder"]
   > ![Odczyt uprawnień do zapisu](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Kliknij przycisk **gotowe**. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-a-permission-group-for-the-api-user"></a>Tworzenie grupy uprawnień dla użytkownika interfejsu API

1. W centrum administracyjnym SuccessFactors Wyszukaj pozycję *Zarządzaj grupami uprawnień* , a następnie wybierz pozycję **Zarządzaj grupami uprawnień** z wyników wyszukiwania.

   > [!div class="mx-imgBorder"]
   > ![Zarządzaj grupami uprawnień](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. W oknie Zarządzanie grupami uprawnień kliknij pozycję **Utwórz nowe**.

   > [!div class="mx-imgBorder"]
   > ![Dodaj nową grupę](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Dodaj nazwę grupy dla nowej grupy. Nazwa grupy powinna wskazywać, że grupa jest przeznaczony dla użytkowników interfejsu API.

   > [!div class="mx-imgBorder"]
   > ![Nazwa grupy uprawnień](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Dodaj członków do grupy. Można na przykład wybrać pozycję **Nazwa użytkownika** z menu rozwijanego Pula osób, a następnie wprowadzić nazwę użytkownika konta interfejsu API, która będzie używana do integracji. 

   > [!div class="mx-imgBorder"]
   > ![Dodawanie członków grupy](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. Kliknij pozycję **gotowe** , aby zakończyć tworzenie grupy uprawnień.

### <a name="grant-permission-role-to-the-permission-group"></a>Udziel uprawnienia roli do grupy uprawnień

1. W centrum administracyjnym SuccessFactors Wyszukaj pozycję *Zarządzaj rolami uprawnień* , a następnie wybierz pozycję **Zarządzaj rolami uprawnień** z wyników wyszukiwania.
1. Z **listy rola uprawnień** wybierz rolę utworzoną na potrzeby uprawnień użycia interfejsu API.
1. W obszarze **Przypisz tę rolę do...** kliknij przycisk **Dodaj.** ...
1. Z menu rozwijanego wybierz pozycję **Grupa uprawnień** , a następnie kliknij pozycję **Wybierz...** , aby otworzyć okno grupy, aby wyszukać i wybrać utworzoną powyżej grupę. 

   > [!div class="mx-imgBorder"]
   > ![Dodaj grupę uprawnień](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Przejrzyj uprawnienia przyznane grupie uprawnień. 
   > [!div class="mx-imgBorder"]
   > ![Szczegóły roli i grupy uprawnień](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Kliknij przycisk **Zapisz zmiany**.

## <a name="preparing-for-successfactors-writeback"></a>Przygotowywanie do zapisywania zwrotnego SuccessFactors

Aplikacja do obsługi funkcji zapisywania zwrotnego SuccessFactors używa pewnych wartości *kodu* do ustawiania poczty e-mail i numerów telefonów w centrali. Te wartości *kodu* są ustawiane jako wartości stałe w tabeli mapowania atrybutów i różnią się dla każdego wystąpienia SuccessFactors. Ta sekcja zawiera kroki umożliwiające przechwycenie tych wartości *kodu* .

   > [!NOTE]
   > Aby wykonać kroki opisane w tej sekcji, skontaktuj się z administratorem SuccessFactors. 

### <a name="identify-email-and-phone-number-picklist-names"></a>Identyfikowanie nazw list wyboru adresów E-mail i numerów telefonów 

W programie SAP SuccessFactors *Lista wyboru* jest konfigurowalnym zestawem opcji, z których użytkownik może dokonać wyboru. Różne typy poczty e-mail i numeru telefonu (np. Business, Personal, Other) są reprezentowane przy użyciu listy wyboru. W tym kroku określimy listę wyboru skonfigurowanych w dzierżawie SuccessFactors do przechowywania wartości poczty e-mail i numerów telefonów. 
 
1. W centrum administracyjnym SuccessFactors Wyszukaj pozycję *Zarządzanie konfiguracją biznesową*. 

   > [!div class="mx-imgBorder"]
   > ![Zarządzanie konfiguracją biznesową](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. W obszarze **HRIS elementy** wybierz pozycję **EmailInfo** i kliknij *szczegóły* pola **Typ wiadomości e-mail** .

   > [!div class="mx-imgBorder"]
   > ![Pobierz informacje e-mail](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. Na stronie Szczegóły **typu wiadomości e-mail** Zanotuj nazwę listy wyboru skojarzonej z tym polem. Domyślnie jest to **ecEmailType**. Może się jednak różnić w dzierżawie. 

   > [!div class="mx-imgBorder"]
   > ![Zidentyfikuj listę wyboru wiadomości e-mail](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. W obszarze **HRIS elementy** wybierz pozycję **PhoneInfo** i kliknij *szczegóły* dla pola **Typ telefonu** .

   > [!div class="mx-imgBorder"]
   > ![Uzyskaj informacje o telefonie](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. Na stronie Szczegóły **typu telefonu** Zanotuj nazwę listy wyboru skojarzonej z tym polem. Domyślnie jest to **ecPhoneType**. Może się jednak różnić w dzierżawie. 

   > [!div class="mx-imgBorder"]
   > ![Zidentyfikuj listę wyboru telefonu](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>Pobieranie stałej wartości dla elementu poczty e-mail

1. W centrum administracyjnym SuccessFactors, Wyszukaj i Otwórz *centrum list wyboru*. 
1. Użyj nazwy listy wyboru wiadomości e-mail przechwyconej w poprzedniej sekcji (np. ecEmailType), aby znaleźć listę wyboru wiadomości e-mail. 

   > [!div class="mx-imgBorder"]
   > ![Znajdź listę wyboru typu wiadomości e-mail](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. Otwórz listę wyboru aktywnych wiadomości e-mail. 

   > [!div class="mx-imgBorder"]
   > ![Otwórz listę wyboru typu aktywnej wiadomości e-mail](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. Na stronie listy wyboru typu wiadomości e-mail wybierz pozycję *służbowy* adres e-mail.

   > [!div class="mx-imgBorder"]
   > ![Wybierz typ służbowego adresu e-mail](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. Zanotuj **Identyfikator opcji** skojarzony z *biznesową* pocztą e-mail. Jest to kod, który będzie używany z *wiadomościami e-mailtype* w tabeli mapowania atrybutów.

   > [!div class="mx-imgBorder"]
   > ![Pobierz kod typu adresu e-mail](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > Porzuć znak przecinka podczas kopiowania na wartość. Na przykład jeśli wartość **identyfikatora opcji** to *8 448* , należy ustawić numer *poczty e-mail* w usłudze Azure AD na stałą liczbę *8448* (bez znaku przecinka). 

### <a name="retrieve-constant-value-for-phonetype"></a>Pobieranie stałej wartości dla telefonutype

1. W centrum administracyjnym SuccessFactors, Wyszukaj i Otwórz *centrum list wyboru*. 
1. Użyj nazwy listy wyboru telefonu przechwyconej w poprzedniej sekcji, aby znaleźć listę wyboru telefonu. 

   > [!div class="mx-imgBorder"]
   > ![Znajdź listę wyboru typu telefonu](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. Otwórz listę wyboru aktywnego telefonu. 

   > [!div class="mx-imgBorder"]
   > ![Otwórz listę wyboru aktywnego typu telefonu](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. Na stronie listy wyboru typu telefonu Przejrzyj różne typy telefonów wymienione w obszarze **wartości listy wyboru**.

   > [!div class="mx-imgBorder"]
   > ![Przejrzyj typy telefonów](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. Zanotuj **Identyfikator opcji** skojarzony z telefonem *służbowym* . Jest to kod, który będzie używany z *businessPhoneType* w tabeli mapowania atrybutów.

   > [!div class="mx-imgBorder"]
   > ![Pobierz służbowy kod telefonu](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. Zanotuj **Identyfikator opcji** skojarzony z numerem telefonu *komórkowego* . Jest to kod, który będzie używany z *cellPhoneType* w tabeli mapowania atrybutów.

   > [!div class="mx-imgBorder"]
   > ![Pobierz kod telefonu komórkowego](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > Porzuć znak przecinka podczas kopiowania na wartość. Na przykład jeśli **Identyfikator opcji** ma wartość *10 606* , ustaw *cellPhoneType* w usłudze Azure AD na stałą numer *10606* (bez znaku przecinka). 


## <a name="configuring-successfactors-writeback-app"></a>Konfigurowanie aplikacji zapisywania zwrotnego SuccessFactors

Ta sekcja zawiera kroki dla programu 

* [Dodawanie aplikacji łącznika aprowizacji i Konfigurowanie łączności z usługą SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurowanie mapowań atrybutów](#part-2-configure-attribute-mappings)
* [Włączanie i uruchamianie aprowizacji użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Część 1. Dodawanie aplikacji łącznika aprowizacji i Konfigurowanie łączności z usługą SuccessFactors

**Aby skonfigurować zapisywanie zwrotne SuccessFactors:**

1. Przejdź do strony <https://portal.azure.com>

2. Na lewym pasku nawigacyjnym wybierz pozycję **Azure Active Directory**

3. Wybierz **aplikacje przedsiębiorstwa** , a następnie **wszystkie aplikacje**.

4. Wybierz pozycję **Dodaj aplikację** , a następnie wybierz kategorię **wszystkie** .

5. Wyszukaj funkcję **zapisywania zwrotnego SuccessFactors** i Dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i wyświetleniu ekranu Szczegóły aplikacji wybierz opcję **aprowizacji**

7. Zmień tryb **aprowizacji** **Mode** na **automatyczny**

8. Wypełnij sekcję **poświadczenia administratora** w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta użytkownika interfejsu API SuccessFactors z DOŁĄCZONYm identyfikatorem firmy. Ma format: **username \@ companyID**

   * **Hasło administratora —** Wprowadź hasło dla konta użytkownika interfejsu API SuccessFactors. 

   * **Adres URL dzierżawy —** Wprowadź nazwę punktu końcowego usług interfejsu API OData SuccessFactors. Wprowadź tylko nazwę hosta serwera bez protokołu HTTP lub https. Ta wartość powinna wyglądać następująco: `api4.successfactors.com` .

   * **Wiadomość e-mail z powiadomieniem —** Wprowadź adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".
    > [!NOTE]
    > Usługa Azure AD Provisioning wysyła powiadomienie e-mail, jeśli zadanie aprowizacji przejdzie do stanu [kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md) .

   * Kliknij przycisk **Testuj połączenie** . Jeśli test połączenia zakończy się pomyślnie, kliknij przycisk **Zapisz** u góry. Jeśli to się nie powiedzie, sprawdź, czy poświadczenia i adres URL SuccessFactors są prawidłowe.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Po pomyślnym zapisaniu poświadczeń w sekcji **mapowania** zostanie wyświetlone mapowanie domyślne. Odśwież stronę, jeśli mapowania atrybutów nie są widoczne.  

### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów

W tej sekcji skonfigurujesz sposób przepływu danych przez użytkownika z SuccessFactors do Active Directory.

1. Na karcie aprowizacji w obszarze **mapowania** kliknij pozycję **Udostępnij Azure Active Directory użytkownikom**.

1. W polu **Zakres obiektu źródłowego** możesz wybrać, które zestawy użytkowników w usłudze Azure AD mają być uwzględniane do zapisu przez zdefiniowanie zestawu filtrów opartych na atrybutach. Zakresem domyślnym jest "Wszyscy użytkownicy w usłudze Azure AD". 
   > [!TIP]
   > Podczas konfigurowania aplikacji do aprowizacji po raz pierwszy należy przetestować i zweryfikować mapowania atrybutów oraz wyrażenia, aby upewnić się, że daje żądany wynik. Firma Microsoft zaleca używanie filtrów określania zakresu w obszarze **zakres obiektów źródłowych** do testowania mapowań za pomocą kilku użytkowników testowych z usługi Azure AD. Po sprawdzeniu, czy mapowania działają, można usunąć filtr lub stopniowo rozwijać go, aby uwzględnić więcej użytkowników.

1. Pole **akcji obiektu docelowego** obsługuje tylko operację **aktualizacji** .

1. W tabeli mapowania w obszarze **mapowania atrybutów** można zmapować następujące atrybuty Azure Active Directory na SuccessFactors. Poniższa tabela zawiera wskazówki dotyczące sposobu mapowania atrybutów zapisu. 

   | \# | Atrybut usługi Azure AD | SuccessFactors — atrybut | Uwagi |
   |--|--|--|--|
   | 1 | IDPracownika | personIdExternal | Domyślnie ten atrybut jest zgodnym identyfikatorem. Zamiast IDPracownika można użyć dowolnego innego atrybutu usługi Azure AD, który może przechowywać wartość równą personIdExternal w SuccessFactors.    |
   | 2 | mail (poczta) | poczta e-mail | Mapowanie źródła atrybutów wiadomości e-mail. Do celów testowych można mapować element userPrincipalName na adres e-mail. |
   | 3 | 8448 | adres e-mail | Ta stała wartość jest wartością identyfikatora SuccessFactors skojarzoną z firmową pocztą e-mail. Zaktualizuj tę wartość, aby odpowiadała środowisku SuccessFactors. Zapoznaj się z sekcją [pobieranie stałej wartości parametru emailtype](#retrieve-constant-value-for-emailtype) , aby poznać procedurę ustawiania tej wartości. |
   | 4 | true | emailIsPrimary | Ten atrybut służy do ustawiania firmowej poczty e-mail jako podstawowej w SuccessFactors. Jeśli służbowy adres e-mail nie jest podstawowy, ustaw tę flagę na wartość false. |
   | 5 | userPrincipalName | [custom01 – custom15] | Przy użyciu **Dodaj nowe mapowanie** można opcjonalnie zapisać element userPrincipalName lub dowolny atrybut usługi Azure AD w atrybucie niestandardowym dostępnym w obiekcie użytkownika SuccessFactors.  |
   | 6 | Premium-samAccountName | nazwa użytkownika | Przy użyciu **Dodaj nowe mapowanie** można opcjonalnie zmapować lokalny sAMAccountName na SuccessFactors atrybut username. |
   | 7 | Logowanie jednokrotne | loginMethod | Jeśli dzierżawa SuccessFactors jest skonfigurowana dla [częściowego logowania jednokrotnego](https://apps.support.sap.com/sap/support/knowledge/en/2320766), za pomocą polecenia Dodaj nowe mapowanie można opcjonalnie ustawić loginMethod jako stałą wartość "SSO" lub "PWD". |
   | 8 | telephoneNumber | businessPhoneNumber | To mapowanie służy *do przepływu numeru* telefonu z usługi Azure AD do SuccessFactors służbowego lub służbowego. |
   | 9 | 10605 | businessPhoneType | Ta stała wartość jest wartością identyfikatora SuccessFactors skojarzoną z telefonem służbowym. Zaktualizuj tę wartość, aby odpowiadała środowisku SuccessFactors. Zapoznaj się z sekcją [pobieranie stałej wartości dla telefonutype](#retrieve-constant-value-for-phonetype) , aby poznać procedurę ustawiania tej wartości. |
   | 10 | true | businessPhoneIsPrimary | Ten atrybut służy do ustawiania głównej flagi numeru telefonu służbowego. Prawidłowe wartości to true i false. |
   | 11 | telefon komórkowy | cellPhoneNumber | To mapowanie służy *do przepływu numeru* telefonu z usługi Azure AD do SuccessFactors służbowego lub służbowego. |
   | 12 | 10606 | cellPhoneType | Ta stała wartość jest wartością identyfikatora SuccessFactors skojarzoną z numerem telefonu komórkowego. Zaktualizuj tę wartość, aby odpowiadała środowisku SuccessFactors. Zapoznaj się z sekcją [pobieranie stałej wartości dla telefonutype](#retrieve-constant-value-for-phonetype) , aby poznać procedurę ustawiania tej wartości. |
   | 13 | fałsz | cellPhoneIsPrimary | Ten atrybut służy do ustawiania głównej flagi numeru telefonu komórkowego. Prawidłowe wartości to true i false. |
 
1. Zweryfikuj i Przejrzyj mapowania atrybutów. 
 
    >[!div class="mx-imgBorder"]
    >![Mapowanie atrybutu zapisywania zwrotnego](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. Kliknij przycisk **Zapisz** , aby zapisać mapowania. Następnie zaktualizujemy wyrażenia interfejsu API ścieżki JSON, aby używać kodów telefonutype w wystąpieniu usługi SuccessFactors. 
1. Wybierz pozycję **Pokaż opcje zaawansowane**. 

    >[!div class="mx-imgBorder"]
    >![Pokaż opcje zaawansowane](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Kliknij pozycję **Edytuj listę atrybutów dla SuccessFactors**. 

   > [!NOTE] 
   > Jeśli opcja **Edytuj listę atrybutów dla opcji SuccessFactors** nie jest wyświetlana w Azure Portal, użyj adresu URL, *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* Aby uzyskać dostęp do strony. 

1. Kolumna **wyrażenie interfejsu API** w tym widoku wyświetla wyrażenia ścieżki JSON używane przez łącznik. 
1. Zaktualizuj wyrażenia ścieżki JSON dla telefonu służbowego i telefonu komórkowego, aby użyć wartości identyfikatora ( *businessPhoneType* i *cellPhoneType* ) odpowiadającej Twojemu środowisku. 

    >[!div class="mx-imgBorder"]
    >![Zmiana ścieżki w formacie JSON telefonu](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. Kliknij przycisk **Zapisz** , aby zapisać mapowania.

## <a name="enable-and-launch-user-provisioning"></a>Włączanie i uruchamianie aprowizacji użytkowników

Po zakończeniu konfiguracji aplikacji SuccessFactorsing (Inicjowanie obsługi administracyjnej) można włączyć usługę aprowizacji w Azure Portal.

> [!TIP]
> Domyślnie po włączeniu usługi aprowizacji zostaną zainicjowane operacje aprowizacji dla wszystkich użytkowników w zakresie. Jeśli występują błędy związane z mapowaniem lub błędami danych, zadanie aprowizacji może się nie powieść i przejść do stanu kwarantanny. Aby tego uniknąć, najlepszym rozwiązaniem jest zalecamy skonfigurowanie filtru **zakresu obiektów źródłowych** i przetestowanie mapowań atrybutów z kilkoma użytkownikami testowymi przed uruchomieniem pełnej synchronizacji dla wszystkich użytkowników. Po sprawdzeniu, czy mapowania działają i dają odpowiednie wyniki, można usunąć filtr lub stopniowo rozwijać go, aby uwzględnić więcej użytkowników.

1. Na karcie **aprowizacji** Ustaw **stan aprowizacji** na **włączone**.

1. Wybierz **zakres**. Można wybrać jedną z następujących opcji: 
   * **Synchronizuj wszystkich użytkowników i grupy** : Wybierz tę opcję, jeśli planujesz napisać zamapowane atrybuty wszystkich użytkowników z usługi Azure AD do SuccessFactors, z zastrzeżeniem reguł określania zakresu zdefiniowanego w obszarze **mapowania**  ->  **obiektów źródłowych**. 
   * **Synchronizuj tylko przypisanych użytkowników i grupy** : Wybierz tę opcję, jeśli planujesz napisać zamapowane atrybuty tylko dla użytkowników przypisanych do tej aplikacji w **Application**  ->  **Manage**  ->  opcji menu Zarządzanie **użytkownikami i grupami** aplikacji. Ci użytkownicy podlegają także regułom określania zakresu zdefiniowanym **Mappings** w obszarze  ->  **zakres obiektów źródłowych** mapowania.

   > [!div class="mx-imgBorder"]
   > ![Wybierz zakres zapisywania zwrotnego](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > Aplikacja inicjowania obsługi SuccessFactors zapisywania zwrotnego nie obsługuje "przypisania grupy". Obsługiwane jest tylko "przypisanie użytkownika". 

1. Kliknij przycisk **Zapisz**.

1. Ta operacja rozpocznie synchronizację początkową, która może potrwać zmienną liczbę godzin w zależności od liczby użytkowników w dzierżawie usługi Azure AD i zakresu zdefiniowanego dla tej operacji. Możesz sprawdzić, czy pasek postępu śledzi postęp cyklu synchronizacji. 

1. W dowolnym momencie sprawdź kartę **dzienniki aprowizacji** w Azure Portal, aby zobaczyć, jakie akcje zostały wykonane przez usługę aprowizacji. Dzienniki aprowizacji zawierają listę wszystkich poszczególnych zdarzeń synchronizacji wykonywanych przez usługę aprowizacji. 

1. Po zakończeniu synchronizacji początkowej zostanie zapisany raport z podsumowaniem inspekcji na karcie **aprowizacji** , jak pokazano poniżej.

   > [!div class="mx-imgBorder"]
   > ![Pasek postępu aprowizacji](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Obsługiwane scenariusze, znane problemy i ograniczenia

Zapoznaj się z [sekcją scenariusze zapisywania zwrotnego](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) przewodnika dotyczącego integracji oprogramowania SAP SuccessFactors. 

## <a name="next-steps"></a>Następne kroki

* [Szczegółowe informacje na temat integracji usług Azure AD i SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md)
* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować Logowanie jednokrotne między SuccessFactors i Azure Active Directory](successfactors-tutorial.md)
* [Dowiedz się, jak zintegrować inne aplikacje SaaS z Azure Active Directory](tutorial-list.md)
* [Dowiedz się, jak eksportować i importować konfiguracje aprowizacji](../app-provisioning/export-import-provisioning-configuration.md)