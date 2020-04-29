---
title: Azure Active Directory wykrywania ryzyka | Microsoft Docs
description: Ten artice zawiera szczegółowy przegląd czynników ryzyka, które są wykrywalne.
services: active-directory
keywords: Azure Active Directory Identity Protection, bezpieczeństwo, ryzyko, poziom ryzyka, usterka, zasady zabezpieczeń
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4caa248f6972609ecb6bf71dd521c68d78cebd70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80383960"
---
# <a name="azure-active-directory-risk-detections"></a>Azure Active Directory wykrywania ryzyka

Większość naruszeń zabezpieczeń odbywa się, gdy osoby atakujące uzyskują dostęp do środowiska przez kradzież tożsamości użytkownika. Odnajdywanie zagrożonych tożsamości nie jest łatwe. Azure Active Directory używa adaptacyjnych algorytmów uczenia maszynowego i heurystyki do wykrywania podejrzanych działań, które są związane z kontami użytkowników. Każda wykryte podejrzane działania są przechowywane w rekordzie nazywanym **wykryciem ryzyka**.

Istnieją dwa miejsca, w których można przejrzeć zgłoszone wykrywania zagrożeń:

 - **Raportowanie usługi Azure AD** — wykrywanie ryzyka są częścią raportów zabezpieczeń usługi Azure AD. Aby uzyskać więcej informacji, zapoznaj się z [raportem](concept-user-at-risk.md) o zabezpieczeniach dotyczące użytkowników i [ryzykownych](concept-risky-sign-ins.md)logowań.

 - Wykrycia ryzyka **Azure AD Identity Protection** są również częścią funkcji raportowania [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

Ponadto można użyć [interfejsu API wykrywania ryzyka ochrony tożsamości](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) , aby uzyskać programowy dostęp do wykrywania zabezpieczeń przy użyciu Microsoft Graph. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph](../identity-protection/graph-get-started.md). 

Obecnie Azure Active Directory wykrywa sześć typów wykrycia ryzyka:

- [Użytkownicy z ujawnionymi poświadczeniami](#leaked-credentials) 
- [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) 
- [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) 
- [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) 
- [Logowania z adresów IP związanych z podejrzanymi działaniami](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) 

![Wykrywanie ryzyka](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> Czasami może się okazać, że wykrywanie ryzyka nie ma odpowiedniego wpisu logowania w [raporcie logowania](concept-sign-ins.md). Wynika to z faktu, że usługa Identity Protection szacuje ryzyko dla logowania **interaktywnego** i **nieinterakcyjnego** , podczas gdy raport logowania zawiera tylko interakcyjne logowania.

Szczegółowe informacje na temat wykrytego wykrywania ryzyka są powiązane z subskrypcją usługi Azure AD. 

* Za pomocą **Azure AD — wersja Premium P2**można uzyskać najbardziej szczegółowe informacje na temat wszystkich podstawowych wykryć. 
* W **wersji Azure AD — wersja Premium P1**wykrycie zaawansowane (takie jak nieznane właściwości logowania) nie jest objęte licencją i zostanie wyświetlone pod nazwą **logowania z wykrytym dodatkowym ryzykiem**. Ponadto pola szczegóły poziomu ryzyka i ryzyka są ukryte.

Chociaż wykrywanie zagrożeń reprezentuje już istotny aspekt ochrony tożsamości, można także ręcznie rozwiązać te problemy lub zaimplementować automatyczne odpowiedzi przez skonfigurowanie zasad dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Azure Active Directory Identity Protection (Ochrona tożsamości w usłudze Azure Active Directory)](../active-directory-identityprotection.md).

## <a name="risk-detection-types"></a>Typy wykrywania ryzyka

Właściwość **Typ wykrywania ryzyka** jest identyfikatorem podejrzanej akcji, dla której został utworzony rekord wykrywania ryzyka.

Ciągłe inwestycje firmy Microsoft w proces wykrywania prowadzą do:

- Poprawa dokładności wykrywania istniejących wykryć ryzyka 
- Nowe typy wykrywania ryzyka, które zostaną dodane w przyszłości

### <a name="leaked-credentials"></a>Nieujawnione poświadczenia

Gdy cybernetycznymi naruszają prawidłowe hasła dla uprawnionych użytkowników, często udostępniają te poświadczenia. Jest to zwykle realizowane przez zaksięgowanie ich publicznie w ciemnych witrynach sieci Web lub wklejanie lub przez handel lub sprzedawanie poświadczeń na czarnym rynku. Usługa Microsoft wycieka poświadczeń uzyskuje pary nazw użytkowników i haseł, monitorując publiczne i ciemne witryny sieci Web i wykonując następujące działania:

- Wizytujących
- Przepisy prawne
- Zespoły ds. zabezpieczeń w firmie Microsoft
- Inne zaufane źródła 

Gdy usługa uzyskuje pary username i Password, są sprawdzane pod kątem bieżących prawidłowych poświadczeń przez użytkowników usługi AAD. Gdy zostanie znalezione dopasowanie, oznacza to, że złamano hasło użytkownika i zostanie utworzone **przecieki zagrożeń** .

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Ten typ wykrywania ryzyka identyfikuje użytkowników, którzy pomyślnie logowali się z adresu IP, który został zidentyfikowany jako anonimowy adres IP serwera proxy. Te serwery proxy są używane przez osoby, które chcą ukryć adres IP urządzenia i mogą być używane do złośliwego celu.

### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Ten typ wykrywania ryzyka identyfikuje dwa logowania pochodzące z lokalizacji geograficznie odległych, gdzie co najmniej jedna z tych lokalizacji może być nietypowa dla użytkownika, pod kątem wcześniejszego zachowania. Ten algorytm uczenia maszynowego uwzględnia między innymi różne czynniki czas między dwoma logowaniami i czas, jaki zajęł użytkownikowi w podróży od pierwszej lokalizacji do drugiego, co oznacza, że inny użytkownik korzysta z tych samych poświadczeń.

Algorytm ignoruje oczywiste "fałszywie dodatnie" przyczyniające się do niemożliwych warunków podróży, takich jak sieci VPN i lokalizacje regularnie używane przez innych użytkowników w organizacji. System ma początkowy okres uczenia wynoszący 14 dni, podczas którego nauczy się zachowanie logowania nowego użytkownika. 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowanie z nieznanych lokalizacji

Ten typ wykrywania ryzyka traktuje w przeszłości lokalizacje logowania (IP, szerokości/długości geograficznej i ASN), aby określić nowe/nieznane lokalizacje. System przechowuje informacje o poprzednich lokalizacjach używanych przez użytkownika i uwzględnia te "znane" lokalizacje. Wykrywanie ryzyka jest wyzwalane, gdy logowanie następuje z lokalizacji, która nie znajduje się na liście znanych lokalizacji. System ma początkowy okres uczenia 30 dni, podczas którego nie flaguje żadnych nowych lokalizacji jako nieznanych lokalizacji. System ignoruje logowania ze znanych urządzeń i lokalizacje, które znajdują się geograficznie blisko znanej lokalizacji. 

Program Identity Protection wykrywa logowania z nieznanych lokalizacji również dla uwierzytelniania podstawowego/starszych protokołów. Ponieważ te protokoły nie mają nowoczesnych znanych funkcji, takich jak identyfikator klienta, nie ma wystarczającej ilości danych telemetrycznych, aby zmniejszyć liczbę fałszywie dodatnich. Aby zmniejszyć liczbę wykrytych wykryć zagrożeń, należy przejść do nowoczesnego uwierzytelniania.   

> [!NOTE]
> Jeśli nazwa użytkownika i hasło logowania nie są zgodne, logowanie zakończy się niepowodzeniem, a wykrywanie ryzyka nie wystąpi. Logowanie się z nieznanych wykrywania zagrożeń w lokalizacji jest wyzwalane tylko w przypadku pomyślnych logowań.

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

Ten typ wykrywania ryzyka identyfikuje logowania z urządzeń zainfekowanych złośliwym oprogramowaniem, które są znane, aby aktywnie komunikować się z serwerem bot. Jest to określane przez skorelowanie adresów IP urządzenia użytkownika z adresami IP, które były w kontakcie z serwerem bot. 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP związanych z podejrzanymi działaniami
Ten typ wykrywania ryzyka identyfikuje adresy IP, z których były widoczne duże liczby nieudanych prób logowania na wielu kontach użytkowników w krótkim czasie. Dopasowuje to wzorce ruchu adresów IP używanych przez osoby atakujące i jest silnym wskaźnikiem, że konta są już lub zostały naruszone. Jest to algorytm uczenia maszynowego, który ignoruje oczywiste fałszywe-pozytywne, takie jak adresy IP, które są regularnie używane przez innych użytkowników w organizacji.  System ma początkowy okres uczenia wynoszący 14 dni, w którym nauczysz zachowanie logowania nowego użytkownika i nowej dzierżawy.

## <a name="detection-type"></a>Typ wykrywania

Właściwość typ wykrywania jest wskaźnikiem (w**czasie rzeczywistym** lub **w trybie offline**) w celu wykrycia czasu wykrywania ryzyka. Obecnie wykryto większość wykrycia ryzyka w trybie offline w operacji po zakończeniu przetwarzania po wykryciu ryzyka.

W poniższej tabeli wymieniono czas, jaki jest potrzebny dla typu wykrywania do wyświetlenia w powiązanym raporcie:

| Typ wykrywania | Raportowanie opóźnienia |
| --- | --- |
| Przesyłanie w czasie rzeczywistym | od 5 do 10 minut |
| W trybie offline | od 2 do 4 godzin |


W przypadku typów wykrywania ryzyka Azure Active Directory wykrywane są typy wykrywania:

| Typ wykrywania ryzyka | Typ wykrywania |
| :-- | --- | 
| [Użytkownicy z ujawnionymi poświadczeniami](#leaked-credentials) | W trybie offline |
| [Logowania z anonimowych adresów IP](#sign-ins-from-anonymous-ip-addresses) | Przesyłanie w czasie rzeczywistym |
| [Niemożliwa podróż do nietypowych lokalizacji](#impossible-travel-to-atypical-locations) | W trybie offline |
| [Logowania z nieznanych lokalizacji](#sign-in-from-unfamiliar-locations) | Przesyłanie w czasie rzeczywistym |
| [Logowania z zainfekowanych urządzeń](#sign-ins-from-infected-devices) | W trybie offline |
| [Logowania z adresów IP związanych z podejrzanymi działaniami](#sign-ins-from-ip-addresses-with-suspicious-activity) | W trybie offline|


## <a name="risk-level"></a>Poziom ryzyka

Właściwość poziomu ryzyka wykrywania ryzyka jest wskaźnikiem (**wysoki**, **Średni**lub **niski**) dla ważności i zaufania wykrywania ryzyka. Ta właściwość pomaga określić priorytety działań, które należy wykonać. 

Ważność wykrywania ryzyka reprezentuje siłę sygnału jako predykcyjny kompromis dotyczący naruszenia tożsamości. Wiarygodność jest wskaźnikiem dla możliwości fałszywych wartości dodatnich. 

Na przykład: 

* **Wysoki**: wysoki poziom zaufania i wysoka ważność. Te zdarzenia są silnymi wskaźnikami, w przypadku których zabezpieczenia tożsamości zostały naruszone, a wszystkie konta użytkowników, których to dotyczy, powinny być natychmiast korygowane.

* **Średni**: wysoka ważność, ale niższe ryzyko związane z zaufaniem lub na odwrót. Zdarzenia te są potencjalnie ryzykowne i należy skorygować wszystkie konta użytkowników, których dotyczy problem.

* **Niski**: wykrywanie ryzyka niskiego zaufania i niskiego poziomu ważności. To zdarzenie może nie wymagać natychmiastowego działania, ale w połączeniu z innymi wykryciami ryzyka może zapewnić silne wskazanie naruszenia bezpieczeństwa tożsamości.

![Poziom ryzyka](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>Nieujawnione poświadczenia

Wykrywanie ryzyka przecieków poświadczeń są klasyfikowane jako **wysokie**, ponieważ zapewniają jasne wskazanie, że nazwa użytkownika i hasło są dostępne dla osoby atakującej.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Logowania z anonimowych adresów IP

Poziom ryzyka dla tego typu wykrywania ryzyka jest **Średni** , ponieważ anonimowy adres IP nie jest silnym wskaźnikiem naruszenia zabezpieczeń konta. Zalecamy natychmiastowe skontaktowanie się z użytkownikiem w celu sprawdzenia, czy korzystali z anonimowych adresów IP.


### <a name="impossible-travel-to-atypical-locations"></a>Niemożliwa podróż do nietypowych lokalizacji

Niemożliwa podróż jest zwykle dobrym wskaźnikiem, że haker mógł pomyślnie się zalogować. Jednak fałszywe-pozytywne mogą wystąpić, gdy użytkownik podróżuje przy użyciu nowego urządzenia lub sieci VPN, które zwykle nie są używane przez innych użytkowników w organizacji. Innym źródłem fałszywych wartości fałszywych są aplikacje, które niepoprawnie przechodzą adresy IP serwera jako adresy IP klienta, co może spowodować pojawienie się miejsca logowania z centrum danych, w którym jest hostowana zaplecze aplikacji (często są to firmy Microsoft datacenters, które mogą spowodować, że podczas logowania pochodzą z adresów IP należących do firmy Microsoft). W wyniku tych fałszywych, poziom ryzyka dla tego wykrywania ryzyka to **Średni**.

> [!TIP]
> Można zmniejszyć liczbę raportowanych fałszywych wartości false-pozytywne dla tego typu wykrywania ryzyka przez skonfigurowanie [nazwanych lokalizacji](../active-directory-named-locations.md). 

### <a name="sign-in-from-unfamiliar-locations"></a>Logowanie z nieznanych lokalizacji

Nieznane lokalizacje mogą zapewnić mocną wskazówkę, że osoba atakująca może korzystać z skradzionej tożsamości. Fałszywe-pozytywne mogą wystąpić, gdy użytkownik podróżuje, próbuje nowe urządzenie lub korzysta z nowej sieci VPN. W wyniku tych fałszywych wartości dodatnich poziom ryzyka dla tego typu zdarzenia to **Średni**.

### <a name="sign-ins-from-infected-devices"></a>Logowania z zainfekowanych urządzeń

To wykrywanie ryzyka identyfikuje adresy IP, a nie urządzenia użytkowników. Jeśli kilka urządzeń znajduje się za pojedynczym adresem IP, a tylko niektóre z nich są kontrolowane przez sieć bot, logowania z innych urządzeń wyzwalają to zdarzenie niekoniecznie, co oznacza, że to wykrywanie ryzyka jest sklasyfikowane jako **niskie**.  

Zalecamy skontaktowanie się z użytkownikiem i przeskanowanie wszystkich urządzeń użytkownika. Istnieje również możliwość, że urządzenie osobiste użytkownika jest zainfekowane lub że ktoś inny używa zainfekowanego urządzenia z tego samego adresu IP, który jest używany przez użytkownika. Zainfekowane urządzenia są często Zainfekowane złośliwym oprogramowaniem, które nie zostało jeszcze zidentyfikowane przez oprogramowanie antywirusowe, i mogą także wskazywać wszelkie złe nawyki użytkowników, które mogą spowodować zainfekowanie urządzenia.

Więcej informacji o sposobie rozwiązywania infekcji złośliwym oprogramowaniem znajduje się w [Centrum ochrony przed złośliwym](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/)oprogramowaniem.

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>Logowania z adresów IP związanych z podejrzanymi działaniami

Zalecamy skontaktowanie się z użytkownikiem w celu sprawdzenia, czy rzeczywiście zalogowano się z adresu IP, który został oznaczony jako podejrzany. Poziomem ryzyka dla tego typu zdarzenia jest "**średnia**", ponieważ niektóre urządzenia mogą znajdować się za tym samym adresem IP, podczas gdy tylko niektóre mogą być odpowiedzialne za podejrzane działania. 


## <a name="next-steps"></a>Następne kroki

* [Raport o zabezpieczeniach użytkowników z ryzykiem](concept-user-at-risk.md)
* [Raport zabezpieczeń dotyczący ryzykownych logowań](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md).
