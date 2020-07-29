---
title: 'Szybki Start: Dodawanie aplikacji do dzierżawy usługi Azure Active Directory (Azure AD)'
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
ms.openlocfilehash: 79d80a5b94a753b43715c1ccd8fc2059ff84cfca
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338031"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Szybki Start: Dodawanie aplikacji do dzierżawy usługi Azure Active Directory (Azure AD)

Usługa Azure Active Directory (Azure AD) udostępnia galerię, która zawiera tysiące wstępnie zintegrowanych aplikacji. Wiele aplikacji używanych przez organizację prawdopodobnie znajduje się już w galerii.

Po dodaniu aplikacji do dzierżawy usługi Azure AD, można:

- Skonfiguruj właściwości aplikacji.
- Zarządzanie dostępem użytkowników do aplikacji za pomocą zasad dostępu warunkowego.
- Skonfiguruj Logowanie jednokrotne, aby użytkownicy mogli logować się do aplikacji przy użyciu swoich poświadczeń usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać aplikację do dzierżawy usługi Azure AD, potrzebne są:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- (Opcjonalnie: kończenie [wyświetlania aplikacji](view-applications-portal.md)).

>[!IMPORTANT]
>Zalecamy użycie środowiska nieprodukcyjnego w celu przetestowania kroków opisanych w tym przewodniku Szybki Start.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Dodawanie aplikacji do dzierżawy usługi Azure AD

Aby dodać aplikację do dzierżawy usługi Azure AD:

1. W [Azure Portal](https://portal.azure.com)w lewym panelu nawigacyjnym wybierz pozycję **Azure Active Directory**.
2. W okienku **Azure Active Directory** wybierz pozycję **aplikacje dla przedsiębiorstw**. Zostanie otwarte okienko **wszystkie aplikacje** z losową próbką aplikacji w dzierżawie usługi Azure AD.
3. W okienku **aplikacje dla przedsiębiorstw** wybierz pozycję **Nowa aplikacja**. 
    ![Wybierz pozycję Nowa aplikacja, aby dodać aplikację galerii do swojej dzierżawy](media/add-application-portal/new-application.png)
4. Przejdź do nowego środowiska w wersji zapoznawczej galerii: na transparencie w górnej części **strony Dodawanie aplikacji**wybierz link, który znajduje się w **tym miejscu, aby wypróbować nową i udoskonaloną galerię aplikacji**.
5. Zostanie otwarte okienko **Przeglądaj Galerię usługi Azure AD (wersja zapoznawcza)** , w którym są wyświetlane kafelki dla platform chmurowych, aplikacji lokalnych i polecanych aplikacji. Aplikacje wymienione w sekcji **Polecane aplikacje** mają ikony wskazujące, czy obsługują one funkcję federacyjnego logowania jednokrotnego (SSO) i aprowizacji.
    ![Wyszukaj aplikację według nazwy lub kategorii](media/add-application-portal/browse-gallery.png)
6. Możesz przeglądać galerię aplikacji, którą chcesz dodać, lub wyszukać aplikację, wprowadzając jej nazwę w polu wyszukiwania. Następnie wybierz aplikację z wyników. W formularzu można edytować nazwę aplikacji w celu dopasowania jej do potrzeb organizacji. W tym przykładzie wybrano witrynę GitHub i zmieniono nazwę na **GitHub-test**.
    ![Pokazuje, jak dodać aplikację z galerii](media/add-application-portal/create-application.png)
    >[!TIP]
    >Jeśli szukana aplikacja nie znajduje się w galerii, możesz kliknąć link **Utwórz własną aplikację** , a następnie w obszarze **co chcesz zrobić z aplikacją?** wybierz opcję **Zintegruj każdą inną aplikację, której nie ma w galerii**. Firma Microsoft pracowała już z wieloma deweloperami aplikacji w celu wstępnego skonfigurowania ich do pracy z usługą Azure AD. Są to aplikacje, które są wyświetlane w galerii. Jeśli jednak aplikacja, którą chcesz dodać, nie znajduje się na liście, możesz utworzyć nową, ogólną, niestandardową aplikację, a następnie skonfigurować ją samodzielnie lub przy użyciu wskazówek utworzonych przez dewelopera.
7. Wybierz przycisk **Utwórz**. Zostanie wyświetlona strona wprowadzenia z opcjami konfigurowania aplikacji w organizacji.

Ukończono dodawanie aplikacji. W następnym przewodniku szybki start pokazano, jak zmienić logo i edytować inne właściwości aplikacji.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie aplikacji](add-application-portal-configure.md)
- [Konfigurowanie logowania jednokrotnego](add-application-portal-setup-sso.md)
- [Usuwanie aplikacji](delete-application-portal.md)