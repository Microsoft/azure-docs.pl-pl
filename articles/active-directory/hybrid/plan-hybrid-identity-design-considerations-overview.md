---
title: Zagadnienia dotyczące projektowania tożsamości hybrydowej Azure Active Directory — omówienie | Microsoft Docs
description: Omówienie i mapa zawartości Przewodnik po zagadnieniach dotyczących projektowania tożsamości hybrydowej
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997736"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Zagadnienia dotyczące projektowania tożsamości hybrydowej usługi Azure Active Directory
Urządzenia oparte na konsumentach stanowią prostą chmurę firmową, a aplikacje typu "oprogramowanie jako usługa" (SaaS) oparte na chmurze można łatwo zastosować. W związku z tym, zachowanie kontroli dostępu aplikacji użytkowników w wewnętrznych centrach danych i na platformach w chmurze jest trudne.  

Rozwiązania firmy Microsoft do obsługi tożsamości obejmują zarówno funkcje lokalne, jak i chmurowe, tworząc jedną tożsamość użytkownika na potrzeby uwierzytelniania i autoryzacji w kontekście wszystkich zasobów, niezależnie od lokalizacji. Koncepcja ta jest znana jako tożsamość hybrydowa. Istnieją różne opcje projektowania i konfiguracji dla tożsamości hybrydowej przy użyciu rozwiązań firmy Microsoft, a w niektórych przypadkach może być trudne, aby określić, która kombinacja najlepiej spełni wymagania organizacji. 

Ten przewodnik po zagadnieniach dotyczących projektowania tożsamości hybrydowej pomoże Ci zrozumieć, jak projektować rozwiązanie hybrydowej tożsamości, które najlepiej odpowiada wymaganiom biznesowym i technologicznym organizacji.  Ten przewodnik zawiera szczegółowe informacje na temat serii kroków i zadań, które można wykonać, aby pomóc w zaprojektowaniu hybrydowego rozwiązania do obsługi tożsamości, które spełnia wymagania organizacji. W ramach kroków i zadań przewodnik zawiera odpowiednie technologie i opcje funkcji dostępne dla organizacji spełniających wymagania dotyczące jakości funkcjonalności i usług (np. dostępność, skalowalność, wydajność, możliwości zarządzania i bezpieczeństwo). 

W tym celu Przewodnik po zagadnieniach dotyczących projektowania tożsamości hybrydowej ma na celu udzielenie odpowiedzi na następujące pytania: 

* Jakie pytania muszę zadawać i odpowiedzieć na rozwiązanie hybrydowego projektu dla technologii lub domeny problemu, który najlepiej spełnia moje wymagania?
* Jaką sekwencję działań należy wykonać, aby zaprojektować rozwiązanie do tworzenia tożsamości hybrydowej dla technologii lub domeny problemu? 
* Jakie są dostępne opcje technologii i konfiguracji hybrydowej tożsamości, aby pomóc mi spełnić wymagania? Jakie są różnice między tymi opcjami, aby można było wybrać najlepszą opcję dla mojej firmy?

## <a name="who-is-this-guide-intended-for"></a>Dla kogo jest przeznaczony ten przewodnik?
 CIO, CITO, większość architektów tożsamości, architektów korporacyjnych i architektów IT odpowiedzialnych za projektowanie hybrydowego rozwiązania do tworzenia tożsamości dla średnich lub dużych organizacji.

## <a name="how-can-this-guide-help-you"></a>W czym może być pomocny ten przewodnik?
Korzystając z tego przewodnika, można zrozumieć, jak zaprojektować rozwiązanie do obsługi tożsamości hybrydowej, które jest w stanie zintegrować oparty na chmurze system zarządzania tożsamościami z obecnym lokalnym rozwiązaniem do obsługi tożsamości. 

Na poniższej ilustracji przedstawiono przykładowe rozwiązanie do obsługi tożsamości hybrydowej, które umożliwia administratorom IT zarządzanie systemem Windows Server Active Directory, które znajdują się w środowisku lokalnym z Microsoft Azure Active Directory, aby umożliwić użytkownikom korzystanie z pojedynczych Sign-On (SSO) w aplikacjach znajdujących się w chmurze i lokalnie.

![Przykład](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

Powyższa ilustracja to przykład hybrydowego rozwiązania do zarządzania tożsamościami, które korzysta z usług w chmurze do integracji z funkcjami lokalnymi w celu zapewnienia pojedynczego środowiska w procesie uwierzytelniania użytkowników końcowych i ułatwia zarządzanie tymi zasobami. Chociaż ten przykład może być typowym scenariuszem, projekt tożsamości hybrydowej każdej organizacji może być inny niż przykład przedstawiony na rysunku 1 ze względu na inne wymagania. 

Ten przewodnik zawiera szereg kroków i zadań, które można wykonać, aby zaprojektować rozwiązanie do obsługi tożsamości hybrydowej spełniające unikatowe wymagania w organizacji. W ramach poniższych kroków i zadań przewodnik przedstawia odpowiednie technologie i opcje funkcji dostępne w celu spełnienia wymagań dotyczących poziomu jakości funkcjonalności i usług dla organizacji.

**Założenia**: masz pewne doświadczenie w korzystaniu z systemu Windows Server, Active Directory Domain Services i Azure Active Directory. W tym dokumencie przyjęto założenie, że szukasz, jak te rozwiązania mogą spełniać własne potrzeby biznesowe lub zintegrowane rozwiązanie.

## <a name="design-considerations-overview"></a>Omówienie zagadnień dotyczących projektowania
Ten dokument zawiera zestaw kroków i zadań, które można wykonać, aby zaprojektować rozwiązanie hybrydowej tożsamości, które najlepiej spełnia Twoje wymagania. Kroki są prezentowane w uporządkowanej kolejności. Zagadnienia dotyczące projektowania, które można poznać w kolejnych krokach, mogą wymagać zmiany decyzji podjętych we wcześniejszych krokach, z powodu sprzecznych opcji projektowych. Każda próba jest wykonywana w celu powiadomienia o potencjalnych konfliktach projektowych w całym dokumencie. 

Dojdziemy do projektu, który najlepiej spełnia Twoje wymagania dopiero po przeprowadzeniu iteracji przez kroki tyle razy, ile jest to konieczne, aby uwzględnić wszystkie zagadnienia w dokumencie. 

| Faza tożsamości hybrydowej | Lista tematów |
| --- | --- |
| Określanie wymagań dotyczących tożsamości |[Określanie potrzeb firmy](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Określanie wymagań dotyczących synchronizacji katalogów](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definiowanie hybrydowej strategii wdrażania tożsamości](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Planowanie zwiększenia bezpieczeństwa danych dzięki rozwiązaniu silnej tożsamości |[Określanie wymagań dotyczących ochrony danych](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Określanie wymagań dotyczących zarządzania zawartością](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Określanie wymagań dotyczących kontroli dostępu](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Określanie wymagań dotyczących odpowiadania na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definiowanie strategii ochrony danych](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Planowanie cyklu życia tożsamości hybrydowej |[Określanie zadań hybrydowego zarządzania tożsamościami](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Zarządzanie synchronizacją](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Określ strategię wdrażania hybrydowego zarządzania tożsamościami](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących tożsamości](plan-hybrid-identity-design-considerations-business-needs.md)

