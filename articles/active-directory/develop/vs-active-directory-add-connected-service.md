---
title: Korzystanie z usługi połączonej Active Directory (Visual Studio)
description: Dodawanie Azure Active Directory przy użyciu okna dialogowego Dodawanie usług połączonych programu Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: a1ba7db72743ac122a697bf271e783ec64e041e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88165485"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Dodawanie Azure Active Directory przy użyciu usług połączonych w programie Visual Studio

Za pomocą Azure Active Directory (Azure AD) można obsługiwać pojedyncze Sign-On (SSO) dla aplikacji sieci Web ASP.NET MVC lub Active Directory uwierzytelnianie w usługach interfejsu API sieci Web. Przy użyciu uwierzytelniania usługi Azure AD użytkownicy mogą łączyć się z aplikacjami sieci Web przy użyciu swoich kont z Azure Active Directory. Zalety uwierzytelniania usługi Azure AD za pomocą interfejsu API sieci Web obejmują ulepszone zabezpieczenia danych podczas ujawniania interfejsu API z aplikacji sieci Web. W usłudze Azure AD nie trzeba zarządzać osobnym systemem uwierzytelniania przy użyciu własnego konta i zarządzania użytkownikami.

Ten artykuł i jego artykuły towarzyszące zawierają szczegółowe informacje dotyczące korzystania z funkcji usługi połączonej programu Visual Studio dla Active Directory. Ta funkcja jest dostępna w programie Visual Studio 2015 i nowszych.

W tej chwili usługa połączona Active Directory nie obsługuje aplikacji ASP.NET Core.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure: Jeśli nie masz konta platformy Azure, możesz [zarejestrować się w celu uzyskania bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) lub [aktywować korzyści dla subskrybentów programu Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **Program Visual Studio 2015** lub nowszy. [Pobierz teraz program Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Łączenie się z usługą Azure Active Directory przy użyciu okna dialogowego połączone usługi

1. W programie Visual Studio Utwórz lub Otwórz projekt ASP.NET MVC lub projekt internetowego interfejsu API ASP.NET. Można korzystać z szablonów platformy MVC, interfejsu Single-Page API sieci Web, aplikacji usługi Azure Mobile, aplikacji mobilnej platformy Azure i usług Azure Mobile Service.

1. Wybierz **projekt > Dodaj połączonej usługi..** . menu, lub kliknij dwukrotnie węzeł **usługi połączone** znajdujący się w projekcie w Eksplorator rozwiązań.

1. Na stronie **podłączone usługi** wybierz pozycję **uwierzytelnianie z Azure Active Directory**.

    ![Strona usługi połączone](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Na stronie **wprowadzenie** wybierz pozycję **dalej**. Jeśli widzisz błędy na tej stronie, zapoznaj się z tematem [Diagnozowanie błędów za pomocą usługi połączonej Azure Active Directory](vs-active-directory-error.md).

    ![Strona wprowadzenia](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Na stronie **Logowanie** jednokrotne wybierz domenę z listy rozwijanej **domena** . Lista zawiera wszystkie domeny dostępne przez konta wymienione w oknie dialogowym Ustawienia konta w programie Visual Studio (**Ustawienia konta > plików...**). Alternatywnie możesz wprowadzić nazwę domeny, jeśli nie znajdziesz tego, na czym szukasz, na przykład `mydomain.onmicrosoft.com` . Możesz wybrać opcję tworzenia aplikacji Azure Active Directory lub użyć ustawień z istniejącej aplikacji Azure Active Directory. Po zakończeniu wybierz pozycję **dalej** .

    ![Strona logowania jednokrotnego](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Na stronie **dostęp do katalogu** wybierz opcję **Czytaj dane katalogu** zgodnie z potrzebami. Deweloperzy zazwyczaj obejmują tę opcję.

    ![Strona dostępu do katalogu](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Wybierz pozycję **Zakończ** , aby rozpocząć modyfikacje projektu, aby włączyć uwierzytelnianie w usłudze Azure AD. Program Visual Studio pokazuje postęp w tym czasie:

    ![Active Directory postęp usługi połączonej](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Po zakończeniu procesu program Visual Studio otwiera przeglądarkę w jednym z następujących artykułów zgodnie z wymaganiami typu projektu:

    - [Rozpoczynanie pracy z projektami .NET MVC](vs-active-directory-dotnet-getting-started.md)
    - [Rozpoczynanie pracy z projektami WebAPI](vs-active-directory-webapi-getting-started.md)

1. Na [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)można także zobaczyć domenę Active Directory.

## <a name="how-your-project-is-modified"></a>Jak projekt jest modyfikowany

Po dodaniu połączonej usługi Kreator programu Visual Studio dodaje Azure Active Directory i skojarzone odwołania do projektu. Pliki konfiguracji i pliki kodu w projekcie są również modyfikowane w celu dodania obsługi usługi Azure AD. Konkretne modyfikacje wprowadzane przez program Visual Studio zależą od typu projektu. Szczegółowe informacje znajdują się w następujących artykułach:

- [Co się stało z moim projektem .NET MVC?](vs-active-directory-dotnet-what-happened.md)
- [Co się stało z moim projektem interfejsu API sieci Web?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla Azure Active Directory](./authentication-vs-authorization.md)
- [dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET](quickstart-v2-aspnet-webapp.md)