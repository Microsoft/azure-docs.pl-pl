---
title: Wymagania wstępne dotyczące interfejsu API Azure Active Directory raportowania | Microsoft Docs
description: Dowiedz się więcej o wymaganiach wstępnych dotyczących uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00c519ef06637c5193b347f0bbc906c6232a7ca8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532538"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Wymagania wstępne dotyczące uzyskiwania dostępu do interfejsu API Azure Active Directory raportowania

[Interfejsy API raportowania usługi Azure Active Directory (Azure AD)](./concept-reporting-api.md) umożliwiają dostęp programowy do danych za pomocą zestawu interfejsów API opartych na architekturze REST. Te interfejsy API można wywołać z języków programowania i narzędzi.

Interfejs API raportowania [autoryzuje dostęp](../../api-management/api-management-howto-protect-backend-with-aad.md) do internetowych interfejsów API przy użyciu protokołu OAuth.

Aby przygotować dostęp do interfejsu API raportowania, należy:

1. [Przypisywanie ról](#assign-roles)
2. [Wymagania licencyjne](#license-requirements)
3. [Rejestrowanie aplikacji](#register-an-application)
4. [Udzielenie uprawnień](#grant-permissions)
5. [Zbieranie ustawień konfiguracji](#gather-configuration-settings)

## <a name="assign-roles"></a>Przypisywanie ról

Aby uzyskać dostęp do danych raportowania za pośrednictwem interfejsu API, musisz mieć przypisaną jedną z następujących ról:

- Czytelnik zabezpieczeń

- Administrator zabezpieczeń

- Administrator globalny

## <a name="license-requirements"></a>Wymagania licencyjne

Aby można było uzyskać dostęp do raportów logowania dla dzierżawy, dzierżawa usługi Azure AD musi mieć skojarzoną Azure AD — wersja Premium dzierżawę. Azure AD — wersja Premium P1 (lub więcej) jest wymagana do uzyskania dostępu do raportów logowania dla dowolnej dzierżawy usługi Azure AD. Alternatywnie, jeśli typ katalogu jest Azure AD B2C, raporty logowania są dostępne za pośrednictwem interfejsu API bez żadnych dodatkowych wymagań licencyjnych. 


## <a name="register-an-application"></a>Rejestrowanie aplikacji

Rejestracja jest potrzebna, nawet jeśli uzyskujesz dostęp do interfejsu API raportowania przy użyciu skryptu. Rejestracja zwraca identyfikator **aplikacji**, który jest wymagany dla wywołań autoryzacji i umożliwia kodowi odbieranie tokenów.

Aby skonfigurować katalog do uzyskiwania dostępu do interfejsu API raportowania usługi Azure AD, musisz zalogować się do usługi [Azure Portal](https://portal.azure.com) przy użyciu konta administratora platformy Azure, które jest również członkiem roli katalogu **Administrator** globalny w dzierżawie usługi Azure AD.

> [!IMPORTANT]
> Aplikacje działające w ramach poświadczeń z uprawnieniami administratora mogą być bardzo zaawansowane, dlatego należy przechowywać identyfikator i poświadczenia tajne aplikacji w bezpiecznej lokalizacji.
> 

**Aby zarejestrować aplikację usługi Azure AD:**

1. W [okienku Azure Portal](https://portal.azure.com)wybierz pozycję **Azure Active Directory** okienku nawigacji po lewej stronie.
   
    ![Zrzut ekranu Azure Active Directory wybrane z Azure Portal menu.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stronie **Azure Active Directory** wybierz pozycję **Rejestracje aplikacji**.

    ![Zrzut ekranu Rejestracje aplikacji wybrane z menu Zarządzaj.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Na stronie **Rejestracje aplikacji** wybierz pozycję **Nowa rejestracja.**

    ![Zrzut ekranu przedstawia wybraną opcję Nowa rejestracja.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. Strona **Rejestracja** aplikacji:

    ![Zrzut ekranu przedstawia stronę Rejestrowanie aplikacji, na której można wprowadzić wartości w tym kroku.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. W polu **tekstowym** Nazwa wpisz `Reporting API application` .

    b. W **przypadku opcji Obsługiwane konta wpisz**, wybierz pozycję Konta tylko w tej **organizacji.**

    c. W polu **tekstowym Redirect URL**  (Adres URL przekierowania) wybierz pozycję **Web** (Sieć Web) wpisz `https://localhost` .

    d. Wybierz pozycję **Zarejestruj**. 


## <a name="grant-permissions"></a>Udzielenie uprawnień 

W zależności od interfejsu API, do którego chcesz uzyskać dostęp, musisz przyznać aplikacji następujące uprawnienia:  

| Interfejs API | Uprawnienie |
| --- | --- |
| Windows Azure Active Directory | Odczyt danych katalogu |
| Microsoft Graph | Odczytywanie wszystkich danych dziennika inspekcji |

![Zrzut ekranu przedstawiający miejsce, w którym można wybrać pozycję Dodaj uprawnienie w okienku uprawnienia administratora.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

W poniższej sekcji wymieniono kroki dla obu interfejsów API. Jeśli nie chcesz uzyskać dostępu do jednego z interfejsów API, możesz pominąć powiązane kroki.

**Aby udzielić aplikacji uprawnień do korzystania z interfejsów API:**


1. Wybierz **pozycję Uprawnienia interfejsu API,** **a następnie pozycję Dodaj uprawnienie.** 

    ![Zrzut ekranu przedstawia stronę Uprawnienia administratora aplikacji, na której można wybrać pozycję Dodaj uprawnienie.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Na stronie Request API permissions (Żądanie uprawnień **interfejsu API)** znajdź **stronę Support legacy API** Azure Active Directory Graph (Obsługa starszego interfejsu API Azure Active Directory **Graph).** 

    ![Zrzut ekranu przedstawia stronę Żądanie uprawnień klienta, na której można wybrać pozycję Azure Active Directory Graph.](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Na stronie **Wymagane uprawnienia** wybierz pozycję **Uprawnienia** aplikacji, rozwiń pole **wyboru Katalog** **Directory.ReadAll.**  Wybierz **pozycję Dodaj uprawnienia.**

    ![Zrzut ekranu przedstawia stronę Żądanie uprawnień administratora aplikacji, na której można wybrać pozycję Uprawnienia aplikacji.](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Na stronie **Reporting API Application - API Permissions (Aplikacja interfejsu API** raportowania — uprawnienia interfejsu API) wybierz pozycję Grant admin consent **(Przyznaj zgodę administratora).** 

    ![Zrzut ekranu przedstawiający stronę raportowania uprawnień aplikacji interfejsu UŻYTKOWNIKA aplikacji interfejsu UŻYTKOWNIKA, na której można wybrać pozycję U dostęp administratora.](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Uwaga: **Microsoft Graph** jest domyślnie dodawana podczas rejestracji interfejsu API.

    ![Zrzut ekranu przedstawia stronę uprawnień administratora aplikacji, na której można wybrać pozycję Dodaj uprawnienie.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Zbieranie ustawień konfiguracji 

W tej sekcji pokazano, jak pobrać następujące ustawienia z katalogu:

- Nazwa domeny
- Identyfikator klienta
- Klucz tajny klienta

Te wartości są potrzebne podczas konfigurowania wywołań interfejsu API raportowania. 

### <a name="get-your-domain-name"></a>Uzyskiwanie nazwy domeny

**Aby uzyskać nazwę domeny:**

1. W [okienku Azure Portal](https://portal.azure.com)okienku nawigacji po lewej stronie wybierz **pozycję Azure Active Directory**.
   
    ![Zrzut ekranu Azure Active Directory wybrany z Azure Portal menu.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Na stronie **Azure Active Directory** wybierz pozycję **Niestandardowe nazwy domen.**

    ![Zrzut ekranu przedstawia nazwy domen niestandardowych wybrane Azure Active Directory.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Skopiuj nazwę domeny z listy domen.


### <a name="get-your-applications-client-id"></a>Uzyskiwanie identyfikatora klienta aplikacji

**Aby uzyskać identyfikator klienta aplikacji:**

1. W [okienku Azure Portal](https://portal.azure.com)okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**.
   
    ![Zrzut ekranu Azure Active Directory wybrany z Azure Portal menu.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Wybierz aplikację na **stronie Rejestracje** aplikacji.

3. Na stronie aplikacji przejdź do identyfikatora **aplikacji** i wybierz pozycję **Kliknij, aby skopiować .**

    ![Zrzut ekranu przedstawia stronę Raportowanie aplikacji P I, na której można skopiować aplikację.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Pobierz klucz tajny klienta aplikacji
 Unikaj błędów podczas próby uzyskania dostępu do dzienników inspekcji lub logowania przy użyciu interfejsu API.

**Aby uzyskać klucz tajny klienta aplikacji:**

1. W [okienku Azure Portal](https://portal.azure.com)okienku nawigacji po lewej stronie kliknij pozycję **Azure Active Directory**.
   
    ![Zrzut ekranu Azure Active Directory wybrane z Azure Portal menu.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Wybierz aplikację na **stronie Rejestracje** aplikacji.

3.  Wybierz **pozycję Certyfikaty i wpisy tajne** na stronie Aplikacja **interfejsu API,** w sekcji **Klucze tajne** klienta kliknij pozycję + Nowy klucz **tajny klienta.** 

    ![Zrzut ekranu przedstawia stronę & wpisów tajnych, na której można dodać klucz tajny klienta.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Na stronie **Dodawanie tajnego klienta** dodaj:

    a. W polu **tekstowym** Opis wpisz `Reporting API` .

    b. W **przypadku wygaśnięcia wybierz** pozycję Za **2 lata.**

    c. Kliknij pozycję **Zapisz**.

    d. Skopiuj wartość klucza.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Rozwiązywanie problemów z błędami w interfejsie API raportowania

Ta sekcja zawiera listę typowych komunikatów o błędach, które mogą wystąpić podczas uzyskiwania dostępu do raportów aktywności przy użyciu interfejsu API Microsoft Graph api oraz kroki rozwiązywania problemów.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Błąd: Nie można pobrać ról użytkownika z Microsoft Graph

 Zaloguj się do swojego konta przy użyciu obu przycisków logowania w interfejsie użytkownika eksploratora programu Graph, aby uniknąć błędu podczas próby zalogowania się przy użyciu Eksploratora programu Graph. 

![Eksplorator programu Graph](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Błąd: Nie można sprawdzić licencji Premium z Microsoft Graph 

Jeśli ten komunikat o błędzie zostanie wyświetlony podczas próby uzyskania  dostępu do logowania za pomocą Eksploratora programu Graph, wybierz pozycję Modyfikuj uprawnienia poniżej swojego konta na lewym pasku dostępu, a następnie wybierz pozycję **Tasks.ReadWrite** i **Directory.Read.All.** 

![Interfejs użytkownika modyfikowania uprawnień](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Błąd: Dzierżawa nie jest B2C lub dzierżawa nie ma licencji Premium

Uzyskiwanie dostępu do raportów logowania wymaga licencji Azure Active Directory Premium 1 (P1). Jeśli ten komunikat o błędzie jest wyświetlany podczas uzyskiwania dostępu do logowania, upewnij się, że dzierżawa ma licencję usługi Azure AD P1.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Błąd: Dozwolone role nie obejmują użytkownika. 

 Unikaj błędów podczas próby uzyskania dostępu do dzienników inspekcji lub logowania przy użyciu interfejsu API. Upewnij się, że Twoje konto jest częścią roli Czytelnik **zabezpieczeń** lub Czytelnik **raportów** w dzierżawie Azure Active Directory zabezpieczeń.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Błąd: Aplikacja nie ma uprawnienia "Odczyt danych katalogu" w usłudze AAD 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Błąd: Aplikacja nie ma uprawnienia Microsoft Graph API "Odczyt wszystkich danych dziennika inspekcji"

Wykonaj kroki opisane w części Wymagania wstępne, aby uzyskać dostęp do interfejsu [API](howto-configure-prerequisites-for-reporting-api.md) raportowania Azure Active Directory, aby upewnić się, że aplikacja działa z odpowiednim zestawem uprawnień. 

## <a name="next-steps"></a>Następne kroki

* [Pobieranie danych przy użyciu interfejsu API raportowania usługi Azure Active Directory z certyfikatami](tutorial-access-api-with-certificates.md)
* [Dokumentacja interfejsu API inspekcji](/graph/api/resources/directoryaudit) 
* [Dokumentacja interfejsu API raportów działań logowania](/graph/api/resources/signin)