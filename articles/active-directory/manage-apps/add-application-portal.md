---
title: 'Szybki Start: Dodawanie aplikacji do dzierżawy usługi Azure Active Directory (Azure AD)'
description: Ten przewodnik Szybki start używa witryny Azure Portal do dodawania aplikacji z galerii do dzierżawy usługi Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: 2d3b0be88a23bbbb0573b4d11a2e5a39dc6254da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259071"
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
4. Przejdź do nowego środowiska w wersji zapoznawczej galerii: na transparencie w górnej części **strony Dodawanie aplikacji** wybierz link, który znajduje się w **tym miejscu, aby wypróbować nową i udoskonaloną galerię aplikacji**.
5. Zostanie otwarte okienko **Przeglądaj Galerię usługi Azure AD (wersja zapoznawcza)** , w którym są wyświetlane kafelki dla platform chmurowych, aplikacji lokalnych i polecanych aplikacji. Aplikacje wymienione w sekcji **Polecane aplikacje** mają ikony wskazujące, czy obsługują one funkcję federacyjnego logowania jednokrotnego (SSO) i aprowizacji. 
    ![Wyszukaj aplikację według nazwy lub kategorii](media/add-application-portal/browse-gallery.png)
6. Możesz przeglądać galerię aplikacji, którą chcesz dodać, lub wyszukać aplikację, wprowadzając jej nazwę w polu wyszukiwania. Następnie wybierz aplikację z wyników. 
7. Następny krok zależy od sposobu, w jaki Deweloper aplikacji zaimplementował Logowanie jednokrotne (SSO). Logowanie jednokrotne może być implementowane przez deweloperów aplikacji na cztery sposoby. Cztery sposoby to SAML, OpenID Connect Connect, Password i Linked. Po dodaniu aplikacji można wybrać filtrowanie i wyświetlanie tylko aplikacji przy użyciu określonej implementacji logowania jednokrotnego, jak pokazano na zrzucie ekranu. Na przykład popularnym standardem do implementowania logowania jednokrotnego jest nazywana SAML (SAML). Innym popularnym standardem jest nazywa się OpenID Connect Connect (OIDC). Sposób konfigurowania logowania jednokrotnego przy użyciu tych standardów jest różny, więc Zanotuj Typ logowania jednokrotnego, który jest implementowany przez dodawaną aplikację.

    :::image type="content" source="media/add-application-portal/sso-types.png" alt-text="Zrzut ekranu przedstawia Selektor typów logowania jednokrotnego." lightbox="media/add-application-portal/sso-types.png":::

    - Jeśli deweloper aplikacji użył **standardu OIDC** na potrzeby rejestracji jednokrotnej, wybierz pozycję **Utwórz konto**. Zostanie wyświetlona strona konfiguracji. Następnie przejdź do przewodnika Szybki Start dotyczącego konfigurowania logowania jednokrotnego opartego na usłudze OIDC.
    :::image type="content" source="media/add-application-portal/sign-up-oidc-sso.png" alt-text="Zrzut ekranu przedstawia Dodawanie aplikacji SSO opartej na OIDC.":::

    - Jeśli deweloper aplikacji użył protokołu **SAML Standard** dla logowania jednokrotnego, wybierz pozycję **Utwórz**. Zostanie wyświetlona strona wprowadzenia z opcjami konfigurowania aplikacji w organizacji. W formularzu można edytować nazwę aplikacji w celu dopasowania jej do potrzeb organizacji. Następnie przejdź do przewodnika Szybki Start dotyczącego konfigurowania logowania jednokrotnego opartego na protokole SAML.
    :::image type="content" source="media/add-application-portal/create-application.png" alt-text="Zrzut ekranu przedstawia Dodawanie aplikacji SSO opartej na protokole SAML.":::


> [!IMPORTANT]
> Istnieją pewne kluczowe różnice między implementacjami SSO opartymi na protokole SAML i OIDC. W przypadku aplikacji opartych na protokole SAML można dodać wiele wystąpień tej samej aplikacji. Na przykład GitHub1, GitHub2 itd. W przypadku aplikacji opartych na OIDC można dodać tylko jedno wystąpienie aplikacji. Jeśli dodano już aplikację opartą na OIDC i próbujesz ponownie dodać tę samą aplikację i udostępnić ją dwukrotnie, nie zostanie ona ponownie dodana w dzierżawie.

Jeśli szukana aplikacja nie znajduje się w galerii, możesz wybrać link **Utwórz własną aplikację** , a następnie w obszarze **co chcesz zrobić z aplikacją?** wybierz opcję **Zintegruj każdą inną aplikację, której nie ma w galerii**. Firma Microsoft pracowała już z wieloma deweloperami aplikacji w celu wstępnego skonfigurowania ich do pracy z usługą Azure AD. Wstępnie skonfigurowane aplikacje są wyświetlane w galerii. Jeśli jednak aplikacja, którą chcesz dodać, nie znajduje się na liście, możesz utworzyć nową, ogólną, niestandardową aplikację, a następnie skonfigurować ją samodzielnie lub przy użyciu wskazówek utworzonych przez dewelopera.

Ukończono dodawanie aplikacji. W następnym przewodniku szybki start pokazano, jak zmienić logo i edytować inne właściwości aplikacji.

> [!TIP]
> Zarządzanie aplikacjami można zautomatyzować za pomocą interfejs API programu Graph, zobacz [Automatyzowanie zarządzania aplikacjami za pomocą Microsoft Graph interfejsu API](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz kontynuować serii szybkiego startu, Rozważ usunięcie aplikacji w celu oczyszczenia dzierżawy testowej. Usuwanie aplikacji jest omówione w ostatnim przewodniku szybki start w tej serii, zobacz [usuwanie aplikacji](delete-application-portal.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak skonfigurować aplikację.
> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji](add-application-portal-configure.md)