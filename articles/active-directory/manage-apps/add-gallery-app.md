---
title: Dodawanie aplikacji Galerii — Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak dodać aplikację z galerii usługi Azure AD do aplikacji platformy Azure dla przedsiębiorstw.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77b1794f5dde4d7e346e45c2da5d24195d69b50e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555191"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Dodawanie aplikacji galerii do organizacji usługi Azure AD

Azure Active Directory (Azure AD) zawiera galerię zawierającą tysiące wstępnie zintegrowanych aplikacji, które są włączone przy użyciu logowania jednokrotnego w przedsiębiorstwie. W tym artykule opisano ogólne kroki dodawania aplikacji z galerii do organizacji usługi Azure AD.

> [!IMPORTANT]
> Najpierw sprawdź, czy aplikacja znajduje się na [liście samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/). Prawdopodobnie znajdziesz wskazówki krok po kroku dotyczące dodawania i konfigurowania aplikacji galerii, którą chcesz dodać.

## <a name="add-a-gallery-application"></a>Dodawanie aplikacji galerii

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny dzierżawy usługi Azure AD, administrator aplikacji w chmurze lub administrator aplikacji.

1. W [Azure Portal](https://portal.azure.com)w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

1. W okienku **Azure Active Directory** wybierz pozycję **aplikacje dla przedsiębiorstw**.

    ![Otwarte aplikacje dla przedsiębiorstw](media/add-gallery-app/open-enterprise-apps.png)


3. Aby dodać aplikację galerii do dzierżawy, wybierz pozycję **Nowa aplikacja**.

    ![Wybierz pozycję Nowa aplikacja, aby dodać aplikację galerii do swojej dzierżawy](media/add-gallery-app/new-application.png)

 4. Przejdź do nowego środowiska w wersji zapoznawczej galerii: na transparencie w górnej części **strony Dodawanie aplikacji**wybierz link, który znajduje się w **tym miejscu, aby wypróbować nową i udoskonaloną galerię aplikacji**.

5. Zostanie otwarte okienko **przeglądanie galerii usługi Azure AD** zawierające kafelki dla platform chmurowych, aplikacji lokalnych i polecanych aplikacji. Należy zauważyć, że aplikacje wymienione w sekcji **Polecane aplikacje** mają ikony wskazujące, czy obsługują one funkcję federacyjnego logowania jednokrotnego (SSO) i aprowizacji.

    ![Wyszukaj aplikację według nazwy lub kategorii](media/add-gallery-app/browse-gallery.png)

6. Przejrzyj galerię aplikacji, którą chcesz dodać, lub Wyszukaj aplikację, wprowadzając jej nazwę w polu wyszukiwania. Następnie wybierz aplikację z wyników. Obowiązkowe W formularzu można edytować nazwę aplikacji, aby odpowiadała potrzebom organizacji.

    ![Pokazuje, jak dodać aplikację z galerii](media/add-gallery-app/create-application.png)

7. Wybierz pozycję **Utwórz**. Zostanie wyświetlona strona wprowadzenia z opcjami konfigurowania aplikacji w organizacji.

## <a name="configure-user-sign-in-properties"></a>Konfigurowanie właściwości logowania użytkownika

1. Wybierz pozycję **Właściwości** , aby otworzyć okienko właściwości do edycji.

    ![Edytuj okienko właściwości](media/add-gallery-app/edit-properties.png)

1. Ustaw następujące opcje, aby określić, jak użytkownicy przypisani lub nieprzypisane do aplikacji mogą zalogować się do aplikacji, a użytkownik może zobaczyć aplikację w panelu dostępu.

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

       | Ustawienia właściwości aplikacji | | | Środowisko użytkowników nieprzypisanych | |
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

1. Aby użyć niestandardowego logo, Utwórz logo o 215 przez 215 pikseli i Zapisz je w formacie PNG. Następnie przejdź do swojego logo i przekaż go.

    ![Zmienianie logo](media/add-gallery-app/change-logo.png)

1. Gdy skończysz, wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

Po dodaniu aplikacji do organizacji usługi Azure AD [Wybierz metodę logowania](what-is-single-sign-on.md#choosing-a-single-sign-on-method) jednokrotnego, której chcesz użyć, i zapoznaj się z odpowiednim artykułem poniżej:

- [Konfigurowanie logowania jednokrotnego opartego na języku SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie logowania jednokrotnego przy użyciu hasła](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie połączonego logowania](configure-linked-sign-on.md)

