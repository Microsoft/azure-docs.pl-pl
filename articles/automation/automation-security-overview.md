---
title: Wprowadzenie do uwierzytelniania w Azure Automation
description: Ten artykuł zawiera przegląd zabezpieczeń usługi Automation i różnych dostępnych metod uwierzytelniania dla kont usługi Azure Automation.
keywords: zabezpieczenia usługi automation, bezpieczna usługa automation, uwierzytelnianie usługi automation
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 3d3dbaad18f6acbe1ddf17d81f54e4232c838dd7
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787417"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Wprowadzenie do uwierzytelniania w usłudze Azure Automation

Usługa Azure Automation pozwala na zautomatyzowanie zadań w odniesieniu do zasobów platformy Azure, a także zasobów lokalnych oraz pochodzących od innych dostawców chmury, takich jak usługa Amazon Web Services (AWS). Aby element Runbook mógł wykonać żądane działania, musi mieć uprawnienia do bezpiecznego dostępu do zasobów z minimalnymi prawami wymaganymi w ramach subskrypcji.

W tym artykule opisano różne scenariusze uwierzytelniania obsługiwane przez Azure Automation oraz sposób rozpoczynania pracy w oparciu o środowisko lub środowiska, którymi trzeba zarządzać.  

## <a name="automation-account-overview"></a>Konto usługi Automation — omówienie

Kiedy uruchamiasz usługę Azure Automation po raz pierwszy, musisz utworzyć co najmniej jedno konto usługi Automation. Konta usługi Automation umożliwiają izolowanie zasobów usługi Automation (elementy Runbook, elementy zawartości, konfiguracje) od zasobów znajdujących się na innych kontach usługi Automation. Konta usługi Automation można wykorzystać do rozdzielenia zasobów w ramach oddzielnych środowisk logicznych. Na przykład jednego konta można użyć dla środowiska rozwojowego, innego dla produkcyjnego, a jeszcze innego dla lokalnego. Konto usługi Azure Automation różni się od konta Microsoft lub kont utworzonych w ramach subskrypcji platformy Azure.

Zasoby usługi Automation na poszczególnych kontach są skojarzone z pojedynczym regionem świadczenia usługi Azure, ale za pomocą kont usługi Automation można zarządzać wszystkimi zasobami w ramach subskrypcji. Głównym powodem tworzenia kont usługi Automation w różnych regionach jest istnienie zasad, które wymagają, aby dane i zasoby były izolowane w określonym regionie.

Wszystkie zadania, które wykonuje się w odniesieniu do zasobów za pomocą usługi Azure Resource Manager i poleceń cmdlet platformy Azure w usłudze Azure Automation, muszą zostać uwierzytelnione na platformie Azure przy użyciu funkcji uwierzytelniania opartego na poświadczeniu tożsamości organizacyjnej w usłudze Azure Active Directory. Konta Uruchom jako w Azure Automation umożliwiają uwierzytelnianie w celu zarządzania zasobami na platformie Azure przy użyciu poleceń cmdlet platformy Azure. Podczas tworzenia konta Uruchom jako jest tworzony nowy użytkownik głównej nazwy usługi w Azure Active Directory (AD) i przypisuje rolę współautor do tego użytkownika na poziomie subskrypcji. W przypadku elementów Runbook, które używają hybrydowych procesów roboczych elementów Runbook na maszynach wirtualnych platformy Azure, można użyć [uwierzytelniania elementu Runbook z tożsamościami zarządzanymi](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) zamiast kont Uruchom jako do uwierzytelniania w zasobach platformy Azure.

Nazwa główna usługi dla konta Uruchom jako nie ma uprawnień do domyślnego odczytywania usługi Azure AD. Jeśli chcesz dodać uprawnienia do odczytu lub zarządzania usługą Azure AD, musisz udzielić uprawnień do nazwy głównej usługi w obszarze **uprawnienia interfejsu API**. Aby dowiedzieć się więcej, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

Dostępna w usłudze Azure Resource Manager kontrola dostępu oparta na rolach umożliwia przypisywanie praw wykonywania dozwolonych akcji do konta użytkownika usługi Azure AD i konta Uruchom jako oraz pozwala na uwierzytelnianie tego obiektu głównego usługi. Dalsze informacje pomagające w tworzeniu modelu zarządzania uprawnieniami w usłudze Automation można znaleźć w artykule [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).  

Elementy Runbook działające w ramach hybrydowego procesu roboczego elementu Runbook w centrum danych lub w przypadku usług obliczeniowych w innych środowiskach chmurowych, takich jak AWS, nie mogą korzystać z tej samej metody, która jest zwykle używana w przypadku elementów Runbook uwierzytelnianych w zasobach platformy Azure. Jest to spowodowane faktem, że te zasoby działają poza platformą Azure i dlatego wymagają własnych poświadczeń zabezpieczeń zdefiniowanych w usłudze Automation do uwierzytelniania w zasobach, z których korzystają lokalnie. Aby uzyskać więcej informacji na temat uwierzytelniania elementów Runbook w ramach procesów roboczych elementów Runbook, zobacz temat [uwierzytelnianie elementów Runbook dla hybrydowych procesów roboczych elementów Runbook](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Następne kroki

* [Utwórz konto usługi Automation na podstawie Azure Portal](automation-create-standalone-account.md).

* [Utwórz konto usługi Automation przy użyciu szablonu Azure Resource Manager](automation-create-account-template.md).

* [Uwierzytelnianie za pomocą Amazon Web Services (AWS)](automation-config-aws-account.md).
