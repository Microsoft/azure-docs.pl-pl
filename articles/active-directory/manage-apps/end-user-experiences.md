---
title: Środowiska użytkownika końcowego dla aplikacji — Azure Active Directory
description: Azure Active Directory (Azure AD) oferuje kilka dostosowywalnych metod wdrażania aplikacji dla użytkowników końcowych w organizacji.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: 0d3f1cab0637c7409cbc45f4f5a5374b2b5b6a77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259257"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Środowisko użytkownika końcowego dla aplikacji w Azure Active Directory

Azure Active Directory (Azure AD) oferuje kilka dostosowywalnych metod wdrażania aplikacji dla użytkowników końcowych w organizacji:

* Moje aplikacje w usłudze Azure AD
* Microsoft 365 uruchamiania aplikacji
* Bezpośrednie logowanie do aplikacji federacyjnych
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Metody wybrane do wdrożenia w organizacji są takie same.

## <a name="azure-ad-my-apps"></a>Moje aplikacje w usłudze Azure AD

Moje aplikacje w https://myapps.microsoft.com programie to portal oparty na sieci Web, który umożliwia użytkownikowi końcowemu konta organizacyjnego w Azure Active Directory wyświetlanie i uruchamianie aplikacji, do których udzielono dostępu przez administratora usługi Azure AD. Jeśli jesteś użytkownikiem końcowym z [Azure Active Directory — wersja Premium](https://azure.microsoft.com/pricing/details/active-directory/), możesz również korzystać z funkcji samoobsługowego zarządzania grupami za pomocą aplikacji.

Domyślnie wszystkie aplikacje są wyświetlane na jednej stronie. Można jednak użyć kolekcji, aby zgrupować powiązane aplikacje i przedstawić je na osobnej karcie, co ułatwia ich znalezienie. Można na przykład użyć kolekcji do tworzenia logicznych grup aplikacji dla określonych ról zadań, zadań, projektów i tak dalej. Aby uzyskać więcej informacji, zobacz [Tworzenie kolekcji w portalu My Apps](access-panel-collections.md). 

Moje aplikacje są oddzielone od Azure Portal i nie wymagają, aby użytkownicy mieli subskrypcję platformy Azure ani subskrypcję Microsoft 365.

Aby uzyskać więcej informacji o usłudze Azure AD moje aplikacje, zobacz [wprowadzenie do moich aplikacji](../user-help/my-apps-portal-end-user-access.md).

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 uruchamiania aplikacji

W przypadku organizacji, które mają wdrożone Microsoft 365 aplikacje przypisane do użytkowników za pomocą usługi Azure AD również będą wyświetlane w portalu pakietu Office 365 pod adresem [https://portal.office.com/myapps](https://portal.office.com/myapps) . Dzięki temu użytkownicy w organizacji mogą korzystać z aplikacji bez konieczności korzystania z drugiego portalu i są zalecanym rozwiązaniem do uruchamiania aplikacji dla organizacji korzystających z Microsoft 365.

Aby uzyskać więcej informacji na temat uruchamiania aplikacji pakietu Office 365, zobacz temat Wyświetlanie [aplikacji w programie uruchamiania aplikacji pakietu office 365](/previous-versions/office/office-365-api/).

## <a name="direct-sign-on-to-federated-apps"></a>Bezpośrednie logowanie do aplikacji federacyjnych

Większość aplikacji federacyjnych, które obsługują protokół SAML 2,0, WS-Federation lub OpenID Connect, również umożliwia użytkownikom uruchamianie w aplikacji, a następnie logowanie za pomocą usługi Azure AD odbywa się przez automatyczne przekierowanie lub przez kliknięcie linku do logowania. Jest to nazywane logowaniem zainicjowanym przez dostawcę usługi i większość aplikacji federacyjnych w galerii aplikacji usługi Azure AD obsługuje tę funkcję (zobacz dokumentację połączoną z kreatorem konfiguracji logowania jednokrotnego aplikacji w Azure Portal, aby uzyskać szczegółowe informacje).

## <a name="direct-sign-on-links"></a>Linki bezpośrednie logowania

Usługa Azure AD obsługuje także bezpośrednie łącza do poszczególnych aplikacji, które obsługują Logowanie jednokrotne oparte na hasłach, połączone Logowanie jednokrotne i dowolną formę federacyjnego logowania jednokrotnego.

Te linki są specjalnie spreparowanymi adresami URL, które wysyłają użytkownika za pośrednictwem procesu logowania usługi Azure AD dla określonej aplikacji bez konieczności uruchamiania ich przez użytkownika z usługi Azure AD moje aplikacje lub Microsoft 365. Te **adresy URL dostępu użytkowników** można znaleźć w obszarze właściwości dostępnych aplikacji dla przedsiębiorstw. W Azure Portal wybierz pozycję **Azure Active Directory**  >  **aplikacje dla przedsiębiorstw**. Wybierz aplikację, a następnie wybierz pozycję **Właściwości**.

![Przykład adresu URL dostępu użytkownika we właściwościach usługi Twitter](media/end-user-experiences/direct-sign-on-link.png)

Te linki można kopiować i wklejać wszędzie tam, gdzie chcesz udostępnić link logowania do wybranej aplikacji. Może to być w wiadomości e-mail lub w dowolnym niestandardowym portalu opartym na sieci Web skonfigurowanym do uzyskiwania dostępu do aplikacji użytkownika. Oto przykład adresu URL logowania jednokrotnego w usłudze Azure AD dla usługi Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Podobnie jak w przypadku adresów URL specyficznych dla organizacji dla aplikacji, można jeszcze bardziej dostosować ten adres URL, dodając jedną z aktywnych lub zweryfikowanych domen dla katalogu po domenie *myapps.Microsoft.com* . Gwarantuje to, że każde oznakowanie organizacyjne zostanie załadowane natychmiast na stronie logowania bez konieczności wprowadzania identyfikatora użytkownika jako pierwszej:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Gdy autoryzowany użytkownik kliknie jedno z tych linków specyficznych dla aplikacji, po raz pierwszy zobaczy swoją organizacyjną stronę logowania (przy założeniu, że nie są jeszcze zarejestrowani), a po zalogowaniu się do swojej aplikacji nie należy najpierw zatrzymywać aplikacji. Jeśli użytkownik nie ma wymagań wstępnych, aby uzyskać dostęp do aplikacji, na przykład rozszerzenie przeglądarki jednokrotnego podpisywania opartego na hasłach, wówczas zostanie wyświetlony monit o zainstalowanie brakującego rozszerzenia. Adres URL linku pozostaje również stały, jeśli konfiguracja logowania jednokrotnego dla aplikacji zostanie zmieniona.

Te linki używają tych samych mechanizmów kontroli dostępu jak moje aplikacje i Microsoft 365, a tylko Ci użytkownicy lub grupy, którzy zostali przypisani do aplikacji w Azure Portal, będą mogli pomyślnie uwierzytelnić się. Jednak każdy użytkownik, który nie ma autoryzacji, zobaczy komunikat z informacją, że nie udzielono dostępu i ma link umożliwiający załadowanie aplikacji w celu wyświetlenia dostępnych aplikacji, do których mają dostęp.

## <a name="next-steps"></a>Następne kroki

* [Seria szybkiego startu w zarządzaniu aplikacjami](view-applications-portal.md)
* [Co to jest logowanie jednokrotne?](what-is-single-sign-on.md)
* [Przewodnik po rozpoczęciu integracji Azure Active Directory z aplikacjami](plan-an-application-integration.md)