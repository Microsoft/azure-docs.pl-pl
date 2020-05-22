---
title: Omówienie uwierzytelniania konta Azure Automation
description: Ten artykuł zawiera omówienie uwierzytelniania konta Azure Automation.
keywords: zabezpieczenia usługi automation, bezpieczna usługa automation, uwierzytelnianie usługi automation
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: f78161590c897e6337e8694acb9f8a6b34258246
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743802"
---
# <a name="automation-account-authentication-overview"></a>Omówienie uwierzytelniania konta usługi Automation

Usługa Azure Automation pozwala na zautomatyzowanie zadań w odniesieniu do zasobów platformy Azure, a także zasobów lokalnych oraz pochodzących od innych dostawców chmury, takich jak usługa Amazon Web Services (AWS). Można używać elementów Runbook do automatyzowania zadań lub hybrydowego procesu roboczego elementu Runbook, jeśli masz zadania spoza platformy Azure do zarządzania. Każde środowisko wymaga uprawnień do bezpiecznego dostępu do zasobów z minimalnymi prawami wymaganymi w ramach subskrypcji platformy Azure.

W tym artykule opisano scenariusze uwierzytelniania obsługiwane przez Azure Automation i zawarto informacje na temat rozpoczynania pracy w oparciu o środowisko lub środowiska, którymi należy zarządzać.

## <a name="automation-account"></a>Konto usługi Automation 

Kiedy uruchamiasz usługę Azure Automation po raz pierwszy, musisz utworzyć co najmniej jedno konto usługi Automation. Konta usługi Automation umożliwiają izolowanie zasobów usługi Automation, elementów Runbook, zasobów, konfiguracji, od zasobów innych kont. Konta usługi Automation można wykorzystać do rozdzielenia zasobów w ramach oddzielnych środowisk logicznych. Na przykład jednego konta można użyć dla środowiska rozwojowego, innego dla produkcyjnego, a jeszcze innego dla lokalnego. Konto usługi Azure Automation różni się od konta Microsoft lub kont utworzonych w ramach subskrypcji platformy Azure. Aby zapoznać się z wprowadzeniem do tworzenia konta usługi Automation, zobacz [Tworzenie konta usługi Automation](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Zasoby automatyzacji

Zasoby usługi Automation dla każdego konta usługi Automation są skojarzone z pojedynczym regionem świadczenia usługi Azure, ale konto może zarządzać wszystkimi zasobami w ramach subskrypcji platformy Azure. Głównym powodem tworzenia kont usługi Automation w różnych regionach jest to, że zasady, które wymagają, aby dane i zasoby były izolowane w określonym regionie.

Wszystkie zadania utworzone przy użyciu Azure Resource Manager i poleceń cmdlet programu PowerShell w programie Azure Automation muszą uwierzytelniać się na platformie Azure przy użyciu uwierzytelniania opartego na poświadczeniach tożsamości organizacji w usłudze Azure Active Directory (Azure AD). 

## <a name="run-as-account"></a>Konto Uruchom jako

Konta Uruchom jako w Azure Automation umożliwiają uwierzytelnianie w celu zarządzania zasobami platformy Azure przy użyciu poleceń cmdlet programu PowerShell. Podczas tworzenia konta Uruchom jako zostaje utworzony nowy użytkownik głównej nazwy usługi w usłudze Azure AD i przypisuje rolę współautor do tego użytkownika na poziomie subskrypcji. W przypadku elementów Runbook, które używają hybrydowych procesów roboczych elementów Runbook na maszynach wirtualnych platformy Azure, można użyć [uwierzytelniania elementu Runbook z tożsamościami zarządzanymi](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) zamiast kont Uruchom jako do uwierzytelniania w zasobach platformy Azure.

## <a name="service-principal-for-run-as-account"></a>Nazwa główna usługi dla konta Uruchom jako

Nazwa główna usługi dla konta Uruchom jako nie ma uprawnień do domyślnego odczytywania usługi Azure AD. Jeśli chcesz dodać uprawnienia do odczytu lub zarządzania usługą Azure AD, musisz udzielić uprawnień do jednostki usługi w obszarze **uprawnienia interfejsu API**. Aby dowiedzieć się więcej, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Kontrola dostępu oparta na rolach jest dostępna w Azure Resource Manager, aby udzielić dozwolonych akcji do konta użytkownika usługi Azure AD i konta Uruchom jako, a także uwierzytelnić jednostkę usługi. Dalsze informacje pomagające w tworzeniu modelu zarządzania uprawnieniami w usłudze Automation można znaleźć w artykule [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).  

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Uwierzytelnianie elementu Runbook za pomocą hybrydowego procesu roboczego elementu Runbook 

Elementy Runbook działające w ramach hybrydowego procesu roboczego elementu Runbook w centrum danych lub w przypadku usług obliczeniowych w innych środowiskach chmurowych, takich jak AWS, nie mogą korzystać z tej samej metody, która jest zwykle używana w przypadku elementów Runbook uwierzytelnianych w zasobach platformy Azure. Jest to spowodowane faktem, że te zasoby działają poza platformą Azure i dlatego wymagają własnych poświadczeń zabezpieczeń zdefiniowanych w usłudze Automation do uwierzytelniania w zasobach, z których korzystają lokalnie. Aby uzyskać więcej informacji na temat uwierzytelniania elementów Runbook za pomocą procesów roboczych elementów Runbook, zobacz [Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta usługi Automation w witrynie Azure Portal](automation-create-standalone-account.md)
* [Tworzenie konta usługi Automation przy użyciu szablonu Azure Resource Manager](automation-create-account-template.md)
* [Uwierzytelnianie za pomocą Amazon Web Services (AWS)](automation-config-aws-account.md)