---
title: Nieoczekiwana aplikacja na liście Moje aplikacje | Microsoft Docs
description: Jak wyświetlić wszystkie aplikacje w dzierżawie i zrozumieć, jak aplikacje pojawiają się na liście wszystkie aplikacje w obszarze aplikacje dla przedsiębiorstw
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
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1878004f51a66ba414c3e74be97c7135a51295bc
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763707"
---
# <a name="unexpected-application-in-my-applications-list"></a>Nieoczekiwana aplikacja na liście Moje aplikacje

Ten artykuł pomaga zrozumieć, jak aplikacje pojawiają się na liście **wszystkie aplikacje** w obszarze **aplikacje dla przedsiębiorstw**. 

## <a name="how-to-see-all-applications-in-your-tenant"></a>Jak wyświetlić wszystkie aplikacje w dzierżawie

Aby wyświetlić wszystkie aplikacje w dzierżawie, należy użyć kontrolki **filtru** , aby wyświetlić **wszystkie aplikacje** na liście **wszystkie aplikacje** . Wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5.  Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

6.  Kliknij przycisk Użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje**.

7.  W okienku **Filtr** ustaw opcję **Pokaż** na **wszystkie aplikacje.**

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Dlaczego określona Aplikacja pojawia się na liście moje wszystkie aplikacje?

Po przefiltrowaniu do **wszystkich aplikacji**na **liście** **wszystkie aplikacje** są wyświetlane wszystkie obiekty główne usługi w dzierżawie. Obiekty główne usługi mogą znajdować się na tej liście na różne sposoby:

1. Po dodaniu aplikacji z galerii aplikacji, w tym:

   1. **Aplikacje z galerii usługi Azure AD** — aplikacja, która została wstępnie zintegrowana pod kątem logowania jednokrotnego w usłudze Azure AD

   2. **Aplikacje serwera proxy aplikacji** — aplikacja działająca w środowisku lokalnym, w której chcesz zapewnić bezpieczne logowanie jednokrotne na zewnątrz

   3. **Aplikacje opracowane niestandardowo** — aplikacja, którą organizacja chce opracowywać na platformie tworzenia aplikacji usługi Azure AD, ale która może jeszcze nie istnieć

   4. **Aplikacje spoza galerii** — przenoszenie własnych aplikacji Dowolny link internetowy lub każda aplikacja, która renderuje pole nazwy użytkownika i hasła, obsługuje protokoły protokołu SAML lub OpenID Connect Connect lub obsługuje standard scim, które chcesz zintegrować z logowaniem jednokrotnym w usłudze Azure AD.

2. Po zarejestrowaniu się w programie lub zalogowaniu się do programu Aplikacja platformy<sup>Rd</sup> dla platformy Microsoft jest zintegrowana z usługą Azure Active Directory. Przykładem jest [Narzędzia Smartsheet](https://app.smartsheet.com/b/home) lub [Docusign](https://www.docusign.net/member/MemberLogin.aspx).

3. Podczas rejestrowania się w usłudze lub dodawania licencji do użytkownika lub grupy do aplikacji pierwszej firmy, takiej jak [Microsoft Office 365](https://products.office.com/)

4. Po dodaniu nowej rejestracji aplikacji, tworząc aplikację niestandardową przy użyciu [rejestru aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)

5. Po dodaniu nowej rejestracji aplikacji przez utworzenie niestandardowej aplikacji za pomocą [portalu rejestracji aplikacji w wersji 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)

6. Po dodaniu aplikacji, która jest opracowywana przy użyciu [metod uwierzytelniania ASP.NET](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) lub [usług połączonych](https://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx) programu Visual Studio

7. Podczas tworzenia obiektu jednostki usługi przy użyciu [modułu Azure AD PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0)

8. Gdy [wyrażasz zgodę na aplikację](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) jako administrator do korzystania z danych w dzierżawie

9. Gdy [użytkownik wyraża zgodę na aplikację](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) w celu korzystania z danych w dzierżawie

10. Po włączeniu niektórych usług, które przechowują dane w dzierżawie. Przykładem jest Resetowanie hasła, które jest modelowane jako nazwa główna usługi do bezpiecznego przechowywania zasad resetowania hasła.

Aby uzyskać więcej informacji na temat sposobu dodawania aplikacji do katalogu, Przeczytaj [temat jak i dlaczego aplikacje są dodawane do usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added).

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chcę usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć przypisanie użytkownika lub grupy do aplikacji, postępuj zgodnie z instrukcjami podanymi w temacie [usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artykule.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chcę wyłączyć dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkownika do aplikacji, wykonaj kroki opisane w artykule [wyłączanie logowania użytkownika dla aplikacji przedsiębiorstwa w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) .

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę całkowicie usunąć aplikację

Aby **usunąć aplikację**, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz usunąć.

7. Po załadowaniu aplikacji kliknij przycisk **Usuń** ikonę w okienku **Przegląd** górnej aplikacji.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcę wyłączyć wszystkie przyszłe operacje wyrażania zgody użytkownika na dowolną aplikację

Wyłączenie zgody użytkownika na cały katalog uniemożliwi użytkownikom końcowym przesyłanie ich do żadnej aplikacji. Administratorzy mogą w dalszym ciągu wyrazić zgodę w imieniu użytkownika. Aby dowiedzieć się więcej o zgodzie aplikacji oraz o tym, dlaczego warto lub nie chcieć wyrazić zgodę, przeczytaj artykuł [Informacje o zgodzie użytkownika i administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Aby **wyłączyć wszystkie przyszłe operacje wyrażania zgody użytkowników w całym katalogu**, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **Ustawienia użytkownika**.

6.  Wyłącz wszystkie przyszłe operacje wyrażania zgody użytkownika przez ustawienie opcji **Użytkownicy mogą zezwalać aplikacjom na dostęp do ich** przełączników danych **, a następnie** kliknij przycisk **Zapisz** .

## <a name="next-steps"></a>Następne kroki
[Zarządzanie aplikacjami za pomocą Azure Active Directory](what-is-application-management.md)
