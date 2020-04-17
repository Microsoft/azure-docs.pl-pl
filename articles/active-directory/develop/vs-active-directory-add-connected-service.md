---
title: Korzystanie z usługi połączonej z usługą Active Directory (Visual Studio)
description: Dodawanie usługi Azure Active Directory przy użyciu okna dialogowego Dodawanie połączonych usług w programie Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: d5b6452684757aead684356fd9bb032b90c58c4a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535829"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Dodawanie usługi Azure Active Directory przy użyciu usług połączonych w programie Visual Studio

Korzystając z usługi Azure Active Directory (Azure AD), można obsługiwać logowanie jednokrotne (SSO) dla ASP.NET aplikacji sieci Web MVC lub uwierzytelniania usługi Active Directory w usługach interfejsu API sieci Web. Dzięki uwierzytelnianiu usługi Azure AD użytkownicy mogą używać swoich kont z usługi Azure Active Directory do łączenia się z aplikacjami sieci Web. Zalety uwierzytelniania usługi Azure AD z interfejsem API sieci Web obejmują zwiększone zabezpieczenia danych podczas ujawniania interfejsu API z aplikacji sieci web. Za pomocą usługi Azure AD nie trzeba zarządzać oddzielnym systemem uwierzytelniania z własnym kontem i zarządzaniem użytkownikami.

Ten artykuł i jego artykuły towarzyszące zawierają szczegółowe informacje na temat korzystania z funkcji połączonej usługi Visual Studio dla usługi Active Directory. Możliwość jest dostępna w programie Visual Studio 2015 i nowszych.

Obecnie usługa połączona z usługą Active Directory nie obsługuje aplikacji ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure: jeśli nie masz konta platformy Azure, możesz [zarejestrować się w celu bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) lub [aktywować korzyści dla subskrybenta programu Visual Studio.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)
- **Visual Studio 2015** lub nowszych. [Pobierz program Visual Studio już teraz](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Łączenie się z usługą Azure Active Directory przy użyciu okna dialogowego Połączone usługi

1. W programie Visual Studio utwórz lub otwórz projekt ASP.NET MVC lub ASP.NET projektu interfejsu API sieci Web. Można użyć szablonów MVC, Web API, Aplikacji jednostronicowej, aplikacji interfejsu API platformy Azure, aplikacji mobilnej Azure i usługi Azure Mobile Service.

1. Wybierz polecenie menu **Project > Add Connected Service...** lub kliknij dwukrotnie węzeł Połączone **usługi** znalezione w projekcie w Eksploratorze rozwiązań.

1. Na stronie **Połączone usługi** wybierz pozycję **Uwierzytelnianie za pomocą usługi Azure Active Directory**.

    ![Strona Połączone usługi](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na stronie **Wprowadzenie** wybierz pozycję **Dalej**. Jeśli na tej stronie są widoczne błędy, zapoznaj się [z opisem błędów diagnozowania za pomocą usługi Azure Active Directory Connected Service](vs-active-directory-error.md).

    ![Strona wprowadzająca](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na stronie **Jednokrotne znakowanie wybierz** domenę z listy rozwijanej **Domena.** Lista zawiera wszystkie domeny dostępne dla kont wymienionych w oknie dialogowym Ustawienia kont programu Visual Studio **(Ustawienia > pliku...**). Alternatywnie możesz wprowadzić nazwę domeny, jeśli nie znajdziesz tej, której szukasz, `mydomain.onmicrosoft.com`takiej jak . Możesz wybrać opcję utworzenia aplikacji usługi Azure Active Directory lub użyć ustawień z istniejącej aplikacji usługi Azure Active Directory. Po zakończeniu wybierz **pozycję Dalej.**

    ![Znak jednokrotny na stronie](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na stronie **Dostęp do katalogu** wybierz opcję Odczyt danych **katalogu** zgodnie z potrzebami. Deweloperzy zazwyczaj obejmują tę opcję.

    ![Strona dostępu do katalogu](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Wybierz **pozycję Zakończ,** aby rozpocząć modyfikacje projektu, aby włączyć uwierzytelnianie usługi Azure AD. Visual Studio pokazuje postęp w tym czasie:

    ![Postęp usługi połączonej z usługą Active Directory](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Po zakończeniu procesu program Visual Studio otwiera przeglądarkę do jednego z następujących artykułów, odpowiednio do typu projektu:

    - [Rozpoczynanie pracy z projektami .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Rozpoczynanie pracy z projektami WebAPI](vs-active-directory-webapi-getting-started.md)

1. Domenę usługi Active Directory można również wyświetlić w [witrynie Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Jak modyfikowany jest projekt

Po dodaniu połączonej usługi kreatora program Visual Studio dodaje usługę Azure Active Directory i skojarzone odwołania do projektu. Pliki konfiguracyjne i pliki kodu w projekcie są również modyfikowane, aby dodać obsługę usługi Azure AD. Konkretne modyfikacje, które wykonuje visual studio zależy od typu projektu. Zobacz następujące artykuły, aby uzyskać szczegółowe informacje:

- [Co się stało z moim projektem .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [Co się stało z moim projektem interfejsu API sieci Web?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla usługi Azure Active Directory](authentication-scenarios.md)
- [dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET](quickstart-v2-aspnet-webapp.md)
