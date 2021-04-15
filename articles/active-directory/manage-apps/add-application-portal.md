---
title: 'Szybki start: dodawanie aplikacji do dzierżawy Azure Active Directory (Azure AD)'
description: Ten przewodnik Szybki start używa witryny Azure Portal do dodawania aplikacji z galerii do dzierżawy usługi Azure Active Directory (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: iangithinji
ms.openlocfilehash: d7827d6419e4d820f8aca482df2cfae83eaf319d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378894"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Szybki start: dodawanie aplikacji do dzierżawy Azure Active Directory (Azure AD)

Usługa Azure Active Directory (Azure AD) udostępnia galerię, która zawiera tysiące wstępnie zintegrowanych aplikacji. Wiele aplikacji, z których korzysta organizacja, prawdopodobnie jest już w galerii.

Po dodaniu aplikacji do dzierżawy usługi Azure AD, można:

- Skonfiguruj właściwości aplikacji.
- Zarządzanie dostępem użytkowników do aplikacji przy użyciu zasad dostępu warunkowego.
- Skonfiguruj logowanie pojedyncze, aby użytkownicy mogą logować się do aplikacji przy użyciu poświadczeń usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać aplikację do dzierżawy usługi Azure AD, potrzebne są:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Jedna z następujących ról: administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.
- (Opcjonalnie: ukończenie [wyświetlania aplikacji).](view-applications-portal.md)

>[!IMPORTANT]
>Do testowania kroków w tym przewodniku Szybki start zalecamy użycie środowiska nieprodukcyjną.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Dodawanie aplikacji do dzierżawy usługi Azure AD

Aby dodać aplikację do dzierżawy usługi Azure AD:

1. W [okienku Azure Portal](https://portal.azure.com)panelu nawigacyjnym po lewej stronie wybierz **pozycję Azure Active Directory**.
2. W **okienku Azure Active Directory** wybierz pozycję **Aplikacje dla przedsiębiorstw.** Zostanie **otwarte okienko** Wszystkie aplikacje, w którym zostanie wyświetlona losowa próbka aplikacji w dzierżawie usługi Azure AD.
3. W **okienku Aplikacje dla przedsiębiorstw** wybierz pozycję **Nowa aplikacja.** 
    ![Wybierz pozycję Nowa aplikacja, aby dodać aplikację z galerii do dzierżawy](media/add-application-portal/new-application.png)
4. Przejdź do nowego podglądu galerii: na banerze w górnej części strony Dodawanie aplikacji wybierz link Kliknij **tutaj,** aby wypróbować nową i ulepszoną **galerię aplikacji.**
5. Zostanie **otwarte okienko Przeglądanie galerii usługi Azure AD (wersja zapoznawcza)** z wyświetlonymi kafelkami dla platform w chmurze, aplikacji lokalnych i polecanych aplikacji. Aplikacje wymienione w sekcji **Polecane aplikacje** mają ikony wskazujące, czy obsługują federowane logowanie jednokrotne (SSO) i aprowizowanie. 
    ![Wyszukiwanie aplikacji według nazwy lub kategorii](media/add-application-portal/browse-gallery.png)
6. Możesz przeglądać galerię aplikacji, którą chcesz dodać, lub wyszukać aplikację, wprowadzając jej nazwę w polu wyszukiwania. Następnie wybierz aplikację z wyników. 
7. Następny krok zależy od sposobu, w jaki deweloper aplikacji zaimplementował logowanie jednokrotne. Logowanie pojedyncze może być implementowane przez deweloperów aplikacji na cztery sposoby. Te cztery sposoby to SAML, OpenID Connect, Password i Linked. Podczas dodawania aplikacji można wybrać filtrowanie i wyświetlanie tylko aplikacji korzystających z określonej implementacji logowania jednokrotnego, jak pokazano na zrzucie ekranu. Na przykład popularny standard implementacji logowania jednokrotnego nosi nazwę SAML (SAML). Innym popularnym standardem jest OpenId Connect (OIDC). Sposób konfigurowania logowania jednokrotnego przy użyciu tych standardów jest inny, dlatego zwróć uwagę na typ logowania jednokrotnego implementowany przez aplikację, która jest dodawania.

    :::image type="content" source="media/add-application-portal/sso-types.png" alt-text="Zrzut ekranu przedstawia selektor typów logowania jednokrotnego." lightbox="media/add-application-portal/sso-types.png":::

    - Jeśli deweloper aplikacji użył standardu **OIDC** dla logowania jednokrotnego, wybierz **pozycję Zarejestruj się.** Zostanie wyświetlona strona konfiguracji. Następnie przejdź do przewodnika Szybki start na temat konfigurowania logowania pojedynczego opartego na OIDC.
    :::image type="content" source="media/add-application-portal/sign-up-oidc-sso.png" alt-text="Zrzut ekranu przedstawiający dodawanie aplikacji logowania jednokrotnego opartego na OIDC.":::

    - Jeśli deweloper aplikacji użył standardu **SAML** dla logowania jednokrotnego, wybierz pozycję **Utwórz**. Zostanie wyświetlona strona wprowadzenia z opcjami konfigurowania aplikacji w organizacji. W formularzu można edytować nazwę aplikacji, aby dopasować ją do potrzeb organizacji. Następnie przejdź do przewodnika Szybki start na temat konfigurowania logowania pojedynczego opartego na saml.
    :::image type="content" source="media/add-application-portal/create-application.png" alt-text="Zrzut ekranu przedstawiający dodawanie aplikacji logowania jednokrotnego opartej na saml.":::


> [!IMPORTANT]
> Istnieją pewne podstawowe różnice między implementacjami logowania jednokrotnego opartymi na saml i OIDC. Za pomocą aplikacji opartych na saml można dodać wiele wystąpień tej samej aplikacji. Na przykład GitHub1, GitHub2 itp. W przypadku aplikacji opartych na OIDC można dodać tylko jedno wystąpienie aplikacji. Jeśli aplikacja oparta na OIDC została już dodana i spróbujesz ponownie dodać tę samą aplikację i dwukrotnie wyrazić zgodę, nie zostanie ona ponownie dodana w dzierżawie.

Jeśli aplikacja, której szukasz, nie znajduje się w galerii, możesz wybrać **link** Utwórz własną aplikację, a następnie w obszarze Co chcesz zrobić z **aplikacją?** wybierz pozycję Integruj dowolną inną aplikację, której nie znajdziesz w **galerii**. Firma Microsoft współpracowała już z wieloma deweloperami aplikacji w celu wstępnego skonfigurowania ich do pracy z usługą Azure AD. Wstępnie skonfigurowane aplikacje są wyświetlane w galerii. Jeśli jednak aplikacji, którą chcesz dodać, nie ma na liście, możesz utworzyć nową, ogólną aplikację, a następnie skonfigurować ją samodzielnie lub za pomocą wskazówek dewelopera, który ją utworzył.

Zakończono dodawanie aplikacji. W następnym przewodniku Szybki start pokazano, jak zmienić logo i edytować inne właściwości aplikacji.

> [!TIP]
> Możesz zautomatyzować zarządzanie aplikacją przy użyciu interfejsu interfejs Graph API, zobacz [Automatyzowanie](/graph/application-saml-sso-configure-api)zarządzania aplikacją za pomocą Microsoft Graph API.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie zamierzasz kontynuować pracy z serią Szybki start, rozważ usunięcie aplikacji w celu oczyszczenia dzierżawy testowej. Usuwanie aplikacji jest uwzględnione w ostatnim przewodniku Szybki start z tej serii. [Zobacz Usuwanie aplikacji.](delete-application-portal.md)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak skonfigurować aplikację, należy przejść do następnego artykułu.
> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji](add-application-portal-configure.md)