---
title: Jak i dlaczego aplikacje są dodawane do usługi Azure AD
titleSuffix: Microsoft identity platform
description: Co oznacza dodanie aplikacji do usługi Azure AD i jak można ją tam uzyskać?
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
ms.openlocfilehash: ac02638dfdef4867e93e277175df82be18be66a7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530090"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Jak i dlaczego aplikacje są dodawane do usługi Azure AD

Istnieją dwie reprezentacje aplikacji w usłudze Azure AD:

* [Obiekty aplikacji](app-objects-and-service-principals.md#application-object) — chociaż istnieją [wyjątki,](#notes-and-exceptions)obiekty aplikacji można traktować jako definicję aplikacji.
* [Jednostki usługi](app-objects-and-service-principals.md#service-principal-object) — mogą być traktowane jako wystąpienie aplikacji. Jednostki usługi zazwyczaj odwołują się do obiektu aplikacji, a jeden obiekt aplikacji może być przywołyny przez wiele podmiotów głównych usługi w różnych katalogach.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Co to są obiekty aplikacji i skąd pochodzą?

Obiektami aplikacji [można zarządzać](app-objects-and-service-principals.md#application-object) w Azure Portal za pomocą [usługi Rejestracje](https://aka.ms/appregistrations) aplikacji. Obiekty aplikacji opisują aplikację w usłudze Azure AD i można ją traktować jako definicję aplikacji, dzięki czemu usługa może wiedzieć, jak wystawiać tokeny dla aplikacji na podstawie jej ustawień. Obiekt aplikacji będzie istniał tylko w katalogu macierzystym, nawet jeśli jest to aplikacja wielodostępna obsługująca jednostki usługi w innych katalogach. Obiekt aplikacji może zawierać dowolne z następujących informacji (a także dodatkowe informacje, które nie zostały wymienione tutaj):

* Nazwa, logo i wydawca
* Przekierowywanie URI
* Wpisy tajne (klucze symetryczne i/lub asymetryczne używane do uwierzytelniania aplikacji)
* Zależności interfejsu API (OAuth)
* Opublikowane interfejsy API/zasoby/zakresy (OAuth)
* Role aplikacji (RBAC)
* Metadane i konfiguracja logowania jednokrotnego
* Metadane i konfiguracja aprowizowania użytkowników
* Metadane i konfiguracja serwera proxy

Obiekty aplikacji można tworzyć za pomocą wielu ścieżek, w tym:

* Rejestracje aplikacji w Azure Portal
* Tworzenie nowej aplikacji przy użyciu Visual Studio i konfigurowanie jej do korzystania z uwierzytelniania usługi Azure AD
* Gdy administrator dodaje aplikację z galerii aplikacji (co spowoduje również utworzenie jednostki usługi)
* Używanie interfejsu API Microsoft Graph lub programu PowerShell do tworzenia nowej aplikacji
* Wiele innych, w tym różne doświadczenia deweloperów na platformie Azure i w eksploratorze interfejsów API w centrach deweloperów

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Co to są jednostki usługi i skąd pochodzą?

Jednostkami usług [w chmurze](app-objects-and-service-principals.md#service-principal-object) można zarządzać za Azure Portal za pomocą [aplikacji dla](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) przedsiębiorstw. Jednostki usług określają aplikację łączącą się z usługą Azure AD i mogą być traktowane jako wystąpienie aplikacji w katalogu. Dla każdej aplikacji może mieć co najmniej jeden obiekt aplikacji (który jest zarejestrowany w katalogu "home") i co najmniej jeden obiekt jednostki usługi reprezentujący wystąpienia aplikacji w każdym katalogu, w którym działa. 

Jednostką usługi może być:

* Odwołanie z powrotem do obiektu aplikacji za pośrednictwem właściwości identyfikatora aplikacji
* Rekordy przypisań roli aplikacji użytkownika lokalnego i grupy
* Rekordy uprawnień użytkownika lokalnego i administratora przyznanych aplikacji
  * Na przykład: uprawnienie aplikacji do uzyskiwania dostępu do poczty e-mail określonego użytkownika
* Rekordy zasad lokalnych, w tym zasad dostępu warunkowego
* Rekordy alternatywnych ustawień lokalnych dla aplikacji
  * Reguły przekształcania oświadczeń
  * Mapowania atrybutów (aprowizowanie użytkowników)
  * Role aplikacji specyficzne dla katalogu (jeśli aplikacja obsługuje role niestandardowe)
  * Nazwa lub logo specyficzne dla katalogu

Podobnie jak obiekty aplikacji jednostki usługi można również tworzyć za pomocą wielu ścieżek, takich jak:

* Gdy użytkownicy logują się do aplikacji innej firmy zintegrowanej z usługą Azure AD
  * Podczas logowania użytkownicy są proszeni o nadanie aplikacji uprawnień dostępu do swojego profilu i innych uprawnień. Pierwsza osoba, która wyraziła zgodę, powoduje dodanie jednostki usługi reprezentującej aplikację do katalogu.
* Gdy użytkownicy logują się do usługi Microsoft Usługi online na [przykład Microsoft 365](https://products.office.com/)
  * Jeśli subskrybujesz subskrypcję usługi Microsoft 365 lub rozpoczynasz okres próbny, co najmniej jedna nazwa główna usługi jest tworzona w katalogu reprezentującym różne usługi, które są używane do dostarczania wszystkich funkcji skojarzonych z Microsoft 365.
  * Niektóre Microsoft 365 takie jak SharePoint tworzą jednostki usługi na bieżąco, aby umożliwić bezpieczną komunikację między składnikami, w tym przepływami pracy.
* Gdy administrator doda aplikację z galerii aplikacji (spowoduje to również utworzenie bazowego obiektu aplikacji)
* Dodawanie aplikacji do korzystania z usługi [Azure AD serwer proxy aplikacji](../manage-apps/application-proxy.md)
* Łączenie aplikacji na temat logowania jednokrotnego przy użyciu logowania jednokrotnego SAML lub hasła
* Programowo za pośrednictwem interfejsu API Microsoft Graph lub programu PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>W jaki sposób obiekty aplikacji i jednostki usługi są ze sobą powiązane?

Aplikacja ma w swoim katalogu macierzystym jeden obiekt aplikacji, do którego odwołuje się co najmniej jeden obiekt usługi w każdym z katalogów, w których działa (w tym katalog macierzysty aplikacji).

![Pokazuje relację między obiektami aplikacji i jednostkami usługi][apps_service_principals_directory]

Na powyższym diagramie firma Microsoft utrzymuje dwa katalogi wewnętrznie (wyświetlane po lewej stronie), których używa do publikowania aplikacji:

* Jeden dla Microsoft Apps (usługi firmy Microsoft katalogu)
* Jeden dla wstępnie zintegrowanych aplikacji innych firm (katalog galerii aplikacji)

Wydawcy/dostawcy aplikacji, którzy integrują się z usługą Azure AD, muszą mieć katalog publikowania (widoczny po prawej stronie jako "Some SaaS Directory").

Aplikacje, które dodajesz samodzielnie (reprezentowane jako **aplikacja (Twoja)** na diagramie), obejmują:

* Opracowane aplikacje (zintegrowane z usługą Azure AD)
* Aplikacje, które nałączono do logowania pojedynczego
* Aplikacje opublikowane przy użyciu serwera proxy aplikacji usługi Azure AD

### <a name="notes-and-exceptions"></a>Uwagi i wyjątki

* Nie wszystkie jednostki usługi wskazują obiekt aplikacji. Podczas tworzenia usługi Azure AD usługi udostępniane aplikacjom były bardziej ograniczone, a nazwa główna usługi była wystarczająca do ustanowienia tożsamości aplikacji. Oryginalna jednostka usługi była bliżej Windows Server Active Directory konta usługi. Z tego powodu nadal można tworzyć jednostki usługi za pomocą różnych ścieżek, takich jak użycie programu Azure AD PowerShell, bez wcześniejszego tworzenia obiektu aplikacji. Interfejs MICROSOFT GRAPH API wymaga obiektu aplikacji przed utworzeniem jednostki usługi.
* Nie wszystkie opisane powyżej informacje są obecnie udostępniane programowo. Następujące elementy są dostępne tylko w interfejsie użytkownika:
  * Reguły przekształcania oświadczeń
  * Mapowania atrybutów (aprowizowanie użytkowników)
* Aby uzyskać bardziej szczegółowe informacje na temat jednostki usługi i obiektów aplikacji, zobacz dokumentację interfejsu MICROSOFT GRAPH API:
  * [Aplikacja](/graph/api/resources/application)
  * [Jednostka usługi](/graph/api/resources/serviceprincipal)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Dlaczego aplikacje integrują się z usługą Azure AD?

Aplikacje są dodawane do usługi Azure AD w celu wykorzystania co najmniej jednej z oferowanych przez nie usług, takich jak:

* Uwierzytelnianie i autoryzacja aplikacji
* Uwierzytelnianie i autoryzacja użytkowników
* Logowanie jednokrotne przy użyciu federacji lub hasła
* Aprowizowanie i synchronizacja użytkowników
* Kontrola dostępu oparta na rolach — używanie katalogu do definiowania ról aplikacji w celu przeprowadzania kontroli autoryzacji opartej na rolach w aplikacji
* Usługi autoryzacji OAuth — używane przez Microsoft 365 i inne aplikacje firmy Microsoft do autoryzacji dostępu do interfejsów API/zasobów
* Publikowanie aplikacji i serwer proxy — publikowanie aplikacji z sieci prywatnej w Internecie
* Atrybuty rozszerzenia schematu katalogu — rozszerzanie schematu obiektów [jednostki usługi](active-directory-schema-extensions.md) i użytkownika w celu przechowywania dodatkowych danych w usłudze Azure AD 

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kto ma uprawnienia do dodawania aplikacji do mojego wystąpienia usługi Azure AD?

Chociaż istnieją pewne zadania, które mogą wykonywać tylko administratorzy globalni (np. dodawanie aplikacji z galerii aplikacji i konfigurowanie aplikacji do korzystania z usługi serwer proxy aplikacji), domyślnie wszyscy użytkownicy w katalogu mają uprawnienia do rejestrowania obiektów aplikacji, które opracowują, i dyskrecji, w których aplikacje są przez nich współużytkowane/zapewniają dostęp do danych organizacji za pośrednictwem zgody. Jeśli użytkownik jest pierwszym użytkownikiem w katalogu, który zalogował się do aplikacji i wyraził zgodę, spowoduje to utworzenie jednostki usługi w dzierżawie. W przeciwnym razie informacje o udzieleniu zgody będą przechowywane w istniejącej jednostki usługi.

Zezwolenie użytkownikom na rejestrację i wyrażanie zgody na aplikacje może początkowo brzmieć zgodnie z tym, ale należy pamiętać o następujących kwestiach:


* Aplikacje przez wiele lat mogły korzystać Windows Server Active Directory na potrzeby uwierzytelniania użytkowników bez konieczności rejestrowania lub rejestrowania aplikacji w katalogu. Teraz organizacja będzie mieć lepszy wgląd w to, ile aplikacji korzysta z katalogu i w jakim celu.
* Delegowanie tych obowiązków do użytkowników nie ma potrzeby rejestracji i publikowania aplikacji sterowanej przez administratora. W Active Directory Federation Services (ADFS) prawdopodobnie administrator musiał dodać aplikację jako jednostkę jednostki zależnej w imieniu swoich deweloperów. Teraz deweloperzy mogą korzystać z samoobsługi.
* Użytkownicy logujący się do aplikacji przy użyciu kont organizacji w celach biznesowych są dobrym rozwiązaniem. Jeśli użytkownik opuści organizację, automatycznie utraci dostęp do swojego konta w aplikacji, z których korzysta.
* Dobrym rozwiązaniem jest posiadanie rekordu danych, które były udostępniane którym aplikacjom. Dane są bardziej transportowalne niż kiedykolwiek wcześniej i warto mieć jasny rejestr osób, które udostępniły dane którym aplikacjom.
* Właściciele interfejsu API, którzy używają usługi Azure AD do uwierzytelniania OAuth, decydują dokładnie, jakie uprawnienia użytkownicy mogą przyznać aplikacjom, a które wymagają zgody administratora. Tylko administratorzy mogą wyrazić zgodę na większe zakresy i bardziej znaczące uprawnienia, podczas gdy zgoda użytkownika jest w zakresie danych i możliwości użytkowników.
* Gdy użytkownik dodaje lub zezwala aplikacji na dostęp do swoich danych, zdarzenie może być inspekcji, dzięki czemu można wyświetlić raporty inspekcji w usłudze Azure Portal, aby określić, jak aplikacja została dodana do katalogu.

Jeśli nadal chcesz uniemożliwić użytkownikom w katalogu rejestrowanie aplikacji i logowanie się do aplikacji bez zatwierdzania przez administratora, istnieją dwa ustawienia, które można zmienić, aby wyłączyć te możliwości:

* Aby uniemożliwić użytkownikom wyrażanie zgody na aplikacje we własnym imieniu:
  1. W Azure Portal przejdź do sekcji [Ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) w obszarze Aplikacje dla przedsiębiorstw.
  2. Zmień ustawienia Użytkownicy mogą wyrazić zgodę na dostęp aplikacji do danych **firmy w ich imieniu na** **nie.**
     
     > [!NOTE]
     > Jeśli zdecydujesz się wyłączyć zgodę użytkownika, administrator będzie musi wyrazić zgodę na każdą nową aplikację, z których użytkownik musi korzystać.

* Aby uniemożliwić użytkownikom rejestrowanie własnych aplikacji:
  1. W Azure Portal przejdź do sekcji [Ustawienia użytkownika](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) w obszarze Azure Active Directory
  2. Zmień **ustawienia Użytkownicy mogą rejestrować aplikacje na** **nie.**

> [!NOTE]
> Sama firma Microsoft używa konfiguracji domyślnej z użytkownikami, którzy mogą rejestrować aplikacje i wyrażać zgodę na aplikacje we własnym imieniu.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
