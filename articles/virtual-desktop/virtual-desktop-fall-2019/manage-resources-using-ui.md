---
title: Wdrażanie narzędzia do zarządzania z szablonem Azure Resource Manager — Azure
description: Jak zainstalować narzędzie interfejsu użytkownika z szablonem Azure Resource Manager w celu zarządzania zasobami pulpitu wirtualnego systemu Windows (klasycznym).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f96365bcdf64d19dc0b894f2f1230233b3137bc7
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842655"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-an-azure-resource-manager-template"></a>Wdrażanie narzędzia do zarządzania pulpitem wirtualnym systemu Windows (klasycznego) przy użyciu szablonu Azure Resource Manager

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows.

Instrukcje zawarte w tym artykule przedstawiają sposób wdrażania interfejsu użytkownika przy użyciu szablonu Azure Resource Manager.

## <a name="important-considerations"></a>Istotne zagadnienia

Ponieważ aplikacja wymaga zgody na współdziałanie z pulpitem wirtualnym systemu Windows, to narzędzie nie obsługuje scenariuszy biznes-to-Business (B2B). Każda subskrypcja dzierżawy usługi Azure Active Directory (AAD) będzie potrzebować oddzielnego wdrożenia narzędzia do zarządzania.

To narzędzie do zarządzania jest przykładem. Firma Microsoft zapewni ważne aktualizacje zabezpieczeń i ich jakości. [Kod źródłowy jest dostępny w](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)serwisie GitHub. Zachęcamy klientów i partnerów do dostosowania narzędzia do potrzeb firmy.

Do następujących przeglądarek są zgodne narzędzia do zarządzania:
- Google Chrome 68 lub nowsza
- Microsoft Edge 40,15063 lub nowsza
- Mozilla Firefox 52,0 lub nowsza
- Przeglądarka Safari 10 lub nowsza (tylko macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Co jest potrzebne do wdrożenia narzędzia do zarządzania

Przed wdrożeniem narzędzia do zarządzania musisz mieć Azure Active Directory użytkownika (Azure AD), aby utworzyć rejestrację aplikacji i wdrożyć interfejs użytkownika zarządzania. Ten użytkownik musi:

- Usługa Azure AD Multi-Factor Authentication (MFA) jest wyłączona
- Masz uprawnienia do tworzenia zasobów w ramach subskrypcji platformy Azure
- Masz uprawnienia do tworzenia aplikacji usługi Azure AD. Wykonaj następujące kroki, aby sprawdzić, czy użytkownik ma wymagane uprawnienia, postępując zgodnie z instrukcjami w obszarze [wymagane uprawnienia](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Po wdrożeniu i skonfigurowaniu narzędzia do zarządzania zalecamy poproszenie użytkownika o uruchomienie interfejsu użytkownika zarządzania, aby upewnić się, że wszystko działa. Użytkownik, który uruchamia interfejs użytkownika zarządzania, musi mieć przypisanie roli, które umożliwia im wyświetlanie lub edytowanie dzierżawy pulpitów wirtualnych systemu Windows.

## <a name="deploy-the-management-tool"></a>Wdrażanie narzędzia do zarządzania

Przed rozpoczęciem upewnij się, że aplikacje serwera i klienta mają zgodę, odwiedzając [stronę zgody na pulpit wirtualny systemu Windows](https://rdweb.wvd.microsoft.com) dla reprezentowanego Azure Active Directory (AAD).

Postępuj zgodnie z poniższymi instrukcjami, aby wdrożyć szablon usługi Azure Resource Management:

1. Przejdź do [strony usługi GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Wdróż szablon na platformie Azure.
    - Jeśli wdrażasz w ramach subskrypcji przedsiębiorstwa, przewiń w dół i wybierz pozycję **Wdróż na platformie Azure**.
    - Jeśli wdrażasz w ramach subskrypcji dostawcy rozwiązań w chmurze, postępuj zgodnie z poniższymi instrukcjami, aby wdrożyć platformę Azure:
        1. Przewiń w dół i kliknij prawym przyciskiem myszy pozycję **Wdróż na platformie Azure**, a następnie wybierz pozycję **Kopiuj lokalizację linku**.
        2. Otwórz Edytor tekstu, taki jak Notatnik, i Wklej tam link.
        3. Po prawej stronie <https://portal.azure.com/> i przed wystąpieniem programu (#) wprowadź znak (@), a po nim nazwę domeny dzierżawy. Oto przykład formatu: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/> .
        4. Zaloguj się do Azure Portal jako użytkownik z uprawnieniami administratora/współautora do subskrypcji dostawcy rozwiązań w chmurze.
        5. Wklej skopiowany link do edytora tekstu na pasku adresu.
3. Wprowadzając parametry, wykonaj następujące czynności:
    - Dla parametru **isServicePrincipal** wybierz **wartość false**.
    - W polu poświadczenia wprowadź poświadczenia usługi Azure AD z wyłączonym uwierzytelnianiem wieloskładnikowym. Te poświadczenia będą używane do tworzenia aplikacji usługi Azure AD i zasobów platformy Azure. Aby dowiedzieć się więcej, zobacz temat [czego potrzebujesz do wdrożenia narzędzia do zarządzania](#what-you-need-to-deploy-the-management-tool).
    - Dla programu **ApplicationName** Użyj unikatowej nazwy aplikacji, która zostanie zarejestrowana w Azure Active Directory. Ta nazwa będzie również używana dla adresu URL aplikacji sieci Web. Można na przykład użyć nazwy takiej jak "Apr3UX".
4. Po podaniu parametrów Zaakceptuj warunki i postanowienia, a następnie wybierz pozycję **Kup**.

## <a name="provide-consent-for-the-management-tool"></a>Wyrażanie zgody na narzędzie do zarządzania

Po zakończeniu szablonu Azure Resource Manager GitHub znajdziesz grupę zasobów zawierającą dwie usługi aplikacji wraz z jednym planem usługi App Service w Azure Portal.

Przed zalogowaniem się i użyciem narzędzia do zarządzania należy wyrazić zgodę na nową aplikację usługi Azure AD skojarzoną z narzędziem do zarządzania. Udostępnienie zgody pozwala narzędziu do zarządzania wykonywać wywołania Zarządzanie pulpitami wirtualnymi systemu Windows w imieniu użytkownika, który jest aktualnie zalogowany do narzędzia.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający uprawnienia podane podczas wyrażania zgody na narzędzie do zarządzania interfejsami użytkownika.](../media/management-ui-delegated-permissions.png)

Aby określić, którego użytkownika możesz użyć do zalogowania się do narzędzia, przejdź do [strony Ustawienia użytkownika Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) i zanotuj wartość dla **użytkowników, którzy będą mogli wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu**.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający, czy użytkownicy mogą udzielać zgody na aplikacje dla samego użytkownika.](../media/management-ui-user-consent-allowed.png)

- Jeśli wartość jest równa **tak**, możesz zalogować się przy użyciu dowolnego konta użytkownika w Azure Active Directory i zapewnić zgodę tylko dla tego użytkownika. Jednak w przypadku zalogowania się do narzędzia do zarządzania z innym użytkownikiem później należy ponownie wykonać tę samą zgodę.
- Jeśli wartość jest równa **no**, należy zalogować się jako Administrator globalny w Azure Active Directory i zapewnić zgodę administratora dla wszystkich użytkowników w katalogu. Żaden inny użytkownik nie będzie monitowany na monit o wyrażenie zgody.


Po podjęciu decyzji o tym, którego użytkownika będziesz używać, aby wyrazić zgodę, postępuj zgodnie z tymi instrukcjami, aby wyrazić zgodę na narzędzie:

1. Przejdź do zasobów platformy Azure, wybierz zasób App Services platformy Azure o nazwie podanej w szablonie (na przykład Apr3UX) i przejdź do adresu URL skojarzonego z nim. na przykład  `https://rdmimgmtweb-210520190304.azurewebsites.net` .
2. Zaloguj się przy użyciu odpowiedniego konta użytkownika Azure Active Directory.
3. W przypadku uwierzytelnienia za pomocą administratora globalnego możesz teraz zaznaczyć pole wyboru, aby **wyrazić zgodę w imieniu organizacji**. Wybierz pozycję **Akceptuj** , aby wyrazić zgodę.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający stronę pełna zgoda, którą zobaczy użytkownik lub administrator.](../media/management-ui-consent-page.png)

Spowoduje to przejście do narzędzia do zarządzania.

## <a name="use-the-management-tool"></a>Korzystanie z narzędzia do zarządzania

Po udzieleniu zgody na organizację lub dla określonego użytkownika można w dowolnym momencie uzyskać dostęp do narzędzia do zarządzania.

Postępuj zgodnie z poniższymi instrukcjami, aby uruchomić narzędzie:

1. Wybierz zasób App Services platformy Azure o nazwie podanej w szablonie (na przykład Apr3UX) i przejdź do adresu URL skojarzonego z nim. na przykład  `https://rdmimgmtweb-210520190304.azurewebsites.net` .
2. Zaloguj się przy użyciu poświadczeń pulpitu wirtualnego systemu Windows.
3. Po wyświetleniu monitu o wybranie grupy dzierżawców wybierz z listy rozwijanej pozycję **Domyślna grupa dzierżawców** .
4. Po wybraniu **domyślnej grupy dzierżawców** menu powinno być wyświetlane po lewej stronie okna. W tym menu Znajdź nazwę grupy dzierżawców i wybierz ją.

  > [!NOTE]
  > Jeśli masz niestandardową grupę dzierżawców, wprowadź nazwę ręcznie zamiast wybierać ją z listy rozwijanej.

## <a name="report-issues"></a>Zgłaszanie problemów

Jeśli występują problemy z narzędziem do zarządzania lub innymi narzędziami pulpitu wirtualnego systemu Windows, postępuj zgodnie z instrukcjami w temacie [Azure Resource Manager templates for usługi pulpitu zdalnego](https://github.com/Azure/RDS-Templates/blob/master/README.md) , aby zgłosić je w serwisie GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak wdrożyć narzędzie do zarządzania i połączyć się z nim, możesz dowiedzieć się, jak za pomocą usługi platformy Azure monitorować problemy z usługami i klasyfikatory kondycji. Aby dowiedzieć się więcej, zobacz [Samouczek dotyczący konfigurowania alertów usługi](set-up-service-alerts-2019.md).