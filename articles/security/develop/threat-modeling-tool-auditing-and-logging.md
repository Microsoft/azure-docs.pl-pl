---
title: Inspekcja i rejestrowanie — Microsoft Threat Modeling Tool — Azure | Microsoft Docs
description: Dowiedz się więcej na temat ograniczenia inspekcji i rejestrowania w Threat Modeling Tool. Zobacz informacje o ograniczeniach i Wyświetl przykłady kodu.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 9d3f3ca7b5d4516c2ad5dc9cb19a2eaed0a8a4a8
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518281"
---
# <a name="security-frame-auditing-and-logging--mitigations"></a>Ramka zabezpieczeń: Inspekcja i rejestrowanie | Środki zaradcze 

| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Dynamics CRM**    | <ul><li>[Zidentyfikuj poufne jednostki w rozwiązaniu i zaimplementuj inspekcję zmian](#sensitive-entities)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Upewnij się, że Inspekcja i rejestrowanie są wymuszane w aplikacji](#auditing)</li><li>[Upewnij się, że rotacja i separacja dzienników są stosowane](#log-rotation)</li><li>[Upewnij się, że aplikacja nie rejestruje poufnych danych użytkownika](#log-sensitive-data)</li><li>[Upewnij się, że pliki inspekcji i dziennika mają ograniczony dostęp](#log-restricted-access)</li><li>[Upewnij się, że zdarzenia zarządzania użytkownikami są rejestrowane](#user-management)</li><li>[Upewnij się, że system ma wbudowane zabezpieczenia przed nieprawidłowym użyciem](#inbuilt-defenses)</li><li>[Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w programie Azure App Service](#diagnostics-logging)</li></ul> |
| **Database** (Baza danych) | <ul><li>[Upewnij się, że inspekcja logowania jest włączona na SQL Server](#identify-sensitive-entities)</li><li>[Włączanie wykrywania zagrożeń w usłudze Azure SQL](#threat-detection)</li></ul> |
| **Azure Storage** | <ul><li>[Używanie analityka magazynu platformy Azure do inspekcji dostępu do usługi Azure Storage](#analytics)</li></ul> |
| **WCF** | <ul><li>[Implementowanie wystarczającego rejestrowania](#sufficient-logging)</li><li>[Implementowanie wystarczającej obsługi błędów inspekcji](#audit-failure-handling)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że Inspekcja i rejestrowanie są wymuszane w interfejsie API sieci Web](#logging-web-api)</li></ul> |
| **Brama pola IoT** | <ul><li>[Upewnij się, że w bramie pola jest wymuszane odpowiednie inspekcje i rejestrowanie](#logging-field-gateway)</li></ul> |
| **Brama usługi IoT Cloud** | <ul><li>[Upewnij się, że w bramie w chmurze jest wymuszane odpowiednie inspekcje i rejestrowanie](#logging-cloud-gateway)</li></ul> |

## <a name="identify-sensitive-entities-in-your-solution-and-implement-change-auditing"></a><a id="sensitive-entities"></a>Zidentyfikuj poufne jednostki w rozwiązaniu i zaimplementuj inspekcję zmian

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | Zidentyfikuj jednostki w rozwiązaniu zawierającym dane poufne i zaimplementuj inspekcję zmian w tych jednostkach i polach |

## <a name="ensure-that-auditing-and-logging-is-enforced-on-the-application"></a><a id="auditing"></a>Upewnij się, że Inspekcja i rejestrowanie są wymuszane w aplikacji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | Włącz inspekcję i rejestrowanie na wszystkich składnikach. Dzienniki inspekcji powinny przechwycić kontekst użytkownika. Zidentyfikuj wszystkie ważne zdarzenia i Rejestruj te zdarzenia. Implementowanie scentralizowanego rejestrowania |

## <a name="ensure-that-log-rotation-and-separation-are-in-place"></a><a id="log-rotation"></a>Upewnij się, że rotacja i separacja dzienników są stosowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | <p>Obrót dziennika jest zautomatyzowanym procesem używanym w administracji systemu, w którym są archiwizowane datowane pliki dziennika. Serwery, na których uruchamiane są duże aplikacje, często rejestrują każde żądanie: w przypadku dzienników zbiorczych, rotacja dzienników jest sposobem ograniczenia całkowitego rozmiaru dzienników przy jednoczesnym umożliwieniu analizy ostatnich zdarzeń. </p><p>Separacja dzienników oznacza, że musisz przechowywać pliki dziennika na innej partycji, w której działa system operacyjny/aplikacja, aby uniknąć ataku typu "odmowa usługi" lub zmiany jego wydajności na starszą wersję.</p>|

## <a name="ensure-that-the-application-does-not-log-sensitive-user-data"></a><a id="log-sensitive-data"></a>Upewnij się, że aplikacja nie rejestruje poufnych danych użytkownika

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | <p>Sprawdź, czy nie rejestrujesz poufnych danych przesyłanych przez użytkownika do witryny. Sprawdzaj zamierzone rejestrowanie, a także efekty uboczne spowodowane problemami z projektowaniem. Przykładowe dane poufne to:</p><ul><li>Poświadczenia użytkownika</li><li>Numer PESEL lub inne informacje identyfikujące</li><li>Numery kart kredytowych lub inne informacje finansowe</li><li>Informacje o kondycji</li><li>Klucze prywatne lub inne dane, których można użyć do odszyfrowania zaszyfrowanych informacji</li><li>Informacje o systemie lub aplikacji, których można użyć do bardziej efektywnego ataku na aplikację</li></ul>|

## <a name="ensure-that-audit-and-log-files-have-restricted-access"></a><a id="log-restricted-access"></a>Upewnij się, że pliki inspekcji i dziennika mają ograniczony dostęp

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | <p>Upewnij się, że prawa dostępu do plików dziennika są odpowiednio ustawione. Konta aplikacji powinny mieć dostęp tylko do zapisu, a operatorzy i personel pomocy technicznej powinni mieć dostęp tylko do odczytu, jeśli jest to konieczne.</p><p>Kontami administratorów są jedyne konta, które powinny mieć pełny dostęp. Sprawdź listę ACL systemu Windows w plikach dziennika, aby upewnić się, że są one prawidłowo ograniczone:</p><ul><li>Konta aplikacji powinny mieć dostęp tylko do zapisu</li><li>Operatorzy i personel pomocy technicznej powinni mieć dostęp tylko do odczytu w razie konieczności</li><li>Administratorzy są jedynymi kontami, które powinny mieć pełny dostęp</li></ul>|

## <a name="ensure-that-user-management-events-are-logged"></a><a id="user-management"></a>Upewnij się, że zdarzenia zarządzania użytkownikami są rejestrowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | <p>Upewnij się, że aplikacja monitoruje zdarzenia zarządzania użytkownikami, takie jak pomyślne i nieudane logowania użytkowników, resetowanie haseł, zmiany haseł, blokada konta, Rejestracja użytkownika. Dzięki temu można wykrywać potencjalnie podejrzane działania i reagować na nie. Umożliwia również zbieranie danych operacyjnych; na przykład, aby śledzić, kto uzyskuje dostęp do aplikacji</p>|

## <a name="ensure-that-the-system-has-inbuilt-defenses-against-misuse"></a><a id="inbuilt-defenses"></a>Upewnij się, że system ma wbudowane zabezpieczenia przed nieprawidłowym użyciem

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki**                   | <p>Należy umieścić kontrolki, które generują wyjątek zabezpieczeń w przypadku nieprawidłowego użycia aplikacji. Na przykład jeśli walidacja danych wejściowych jest na miejscu, a osoba atakująca próbuje wstrzyknąć złośliwy kod, który nie jest zgodny z wyrażeniem regularnym, może zostać zgłoszony wyjątek zabezpieczeń, który może być przejawem nieprawidłowego użycia systemu</p><p>Na przykład zaleca się rejestrowanie wyjątków zabezpieczeń i podejmowanie akcji dla następujących problemów:</p><ul><li>Walidacja danych wejściowych</li><li>Naruszenia CSRF</li><li>Rozliczenie (górny limit liczby żądań na użytkownika na zasób)</li><li>Naruszenia przekazywania plików</li><ul>|

## <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a><a id="diagnostics-logging"></a>Włączanie rejestrowania diagnostycznego dla aplikacji sieci Web w programie Azure App Service

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | EnvironmentType — Azure |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Platforma Azure udostępnia wbudowaną diagnostykę, która pomaga w debugowaniu aplikacji sieci Web App Service. Dotyczy to również aplikacji API Apps i Mobile Apps. App Service Web Apps udostępnia funkcje diagnostyczne do rejestrowania informacji z serwera sieci Web i aplikacji sieci Web.</p><p>Są one logicznie rozdzielone na diagnostykę serwera sieci Web i diagnostykę aplikacji</p>|

## <a name="ensure-that-login-auditing-is-enabled-on-sql-server"></a><a id="identify-sensitive-entities"></a>Upewnij się, że inspekcja logowania jest włączona na SQL Server

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Konfigurowanie inspekcji logowania](/sql/ssms/configure-login-auditing-sql-server-management-studio) |
| **Kroki** | <p>Należy włączyć inspekcję logowania serwera bazy danych, aby wykrywać/potwierdzać ataki z zgadywaniem hasła. Ważne jest, aby przechwytywać nieudane próby logowania. Przechwytywanie sukcesów i nieudanych prób zalogowania zapewnia dodatkowe korzyści podczas dochodzeń śledczej</p>|

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Włączanie wykrywania zagrożeń w usłudze Azure SQL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Usługi SQL Azure |
| **Atrybuty**              | Wersja SQL — V12 |
| **Odwołania**              | [Wprowadzenie do wykrywania zagrożeń SQL Database](../../azure-sql/database/threat-detection-configure.md)|
| **Kroki** |<p>Wykrywanie zagrożeń wykrywa nietypowe działania bazy danych wskazujące na potencjalne zagrożenia bezpieczeństwa bazy danych. Zapewnia nową warstwę zabezpieczeń, która umożliwia klientom wykrywanie potencjalnych zagrożeń i reagowanie na nie w miarę ich występowania, zapewniając alerty zabezpieczeń w przypadku nietypowych działań.</p><p>Użytkownicy mogą eksplorować podejrzane zdarzenia za pomocą inspekcji Azure SQL Database, aby określić, czy mają one wpływ na próbę uzyskania dostępu do bazy danych, naruszenia jej lub wykorzystania.</p><p>Wykrywanie zagrożeń ułatwia rozwiązywanie potencjalnych zagrożeń dla bazy danych bez potrzeby eksperta zabezpieczeń lub zarządzania zaawansowanymi systemami monitorowania zabezpieczeń</p>|

## <a name="use-azure-storage-analytics-to-audit-access-of-azure-storage"></a><a id="analytics"></a>Używanie analityka magazynu platformy Azure do inspekcji dostępu do usługi Azure Storage

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy |
| **Odwołania**              | [Używanie analityka magazynu do monitorowania typu autoryzacji](../../storage/blobs/security-recommendations.md#loggingmonitoring) |
| **Kroki** | <p>Każde konto magazynu może umożliwić analityka magazynu platformy Azure wykonywanie rejestrowania i przechowywanie danych metryk. Dzienniki analizy magazynu zawierają ważne informacje, takie jak metoda uwierzytelniania używana przez kogoś podczas uzyskiwania dostępu do magazynu.</p><p>Może to być naprawdę przydatne, jeśli masz ścisłą ochronę dostępu do magazynu. Na przykład w Blob Storage można ustawić wszystkie kontenery jako prywatne i zaimplementować korzystanie z usługi sygnatury dostępu współdzielonego w aplikacjach. Następnie można regularnie sprawdzać dzienniki, aby sprawdzić, czy dostęp do obiektów BLOB odbywa się przy użyciu kluczy konta magazynu, co może wskazywać na naruszenie zabezpieczeń lub jeśli obiekty blob są publiczne, ale nie powinny być.</p>|

## <a name="implement-sufficient-logging"></a><a id="sufficient-logging"></a>Implementowanie wystarczającego rejestrowania

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_logging) |
| **Kroki** | <p>Brak odpowiedniego dziennika inspekcji po zdarzeniu zabezpieczeń może utrudnić śledczej wysiłki. Windows Communication Foundation (WCF) oferuje możliwość rejestrowania udanych i/lub nieudanych prób uwierzytelnienia.</p><p>Rejestrowanie nieudanych prób uwierzytelnienia może ostrzec administratorów o potencjalnych atakach z wykorzystaniem pełnego wymuszania. Podobnie rejestrowanie pomyślnych zdarzeń uwierzytelniania może zapewnić przydatny dziennik inspekcji, gdy zostanie naruszone wiarygodne konto. Włącz funkcję inspekcji zabezpieczeń usługi WCF |

### <a name="example"></a>Przykład
Poniżej przedstawiono przykładową konfigurację z włączoną inspekcją
```
<system.serviceModel>
    <behaviors>
        <serviceBehaviors>
            <behavior name=""NewBehavior"">
                <serviceSecurityAudit auditLogLocation=""Default""
                suppressAuditFailure=""false"" 
                serviceAuthorizationAuditLevel=""SuccessAndFailure""
                messageAuthenticationAuditLevel=""SuccessAndFailure"" />
                ...
            </behavior>
        </servicebehaviors>
    </behaviors>
</system.serviceModel>
```

## <a name="implement-sufficient-audit-failure-handling"></a><a id="audit-failure-handling"></a>Implementowanie wystarczającej obsługi błędów inspekcji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_insufficient_audit_failure_handling) |
| **Kroki** | <p>Rozwinięte rozwiązanie jest skonfigurowane tak, aby nie generować wyjątku, gdy zapis w dzienniku inspekcji nie powiedzie się. Jeśli usługa WCF została skonfigurowana, aby nie zgłaszać wyjątku, gdy nie można zapisać w dzienniku inspekcji, program nie będzie powiadamiany o awarii i może nie wystąpić Inspekcja krytycznych zdarzeń zabezpieczeń.</p>|

### <a name="example"></a>Przykład
`<behavior/>`Poniższy element pliku konfiguracji WCF instruuje program WCF, aby nie powiadamiał aplikacji o niepowodzeniu zapisywania w dzienniku inspekcji przez program WCF.
```
<behaviors>
    <serviceBehaviors>
        <behavior name="NewBehavior">
            <serviceSecurityAudit auditLogLocation="Application"
            suppressAuditFailure="true"
            serviceAuthorizationAuditLevel="Success"
            messageAuthenticationAuditLevel="Success" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Skonfiguruj funkcję WCF w celu powiadomienia programu za każdym razem, gdy nie można zapisać w dzienniku inspekcji. Program powinien mieć alternatywny Schemat powiadomień na potrzeby alertów organizacji, że dzienniki inspekcji nie są utrzymywane. 

## <a name="ensure-that-auditing-and-logging-is-enforced-on-web-api"></a><a id="logging-web-api"></a>Upewnij się, że Inspekcja i rejestrowanie są wymuszane w interfejsie API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Włącz inspekcję i rejestrowanie interfejsów API sieci Web. Dzienniki inspekcji powinny przechwycić kontekst użytkownika. Zidentyfikuj wszystkie ważne zdarzenia i Rejestruj te zdarzenia. Implementowanie scentralizowanego rejestrowania |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-field-gateway"></a><a id="logging-field-gateway"></a>Upewnij się, że w bramie pola jest wymuszane odpowiednie inspekcje i rejestrowanie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Gdy wiele urządzeń nawiązuje połączenie z bramą pola, upewnij się, że próby połączenia i stan uwierzytelniania (powodzenie lub niepowodzenie) dla poszczególnych urządzeń są rejestrowane i utrzymywane na bramie pola.</p><p>Ponadto w przypadkach, w których Brama pola zachowuje IoT Hub poświadczenia dla poszczególnych urządzeń, należy się upewnić, że inspekcja jest wykonywana po pobraniu tych poświadczeń. Opracowuj proces okresowego przekazywania dzienników do usługi Azure IoT Hub/magazynu w celu długoterminowego przechowywania.</p> |

## <a name="ensure-that-appropriate-auditing-and-logging-is-enforced-on-cloud-gateway"></a><a id="logging-cloud-gateway"></a>Upewnij się, że w bramie w chmurze jest wymuszane odpowiednie inspekcje i rejestrowanie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama usługi IoT Cloud | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Wprowadzenie do monitorowania operacji IoT Hub](../../iot-hub/iot-hub-operations-monitoring.md) |
| **Kroki** | <p>Projektowanie na potrzeby zbierania i przechowywania danych inspekcji zebranych za poorednictwem monitorowania IoT Hub operacji. Włącz następujące kategorie monitorowania:</p><ul><li>Operacje tożsamości urządzeń</li><li>Komunikacja między urządzeniem i chmurą</li><li>Komunikacja między chmurą i urządzeniem</li><li>Połączenia</li><li>Operacje przekazywania plików</li></ul>|