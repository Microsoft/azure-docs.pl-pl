---
title: 'Samouczek: Konfigurowanie zapisywania zwrotnego dla produktu Workday w Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować zapisywanie zwrotne atrybutów z usługi Azure AD do produktu Workday
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: 8c76bddc0fae024b0dd2bdd27d6b1e10d71dec71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90017476"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Samouczek: Konfigurowanie zapisywania zwrotnego atrybutów z usługi Azure AD do produktu Workday
Celem tego samouczka jest wyświetlenie czynności, które należy wykonać w celu przeprowadzenia zapisywania atrybutów zapisu z usługi Azure AD do produktu Workday. Aplikacja do obsługi funkcji zapisywania zwrotnego dla produktu Workday obsługuje Przypisywanie wartości do następujących atrybutów produktu Workday:
* Służbowy adres E-mail 
* Nazwa użytkownika produktu Workday
* Numer telefonu stacjonarnego służbowego (w tym kod kraju, kod obszaru, numer i rozszerzenie)
* Flaga podstawowa służbowego numeru telefonu stacjonarnego
* Numer telefonu służbowego (w tym kod kraju, kod obszaru, numer)
* Flaga podstawowa służbowej aplikacji mobilnej

## <a name="overview"></a>Omówienie

Po skonfigurowaniu integracji przychodzącej aprowizacji przy użyciu aplikacji [Workday do lokalnej usługi AD](workday-inbound-tutorial.md) Provisioning lub [dla produktu Workday w aplikacji Azure AD](workday-inbound-cloud-only-tutorial.md) Provisioning można opcjonalnie skonfigurować aplikację zapisywania zwrotnego w programie Workday w celu zapisania informacji kontaktowych, takich jak służbowy adres e-mail i numer telefonu do dnia roboczego. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Dla kogo to rozwiązanie do aprowizacji użytkowników jest najlepiej dopasowane?

To rozwiązanie do obsługi funkcji zapisywania zwrotnego użytkowników w programie Workday jest idealnym rozwiązaniem dla:

* Organizacje używające Microsoft 365 do zapisywania zwrotnego autorytatywnych atrybutów zarządzanych przez niego (takich jak adres e-mail, nazwa użytkownika i numer telefonu) z powrotem do produktu Workday

## <a name="configure-integration-system-user-in-workday"></a>Konfiguruj użytkownika systemu integracji w programie Workday

Zapoznaj się z sekcją [Konfigurowanie użytkownika systemu integracyjnego](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) na potrzeby tworzenia konta użytkownika integracji z systemem Workday z uprawnieniami do pobierania danych procesu roboczego. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Konfigurowanie zapisywania zwrotnego atrybutów usługi Azure AD w programie Workday

Postępuj zgodnie z tymi instrukcjami, aby skonfigurować Stornowanie adresów e-mail użytkowników i nazwy użytkownika z Azure Active Directory na dzień roboczy.

* [Dodawanie aplikacji łącznika usługi zapisywania zwrotnego i Tworzenie połączenia z produktem Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurowanie mapowań atrybutów zapisywania zwrotnego](#part-2-configure-writeback-attribute-mappings)
* [Włączanie i uruchamianie aprowizacji użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie aplikacji łącznika usługi zapisywania zwrotnego i Tworzenie połączenia z produktem Workday

**Aby skonfigurować łącznik zapisywania zwrotnego dla produktu Workday:**

1. Przejdź do adresu <https://portal.azure.com>.

2. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.

3. Wybierz **aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje**.

4. Wybierz pozycję **Dodaj aplikację**, a następnie wybierz kategorię **wszystkie** .

5. Wyszukaj funkcję **zapisywania zwrotnego dla produktu Workday**i Dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i wyświetleniu ekranu Szczegóły aplikacji wybierz opcję **Inicjowanie obsługi**.

7. Zmień tryb **aprowizacji** **Mode** na **automatyczny**.

8. Wypełnij sekcję **poświadczenia administratora** w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta programu integracji z systemem Workday z dołączoną nazwą domeny dzierżawy. Powinien wyglądać następująco: *username \@ contoso4*

   * **Hasło administratora —** Wprowadź hasło konta systemu integracji produktu Workday

   * **Adres URL dzierżawy —** Wprowadź adres URL punktu końcowego usług sieci Web Workday dla dzierżawy. Ta wartość powinna wyglądać następująco: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources` , gdzie *contoso4* jest zastępowana prawidłową nazwą dzierżawy, a *WD3-Impl* jest zastępowana prawidłowym ciągiem środowiska (w razie potrzeby).

   * **Wiadomość e-mail z powiadomieniem —** Wprowadź adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

   * Kliknij przycisk **Testuj połączenie** . Jeśli test połączenia zakończy się pomyślnie, kliknij przycisk **Zapisz** u góry. Jeśli to się nie powiedzie, sprawdź, czy adres URL i poświadczenia produktu Workday są prawidłowe w programie Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów zapisywania zwrotnego

W tej sekcji skonfigurujesz sposób przepływu atrybutów zapisywania zwrotnego z usługi Azure AD do produktu Workday. 

1. Na karcie Inicjowanie obsługi w obszarze **mapowania**kliknij nazwę mapowania.

2. W polu **Zakres obiektu źródłowego** można opcjonalnie odfiltrować, które zestawy użytkowników w Azure Active Directory powinny być częścią funkcji zapisywania zwrotnego. Zakresem domyślnym jest "Wszyscy użytkownicy w usłudze Azure AD".

3. W sekcji **mapowania atrybutów** zaktualizuj pasujący identyfikator, aby wskazać atrybut w Azure Active Directory, w którym jest przechowywany identyfikator pracownika produktu Workday lub jego identyfikator. Popularną metodą dopasowania jest synchronizacja identyfikatora procesu roboczego Workday lub identyfikatora pracownika do extensionAttribute1-15 w usłudze Azure AD, a następnie użycie tego atrybutu w usłudze Azure AD w celu dopasowania użytkowników do programu w dniach roboczych.

4. Zazwyczaj mapowanie atrybutu *userPrincipalName* usługi Azure AD do atrybutu *UserID* produktu Workday i mapowanie atrybutu *poczty* usługi Azure AD na atrybut *EmailAddress* produktu Workday. 

     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Aby zmapować wartości atrybutów numeru telefonu z usługi Azure AD na dzień roboczy, Skorzystaj z wskazówek przedstawionych poniżej. 

     | Atrybut telefonu Workday | Spodziewana wartość | Wskazówki dotyczące mapowania |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | PRAWDA/FAŁSZ | Mapowanie stałej lub wyrażenia, którego wynikiem jest wartość ciągu "true" lub "false". |
     | WorkphoneLandlineCountryCodeName | [Trzy litery ISO 3166-1 — kod kraju](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Stałe lub mapowanie wyrażenia, którego dane wyjściowe to kod kraju w postaci trzech liter. |
     | WorkphoneLandlineCountryCodeNumber | [Międzynarodowy kod wywołujący](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Stałe lub mapowanie wyrażenia, którego dane wyjściowe są prawidłowym kodem kraju (bez znaku +). |
     | WorkphoneLandlineNumber | Pełny numer telefonu obejmujący kod obszaru | Mapuj na numer *telefonu* . Użyj wyrażenia regularnego, aby usunąć odstępy, nawiasy i kod kraju. Zobacz przykład poniżej. |
     | WorkphoneLandlineExtension | Numer rozszerzenia | Jeśli numer *telefonu* zawiera rozszerzenie, użyj wyrażenia regularnego do wyodrębnienia wartości. |
     | WorkphoneMobileIsPrimary | PRAWDA/FAŁSZ | Mapowanie stałe lub mapowanie wyrażenia, którego wynikiem jest wartość "true" lub "false" |
     | WorkphoneMobileCountryCodeName | [Trzy litery ISO 3166-1 — kod kraju](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Stałe lub mapowanie wyrażenia, którego dane wyjściowe to kod kraju w postaci trzech liter. |
     | WorkphoneMobileCountryCodeNumber | [Międzynarodowy kod wywołujący](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Stałe lub mapowanie wyrażenia, którego dane wyjściowe są prawidłowym kodem kraju (bez znaku +). |
     | WorkphoneMobileNumber | Pełny numer telefonu obejmujący kod obszaru | Mapowanie na atrybut *Mobile* . Użyj wyrażenia regularnego, aby usunąć odstępy, nawiasy i kod kraju. Zobacz przykład poniżej. |

     > [!NOTE]
     > Podczas wywoływania usługi sieci Web Change_Work_Contact Workday usługa Azure AD wysyła następujące wartości stałe: <br>
     > * **Communication_Usage_Type_ID** jest ustawiony na ciąg stałej "Work" <br>
     > * **Phone_Device_Type_ID** jest ustawiony na stały ciąg "Mobile" dla numerów telefonów komórkowych i "stacjonarnych" numerów telefonów stacjonarnych. <br>
     > 
     > Wystąpią błędy zapisywania zwrotnego, jeśli dzierżawa w programie Workday będzie używać różnych Type_IDs. Aby uniknąć takich niepowodzeń, można użyć zadania **obsługi identyfikatorów odwołań** w dniach roboczych i zaktualizować Type_IDs tak, aby odpowiadały wartościom używanym przez usługę Azure AD. <br>
     >  

     **Wyrażenia wyrażeń regularnych odwołania — przykład 1**

     Użyj poniższego wyrażenia regularnego, jeśli numer telefonu w usłudze Azure AD jest ustawiony przy użyciu formatu wymaganego do samoobsługowego resetowania hasła (SSPR). <br>
     Przykład: Jeśli wartość numeru telefonu to + 1 1112223333 — > następnie wyrażenie regularne będzie wyprowadzać dane wyjściowe 1112223333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Wyrażenia wyrażeń regularnych — przykład 2**

     Użyj poniższego wyrażenia regularnego, jeśli numer telefonu w usłudze Azure AD jest ustawiony przy użyciu formatu (XXX) XXX-XXXX. <br>
     Przykład: Jeśli wartość numeru telefonu to (111) 222-3333 — > następnie wyrażenie regularne będzie wyprowadzać dane wyjściowe 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. Aby zapisać mapowania, kliknij pozycję **Zapisz** w górnej części sekcji Attribute-Mapping.

## <a name="enable-and-launch-user-provisioning"></a>Włączanie i uruchamianie aprowizacji użytkowników

Po zakończeniu konfiguracji aplikacji inicjowania obsługi dla programu Workday można włączyć usługę aprowizacji w Azure Portal.

> [!TIP]
> Domyślnie po włączeniu usługi aprowizacji zostaną zainicjowane operacje aprowizacji dla wszystkich użytkowników w zakresie. Jeśli występują błędy w mapowaniu lub problemach z danymi w dniach roboczych, zadanie aprowizacji może się nie powieść i przejść do stanu kwarantanny. Aby tego uniknąć, najlepszym rozwiązaniem jest zalecamy skonfigurowanie filtru **zakresu obiektów źródłowych** i przetestowanie mapowań atrybutów z kilkoma użytkownikami testowymi przed uruchomieniem pełnej synchronizacji dla wszystkich użytkowników. Po sprawdzeniu, czy mapowania działają i dają odpowiednie wyniki, można usunąć filtr lub stopniowo rozwijać go, aby uwzględnić więcej użytkowników.

1. Na karcie **aprowizacji** Ustaw **stan aprowizacji** na **włączone**.

2. Kliknij przycisk **Zapisz**.

3. Ta operacja rozpocznie synchronizację początkową, która może potrwać zmienną liczbę godzin w zależności od liczby użytkowników w katalogu źródłowym. Możesz sprawdzić, czy pasek postępu śledzi postęp cyklu synchronizacji. 

4. W dowolnym momencie sprawdź kartę **dzienniki inspekcji** w Azure Portal, aby zobaczyć, jakie akcje zostały wykonane przez usługę aprowizacji. W dziennikach inspekcji są wyświetlane wszystkie zdarzenia synchronizacji wykonywane przez usługę aprowizacji, takie jak użytkownicy zaimportowani ze źródła i eksportowani do aplikacji docelowej.  

5. Po zakończeniu synchronizacji początkowej zostanie zapisany raport podsumowujący na karcie **aprowizacji** , jak pokazano poniżej.

     > [!div class="mx-imgBorder"]
     > ![Pasek postępu aprowizacji](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować Logowanie jednokrotne między produktem Workday i Azure Active Directory](workday-tutorial.md)
* [Dowiedz się, jak zintegrować inne aplikacje SaaS z Azure Active Directory](tutorial-list.md)
* [Dowiedz się, jak eksportować i importować konfiguracje aprowizacji](../app-provisioning/export-import-provisioning-configuration.md)

