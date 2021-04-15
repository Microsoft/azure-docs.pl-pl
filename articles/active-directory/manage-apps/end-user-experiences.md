---
title: Środowisko użytkownika końcowego dla aplikacji — Azure Active Directory
description: Azure Active Directory (Azure AD) oferuje kilka sposobów wdrażania aplikacji dla użytkowników końcowych w organizacji.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iangithinji
ms.reviewer: arvindh
ms.openlocfilehash: c555899a65a5e8cf4c8fcc6214e4dcbda3931f08
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374237"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Środowisko użytkownika końcowego dla aplikacji w Azure Active Directory

Azure Active Directory (Azure AD) oferuje kilka owalnych sposobów wdrażania aplikacji dla użytkowników końcowych w organizacji:

* Azure AD Moje aplikacje
* Microsoft 365 uruchamiania aplikacji
* Bezpośrednie logowanie do aplikacji federacyjnych
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Metody, które wybierzesz do wdrożenia w organizacji, są Twoim uznania.

## <a name="azure-ad-my-apps"></a>Azure AD Moje aplikacje

Moje aplikacje jest portalem internetowym, który umożliwia użytkownikowi końcowemu z kontem organizacyjnym w usłudze Azure Active Directory wyświetlanie i uruchamianie aplikacji, do których administrator usługi Azure AD udzielił mu https://myapps.microsoft.com dostępu. Jeśli jesteś użytkownikiem końcowym z [Azure Active Directory — wersja Premium](https://azure.microsoft.com/pricing/details/active-directory/), możesz również korzystać z funkcji samoobsługowego zarządzania grupą za pośrednictwem Moje aplikacje.

Domyślnie wszystkie aplikacje są wyświetlane razem na jednej stronie. Można jednak używać kolekcji do grupowania powiązanych aplikacji i prezentowania ich na osobnej karcie, co ułatwia ich znajdowanie. Na przykład można użyć kolekcji do utworzenia logicznych grup aplikacji dla określonych ról zadań, zadań, projektów i tak dalej. Aby uzyskać więcej informacji, zobacz Create collections on the Moje aplikacje portal (Tworzenie [kolekcji w Moje aplikacje portal).](access-panel-collections.md) 

Moje aplikacje jest oddzielona od Azure Portal i nie wymaga, aby użytkownicy mieli subskrypcję platformy Azure ani Microsoft 365 subskrypcji.

Aby uzyskać więcej informacji na temat usługi Azure AD Moje aplikacje, zobacz [wprowadzenie do usługi Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 uruchamiania aplikacji

W przypadku organizacji, które wdrożyły usługę Microsoft 365, aplikacje przypisane do użytkowników za pośrednictwem usługi Azure AD będą również wyświetlane w portalu usługi Office 365 pod witrynie [https://portal.office.com/myapps](https://portal.office.com/myapps) . Dzięki temu użytkownicy w organizacji mogą łatwo i wygodnie uruchamiać swoje aplikacje bez konieczności korzystania z drugiego portalu. Jest to zalecane rozwiązanie do uruchamiania aplikacji dla organizacji korzystających z Microsoft 365.

Aby uzyskać więcej informacji na temat uruchamiania aplikacji usługi Office 365, zobacz Have your app appear in the Office 365 app launcher (Uruchamianie aplikacji usługi [Office 365).](/previous-versions/office/office-365-api/)

## <a name="direct-sign-on-to-federated-apps"></a>Bezpośrednie logowanie do aplikacji federacyjnych

Większość aplikacji federowanych, które obsługują program SAML 2.0, WS-Federation lub OpenID Connect, obsługuje również możliwość uruchamiania aplikacji przez użytkowników, a następnie logowania się za pośrednictwem usługi Azure AD przez automatyczne przekierowanie lub kliknięcie linku w celu zalogowania się. Ta usługa jest znana jako logowanie inicjowane przez dostawcę usług, a większość aplikacji federacyjnych w galerii aplikacji usługi Azure AD obsługuje tę obsługę (aby uzyskać szczegółowe informacje, zobacz dokumentację powiązaną z kreatorem konfiguracji logowania Azure Portal aplikacji).

## <a name="direct-sign-on-links"></a>Linki logowania bezpośredniego

Usługa Azure AD obsługuje również bezpośrednie linki do logowania pojedynczego do poszczególnych aplikacji, które obsługują logowanie pojedyncze oparte na hasłach, połączone logowanie pojedyncze i dowolną postać federacyjne logowaniem pojedynczym.

Te linki to specjalnie przygotowane adresy URL, które wysyłają użytkownika za pośrednictwem procesu logowania do usługi Azure AD dla określonej aplikacji bez konieczności uruchamiania ich z usługi Azure AD Moje aplikacje lub Microsoft 365. Te **adresy URL dostępu użytkowników** można znaleźć we właściwościach dostępnych aplikacji dla przedsiębiorstw. W Azure Portal wybierz pozycję **Azure Active Directory**  >  **Enterprise.** Wybierz aplikację, a następnie wybierz pozycję **Właściwości.**

![Przykład adresu URL dostępu użytkownika we właściwościach usługi Twitter](media/end-user-experiences/direct-sign-on-link.png)

Te linki można kopiować i wklejać w dowolnym miejscu, w którym chcesz podać link logowania do wybranej aplikacji. Może to być adres e-mail lub dowolny niestandardowy portal internetowy, który został ustawiony na dostęp do aplikacji użytkownika. Oto przykład adresu URL bezpośredniego logowania pojedynczego usługi Azure AD dla usługi Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobnie jak w przypadku adresów URL specyficznych dla Moje aplikacje, możesz dodatkowo dostosować ten adres URL, dodając jedną z aktywnych lub zweryfikowanych domen dla katalogu po myapps.microsoft.com *domeny.* Dzięki temu wszelkie znaki organizacyjne są ładowane natychmiast na stronie logowania bez konieczności wcześniejszego wprowadzania identyfikatora użytkownika przez użytkownika:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Gdy autoryzowany użytkownik kliknie jeden z tych linków specyficznych dla aplikacji, najpierw zobaczy stronę logowania organizacji (przy założeniu, że nie jest jeszcze zalogowany), a po zalogowaniu zostanie przekierowany do aplikacji bez zatrzymywania się Moje aplikacje rejestracji. Jeśli użytkownik nie ma wymagań wstępnych dostępu do aplikacji, takich jak rozszerzenie przeglądarki ze znakiem pojedynczym oparte na hasłach, link wyświetli monit o zainstalowanie brakującego rozszerzenia. Adres URL linku pozostaje również stały, jeśli konfiguracja logowania pojedynczego dla aplikacji zmieni się.

Te linki korzystają z tych samych mechanizmów kontroli dostępu co usługi Moje aplikacje i Microsoft 365 i tylko użytkownicy lub grupy przypisane do aplikacji w programie Azure Portal będą mogli pomyślnie uwierzytelnić się. Jednak każdy użytkownik, który nie ma autoryzacji, zobaczy komunikat z wyjaśnieniem, że nie udzielono mu dostępu, i zostanie im nadany link do ładowania Moje aplikacje w celu wyświetlenia dostępnych aplikacji, do których mają dostęp.

## <a name="next-steps"></a>Następne kroki

* [Seria przewodników Szybki start na temat zarządzania aplikacją](view-applications-portal.md)
* [Co to jest logowanie pojedyncze?](what-is-single-sign-on.md)
* [Przewodnik integrowania Azure Active Directory z aplikacjami](plan-an-application-integration.md)