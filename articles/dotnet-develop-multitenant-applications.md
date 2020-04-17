---
title: Wzorzec aplikacji sieci Web z wieloma dzierżawami | Dokumenty firmy Microsoft
description: Znajdź przeglądy architektury i wzorce projektów, które opisują sposób implementacji aplikacji sieci web z wieloma dzierżawami na platformie Azure.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: d1441ede9f448b3e6ffb0726c2ee92f192369e9a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481834"
---
# <a name="multitenant-applications-in-azure"></a>Aplikacje wielodostępne na platformie Azure
Aplikacja wielodostępna jest zasobem udostępnionym, który umożliwia "użytkownikom w oddzielnych dzierżawach" wyświetlanie aplikacji tak, jakby była ich własna. Typowy scenariusz, który nadaje się do aplikacji wielodostępnej jest taki, w którym wszyscy użytkownicy aplikacji z różnych dzierżaw może chcieć dostosować środowisko użytkownika, ale w przeciwnym razie mają te same podstawowe wymagania biznesowe. Przykładami dużych aplikacji wielodostępnych są usługi Office 365, Outlook.com i visualstudio.com.

Z punktu widzenia dostawcy aplikacji korzyści wynikające z wielodostępności odnoszą się głównie do efektywności operacyjnej i kosztowej. Jedna wersja aplikacji może zaspokoić potrzeby wielu dzierżawców/klientów, umożliwiając konsolidację zadań administracyjnych systemu, takich jak monitorowanie, dostrajanie wydajności, konserwacja oprogramowania i tworzenie kopii zapasowych danych.

Poniżej znajduje się lista najważniejszych celów i wymagań z punktu widzenia dostawcy.

* **Inicjowanie obsługi administracyjnej:** Musi być w stanie aprowizować nowych dzierżaw dla aplikacji.  W przypadku aplikacji wielodostępnych z dużą liczbą dzierżawców zwykle konieczne jest zautomatyzowanie tego procesu, włączając samoobsługowe inicjowanie obsługi administracyjnej.
* **Łatwość konserwacji:** Musisz mieć możliwość uaktualnienia aplikacji i wykonywania innych zadań konserwacyjnych, podczas gdy wielu dzierżawców używa jej.
* **Monitorowanie:** Musisz mieć możliwość monitorowania aplikacji przez cały czas, aby zidentyfikować wszelkie problemy i je rozwiązać. Obejmuje to monitorowanie, jak każda dzierżawa jest przy użyciu aplikacji.

Prawidłowo zaimplementowana aplikacja wielodostępna zapewnia użytkownikom następujące korzyści.

* **Izolacja**: Działania poszczególnych dzierżawców nie mają wpływu na korzystanie z aplikacji przez innych dzierżawców. Dzierżawcy nie mogą uzyskać dostępu do danych innych. Wydaje się, że dzierżawca, jakby mają wyłączne korzystanie z aplikacji.
* **Dostępność**: Indywidualni dzierżawcy chcą, aby aplikacja była stale dostępna, być może z gwarancjami zdefiniowanymi w umowy SLA. Ponownie działania innych dzierżawców nie powinny mieć wpływu na dostępność aplikacji.
* **Skalowalność:** aplikacja skaluje się, aby sprostać wymaganiom poszczególnych dzierżawców. Obecność i działania innych dzierżawców nie powinny mieć wpływu na wydajność aplikacji.
* **Koszty:** Koszty są niższe niż uruchamianie dedykowanej aplikacji z jedną dzierżawą, ponieważ wielonajemność umożliwia udostępnianie zasobów.
* **Możliwość dostosowania**. Możliwość dostosowania aplikacji dla poszczególnych dzierżawy na różne sposoby, takie jak dodawanie lub usuwanie funkcji, zmiana kolorów i logo, a nawet dodawanie własnego kodu lub skryptu.

Krótko mówiąc, chociaż istnieje wiele zagadnień, które należy wziąć pod uwagę, aby zapewnić wysoce skalowalną usługę, istnieje również wiele celów i wymagań, które są wspólne dla wielu aplikacji wielodostępnych. Niektóre z nich mogą nie być istotne w konkretnych scenariuszach, a znaczenie poszczególnych celów i wymagań będzie się różnić w każdym scenariuszu. Jako dostawca aplikacji wielodostępnej, będziesz mieć również cele i wymagania, takie jak, spełniając cele i wymagania dzierżawy, rentowność, rozliczenia, wiele poziomów usług, inicjowanie obsługi administracyjnej, monitorowanie konserwacji i automatyzacji.

Aby uzyskać więcej informacji na temat dodatkowych zagadnień projektowych aplikacji wielodostępnej, zobacz [Hostowanie aplikacji wielodostępnej na platformie Azure.][Hosting a Multi-Tenant Application on Azure] Aby uzyskać informacje na temat typowych wzorców architektury danych w aplikacjach baz danych typu oprogramowanie jako usługa (SaaS), zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database). 

Platforma Azure udostępnia wiele funkcji, które umożliwiają rozwiązanie kluczowych problemów napotkanych podczas projektowania systemu wielodostępnego.

**Izolacja**

* Segmentuj dzierżawce witryny według nagłówków hostów z komunikacją TLS lub bez niej
* Segment dzierżawy witryny sieci Web według parametrów kwerendy
* Usługi sieci Web w rolach procesu roboczego
  * Role procesu roboczego, które zazwyczaj przetwarzają dane w wewnętrznej yskładie aplikacji.
  * Role sieci Web, które zazwyczaj działają jako frontend dla aplikacji.

**Storage**

Zarządzanie danymi, takie jak usługa Azure SQL Database lub usługi Azure Storage, takie jak usługa Tabela, która zapewnia usługi przechowywania dużych ilości danych nieustrukturyzowanych i usługi obiektów Blob, która zapewnia usługi do przechowywania dużych ilości nieustrukturyzowanego tekstu lub danych binarnych, takich jak wideo, audio i obrazy.

* Zabezpieczanie danych wielodostępnych w bazie danych SQL na dzierżawcę identyfikatorów SQL Server.
* Za pomocą tabel platformy Azure dla zasobów aplikacji, określając zasady dostępu na poziomie kontenera, można mieć możliwość dostosowania uprawnień bez konieczności wystawiania nowych adresów URL dla zasobów chronionych za pomocą podpisów dostępu współdzielonego.
* Kolejki platformy Azure dla zasobów aplikacji kolejki platformy Azure są często używane do przetwarzania dysku w imieniu dzierżawców, ale mogą być również używane do dystrybucji pracy wymaganej do inicjowania obsługi administracyjnej lub zarządzania.
* Kolejki magistrali usług dla zasobów aplikacji, która wypycha pracę do udostępnionej usługi, można użyć jednej kolejki, w której każdy nadawca dzierżawy ma tylko uprawnienia (wynikające z oświadczeń wystawionych przez usługę ACS) do wypychania do tej kolejki, podczas gdy tylko odbiorcy z usługi mają uprawnienia do pobierania z kolejki danych pochodzących z wielu dzierżawców.

**Usługi połączeń i zabezpieczeń**

* Usługa Azure Service Bus — infrastruktura obsługi wiadomości, która znajduje się między aplikacjami, umożliwiając im wymianę wiadomości w luźny sposób w celu zwiększenia skali i odporności.

**Usługi sieciowe**

Platforma Azure oferuje kilka usług sieciowych, które obsługują uwierzytelnianie i zwiększają łatwość zarządzania hostowanymi aplikacjami. Usługi te są następujące:

* Usługa Azure Virtual Network umożliwia aprowizwiązanie wirtualnych sieci prywatnych (VPN) na platformie Azure i zarządzanie nimi oraz bezpieczne łączenie ich z lokalną infrastrukturą IT.
* Menedżer ruchu w sieci wirtualnej umożliwia równoważenie obciążenia ruchu przychodzącego w wielu hostowanych usługach platformy Azure, niezależnie od tego, czy są one uruchomione w tym samym centrum danych, czy w różnych centrach danych na całym świecie.
* Usługa Azure Active Directory (Azure AD) to nowoczesna usługa oparta na rest, która zapewnia zarządzanie tożsamościami i możliwości kontroli dostępu dla aplikacji w chmurze. Korzystanie z usługi Azure AD dla zasobów aplikacji zapewnia łatwy sposób uwierzytelniania i autoryzowania użytkowników, aby uzyskać dostęp do aplikacji sieci web i usług, umożliwiając jednocześnie funkcje uwierzytelniania i autoryzacji, które mają być uwzględniane z kodu.
* Usługa Azure Service Bus zapewnia bezpieczną obsługę wiadomości i możliwości przepływu danych dla aplikacji rozproszonych i hybrydowych, takich jak komunikacja między aplikacjami hostowanymi platformy Azure a lokalnymi aplikacjami i usługami, bez konieczności tworzenia złożonych infrastruktury zapory i zabezpieczeń. Za pomocą usługi Service Bus Relay for Application Resources, aby uzyskać dostęp do usług, które są udostępniane jako punkty końcowe może należeć do dzierżawy (na przykład hostowane poza systemem, takich jak lokalnie), lub mogą być usługi aprowizowane specjalnie dla dzierżawy (ponieważ poufne, specyficzne dla dzierżawy dane przesuń przez nich).

**Zasoby inicjowania obsługi administracyjnej**

Platforma Azure udostępnia wiele sposobów aprowizowania nowych dzierżaw dla aplikacji. W przypadku aplikacji wielodostępnych z dużą liczbą dzierżawców zwykle konieczne jest zautomatyzowanie tego procesu, włączając samoobsługowe inicjowanie obsługi administracyjnej.

* Role procesu roboczego umożliwiają aprowizowanie i anulowanie obsługi administracyjnej dla zasobów dzierżawy (na przykład gdy nowa dzierżawa rejestruje się lub anuluje), zbieranie metryk do użycia pomiarów i zarządzanie skalą zgodnie z określonym harmonogramem lub w odpowiedzi na przekroczenie progów kluczowych wskaźników wydajności. Ta sama rola może również służyć do wypychania aktualizacji i uaktualnień do rozwiązania.
* Obiekty BLOB platformy Azure mogą służyć do inicjowania obsługi administracyjnej zasobów obliczeniowych lub wstępnie zainicjowanych zasobów magazynu dla nowych dzierżaw, zapewniając jednocześnie zasady dostępu na poziomie kontenera w celu ochrony pakietów usługi obliczeniowej, obrazów VHD i innych zasobów.
* Opcje inicjowania obsługi administracyjnej zasobów bazy danych SQL dla dzierżawy obejmują:
  
  * DDL w skryptach lub osadzone jako zasoby w zestawach.
  * Pakiety DAC programu SQL Server 2008 R2 wdrożone programowo.
  * Kopiowanie z głównej bazy danych odwołań.
  * Używanie bazy danych Import i eksportowanie do aprowizowania nowych baz danych z pliku.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
