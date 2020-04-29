---
title: Zaawansowane wykrywanie ataków potokach wieloetapowych na platformie Azure — wskaźnik
description: Skorzystaj z technologii Fusion na platformie Azure wskaźnikowej, aby zmniejszyć zmęczenie alertu i utworzyć zdarzenia umożliwiające podejmowanie działań, które opierają się na zaawansowaniu wykrywania ataków potokach wieloetapowych.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77587927"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Zaawansowane wykrywanie ataków potokach wieloetapowych na platformie Azure — wskaźnik


> [!IMPORTANT]
> Niektóre funkcje Fusion na platformie Azure wskaźnikowej są obecnie dostępne w publicznej wersji zapoznawczej.
> Te funkcje są udostępniane bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Korzystając z technologii Fusion, która jest oparta na uczeniu maszynowym, wskaźnik platformy Azure może automatycznie wykrywać ataki potokach wieloetapowych przez połączenie anomalii zachowań i podejrzanych działań, które są obserwowane na różnych etapach łańcucha kasowania. Na platformie Azure wskaźnik wygeneruje zdarzenia, które w przeciwnym razie byłyby trudne do przechwycenia. Zdarzenia te obejmowaćą co najmniej dwa alerty lub działania. Zgodnie z projektem te zdarzenia to małe ilości, wysoka wierność i wysoka ważność.

Takie rozwiązanie jest dostosowane do danego środowiska, ale nie tylko skraca fałszywe dodatnie stawki, ale mogą również wykrywać ataki z ograniczoną lub brakującymi informacjami.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfiguracja zaawansowanego wieloetapowego wykrywania ataków

To wykrywanie jest domyślnie włączone w wskaźniku na platformie Azure. Aby sprawdzić stan lub wyłączyć go prawdopodobnie z powodu użycia alternatywnego rozwiązania do tworzenia zdarzeń na podstawie wielu alertów, wykonaj następujące instrukcje:

1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do **usługi Azure wskaźnik** > **konfiguracji** > **analizy**

3. Wybierz pozycję **aktywne reguły** i Znajdź **Zaawansowane wykrywanie ataków potokach wieloetapowych** w kolumnie **Nazwa** . Sprawdź kolumnę **stan** , aby sprawdzić, czy to wykrywanie jest włączone czy wyłączone.

4. Aby zmienić stan, wybierz ten wpis i w bloku **Zaawansowane wykrywanie ataków potokach wieloetapowych** wybierz pozycję **Edytuj**.

5. W bloku **Kreator tworzenia reguł** zmiana stanu jest automatycznie wybierana, więc wybierz kolejno pozycje **Dalej: przegląd**i **Zapisz**. 

Szablony reguł nie mają zastosowania w przypadku zaawansowanego wykrywania ataków potokach wieloetapowych.

> [!NOTE]
> Dane historyczne platformy Azure są obecnie wykorzystywane do uczenia systemów uczenia maszynowego w ciągu 30 dni od danych historycznych. Te dane są zawsze szyfrowane przy użyciu kluczy firmy Microsoft, które są przekazywane przez potok uczenia maszynowego. Jednak dane szkoleniowe nie są szyfrowane przy użyciu [kluczy zarządzanych przez klienta (CMK)](customer-managed-keys.md) , jeśli włączono CMK w obszarze roboczym wskaźnikowego platformy Azure. Aby zrezygnować z fuzji, przejdź do obszaru **Azure wskaźnik** \> **konfiguracji** \> ** \> analizy \> ** , a następnie w kolumnie **stan** kliknij pozycję **Wyłącz.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Łączenie przy użyciu sieci Palo Alto i usługi Microsoft Defender ATP

Te scenariusze łączą dwa podstawowe dzienniki używane przez analityków zabezpieczeń: Dzienniki zapory z sieci Palo Alto i dzienników wykrywania punktów końcowych z usługi Microsoft Defender ATP. We wszystkich scenariuszach wymienionych poniżej zostanie wykryte podejrzane działanie w punkcie końcowym, które obejmuje zewnętrzny adres IP, po którym następuje nietypowy ruch z zewnętrznego adresu IP z powrotem do zapory. W Palo Alto dzienników wskaźnik platformy Azure koncentruje się na [dziennikach zagrożeń](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), a ruch jest uznawany za podejrzany, gdy zagrożenia są dozwolone (podejrzane dane, pliki, zalewania, pakiety, skanowania, programy szpiegujące, adresy URL, wirusy, luki, pożarem-wirusy, Wildfires).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Żądanie sieciowe do sieci TOR zachowywanie anonimowości, po którym następuje anomalia ruch oflagowany przez zaporę Palo Alto Networks.

W tym scenariuszu wskaźnik na platformie Azure najpierw wykrywa alert informujący o tym, że usługa Microsoft Defender Advanced Threat Protection wykryła żądanie sieciowe do usługi TOR zachowywanie anonimowości, która prowadzi do nietypowej aktywności. Ta Inicjalizacja została zainicjowana w ramach konta {account name} z IDENTYFIKATORem SID {SID} o godzinie {Time}. Wychodzący adres IP do połączenia: {IndividualIp}.
Następnie nietypowe działanie zostało wykryte przez zaporę Palo Alto Networks pod adresem {TimeGenerated}. Wskazuje to na złośliwy ruch wprowadzony w sieci, docelowy adres IP dla ruchu sieciowego to {DestinationIP}.

Ten scenariusz jest obecnie w publicznej wersji zapoznawczej.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Program PowerShell wykonał podejrzane połączenie sieciowe, a następnie nietypowy ruch oflagowany przez zaporę Palo Alto Networks.

W tym scenariuszu wskaźnik na platformie Azure najpierw wykrywa alert informujący o tym, że usługa Microsoft Defender Advanced Threat Protection wykryła podejrzane połączenie sieciowe prowadzące do nietypowego działania wykrytego przez zaporę sieci Palo Alto. Ta nazwa została zainicjowana przez konto {Account Name} z IDENTYFIKATORem SID {SID} o godzinie {Time}. Wychodzący adres IP do połączenia: {IndividualIp}. Następnie nietypowe działanie zostało wykryte przez zaporę Palo Alto Networks pod adresem {TimeGenerated}. Oznacza to, że złośliwy ruch został wprowadzony w sieci. Docelowy adres IP dla ruchu sieciowego to {DestinationIP}.

Ten scenariusz jest obecnie w publicznej wersji zapoznawczej.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Połączenie wychodzące z adresem IP z historią prób nieautoryzowanego dostępu, po których następuje nietypowy ruch oflagowany przez zaporę Palo Alto Networks

W tym scenariuszu badanie wskaźnikowe platformy Azure wykrywa alert informujący o tym, że usługa Microsoft Defender Advanced Threat Protection wykryła połączenie wychodzące z adresem IP ze historięm nieautoryzowanych prób dostępu, które prowadzą do wykrycia nietypowego działania przez zaporę Palo Alto Networks. Ta nazwa została zainicjowana przez konto {Account Name} z IDENTYFIKATORem SID {SID} o godzinie {Time}. Wychodzący adres IP do połączenia: {IndividualIp}. Po wykonaniu tej czynności nietypowe działanie zostało wykryte przez zaporę Palo Alto Networks pod adresem {TimeGenerated}. Oznacza to, że złośliwy ruch został wprowadzony w sieci. Docelowy adres IP dla ruchu sieciowego to {DestinationIP}.

Ten scenariusz jest obecnie w publicznej wersji zapoznawczej.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Łączenie przy użyciu usługi Identity Protection i Microsoft Cloud App Security

Przy użyciu zaawansowanego wykrywania ataków potokach wieloetapowych usługa Azure — Wskaźnikowanie obsługuje następujące scenariusze, które łączą zdarzenia anomalii z Azure Active Directory Identity Protection i Microsoft Cloud App Security:

- [Niemożliwa podróż do nietypowej lokalizacji, po której następuje nietypowe działanie związane z pakietem Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Działania związane z logowaniem dla nieznanej lokalizacji, po której występuje nietypowe działanie pakietu Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Aktywność logowania z zainfekowanego urządzenia, po którym następuje nietypowe działanie związane z pakietem Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Aktywność logowania z anonimowego adresu IP, po którym następuje nietypowe działanie pakietu Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Działania związane z logowaniem z użytkownika z nieujawnionymi poświadczeniami, po których następuje nietypowe działanie związane z pakietem Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Konieczne jest posiadanie [łącznika danych Azure AD Identity Protection](connect-azure-ad-identity-protection.md) i skonfigurowanych łączników [Cloud App Security](connect-cloud-app-security.md) .

W opisach, które obserwują, wskaźnik na platformie Azure będzie wyświetlał rzeczywistą wartość z danych przedstawionych na tej stronie jako zmienne w nawiasach. Na przykład rzeczywista nazwa wyświetlana konta, a nie \< *nazwa konta*> i rzeczywista liczba, a nie \< *Liczba*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Niemożliwa podróż do nietypowej lokalizacji, po której następuje nietypowe działanie związane z pakietem Office 365

Istnieje siedem możliwych zdarzeń na platformie Azure, które łączą niemożliwa podróż do nietypowych alertów dotyczących lokalizacji z Azure AD Identity Protection i nietypowych alertów pakietu Office 365 generowanych przez Microsoft Cloud App Security:

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do eksfiltracji skrzynek pocztowych pakietu Office 365**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> od niemożliwego przejazdu do \< *lokalizacji*>, nietypowej lokalizacji, po której nastąpi podejrzana reguła przekazywania skrzynki odbiorczej w skrzynce odbiorczej użytkownika.
    
    Może to wskazywać na naruszenie zabezpieczeń konta i użycie skrzynki pocztowej do wyprowadzać informacji z Twojej organizacji. \< *Nazwa konta* użytkownika> utworzyć lub zaktualizować regułę przekazywania skrzynki odbiorczej, która przekazuje wszystkie przychodzące wiadomości e-mail na \< *adres e-mail* adresu zewnętrznego>.

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do podejrzanych działań administracyjnych aplikacji w chmurze**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> od niemożliwego przejazdu do \< *lokalizacji*>, nietypowej lokalizacji.
    
    Następnie \< *nazwa konta* konta> przeprowadzona przez \< *liczbę*> działania administracyjne w ramach jednej sesji.

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do usunięcia pliku masowego**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> do \< *lokalizacji*>, nietypowej lokalizacji. 
    
    Następnie \< *nazwa konta* konta> usunięta \< *Liczba*> unikatowych plików w ramach jednej sesji.

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do pobrania pliku masowego**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> od niemożliwego przejazdu do \< *lokalizacji*>, nietypowej lokalizacji. 
    
    Następnie \< *nazwa konta* konta> pobrana przez \< *liczbę*> unikatowych plików w ramach jednej sesji.

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do personifikacji pakietu Office 365**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> od niemożliwego przejazdu do \< *lokalizacji*>, nietypowej lokalizacji. 
    
    Następnie \< *nazwa konta* konta> wykonywania nietypowej\<*ilości działań personifikacji (>* ) w ramach jednej sesji.

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do masowego udostępniania plików**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> od niemożliwego przejazdu do \< *lokalizacji*>, nietypowej lokalizacji. 
    
    Następnie \< *nazwa konta* konta> współużytkowana przez \< *wiele*> unikatowych plików w ramach jednej sesji.

- **Niemożliwa podróż do nietypowych lokalizacji prowadzących do oprogramowania wymuszającego okup w aplikacji w chmurze**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> od niemożliwego przejazdu do \< *lokalizacji*>, nietypowej lokalizacji. 
    
    Następnie \< *nazwa konta* konta> przekazana \< *Liczba* plików> i usunięto łączną \< *liczbę* plików>. 
    
    Ten wzorzec aktywności jest wskaźnikiem potencjalnego ataku z wykorzystaniem oprogramowania wymuszającego okup.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Działania związane z logowaniem dla nieznanej lokalizacji, po której występuje nietypowe działanie pakietu Office 365

Istnieje siedem możliwych zdarzeń związanych z platformą Azure, które łączą działania związane z logowaniem w przypadku nieznanych alertów dotyczących lokalizacji z Azure AD Identity Protection i nietypowych alertów pakietu Office 365 generowanych przez Microsoft Cloud App Security.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do usługi Exchange Online Skrzynka pocztowa eksfiltracji**
    
    Ten alert to wskazanie zdarzenia logowania według \< *nazwy konta*> z \< *lokalizacji*>, nieznanej lokalizacji, a po niej została ustawiona podejrzana reguła przekazywania skrzynki odbiorczej w skrzynce odbiorczej użytkownika.
    
    Może to wskazywać na naruszenie zabezpieczeń konta i użycie skrzynki pocztowej do wyprowadzać informacji z Twojej organizacji. \< *Nazwa konta* użytkownika> utworzyć lub zaktualizować regułę przekazywania skrzynki odbiorczej, która przekazuje wszystkie przychodzące wiadomości e-mail na \< *adres e-mail* adresu zewnętrznego>. 

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do podejrzanych działań administracyjnych aplikacji w chmurze**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *lokalizacji*>, nieznanej lokalizacji. 
    
    Następnie \< *nazwa konta* konta> wykonana \< *na> działania administracyjne w ramach jednej* sesji.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do usunięcia pliku masowego**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *lokalizacji*>, nieznanej lokalizacji. 
    
    Następnie \< *nazwa konta* konta> usunięta \< *Liczba*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do pobrania pliku masowego**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *lokalizacji*>, nieznanej lokalizacji. 
    
    Następnie \< *nazwa konta* konta> pobrana przez \< *liczbę*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do personifikacji pakietu Office 365**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *lokalizacji*>, nieznanej lokalizacji.
    
    Następnie \< *nazwa konta* konta> personifikowana przez \< *liczbę*> różnych kont w ramach jednej sesji.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do masowego udostępniania plików**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *lokalizacji*>, nieznanej lokalizacji. 
    
    Następnie \< *nazwa konta* konta> współużytkowana przez \< *wiele*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z nieznanej lokalizacji prowadzącej do oprogramowania wymuszającego okup w aplikacji w chmurze**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *lokalizacji*>, nieznanej lokalizacji. 
    
    Następnie \< *nazwa konta* konta> przekazana \< *Liczba* plików> i usunięto łączną \< *liczbę* plików>. 
    
    Ten wzorzec aktywności jest wskaźnikiem potencjalnego ataku z wykorzystaniem oprogramowania wymuszającego okup.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Aktywność logowania z zainfekowanego urządzenia, po którym następuje nietypowe działanie związane z pakietem Office 365

Istnieje siedem możliwych zdarzeń związanych z systemem Azure, które łączą aktywność logowania z poziomu alertów z zainfekowanych urządzeń z Azure AD Identity Protection i nietypowych alertów Office 365 generowanych przez Microsoft Cloud App Security:

- **Zdarzenie logowania z zainfekowanego urządzenia wiodącego w skrzynce pocztowej usługi Office 365 eksfiltracji**
    
    Ten alert to wskazanie zdarzenia logowania według \< *nazwy konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem, po czym została ustawiona podejrzana reguła przekazywania skrzynki odbiorczej w skrzynce odbiorczej użytkownika.
    
    Może to wskazywać na naruszenie zabezpieczeń konta i użycie skrzynki pocztowej do wyprowadzać informacji z Twojej organizacji. \< *Nazwa konta* użytkownika> utworzyć lub zaktualizować regułę przekazywania skrzynki odbiorczej, która przekazuje wszystkie przychodzące wiadomości e-mail na \< *adres e-mail* adresu zewnętrznego>. 

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do podejrzanych działań administracyjnych aplikacji w chmurze**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem.
    
    Następnie \< *nazwa konta* konta> wykonana \< *na> działania administracyjne w ramach jednej* sesji.

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do usunięcia pliku masowego**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem. 
    
    Następnie \< *nazwa konta* konta> usunięta \< *Liczba*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z zainfekowanego urządzenia, które prowadzi do pobrania pliku masowego**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem. 
    
    Następnie \< *nazwa konta* konta> pobrana przez \< *liczbę*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do personifikacji pakietu Office 365**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem. 
    
    Następnie \< *nazwa konta* konta> personifikowana przez \< *liczbę*> różnych kont w ramach jednej sesji.

- **Zdarzenie logowania z zainfekowanego urządzenia, które prowadzi do masowego udostępniania plików**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem. 
    
    Następnie \< *nazwa konta* konta> współużytkowana przez \< *wiele*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z zainfekowanego urządzenia prowadzącego do oprogramowania wymuszającego okup w aplikacji w chmurze**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z urządzenia potencjalnie zainfekowanego złośliwym oprogramowaniem. 
    
    Następnie \< *nazwa konta* konta> przekazana \< *Liczba* plików> i usunięto łączną \< *liczbę* plików>. 
    
    Ten wzorzec aktywności jest wskaźnikiem potencjalnego ataku z wykorzystaniem oprogramowania wymuszającego okup.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Aktywność logowania z anonimowego adresu IP, po którym następuje nietypowe działanie pakietu Office 365

Istnieje siedem możliwych zdarzeń związanych z systemem Azure, które łączą aktywność logowania z alertów anonimowych adresów IP z Azure AD Identity Protection i anomalii alertów pakietu Office 365 generowanych przez Microsoft Cloud App Security:

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do skrzynki pocztowej pakietu Office 365 eksfiltracji**
    
    Ten alert to wskazanie zdarzenia logowania według \< *nazwy konta*> z \< *adresu IP* anonimowego adresu IP serwera proxy>, po którym ustawiono podejrzaną regułę przekazywania skrzynki odbiorczej w skrzynce odbiorczej użytkownika.
    
    Może to wskazywać na naruszenie zabezpieczeń konta i użycie skrzynki pocztowej do wyprowadzać informacji z Twojej organizacji. \< *Nazwa konta* użytkownika> utworzyć lub zaktualizować regułę przekazywania skrzynki odbiorczej, która przekazuje wszystkie przychodzące wiadomości e-mail na \< *adres e-mail* adresu zewnętrznego>. 

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do podejrzanych działań administracyjnych aplikacji w chmurze**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *adresu IP* anonimowego adresu IP serwera proxy>. 
    
    Następnie \< *nazwa konta* konta> wykonana \< *na> działania administracyjne w ramach jednej* sesji.

- **Zdarzenie logowania z anonimowego adresu IP prowadzące do usunięcia pliku masowego**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *adresu IP* anonimowego adresu IP serwera proxy>. 
    
    Następnie \< *nazwa konta* konta> usunięta \< *Liczba*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z anonimowego adresu IP prowadzące do pobierania pliku masowego**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *adresu IP* anonimowego adresu IP serwera proxy>. 
    
    Następnie \< *nazwa konta* konta> pobrana przez \< *liczbę*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z anonimowego adresu IP prowadzącego do personifikacji pakietu Office 365**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *adresu IP* anonimowego adresu IP serwera proxy>. 
    
    Następnie \< *nazwa konta* konta> personifikowana przez \< *liczbę*> różnych kont w ramach jednej sesji.

- **Zdarzenie logowania z anonimowego adresu IP, które prowadzi do masowego udostępniania plików**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *adresu IP* anonimowego adresu IP serwera proxy>. 
    
    Następnie \< *nazwa konta* konta> współużytkowana przez \< *wiele*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z anonimowego adresu IP do oprogramowania wymuszającego okup w aplikacji w chmurze**
    
    Ten alert to oznaczenie zdarzenia logowania według \< *nazwy konta*> z \< *adresu IP* anonimowego adresu IP serwera proxy>. 
    
    Następnie \< *nazwa konta* konta> przekazana \< *Liczba* plików> i usunięto łączną \< *liczbę* plików>. 
    
    Ten wzorzec aktywności jest wskaźnikiem potencjalnego ataku z wykorzystaniem oprogramowania wymuszającego okup.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Działania związane z logowaniem z użytkownika z nieujawnionymi poświadczeniami, po których następuje nietypowe działanie związane z pakietem Office 365

Dostępne są siedem zdarzeń wskaźnikowych platformy Azure, które łączą działania związane z logowaniem z użytkownika z alertami dotyczącymi nieujawnionych poświadczeń z Azure AD Identity Protection i anomalii alertów Office 365 generowanych przez Microsoft Cloud App Security:

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do eksfiltracji skrzynek pocztowych pakietu Office 365**
    
    Ten alert wskazuje na to, że zdarzenie logowania według \< *nazwy konta*> używało przecieków poświadczeń, a następnie ustawiono podejrzaną regułę przekazywania skrzynki odbiorczej w skrzynce odbiorczej użytkownika. 
    
    Może to wskazywać na naruszenie zabezpieczeń konta i użycie skrzynki pocztowej do wyprowadzać informacji z Twojej organizacji. \< *Nazwa konta* użytkownika> utworzyć lub zaktualizować regułę przekazywania skrzynki odbiorczej, która przekazuje wszystkie przychodzące wiadomości e-mail na \< *adres e-mail* adresu zewnętrznego>. 

- **Zdarzenie logowania od użytkownika z nieujawnionymi poświadczeniami prowadzącymi do podejrzanych działań administracyjnych aplikacji w chmurze**
    
    Ten alert wskazuje na to, że zdarzenie logowania według \< *nazwy konta*> używało przecieków poświadczeń.
    
    Następnie \< *nazwa konta* konta> wykonana \< *na> działania administracyjne w ramach jednej* sesji.

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do usunięcia pliku masowego**
    
    Ten alert wskazuje na to, że zdarzenie logowania według \< *nazwy konta*> używało przecieków poświadczeń.
    
    Następnie \< *nazwa konta* konta> usunięta \< *Liczba*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do pobierania pliku masowego**
    
    Ten alert wskazuje na to, że zdarzenie logowania według \< *nazwy konta*> używało przecieków poświadczeń.
    
    Następnie \< *nazwa konta* konta> pobrana przez \< *liczbę*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania z użytkownika z nieujawnionymi poświadczeniami prowadzącymi do personifikacji pakietu Office 365**
    
    Ten alert wskazuje na to, że zdarzenie logowania według \< *nazwy konta*> używało przecieków poświadczeń. 
    
    Następnie \< *nazwa konta* konta> personifikowana przez \< *liczbę*> różnych kont w ramach jednej sesji.

- **Zdarzenie logowania od użytkownika z nieujawnionymi poświadczeniami prowadzącymi do masowego udostępniania plików**
    
    Ten alert wskazuje na to, że zdarzenie logowania według \< *nazwy konta*> używało przecieków poświadczeń.
    
    Następnie \< *nazwa konta* konta> współużytkowana przez \< *wiele*> unikatowych plików w ramach jednej sesji.

- **Zdarzenie logowania użytkownika z nieujawnionymi poświadczeniami do oprogramowania wymuszającego okup w aplikacji w chmurze**
    
    Ten alert wskazuje na to, że zdarzenie logowania według \< *nazwy konta*> używało przecieków poświadczeń. 
    
    Następnie \< *nazwa konta* konta> przekazana \< *Liczba* plików> i usunięto łączną \< *liczbę* plików>. 
    
    Ten wzorzec aktywności jest wskaźnikiem potencjalnego ataku z wykorzystaniem oprogramowania wymuszającego okup.

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się więcej na temat zaawansowanego wykrywania ataków potokach wieloetapowych. możesz zainteresować się następującymi przewodnikami Szybki Start, aby dowiedzieć się, jak uzyskać wgląd w dane i potencjalne zagrożenia: Rozpoczynanie [pracy z platformą Azure](quickstart-get-visibility.md).

Jeśli wszystko jest gotowe do zbadania zdarzeń utworzonych dla Ciebie, zobacz następujący samouczek: [Zbadaj zdarzenia za pomocą platformy Azure](tutorial-investigate-cases.md).

