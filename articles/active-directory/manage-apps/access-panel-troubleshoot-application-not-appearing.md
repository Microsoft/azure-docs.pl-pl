---
title: Przypisana aplikacja nie jest wyświetlana na panelu dostępu | Microsoft Docs
description: Rozwiązywanie problemów z niewyświetlaniem aplikacji w panelu dostępu
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/09/2018
ms.author: kenwith
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69f5196484b841e8f0de72ce52ae48e00963f6f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760999"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Przypisana aplikacja nie jest wyświetlana na panelu dostępu

Panel dostępu jest portalem opartym na sieci Web, który umożliwia użytkownikowi z kontem służbowym w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD udzielił im dostępu. Te aplikacje są konfigurowane w imieniu użytkownika w portalu usługi Azure AD. Aplikacja musi być poprawnie skonfigurowana i przypisana do użytkownika lub grupy, do której należy użytkownik, aby wyświetlić aplikację w panelu dostępu.

Typ aplikacji widocznych dla użytkownika można podzielić na następujące kategorie:

-   Aplikacje pakietu Office 365

-   Aplikacje firmy Microsoft i innych firm skonfigurowane z logowaniem jednokrotnym opartym na Federacji

-   Aplikacje logowania jednokrotnego oparte na hasłach

-   Aplikacje z istniejącymi rozwiązaniami logowania jednokrotnego

## <a name="general-issues-to-check-first"></a>Ogólne problemy do sprawdzenia w pierwszej kolejności

-   Jeśli aplikacja została właśnie dodana do użytkownika, spróbuj zalogować się i ponownie w panelu dostępu użytkownika za kilka minut, aby sprawdzić, czy aplikacja została dodana.

-   Jeśli licencja została właśnie usunięta z użytkownika lub grupy, użytkownik jest członkiem tego, że może zająć dużo czasu, w zależności od rozmiaru i złożoności grupy w celu wprowadzenia zmian. Zezwalaj na dodatkowy czas przed zalogowaniem się do panelu dostępu.

## <a name="problems-related-to-application-configuration"></a>Problemy związane z konfiguracją aplikacji

Aplikacja może nie być wyświetlana w panelu dostępu użytkownika, ponieważ nie jest prawidłowo skonfigurowana. Poniżej przedstawiono kilka sposobów rozwiązywania problemów związanych z konfiguracją aplikacji:

-   [Jak skonfigurować federacyjne Logowanie jednokrotne dla aplikacji w galerii usługi Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Jak skonfigurować federacyjne Logowanie jednokrotne dla aplikacji spoza galerii](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Jak skonfigurować hasło logowania jednokrotnego dla aplikacji w galerii usługi Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Jak skonfigurować hasło logowania jednokrotnego dla aplikacji spoza galerii](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować federacyjne Logowanie jednokrotne dla aplikacji w galerii usługi Azure AD

Wszystkie aplikacje w galerii usługi Azure AD z włączoną funkcją logowania jednokrotnego dla firm mają dostępny samouczek krok po kroku. Aby uzyskać szczegółowe wskazówki krok po kroku, możesz uzyskać dostęp do [listy samouczków dotyczących integrowania aplikacji SaaS z Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) .

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (adres URL logowania, identyfikator, adres URL odpowiedzi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobierz metadane i certyfikat usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurowanie wartości metadanych usługi Azure AD w aplikacji (adres URL logowania, wystawca, adres URL wylogowania i certyfikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  W polu tekstowym **Wprowadź nazwę** w sekcji **Dodaj z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

8.  Przed dodaniem aplikacji można zmienić jej nazwę z pola tekstowego **Nazwa** .

9.  Kliknij przycisk **Dodaj** , aby dodać aplikację.

Po krótkim czasie będzie możliwe wyświetlenie okienka konfiguracja aplikacji.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurowanie logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Wybierz pozycję **Logowanie oparte** na protokole SAML z listy rozwijanej **tryb** .

9. Wprowadź wymagane wartości w polu **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako logowanie jednokrotne zainicjowane przez usługę SP, adres URL logowania to wymagana wartość. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

   2. Aby skonfigurować aplikację jako zainicjowaną przez dostawcy tożsamości Logowanie jednokrotne, adres URL odpowiedzi to wymagana wartość. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

10. **Opcjonalne:** aby wyświetlić wartości niewymagane, kliknij pozycję **Pokaż zaawansowane ustawienia adresu URL** .

11. W polu **atrybuty użytkownika**wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** .

12. **Opcjonalnie:** kliknij przycisk **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

    Aby dodać atrybut:

    1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

13. Kliknij pozycję **Konfiguruj &lt; nazwę &gt; aplikacji** , aby uzyskać dostęp do dokumentacji dotyczącej sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz adresy URL metadanych i certyfikat wymagany do skonfigurowania logowania jednokrotnego w aplikacji.

14. Kliknij przycisk **Zapisz** , aby zapisać konfigurację.

15. Przypisz użytkowników do aplikacji.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub dodać atrybuty użytkownika, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, którą chcesz wyświetlić w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. W sekcji **atrybuty użytkownika** wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** . Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE] 
   >Usługa Azure AD wybiera format atrybutu NameID (identyfikator użytkownika) w oparciu o wybraną wartość lub format żądany przez aplikację w elemencie SAML AuthRequest. Aby uzyskać więcej informacji, zapoznaj się z artykułem Logowanie jednokrotne [protokołu SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij przycisk **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

   Aby dodać atrybut:

   1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **Zapisz.** W tabeli zostanie wyświetlony nowy atrybut.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane lub certyfikat usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **certyfikat podpisywania SAML** , a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga skonfigurowania logowania jednokrotnego, zobaczysz opcję pobrania XML metadanych lub certyfikatu.

   Usługa Azure AD nie udostępnia adresu URL do pobrania metadanych. Metadane można pobrać tylko jako plik XML.

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować federacyjne Logowanie jednokrotne dla aplikacji spoza galerii

Aby skonfigurować aplikację spoza galerii, musisz mieć usługę Azure AD w wersji Premium, a aplikacja obsługuje protokół SAML 2,0. Aby uzyskać więcej informacji na temat wersji usługi Azure AD, zobacz [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (adres URL logowania, identyfikator, adres URL odpowiedzi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobierz metadane i certyfikat usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurowanie wartości metadanych usługi Azure AD w aplikacji (adres URL logowania, wystawca, adres URL wylogowania i certyfikat)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (adres URL logowania, identyfikator, adres URL odpowiedzi)

Aby skonfigurować Logowanie jednokrotne dla aplikacji, która nie znajduje się w galerii usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6. Kliknij pozycję **aplikacja niebędąca galerią** w sekcji **Dodaj własną aplikację** .

7. Wprowadź nazwę aplikacji w polu tekstowym **Nazwa** .

8. Kliknij przycisk **Dodaj** , aby dodać aplikację.

9. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

10. Wybierz pozycję **Logowanie oparte** na protokole SAML na liście rozwijanej **tryb** .

11. Wprowadź wymagane wartości w polu **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

    1. Aby skonfigurować aplikację jako zainicjowaną przez dostawcy tożsamości, wprowadź adres URL odpowiedzi i identyfikator.

    2.  **Opcjonalne:** Aby skonfigurować aplikację jako logowanie jednokrotne zainicjowane przez usługę SP, adres URL logowania to wymagana wartość.

12. W polu **atrybuty użytkownika**wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** .

13. **Opcjonalnie:** kliknij przycisk **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

    Aby dodać atrybut:

    1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

14. Kliknij pozycję **Konfiguruj &lt; nazwę &gt; aplikacji** , aby uzyskać dostęp do dokumentacji dotyczącej sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz adresy URL i certyfikaty usługi Azure AD wymagane dla aplikacji.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub dodać atrybuty użytkownika, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. W sekcji **atrybuty użytkownika** wybierz unikatowy identyfikator dla użytkowników na liście rozwijanej **Identyfikator użytkownika** . Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE] 
   >Usługa Azure AD wybiera format atrybutu NameID (identyfikator użytkownika) w oparciu o wybraną wartość lub format żądany przez aplikację w elemencie SAML AuthRequest. Aby uzyskać więcej informacji, zapoznaj się z artykułem Logowanie jednokrotne [protokołu SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij przycisk **Wyświetl i edytuj wszystkie inne atrybuty użytkownika** , aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownik loguje się.

   Aby dodać atrybut:

   1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane lub certyfikat usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której skonfigurowano Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **certyfikat podpisywania SAML** , a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga skonfigurowania logowania jednokrotnego, zobaczysz opcję pobrania XML metadanych lub certyfikatu.

Usługa Azure AD nie udostępnia adresu URL do pobrania metadanych. Metadane można pobrać tylko jako plik XML.

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować Logowanie jednokrotne przy użyciu hasła dla aplikacji z galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  W polu tekstowym **Wprowadź nazwę** w sekcji **Dodaj z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

8.  Przed dodaniem aplikacji można zmienić jej nazwę z pola tekstowego **Nazwa** .

9.  Kliknij przycisk **Dodaj** , aby dodać aplikację.

Po krótkim czasie zobaczysz okienko Konfiguracja aplikacji.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8. Wybierz pozycję **Logowanie oparte na haśle.**

9. [Przypisz użytkowników do aplikacji](#how-to-assign-a-user-to-an-application-directly).

10. Dodatkowo można także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając opcję **zaktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować Logowanie jednokrotne przy użyciu hasła dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, należy wykonać następujące:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [Azure Portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**.

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij przycisk **Dodaj** w prawym górnym rogu w okienku aplikacje dla **przedsiębiorstw** .

6.  Kliknij pozycję **aplikacja niebędąca galerią.**

7.  Wprowadź nazwę aplikacji w polu tekstowym **Nazwa** . Wybierz pozycję **Dodaj.**

Po krótkim czasie będzie możliwe wyświetlenie okienka konfiguracja aplikacji.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować Logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

    1.  Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6.  Wybierz aplikację, dla której chcesz skonfigurować Logowanie jednokrotne.

7.  Po załadowaniu aplikacji kliknij pozycję **Logowanie** jednokrotne w menu nawigacji po lewej stronie aplikacji.

8.  Wybierz pozycję **Logowanie oparte na haśle.**

9.  Wprowadź **adres URL logowania**. Jest to adres URL, pod którym użytkownicy wprowadzają nazwę użytkownika i hasło, aby się zalogować. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. [Przypisz użytkowników do aplikacji](#how-to-assign-a-user-to-an-application-directly).

11. Dodatkowo można także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając opcję **zaktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy otrzymają monit o wprowadzenie poświadczeń podczas uruchamiania.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemy związane z przypisywaniem aplikacji do użytkowników

Użytkownik może nie wyświetlać aplikacji w panelu dostępu, ponieważ nie są przypisani do aplikacji. Poniżej przedstawiono kilka sposobów sprawdzenia:

-   [Sprawdź, czy użytkownik jest przypisany do aplikacji](#check-if-a-user-is-assigned-to-the-application)

-   [Jak bezpośrednio przypisać użytkownika do aplikacji](#how-to-assign-a-user-to-an-application-directly)

-   [Sprawdź, czy użytkownik jest przypisany do licencji powiązanej z aplikacją](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Jak przypisać licencję do użytkownika](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Sprawdź, czy użytkownik jest przypisany do aplikacji

Aby sprawdzić, czy użytkownik jest przypisany do aplikacji, wykonaj poniższe czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

6. **Wyszukaj** nazwę aplikacji.

7. Kliknij pozycję **Użytkownicy i grupy**.

8. Sprawdź, czy użytkownik jest przypisany do aplikacji.

   * Jeśli nie, wykonaj kroki opisane w sekcji "Jak przypisać użytkownika do aplikacji bezpośrednio".

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak bezpośrednio przypisać użytkownika do aplikacji

Aby bezpośrednio przypisać co najmniej jednego użytkownika do aplikacji, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny**.

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** na górze listy **Użytkownicy i grupy** , aby otworzyć okienko **Dodaj przypisanie** .

9. Kliknij selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wpisz **pełną nazwę** lub **adres e-mail** użytkownika, którego chcesz przypisać, w polu Wyszukaj **według nazwy lub adresu e-mail** .

11. Umieść kursor nad **użytkownikiem** na liście, aby odsłonić **pole wyboru**. Kliknij pole wyboru obok zdjęcia lub logo profilu użytkownika, aby dodać użytkownika do **wybranej** listy.

12. **Opcjonalne:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, wpisz inną **pełną nazwę** lub **adres e-mail** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz** , aby dodać do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **roli wybierz** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych użytkowników.

15. Kliknij przycisk **Przypisz** , aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrane przez Ciebie użytkownicy będą mogli uruchamiać te aplikacje w panelu dostępu.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Sprawdź, czy użytkownik jest objęty licencją powiązaną z aplikacją

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5. Kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7. Kliknij pozycję **licencje** , aby zobaczyć, które licencje aktualnie przypisane do użytkownika.

   * Jeśli użytkownik jest przypisany do licencji pakietu Office, spowoduje to, że aplikacje pakietu Office w pierwszej kolejności będą widoczne w panelu dostępu użytkownika.

### <a name="how-to-assign-a-user-a-license"></a>Jak przypisać licencję użytkownika 

Aby przypisać licencję do użytkownika, wykonaj następujące czynności:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7.  Kliknij pozycję **licencje** , aby zobaczyć, które licencje aktualnie przypisane do użytkownika.

8.  Kliknij przycisk **Przypisz** .

9.  Wybierz **co najmniej jeden produkt** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij element **Opcje przypisania** , aby szczegółowo przypisać produkty. Po zakończeniu kliknij przycisk **OK** .

11. Kliknij przycisk **Przypisz** , aby przypisać te licencje temu użytkownikowi.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemy związane z przypisywaniem aplikacji do grup

Użytkownik może wyświetlić aplikację w panelu dostępu, ponieważ są one częścią grupy, do której przypisano aplikację. Poniżej przedstawiono kilka sposobów sprawdzenia:

-   [Sprawdź członkostwo w grupach użytkowników](#check-a-users-group-memberships)

-   [Jak przypisać aplikację do grupy bezpośrednio](#how-to-assign-an-application-to-a-group-directly)

-   [Sprawdź, czy użytkownik jest częścią grupy przypisanej do licencji](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Jak przypisać licencję do grupy](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Sprawdź członkostwo w grupach użytkowników

Aby sprawdzić członkostwo grupy, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5. Kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7. Kliknij pozycję **grupy**.

8. Sprawdź, czy użytkownik jest częścią grupy przypisanej do aplikacji.

   * Jeśli chcesz usunąć użytkownika z grupy, **kliknij wiersz** grupy i wybierz pozycję Usuń.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Jak przypisać aplikację do grupy bezpośrednio

Aby bezpośrednio przypisać jedną lub więcej grup do aplikacji, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny**.

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** w menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** na górze listy **Użytkownicy i grupy** , aby otworzyć okienko **Dodaj przypisanie** .

9. Kliknij selektor **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

10. Wpisz **pełną nazwę** grupy, którą chcesz przypisać, w polu Wyszukaj **według nazwy lub adresu e-mail** .

11. Umieść kursor nad **grupą** na liście, aby odsłonić **pole wyboru**. Kliknij pole wyboru obok zdjęcia lub logo profilu grupy, aby dodać użytkownika do **wybranej** listy.

12. **Opcjonalne:** Jeśli chcesz **dodać więcej niż jedną grupę**, wpisz inną **pełną nazwę grupy** w polu **Wyszukaj według nazwy lub adresu e-mail** , a następnie kliknij pole wyboru, aby dodać tę grupę do **wybranej** listy.

13. Po zakończeniu wybierania grup kliknij przycisk **Wybierz** , aby dodać do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **roli wybierz** w okienku **Dodaj przypisanie** , aby wybrać rolę, która ma zostać przypisana do wybranych grup.

15. Kliknij przycisk **Przypisz** , aby przypisać aplikację do wybranych grup.

Po krótkim czasie wybrane przez Ciebie użytkownicy będą mogli uruchamiać te aplikacje w panelu dostępu.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Sprawdź, czy użytkownik jest częścią grupy przypisanej do licencji

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5. Kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7. Kliknij pozycję **grupy**.

8. Kliknij wiersz konkretnej grupy.

9. Kliknij pozycję **licencje** , aby sprawdzić, które licencje zostały przypisane do grupy.

   * Jeśli grupa jest przypisana do licencji pakietu Office, może to spowodować, że niektóre aplikacje pakietu Office w pierwszej kolejności będą widoczne w panelu dostępu użytkownika.

### <a name="how-to-assign-a-license-to-a-group"></a>Jak przypisać licencję do grupy

Aby przypisać licencję do grupy, wykonaj następujące czynności:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **wszystkie grupy**.

6.  **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby wybrać.

7.  Kliknij pozycję **licencje** , aby sprawdzić, które licencje zostały przypisane do grupy.

8.  Kliknij przycisk **Przypisz** .

9.  Wybierz **co najmniej jeden produkt** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij element **Opcje przypisania** , aby szczegółowo przypisać produkty. Po zakończeniu kliknij przycisk **OK** .

11. Kliknij przycisk **Przypisz** , aby przypisać te licencje do tej grupy. Może to zająć dużo czasu, w zależności od rozmiaru i złożoności grupy.

>[!NOTE]
>Aby to przyspieszyć, należy rozważyć tymczasowe przypisanie licencji bezpośrednio do użytkownika. 
>
>

## <a name="next-steps"></a>Następne kroki
[Dodawanie nowych użytkowników do usługi Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

