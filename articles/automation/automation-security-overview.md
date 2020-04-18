---
title: Wprowadzenie do uwierzytelniania w usłudze Azure Automation
description: Ten artykuł zawiera przegląd zabezpieczeń usługi Automation i różnych dostępnych metod uwierzytelniania dla kont usługi Azure Automation.
keywords: zabezpieczenia usługi automation, bezpieczna usługa automation, uwierzytelnianie usługi automation
services: automation
ms.subservice: process-automation
ms.date: 03/19/2018
ms.topic: conceptual
ROBOTS: NOINDEX
ms.openlocfilehash: 6c823e613bdc6566b683d600051d7c0315979cf6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604802"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Wprowadzenie do uwierzytelniania w usłudze Azure Automation  
Usługa Azure Automation pozwala na zautomatyzowanie zadań w odniesieniu do zasobów platformy Azure, a także zasobów lokalnych oraz pochodzących od innych dostawców chmury, takich jak usługa Amazon Web Services (AWS).  Aby element Runbook mógł wykonać żądane działania, musi mieć uprawnienia do bezpiecznego dostępu do zasobów z minimalnymi prawami wymaganymi w ramach subskrypcji.

W tym artykule uwzględniono różne scenariusze uwierzytelniania obsługiwane przez usługę Azure Automation i opisano, w jaki sposób można rozpocząć pracę w zależności od środowiska lub środowisk, którymi trzeba zarządzać.  

## <a name="automation-account-overview"></a>Konto usługi Automation — omówienie
Kiedy uruchamiasz usługę Azure Automation po raz pierwszy, musisz utworzyć co najmniej jedno konto usługi Automation. Konta usługi Automation umożliwiają izolowanie zasobów usługi Automation (elementy Runbook, elementy zawartości, konfiguracje) od zasobów znajdujących się na innych kontach usługi Automation. Konta usługi Automation można wykorzystać do rozdzielenia zasobów w ramach oddzielnych środowisk logicznych. Na przykład jednego konta można użyć dla środowiska rozwojowego, innego dla produkcyjnego, a jeszcze innego dla lokalnego.  Konto usługi Azure Automation różni się od konta Microsoft lub kont utworzonych w ramach subskrypcji platformy Azure.

Zasoby usługi Automation na poszczególnych kontach są skojarzone z pojedynczym regionem świadczenia usługi Azure, ale za pomocą kont usługi Automation można zarządzać wszystkimi zasobami w ramach subskrypcji. Głównym powodem tworzenia kont usługi Automation w różnych regionach jest istnienie zasad, które wymagają, aby dane i zasoby były izolowane w określonym regionie.

Wszystkie zadania, które wykonuje się w odniesieniu do zasobów za pomocą usługi Azure Resource Manager i poleceń cmdlet platformy Azure w usłudze Azure Automation, muszą zostać uwierzytelnione na platformie Azure przy użyciu funkcji uwierzytelniania opartego na poświadczeniu tożsamości organizacyjnej w usłudze Azure Active Directory.  Uwierzytelnianie oparte na certyfikatach było pierwotną metodą uwierzytelniania za pomocą klasycznej platformy Azure, ale jego konfigurowanie było skomplikowane.  Uwierzytelnianie na platformie Azure za pomocą użytkownika usługi Azure AD zostało wprowadzone w 2014 roku nie tylko po to, aby uprościć proces konfigurowania konta usługi Automation, ale także po to, aby umożliwić uwierzytelnianie nieinteraktywne na platformie Azure za pomocą jednego konta użytkownika, które współdziała zarówno z usługą Azure Resource Manager, jak i zasobami klasycznymi.   

Obecnie po utworzeniu nowego konta usługi Automation witryna Azure Portal automatycznie tworzy:

* Konto Uruchom jako, które tworzy nową nazwę główną usługi w usłudze Azure Active Directory i certyfikat oraz przypisuje rolę Współautor kontroli dostępu opartej na rolach (RBAC), która jest używana do zarządzania zasobami usługi Resource Manager za pomocą elementów runbook.
* Klasyczne konto Uruchom jako przez przekazanie certyfikatu zarządzania, które jest używane do zarządzania klasycznymi zasobami platformy Azure przy użyciu elementów runbook.  

Dostępna w usłudze Azure Resource Manager kontrola dostępu oparta na rolach umożliwia przypisywanie praw wykonywania dozwolonych akcji do konta użytkownika usługi Azure AD i konta Uruchom jako oraz pozwala na uwierzytelnianie tego obiektu głównego usługi.  Dalsze informacje pomagające w tworzeniu modelu zarządzania uprawnieniami w usłudze Automation można znaleźć w artykule [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).  

Elementy Runbook uruchomione w ramach hybrydowego procesu roboczego elementu Runbook w centrum danych lub względem usług obliczeniowych AWS nie mogą korzystać z tej samej metody, która jest zwykle używana w odniesieniu do elementów Runbook uwierzytelnianych w zasobach platformy Azure.  Jest to spowodowane faktem, że te zasoby działają poza platformą Azure i dlatego wymagają własnych poświadczeń zabezpieczeń zdefiniowanych w usłudze Automation do uwierzytelniania w zasobach, z których korzystają lokalnie.  

## <a name="authentication-methods"></a>Metody uwierzytelniania
Poniższa tabela zawiera zestawienie różnych metod uwierzytelniania dla środowisk obsługiwanych przez usługę Azure Automation oraz linki do artykułów opisujących sposób konfigurowania uwierzytelniania dla elementów Runbook.

| Metoda | Środowisko | Artykuł |
| --- | --- | --- |
| Konto użytkownika usługi Azure AD |Usługa Azure Resource Manager i klasyczna platforma Azure |[Uwierzytelnianie elementów Runbook przy użyciu konta użytkownika usługi Azure AD](automation-create-aduser-account.md) |
| Konto Uruchom jako platformy Azure |Azure Resource Manager |[Uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md) |
| Klasyczne konto Uruchom jako platformy Azure |Klasyczny portal Azure |[Uwierzytelnianie elementów Runbook przy użyciu konta Uruchom jako platformy Azure](automation-sec-configure-azure-runas-account.md) |
| Uwierzytelnianie systemu Windows |Lokalne centrum danych |[Uwierzytelnianie elementów Runbook dla hybrydowych procesów roboczych Runbook](automation-hybrid-runbook-worker.md) |
| Poświadczenia AWS |Amazon Web Services |[Uwierzytelnianie elementów Runbook w usłudze Amazon Web Services](automation-config-aws-account.md) |

