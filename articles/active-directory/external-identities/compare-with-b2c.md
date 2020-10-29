---
title: Porównanie tożsamości zewnętrznych — Azure Active Directory | Microsoft Docs
description: Tożsamości zewnętrzne usługi Azure AD umożliwiają osobom spoza organizacji dostęp do aplikacji i zasobów przy użyciu własnej tożsamości. Porównanie rozwiązań dla tożsamości zewnętrznych, w tym Azure Active Directory współpracy B2B i Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 10/23/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 420cc2bc38d079fa95f6b90ed20cb31e994f4ea3
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027078"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Co to są tożsamości zewnętrzne w Azure Active Directory?

Tożsamości zewnętrzne w usłudze Azure AD umożliwiają osobom spoza organizacji dostęp do aplikacji i zasobów, jednocześnie pozwalając im na logowanie się przy użyciu dowolnej tożsamości, którą preferują. Partnerzy, dystrybutorzy, dostawcy, dostawcy i inni użytkownicy-Goście mogą "przenieść własne tożsamości". Niezależnie od tego, czy mają tożsamość elektroniczną wydaną przez firmę, czy przez rząd lub niezarządzaną tożsamość społecznościową, taką jak Google lub Facebook, mogą korzystać z własnych poświadczeń w celu zalogowania się. Dostawca tożsamości użytkownika zewnętrznego zarządza swoją tożsamością i umożliwia zarządzanie dostępem do aplikacji za pomocą usługi Azure AD w celu zapewnienia ochrony zasobów.

## <a name="external-identities-scenarios"></a>Scenariusze tożsamości zewnętrznych

Tożsamości zewnętrzne usługi Azure AD koncentrują się mniej na relacji użytkownika z Twoją organizacją i nie tylko w tym, jak użytkownik chce zalogować się do aplikacji i zasobów. W ramach tej platformy usługa Azure AD obsługuje wiele scenariuszy od współpracy między firmami (B2B, Business-to-Business) w celu zarządzania dostępem do aplikacji przeznaczonych dla klientów/klienta lub dla obywateli (Business-to-Customer, lub B2C).

- **Udostępnianie aplikacji i zasobów użytkownikom zewnętrznym (współpraca B2B)** . Zapraszanie użytkowników zewnętrznych do własnej dzierżawy jako użytkowników "Gość", do których można przypisać uprawnienia (na potrzeby autoryzacji), zezwalając im na używanie istniejących poświadczeń (na potrzeby uwierzytelniania). Użytkownicy logują się do udostępnionych zasobów przy użyciu prostego procesu zaproszenia i wykupu przy użyciu swojego konta służbowego lub szkolnego. [Zarządzanie prawami w usłudze Azure AD](../governance/entitlement-management-overview.md) umożliwia również Konfigurowanie zasad służących do [zarządzania dostępem użytkowników zewnętrznych](../governance/entitlement-management-external-users.md#how-access-works-for-external-users). Teraz dzięki dostępności [przepływów użytkowników do samoobsługowego rejestrowania (wersja zapoznawcza)](self-service-sign-up-overview.md)możesz zezwolić zewnętrznym użytkownikom na rejestrowanie się w przypadku aplikacji. Środowisko można dostosować tak, aby zezwalało na logowanie się przy użyciu tożsamości służbowej, szkolnej lub społecznościowej (np. Google lub Facebook). Możesz również zbierać informacje o użytkowniku podczas procesu rejestracji. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją B2B usługi Azure AD](index.yml).

- **Twórz podróże użytkowników przy użyciu rozwiązania do zarządzania tożsamościami w postaci białych etykiet dla aplikacji przeznaczonych dla klientów i klientów (Azure AD B2C)** . Jeśli jesteś biznesem lub deweloperem tworzącym aplikacje dla klientów, możesz skalować do milionów klientów, klientów lub obywateli przy użyciu Azure AD B2C. Deweloperzy mogą korzystać z usługi Azure AD jako w pełni funkcjonalnego systemu zarządzania tożsamościami i dostępem klientów (CIAM) dla swoich aplikacji. Klienci mogą zalogować się przy użyciu tożsamości, które zostały już ustanowione (na przykład Facebook lub Gmail). Dzięki Azure AD B2C można w pełni dostosowywać i kontrolować sposób tworzenia kont i logowania klientów oraz zarządzania ich profilami podczas korzystania z aplikacji. Aby uzyskać więcej informacji, zobacz [dokumentację Azure AD B2C](../../active-directory-b2c/index.yml).

## <a name="compare-external-identities-solutions"></a>Porównanie rozwiązań dla tożsamości zewnętrznych

Poniższa tabela zawiera szczegółowe porównanie scenariuszy, które można włączyć przy użyciu tożsamości zewnętrznych usługi Azure AD.

|   | Współpraca z użytkownikami zewnętrznymi (B2B) | Dostęp do aplikacji dla użytkowników i klientów (B2C)  |
| ---- | --- | --- |
| **Scenariusz podstawowy** | Współpraca przy użyciu aplikacji firmy Microsoft (Microsoft 365, zespołów itp.) lub własnych aplikacji (aplikacje SaaS, aplikacje opracowane niestandardowo itp.).  | Zarządzanie tożsamościami i dostępem dla nowoczesnych SaaS lub niestandardowych aplikacji (nie aplikacji firmy Microsoft).   |
| **Przeznaczone dla**    | Współpraca z partnerami biznesowymi z organizacji zewnętrznych, takich jak dostawcy, partnerzy i dostawcy. Użytkownicy są wyświetlani jako użytkownicy-Goście w Twoim katalogu. Ci użytkownicy mogą lub nie mogą nimi zarządzać.  | Klienci danego produktu. Tymi użytkownikami zarządza się w oddzielnym katalogu usługi Azure AD.  |
| **Obsługiwani dostawcy tożsamości** | Użytkownicy zewnętrzni mogą współpracować przy użyciu kont służbowych, kont szkolnych, dowolnego adresu e-mail, opartych na protokole SAML i WS-Fed dostawców tożsamości, usługi Gmail i serwisu Facebook.  | Użytkownicy-odbiorcy z lokalnymi kontami aplikacji (dowolny adres e-mail lub nazwa użytkownika), różne obsługiwane tożsamości społecznościowe i użytkownicy z tożsamościami wystawionymi przez firmę i rządów publicznych za pośrednictwem bezpośredniej Federacji.       |
| **Zarządzanie użytkownikami zewnętrznymi**   | Użytkownicy zewnętrzni są zarządzani w tym samym katalogu co pracownicy, ale zazwyczaj są adnotacjami jako użytkownicy-Goście. Użytkownicy-Goście mogą być zarządzani w taki sam sposób jak pracownicy, dodawani do tych samych grup i tak dalej.    | Użytkownicy zewnętrzni są zarządzani w katalogu Azure AD B2C. Są one zarządzane oddzielnie od pracownika organizacji i katalogu partnera (jeśli istnieją).  |
| **Logowanie jednokrotne**      | Obsługiwane jest logowanie jednokrotne do wszystkich aplikacji połączonych z usługą Azure AD. Na przykład możesz zapewnić dostęp do Microsoft 365 lub aplikacji lokalnych oraz innych aplikacji SaaS, takich jak Salesforce lub Workday.    | Logowanie jednokrotne do aplikacji należących do klienta w ramach dzierżaw usługi Azure AD B2C jest obsługiwane. Logowanie jednokrotne do Microsoft 365 lub innych aplikacji Microsoft SaaS nie jest obsługiwane.    |
| **Zasady zabezpieczeń i zgodność**        | Zarządzane przez hosta/zapraszanie organizacji (na przykład z [zasadami dostępu warunkowego](conditional-access.md)). | Zarządzane przez organizację za pośrednictwem dostępu warunkowego i ochrony tożsamości.        |
| **Znakowanie**  | Używana jest marka hosta/zapraszania organizacji.    | W pełni dostosowywalne oznakowanie na aplikację lub organizację.   |
| **Model rozliczania** | [Cennik dotyczący tożsamości zewnętrznych](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) na podstawie aktywnych użytkowników w miesiącu (Mau). <br>(Zobacz również: [szczegóły konfiguracji B2B](external-identities-pricing.md)) | [Cennik dotyczący tożsamości zewnętrznych](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) na podstawie aktywnych użytkowników w miesiącu (Mau). <br>(Zobacz również: [B2C — szczegóły instalacji](../../active-directory-b2c/billing.md)) |
| **Więcej informacji** | [wpis w blogu](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentacja](what-is-b2b.md)                   | [strona produktu](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentacja](../../active-directory-b2c/index.yml)       |

Zabezpiecz klientów i partnerów poza granicami organizacyjnymi przy użyciu tożsamości zewnętrznych usługi Azure AD i zarządzaj nimi.

## <a name="about-multitenant-applications"></a>Informacje o aplikacjach wielodostępnych

Jeśli udostępniasz aplikację jako usługę i nie chcesz zarządzać kontami użytkowników, możesz wybrać aplikację wielodostępną. Gdy opracowujesz aplikacje przeznaczone dla innych dzierżawców usługi Azure AD, możesz kierować użytkowników z jednej organizacji (pojedynczego dzierżawy) lub użytkowników z dowolnej organizacji, która ma już dzierżawę usługi Azure AD (aplikacje wielodostępne). Rejestracje aplikacji w usłudze Azure AD jest domyślnie jedną dzierżawą, ale można dokonać rejestracji wielodostępnej. Ta aplikacja wielodostępna jest już zarejestrowana w usłudze Azure AD. Jednak każdy użytkownik usługi Azure AD z dowolnej organizacji może korzystać z aplikacji bez dodatkowej pracy z Twoją częścią. Aby uzyskać więcej [informacji, zobacz](../develop/howto-convert-app-to-be-multi-tenant.md) [Zarządzanie tożsamościami w aplikacjach wielodostępnych](https://docs.microsoft.com/azure/architecture/multitenant-identity/).

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Informacje na temat usługi Azure AD B2C](../../active-directory-b2c/overview.md)
