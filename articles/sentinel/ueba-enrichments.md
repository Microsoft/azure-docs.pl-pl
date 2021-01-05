---
title: Informacje o wzbogacaniu UEBA platformy Azure Microsoft Docs
description: W tym artykule przedstawiono wzbogacenia jednostek wygenerowane przez analizę zachowań jednostek na platformie Azure.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/04/2021
ms.author: yelevin
ms.openlocfilehash: daba8fc1f645b51dc8668c806be63744b6ae0842
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901789"
---
# <a name="azure-sentinel-ueba-enrichments-reference"></a>Dokumentacja wzbogaceń UEBA platformy Azure

Te tabele zawierają listę i opisują wzbogacania jednostek, których można użyć do skoncentrowania się i wyostrzenia badania zdarzeń zabezpieczeń.

Pierwsze dwie tabele, szczegółowe informacje o **użytkowniku** i usługa **Device** Insights zawierają dane dotyczące jednostek z usług Active Directory i Azure AD oraz źródeł analizy zagrożeń firmy Microsoft.

<a name="baseline-explained"></a>Pozostałe tabele w obszarze **tabele usługi Activity Insights** zawierają informacje o jednostkach oparte na profilach zachowań utworzonych w ramach analizy zachowań jednostek na platformie Azure. Działania są analizowane względem planu bazowego, który jest dynamicznie kompilowany za każdym razem, gdy jest używany. Każde działanie ma zdefiniowany lookbacky okres, z którego pochodzi Dynamiczna linia bazowa. Ten okres jest określony w kolumnie [**linia bazowa**](#activity-insights-tables) w tej tabeli.

> [!NOTE] 
> Pole **nazwy wzbogacania** we wszystkich trzech tabelach wyświetla dwa wiersze informacji. Pierwszy, **pogrubiony**, jest "przyjazną nazwą" wzbogacania. Sekunda *(w kursywie i nawiasach)* jest nazwą pola wzbogacania, które jest przechowywane w [**tabeli analizy zachowań**](identify-threats-with-entity-behavior-analytics.md#data-schema).

## <a name="user-insights-table"></a>Tabela szczegółowych informacji o użytkowniku

| Nazwa wzbogacania | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Nazwa wyświetlana konta**<br>*(AccountDisplayName)* | Nazwa wyświetlana konta użytkownika. | Administrator, Hayden Cooka |
| **Domena konta**<br>*(AccountDomain)* | Nazwa domeny konta użytkownika. |  |
| **Identyfikator obiektu konta**<br>*(AccountObjectID)* | Identyfikator obiektu konta użytkownika. | a58df659-5cab-446c-9dd0-5a3af20ce1c2 |
| **Wielkie promień**<br>*(BlastRadius)* | Promień jest obliczany na podstawie kilku czynników: pozycji użytkownika w drzewie organizacji oraz Azure Active Directory ról i uprawnień użytkownika. | Niski, średni, wysoki |
| **Jest kontem uśpionym**<br>*(IsDormantAccount)* | Konto nie zostało użyte w ciągu ostatnich 180 dni. | Prawda, FAŁSZ |
| **Jest administratorem lokalnym**<br>*(IsLocalAdmin)* | Konto ma uprawnienia administratora lokalnego. | Prawda, FAŁSZ |
| **Jest nowym kontem**<br>*(IsNewAccount)* | Konto zostało utworzone w ciągu ostatnich 30 dni. | Prawda, FAŁSZ |
| **Lokalny identyfikator SID**<br>*(OnPremisesSID)* | Lokalny identyfikator SID użytkownika związany z akcją. | S-1-5-21-1112946627-1321165628-2437342228-1103 |
|

## <a name="device-insights-table"></a>Tabela szczegółowych informacji o urządzeniu

| Nazwa wzbogacania | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Przeglądarka**<br>*Browser* | Przeglądarka użyta w akcji. | Edge, Chrome |
| **Rodzina urządzeń**<br>*(DeviceFamily)* | Rodzina urządzeń używana w akcji. | Windows |
| **Typ urządzenia**<br>*Elementu DeviceType* | Typ urządzenia klienckiego używany w akcji | Klasyczna |
| **INTERNETOWE**<br>*INTERNETOWE* | Usługodawca internetowy używany w akcji. |  |
| **System operacyjny**<br>*OperatingSystem* | System operacyjny używany w akcji. | Windows 10 |
| **Opis wskaźnika zagrożenia firmy Intel**<br>*(ThreatIntelIndicatorDescription)* | Opis pokrytych wskaźników zagrożeń rozpoznanych na podstawie adresu IP użytego w akcji. | Host należy do botnet: azorult |
| **Typ wskaźnika zagrożenia firmy Intel**<br>*(ThreatIntelIndicatorType)* | Typ wskaźnika zagrożenia rozpoznanego na podstawie adresu IP użytego w akcji. | Botnet, C2, CryptoMining, sieci darknet, DDoS, MaliciousUrl, złośliwe oprogramowanie, phishing, proxy, OPIERAJĄCA, listy do obejrzenia |
| **Agent użytkownika**<br>*UserAgent* | Agent użytkownika używany w akcji. | Biblioteka klienta Microsoft Azure Graph 1,0,<br>Swagger — codegen/1.4.0.0/CSharp,<br>EvoSTS |
| **Rodzina agentów użytkownika**<br>*(UserAgentFamily)* | Rodzina agentów użytkownika użyta w akcji. | Chrome, Edge, Firefox |
|

## <a name="activity-insights-tables"></a>Tabele szczegółowe informacje o działaniu

#### <a name="action-performed"></a>Wykonano akcję

| Nazwa wzbogacania | [Linia bazowa](#baseline-explained) (dni) | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Akcja wykonywana przez użytkownika po raz pierwszy**<br>*(FirstTimeUserPerformedAction)* | 180 | Akcja została wykonana po raz pierwszy przez użytkownika. | Prawda, FAŁSZ |
| **Nietypowe działanie wykonywane przez użytkownika**<br>*(ActionUncommonlyPerformedByUser)* | 10 | Akcja nie jest zazwyczaj wykonywana przez użytkownika. | Prawda, FAŁSZ |
| **Nietypowe działanie między elementami równorzędnymi**<br>*(ActionUncommonlyPerformedAmongPeers)* | 180 | Akcja nie jest często wykonywana między elementami równorzędnymi użytkownika. | Prawda, FAŁSZ |
| **Akcja po raz pierwszy wykonywana w dzierżawie**<br>*(FirstTimeActionPerformedInTenant)* | 180 | Akcja została wykonana po raz pierwszy przez dowolną osobę w organizacji. | Prawda, FAŁSZ |
| **Akcja niewspólna wykonywana w dzierżawie**<br>*(ActionUncommonlyPerformedInTenant)* | 180 | Akcja nie jest często wykonywana w organizacji. | Prawda, FAŁSZ |
|

#### <a name="app-used"></a>Użyta aplikacja

| Nazwa wzbogacania | [Linia bazowa](#baseline-explained) (dni) | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Aplikacja używana po raz pierwszy**<br>*(FirstTimeUserUsedApp)* | 180 | Aplikacja została po raz pierwszy użyta przez użytkownika. | Prawda, FAŁSZ |
| **Aplikacja niepospolitie używana przez użytkownika**<br>*(AppUncommonlyUsedByUser)* | 10 | Aplikacja nie jest często używana przez użytkownika. | Prawda, FAŁSZ |
| **Aplikacja nieczęsto używana między elementami równorzędnymi**<br>*(AppUncommonlyUsedAmongPeers)* | 180 | Aplikacja nie jest często używana między elementami równorzędnymi użytkownika. | Prawda, FAŁSZ |
| **Aplikacja po raz pierwszy obserwowana w dzierżawie**<br>*(FirstTimeAppObservedInTenant)* | 180 | Aplikacja została zaobserwowana po raz pierwszy w organizacji. | Prawda, FAŁSZ |
| **Aplikacja niewspólna używana w dzierżawie**<br>*(AppUncommonlyUsedInTenant)* | 180 | Aplikacja nie jest często używana w organizacji. | Prawda, FAŁSZ |
| 

#### <a name="browser-used"></a>Użyta przeglądarka

| Nazwa wzbogacania | [Linia bazowa](#baseline-explained) (dni) | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Użytkownik po raz pierwszy połączony za pośrednictwem przeglądarki**<br>*(FirstTimeUserConnectedViaBrowser)* | 30 | Przeglądarka została zaobserwowana po raz pierwszy przez użytkownika. | Prawda, FAŁSZ |
| **Nietypowe użycie przeglądarki przez użytkownika**<br>*(BrowserUncommonlyUsedByUser)* | 10 | Przeglądarka nie jest często używana przez użytkownika. | Prawda, FAŁSZ |
| **Nietypowe użycie przeglądarki między elementami równorzędnymi**<br>*(BrowserUncommonlyUsedAmongPeers)* | 30 | Przeglądarka nie jest często używana między elementami równorzędnymi użytkownika. | Prawda, FAŁSZ |
| **Przeglądarka po raz pierwszy obserwowana w dzierżawie**<br>*(FirstTimeBrowserObservedInTenant)* | 30 | Przeglądarka została zaobserwowana po raz pierwszy w organizacji. | Prawda, FAŁSZ |
| **Nietypowe użycie przeglądarki w dzierżawie**<br>*(BrowserUncommonlyUsedInTenant)* | 30 | Przeglądarka nie jest często używana w organizacji. | Prawda, FAŁSZ |
| 

#### <a name="country-connected-from"></a>Kraj połączony z

| Nazwa wzbogacania | [Linia bazowa](#baseline-explained) (dni) | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Użytkownik po raz pierwszy nawiązał połączenie z kraju**<br>*(FirstTimeUserConnectedFromCountry)* | 90 | Lokalizacja geograficzna, jak to zostało rozwiązane z adresu IP, została połączona po raz pierwszy przez użytkownika. | Prawda, FAŁSZ |
| **Kraj niewspólnie połączony przez użytkownika**<br>*(CountryUncommonlyConnectedFromByUser)* | 10 | Lokalizacja geograficzna, zgodna z adresem IP, nie jest często połączona przez użytkownika. | Prawda, FAŁSZ |
| **Kraj niewspólnie połączony między elementami równorzędnymi**<br>*(CountryUncommonlyConnectedFromAmongPeers)* | 90 | Lokalizacja geograficzna, zgodna z adresem IP, nie jest często połączona z elementów równorzędnych użytkownika. | Prawda, FAŁSZ |
| **Pierwsze połączenie z kraju obserwowane w dzierżawie**<br>*(FirstTimeConnectionFromCountryObservedInTenant)* | 90 | Kraj został połączony po raz pierwszy przez dowolną osobę w organizacji. | Prawda, FAŁSZ |
| **Kraj niewspólnie połączony z dzierżawy**<br>*(CountryUncommonlyConnectedFromInTenant)* | 90 | Lokalizacja geograficzna, zgodna z adresem IP, nie jest często połączona z w organizacji. | Prawda, FAŁSZ |
| 

#### <a name="device-used-to-connect"></a>Urządzenie używane do nawiązania połączenia

| Nazwa wzbogacania | [Linia bazowa](#baseline-explained) (dni) | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Użytkownik po raz pierwszy połączony z urządzeniem**<br>*(FirstTimeUserConnectedFromDevice)* | 30 | Urządzenie źródłowe zostało połączone po raz pierwszy przez użytkownika. | Prawda, FAŁSZ |
| **Urządzenie używane niewspólnie przez użytkownika**<br>*(DeviceUncommonlyUsedByUser)* | 10 | Urządzenie nie jest często używane przez użytkownika. | Prawda, FAŁSZ |
| **Urządzenie niewspólnie używane między elementami równorzędnymi**<br>*(DeviceUncommonlyUsedAmongPeers)* | 180 | Urządzenie nie jest często używane między elementami równorzędnymi użytkownika. | Prawda, FAŁSZ |
| **Pierwsze urządzenie obserwowane w dzierżawie**<br>*(FirstTimeDeviceObservedInTenant)* | 30 | Urządzenie zostało zaobserwowane po raz pierwszy w organizacji. | Prawda, FAŁSZ |
| **Urządzenie używane niewspólnie w dzierżawie**<br>*(DeviceUncommonlyUsedInTenant)* | 180 | Urządzenie nie jest często używane w organizacji. | Prawda, FAŁSZ |
| 

#### <a name="other-device-related"></a>Inne powiązane z urządzeniem

| Nazwa wzbogacania | [Linia bazowa](#baseline-explained) (dni) | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Użytkownik po raz pierwszy zalogowany na urządzeniu**<br>*(FirstTimeUserLoggedOnToDevice)* | 180 | Urządzenie docelowe zostało połączone po raz pierwszy przez użytkownika. | Prawda, FAŁSZ |
| **Rodzina urządzeń nie została użyta w dzierżawie**<br>*(DeviceFamilyUncommonlyUsedInTenant)* | 30 | Rodzina urządzeń nie jest często używana w organizacji. | Prawda, FAŁSZ |
| 

#### <a name="internet-service-provider-used-to-connect"></a>Usługodawca internetowy używany do nawiązywania połączeń

| Nazwa wzbogacania | [Linia bazowa](#baseline-explained) (dni) | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Użytkownik po raz pierwszy połączony za pośrednictwem usługodawcy internetowego**<br>*(FirstTimeUserConnectedViaISP)* | 30 | Usługodawca internetowy był po raz pierwszy zaobserwowany przez użytkownika. | Prawda, FAŁSZ |
| **Usługodawca internetowy nieczęsto używany przez użytkownika**<br>*(ISPUncommonlyUsedByUser)* | 10 | Usługodawca internetowy nie jest często używany przez użytkownika. | Prawda, FAŁSZ |
| **Usługodawca internetowy nieczęsto używany między elementami równorzędnymi**<br>*(ISPUncommonlyUsedAmongPeers)* | 30 | Usługodawca internetowy nie jest często używany między elementami równorzędnymi użytkownika. | Prawda, FAŁSZ |
| **Pierwsze połączenie za pośrednictwem usługodawcy internetowego w dzierżawie**<br>*(FirstTimeConnectionViaISPInTenant)* | 30 | Usługodawca internetowy był zaobserwowany po raz pierwszy w organizacji. | Prawda, FAŁSZ |
| **Usługodawca internetowy nietypowego użycia w dzierżawie**<br>*(ISPUncommonlyUsedInTenant)* | 30 | Usługodawca internetowy nie jest często używany w organizacji. | Prawda, FAŁSZ |
| 

#### <a name="resource-accessed"></a>Dostęp do zasobów

| Nazwa wzbogacania | [Linia bazowa](#baseline-explained) (dni) | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Zasób po raz pierwszy uzyskuje dostęp do użytkownika**<br>*(FirstTimeUserAccessedResource)* | 180 | Użytkownik uzyskał dostęp do zasobu po raz pierwszy przez użytkownika. | Prawda, FAŁSZ |
| **Zasób nietypowy do użycia przez użytkownika**<br>*(ResourceUncommonlyAccessedByUser)* | 10 | Zasób nie jest zazwyczaj używany przez użytkownika. | Prawda, FAŁSZ |
| **Zasób nietypowego dostępu między elementami równorzędnymi**<br>*(ResourceUncommonlyAccessedAmongPeers)* | 180 | Zasób nie jest często używany między elementami równorzędnymi użytkownika. | Prawda, FAŁSZ |
| **Pierwszy raz dostęp do zasobu w dzierżawie**<br>*(FirstTimeResourceAccessedInTenant)* | 180 | Dostęp do zasobu uzyskano po raz pierwszy przez dowolną osobę w organizacji. | Prawda, FAŁSZ |
| **Zasób nietypowy do użycia w dzierżawie**<br>*(ResourceUncommonlyAccessedInTenant)* | 180 | Zasób nie jest zazwyczaj używany w organizacji. | Prawda, FAŁSZ |
| 

#### <a name="miscellaneous"></a>Różne

| Nazwa wzbogacania | [Linia bazowa](#baseline-explained) (dni) | Opis | Wartość przykładowa |
| --- | --- | --- | --- |
| **Akcja podczas ostatniego wykonywania użytkownika**<br>*(LastTimeUserPerformedAction)* | 180 | Czas ostatniego wykonania tej czynności przez użytkownika. | <Timestamp> |
| **Podobna akcja nie została wykonana w przeszłości**<br>*(SimilarActionWasn'tPerformedInThePast)* | 30 | Użytkownik nie wykonał żadnej akcji w tym samym dostawcy zasobów. | Prawda, FAŁSZ |
| **Źródłowa lokalizacja adresu IP**<br>*(SourceIPLocation)* | *Nie dotyczy* | Kraj został rozpoznany ze źródłowego adresu IP akcji. | [Surrey, Anglia] |
| **Nietypowe duże ilości operacji**<br>*(UncommonHighVolumeOfOperations)* | 7 | Użytkownik wykonał szereg podobnych operacji w ramach tego samego dostawcy. | Prawda, FAŁSZ |
| **Nietypowa liczba błędów dostępu warunkowego usługi Azure AD**<br>*(UnusualNumberOfAADConditionalAccessFailures)* | 5 | Nie można uwierzytelnić nietypowej liczby użytkowników ze względu na dostęp warunkowy | Prawda, FAŁSZ |
| **Dodaliśmy nietypową liczbę urządzeń**<br>*(UnusualNumberOfDevicesAdded)* | 5 | Użytkownik dodał nietypową liczbę urządzeń. | Prawda, FAŁSZ |
| **Nietypowa Liczba usuniętych urządzeń**<br>*(UnusualNumberOfDevicesDeleted)* | 5 | Użytkownik usunął nietypową liczbę urządzeń. | Prawda, FAŁSZ |
| **Nietypowa liczba użytkowników dodanych do grupy**<br>*(UnusualNumberOfUsersAddedToGroup)* | 5 | Użytkownik dodał do grupy nietypową liczbę użytkowników. | Prawda, FAŁSZ |
|