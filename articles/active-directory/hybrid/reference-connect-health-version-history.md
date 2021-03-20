---
title: Historia wersji programu Azure AD Connect Health
description: W tym dokumencie opisano wersje Azure AD Connect Health i informacje zawarte w tych wersjach.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/10/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7653f850edc910fc78b14a628b87dcb22aeb903
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279418"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: historia wersji
Zespół Azure Active Directory regularnie aktualizuje Azure AD Connect Health za pomocą nowych funkcji i funkcji. W tym artykule wymieniono wersje i funkcje, które zostały wydane.  

> [!NOTE]
> Agenci programu Connect Health są automatycznie aktualizowani po wydaniu nowej wersji. Upewnij się, że ustawienia autouaktualniania są włączone z Azure Portal.
>

Azure AD Connect Health synchronizacji jest zintegrowana z instalacją Azure AD Connect. Przeczytaj więcej na temat [historii wersji Azure AD Connect](./reference-connect-version-history.md) , aby uzyskać informacje zwrotne dotyczące funkcji, zagłosuj na [kanale głosu użytkownika programu Connect Health](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="april-2020"></a>Kwiecień 2020 r.
**Aktualizacja agenta**

- Agent Azure AD Connect Health dla AD FS (wersja 3.1.77.0)

   1.   Poprawka błędu dla alertu "Nieprawidłowa nazwa główna usługi (SPN) dla usługi AD FS", dla której alert został niepoprawnie zgłoszony.


## <a name="july-2019"></a>Lipiec 2019 r.
**Aktualizacja agenta**
* Agent Azure AD Connect Health dla AD FS (wersja 3.1.59.0) 
   1. Zmiana tekstu w TestWindowsTransport
   2. Zmiany dotyczące przekazywania AD FS RP
   
* Agent Azure AD Connect Health dla AD FS (wersja 3.1.56.0) 
   1. Dodaj test TestWindowsTransport i Usuń testy WsTrust Endpoints w teście CheckOffice365Endpoints
   2. Rejestruj system operacyjny i informacje o programie .NET
   3. Zwiększ rozmiar przekazywania komunikatu konfiguracji jednostki UZALEŻNIONej do 1 MB.
   4. Poprawki błędów
   
* Agent Azure AD Connect Health dla AD DS (wersja 3.1.56.0) 
   1. Rejestruj system operacyjny i informacje o programie .NET 
   2. Poprawki błędów

## <a name="may-2019"></a>Maj 2019 r.
**Aktualizacja agenta:** 
* Agent Azure AD Connect Health dla AD FS (wersja 3.1.51.0) 
   1. Poprawka usterki do rozróżnienia między wieloma logowaniami, które współużytkują ten sam identyfikator żądania klienta.
   2. Poprawka usterki służąca do analizowania nieprawidłowych błędów nazwy użytkownika/hasła na serwerach zlokalizowanych w języku.   

## <a name="april-2019"></a>Kwiecień 2019 r.
**Aktualizacja agenta:** 
* Agent Azure AD Connect Health dla AD FS (wersja 3.1.46.0) 
   1. Poprawka sprawdzania duplikatu procesu alertu SPN dla usług AD FS

## <a name="march-2019"></a>Marzec 2019 r.
**Aktualizacja agenta:** 
* Agent Azure AD Connect Health dla AD DS (wersja 3.1.41.0)  
   1. Kolekcja wersji platformy .NET
   2. Poprawa zbierania liczników wydajności w przypadku braku określonych kategorii
   3. Poprawka błędu na zapobieganiu duplikowania wielu wystąpień agentów monitorowania

* Agent Azure AD Connect Health dla AD FS (wersja 3.1.41.0) 
   1. Integrowanie i uaktualnianie AD FS skryptów testowych przy użyciu ADFSToolBox
   2. Implementowanie kolekcji wersji platformy .NET
   3. Poprawa zbierania liczników wydajności w przypadku braku określonych kategorii
   4. Poprawka błędu na zapobieganiu duplikowania wielu wystąpień agentów monitorowania


## <a name="november-2018"></a>Listopad 2018 r.
**Nowe funkcje w wersji ogólnie dostępnej:** 
* Azure AD Connect Health do synchronizacji — diagnozowanie i korygowanie zduplikowanych błędów synchronizacji atrybutów z portalu

**Aktualizacja agenta:** 
* Agent Azure AD Connect Health dla AD DS (wersja 3.1.24.0) 
   1. Protokół Transport Layer Security (TLS) w wersji 1,2 zgodność i wymuszanie
   2. Zmniejsz poziom hałasu alertów wykazu globalnego
   3. Poprawki błędów rejestracji agenta kondycji

* Agent Azure AD Connect Health dla AD FS (wersja 3.1.24.0)  
   1. Protokół Transport Layer Security (TLS) w wersji 1,2 zgodność i wymuszanie
   2. Obsługa Test-ADFSRequestToken dla zlokalizowanego systemu operacyjnego
   3. Rozwiązano problem z blokowaniem EventHandler agenta diagnostycznego
   4. Poprawki błędów rejestracji agenta kondycji

## <a name="august-2018"></a>Sierpień 2018 r. 
*  Azure AD Connect Health Agent do synchronizacji (wersja 3.1.7.0) wydana z Azure AD Connect wersja 1.1.880.0    
   1. Poprawka dla [wysokiego problemu z procesorem CPU agenta monitorowania z wersjami .NET Framework KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Czerwiec 2018 r. 
**Nowe funkcje w wersji zapoznawczej:** 
* Azure AD Connect Health do synchronizacji — diagnozowanie i korygowanie zduplikowanych błędów synchronizacji atrybutów z portalu 

**Aktualizacja agenta:** 
* Agent Azure AD Connect Health dla AD DS (wersja 3.1.7.0)    
  1. Poprawka dla [wysokiego problemu z procesorem CPU agenta monitorowania z wersjami .NET Framework KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Agent Azure AD Connect Health dla AD FS (wersja 3.1.7.0)  
  1. Poprawka dla [wysokiego problemu z procesorem CPU agenta monitorowania z wersjami .NET Framework KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Poprawki wyników testów na serwerze pomocniczym usług AD FS 2016
   
* Agent Azure AD Connect Health dla AD FS (wersja 3.1.2.0)  
  1. Poprawka do zarządzania pamięcią agenta i powiązanych alertów przeznaczonych dla wersji 3.0.244.0


## <a name="may-2018"></a>Maj 2018 r.
**Aktualizacja agenta:**
* Agent Azure AD Connect Health dla AD DS (wersja 3.0.244.0)
  1. Poprawa ochrony prywatności agenta  
  2. Poprawki błędów i ogólne ulepszenia

* Agent Azure AD Connect Health dla AD FS (wersja 3.0.244.0)
  1. Udoskonalenia usługi diagnostyki agentów i powiązanego modułu programu PowerShell
  2. Poprawa ochrony prywatności agenta  
  3. Poprawki błędów i ogólne ulepszenia

* Azure AD Connect Health Agent do synchronizacji (wersja 3.0.164.0) wydana z Azure AD Connect wersja 1.1.819.0 
  1. Poprawa ochrony prywatności agenta  
  2. Poprawki błędów i ogólne ulepszenia


## <a name="march-2018"></a>Marzec 2018 r.
**Nowe funkcje w wersji zapoznawczej:**
* Azure AD Connect Health dla raportu i alertu AD FS dotyczącego ryzykownego adresu IP.

**Aktualizacja agenta:**

* Agent Azure AD Connect Health dla AD DS (wersja 3.0.176.0)
  1. Ulepszenia dostępności agentów 
  2. Poprawki błędów i ogólne ulepszenia
* Agent Azure AD Connect Health dla AD FS (wersja 3.0.176.0)
  1. Ulepszenia dostępności agentów 
  2. Poprawki błędów i ogólne ulepszenia
* Azure AD Connect Health Agent do synchronizacji (wersja 3.0.129.0) wydana z Azure AD Connect wersja 1.1.750.0  
  1. Ulepszenia dostępności agentów 
  2. Poprawki błędów i ogólne ulepszenia

## <a name="december-2017"></a>Grudzień 2017
**Aktualizacja agenta:**

* Agent Azure AD Connect Health dla AD DS (wersja 3.0.145.0)
  1. Ulepszenia dostępności agentów 
  2. Dodano nowe polecenia rozwiązywania problemów z agentem
  3. Poprawki błędów i ogólne ulepszenia
* Agent Azure AD Connect Health dla AD FS (wersja 3.0.145.0)
  1. Dodano nowe polecenia rozwiązywania problemów z agentem
  2. Ulepszenia dostępności agentów 
  3. Poprawki błędów i ogólne ulepszenia
  
## <a name="october-2017"></a>Październik 2017
**Aktualizacja agenta:**

 * Azure AD Connect Health Agent do synchronizacji (wersja 3.0.129.0) wydana z Azure AD Connect wersja 1.1.649.0
<br></br> Rozwiązano problem ze zgodnością wersji między Azure AD Connect a agentem Azure AD Connect Health na potrzeby synchronizacji. Ten problem dotyczy klientów, którzy wykonują Azure AD Connect uaktualnieniem w miejscu do wersji 1.1.647.0, ale obecnie ma agenta kondycji w wersji 3.0.127.0. Po uaktualnieniu Agent kondycji nie będzie mógł wysyłać danych o kondycji dotyczących usługi Azure AD Connect Synchronization do Usługa kondycji Azure AD. Ta poprawka powoduje zainstalowanie agenta kondycji w wersji 3.0.129.0 podczas Azure AD Connect uaktualnienia w miejscu. Wersja agenta kondycji 3.0.129.0 nie ma problemu ze zgodnością w wersji 1.1.649.0 Azure AD Connect.

## <a name="july-2017"></a>Lipiec 2017
**Aktualizacja agenta:**

* Agent Azure AD Connect Health dla AD DS (wersja 3.0.68.0)
  1. Poprawki błędów i ogólne ulepszenia
  2. Wsparcie dla chmury suwerennej
* Agent Azure AD Connect Health dla AD FS (wersja 3.0.68.0)
  1. Poprawki błędów i ogólne ulepszenia
  2. Wsparcie dla chmury suwerennej
* Azure AD Connect Health Agent do synchronizacji (wersja 3.0.68.0) wydana z Azure AD Connect wersja 1.1.614.0
  1. Obsługa Microsoft Azure Government chmury i Microsoft Cloud Niemiec

## <a name="april-2017"></a>Kwiecień 2017      
**Aktualizacja agenta:**

* Agent Azure AD Connect Health dla AD FS (wersja 3.0.12.0)
  1. Poprawki błędów i ogólne ulepszenia
* Agent Azure AD Connect Health dla AD DS (wersja 3.0.12.0)
  1. Ulepszenia przekazywania liczników wydajności
  2. Poprawki błędów i ogólne ulepszenia

## <a name="october-2016"></a>Październik 2016
**Aktualizacja agenta:**

* Agent Azure AD Connect Health dla AD FS (wersja 2.6.408.0)
* Udoskonalenia wykrywania adresów IP klientów w żądaniach uwierzytelniania
* Poprawki błędów związanych z alertami
* Agent Azure AD Connect Health dla AD DS (wersja 2.6.408.0)
* Poprawki błędów związanych z alertami.
* Azure AD Connect Health Agent do synchronizacji (wersja 2.6.353.0) wydana z Azure AD Connect wersja 1.1.281.0
* Podaj wymagane dane dla raportów o błędach synchronizacji
* Poprawki błędów związanych z alertami

**Nowe funkcje w wersji zapoznawczej:**

* Raporty o błędach synchronizacji dla Azure AD Connect

**Nowe funkcje:**

* Pole Azure AD Connect Health dla AD FS — adres IP jest dostępne w raporcie dotyczącym najważniejszych użytkowników 50 z nieprawidłową nazwą użytkownika i hasłem.

## <a name="july-2016"></a>Lipiec 2016
**Nowe funkcje w wersji zapoznawczej:**

* [Azure AD Connect Health AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Styczeń 2016
**Aktualizacja agenta:**

* Agent Azure AD Connect Health dla AD FS (wersja 2.6.91.1512)

**Nowe funkcje:**

* [Narzędzie do testowania łączności dla agentów Azure AD Connect Health](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Listopad 2015
**Nowe funkcje:**

* Obsługa [kontroli dostępu opartej na rolach (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac)

**Nowe funkcje w wersji zapoznawczej:**

* [Azure AD Connect Health do synchronizacji](how-to-connect-health-sync.md).

**Rozwiązano problemy:**

* Poprawki błędów występujących podczas rejestracji agenta.

## <a name="september-2015"></a>Wrzesień 2015
**Nowe funkcje:**

* Niewłaściwy Raport z hasłem username dla AD FS
* Obsługa konfigurowania nieuwierzytelnionego serwera proxy HTTP
* Obsługa konfigurowania agenta w trybie Server Core
* Ulepszenia alertów dla AD FS
* Udoskonalenia Azure AD Connect Health agenta AD FS na potrzeby łączności i przekazywania danych.

**Rozwiązano problemy:**

* Poprawki błędów w szczegółowe informacje dotyczące użycia dla AD FS typów błędów.

## <a name="june-2015"></a>Czerwiec 2015
**Początkowa wersja Azure AD Connect Health dla AD FS i AD FS serwera proxy.**

**Nowe funkcje:**

* Alerty dotyczące monitorowania AD FS i AD FS serwerów proxy przy użyciu powiadomień e-mail.
* Łatwy dostęp do topologii AD FS i wzorców w AD FS licznikach wydajności.
* Trend w przypadku pomyślnych żądań tokenów na serwerach AD FS pogrupowanych według aplikacji, metod uwierzytelniania, lokalizacji sieciowej żądania itp.
* Trendy niepomyślnych żądań na serwerach AD FS pogrupowane według aplikacji, typów błędów itp.
* Prostsze wdrażanie agentów przy użyciu poświadczeń administratora globalnego usługi Azure AD.  

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [monitorowaniu lokalnej infrastruktury tożsamości i usług synchronizacji w chmurze](./whatis-azure-ad-connect.md).