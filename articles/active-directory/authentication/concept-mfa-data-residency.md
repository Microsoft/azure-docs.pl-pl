---
title: Dane uwierzytelniania wieloskładnikowego usługi Azure AD
description: Dowiedz się, jakie dane osobowe i organizacyjne są przechowywane w usłudze Azure AD wieloskładnikowe magazyny uwierzytelniania, i użytkowników oraz jakie dane pozostają w kraju/regionie pochodzenia.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c8df67a8cfed92e478caacca1171b7a48fa9ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932900"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Dane w miejscu zamieszkania i dane klienta dotyczące uwierzytelniania wieloskładnikowego usługi Azure AD

Azure Active Directory (Azure AD) przechowuje dane klientów w lokalizacji geograficznej na podstawie adresu, który organizacja udostępnia w przypadku subskrybowania usługi online firmy Microsoft, takiej jak Microsoft 365 lub Azure. Aby uzyskać informacje o tym, gdzie są przechowywane dane klientów, zobacz [gdzie znajdują się Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) w centrum zaufania firmy Microsoft.

Oparte na chmurze uwierzytelnianie wieloskładnikowe usługi Azure AD i proces serwera uwierzytelniania wieloskładnikowego platformy Azure oraz przechowywanie danych osobowych i danych organizacji. W tym artykule opisano, jak i gdzie są przechowywane dane.

Usługa uwierzytelniania wieloskładnikowego usługi Azure AD zawiera centra danych w Stany Zjednoczone, Europie i Azja i Pacyfik. Następujące działania pochodzą z regionalnych centrów danych, z wyjątkiem sytuacji, w których zostały zanotowane:

* Połączenia telefoniczne z uwierzytelnianiem wieloskładnikowym pochodzą z Stany Zjednoczone centrów danych i są kierowane przez globalnych dostawców.
* Żądania uwierzytelniania użytkownika ogólnego zastosowania z innych regionów są obecnie przetwarzane na podstawie lokalizacji użytkownika.
* Powiadomienia wypychane korzystające z aplikacji Microsoft Authenticator są obecnie przetwarzane w regionalnych centrach danych na podstawie lokalizacji użytkownika. Usługi urządzenia specyficzne dla dostawcy, takie jak Apple Push Notification Service, mogą znajdować się poza lokalizacją użytkownika.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Dane osobowe przechowywane przez uwierzytelnianie wieloskładnikowe usługi Azure AD

Dane osobowe to informacje na poziomie użytkownika, które są skojarzone z określoną osobą. Następujące magazyny danych zawierają informacje osobiste:

* Zablokowani użytkownicy
* Pominięte użytkownicy
* Microsoft Authenticator żądania zmiany tokenu urządzenia
* Raporty dotyczące aktywności uwierzytelniania wieloskładnikowego — należy przechowywać aktywność uwierzytelniania wieloskładnikowego z poziomu uwierzytelniania wieloskładnikowego: rozszerzenia serwera NPS, karty AD FS i serwera MFA.
* Microsoft Authenticator aktywacje

Te informacje są przechowywane przez 90 dni.

Uwierzytelnianie wieloskładnikowe usługi Azure AD nie rejestruje danych osobowych, takich jak nazwy użytkowników, numery telefonów lub adresy IP. *UserObjectId* jednak identyfikuje próby uwierzytelniania dla użytkowników. Dane dziennika są przechowywane przez 30 dni.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Dane przechowywane przez uwierzytelnianie wieloskładnikowe usługi Azure AD

W przypadku chmur publicznych platformy Azure, z wyłączeniem uwierzytelniania Azure AD B2C, rozszerzenia serwera NPS i karty Active Directory Federation Services systemu Windows Server 2016 lub 2019 (AD FS), przechowywane są następujące dane osobowe:

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | Dzienniki uwierzytelniania wieloskładnikowego     |
| Jednokierunkowa wiadomość SMS                          | Dzienniki uwierzytelniania wieloskładnikowego     |
| Połączenie głosowe                           | Dzienniki uwierzytelniania wieloskładnikowego<br/>Magazyn danych raportu aktywności wieloskładnikowej<br/>Zablokowani użytkownicy (w przypadku zgłoszenia oszustwa) |
| Powiadomienie Microsoft Authenticator | Dzienniki uwierzytelniania wieloskładnikowego<br/>Magazyn danych raportu aktywności wieloskładnikowej<br/>Zablokowani użytkownicy (w przypadku zgłoszenia oszustwa)<br/>Żądania zmiany w przypadku zmiany tokenu urządzenia Microsoft Authenticator |

W przypadku Microsoft Azure Government, Microsoft Azure (Niemcy), Microsoft Azure obsługiwane przez firmę 21Vianet, Azure AD B2C uwierzytelniania, rozszerzenia serwera NPS i karty AD FS systemu Windows Server 2016 lub 2019, następujące dane osobowe są przechowywane:

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | Dzienniki uwierzytelniania wieloskładnikowego<br/>Magazyn danych raportu aktywności wieloskładnikowej |
| Jednokierunkowa wiadomość SMS                          | Dzienniki uwierzytelniania wieloskładnikowego<br/>Magazyn danych raportu aktywności wieloskładnikowej |
| Połączenie głosowe                           | Dzienniki uwierzytelniania wieloskładnikowego<br/>Magazyn danych raportu aktywności wieloskładnikowej<br/>Zablokowani użytkownicy (w przypadku zgłoszenia oszustwa) |
| Powiadomienie Microsoft Authenticator | Dzienniki uwierzytelniania wieloskładnikowego<br/>Magazyn danych raportu aktywności wieloskładnikowej<br/>Zablokowani użytkownicy (w przypadku zgłoszenia oszustwa)<br/>Żądania zmiany w przypadku zmiany tokenu urządzenia Microsoft Authenticator |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Dane przechowywane przez serwer uwierzytelniania wieloskładnikowego platformy Azure

W przypadku korzystania z serwera uwierzytelniania wieloskładnikowego platformy Azure przechowywane są następujące dane osobowe.

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie oferuje już serwera uwierzytelniania wieloskładnikowego dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego, powinni korzystać z uwierzytelniania wieloskładnikowego usługi Azure AD opartego na chmurze. Istniejący klienci, którzy aktywują serwer uwierzytelniania wieloskładnikowego przed 1 lipca 2019, mogą pobrać najnowszą wersję i aktualizacje i wygenerować poświadczenia aktywacji w zwykły sposób.

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | Dzienniki uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej |
| Jednokierunkowa wiadomość SMS                          | Dzienniki uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej |
| Połączenie głosowe                           | Dzienniki uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej<br />Zablokowani użytkownicy (w przypadku zgłoszenia oszustwa) |
| Powiadomienie Microsoft Authenticator | Dzienniki uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej<br />Zablokowani użytkownicy (w przypadku zgłoszenia oszustwa)<br />Zmień żądania, gdy Microsoft Authenticator zmiany tokenu urządzenia |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Dane organizacji przechowywane przez wieloskładnikowe uwierzytelnianie usługi Azure AD

Dane organizacji to informacje na poziomie dzierżawy, które mogą uwidaczniać konfigurację lub instalację środowiska. Ustawienia dzierżawy z następujących Azure Portal strony uwierzytelniania wieloskładnikowego mogą przechowywać dane organizacji, takie jak progi blokady lub informacje o IDENTYFIKATORze rozmówcy dla przychodzących żądań uwierzytelniania telefonu:

* Blokada konta
* Alert dotyczący wykrycia oszustwa
* Powiadomienia
* Ustawienia połączenia telefonicznego

W przypadku serwera uwierzytelniania wieloskładnikowego platformy Azure następujące strony Azure Portal mogą zawierać dane organizacji:

* Ustawienia serwera
* Jednorazowe obejście
* Reguły buforowania
* Stan serwera uwierzytelniania wieloskładnikowego

## <a name="multifactor-authentication-logs-location"></a>Lokalizacja dzienników uwierzytelniania wieloskładnikowego

W poniższej tabeli przedstawiono lokalizację dzienników usług dla chmur publicznych.

| Chmura publiczna| Dzienniki logowania | Raport aktywności wieloskładnikowej        | Dzienniki usługi uwierzytelniania wieloskładnikowego       |
|-------------|--------------|----------------------------------------|------------------------|
| Stany Zjednoczone          | Stany Zjednoczone           | Stany Zjednoczone                                     | Stany Zjednoczone                     |
| Europa      | Europa       | Stany Zjednoczone                                     | Europa <sup>2</sup>    |
| Australia   | Australia    | Stany Zjednoczone<sup>1</sup>                         | Australia <sup>2</sup> |

<sup>1</sup> Dzienniki kodu OATH są przechowywane w Australii.

<sup>2</sup> Rozmowy głosowe dzienniki usługi uwierzytelniania wieloskładnikowego są przechowywane w Stany Zjednoczone.

W poniższej tabeli przedstawiono lokalizację dzienników usług dla Niesuwerennych chmur.

| Suwerenna chmura                      | Dzienniki logowania                         | Raport aktywności wieloskładnikowej (zawiera dane osobowe)| Dzienniki usługi uwierzytelniania wieloskładnikowego |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure (Niemcy)              | Niemcy                              | Stany Zjednoczone                            | Stany Zjednoczone               |
| Azure w Chinach — 21Vianet                 | Chiny                                | Stany Zjednoczone                            | Stany Zjednoczone               |
| Chmura firmy Microsoft dla instytucji rządowych           | Stany Zjednoczone                                   | Stany Zjednoczone                            | Stany Zjednoczone               |

Raporty dotyczące działań związanych z uwierzytelnianiem wieloskładnikowym zawierają dane osobowe, takie jak główna nazwa użytkownika (UPN) i pełny numer telefonu.

Do obsługi usługi są używane dzienniki usługi uwierzytelniania wieloskładnikowego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o tym, jakie informacje o użytkowniku są zbierane przez uwierzytelnianie wieloskładnikowe usługi Azure AD oparte na chmurze i serwer uwierzytelniania wieloskładnikowego platformy Azure, zobacz [zbieranie danych użytkownika uwierzytelniania](howto-mfa-reporting-datacollection.md)wieloskładnikowego usługi Azure AD.
