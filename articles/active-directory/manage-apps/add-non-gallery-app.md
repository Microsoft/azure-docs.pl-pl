---
title: Dodaj aplikację bez galerii — platforma tożsamości firmy Microsoft | Microsoft Docs
description: Dodaj aplikację spoza galerii do dzierżawy usługi Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5db8aed0a47e7d8d928ef3287010d60efbc5e5da
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200457"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Dodawanie aplikacji nieznajdującej się na liście (bez galerii) do organizacji usługi Azure AD

Oprócz opcji dostępnych w [galerii aplikacji usługi Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)można dodać **aplikację bez galerii**. Możesz dodać dowolną aplikację, która już istnieje w organizacji, lub dowolną aplikację innej firmy od dostawcy, który nie jest jeszcze częścią galerii usługi Azure AD. W zależności od [umowy licencyjnej](https://azure.microsoft.com/pricing/details/active-directory/)dostępne są następujące funkcje:

- Samoobsługowa integracja dowolnej aplikacji, która obsługuje [SAML (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) dostawców tożsamości (zainicjowanych przez program SP lub dostawcy tożsamości)
- Samoobsługowa integracja dowolnej aplikacji sieci Web, która ma stronę logowania opartą na języku HTML przy użyciu [logowania jednokrotnego opartego na hasłach](what-is-single-sign-on.md#password-based-sso)
- Samoobsługowe połączenie aplikacji korzystających z [systemu do obsługi międzydomenowego protokołu zarządzania tożsamościami (standard scim) na potrzeby aprowizacji użytkowników](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Możliwość dodawania linków do dowolnej aplikacji w programie [uruchamiania aplikacji pakietu Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) lub w [panelu dostępu usługi Azure AD](what-is-single-sign-on.md#linked-sign-on)

W tym artykule opisano sposób dodawania aplikacji spoza galerii do aplikacji dla **przedsiębiorstw** w Azure Portal bez pisania kodu. Jeśli zamiast tego szukasz wskazówek dla deweloperów, jak zintegrować aplikacje niestandardowe z usługą Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](../develop/authentication-scenarios.md). Podczas tworzenia aplikacji, która korzysta z nowoczesnego protokołu, takiego jak [OpenID Connect Connect/OAuth](../develop/active-directory-v2-protocols.md) do uwierzytelniania użytkowników, możesz zarejestrować ją na platformie tożsamości firmy Microsoft, korzystając z [Rejestracje aplikacji](../develop/quickstart-register-app.md) środowiska w Azure Portal.

## <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/) przy użyciu konta administratora platformy tożsamości firmy Microsoft.

2. Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **Nowa aplikacja**.

3. (Opcjonalne, ale zalecane) W polu wyszukiwania **Przejrzyj galerię usługi Azure AD** wprowadź nazwę wyświetlaną aplikacji. 

4. Wybierz pozycję **Utwórz własną aplikację**. Zostanie wyświetlona strona **Tworzenie własnej aplikacji** .

   ![Dodawanie aplikacji](media/add-non-gallery-app/create-your-own-application.png)

5. Zacznij wpisywać nazwę wyświetlaną dla nowej aplikacji. Jeśli istnieją aplikacje galerii o podobnych nazwach, zostaną one wyświetlone na liście wyników wyszukiwania.

   > [!NOTE]
   > Jeśli to możliwe, zalecamy używanie wersji galerii aplikacji. Jeśli aplikacja, którą chcesz dodać, pojawia się w wynikach wyszukiwania, wybierz aplikację i pomiń pozostałą część tej procedury.

6. W obszarze **co chcesz zrobić z aplikacją?** wybierz opcję **Zintegruj każdą inną aplikację, która nie znajduje się w galerii**. Ta opcja jest zwykle używana w przypadku aplikacji SAML i WS-karmione.

   > [!NOTE]
   > Pozostałe dwie opcje są używane w następujących scenariuszach:
   >* **Skonfiguruj serwer proxy aplikacji dla bezpiecznego dostępu zdalnego do aplikacji lokalnej** otwiera stronę konfiguracji dla serwer proxy aplikacji usługi Azure AD i łączników platformy Azure.
   >* **Zarejestrowanie aplikacji, nad którą pracujesz, aby zintegrować usługę z usługą Azure AD** , spowoduje otwarcie strony **rejestracje aplikacji** . Ta opcja jest zwykle używana w przypadku aplikacji OpenID Connect Connect.

7. Wybierz pozycję **Utwórz**. Zostanie otwarta strona **Przegląd** aplikacji.

## <a name="configure-user-sign-in-properties"></a>Konfigurowanie właściwości logowania użytkownika

1. Wybierz pozycję **Właściwości** , aby otworzyć okienko właściwości do edycji.

    ![Edytuj okienko właściwości](media/add-non-gallery-app/edit-properties.png)

2. Ustaw następujące opcje, aby określić, jak użytkownicy przypisani lub nieprzypisane do aplikacji mogą zalogować się do aplikacji, a użytkownik może zobaczyć aplikację w panelu dostępu.

    - Opcja **Włączono dla użytkowników w celu logowania się** określa, czy użytkownicy przypisani do aplikacji mogą się logować.
    - **Wymagane przypisanie użytkownika** określa, czy użytkownicy, którzy nie są przypisani do aplikacji, mogą się zalogować.
    - Opcja **Widoczne dla użytkownika** określa, czy użytkownicy przypisani do aplikacji mogą ją widzieć w panelu dostępu i narzędziu do uruchamiania aplikacji usługi O365.

      Zachowanie w przypadku użytkowników **przypisanych**:

       | Właściwość aplikacji | Właściwość aplikacji | Właściwość aplikacji | Środowisko użytkowników przypisanych | Środowisko użytkowników przypisanych |
       |---|---|---|---|---|
       | Włączono dla użytkowników w celu logowania się? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Czy przypisani użytkownicy mogą się logować? | Czy przypisani użytkownicy widzą aplikację?* |
       | tak | tak | tak | tak | tak  |
       | tak | tak | nie  | tak | nie   |
       | tak | nie  | tak | tak | tak  |
       | tak | nie  | nie  | tak | nie   |
       | nie  | tak | tak | nie  | nie   |
       | nie  | tak | nie  | nie  | nie   |
       | nie  | nie  | tak | nie  | nie   |
       | nie  | nie  | nie  | nie  | nie   |

      Zachowanie w przypadku użytkowników **nieprzypisanych**:

       | Właściwość aplikacji | Właściwość aplikacji | Właściwość aplikacji | Środowisko użytkowników nieprzypisanych | Środowisko użytkowników nieprzypisanych |
       |---|---|---|---|---|
       | Włączono, aby użytkownicy mogli się zalogować? | Wymagane przypisanie użytkownika? | Widoczne dla użytkowników? | Czy nieprzypisani użytkownicy mogą się logować? | Czy nieprzypisani użytkownicy widzą aplikację?* |
       | tak | tak | tak | nie  | nie   |
       | tak | tak | nie  | nie  | nie   |
       | tak | nie  | tak | tak | nie   |
       | tak | nie  | nie  | tak | nie   |
       | nie  | tak | tak | nie  | nie   |
       | nie  | tak | nie  | nie  | nie   |
       | nie  | nie  | tak | nie  | nie   |
       | nie  | nie  | nie  | nie  | nie   |

     *Czy użytkownik widzi aplikację w panelu dostępu i narzędziu do uruchamiania aplikacji usługi Office 365?

3. Aby użyć niestandardowego logo, Utwórz logo o 215 przez 215 pikseli i Zapisz je w formacie PNG. Następnie przejdź do swojego logo i przekaż go.

    ![Zmienianie logo](media/add-non-gallery-app/change-logo.png)

4. Gdy skończysz, wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Po dodaniu aplikacji do organizacji usługi Azure AD [Wybierz metodę logowania](what-is-single-sign-on.md#choosing-a-single-sign-on-method) jednokrotnego, której chcesz użyć, i zapoznaj się z odpowiednim artykułem poniżej:

- [Konfigurowanie logowania jednokrotnego opartego na języku SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie logowania jednokrotnego przy użyciu hasła](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie połączonego logowania](configure-linked-sign-on.md)
