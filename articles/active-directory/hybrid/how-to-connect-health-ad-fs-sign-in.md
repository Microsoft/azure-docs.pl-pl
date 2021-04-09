---
title: AD FS logowania w usłudze Azure AD za pomocą programu Connect Health | Microsoft Docs
description: W tym dokumencie opisano sposób integrowania AD FS logowania przy użyciu raportu Azure AD Connect Health logowania.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103574797"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>AD FS logowania w usłudze Azure AD za pomocą programu Connect Health — wersja zapoznawcza

AD FS logowania można teraz zintegrować z raportem Azure Active Directory logowania przy użyciu programu Connect Health. Raport [raport dotyczący logowania usługi Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.) zawiera informacje o tym, kiedy użytkownicy, aplikacje i zarządzane zasoby logują się w usłudze Azure AD i uzyskują dostęp do zasobów. 

Agent Connect Health dla AD FS umożliwia skorelowanie wielu identyfikatorów zdarzeń z AD FS, zależnie od wersji serwera, w celu dostarczenia informacji o żądaniu i szczegóły błędu, jeśli żądanie nie powiedzie się. Te informacje są skorelowane ze schematem raportu logowania usługi Azure AD i wyświetlane w środowisku użytkownika raportu Sign-In usługi Azure AD. Obok raportu jest dostępny nowy strumień Log Analytics z danymi AD FS i nowym szablonem skoroszytu Azure Monitor. Szablon może być używany i modyfikowany w celu uzyskania szczegółowej analizy scenariuszy, takich jak blokady konta AD FS, nieudane próby wprowadzenia hasła i przekroczenia nieoczekiwanych prób logowania.

## <a name="prerequisites"></a>Wymagania wstępne
* Azure AD Connect Health AD FS zainstalowanych i uaktualnionych do najnowszej wersji.
* Rola czytelnika Administrator globalny lub raporty, aby wyświetlić logowania do usługi Azure AD

## <a name="what-data-is-displayed-in-the-report"></a>Jakie dane są wyświetlane w raporcie?
Dostępne dane odzwierciedlają te same dane, które są dostępne dla logowania do usługi Azure AD. Pięć kart z informacjami będzie dostępnych na podstawie typu logowania, usługi Azure AD lub AD FS. Program Connect Health skorelowanie zdarzeń z AD FS, zależnie od wersji serwera i dopasowuje je do schematu AD FS. 



#### <a name="user-sign-ins"></a>Logowania użytkowników 
Każda karta w bloku logowania pokazuje poniższe wartości domyślne:
* Data logowania
* Identyfikator żądania
* Nazwa użytkownika lub identyfikator użytkownika
* Stan logowania
* Adres IP urządzenia używanego do logowania
* Identyfikator Sign-In

#### <a name="authentication-method-information"></a>Informacje o metodzie uwierzytelniania
Na karcie Uwierzytelnianie można wyświetlić następujące wartości. Metoda uwierzytelniania jest pobierana z dzienników inspekcji AD FS.

|Metoda uwierzytelniania|Opis|
|-----|-----|
|Formularze|Uwierzytelnianie nazwy użytkownika/hasła|
|Windows|Uwierzytelnianie zintegrowane systemu Windows|
|Certyfikat|Uwierzytelnianie przy użyciu certyfikatów SmartCard/VirtualSmart|
|WindowsHelloForBusiness|To pole służy do uwierzytelniania za pomocą usługi Windows Hello dla firm. (Microsoft Passport uwierzytelniania)|
|Urządzenie | Wyświetlany, jeśli wybrano uwierzytelnianie urządzenia jako "podstawowe" z intranetu/ekstranetu i jest wykonywane uwierzytelnianie urządzenia.  W tym scenariuszu nie ma oddzielnego uwierzytelniania użytkownika.| 
|Federacyjni|AD FS nie przeprowadził uwierzytelniania, ale wysłał go do dostawcy tożsamości innej firmy|
|Logowanie jednokrotne |Jeśli użyto tokenu logowania jednokrotnego, to pole będzie wyświetlane. Jeśli logowanie jednokrotne ma uwierzytelnianie wieloskładnikowe, będzie widoczne jako wieloskładnikowe|
|Wieloskładnikowe|Jeśli token logowania jednokrotnego ma uwierzytelnianie wieloskładnikowe i został użyty do uwierzytelnienia, to pole będzie wyświetlane jako wieloskładnikowe|
|Azure MFA|Usługa Azure MFA została wybrana jako dodatkowy Dostawca uwierzytelniania w AD FS i została użyta do uwierzytelniania|
|ADFSExternalAuthenticationProvider|To pole jest używane, jeśli dostawca uwierzytelniania innej firmy został zarejestrowany i użyty do uwierzytelniania|


#### <a name="ad-fs-additional-details"></a>AD FS dodatkowe szczegóły
Następujące szczegóły są dostępne dla AD FS logowania:
* Nazwa serwera
* Łańcuch adresów IP
* Protokół

### <a name="enabling-log-analytics-and-azure-monitor"></a>Włączanie Log Analytics i Azure Monitor
Log Analytics można włączyć dla AD FS logowania i mogą być używane z innymi Log Analytics zintegrowanymi składnikami, takimi jak wskaźnikiem.

> [!NOTE] 
> AD FS logowania mogą znacząco zwiększyć Log Analytics kosztów, w zależności od liczby logowań do AD FS w organizacji. Aby włączyć i wyłączyć Log Analytics, zaznacz pole wyboru dla strumienia.

Aby włączyć Log Analytics dla funkcji, przejdź do bloku Log Analytics i wybierz pozycję strumień "ADFSSignIns". Wybranie tej opcji umożliwi AD FS logowanie do Log Analytics.

Aby uzyskać dostęp do zaktualizowanego szablonu skoroszytu Azure Monitor, przejdź do "Azure Monitor szablonów" i wybierz skoroszyt "logowania".
Aby uzyskać więcej informacji na temat skoroszytów, odwiedź [Azure monitor skoroszyty](https://aka.ms/adfssigninspreview).




### <a name="frequently-asked-questions"></a>Często zadawane pytania
***Jakie są typy logowań, które mogą być wyświetlane?***
Raport logowania obsługuje logowania za pomocą protokołów O uwierzytelnianiu z protokołem WS-auth, SAML i WS-Trust. 

***Jak są różne typy logowań wyświetlane w raporcie logowania?***
Jeśli zostanie wykonane bezproblemowe logowanie jednokrotne, będzie istnieć jeden wiersz dla logowania z jednym IDENTYFIKATORem korelacji.
Jeśli zostanie wykonane uwierzytelnianie pojedynczego czynnika, w dwóch wierszach zostanie wypełniony ten sam identyfikator korelacji, ale z dwoma różnymi metodami uwierzytelniania (tj. formularzami SSO).
W przypadku uwierzytelniania wieloskładnikowego będzie dostępnych trzy wiersze z współdzielonym IDENTYFIKATORem korelacji i trzema odpowiednimi metodami uwierzytelniania (np. Forms, AzureMFA, wieloskładnikowe). W tym konkretnym przykładzie wieloskładnikowy w tym przypadku pokazuje, że logowanie jednokrotne ma uwierzytelnianie wieloskładnikowe.

***Jakie błędy można zobaczyć w raporcie?***
Aby zapoznać się z pełną listą AD FS związanych z błędami, które zostały wprowadzone w raporcie Sign-In i opisach, odwiedź stronę [AD FS informacje o kodzie błędu pomocy](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference)

***Widzę "00000000-0000-0000-0000-000000000000" w sekcji "użytkownik" logowania. Co to oznacza?***
Jeśli logowanie nie powiodło się, a próba użycia nazwy UPN nie jest zgodna z istniejącą nazwą UPN, pola "użytkownik", "username" i "ID użytkownika" będą mieć wartość "00000000-0000-0000-0000-000000000000", a identyfikator logowania zostanie wypełniony przy użyciu podjętej przez użytkownika wartości. W takich przypadkach użytkownik próbujący zalogować się nie istnieje.

***Jak skorelować zdarzenia lokalne z raportem logowania usługi Azure AD?***
Agent Azure AD Connect Health dla AD FS skorelowanie identyfikatorów zdarzeń z AD FS zależnych od wersji serwera. Zdarzenia będą dostępne w dzienniku zabezpieczeń serwerów AD FS. 

***Dlaczego w przypadku niektórych AD FS logowania widzisz wartość NotSet lub NotApplicable (program w IDENTYFIKATORze/nazwie aplikacji?***
Raport AD FS Sign-In będzie wyświetlał identyfikatory OAuth w polu Identyfikator aplikacji dla logowania OAuth. W scenariuszach logowania za pomocą protokołu WS-in WS-Trust identyfikator aplikacji będzie miał wartość NotSet lub NotApplicable (program, a identyfikatory zasobów i jednostek uzależnionych będą obecne w polu Identyfikator zasobu.

***Czy istnieją jakieś znane problemy z raportem w wersji zapoznawczej?***
Raport ma znany problem, którego pole "wymaganie uwierzytelniania" na karcie "podstawowe informacje" zostanie wypełnione jako wartość uwierzytelniania pojedynczego czynnika dla AD FS logowania niezależnie od logowania. Ponadto na karcie Szczegóły uwierzytelniania zostanie wyświetlona wartość "podstawowa lub pomocnicza" w polu wymagania, z poprawkami w toku w celu odróżnienia typów uwierzytelniania podstawowego lub pomocniczego.


## <a name="related-links"></a>Linki pokrewne
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalowanie agenta programu Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Raport dotyczący ryzykownego adresu IP](how-to-connect-health-adfs-risky-ip.md)





