---
title: Jak i dlaczego aplikacje są dodawane do usługi Azure AD
titleSuffix: Microsoft identity platform
description: Co oznacza, że aplikacja ma zostać dodana do usługi Azure AD i jak to się stało?
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 1f6fd0160988802e198ff9388cfeb3232b34b100
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861123"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Jak i dlaczego aplikacje są dodawane do usługi Azure AD

Istnieją dwie reprezentacje aplikacji w usłudze Azure AD:

* [Obiekty aplikacji](app-objects-and-service-principals.md#application-object) — chociaż istnieją [wyjątki](#notes-and-exceptions), obiekty aplikacji mogą być traktowane jako definicje aplikacji.
* [Nazwy główne usługi](app-objects-and-service-principals.md#service-principal-object) — mogą być uznawane za wystąpienie aplikacji. Jednostki usługi zwykle odwołują się do obiektu aplikacji, a jeden obiekt aplikacji może być przywoływany przez wiele jednostek usługi w różnych katalogach.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Co to są obiekty aplikacji i skąd pochodzą?

[Obiektami aplikacji](app-objects-and-service-principals.md#application-object) można zarządzać w Azure Portal za pomocą funkcji [rejestracji aplikacji](https://aka.ms/appregistrations) . Obiekty aplikacji opisują aplikację w usłudze Azure AD i mogą być uznawane za definicje aplikacji, co pozwala usłudze poznać, jak wystawiać tokeny dla aplikacji na podstawie jej ustawień. Obiekt aplikacji będzie istniał tylko w katalogu macierzystym, nawet jeśli jest to aplikacja wielodostępna obsługująca jednostki usługi w innych katalogach. Obiekt aplikacji może zawierać dowolne z następujących elementów (a także dodatkowe informacje, które nie zostały wymienione w tym miejscu):

* Nazwa, logo i Wydawca
* Identyfikatory URI przekierowania
* Wpisy tajne (klucze symetryczne i/lub asymetryczne używane do uwierzytelniania aplikacji)
* Zależności interfejsu API (OAuth)
* Opublikowane interfejsy API/zasoby/zakresy (OAuth)
* Role aplikacji (RBAC)
* Metadane i konfiguracja logowania jednokrotnego
* Metadane i konfiguracja aprowizacji użytkowników
* Metadane i konfiguracja serwera proxy

Obiekty aplikacji można tworzyć za poorednictwem wielu ścieżek, w tym:

* Rejestracje aplikacji w Azure Portal
* Tworzenie nowej aplikacji przy użyciu programu Visual Studio i Konfigurowanie jej do korzystania z uwierzytelniania usługi Azure AD
* Gdy administrator dodaje aplikację z galerii aplikacji (co spowoduje również utworzenie nazwy głównej usługi)
* Tworzenie nowej aplikacji przy użyciu interfejsu API Microsoft Graph lub programu PowerShell
* Wiele innych, w tym różne środowiska deweloperskie na platformie Azure i środowisko Eksploratora interfejsu API w centrach deweloperów

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Co to są jednostki usługi i skąd pochodzą?

[Jednostkami](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) [usługi](app-objects-and-service-principals.md#service-principal-object) można zarządzać w Azure Portal za pomocą środowiska aplikacji dla przedsiębiorstw. Nazwy główne usługi są zarządzane przez aplikację łączącą się z usługą Azure AD i mogą być uznawane za wystąpienie aplikacji w katalogu. Dla każdej z tych aplikacji może istnieć co najwyżej jeden obiekt aplikacji (który jest zarejestrowany w katalogu "Dom"), a co najmniej jedno obiekty główne usługi reprezentujące wystąpienia aplikacji w każdym katalogu, w którym działa. 

Nazwa główna usługi może obejmować:

* Odwołanie z powrotem do obiektu aplikacji za pomocą właściwości identyfikatora aplikacji
* Rekordy lokalnych użytkowników i grup przypisań ról
* Rekordy uprawnień użytkownika lokalnego i administratora przyznanych aplikacji
  * Na przykład: uprawnienie do aplikacji w celu uzyskania dostępu do wiadomości e-mail określonego użytkownika
* Rekordy zasad lokalnych, w tym zasady dostępu warunkowego
* Rekordy alternatywnych ustawień lokalnych dla aplikacji
  * Reguły przekształcania oświadczeń
  * Mapowania atrybutów (Inicjowanie obsługi użytkownika)
  * Role aplikacji specyficzne dla katalogu (Jeśli aplikacja obsługuje role niestandardowe)
  * Nazwa lub logo specyficzne dla katalogu

Podobnie jak obiekty aplikacji, jednostki usługi mogą być również tworzone za poorednictwem wielu ścieżek, takich jak:

* Gdy użytkownicy logują się do aplikacji innej firmy zintegrowanej z usługą Azure AD
  * Podczas logowania użytkownicy są proszeni o przyznanie uprawnień aplikacji w celu uzyskania dostępu do ich profilu i innych uprawnień. Pierwsza osoba, która wyraża zgodę, powoduje, że jednostka usługi reprezentuje aplikację, która ma zostać dodana do katalogu.
* Gdy użytkownicy logują się do Usługi online firmy Microsoft, takich jak [Microsoft 365](https://products.office.com/)
  * Gdy subskrybujesz Microsoft 365 lub Rozpocznij okres próbny, w katalogu zostanie utworzona co najmniej jedna jednostka usługi, która reprezentuje różne usługi, które są używane do dostarczania wszystkich funkcji skojarzonych z Microsoft 365.
  * Niektóre Microsoft 365 usługi, takie jak program SharePoint, regularnie tworzą jednostki usługi, aby umożliwić bezpieczną komunikację między składnikami, w tym przepływami pracy.
* Gdy administrator dodaje aplikację z galerii aplikacji (spowoduje to również utworzenie bazowego obiektu aplikacji)
* Dodawanie aplikacji do korzystania z [usługi Azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md)
* Łączenie aplikacji do logowania jednokrotnego przy użyciu protokołu SAML lub hasła logowania jednokrotnego (SSO)
* Programowo za pośrednictwem interfejsu API Microsoft Graph lub programu PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Jak obiekty aplikacji i jednostki usługi są ze sobą powiązane?

Aplikacja ma jeden obiekt aplikacji w katalogu macierzystym, do którego odwołuje się co najmniej jedna jednostka usługi w każdym z katalogów, w którym działa (w tym katalog macierzysty aplikacji).

![Pokazuje relację między obiektami aplikacji a jednostkami usługi][apps_service_principals_directory]

Na powyższym diagramie firma Microsoft przechowuje dwa katalogi wewnętrznie (widoczne po lewej stronie) używane do publikowania aplikacji:

* Jeden dla aplikacji firmy Microsoft (katalog usług Microsoft)
* Jeden dla wstępnie zintegrowanych aplikacji innych firm (katalog galerii aplikacji)

Wydawcy aplikacji/dostawcy, którzy integrują się z usługą Azure AD, muszą mieć katalog publikowania (widoczny po prawej stronie jako "część katalogu SaaS").

Aplikacje dodawane samodzielnie (reprezentowane jako **aplikacja (własne)** na diagramie) obejmują:

* Aplikacje opracowane (zintegrowane z usługą Azure AD)
* Aplikacje połączone do logowania jednokrotnego
* Aplikacje opublikowane przy użyciu serwera proxy aplikacji usługi Azure AD

### <a name="notes-and-exceptions"></a>Uwagi i wyjątki

* Nie wszystkie jednostki usługi są z powrotem do obiektu aplikacji. Gdy usługa Azure AD została pierwotnie skompilowana, usługi udostępniane aplikacjom były bardziej ograniczone, a jednostka usługi była wystarczająca do ustanowienia tożsamości aplikacji. Oryginalna jednostka usługi była bliższa w kształcie do konta usługi Active Directory systemu Windows Server. Z tego powodu nadal można tworzyć jednostki usługi za pośrednictwem różnych ścieżek, takich jak korzystanie z programu Azure AD PowerShell, bez wcześniejszego tworzenia obiektu aplikacji. Interfejs API Microsoft Graph wymaga obiektu aplikacji przed utworzeniem jednostki usługi.
* Nie wszystkie informacje opisane powyżej są obecnie udostępniane programowo. Następujące elementy są dostępne tylko w interfejsie użytkownika:
  * Reguły przekształcania oświadczeń
  * Mapowania atrybutów (Inicjowanie obsługi użytkownika)
* Aby uzyskać szczegółowe informacje na temat obiektów głównych i aplikacji usługi, zobacz dokumentację dotyczącą interfejsu API Microsoft Graph:
  * [Aplikacja](/graph/api/resources/application)
  * [Nazwa główna usługi](/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Dlaczego aplikacje integrują się z usługą Azure AD?

Aplikacje są dodawane do usługi Azure AD, aby można było korzystać z co najmniej jednej usługi, którą oferuje, w tym:

* Uwierzytelnianie i autoryzacja aplikacji
* Uwierzytelnianie i autoryzacja użytkowników
* Logowanie jednokrotne przy użyciu Federacji lub hasła
* Inicjowanie obsługi i synchronizacja użytkowników
* Kontrola dostępu oparta na rolach — użyj katalogu do definiowania ról aplikacji do wykonywania kontroli autoryzacji opartej na rolach w aplikacji
* Usługi autoryzacji OAuth — używane przez Microsoft 365 i inne aplikacje firmy Microsoft do autoryzacji dostępu do interfejsów API/zasobów
* Publikowanie aplikacji i serwer proxy — publikowanie aplikacji z sieci prywatnej w Internecie
* Atrybuty rozszerzenia schematu katalogu — [rozszerzanie schematu jednostki usługi i obiektów użytkownika](active-directory-schema-extensions.md) w celu przechowywania dodatkowych danych w usłudze Azure AD 

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kto ma uprawnienia do dodawania aplikacji do mojego wystąpienia usługi Azure AD?

Chociaż istnieją pewne zadania, które mogą wykonywać tylko Administratorzy globalni (takie jak dodawanie aplikacji z galerii aplikacji i Konfigurowanie aplikacji do korzystania z serwera proxy aplikacji), domyślnie wszyscy użytkownicy w katalogu mają prawa do rejestrowania obiektów aplikacji, które są przez nich opracowywane, a także umożliwiają dostęp do danych organizacji za pomocą zgody. Jeśli osoba jest pierwszym użytkownikiem w katalogu w celu zalogowania się do aplikacji i udzielenia zgody, spowoduje to utworzenie nazwy głównej usługi w dzierżawie. w przeciwnym razie informacje o udzieleniu zgody będą przechowywane w istniejącej jednostce usługi.

Umożliwienie użytkownikom rejestrowania i wyrażania zgody na aplikacje może początkowo prowadzić dźwięk, ale należy pamiętać o następujących kwestiach:


* Aplikacje mogą korzystać z systemu Windows Server Active Directory do uwierzytelniania użytkowników przez wiele lat bez konieczności rejestrowania lub rejestrowania aplikacji w katalogu. Teraz organizacja będzie lepiej widzieć, jak wiele aplikacji korzysta z katalogu i w jakim celu.
* Delegowanie tych obowiązków do użytkowników wyklucza konieczność rejestracji i publikowania aplikacji opartych na administratorach. W przypadku Active Directory Federation Services (ADFS) najprawdopodobniej administrator musiał dodać aplikację jako jednostkę uzależnioną w imieniu swoich deweloperów. Teraz deweloperzy mogą samoobsługi.
* W przypadku użytkowników logujących się do aplikacji przy użyciu ich kont organizacji w celach roboczych jest to dobry efekt. Jeśli następnie opuścimy organizację, automatycznie utraci dostęp do swojego konta w aplikacji, z której się korzystali.
* Posiadanie rekordu dotyczącego danych, które zostały przez Ciebie udostępnione aplikacji. Dane są bardziej transportowe niż kiedykolwiek wcześniej i warto mieć jasno zarejestrowany, kto udostępnił dane aplikacji.
* Właściciele interfejsu API, którzy korzystają z usługi Azure AD na potrzeby uwierzytelniania OAuth, muszą dokładnie określić uprawnienia, które użytkownicy mogą udzielić aplikacjom oraz które uprawnienia wymagają od administratora. Tylko Administratorzy mogą wyrazić zgodę na większe zakresy i bardziej znaczące uprawnienia, podczas gdy użytkownik wyraża zgodę na dostęp do danych i możliwości użytkowników.
* Gdy użytkownik doda lub zezwoli aplikacji na dostęp do swoich danych, może być przeprowadzana inspekcja zdarzenia, aby można było wyświetlić raporty inspekcji w ramach Azure Portal, aby określić, jak aplikacja została dodana do katalogu.

Jeśli nadal chcesz uniemożliwić użytkownikom w katalogu rejestrowanie aplikacji i logowanie się do aplikacji bez zatwierdzania przez administratora, istnieją dwa ustawienia, które można zmienić, aby wyłączyć te możliwości:

* Aby uniemożliwić użytkownikom wyrażanie zgody na aplikacje w ich imieniu:
  1. W Azure Portal przejdź do sekcji [Ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) w obszarze aplikacje dla przedsiębiorstw.
  2. Zmiana **użytkowników może wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu** do **nie**.
     
     > [!NOTE]
     > W przypadku podjęcia decyzji o wyłączeniu zgody użytkownika Administrator będzie musiał wyrazić zgodę na nową aplikację, której użytkownik musi użyć.

* Aby uniemożliwić użytkownikom rejestrowanie własnych aplikacji:
  1. W Azure Portal przejdź do sekcji [Ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) w obszarze Azure Active Directory
  2. Zmiana **użytkowników może rejestrować aplikacje** na **nie**.

> [!NOTE]
> Sama firma Microsoft używa konfiguracji domyślnej z użytkownikami, którzy mogą rejestrować aplikacje i wyrażać zgodę na aplikacje w ich imieniu.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
