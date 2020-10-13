---
title: Delegowanie ról według zadania administratora — Azure Active Directory | Microsoft Docs
description: Role delegowania zadań związanych z tożsamościami w Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 05/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 692b20bf301bbd7bf03ea1af5527e08d947b50da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88798367"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Role administratorów według zadania administratora w Azure Active Directory

W tym artykule można znaleźć informacje potrzebne do ograniczenia uprawnień administratora użytkownika przez przypisanie najmniej uprzywilejowanych ról w Azure Active Directory (Azure AD). Zadania administratora są organizowane przez obszar funkcji i najmniej uprzywilejowaną rolę wymaganą do wykonania każdego zadania, a także dodatkowe role administratora nieglobalne, które mogą wykonać zadanie.

## <a name="application-proxy"></a>Serwer proxy aplikacji

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie aplikacji serwera proxy aplikacji | Administrator aplikacji | 
Konfigurowanie właściwości grupy łączników | Administrator aplikacji | 
Utwórz rejestrację aplikacji, gdy możliwość jest wyłączona dla wszystkich użytkowników | Deweloper aplikacji | Administrator aplikacji w chmurze, administrator aplikacji
Utwórz grupę łączników | Administrator aplikacji | 
Usuń grupę łączników | Administrator aplikacji | 
Wyłączanie serwera proxy aplikacji | Administrator aplikacji | 
Pobierz usługę łącznika | Administrator aplikacji | 
Odczytaj całą konfigurację | Administrator aplikacji | 

## <a name="external-identitiesb2c"></a>Tożsamości zewnętrzne/B2C

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Tworzenie katalogów Azure AD B2C | Wszyscy użytkownicy niebędący gośćmi ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | 
Tworzenie aplikacji B2C | Administrator globalny | 
Tworzenie aplikacji dla przedsiębiorstw | Administrator aplikacji w chmurze | Administrator aplikacji
Tworzenie, odczytywanie, aktualizowanie i usuwanie zasad B2C | Administrator zasad B2C IEF | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie dostawców tożsamości | Administrator zewnętrznego dostawcy tożsamości | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie przepływów użytkownika resetowania haseł | Administrator przepływu użytkownika z IDENTYFIKATORem zewnętrznym | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie profilu edytowanie przepływów użytkowników | Administrator przepływu użytkownika z IDENTYFIKATORem zewnętrznym | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie przepływów użytkowników logowania | Administrator przepływu użytkownika z IDENTYFIKATORem zewnętrznym | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie przepływu użytkownika przy rejestrowaniu |Administrator przepływu użytkownika z IDENTYFIKATORem zewnętrznym | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie atrybutów użytkownika | Administrator atrybutów przepływu użytkownika zewnętrznego ID | 
Tworzenie, odczytywanie, aktualizowanie i usuwanie użytkowników | Administrator użytkowników
Odczytaj całą konfigurację | Czytnik globalny | 
Odczytaj dzienniki inspekcji B2C | Czytnik globalny ([Zobacz dokumentację](../../active-directory-b2c/faq.md)) | 

> [!NOTE]
> Azure AD B2C czytelnicy globalni nie mają tych samych uprawnień co Administratorzy globalni usługi Azure AD. Jeśli masz Azure AD B2C uprawnienia administratora globalnego, upewnij się, że jesteś w katalogu Azure AD B2C, a nie w katalogu usługi Azure AD.

## <a name="company-branding"></a>Znakowanie firmowe

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie oznaczenia marką firmy | Administrator globalny | 
Odczytaj całą konfigurację | Czytelnicy katalogów | Domyślna rola użytkownika ([Zobacz dokumentację](../fundamentals/users-default-permissions.md))

## <a name="company-properties"></a>Właściwości firmy

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie właściwości firmy | Administrator globalny | 

## <a name="connect"></a>Connect

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Przekazywanie uwierzytelniania | Administrator globalny  | 
Odczytaj całą konfigurację | Czytnik globalny | Administrator globalny  |
Bezproblemowe logowanie jednokrotne | Administrator globalny  | 

## <a name="cloud-provisioning"></a>Inicjowanie obsługi chmury

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Przekazywanie uwierzytelniania | Administrator tożsamości hybrydowej  | 
Odczytaj całą konfigurację | Czytnik globalny | Administrator tożsamości hybrydowej  |
Bezproblemowe logowanie jednokrotne | Administrator tożsamości hybrydowej  | 

## <a name="connect-health"></a>Connect Health

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Dodawanie lub usuwanie usług | Owner ([Zobacz dokumentację](../hybrid/how-to-connect-health-operations.md)) | 
Zastosuj poprawki do błędu synchronizacji | Współautor ([Zobacz dokumentację](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Właściciel
Konfigurowanie powiadomień | Współautor ([Zobacz dokumentację](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Właściciel
Konfigurowanie ustawień | Owner ([Zobacz dokumentację](../hybrid/how-to-connect-health-operations.md)) | 
Konfigurowanie powiadomień dotyczących synchronizacji | Współautor ([Zobacz dokumentację](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Właściciel
Przeczytaj raporty zabezpieczeń usług AD FS | Czytelnik zabezpieczeń | Współautor, właściciel
Odczytaj całą konfigurację | Reader ([Zobacz dokumentację](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Współautor, właściciel
Błędy synchronizacji odczytu | Reader ([Zobacz dokumentację](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Współautor, właściciel
Odczytaj usługi synchronizacji | Reader ([Zobacz dokumentację](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Współautor, właściciel
Wyświetlanie metryk i alertów | Reader ([Zobacz dokumentację](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Współautor, właściciel
Wyświetlanie metryk i alertów | Reader ([Zobacz dokumentację](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Współautor, właściciel
Wyświetl metryki i alerty usługi synchronizacji | Reader ([Zobacz dokumentację](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Współautor, właściciel

## <a name="custom-domain-names"></a>Niestandardowe nazwy domen

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Zarządzanie domenami | Administrator globalny | 
Odczytaj całą konfigurację | Czytelnicy katalogów | Domyślna rola użytkownika ([Zobacz dokumentację](../fundamentals/users-default-permissions.md))

## <a name="domain-services"></a>Domain Services

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Utwórz wystąpienie Azure AD Domain Services | Administrator globalny | 
Wykonaj wszystkie zadania Azure AD Domain Services | Grupa administratorów kontrolera domeny usługi Azure AD ([Zobacz dokumentację](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Odczytaj całą konfigurację | Czytelnik w ramach subskrypcji platformy Azure zawierającej usługę AD DS | 

## <a name="devices"></a>Devices

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Wyłącz urządzenie | Administrator urządzenia w chmurze | 
Włącz urządzenie | Administrator urządzenia w chmurze | 
Odczytaj konfigurację podstawową | Domyślna rola użytkownika ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | 
Odczytaj klucze funkcji BitLocker | Czytelnik zabezpieczeń | Administrator haseł, administrator zabezpieczeń

## <a name="enterprise-applications"></a>Aplikacje dla przedsiębiorstw

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Wyrażanie zgody na uprawnienia delegowane | Administrator aplikacji w chmurze | Administrator aplikacji
Wyrażanie zgody na uprawnienia aplikacji, w tym Microsoft Graph | Administrator aplikacji w chmurze | Administrator aplikacji
Wyrażanie zgody na uprawnienia aplikacji do Microsoft Graph | Administrator ról uprzywilejowanych | 
Wyrażanie zgody na aplikacje uzyskujące dostęp do danych | Domyślna rola użytkownika ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | 
Tworzenie aplikacji dla przedsiębiorstw | Administrator aplikacji w chmurze | Administrator aplikacji
Zarządzanie serwerem proxy aplikacji | Administrator aplikacji | 
Zarządzanie ustawieniami użytkownika | Administrator globalny | 
Przegląd dostępu do odczytu grupy lub aplikacji | Czytelnik zabezpieczeń | Administrator zabezpieczeń, administrator użytkowników
Odczytaj całą konfigurację | Domyślna rola użytkownika ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | 
Aktualizowanie przypisań aplikacji dla przedsiębiorstw | Właściciel aplikacji przedsiębiorstwa ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizowanie właścicieli aplikacji dla przedsiębiorstw | Właściciel aplikacji przedsiębiorstwa ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizowanie właściwości aplikacji dla przedsiębiorstw | Właściciel aplikacji przedsiębiorstwa ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizowanie aprowizacji aplikacji dla przedsiębiorstw | Właściciel aplikacji przedsiębiorstwa ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizacja samoobsługi aplikacji dla przedsiębiorstw | Właściciel aplikacji przedsiębiorstwa ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | Administrator aplikacji w chmurze, administrator aplikacji
Aktualizowanie właściwości logowania jednokrotnego | Właściciel aplikacji przedsiębiorstwa ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | Administrator aplikacji w chmurze, administrator aplikacji

## <a name="entitlement-management"></a>Zarządzanie upoważnieniami
Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Dodawanie zasobów do wykazu | Administrator użytkowników | Za pomocą zarządzania uprawnieniami można delegować to zadanie do właściciela katalogu ([Zobacz dokumentację](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
Dodawanie witryn usługi SharePoint Online do wykazu | Administrator globalny


## <a name="groups"></a>Grupy

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Przypisywanie licencji | Administrator użytkowników | 
Tworzenie grupy: | Administrator grup | Administrator użytkowników
Tworzenie, aktualizowanie i usuwanie przeglądu dostępu do grupy lub aplikacji | Administrator użytkowników | 
Zarządzanie wygaśnięciem grupy | Administrator użytkowników | 
Zarządzenie ustawieniami grup | Administrator grup | Administrator użytkowników | 
Odczytaj wszystkie konfiguracje (z wyjątkiem ukrytych członkostw) | Czytelnicy katalogów | Domyślna rola użytkownika ([Zobacz dokumentację](../fundamentals/users-default-permissions.md))
Odczytaj ukryte członkostwo | Członek grupy | Właściciel grupy, administrator haseł, administrator serwera Exchange, administrator programu SharePoint, administrator zespołów, administrator użytkowników
Odczytuj członkostwo grup z ukrytym członkostwem | Administrator pomocy technicznej | Administrator użytkowników, Administratorzy zespołów
Odwołaj licencję | Administrator licencji | Administrator użytkowników
Aktualizowanie członkostwa w grupie | Właściciel grupy ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | Administrator użytkowników
Aktualizowanie właścicieli grup | Właściciel grupy ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | Administrator użytkowników
Aktualizuj właściwości grupy | Właściciel grupy ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | Administrator użytkowników
Usuwanie grupy | Administrator grup | Administrator użytkowników

## <a name="identity-protection"></a>Identity Protection

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie powiadomień o alertach| Administrator zabezpieczeń | 
Konfigurowanie i włączanie lub wyłączanie zasad usługi MFA| Administrator zabezpieczeń | 
Skonfiguruj i Włącz lub wyłącz zasady dotyczące ryzyka związanego z logowaniem| Administrator zabezpieczeń | 
Konfigurowanie i włączanie lub wyłączanie zasad ryzyka użytkownika | Administrator zabezpieczeń | 
Skonfiguruj cotygodniowe skróty | Administrator zabezpieczeń| 
Odrzuć wszystkie wykrycia ryzyka | Administrator zabezpieczeń | 
Usuwanie lub odrzucanie luk w zabezpieczeniach | Administrator zabezpieczeń | 
Odczytaj całą konfigurację | Czytelnik zabezpieczeń | 
Odczytuj wszystkie wykrycia ryzyka | Czytelnik zabezpieczeń | 
Przeczytaj luki | Czytelnik zabezpieczeń | 

## <a name="licenses"></a>Licencje

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Przypisywanie licencji | Administrator licencji | Administrator użytkowników
Odczytaj całą konfigurację | Czytelnicy katalogów | Domyślna rola użytkownika ([Zobacz dokumentację](../fundamentals/users-default-permissions.md))
Odwołaj licencję | Administrator licencji | Administrator użytkowników
Wypróbuj lub Kup subskrypcję | Administrator rozliczeń | 


## <a name="monitoring---audit-logs"></a>Monitorowanie — dzienniki inspekcji

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Odczyt dzienników inspekcji | Czytelnik raportów | Czytelnik zabezpieczeń, administrator zabezpieczeń

## <a name="monitoring---sign-ins"></a>Monitorowanie — logowanie

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Odczytuj dzienniki logowania | Czytelnik raportów | Czytelnik zabezpieczeń, administrator zabezpieczeń

## <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Usuń wszystkie istniejące hasła aplikacji wygenerowane przez wybranych użytkowników | Administrator globalny | 
Wyłącz usługę MFA | Administrator globalny | 
Włączanie usługi MFA | Administrator globalny | 
Zarządzanie ustawieniami usługi MFA | Administrator globalny | 
Wymagaj od wybranych użytkowników ponownego podania metod kontaktu | Administrator uwierzytelniania | 
Przywracanie uwierzytelniania wieloskładnikowego na wszystkich zapamiętanych urządzeniach  | Administrator uwierzytelniania | 

## <a name="mfa-server"></a>Serwer MFA

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Blokuj/Odblokuj użytkowników | Administrator globalny | 
Konfigurowanie blokady konta | Administrator globalny | 
Konfigurowanie reguł buforowania | Administrator globalny | 
Konfigurowanie alertu oszustwa | Administrator globalny
Konfigurowanie powiadomień | Administrator globalny | 
Skonfiguruj jednorazowe obejście | Administrator globalny | 
Konfigurowanie ustawień połączenia telefonicznego | Administrator globalny | 
Konfiguruj dostawców | Administrator globalny | 
Konfigurowanie ustawień serwera | Administrator globalny | 
Odczytaj raport działania | Czytnik globalny | 
Odczytaj całą konfigurację | Czytnik globalny | 
Odczytaj stan serwera | Czytnik globalny |  

## <a name="organizational-relationships"></a>Relacje w organizacji

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Zarządzaj dostawcami tożsamości | Administrator zewnętrznego dostawcy tożsamości | 
Zarządzanie ustawieniami | Administrator globalny | 
Zarządzanie warunkami użytkowania | Administrator globalny | 
Odczytaj całą konfigurację | Czytnik globalny | 

## <a name="password-reset"></a>Resetowanie hasła

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie metod uwierzytelniania | Administrator globalny |
Konfigurowanie dostosowywania | Administrator globalny |
Konfiguruj powiadomienie | Administrator globalny |
Konfigurowanie integracji lokalnej | Administrator globalny |
Konfigurowanie właściwości resetowania hasła | Administrator użytkowników | Administrator globalny
Konfigurowanie rejestracji | Administrator globalny |
Odczytaj całą konfigurację | Administrator zabezpieczeń | Administrator użytkowników |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Przypisywanie użytkowników do ról | Administrator ról uprzywilejowanych | 
Konfigurowanie ustawień roli | Administrator ról uprzywilejowanych | 
Wyświetl działanie inspekcji | Czytelnik zabezpieczeń | 
Wyświetlanie członkostw ról | Czytelnik zabezpieczeń | 

## <a name="roles-and-administrators"></a>Role i administratorzy

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Zarządzanie przypisaniami ról | Administrator ról uprzywilejowanych | 
Przegląd dostępu do odczytu roli usługi Azure AD  | Czytelnik zabezpieczeń | Administrator zabezpieczeń, administrator ról uprzywilejowanych
Odczytaj całą konfigurację | Domyślna rola użytkownika ([Zobacz dokumentację](../fundamentals/users-default-permissions.md)) | 

## <a name="security---authentication-methods"></a>Zabezpieczenia — metody uwierzytelniania

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie metod uwierzytelniania | Administrator globalny | 
Konfigurowanie ochrony hasłem | Administrator zabezpieczeń
Konfigurowanie blokady inteligentnej | Administrator zabezpieczeń
Odczytaj całą konfigurację | Czytnik globalny | 

## <a name="security---conditional-access"></a>Zabezpieczenia — dostęp warunkowy

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Konfigurowanie zaufanych adresów IP usługi MFA | Administrator dostępu warunkowego | 
Tworzenie niestandardowych kontrolek | Administrator dostępu warunkowego | Administrator zabezpieczeń
Utwórz nazwane lokalizacje | Administrator dostępu warunkowego | Administrator zabezpieczeń
Tworzenie zasad | Administrator dostępu warunkowego | Administrator zabezpieczeń
Tworzenie warunków użytkowania | Administrator dostępu warunkowego | Administrator zabezpieczeń
Utwórz certyfikat łączności sieci VPN | Administrator dostępu warunkowego | Administrator zabezpieczeń
Usuń zasady klasyczne | Administrator dostępu warunkowego | Administrator zabezpieczeń
Usuń warunki użytkowania | Administrator dostępu warunkowego | Administrator zabezpieczeń
Usuń certyfikat łączności sieci VPN | Administrator dostępu warunkowego | Administrator zabezpieczeń
Wyłącz zasady klasyczne | Administrator dostępu warunkowego | Administrator zabezpieczeń
Zarządzanie kontrolkami niestandardowymi | Administrator dostępu warunkowego | Administrator zabezpieczeń
Zarządzaj nazwanymi lokalizacjami | Administrator dostępu warunkowego | Administrator zabezpieczeń
Zarządzanie warunkami użytkowania | Administrator dostępu warunkowego | Administrator zabezpieczeń
Odczytaj całą konfigurację | Czytelnik zabezpieczeń | Administrator zabezpieczeń
Odczytuj nazwane lokalizacje | Czytelnik zabezpieczeń | Administrator dostępu warunkowego, administrator zabezpieczeń

## <a name="security---identity-security-score"></a>Zabezpieczenia — Ocena zabezpieczeń tożsamości

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role | 
---- | --------------------- | ----------------
Odczytaj całą konfigurację | Czytelnik zabezpieczeń | Administrator zabezpieczeń
Odczytaj ocenę zabezpieczeń | Czytelnik zabezpieczeń | Administrator zabezpieczeń
Aktualizowanie stanu zdarzenia | Administrator zabezpieczeń | 

## <a name="security---risky-sign-ins"></a>Zabezpieczenia — ryzykowne logowania

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Odczytaj całą konfigurację | Czytelnik zabezpieczeń | 
Odczytuj ryzykowne logowania | Czytelnik zabezpieczeń | 

## <a name="security---users-flagged-for-risk"></a>Zabezpieczenia — użytkownicy oflagowani w celu ryzyka

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Odrzucanie wszystkich zdarzeń. | Administrator zabezpieczeń | 
Odczytaj całą konfigurację | Czytelnik zabezpieczeń | 
Odczytuj użytkowników oflagowanych w celu ryzyka | Czytelnik zabezpieczeń | 

## <a name="users"></a>Użytkownicy

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Dodawanie użytkownika do roli katalogu | Administrator ról uprzywilejowanych | 
Dodaj użytkownika do grupy | Administrator użytkowników | 
Przypisywanie licencji | Administrator licencji | Administrator użytkowników
Utwórz użytkownika-gościa | Zapraszający gościa | Administrator użytkowników
Tworzenie użytkownika | Administrator użytkowników | 
Usuwanie użytkowników | Administrator użytkowników | 
Unieważnianie tokenów odświeżania ograniczonych administratorów (zobacz dokumentację) | Administrator użytkowników | 
Unieważnij tokeny odświeżania nie będących administratorami (zobacz dokumentację) | Administrator haseł | Administrator użytkowników
Unieważnianie tokenów odświeżania uprzywilejowanych administratorów (zobacz dokumentację) | Administrator uprzywilejowanego uwierzytelniania | 
Odczytaj konfigurację podstawową | Domyślna rola użytkownika ([Zobacz dokumentację](../fundamentals/users-default-permissions.md) | 
Resetowanie hasła dla ograniczonych administratorów (zobacz dokumentację) | Administrator użytkowników | 
Zresetuj hasło niebędących administratorami (zobacz dokumentację) | Administrator haseł | Administrator użytkowników
Resetowanie hasła administratorów uprzywilejowanych | Administrator uprzywilejowanego uwierzytelniania | 
Odwołaj licencję | Administrator licencji | Administrator użytkowników
Aktualizuj wszystkie właściwości z wyjątkiem nazwy głównej użytkownika | Administrator użytkowników | 
Aktualizowanie głównej nazwy użytkownika dla ograniczonych administratorów (zobacz dokumentację) | Administrator użytkowników | 
Zaktualizuj Właściwość głównej nazwy użytkownika dla administratorów uprzywilejowanych (zobacz dokumentację) | Administrator globalny | 
Aktualizowanie ustawień użytkownika | Administrator globalny | 


## <a name="support"></a>Pomoc techniczna

Zadanie | Najmniejsza rola uprzywilejowana | Dodatkowe role
---- | --------------------- | ----------------
Prześlij bilet pomocy technicznej | Administrator usługi | Administrator aplikacji, administrator Azure Information Protection, administrator rozliczeń, administrator aplikacji w chmurze, administrator zgodności, administrator systemu Dynamics 365, administrator usługi Desktop Analytics, administrator programu Power BI Exchange, administrator, administrator, administrator, administrator, Administratorzy zespołu, administrator zespołów, administrator

## <a name="next-steps"></a>Następne kroki

* [Jak przypisać lub usunąć role administratorów usługi Azure AD](directory-manage-roles-portal.md)
* [Informacje o rolach administratorów usługi Azure AD](directory-assign-admin-roles.md)