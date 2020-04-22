---
title: Delegowanie ról według zadań administratora — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: Role delegowania zadań tożsamości w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/03/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd24650c9bf0c4de155b5bfc8723cfa1fef01548
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755418"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Role administratora według zadań administratora w usłudze Azure Active Directory

W tym artykule można znaleźć informacje potrzebne do ograniczenia uprawnień administratora użytkownika, przypisując najmniej uprzywilejowanych ról w usłudze Azure Active Directory (Azure AD). Znajdziesz zadania administratora uporządkowane według obszaru funkcji i najmniej uprzywilejowaną rolę wymaganą do wykonania każdego zadania, wraz z dodatkowymi rolami administratora nieglobalnego, które mogą wykonywać zadanie.

## <a name="application-proxy"></a>Serwer proxy aplikacji

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie aplikacji proxy aplikacji | Administrator aplikacji | 
Konfigurowanie właściwości grupy łączników | Administrator aplikacji | 
Tworzenie rejestracji aplikacji, gdy możliwość jest wyłączona dla wszystkich użytkowników | Deweloper aplikacji | Administrator aplikacji w chmurze, administrator aplikacji
Tworzenie grupy łączników | Administrator aplikacji | 
Usuń grupę łączników | Administrator aplikacji | 
Wyłączanie serwera proxy aplikacji | Administrator aplikacji | 
Pobierz usługę łącznika | Administrator aplikacji | 
Przeczytaj całą konfigurację | Administrator aplikacji | 

## <a name="b2c"></a>B2C

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Tworzenie katalogów usługi Azure AD B2C | Wszyscy użytkownicy niebędący gośćmi[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Tworzenie aplikacji B2C | Administrator globalny | 
Tworzenie aplikacji dla przedsiębiorstw | Administrator aplikacji w chmurze | Administrator aplikacji
Tworzenie, odczytywanie, aktualizowanie i usuwanie zasad B2C | Administrator zasad IEF B2C | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie dostawców tożsamości | Administrator zewnętrznego dostawcy tożsamości | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie przepływów użytkowników resetowania hasła | Administrator przepływu użytkownika B2C | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie przepływów użytkowników edycji profilu | Administrator przepływu użytkownika B2C | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie przepływów użytkowników logowania | Administrator przepływu użytkownika B2C | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie przepływu użytkownika rejestracji |Administrator przepływu użytkownika B2C | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie atrybutów użytkownika | Administrator atrybutów przepływu użytkownika B2C | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie użytkowników | Administrator użytkowników
Przeczytaj całą konfigurację | Czytnik globalny | 
Odczytywanie dzienników inspekcji B2C | Czytnik globalny ([patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs) | 

> [!NOTE]
> Czytniki globalne usługi Azure AD B2C nie mają tych samych uprawnień co administratorzy globalni usługi Azure AD. Jeśli masz uprawnienia administratora globalnego usługi Azure AD B2C, upewnij się, że znajdujesz się w katalogu usługi Azure AD B2C, a nie w katalogu usługi Azure AD.

## <a name="company-branding"></a>Znakowanie firmowe

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie oznaczenia marką firmy | Administrator globalny | 
Przeczytaj całą konfigurację | Czytniki katalogów | Domyślna rola użytkownika[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)

## <a name="company-properties"></a>Właściwości firmy

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie właściwości firmy | Administrator globalny | 

## <a name="connect"></a>Połącz

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Uwierzytelnianie przekazywania | Administrator tożsamości hybrydowej | 
Przeczytaj całą konfigurację | Czytnik globalny | Administrator tożsamości hybrydowej |
Bezproblemowe logowanie jednokrotne | Administrator tożsamości hybrydowej | 

## <a name="connect-health"></a>Connect Health

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Dodawanie lub usuwanie usług | Właściciel[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations) | 
Stosowanie poprawek w celu zsynchronizowania błędu | Współautor[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Właściciel
Konfigurowanie powiadomień | Współautor[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Właściciel
Konfigurowanie ustawień | Właściciel[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations) | 
Konfigurowanie powiadomień o synchronizacji | Współautor[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Właściciel
Czytanie raportów zabezpieczeń usługi ADFS | Czytelnik zabezpieczeń | Współautor, Właściciel
Przeczytaj całą konfigurację | Czytnik[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Współautor, Właściciel
Odczyt błędów synchronizacji | Czytnik[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Współautor, Właściciel
Odczyt usług synchronizacji | Czytnik[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Współautor, Właściciel
Wyświetlanie danych i alertów | Czytnik[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Współautor, Właściciel
Wyświetlanie danych i alertów | Czytnik[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Współautor, Właściciel
Wyświetlanie danych i alertów usługi synchronizacji | Czytnik[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Współautor, Właściciel

## <a name="custom-domain-names"></a>Niestandardowe nazwy domen

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Zarządzanie domenami | Administrator globalny | 
Przeczytaj całą konfigurację | Czytniki katalogów | Domyślna rola użytkownika[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)

## <a name="domain-services"></a>Domain Services

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Tworzenie wystąpienia usług domenowych usługi Azure AD | Administrator globalny | 
Wykonywanie wszystkich zadań usług domenowych usługi Azure AD | Grupa Administratorzy kontrolera domeny usługi Azure AD ([zobacz dokumentację)](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain) | 
Przeczytaj całą konfigurację | Subskrypcja programu Reader na platformie Azure zawierająca usługę USŁUG AD DS | 

## <a name="devices"></a>Urządzenia

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Wyłącz urządzenie | Administrator urządzeń w chmurze | 
Włącz urządzenie | Administrator urządzeń w chmurze | 
Odczyt konfiguracji podstawowej | Domyślna rola użytkownika[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Odczytanie klawiszy funkcji BitLocker | Czytelnik zabezpieczeń | Administrator haseł, Administrator zabezpieczeń

## <a name="enterprise-applications"></a>Aplikacje dla przedsiębiorstw

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Zgoda na wszelkie delegowane uprawnienia | Administrator aplikacji w chmurze | Administrator aplikacji
Zgoda na uprawnienia aplikacji bez uwzględnienia programu Microsoft Graph | Administrator aplikacji w chmurze | Administrator aplikacji
Zgoda na uprawnienia aplikacji do programu Microsoft Graph | Administrator ról uprzywilejowanych | 
Zgoda na aplikacje uzyskujące dostęp do własnych danych | Domyślna rola użytkownika[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Tworzenie aplikacji przedsiębiorstwa | Administrator aplikacji w chmurze | Administrator aplikacji
Zarządzanie serwerem proxy aplikacji | Administrator aplikacji | 
Zarządzanie ustawieniami użytkownika | Administrator globalny | 
Przegląd dostępu do odczytu grupy lub aplikacji | Czytelnik zabezpieczeń | Administrator zabezpieczeń, administrator użytkownika
Przeczytaj całą konfigurację | Domyślna rola użytkownika[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Aktualizowanie przydziałów aplikacji przedsiębiorstwa | Właściciel aplikacji przedsiębiorstwa ([patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizowanie właścicieli aplikacji przedsiębiorstwa | Właściciel aplikacji przedsiębiorstwa ([patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizowanie właściwości aplikacji przedsiębiorstwa | Właściciel aplikacji przedsiębiorstwa ([patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizowanie aprowizacji aplikacji przedsiębiorstwa | Właściciel aplikacji przedsiębiorstwa ([patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizowanie samoobsługi aplikacji przedsiębiorstwa | Właściciel aplikacji przedsiębiorstwa ([patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizowanie właściwości logowania jednokrotnego | Właściciel aplikacji przedsiębiorstwa ([patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrator aplikacji w chmurze, administrator aplikacji

## <a name="entitlement-management"></a>Zarządzanie upoważnieniami
Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Dodawanie zasobów do katalogu | Administrator użytkownika | Za pomocą zarządzania uprawnieniami można delegować to zadanie do właściciela katalogu[(patrz dokumentacja)](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners)
Dodawanie witryn usługi SharePoint Online do katalogu | Administrator globalny


## <a name="groups"></a>Grupy

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Przypisywanie licencji | Administrator użytkownika | 
Tworzenie grupy: | Administrator użytkownika | 
Tworzenie, aktualizowanie lub usuwanie przeglądu dostępu grupy lub aplikacji | Administrator użytkownika | 
Zarządzanie wygasaniem grupy | Administrator użytkownika | 
Zarządzenie ustawieniami grup | Administrator grup | Administrator użytkowników | 
Przeczytaj całą konfigurację (z wyjątkiem ukrytego członkostwa) | Czytniki katalogów | Domyślna rola użytkownika[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)
Przeczytaj ukryte członkostwo | Członek grupy | Właściciel grupy, administrator haseł, administrator programu Exchange, administrator programu SharePoint, administrator zespołów, administrator użytkownika
Przeczytaj członkostwo w grupach z ukrytym członkostwem | Administrator działu pomocy technicznej | Administrator użytkownika, administrator zespołów
Cofnięcie licencji | Administrator licencji | Administrator użytkownika
Aktualizowanie członkostwa w grupie | Właściciel grupy ([patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrator użytkownika
Aktualizowanie właścicieli grup | Właściciel grupy ([patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrator użytkownika
Aktualizowanie właściwości grupy | Właściciel grupy ([patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Administrator użytkownika

## <a name="identity-protection"></a>Identity Protection

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie powiadomień o alertach| Administrator zabezpieczeń | 
Konfigurowanie i włączanie i wyłączanie zasad usługi MFA| Administrator zabezpieczeń | 
Konfigurowanie i włączanie i wyłączanie zasad ryzyka logowania| Administrator zabezpieczeń | 
Konfigurowanie i włączanie i wyłączanie zasad ryzyka użytkownika | Administrator zabezpieczeń | 
Konfigurowanie cotygodniowych skrótów | Administrator zabezpieczeń| 
Odrzucanie wszystkich przypadków wykrywania ryzyka | Administrator zabezpieczeń | 
Naprawianie lub odrzucanie luki w zabezpieczeniach | Administrator zabezpieczeń | 
Przeczytaj całą konfigurację | Czytelnik zabezpieczeń | 
Przeczytaj wszystkie wykrywanie ryzyka | Czytelnik zabezpieczeń | 
Odczytywanie luk w zabezpieczeniach | Czytelnik zabezpieczeń | 

## <a name="licenses"></a>Licencje

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Przypisywanie licencji | Administrator licencji | Administrator użytkownika
Przeczytaj całą konfigurację | Czytniki katalogów | Domyślna rola użytkownika[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)
Cofnięcie licencji | Administrator licencji | Administrator użytkownika
Wypróbuj lub kup subskrypcję | Administrator rozliczeń | 


## <a name="monitoring---audit-logs"></a>Monitorowanie — dzienniki inspekcji

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Odczytywanie dzienników inspekcji | Czytnik raportów | Czytnik zabezpieczeń, administrator zabezpieczeń

## <a name="monitoring---sign-ins"></a>Monitorowanie — logowania

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Odczytywanie dzienników logowania | Czytnik raportów | Czytnik zabezpieczeń, administrator zabezpieczeń

## <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Usuwanie wszystkich istniejących haseł aplikacji generowanych przez wybranych użytkowników | Administrator globalny | 
Wyłączanie usługi MFA | Administrator globalny | 
Włączanie usługi MFA | Administrator globalny | 
Zarządzanie ustawieniami usługi usługi usługi MFA | Administrator globalny | 
Wymaganie od wybranych użytkowników ponownego podania metod kontaktu | Administrator uwierzytelniania | 
Przywracanie uwierzytelniania wieloskładnikowego na wszystkich zapamiętanych urządzeniach  | Administrator uwierzytelniania | 

## <a name="mfa-server"></a>Serwer MFA

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Blokowanie/odblokowywanie użytkowników | Administrator globalny | 
Konfigurowanie blokady konta | Administrator globalny | 
Konfigurowanie reguł buforowania | Administrator globalny | 
Konfigurowanie alertu o oszustwach | Administrator globalny
Konfigurowanie powiadomień | Administrator globalny | 
Konfigurowanie jednorazowego obejścia | Administrator globalny | 
Konfigurowanie ustawień połączenia telefonicznego | Administrator globalny | 
Konfigurowanie dostawców | Administrator globalny | 
Konfigurowanie ustawień serwera | Administrator globalny | 
Czytanie raportu z aktywności | Czytnik globalny | 
Przeczytaj całą konfigurację | Czytnik globalny | 
Odczyt stanu serwera | Czytnik globalny |  

## <a name="organizational-relationships"></a>Relacje w organizacji

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Zarządzanie dostawcami tożsamości | Administrator zewnętrznego dostawcy tożsamości | 
Zarządzanie ustawieniami | Administrator globalny | 
Zarządzanie warunkami użytkowania | Administrator globalny | 
Przeczytaj całą konfigurację | Czytnik globalny | 

## <a name="password-reset"></a>Resetowanie hasła

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie metod uwierzytelniania | Administrator globalny |
Konfigurowanie dostosowywania | Administrator globalny |
Konfigurowanie powiadomień | Administrator globalny |
Konfigurowanie integracji lokalnej | Administrator globalny |
Konfigurowanie właściwości resetowania hasła | Administrator użytkowników | Administrator globalny
Konfigurowanie rejestracji | Administrator globalny |
Przeczytaj całą konfigurację | Administrator zabezpieczeń | Administrator użytkowników |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Przypisywanie użytkowników do ról | Administrator ról uprzywilejowanych | 
Konfigurowanie ustawień roli | Administrator ról uprzywilejowanych | 
Wyświetlanie działania inspekcji | Czytelnik zabezpieczeń | 
Wyświetlanie członkostw w rolach | Czytelnik zabezpieczeń | 

## <a name="roles-and-administrators"></a>Role i administratorzy

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Zarządzanie przypisaniami ról | Administrator ról uprzywilejowanych | 
Przegląd dostępu do odczytu roli usługi Azure AD  | Czytelnik zabezpieczeń | Administrator zabezpieczeń, administrator ról uprzywilejowanych
Przeczytaj całą konfigurację | Domyślna rola użytkownika[(patrz dokumentacja)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 

## <a name="security---authentication-methods"></a>Bezpieczeństwo — metody uwierzytelniania

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie metod uwierzytelniania | Administrator globalny | 
Przeczytaj całą konfigurację | Czytnik globalny | 

## <a name="security---conditional-access"></a>Zabezpieczenia — dostęp warunkowy

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie zaufanych adresów IP usługi MFA | Administrator dostępu warunkowego | 
Tworzenie formantów niestandardowych | Administrator dostępu warunkowego | Administrator zabezpieczeń
Tworzenie nazwanych lokalizacji | Administrator dostępu warunkowego | Administrator zabezpieczeń
Tworzenie zasad | Administrator dostępu warunkowego | Administrator zabezpieczeń
Tworzenie warunków użytkowania | Administrator dostępu warunkowego | Administrator zabezpieczeń
Tworzenie certyfikatu łączności sieci VPN | Administrator dostępu warunkowego | Administrator zabezpieczeń
Usuwanie zasad klasycznych | Administrator dostępu warunkowego | Administrator zabezpieczeń
Usuwanie warunków użytkowania | Administrator dostępu warunkowego | Administrator zabezpieczeń
Usuwanie certyfikatu łączności sieci VPN | Administrator dostępu warunkowego | Administrator zabezpieczeń
Wyłączanie zasad klasycznych | Administrator dostępu warunkowego | Administrator zabezpieczeń
Zarządzanie formantami niestandardowymi | Administrator dostępu warunkowego | Administrator zabezpieczeń
Zarządzanie nazwanymi lokalizacjami | Administrator dostępu warunkowego | Administrator zabezpieczeń
Zarządzanie warunkami użytkowania | Administrator dostępu warunkowego | Administrator zabezpieczeń
Przeczytaj całą konfigurację | Czytelnik zabezpieczeń | Administrator zabezpieczeń
Odczytywanie nazwanych lokalizacji | Czytelnik zabezpieczeń | Administrator dostępu warunkowego, administrator zabezpieczeń

## <a name="security---identity-security-score"></a>Zabezpieczenia — ocena zabezpieczeń tożsamości

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role | 
---- | --------------------- | ----------------
Przeczytaj całą konfigurację | Czytelnik zabezpieczeń | Administrator zabezpieczeń
Odczytanie wyniku zabezpieczeń | Czytelnik zabezpieczeń | Administrator zabezpieczeń
Aktualizowanie stanu zdarzenia | Administrator zabezpieczeń | 

## <a name="security---risky-sign-ins"></a>Bezpieczeństwo — ryzykowne logowania

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Przeczytaj całą konfigurację | Czytelnik zabezpieczeń | 
Czytaj ryzykowne logowania | Czytelnik zabezpieczeń | 

## <a name="security---users-flagged-for-risk"></a>Bezpieczeństwo — użytkownicy oflagowani ze względów ryzyka

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Odrzucanie wszystkich zdarzeń. | Administrator zabezpieczeń | 
Przeczytaj całą konfigurację | Czytelnik zabezpieczeń | 
Odczytanie użytkowników oznaczonych pod kątem ryzyka | Czytelnik zabezpieczeń | 

## <a name="users"></a>Użytkownicy

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Dodawanie roli użytkownika do katalogu | Administrator ról uprzywilejowanych | 
Dodawanie użytkownika do grupy | Administrator użytkownika | 
Przypisywanie licencji | Administrator licencji | Administrator użytkownika
Tworzenie użytkownika-gościa | Zaproszony gość | Administrator użytkownika
Tworzenie użytkownika | Administrator użytkownika | 
Usuwanie użytkowników | Administrator użytkownika | 
Unieważnianie tokenów odświeżania ograniczonych administratorów (zobacz dokumentację) | Administrator użytkownika | 
Unieważnianie tokenów odświeżania niepodaczy (zobacz dokumentację) | Administrator haseł | Administrator użytkownika
Unieważnianie tokenów odświeżania uprzywilejowanych administratorów (zobacz dokumentację) | Administrator uwierzytelniania uprzywilejowanego | 
Odczyt konfiguracji podstawowej | Domyślna rola użytkownika[(zobacz dokumentację](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Resetowanie hasła dla ograniczonych administratorów (zobacz dokumentację) | Administrator użytkownika | 
Resetowanie hasła osób niebędących administratorami (patrz dokumentacja) | Administrator haseł | Administrator użytkownika
Resetowanie hasła uprzywilejowanych administratorów | Administrator uwierzytelniania uprzywilejowanego | 
Cofnięcie licencji | Administrator licencji | Administrator użytkownika
Aktualizuj wszystkie właściwości z wyjątkiem nazwy głównej użytkownika | Administrator użytkownika | 
Aktualizowanie nazwy głównej użytkownika dla ograniczonych administratorów (zobacz dokumentację) | Administrator użytkownika | 
Aktualizacja nazwy głównej użytkownika właściwości na uprzywilejowanych administratorów (zobacz dokumentację) | Administrator globalny | 
Aktualizowanie ustawień użytkownika | Administrator globalny | 


## <a name="support"></a>Pomoc techniczna

Zadanie | Najmniej uprzywilejowana rola | Dodatkowe role
---- | --------------------- | ----------------
Prześlij zgłoszenie pomocy technicznej | Administrator usługi | Administrator aplikacji, administrator usługi Azure Information Protection, administrator rozliczeń, administrator aplikacji w chmurze, administrator zgodności, administrator systemu Dynamics 365, administrator analizy pulpitu, administrator programu Exchange, administrator haseł, administrator usługi Intune, administrator programu Skype dla firm, administrator usługi Power BI, administrator uwierzytelniania z uprawnieniami, administrator programu SharePoint, administrator komunikacji zespołów, administrator zespołów, administrator użytkownika, administrator analizy miejsca pracy

## <a name="next-steps"></a>Następne kroki

* [Jak przypisać lub usunąć role administratora usługi Azure AD](directory-manage-roles-portal.md)
* [Odwołanie do ról administratora usługi Azure AD](directory-assign-admin-roles.md)
