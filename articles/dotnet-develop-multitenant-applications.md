---
title: Wzorzec aplikacji sieci Web z wieloma dzierżawcami | Microsoft Docs
description: Znajdź omówienia architektury i wzorce projektowe opisujące sposób implementacji wielodostępnej aplikacji sieci Web na platformie Azure.
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
ms.custom: devx-track-dotnet
ms.openlocfilehash: 71114b6663d010437e5778d288eacf4e120b3da5
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91395978"
---
# <a name="multitenant-applications-in-azure"></a>Aplikacje wielodostępne na platformie Azure
Aplikacja wielodostępna jest udostępnionym zasobem, który umożliwia "użytkownikom w osobnych dzierżawcach" wyświetlanie aplikacji tak, jakby była własna. Typowym scenariuszem, który ogranicza się do aplikacji wielodostępnej, jest to, w której wszyscy użytkownicy aplikacji z różnych dzierżawców mogą chcieć dostosować środowisko użytkownika, ale w przeciwnym razie mają one te same podstawowe wymagania biznesowe. Przykłady dużych aplikacji wielodostępnych to Microsoft 365, Outlook.com i visualstudio.com.

Z perspektywy dostawcy aplikacji korzyści płynące z wielu dzierżawców odnoszą się głównie do efektywności operacyjnej i ekonomicznej. Jedna wersja aplikacji może spełniać potrzeby wielu dzierżawców/klientów, co pozwala na konsolidację zadań administracyjnych, takich jak monitorowanie, dostrajanie wydajności, konserwacja oprogramowania i kopie zapasowe danych.

Poniżej znajduje się lista najbardziej znaczących celów i wymagań z perspektywy dostawcy.

* **Inicjowanie obsługi**: musisz być w stanie zainicjować obsługę nowych dzierżawców dla aplikacji.  W przypadku aplikacji wielodostępnych z dużą liczbą dzierżawców zazwyczaj konieczne jest zautomatyzowanie tego procesu, włączając samoobsługowe Inicjowanie obsługi administracyjnej.
* **Łatwość konserwacji**: należy mieć możliwość uaktualnienia aplikacji i wykonywania innych zadań konserwacyjnych, gdy korzysta z niej wiele dzierżawców.
* **Monitorowanie**: musisz mieć możliwość monitorowania aplikacji przez cały czas, aby identyfikować problemy i rozwiązywać je. Obejmuje to monitorowanie użycia aplikacji przez poszczególnych dzierżawców.

Prawidłowo zaimplementowana aplikacja wielodostępna zapewnia użytkownikom następujące korzyści.

* **Izolacja**: działania poszczególnych dzierżawców nie wpływają na korzystanie z aplikacji przez innych dzierżawców. Dzierżawcy nie mogą uzyskać dostępu do innych danych. Jest on widoczny dla dzierżawy, ponieważ ma wyłączne korzystanie z aplikacji.
* **Dostępność**: poszczególne dzierżawy chcą, aby aplikacja była stale dostępna, na przykład z gwarancjami zdefiniowanymi w umowie SLA. Ponownie działania innych dzierżawców nie powinny mieć wpływu na dostępność aplikacji.
* **Skalowalność**: aplikacja skaluje się w celu spełnienia wymagań poszczególnych dzierżawców. Obecność i działania innych dzierżawców nie powinny wpływać na wydajność aplikacji.
* **Koszty**: koszty są niższe niż w przypadku korzystania z dedykowanej aplikacji z jedną dzierżawą, ponieważ obsługa wielu dzierżawców umożliwia udostępnianie zasobów.
* **Szerszym**. Możliwość dostosowywania aplikacji dla poszczególnych dzierżawców na różne sposoby, takie jak dodawanie lub usuwanie funkcji, zmiana kolorów i logo, a nawet Dodawanie własnego kodu lub skryptu.

Krótko mówiąc, chociaż istnieje wiele kwestii, które należy wziąć pod uwagę w celu zapewnienia wysokiej skalowalności usługi, istnieją również pewne cele i wymagania, które są wspólne dla wielu aplikacji wielodostępnych. Niektóre z nich mogą nie być istotne w określonych scenariuszach, a znaczenie poszczególnych celów i wymagań różni się w każdym scenariuszu. Jako dostawca aplikacji wielodostępnej również masz cele i wymagania, takie jak, spełniające cele i wymagania dzierżawy, zyskowność, rozliczenie, wiele poziomów usług, Inicjowanie obsługi, monitorowanie utrzymania i automatyzację.

Aby uzyskać więcej informacji na temat dodatkowych zagadnień projektowych aplikacji wielodostępnej, zobacz hosting aplikacji wielodostępnej [na platformie Azure][Hosting a Multi-Tenant Application on Azure]. Aby uzyskać informacje na temat typowych wzorców architektury danych w aplikacjach baz danych typu oprogramowanie jako usługa (SaaS), zobacz artykuł [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md) (Wzorce projektowe dla wielodostępnych aplikacji SaaS korzystających z usługi Azure SQL Database). 

Platforma Azure udostępnia wiele funkcji, które umożliwiają rozwiązywanie najważniejszych problemów występujących podczas projektowania wielodostępnego systemu.

**Izolacji**

* Segmentowanie dzierżawców witryn sieci Web według nagłówków hosta z lub bez komunikacji TLS
* Przesegmentowanie dzierżawców witryn sieci Web według parametrów zapytania
* Usługi sieci Web w rolach procesów roboczych
  * Role procesów roboczych, które zwykle przetwarzają dane w zapleczu aplikacji.
  * Role sieci Web, które zwykle działają jako fronton dla aplikacji.

**Storage**

Zarządzanie danymi, takie jak Azure SQL Database lub usługi magazynu platformy Azure, takie jak Table service, które udostępnia usługi do przechowywania dużych ilości danych bez struktury i Blob service, które udostępniają usługi do przechowywania dużych ilości danych tekstowych lub binarnych bez struktury, takich jak wideo, audio i obrazy.

* Zabezpieczanie wielodostępnych danych w SQL Database logowania SQL Server poszczególnych dzierżawców.
* Za pomocą tabel platformy Azure dla zasobów aplikacji przez określenie zasad dostępu na poziomie kontenera można dostosować uprawnienia bez konieczności wydawania nowych adresów URL dla zasobów chronionych za pomocą sygnatur dostępu współdzielonego.
* Kolejki platformy Azure dla zasobów aplikacji kolejki platformy Azure są często używane do przetwarzania w imieniu dzierżawców, ale mogą być również używane do dystrybucji pracy wymaganej do aprowizacji lub zarządzania.
* Kolejki Service Bus dla zasobów aplikacji, które wypychają prace do współużytkowanej usługi, można użyć pojedynczej kolejki, w której każdy nadawca dzierżawy ma uprawnienia do wypychania do tej kolejki, a tylko odbiorcy z usługi mają uprawnienia do ściągania z kolejki danych pochodzących z wielu dzierżawców.

**Połączenia i usługi zabezpieczeń**

* Azure Service Bus, infrastruktura obsługi komunikatów, która działa między aplikacjami, umożliwiającą im wymianę komunikatów w luźno połączony sposób w celu zwiększenia skalowalności i odporności.

**Usługi sieciowe**

Platforma Azure udostępnia kilka usług sieciowych, które obsługują uwierzytelnianie i usprawniają zarządzanie hostowanymi aplikacjami. Są to następujące usługi:

* Usługa Azure Virtual Network umożliwia udostępnianie wirtualnych sieci prywatnych (VPN) na platformie Azure i zarządzanie nimi, a także bezpieczne łączenie ich z lokalną infrastrukturą INFORMATYCZNą.
* Virtual Network Traffic Manager umożliwia Równoważenie obciążenia ruchem przychodzącym wielu hostowanych usług platformy Azure, niezależnie od tego, czy są one uruchamiane w tym samym centrum danych, czy też w różnych centrach danych na całym świecie.
* Azure Active Directory (Azure AD) to nowoczesne usługi oparte na protokole REST, które zapewniają funkcje zarządzania tożsamościami i kontroli dostępu dla aplikacji w chmurze. Korzystanie z usługi Azure AD dla zasobów aplikacji zapewnia łatwy sposób uwierzytelniania i autoryzowania użytkowników w celu uzyskania dostępu do aplikacji i usług sieci Web, a jednocześnie pozwala na rozprowadzenie funkcji uwierzytelniania i autoryzacji w kodzie.
* Azure Service Bus zapewnia bezpieczną obsługę komunikatów i przepływów danych dla aplikacji rozproszonych i hybrydowych, takich jak komunikacja między aplikacjami hostowanymi przez platformę Azure i lokalnymi aplikacjami i usługami, bez konieczności stosowania złożonej infrastruktury zapory i zabezpieczeń. Użycie Service Bus Relay do zasobów aplikacji w celu uzyskania dostępu do usług, które są dostępne jako punkty końcowe, może należeć do dzierżawy (na przykład hostowanej poza systemem, na przykład lokalnie) lub w przypadku usług zainicjowanych dla dzierżawy (ponieważ poufne dane specyficzne dla dzierżawy są przesyłane między nimi).

**Zasoby aprowizacji**

Platforma Azure udostępnia wiele metod udostępniania nowych dzierżawców dla aplikacji. W przypadku aplikacji wielodostępnych z dużą liczbą dzierżawców zazwyczaj konieczne jest zautomatyzowanie tego procesu, włączając samoobsługowe Inicjowanie obsługi administracyjnej.

* Role procesów roboczych umożliwiają udostępnianie i cofanie aprowizacji dla zasobów dzierżawy (na przykład gdy nowa dzierżawa lub anulowanie), zbiera metryki na potrzeby pomiarów użycia i zarządza skalowaniem zgodnie z określonym harmonogramem lub w reakcji na przekroczenie progów kluczowych wskaźników wydajności. Ta sama rola może być również używana do wypychania aktualizacji i uaktualnień do rozwiązania.
* Obiekty blob platformy Azure mogą służyć do aprowizacji zasobów magazynów obliczeniowych lub wstępnie zainicjowanych dla nowych dzierżawców, zapewniając jednocześnie zasady dostępu na poziomie kontenera do ochrony pakietów usługi obliczeniowej, obrazów dysków VHD i innych zasobów.
* Opcje aprowizacji SQL Database zasobów dla dzierżawy obejmują:
  
  * KOD DDL w skryptach lub osadzony jako zasoby w zestawach.
  * Wdrożone programowo pakiety DAC SQL Server 2008 R2.
  * Kopiowanie z głównej bazy danych referencyjnych.
  * Używanie funkcji importowania i eksportowania bazy danych w celu aprowizacji nowych baz danych z pliku.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
