---
title: Plany wdrażania — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Kompleksowe wskazówki dotyczące wdrażania wielu funkcji usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7a596454a48a1d6fcee77634363dd38f34a4d58
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603350"
---
# <a name="azure-active-directory-deployment-plans"></a>Plany wdrażania usługi Azure Active Directory
Szukasz kompleksowych wskazówek dotyczących wdrażania funkcji usługi Azure Active Directory (Azure AD)? Plany wdrażania usługi Azure AD prowadzą cię przez wartość biznesową, zagadnienia dotyczące planowania i procedury operacyjne potrzebne do pomyślnego wdrożenia wspólnych możliwości usługi Azure AD.

Na dowolnej stronie planu użyj funkcji Drukuj w formacie PDF przeglądarki, aby utworzyć aktualną wersję dokumentacji offline w trybie offline.
## <a name="include-the-right-stakeholders"></a>Uwzględnij odpowiednie zainteresowane strony

Rozpoczynając planowanie wdrożenia dla nowych możliwości, ważne jest, aby uwzględnić kluczowych interesariuszy w całej organizacji. Zaleca się zidentyfikowanie i udokumentowanie osoby lub osób, które spełniają każdą z następujących ról, oraz współpracę z nimi w celu określenia ich zaangażowania w projekt.  

Role mogą obejmować następujące 

|Rola |Opis |
|-|-|
|Użytkowników końcowych|Reprezentatywna grupa użytkowników, dla których zostanie zaimplementowana funkcja. Często wyświetla podgląd zmian w programie pilotażowym.
|Kierownik pomocy technicznej IT|Przedstawiciel organizacji pomocy technicznej IT, który może dostarczyć informacji na temat możliwości wsparcia tej zmiany z punktu widzenia pomocy technicznej.  
|Architekt tożsamości lub administrator globalny platformy Azure|Przedstawiciel zespołu zarządzania tożsamościami odpowiedzialny za zdefiniowanie sposobu dostosowania tej zmiany do podstawowej infrastruktury zarządzania tożsamościami w organizacji.|
|Właściciel firmy aplikacji |Ogólny właściciel firmy aplikacji, których dotyczy problem, które mogą obejmować zarządzanie dostępem.Może również zapewnić dane wejściowe na temat środowiska użytkownika i przydatność tej zmiany z punktu widzenia użytkownika końcowego.
|Właściciel zabezpieczeń|Przedstawiciel zespołu zabezpieczeń, który może się podpisać, że plan spełni wymagania dotyczące zabezpieczeń organizacji.|
|Menedżer zgodności|Osoba w organizacji odpowiedzialna za zapewnienie zgodności z wymaganiami korporacyjnymi, branżowymi lub rządowymi.|

**Poziom zaangażowania może obejmować:**

- **R**przeznaczone do realizacji planu projektu i jego wyników 

- **Pproval**planu projektu i wyników 

- **C**ontributor do planu projektu i wyników 

- **I**nformed planu projektu i wyników


## <a name="best-practices-for-a-pilot"></a>Najlepsze praktyki dla pilota
Pilot pozwala przetestować z małą grupą przed włączeniem możliwości dla wszystkich. Upewnij się, że w ramach testowania każdy przypadek użycia w organizacji jest dokładnie testowany. Przed wprowadzeniem tej funkcji do całej organizacji najlepiej jest kierować reklamy do określonej grupy użytkowników pilotażowych.

W pierwszej fali kieruj się na it, użyteczność i innych odpowiednich użytkowników, którzy mogą testować i przekazywać opinie. Ta opinia powinna być wykorzystywana do dalszego rozwijania komunikatów i instrukcji wysyłanych do użytkowników oraz do przekazywania informacji na temat rodzajów problemów, które mogą widzieć pracownicy pomocy technicznej. 

Rozszerzenie wdrożenia na większe grupy użytkowników powinno zostać przeprowadzone poprzez zwiększenie zakresu ukierunkowanych grup. Można to zrobić za pomocą [dynamicznego członkostwa w grupie](../users-groups-roles/groups-dynamic-membership.md)lub ręcznie dodając użytkowników do grup docelowych.


## <a name="deploy-authentication"></a>Wdrażanie uwierzytelniania

| Możliwości | Opis|
| -| -|
| [Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)| Azure Multi-Factor Authentication (MFA) to rozwiązanie firmy Microsoft służące do przeprowadzania weryfikacji dwuetapowej. Przy użyciu metod uwierzytelniania zatwierdzonych przez administratorów usługa Azure MFA pomaga chronić dostęp do danych i aplikacji, spełniając jednocześnie zapotrzebowanie na prosty proces logowania. |
| [Dostęp warunkowy](../conditional-access/plan-conditional-access.md)| Dzięki dostępowi warunkowemu można zaimplementować decyzje o automatycznej kontroli dostępu dla osób, które mogą uzyskiwać dostęp do aplikacji w chmurze na podstawie warunków. |
| [Samoobsługowe resetowanie haseł](../authentication/howto-sspr-deployment.md)| Samoobsługowe resetowanie hasła pomaga użytkownikom resetować hasła bez interwencji administratora, kiedy i gdzie muszą. |
| [Logowanie bez hasła](../authentication/howto-authentication-passwordless-deployment.md) | Implementowanie uwierzytelniania bez hasła przy użyciu aplikacji Microsoft Authenticator lub kluczy zabezpieczeń FIDO2 w organizacji |

## <a name="deploy-application-management"></a>Wdrażanie zarządzania aplikacjami

| Możliwości | Opis|
| -| - |
| [Rejestracja jednokrotna](../manage-apps/plan-sso-deployment.md)| Logowanie jednokrotne ułatwia użytkownikom dostęp do aplikacji i zasobów potrzebnych do prowadzenia działalności gospodarczej podczas logowania się tylko raz. Po zalogowaniu się mogą przejść z pakietu Microsoft Office do SalesForce do Box do aplikacji wewnętrznych bez konieczności wprowadzania poświadczeń po raz drugi. |
| [Panel dostęp](../manage-apps/access-panel-deployment-plan.md)| Zaoferuj użytkownikom proste centrum do odnajdowania i uzyskiwania dostępu do wszystkich ich aplikacji. Zapewnij im większą produktywność dzięki funkcjom samoobsługowym, takim jak żądanie dostępu do aplikacji i grup lub zarządzanie dostępem do zasobów w imieniu innych osób. |


## <a name="deploy-hybrid-scenarios"></a>Wdrażanie scenariuszy hybrydowych

| Możliwości | Opis|
| -| -|
| [Synchronizowanie skrótów haseł za pomocą usługi ADFS](../hybrid/plan-migrate-adfs-password-hash-sync.md)| Dzięki synchronizacji skrótów haseł haseł użytkowników skróty są synchronizowane z lokalnej usługi Active Directory do usługi Azure AD, dzięki czemu usługa Azure AD uwierzytelnia użytkowników bez interakcji z lokalną usługą Active Directory |
| [Uwierzytelnianie przekazywane za pomocą usługi ADFS](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Uwierzytelnianie przekazywania usługi Azure AD ułatwia użytkownikom logowanie się do aplikacji lokalnych i opartych na chmurze przy użyciu tych samych haseł. Ta funkcja zapewnia użytkownikom lepsze środowisko — o jedno hasło mniej do zapamiętania — i zmniejsza koszty pomocy technicznej DZIAŁU IT, ponieważ użytkownicy rzadziej zapominają o tym, jak się zalogować. Gdy użytkownicy logują się za pomocą usługi Azure AD, ta funkcja weryfikuje ich hasła bezpośrednio w lokalnej usłudze Active Directory. |
| [Serwer proxy aplikacji usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) |Obecnie pracownicy chcą pracować wydajnie w dowolnym miejscu i czasie, na dowolnym urządzeniu. Muszą uzyskać dostęp do aplikacji SaaS w chmurze i aplikacjach firmowych lokalnie. Serwer proxy aplikacji usługi Azure AD umożliwia ten niezawodny dostęp bez kosztownych i złożonych wirtualnych sieci prywatnych (VPN) lub stref zdemilitaryzowanych (DMZ). |
| [Bezproblemowe logowanie jednokrotne](../hybrid/how-to-connect-sso-quick-start.md)| Bezproblemowe logowanie jednokrotne w usłudze Azure Active Directory zapewnia automatyczne logowanie użytkowników, gdy ich urządzenia są połączone z siecią firmową. Dzięki tej funkcji użytkownicy nie będą musieli wpisywać swoich haseł, aby zalogować się do usługi Azure AD i zwykle nie będą musieli wprowadzać swoich nazw użytkowników. Ta funkcja zapewnia autoryzowanym użytkownikom łatwy dostęp do aplikacji chmurowych bez konieczności stosowania dodatkowych składników lokalnych. |

## <a name="deploy-user-provisioning"></a>Wdrażanie inicjowania obsługi administracyjnej użytkowników

| Możliwości | Opis|
| -| -|
| [Aprowizowanie użytkowników](../app-provisioning/plan-auto-user-provisioning.md)| Usługa Azure AD ułatwia automatyzację tworzenia, obsługi i usuwania tożsamości użytkowników w aplikacjach w chmurze (SaaS), takich jak Dropbox, Salesforce, ServiceNow i nie tylko. |
| [Inicjowanie obsługi administracyjnej użytkowników działu hr w chmurze](../app-provisioning/plan-cloud-hr-provision.md)| Inicjowanie obsługi administracyjnej użytkowników usług W chmurze do usługi Active Directory tworzy podstawę dla bieżącego zarządzania tożsamościami i poprawia jakość procesów biznesowych, które opierają się na autorytatywnych danych tożsamości. Korzystając z tej funkcji z produktem hr w chmurze, takim jak Workday lub Successfactors, można bezproblemowo zarządzać cyklem życia tożsamości pracowników i pracowników warunkowych, konfigurując reguły mapujące procesy stolarz-mover-leaver (takie jak Nowy wynajem, Zakończenie, Przeniesienie) na akcje inicjowania obsługi administracyjnej IT (takie jak Tworzenie, Włączanie, Wyłączanie) |

## <a name="deploy-governance-and-reporting"></a>Wdrażanie nadzoru i raportowania

| Możliwości | Opis|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Usługa Azure AD Privileged Identity Management (PIM) ułatwia zarządzanie uprzywilejowanymi rolami administracyjnymi w usługach Azure AD, zasobach platformy Azure i innych usługach online firmy Microsoft. Pim zapewnia rozwiązania, takie jak dostęp just-in-time, przepływy pracy zatwierdzania żądań i w pełni zintegrowane przeglądy dostępu, dzięki czemu można identyfikować, odkrywać i zapobiegać złośliwym działaniom ról uprzywilejowanych w czasie rzeczywistym. |
| [Sprawozdawczość i monitorowanie](../reports-monitoring/plan-monitoring-and-reporting.md)| Projekt rozwiązania do raportowania i monitorowania usługi Azure AD zależy od wymagań prawnych, zabezpieczeń i operacyjnych, a także istniejącego środowiska i procesów. W tym artykule przedstawiono różne opcje projektowania i przewodnik do odpowiedniej strategii wdrażania. |
