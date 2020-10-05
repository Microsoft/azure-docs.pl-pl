---
title: Porównanie tożsamości zewnętrznych — Azure Active Directory | Microsoft Docs
description: Tożsamości zewnętrzne usługi Azure AD umożliwiają osobom spoza organizacji dostęp do aplikacji i zasobów przy użyciu własnej tożsamości. Porównanie rozwiązań dla tożsamości zewnętrznych, w tym Azure Active Directory współpracy B2B i Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 08/05/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08623e6d69ac4db1790c1e9b46089f0c72c0526d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87906078"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Co to są tożsamości zewnętrzne w Azure Active Directory?

Tożsamości zewnętrzne w usłudze Azure AD umożliwiają osobom spoza organizacji dostęp do aplikacji i zasobów, jednocześnie pozwalając im na logowanie się przy użyciu dowolnej tożsamości, którą preferują. Partnerzy, dystrybutorzy, dostawcy, dostawcy i inni użytkownicy-Goście mogą "przenieść własne tożsamości". Niezależnie od tego, czy są one częścią usługi Azure AD, czy innym systemem zarządzanym przez firmę Microsoft, czy też mają niezarządzaną tożsamość społecznościową, taką jak Google lub Facebook, mogą używać swoich własnych poświadczeń do logowania. Dostawca tożsamości zarządza tożsamością użytkownika zewnętrznego i zarządza dostępem do aplikacji za pomocą usługi Azure AD w celu zapewnienia ochrony zasobów. 

## <a name="external-identities-scenarios"></a>Scenariusze tożsamości zewnętrznych

Tożsamości zewnętrzne usługi Azure AD koncentrują się mniej na relacji użytkownika z Twoją organizacją i nie tylko w sposób, w jaki osoba chce zalogować się do aplikacji i zasobów. W ramach tej platformy usługa Azure AD obsługuje wiele scenariuszy od współpracy między firmami (B2B) do tworzenia aplikacji dla klientów i konsumentów (Business-to-Consumer lub B2C).

- **Udostępnianie aplikacji użytkownikom zewnętrznym (współpraca B2B)**. Zapraszanie użytkowników zewnętrznych do własnych dzierżawców jako użytkowników "Gość", do których można przypisać uprawnienia (na potrzeby autoryzacji), zezwalając im na używanie istniejących poświadczeń (na potrzeby uwierzytelniania). Użytkownicy logują się do udostępnionych zasobów przy użyciu prostego procesu zaproszenia i wykupu za pomocą konta służbowego, konta szkoły lub dowolnego konta e-mail. Teraz dzięki dostępności przepływów użytkowników do samoobsługowego rejestrowania (wersja zapoznawcza) możesz także udostępnić użytkownikom zewnętrznym środowisko logowania za pomocą aplikacji, którą chcesz udostępnić. Ustawienia przepływu użytkownika można skonfigurować w taki sposób, aby kontrolować sposób, w jaki użytkownik rejestruje się w aplikacji, i umożliwia im korzystanie z konta służbowego, konta szkolnego lub dowolnej tożsamości społecznościowej (np. Google lub Facebook), których chcą używać.  Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją B2B usługi Azure AD](index.yml).

- **Opracowywanie aplikacji przeznaczonych dla innych dzierżawców usługi Azure AD (pojedyncza dzierżawa lub wiele dzierżawców)**. Podczas tworzenia aplikacji dla usługi Azure AD można kierować użytkowników z jednej organizacji (pojedynczej dzierżawy) lub użytkowników z dowolnej organizacji, która ma już dzierżawę usługi Azure AD (nazywaną aplikacjami wielodostępnymi). Te aplikacje z wieloma dzierżawami są rejestrowane raz przez siebie we własnej usłudze Azure AD, ale mogą być używane przez dowolnego użytkownika usługi Azure AD z dowolnej organizacji bez konieczności wykonywania dodatkowych czynności.

- **Opracowywanie aplikacji o białych etykietach dla odbiorców i klientów (Azure AD B2C)**. Jeśli jesteś biznesem lub deweloperem tworzącym aplikacje dla klientów, możesz skalować do klientów, klientów lub obywateli przy użyciu Azure AD B2C. Deweloperzy mogą korzystać z usługi Azure AD jako w pełni funkcjonalnego systemu tożsamości dla swojej aplikacji, jednocześnie umożliwiając klientom logowanie się przy użyciu tożsamości, która już została ustanowiona (na przykład Facebook lub Gmail). Dzięki Azure AD B2C można w pełni dostosowywać i kontrolować sposób tworzenia kont i logowania klientów oraz zarządzania ich profilami podczas korzystania z aplikacji. Aby uzyskać więcej informacji, zobacz [dokumentację Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/).

## <a name="compare-external-identities-solutions"></a>Porównanie rozwiązań dla tożsamości zewnętrznych

Poniższa tabela zawiera szczegółowe porównanie różnych scenariuszy, które można włączyć przy użyciu tożsamości zewnętrznych usługi Azure AD.

| Aplikacje wielodostępne  | Współpraca z użytkownikami zewnętrznymi (B2B) | Aplikacje dla odbiorców lub klientów (B2C)  |
| ---- | --- | --- |
| Scenariusz podstawowy: oprogramowanie jako usługa (SaaS) dla przedsiębiorstw | Scenariusz podstawowy: współpraca przy użyciu aplikacji firmy Microsoft (Microsoft 365, zespołów,...) lub własnego oprogramowania do współpracy.  | Scenariusz podstawowy: aplikacje transakcyjne używające niestandardowych aplikacji.   |
| Przeznaczone dla: organizacje, które chcą udostępnić oprogramowanie wielu klientom korporacyjnym.    | Przeznaczenie: organizacje, które chcą uwierzytelniać użytkowników z organizacji partnerskiej, niezależnie od dostawcy tożsamości.    | Zamierzone dla: zapraszanie klientów aplikacji mobilnych i sieci Web, niezależnie od tego, czy klienci indywidualni, instytucjonalni, czy organizacyjni są w katalogu usługi Azure AD oddzielony od katalogu Twojej organizacji. |
| Obsługiwane tożsamości: pracownicy z kontami usługi Azure AD. | Obsługiwane tożsamości: pracownicy z kontami służbowymi, partnerzy z kontami służbowymi lub dowolny adres e-mail. Wkrótce będzie możliwa obsługa federacji bezpośredniej.      | Obsługiwane tożsamości: użytkownicy konsumenci z kontami aplikacji lokalnych (dowolny adres e-mail lub nazwa użytkownika) albo dowolna obsługiwana tożsamość społecznościowa z federacją bezpośrednią.       |
| Użytkownicy zewnętrzni są zarządzani w ich własnym katalogu, odizolowany od katalogu, w którym zarejestrowano aplikację.    | Użytkownicy zewnętrzni są zarządzani w tym samym katalogu co pracownicy, ale mają specjalne adnotacje. Mogą być zarządzane w taki sam sposób jak pracownicy, ale mogą być dodawane do tych samych grup i tak dalej.    | Użytkownicy zewnętrzni są zarządzani w katalogu aplikacji. Są one zarządzane oddzielnie od pracownika organizacji i katalogu partnera (jeśli istnieją).  |
| Logowanie jednokrotne: obsługiwane jest logowanie jednokrotne do wszystkich aplikacji połączonych z usługą Azure AD.          | Logowanie jednokrotne: obsługiwane jest logowanie jednokrotne do wszystkich aplikacji połączonych z usługą Azure AD. Na przykład możesz zapewnić dostęp do Microsoft 365 lub aplikacji lokalnych oraz innych aplikacji SaaS, takich jak Salesforce lub Workday.    | Logowanie jednokrotne: obsługiwane jest logowanie jednokrotne do aplikacji należących do klienta w ramach dzierżawców Azure AD B2C. Logowanie jednokrotne do Microsoft 365 lub innych aplikacji Microsoft SaaS nie jest obsługiwane.    |
| Cykl życia klientów: zarządzane przez organizację główną użytkownika.      | Cykl życia partnera: zarządzany przez hosta/organizację zapraszającą.    | Cykl życia klienta: samoobsługowy lub zarządzany przez aplikację.      |
| Zasady zabezpieczeń i zgodność: zarządzane przez hosta/zapraszanie organizacji (na przykład z [zasadami dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).           | Zasady zabezpieczeń i zgodność: zarządzane przez hosta/zapraszanie organizacji (na przykład z [zasadami dostępu warunkowego](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)). | Zasady zabezpieczeń i zgodność: zarządzane przez aplikację.        |
| Znakowanie: używana jest marka Host/zapraszanie do organizacji.   | Znakowanie: używana jest marka Host/zapraszanie do organizacji.    | Znakowanie: zarządzane przez aplikację. Zazwyczaj jest używana marka produktu z danymi organizacji w tle.   |
| Więcej informacji: [Zarządzanie tożsamościami w aplikacjach wielodostępnych](https://docs.microsoft.com/azure/architecture/multitenant-identity/), [przewodniku krok po kroku](https://docs.microsoft.com/azure/active-directory/develop/howto-convert-app-to-be-multi-tenant) | Więcej informacji: [wpis w blogu](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentacja](what-is-b2b.md)                   | Więcej informacji: [strona produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentacja](https://docs.microsoft.com/azure/active-directory-b2c/)       |

Zabezpiecz klientów i partnerów poza granicami organizacyjnymi przy użyciu tożsamości zewnętrznych usługi Azure AD i zarządzaj nimi.

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Informacje na temat usługi Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview)
