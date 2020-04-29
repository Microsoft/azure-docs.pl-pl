---
title: Dane Multi-Factor Authentication platformy Azure
description: Dowiedz się, jakie dane osobowe i firmowe są przechowywane w usłudze Azure Multi-Factor Authenticatione na użytkownikach i w organizacji oraz jakie dane pozostają w kraju pochodzenia.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29ce7631c0ce8ab83edc7b9cd31dfe0db3be5d7e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309794"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Dane dotyczące miejsca zamieszkania i klienta dla Multi-Factor Authentication platformy Azure

Dane klienta są przechowywane w usłudze Azure AD w lokalizacji geograficznej na podstawie adresu dostarczonego przez organizację w przypadku subskrybowania usługi online firmy Microsoft, takiej jak Office 365 i Azure. Aby uzyskać informacje o tym, gdzie są przechowywane dane klienta, możesz użyć sekcji [gdzie znajdują się Twoje dane?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) Centrum zaufania Microsoft.

Oparta na chmurze usługa Azure Multi-Factor Authentication i proces Serwer Multi-Factor Authentication platformy Azure oraz przechowywanie pewnej ilości danych osobowych i danych organizacji. W tym artykule opisano, jak i gdzie są przechowywane dane.

Następujące działania Multi-Factor Authentication obecnie pochodzą z centrów danych USA, z wyjątkiem sytuacji, w których zanotowano:

* Uwierzytelnianie dwuskładnikowe przy użyciu połączeń telefonicznych i wiadomości SMS zazwyczaj pochodzą z centrów danych US i są kierowane przez dostawców globalnych.
    * Żądania uwierzytelniania użytkowników ogólnego przeznaczenia z innych regionów, takich jak Europa lub Australia, są obecnie przetwarzane przez centra danych w tym regionie. Inne zdarzenia, takie jak Samoobsługowe resetowanie haseł, zdarzenia usługi Azure B2C lub scenariusze hybrydowe korzystające z rozszerzenia serwera NPS lub karty AD FS, są obecnie przetwarzane przez centra danych US.
* Powiadomienia wypychane przy użyciu aplikacji Microsoft Authenticator pochodzą z centrów danych US. Ponadto usługi specyficzne dla dostawcy urządzenia mogą również być odtwarzane z różnych regionów.
* Kody OATH są zwykle weryfikowane w Stanach Zjednoczonych
    * Ponownie zdarzenia związane z uwierzytelnianiem użytkowników ogólnego przeznaczenia, które pochodzą z innych regionów, takich jak Europa lub Australia, są przetwarzane przez centra danych w tym regionie. Dodatkowe zdarzenia są obecnie przetwarzane przez centra danych US.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Dane osobowe przechowywane przez Multi-Factor Authentication platformy Azure

Dane osobowe są informacjami o poziomie użytkownika skojarzonymi z określoną osobą. Następujące magazyny danych zawierają informacje osobiste:

* Zablokowani użytkownicy
* Pominięte użytkownicy
* Microsoft Authenticator żądania zmiany tokenu urządzenia
* Raporty działania Multi-Factor Authentication
* Microsoft Authenticator aktywacje

Te informacje są przechowywane przez 90 dni.

Usługa Azure Multi-Factor Authentication nie rejestruje danych osobowych, takich jak nazwa użytkownika, numer telefonu lub adres IP, ale istnieje *UserObjectId* , który identyfikuje Multi-Factor Authentication prób dla użytkowników. Dane dziennika są przechowywane przez 30 dni.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

W przypadku chmur publicznych platformy Azure, z wyłączeniem uwierzytelniania usługi Azure B2C, rozszerzenia serwera NPS i karty AD FS z systemem Windows Server 2016 lub 2019, następujące dane osobowe są przechowywane:

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | W dziennikach Multi-Factor Authentication     |
| Jednokierunkowa wiadomość SMS                          | W dziennikach Multi-Factor Authentication     |
| Połączenie głosowe                           | W dziennikach Multi-Factor Authentication<br />Magazyn danych raportu działania Multi-Factor Authentication<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa |
| Powiadomienie Microsoft Authenticator | W dziennikach Multi-Factor Authentication<br />Magazyn danych raportu działania Multi-Factor Authentication<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa<br />Zmień żądania, gdy Microsoft Authenticator zmiany tokenu urządzenia |

> [!NOTE]
> Magazyn danych raportu aktywności Multi-Factor Authentication jest przechowywany w Stany Zjednoczone dla wszystkich chmur, niezależnie od regionu, w którym jest przetwarzane żądanie uwierzytelnienia. Microsoft Azure (Niemcy), Microsoft Azure obsługiwane przez firmę 21Vianet, a w chmurze Microsoft dla instytucji rządowych istnieją własne niezależne magazyny danych oddzielone od magazynów danych z regionu chmury publicznej, jednak te dane są zawsze przechowywane w Stany Zjednoczone.

W przypadku Microsoft Azure Government, Microsoft Azure (Niemcy), Microsoft Azure obsługiwane przez firmę 21Vianet, uwierzytelnianie B2C Azure, rozszerzenie serwera NPS i system Windows Server 2016 lub 2019 AD FS kartami są przechowywane następujące dane osobowe:

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | W dziennikach Multi-Factor Authentication<br />Magazyn danych raportu działania Multi-Factor Authentication |
| Jednokierunkowa wiadomość SMS                          | W dziennikach Multi-Factor Authentication<br />Magazyn danych raportu działania Multi-Factor Authentication |
| Połączenie głosowe                           | W dziennikach Multi-Factor Authentication<br />Magazyn danych raportu działania Multi-Factor Authentication<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa |
| Powiadomienie Microsoft Authenticator | W dziennikach Multi-Factor Authentication<br />Magazyn danych raportu działania Multi-Factor Authentication<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa<br />Zmień żądania, gdy Microsoft Authenticator zmiany tokenu urządzenia |

### <a name="multi-factor-authentication-server"></a>Serwer Multi-Factor Authentication

W przypadku wdrażania i uruchamiania usługi Azure Serwer Multi-Factor Authentication są przechowywane następujące dane osobowe:

> [!IMPORTANT]
> Od 1 lipca 2019 firma Microsoft nie będzie już oferować Serwer Multi-Factor Authentication w przypadku nowych wdrożeń. Nowi klienci, którzy chcą wymagać uwierzytelniania wieloskładnikowego od użytkowników, powinni korzystać z usługi Azure Multi-Factor Authentication opartej na chmurze. Istniejący klienci, którzy aktywowali Serwer Multi-Factor Authentication przed 1 lipca, będą mogli pobrać najnowszą wersję, przyszłe aktualizacje i generować poświadczenia aktywacji w zwykły sposób.

| Typ zdarzenia                           | Typ magazynu danych |
|--------------------------------------|-----------------|
| Token OATH                           | W dziennikach Multi-Factor Authentication<br />Magazyn danych raportu działania Multi-Factor Authentication |
| Jednokierunkowa wiadomość SMS                          | W dziennikach Multi-Factor Authentication<br />Magazyn danych raportu działania Multi-Factor Authentication |
| Połączenie głosowe                           | W dziennikach Multi-Factor Authentication<br />Magazyn danych raportu działania Multi-Factor Authentication<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa |
| Powiadomienie Microsoft Authenticator | W dziennikach Multi-Factor Authentication<br />Magazyn danych raportu działania Multi-Factor Authentication<br />Zablokowani użytkownicy w przypadku zgłoszenia oszustwa<br />Zmień żądania, gdy Microsoft Authenticator zmiany tokenu urządzenia |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Dane organizacji przechowywane przez Multi-Factor Authentication platformy Azure

Dane organizacji to informacje na poziomie dzierżawy, które mogą uwidaczniać konfigurację lub instalację środowiska. Ustawienia dzierżawy z następujących Azure Portal strony Multi-Factor Authentication mogą przechowywać dane organizacji, takie jak progi blokady lub informacje o IDENTYFIKATORze rozmówcy dla przychodzących żądań uwierzytelniania na telefon:

* Blokada konta
* Alert dotyczący wykrycia oszustwa
* Powiadomienia
* Ustawienia połączenia telefonicznego

W przypadku usługi Azure Serwer Multi-Factor Authentication następujące strony Azure Portal mogą zawierać dane organizacji:

* Ustawienia serwera
* Jednorazowe obejście
* Reguły buforowania
* Stan Serwer Multi-Factor Authentication

## <a name="log-data-location"></a>Lokalizacja danych dziennika

Gdzie przechowywane są informacje o dziennikach, zależy od regionu, w którym są przetwarzane. Większość lokalizacje geograficzne ma natywne możliwości Multi-Factor Authentication platformy Azure, więc dane dziennika są przechowywane w tym samym regionie, w którym przetwarza żądanie Multi-Factor Authentication. W lokalizacje geograficzne bez natywnej pomocy technicznej platformy Azure Multi-Factor Authentication są obsługiwane przez Stany Zjednoczone lub Europa lokalizacje geograficzne i dane dziennika są przechowywane w tym samym regionie, w którym przetwarza żądanie Multi-Factor Authentication.

Niektóre dane dziennika uwierzytelniania podstawowego są przechowywane tylko w Stany Zjednoczone. Microsoft Azure (Niemcy) i Microsoft Azure obsługiwane przez firmę 21Vianet są zawsze przechowywane w odpowiedniej chmurze. Dane dziennika w chmurze firmy Microsoft są zawsze przechowywane w Stany Zjednoczone.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o tym, jakie informacje o użytkowniku są zbierane przez usługę Azure Multi-Factor Authentication i usługę Azure Serwer Multi-Factor Authentication, zobacz [zbieranie danych użytkownika przez platformę azure Multi-Factor Authentication](howto-mfa-reporting-datacollection.md).
