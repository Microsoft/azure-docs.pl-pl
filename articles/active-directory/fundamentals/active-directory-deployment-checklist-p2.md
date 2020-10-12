---
title: Lista kontrolna dotycząca wdrażania usługi Azure AD
description: Lista kontrolna wdrażania funkcji Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd33845c331f907dbd5720ac92c6b1c627f01873
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89318413"
---
# <a name="azure-active-directory-feature-deployment-guide"></a>Przewodnik wdrażania funkcji usługi Azure Active Directory

Może wydawać się zniechęcające wdrożenie Azure Active Directory (Azure AD) w organizacji i zachowanie bezpieczeństwa. Ten artykuł zawiera informacje o typowych zadaniach, które mogą być przydatne dla klientów w fazach, w ciągu 30, 60, 90 dni lub więcej, aby zwiększyć ich stan bezpieczeństwa. Nawet organizacje, które już wdrożyły usługę Azure AD, mogą korzystać z tego przewodnika, aby upewnić się, że najprawdopodobniej wykorzystają one z inwestycji.

Dobrze zaplanowana i uruchomiona infrastruktura tożsamości paves sposób bezpiecznego dostępu do obciążeń i danych wydajności przez znanych użytkowników i urządzenia.

Dodatkowo klienci mogą sprawdzić swoją [tożsamość](identity-secure-score.md) , aby sprawdzić, jak są one zgodne z najlepszymi rozwiązaniami firmy Microsoft. Sprawdź swój Bezpieczny wynik przed i po wdrożeniu tych zaleceń, aby dowiedzieć się, jak dobrze jesteś w porównaniu z innymi osobami w branży i z innymi organizacjami danego rozmiaru.

## <a name="prerequisites"></a>Wymagania wstępne

Wiele zaleceń zawartych w tym przewodniku można zaimplementować przy użyciu Azure AD — wersja Bezpłatna lub bez licencji. W przypadku gdy wymagane są licencje, firma Microsoft gwarantuje, że licencja jest wymagana co najmniej w celu wykonania zadania.

Dodatkowe informacje na temat licencjonowania można znaleźć na następujących stronach:

* [Zarządzanie licencjonowaniem w usłudze Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
* [Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)
* [Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)
* [Wskazówki dotyczące licencjonowania B2B usługi Azure AD](../external-identities/licensing-guidance.md)

## <a name="phase-1-build-a-foundation-of-security"></a>Faza 1: Tworzenie podstawy zabezpieczeń

W tej fazie Administratorzy mogą włączyć podstawowe funkcje zabezpieczeń, aby zapewnić bezpieczniejsze i łatwe korzystanie z programu Foundation w usłudze Azure AD przed zaimportowaniem lub utworzeniem normalnych kont użytkowników. Ta faza podstawowa gwarantuje, że jest to bardziej bezpieczny stan od początku i że użytkownicy końcowi muszą tylko wprowadzać nowe koncepcje.

| Zadanie | Szczegóły | Wymagana licencja |
| ---- | ------ | ---------------- |
| [Wyznacz więcej niż jednego administratora globalnego](../users-groups-roles/directory-emergency-access.md) | Należy przypisać co najmniej dwa stałe konta administratora globalnego tylko w chmurze do użycia w przypadku wystąpienia sytuacji awaryjnej. Te konta nie są używane codziennie i powinny mieć długie i złożone hasła. | Usługa Azure AD — warstwa Bezpłatna |
| [W miarę możliwości używaj nieglobalnych ról administracyjnych](../users-groups-roles/directory-assign-admin-roles.md) | Nadaj administratorom tylko dostęp do tych obszarów, do których potrzebują dostępu. Nie wszyscy administratorzy muszą być administratorami globalnymi. | Usługa Azure AD — warstwa Bezpłatna |
| [Włącz Privileged Identity Management do śledzenia użycia roli administratora](../privileged-identity-management/pim-getting-started.md) | Włącz Privileged Identity Management, aby rozpocząć śledzenie użycia roli administracyjnej. | Usługa Azure AD — wersja Premium P2 |
| [Wdrażanie funkcji samoobsługowego resetowania haseł](../authentication/howto-sspr-deployment.md) | Zmniejszenie liczby zgłoszeń pomocy technicznej dotyczącej resetowania haseł przez umożliwienie personelowi resetowania własnych haseł przy użyciu zasad, które kontroluje administrator. | |
| [Utwórz niestandardową listę wykluczonych haseł dla organizacji](../authentication/tutorial-configure-custom-password-protection.md) | Uniemożliwiaj użytkownikom tworzenie haseł zawierających typowe słowa lub frazy z Twojej organizacji lub obszaru. | |
| [Włączanie integracji lokalnej z ochroną hasłem usługi Azure AD](../authentication/concept-password-ban-bad-on-premises.md) | Rozwiń listę zakazanych haseł do katalogu lokalnego, aby upewnić się, że hasła ustawione lokalnie są zgodne z globalnymi i specyficznymi dla dzierżawą listami haseł. | Usługa Azure AD — wersja Premium P1 |
| [Włącz wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance/) | Zatrzymaj wymaganie od użytkowników zmiany hasła zgodnie z ustalonym harmonogramem, wyłącz wymagania dotyczące złożoności, a użytkownicy są bardziej apt, aby zapamiętać swoje hasła i zapewnić, że są one bezpieczne. | Usługa Azure AD — warstwa Bezpłatna |
| [Wyłącz resetowanie haseł okresowych dla kont użytkowników opartych na chmurze](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire) | Okresowe resetowanie haseł zachęca użytkowników do zwiększania istniejących haseł. Skorzystaj z wytycznych zawartych w dokumencie wskazówki dotyczące haseł w firmie Microsoft i podublowania zasad lokalnych w przypadku użytkowników korzystających tylko z chmury. | Usługa Azure AD — warstwa Bezpłatna |
| [Dostosowywanie inteligentnego blokowania Azure Active Directory](../authentication/howto-password-smart-lockout.md) | Zatrzymywanie blokowania od użytkowników w chmurze przed replikacją do lokalnych Active Directory użytkowników | |
| [Włącz inteligentną blokadę ekstranetu dla AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) | AD FS blokada ekstranetu chroni przed atakami polegającymi na odzgadywaniu hasła, jednocześnie umożliwiając prawidłowym AD FS użytkownikom nadal korzystanie z ich kont. | |
| [Blokuj starsze uwierzytelnianie w usłudze Azure AD przy użyciu dostępu warunkowego](../conditional-access/block-legacy-authentication.md) | Blokuj starsze protokoły uwierzytelniania, takie jak POP, SMTP, IMAP i MAPI, które nie mogą wymusić Multi-Factor Authentication, udostępniając im preferowany punkt wejścia dla źródłami ataków. | Usługa Azure AD — wersja Premium P1 |
| [Wdrażanie Multi-Factor Authentication usługi Azure AD przy użyciu zasad dostępu warunkowego](../authentication/howto-mfa-getstarted.md) | Wymaganie od użytkowników weryfikacji dwuetapowej podczas uzyskiwania dostępu do poufnych aplikacji przy użyciu zasad dostępu warunkowego. | Usługa Azure AD — wersja Premium P1 |
| [Włącz Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) | Włącz śledzenie ryzykownych logowań i zagrożonych poświadczeń dla użytkowników w organizacji. | Usługa Azure AD — wersja Premium P2 |
| [Korzystanie z wykrywania ryzyka w celu wyzwalania zmian związanych z uwierzytelnianiem wieloskładnikowym i hasłami](../authentication/tutorial-risk-based-sspr-mfa.md) | Włącz automatyzację, która może wyzwalać zdarzenia, takie jak uwierzytelnianie wieloskładnikowe, Resetowanie hasła i blokowanie logowania na podstawie ryzyka. | Usługa Azure AD — wersja Premium P2 |
| [Włącz rejestrację łączną do samoobsługowego resetowania haseł i usługi Azure AD Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) | Zezwól użytkownikom na rejestrację z jednego typowego środowiska dla usługi Azure Multi-Factor Authentication i samoobsługowego resetowania hasła. | Usługa Azure AD — wersja Premium P1 |

## <a name="phase-2-import-users-enable-synchronization-and-manage-devices"></a>Faza 2: Importowanie użytkowników, Włączanie synchronizacji i zarządzanie urządzeniami

Następnie dodamy do podstawy ustalonej w fazie 1 przez zaimportowanie naszych użytkowników i włączenie synchronizacji, zaplanowanie dostępu gościa i przygotowanie do obsługi dodatkowych funkcji.

| Zadanie | Szczegóły | Wymagana licencja |
| ---- | ------ | ---------------- |
| [Instalowanie programu Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md) | Przygotuj się do synchronizacji użytkowników z istniejącego katalogu lokalnego do chmury. | Usługa Azure AD — warstwa Bezpłatna |
| [Implementowanie synchronizacji skrótów haseł](../hybrid/how-to-connect-password-hash-synchronization.md) | Zsynchronizuj skróty haseł, aby umożliwić Replikowanie zmian haseł, wykrywanie i korygowanie nieprawidłowych haseł oraz zgłaszanie nieujawnionych poświadczeń. | Usługa Azure AD — warstwa Bezpłatna |
| [Implementuj zapisywanie zwrotne haseł](../authentication/tutorial-enable-sspr-writeback.md) | Zezwalaj na zapisywanie zmian haseł w chmurze z powrotem do lokalnego środowiska Active Directory systemu Windows Server. | Usługa Azure AD — wersja Premium P1 |
| [Implementowanie Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) | Włącz monitorowanie statystyk kondycji klucza dla serwerów Azure AD Connect, serwerów AD FS i kontrolerów domeny. | Usługa Azure AD — wersja Premium P1 |
| [Przypisywanie licencji użytkownikom według członkostwa w grupie w Azure Active Directory](../users-groups-roles/licensing-groups-assign.md) | Oszczędzaj czas i wysiłku, tworząc grupy licencjonowania, które włączają lub wyłączają funkcje według grup zamiast ustawień dla poszczególnych użytkowników. | |
| [Utwórz plan dla dostępu gościa](../external-identities/what-is-b2b.md) | Współpracuj z użytkownikami-Gośćmi, umożliwiając im logowanie do aplikacji i usług przy użyciu własnych tożsamości służbowych, szkolnych lub społecznościowych. | [Wskazówki dotyczące licencjonowania B2B usługi Azure AD](../external-identities/licensing-guidance.md) |
| [Decydowanie o strategii zarządzania urządzeniami](../devices/overview.md) | Zdecyduj, co organizacja zezwala na korzystanie z urządzeń. Zarejestrowanie programu vs sprzęgania i przełączenie urządzenia do firmy. | |
| [Wdróż usługę Windows Hello dla firm w organizacji](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) | Przygotowywanie do uwierzytelniania bezhaseł przy użyciu funkcji Windows Hello | |
| [Wdróż metody uwierzytelniania bezhasło dla użytkowników](../authentication/concept-authentication-passwordless.md) | Udostępnianie użytkownikom wygodnych metod uwierzytelniania bez hasła | Usługa Azure AD — wersja Premium P1 |

## <a name="phase-3-manage-applications"></a>Faza 3: Zarządzanie aplikacjami

W miarę dalszego kompilowania w poprzednich fazach zidentyfikujemy aplikacje kandydujące do migracji i integracji z usługą Azure AD oraz ukończą konfigurację tych aplikacji.

| Zadanie | Szczegóły | Wymagana licencja |
| ---- | ------ | ---------------- |
| Zidentyfikuj swoje aplikacje | Zidentyfikuj aplikacje używane w organizacji: lokalne, SaaS aplikacje w chmurze i inne aplikacje biznesowe. Ustal, czy te aplikacje mogą być zarządzane za pomocą usługi Azure AD. | Nie jest wymagana żadna licencja |
| [Integruj obsługiwane aplikacje SaaS w galerii](../manage-apps/add-application-portal.md) | Usługa Azure AD zawiera galerię zawierającą tysiące wstępnie zintegrowanych aplikacji. Niektóre aplikacje używane przez organizację są prawdopodobnie dostępne w galerii bezpośrednio z Azure Portal. | Usługa Azure AD — warstwa Bezpłatna |
| [Integracja aplikacji lokalnych przy użyciu serwera proxy aplikacji](../manage-apps/application-proxy-add-on-premises-application.md) | Serwer proxy aplikacji umożliwia użytkownikom dostęp do aplikacji lokalnych przez zalogowanie się przy użyciu konta usługi Azure AD. | |

## <a name="phase-4-audit-privileged-identities-complete-an-access-review-and-manage-user-lifecycle"></a>Faza 4: Inspekcja uprzywilejowanych tożsamości, Kończenie przeglądu dostępu i zarządzanie cyklem życia użytkowników

Faza 4 widzi administratorów egzekwowania najniższych zasad uprawnień do administrowania, wykonywania pierwszej oceny dostępu i włączania automatyzacji typowych zadań związanych z cyklem życia użytkownika.

| Zadanie | Szczegóły | Wymagana licencja |
| ---- | ------ | ---------------- |
| [Wymuś korzystanie z Privileged Identity Management](../privileged-identity-management/pim-security-wizard.md) | Usuń role administracyjne z kont użytkowników z normalnym dniem. Po pomyślnym sprawdzeniu uwierzytelniania wieloskładnikowego Użytkownicy administracyjni mogą korzystać z ich roli, zapewniając uzasadnienie biznesowe lub żądając zatwierdzenia od wyznaczenia osób zatwierdzających. | Usługa Azure AD — wersja Premium P2 |
| [Dokończ przegląd dostępu dla ról katalogu usługi Azure AD w usłudze PIM](../privileged-identity-management/pim-how-to-start-security-review.md) | Pracuj z zespołami ds. zabezpieczeń i liderów, aby utworzyć zasady przeglądu dostępu w celu sprawdzenia dostępu administracyjnego na podstawie zasad organizacji. | Usługa Azure AD — wersja Premium P2 |
| [Implementowanie zasad członkostwa w grupie dynamicznej](../users-groups-roles/groups-dynamic-membership.md) | Użyj grup dynamicznych do automatycznego przypisywania użytkowników do grup na podstawie ich atrybutów z HR (lub źródła prawdziwie), takich jak dział, tytuł, region i inne atrybuty. |  |
| [Implementowanie aprowizacji aplikacji na podstawie grupy](../manage-apps/what-is-access-management.md) | Inicjowanie obsługi zarządzania dostępem oparte na grupach umożliwia automatyczne Inicjowanie obsługi użytkowników w aplikacjach SaaS. |  |
| [Automatyzowanie aprowizacji użytkowników i cofanie obsługi administracyjnej](../app-provisioning/user-provisioning.md) | Usuń kroki ręcznego z cyklu życia konta pracownika, aby zapobiec nieautoryzowanemu dostępowi. Zsynchronizuj tożsamości ze źródła prawdziwie (system HR) z usługą Azure AD. |  |

## <a name="next-steps"></a>Następne kroki

[Informacje o licencjonowaniu i cenniku usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Konfiguracje tożsamości i dostępu do urządzeń](/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Wspólne zalecane zasady dostępu do tożsamości i urządzeń](/microsoft-365/enterprise/identity-access-policies)