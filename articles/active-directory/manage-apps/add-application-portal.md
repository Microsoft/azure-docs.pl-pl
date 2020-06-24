---
title: Szybki Start — Dodawanie aplikacji do dzierżawy Azure Active Directory
description: Ten przewodnik Szybki start używa witryny Azure Portal do dodawania aplikacji z galerii do dzierżawy usługi Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: b89ab7e44bc71fa442f332cbba35af450f7d5155
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760664"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-tenant"></a>Szybki start: dodawanie aplikacji do dzierżawy usługi Azure Active Directory

Usługa Azure Active Directory (Azure AD) udostępnia galerię, która zawiera tysiące wstępnie zintegrowanych aplikacji. Niektóre z aplikacji używanych w Twojej organizacji prawdopodobnie znajdują się w galerii. Ten przewodnik Szybki start używa witryny Azure Portal do dodawania aplikacji z galerii do dzierżawy usługi Azure Active Directory (Azure AD).

Po dodaniu aplikacji do dzierżawy usługi Azure AD, można:

- Zarządzanie dostępem użytkowników do aplikacji przy użyciu zasad dostępu warunkowego.
- Konfigurować konta użytkowników do logowania jednokrotnego do aplikacji za pomocą kont usługi Azure AD.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby dodać aplikację do swojej dzierżawy, potrzebujesz:

- Subskrypcji usługi Azure AD
- Subskrypcji z włączonym logowaniem jednokrotnym dla aplikacji

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator globalny dzierżawy usługi Azure AD, administrator aplikacji w chmurze lub administrator aplikacji.

Aby przetestować kroki opisane w tym samouczku, zalecamy użycie środowiska innego niż produkcyjne. Jeśli nie masz nieprodukcyjnego środowiska usługi Azure AD, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-an-application-to-your-azure-ad-tenant"></a>Dodawanie aplikacji do dzierżawy usługi Azure AD

Aby dodać aplikację z galerii do dzierżawy usługi Azure AD:

1. W [Azure Portal](https://portal.azure.com)w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.

2. W okienku **Azure Active Directory** wybierz pozycję **aplikacje dla przedsiębiorstw**. Zostanie otwarte okienko **wszystkie aplikacje** z losową próbką aplikacji w dzierżawie usługi Azure AD.

3. Aby dodać aplikację galerii do dzierżawy, wybierz pozycję **Nowa aplikacja**. 

    ![Wybierz pozycję Nowa aplikacja, aby dodać aplikację galerii do swojej dzierżawy](media/add-application-portal/new-application.png)

 4. Przejdź do nowego środowiska w wersji zapoznawczej galerii: na transparencie w górnej części **strony Dodawanie aplikacji**wybierz link, który znajduje się w **tym miejscu, aby wypróbować nową i udoskonaloną galerię aplikacji**.

5. Zostanie otwarte okienko **Przeglądaj Galerię usługi Azure AD (wersja zapoznawcza)** , w którym są wyświetlane kafelki dla platform chmurowych, aplikacji lokalnych i polecanych aplikacji. Należy zauważyć, że aplikacje wymienione w sekcji **Polecane aplikacje** mają ikony wskazujące, czy obsługują one funkcję federacyjnego logowania jednokrotnego (SSO) i aprowizacji.

    ![Wyszukaj aplikację według nazwy lub kategorii](media/add-application-portal/browse-gallery.png)

6. Możesz przeglądać galerię aplikacji, którą chcesz dodać, lub wyszukać aplikację, wprowadzając jej nazwę w polu wyszukiwania. Następnie wybierz aplikację z wyników. W formularzu można edytować nazwę aplikacji w celu dopasowania jej do potrzeb organizacji. W tym przykładzie zmieniono nazwę na **GitHub-test**.

    ![Pokazuje, jak dodać aplikację z galerii](media/add-application-portal/create-application.png)

7. Wybierz przycisk **Utwórz**. Zostanie wyświetlona strona wprowadzenia z opcjami konfigurowania aplikacji w organizacji.

Zakończono Dodawanie aplikacji. W kolejnych sekcjach pokazano, jak zmienić logo i edytować inne właściwości aplikacji.

## <a name="find-your-azure-ad-tenant-application"></a>Znajdowanie aplikacji dzierżawy usługi Azure AD

Załóżmy, że po przerwie wracasz do konfigurowania swojej aplikacji. Pierwszą rzeczą, którą należy wykonać, jest znalezienie aplikacji.

1. W **[Azure Portal](https://portal.azure.com)** w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.
1. W okienku **Azure Active Directory** wybierz pozycję **aplikacje dla przedsiębiorstw**.
1. Z menu rozwijanego **Typ aplikacji** wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Zastosuj**. Aby dowiedzieć się więcej na temat opcji wyświetlania, zobacz [View tenant applications (Wyświetlanie aplikacji dzierżawy)](view-applications-portal.md).
1. Zostanie wyświetlona lista wszystkich aplikacji w dzierżawie usługi Azure AD. Lista jest przykładem losowym. Aby wyświetlić więcej aplikacji, wybierz pozycję **Pokaż więcej więcej niż** jeden raz.
1. Aby szybko znaleźć aplikację w dzierżawie, wprowadź nazwę aplikacji w polu wyszukiwania i wybierz pozycję **Zastosuj**. Ten przykład umożliwia znalezienie wcześniej dodanej aplikacji usługi GitHub-test.

    ![Pokazuje, jak znaleźć aplikację przy użyciu pola wyszukiwania](media/add-application-portal/find-application.png)

## <a name="configure-user-sign-in-properties"></a>Konfigurowanie właściwości logowania użytkownika

Po znalezieniu aplikacji można ją otworzyć i skonfigurować właściwości aplikacji.

Aby edytować właściwości aplikacji:

1. Wybierz aplikację, aby ją otworzyć.
2. Wybierz pozycję **Właściwości** , aby otworzyć okienko właściwości do edycji.

    ![Wyświetla właściwości ekranu i właściwości aplikacji edytowalnych](media/add-application-portal/edit-properties.png)

3. Poświęć chwilę na zapoznanie się z opcjami logowania. Opcje określają sposób, w jaki użytkownicy przypisani lub nieprzypisane do aplikacji mogą zalogować się do aplikacji. Ponadto opcje określają, czy użytkownik może zobaczyć aplikację w panelu dostępu.

    - **Włączono, aby użytkownicy mogli się zalogować?** Określa, czy użytkownicy przypisani do aplikacji mogą się zalogować.
    - **Czy przypisanie użytkownika jest wymagane?** Określa, czy użytkownicy, którzy nie są przypisani do aplikacji, mogą się zalogować.
    - **Widoczne dla użytkowników?** Określa, czy użytkownicy przypisani do aplikacji mogą ją zobaczyć w panelu dostępu i programie do uruchamiania usługi O365.

4. Poniższe tabele ułatwiają wybranie najlepszych opcji dla potrzeb użytkownika.

   - Zachowanie w przypadku użytkowników **przypisanych**:

       | Ustawienia właściwości aplikacji | | | Środowisko użytkowników przypisanych | |
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

   - Zachowanie w przypadku użytkowników **nieprzypisanych**:

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

## <a name="use-a-custom-logo"></a>Używanie logo niestandardowego

Aby użyć logo niestandardowego:

1. Utwórz logo o rozmiarze 215 x 215 pikseli i zapisz je w formacie PNG.
1. Ponieważ aplikacja została już znaleziona, wybierz aplikację.
1. W lewym okienku wybierz pozycję **Właściwości**.
1. Przekaż logo.
1. Gdy skończysz, wybierz pozycję **Zapisz**. 

    ![Pokazuje, w jaki sposób zmienić logo ze strony właściwości aplikacji](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > Miniatura wyświetlana w tym okienku **Właściwości** nie jest natychmiast aktualizowana. Możesz zamknąć i ponownie otworzyć właściwości, aby wyświetlić zaktualizowaną ikonę.

## <a name="next-steps"></a>Następne kroki

Po dodaniu aplikacji do organizacji usługi Azure AD [Wybierz metodę logowania](what-is-single-sign-on.md#choosing-a-single-sign-on-method) jednokrotnego, której chcesz użyć, i zapoznaj się z odpowiednim artykułem poniżej:

- [Konfigurowanie logowania jednokrotnego opartego na języku SAML](configure-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie logowania jednokrotnego przy użyciu hasła](configure-password-single-sign-on-non-gallery-applications.md)
- [Konfigurowanie połączonego logowania](configure-linked-sign-on.md)
