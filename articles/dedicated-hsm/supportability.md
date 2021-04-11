---
title: Obsługa — dedykowany moduł HSM platformy Azure | Microsoft Docs
description: Opcje pomocy technicznej i obszary odpowiedzialności dla dedykowanego modułu HSM platformy Azure w różnych scenariuszach
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: cd949bdb7c489478df6a16d6dccd0bf358637604
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606933"
---
# <a name="azure-dedicated-hsm-supportability"></a>Dedykowana obsługa modułu HSM platformy Azure

Dedykowana usługa HSM platformy Azure udostępnia urządzenie fizyczne wyłącznie klientom z pełną odpowiedzialnością za kontrolę administracyjną i zarządzaniem. Udostępnione urządzenie to [firmy Thales Luna 7 model HSM A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). Firma Microsoft nie będzie mieć dostępu administracyjnego po zainicjowaniu obsługi administracyjnej przez klienta poza fizycznym załącznikiem portu szeregowego jako rolą monitorowania.  Bez dostępu firma Microsoft nie może korzystać z trwającej konserwacji na poziomie oprogramowania ani obowiązków związanych z administracją systemu. W związku z tym klienci są odpowiedzialni za typowe działania operacyjne.
Klienci są w pełni odpowiedzialni za aplikacje korzystające z sprzętowych modułów zabezpieczeń i powinni współpracować z firmy Thales w celu uzyskania pomocy technicznej lub pomocy opartej na konsultacjach. Ze względu na stopień własności klienta higieny działania, firma Microsoft nie może oferować żadnych gwarancji wysokiej dostępności dla tej usługi. Klient jest odpowiedzialny za zapewnienie, że aplikacje są prawidłowo skonfigurowane do osiągnięcia wysokiej dostępności. Firma Microsoft będzie monitorować i obsługiwać kondycję urządzeń oraz łączność sieciową.

## <a name="getting-support"></a>Uzyskiwanie pomocy technicznej

Obsługa klienta dedykowanego modułu HSM jest wspólnym nakładem pracy między firmą Microsoft i firmy Thales. Wszelkie problemy z sprzętem lub ścieżką sieciową są rozwiązywane przez firmę Microsoft i wszystko, co należy zrobić z rzeczywistym modułem HSM, takim jak konfiguracja, oprogramowanie, oprogramowanie układowe i programowanie aplikacji, zostanie rozwiązane przez firmy Thales. Ten model wsparcia zapewnia najszybszą trasę do najbardziej efektywnej pomocy technicznej. W razie wątpliwości z konkretnym problemem Zgłoś żądanie pomocy technicznej z firmą Microsoft, aby upewnić się, że nastąpi odpowiednie skierowanie. Firma Microsoft będzie utrzymywać wszystkie scenariusze pomocy technicznej i dążyć do osiągnięcia najlepszego wsparcia dla naszych klientów.

## <a name="thales-support"></a>Obsługa firmy Thales

Klienci korzystający z dedykowanej usługi HSM kwalifikują się do pomocy technicznej z firmy Thales zgodnie z planem pomocy technicznej Plus. Wymaga to jedynie procesu rejestracji przy użyciu portalu pomocy technicznej firmy Thales. W celu uzyskania dostępu do dedykowanej usługi HSM w ramach wstępnego zaangażowania z firmą Microsoft zostanie dostarczony identyfikator klienta i instrukcje. Mechanizmem uzyskiwania pomocy technicznej z firmy Thales jest za pośrednictwem [portalu obsługi klienta](https://supportportal.thalesgroup.com/csm).
Najważniejszym punktem jest to, że firmy Thales będzie udostępniać wszystkie programy i dokumentację wymagane do korzystania z modułu HSM (na przykład oprogramowanie dostępu klienta i zestawy SDK) za pośrednictwem pobierania w portalu obsługi klienta.

### <a name="software-components"></a>Składniki oprogramowania

W konfiguracji urządzeń HSM używane są różne składniki oprogramowania:

* Oprogramowanie klienckie
* SDK
* Narzędzia

### <a name="guidance"></a>Wskazówki

Firmy Thales udostępnia wskazówki dotyczące administrowania i konfiguracji za pośrednictwem [portalu obsługi klienta firmy Thales](https://supportportal.thalesgroup.com/csm). Po zalogowaniu się przy użyciu prawidłowego identyfikatora klienta te dokumenty są dostępne do pobrania. Firmy Thales udostępnia również szereg przewodników dotyczących integracji, aby pomóc klientom w różnych scenariuszach i integracji oprogramowania. Aby uzyskać więcej informacji, zobacz [witrynę firmy Thales partner dla firmy Microsoft](https://cpl.thalesgroup.com/partners/overview).

### <a name="support"></a>Pomoc techniczna

Wszelkie problemy dotyczące poziomu oprogramowania lub pytania w odniesieniu do korzystania z sprzętowych modułów zabezpieczeń jako części dedykowanej usługi HSM powinny być kierowane bezpośrednio do pomocy technicznej firmy Thales. Wszystkie składniki oprogramowania wymienione powyżej oraz wszelkie niestandardowe konfiguracje modułu HSM, które są inicjowane przez program, będą rozkierowane przez firmy Thales. Aby uzyskać więcej informacji, zobacz [Portal obsługi klienta firmy Thales](https://supportportal.thalesgroup.com/csm).

### <a name="consulting-services"></a>Usługi konsultingowe

Aby uzyskać pomoc w projektowaniu, tworzeniu i wdrażaniu niestandardowych aplikacji korzystających z modułu HSM, skontaktuj się z przedstawicielem konta usługi firmy Thales.

## <a name="microsoft-support"></a>Pomoc techniczna firmy Microsoft

Firma Microsoft zapewnia, że fizyczne urządzenia HSM są dostępne w sieci i w stanie operacyjnym na wyłączne korzystanie z jednego klienta. Klienci są odpowiedzialni za konfigurację, administrację i zarządzanie urządzeniem. Obowiązki firmy Microsoft obejmują:

* Upewnienie się, że urządzenie ma moc i chłodzenie
* Utrzymywanie stanu operacyjnego modułu HSM (na przykład scenariusze Break/Fix)
* Urządzenie jest dostępne za pośrednictwem sieci.

Do firmy Microsoft należy zgłosić problemy, takie jak następujące:

* Błędy składników
* Pełny błąd urządzenia
* Problemy z dostępem do sieci
* Problemy z inicjowaniem i anulowaniem aprowizacji.

Firma Microsoft ma fizyczny dostęp do urządzenia za pośrednictwem roli monitorowania (będącej rolą nieadministracyjnym), która umożliwia podstawowe dane telemetryczne kondycji.  Dzięki temu firma Microsoft będzie udostępniać klientowi proaktywne powiadomienia o problemach, chyba że klient zdecyduje się ograniczyć to uprawnienie. 

### <a name="provisioning-and-decommissioning"></a>Inicjowanie obsługi i likwidowanie

Gdy klient ma zatwierdzoną rejestrację dla dedykowanej usługi HSM, będzie mógł tworzyć zasoby HSM (obecnie za pośrednictwem programu PowerShell lub interfejsu wiersza polecenia, a nie Azure Portal). Zasób przechodzi przez proces alokacji, który mapuje urządzenie fizyczne w określonym regionie do wstępnie zdefiniowanej sieci wirtualnej (VNet) klienta. Po pobraniu w sieci wirtualnej klient może uzyskać dostęp do urządzenia i skonfigurować go zgodnie z wymaganiami. Klienci uzyskują dostęp do dedykowanych sprzętowych modułów zabezpieczeń przy użyciu oprogramowania i narzędzi klienta firmy Thales. Proces tworzenia zasobów jest obsługiwany przez firmę Microsoft. Niestandardowy proces konfiguracji jest obsługiwany przez firmy Thales. (zobacz firmy Thales support powyżej). Gdy klient zakończył korzystanie z modułu HSM, należy go zresetować (lub zero) w celu zapewnienia braku trwałości danych. Proces resetowania urządzenia spowoduje usunięcie całej konfiguracji i danych niestandardowych. Firma Microsoft dealokuje urządzenie i zwraca je do puli w stanie Pristine. Oznacza to, że gdy urządzenie zostanie zwrócone do puli, nie ma dowodów poprzedniej aktywności klienta. 

### <a name="hardware-issues"></a>Problemy sprzętowe

Urządzenie HSM ma nadmiarowe i wymienne jednostki wentylatorów.  Jednak usunięcie jednostki wentylatora nadal spowoduje naruszenie zdarzenia. Gdy wystąpi awaria składnika, firma Microsoft będzie używać najbardziej odpowiedniego procesu do rozwiązywania problemu dotyczącego poziomu składnika w taki sposób, który powoduje minimalne przerwy i najniższe ryzyko dla naszych klientów.
Dowolna poważna awaria urządzenia spowoduje zastąpienie tego urządzenia nowym urządzeniem z puli wolnych. Klient po prostu zawiera nowe urządzenie w istniejącej parze HA do synchronizacji i powraca do pełnego stanu operacyjnego. Urządzenie, na którym zainstalowano urządzenia, będzie miało usunięte i Shredded w witrynie w centrum danych. 

### <a name="networking-issues"></a>Problemy dotyczące sieci

Jeśli klienci napotykają problemy z dostępem do sieci na urządzeniu HSM, powinni skontaktować się z pomocą techniczną firmy Microsoft. Prostym testem dostępu do sieci jest użycie protokołu SSH do nawiązania połączenia z urządzeniem HSM. W przypadku niepowodzenia skontaktuj się z pomocą techniczną firmy Microsoft.

## <a name="service-level-expectations-for-support"></a>Oczekiwania na poziom usług dla pomocy technicznej

Aby uzyskać informacje na temat poziomów usług pomocy technicznej firmy Microsoft, zapoznaj się z [planem pomocy technicznej Azure](https://azure.microsoft.com/support/plans/).
Aby uzyskać pomoc techniczną dla usługi firmy Thales, zapoznaj się z pomocą [techniczną firmy Thales](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Następne kroki

Zaleca się, aby kluczowe koncepcje, takie jak wysoka dostępność i zabezpieczenia, były dobrze zrozumiałe przed zainicjowaniem obsługi urządzeń oraz projektem lub wdrożeniem aplikacji.

* [Architektura wdrożenia](deployment-architecture.md)
* [Wysoka dostępność](high-availability.md)
* [Zabezpieczenia fizyczne](physical-security.md)
* [Sieć](networking.md)

