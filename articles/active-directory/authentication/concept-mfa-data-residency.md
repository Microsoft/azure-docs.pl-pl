---
title: Dane uwierzytelniania wieloskładnikowego usługi Azure AD
description: Dowiedz się, jakie dane osobowe i organizacyjne są przechowywane w usłudze Azure AD wieloskładnikowe magazyny uwierzytelniania, i użytkowników oraz jakie dane pozostają w kraju/regionie pochodzenia.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208356"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Dane w miejscu zamieszkania i dane klienta dotyczące uwierzytelniania wieloskładnikowego usługi Azure AD

Dane klienta są przechowywane w usłudze Azure AD w lokalizacji geograficznej na podstawie adresu dostarczonego przez organizację podczas subskrybowania usługi online firmy Microsoft, takiej jak Microsoft 365 i Azure. Aby uzyskać informacje o tym, gdzie są przechowywane dane klienta, możesz użyć sekcji [gdzie znajdują się Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Centrum zaufania Microsoft.

Oparte na chmurze uwierzytelnianie wieloskładnikowe usługi Azure AD i proces serwera uwierzytelniania wieloskładnikowego usługi Azure AD oraz przechowywanie pewnej ilości danych osobowych i danych organizacji. W tym artykule opisano, jak i gdzie są przechowywane dane.

Usługa uwierzytelniania wieloskładnikowego usługi Azure AD zawiera centra danych w Stanach Zjednoczonych, Europie i Azja i Pacyfik. Następujące działania pochodzą z regionalnych centrów danych, z wyjątkiem sytuacji, w których zanotowano:

* Uwierzytelnianie wieloskładnikowe przy użyciu połączeń telefonicznych pochodzi z centrów danych USA i są kierowane przez dostawców globalnych.
* Żądania uwierzytelniania użytkowników ogólnego przeznaczenia z innych regionów, takich jak Europa lub Australia, są obecnie przetwarzane na podstawie lokalizacji użytkownika.
* Powiadomienia wypychane przy użyciu aplikacji Microsoft Authenticator są obecnie przetwarzane w regionalnych centrach danych na podstawie lokalizacji użytkownika.
    * Usługi specyficzne dla dostawcy urządzenia, takie jak powiadomienia wypychane firmy Apple, mogą znajdować się poza lokalizacją użytkownika.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Dane osobowe przechowywane przez uwierzytelnianie wieloskładnikowe usługi Azure AD

Dane osobowe są informacjami o poziomie użytkownika skojarzonymi z określoną osobą. Następujące magazyny danych zawierają informacje osobiste:

* Zablokowani użytkownicy
* Pominięte użytkownicy
* Microsoft Authenticator żądania zmiany tokenu urządzenia
* Raporty dotyczące aktywności z uwierzytelnianiem wieloskładnikowym
* Microsoft Authenticator aktywacje

Te informacje są przechowywane przez 90 dni.

Uwierzytelnianie wieloskładnikowe usługi Azure AD nie rejestruje danych osobowych, takich jak nazwa użytkownika, numer telefonu lub adres IP, ale istnieje *UserObjectId* , które identyfikują próby uwierzytelniania wieloskładnikowego dla użytkowników. Dane dziennika są przechowywane przez 30 dni.

### <a name="azure-ad-multifactor-authentication"></a>Uwierzytelnianie wieloskładnikowe usługi Azure AD

W przypadku chmur publicznych platformy Azure, z wyłączeniem uwierzytelniania usługi Azure B2C, rozszerzenia serwera NPS i karty AD FS z systemem Windows Server 2016 lub 2019, następujące dane osobowe są przechowywane:

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | W dziennikach uwierzytelniania wieloskładnikowego     |
| Jednokierunkowa wiadomość SMS                          | W dziennikach uwierzytelniania wieloskładnikowego     |
| Połączenie głosowe                           | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa |
| Powiadomienie Microsoft Authenticator | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa<br />Zmień żądania, gdy Microsoft Authenticator zmiany tokenu urządzenia |

W przypadku Microsoft Azure Government, Microsoft Azure (Niemcy), Microsoft Azure obsługiwane przez firmę 21Vianet, uwierzytelnianie B2C Azure, rozszerzenie serwera NPS i system Windows Server 2016 lub 2019 AD FS kartami są przechowywane następujące dane osobowe:

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej |
| Jednokierunkowa wiadomość SMS                          | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej |
| Połączenie głosowe                           | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa |
| Powiadomienie Microsoft Authenticator | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa<br />Zmień żądania, gdy Microsoft Authenticator zmiany tokenu urządzenia |

### <a name="multifactor-authentication-server"></a>Serwer uwierzytelniania wieloskładnikowego

W przypadku wdrażania i uruchamiania serwera uwierzytelniania wieloskładnikowego usługi Azure AD następujące dane osobowe są przechowywane:

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie będzie już oferować wieloskładnikowego serwera uwierzytelniania dla nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego dla użytkowników, powinni korzystać z uwierzytelniania wieloskładnikowego usługi Azure AD opartego na chmurze. Istniejący klienci, którzy aktywowali serwer uwierzytelniania wieloskładnikowego przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i generować poświadczenia aktywacji w zwykły sposób.

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej |
| Jednokierunkowa wiadomość SMS                          | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej |
| Połączenie głosowe                           | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa |
| Powiadomienie Microsoft Authenticator | W dziennikach uwierzytelniania wieloskładnikowego<br />Magazyn danych raportu aktywności wieloskładnikowej<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa<br />Zmień żądania, gdy Microsoft Authenticator zmiany tokenu urządzenia |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Dane organizacji przechowywane przez wieloskładnikowe uwierzytelnianie usługi Azure AD

Dane organizacji to informacje na poziomie dzierżawy, które mogą uwidaczniać konfigurację lub instalację środowiska. Ustawienia dzierżawy z następujących Azure Portal strony uwierzytelniania wieloskładnikowego mogą przechowywać dane organizacji, takie jak progi blokady lub informacje o IDENTYFIKATORze rozmówcy dla przychodzących żądań uwierzytelniania na telefon:

* Blokada konta
* Alert dotyczący wykrycia oszustwa
* Powiadomienia
* Ustawienia połączenia telefonicznego

W przypadku serwera uwierzytelniania wieloskładnikowego usługi Azure AD następujące strony Azure Portal mogą zawierać dane organizacji:

* Ustawienia serwera
* Jednorazowe obejście
* Reguły buforowania
* Stan serwera uwierzytelniania wieloskładnikowego

## <a name="multifactor-authentication-logs-location"></a>Lokalizacja dzienników uwierzytelniania wieloskładnikowego

W poniższej tabeli przedstawiono lokalizację dzienników usług dla chmur publicznych.

| Chmura publiczna| Dzienniki logowania | Raport aktywności wieloskładnikowej        | Dzienniki usługi uwierzytelniania wieloskładnikowego       |
|-------------|--------------|----------------------------------------|------------------------|
| USA          | USA           | USA                                     | USA                     |
| Europa      | Europa       | USA                                     | Europa <sup>2</sup>    |
| Australia   | Australia    | US<sup>1</sup>                         | Australia <sup>2</sup> |

<sup>1</sup> Dzienniki kodu OATH są przechowywane w Australii

<sup>2</sup> Połączenia głosowe dzienników usługi uwierzytelniania wieloskładnikowego są przechowywane w Stanach Zjednoczonych

W poniższej tabeli przedstawiono lokalizację dzienników usług dla Niesuwerennych chmur.

| Suwerenna chmura                      | Dzienniki logowania                         | Raport aktywności wieloskładnikowej (zawiera dane osobowe)| Dzienniki usługi uwierzytelniania wieloskładnikowego |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure (Niemcy)              | Niemcy                              | USA                            | USA               |
| Microsoft Azure obsługiwane przez firmę 21Vianet | Chiny                                | USA                            | USA               |
| Chmura firmy Microsoft dla instytucji rządowych           | USA                                   | USA                            | USA               |

Dane raportu aktywność uwierzytelniania wieloskładnikowego zawierają dane osobowe, takie jak główna nazwa użytkownika (UPN) i pełny numer telefonu.

Do obsługi usługi są używane dzienniki usługi uwierzytelniania wieloskładnikowego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o tym, jakie informacje o użytkowniku są zbierane przez uwierzytelnianie wieloskładnikowe usługi Azure AD oparte na chmurze i serwer uwierzytelniania wieloskładnikowego usługi Azure AD, zobacz [zbieranie danych użytkownika uwierzytelniania](howto-mfa-reporting-datacollection.md)wieloskładnikowego usługi Azure AD.
