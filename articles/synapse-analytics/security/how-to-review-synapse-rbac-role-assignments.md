---
title: Jak przejrzeć przydziały ról RBAC Synapse w programie Synapse Studio
description: W tym artykule opisano sposób przeglądania przypisań ról Synapse RBAC przy użyciu programu Synapse Studio
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9065ca9c7638f3d2bda36e4831b81963936f4b45
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102159"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>Jak przejrzeć przydziały ról RBAC Synapse

Role RBAC Synapse są używane do przypisywania uprawnień użytkownikom, grupom i innym podmiotom zabezpieczeń w celu umożliwienia dostępu do zasobów Synapse i korzystania z nich.  [Dowiedz się więcej](./synapse-workspace-synapse-rbac.md)

W tym artykule wyjaśniono, jak przeglądać bieżące przypisania ról dla obszaru roboczego.

Za pomocą dowolnej roli RBAC Synapse można wyświetlić listę przypisań ról RBAC Synapse dla wszystkich zakresów, łącznie z przypisaniami do obiektów, do których nie masz dostępu. Tylko administrator Synapse może udzielić dostępu do Synapse RBAC.  

>[!Note]
>Użytkownicy-Goście (Użytkownicy z innej dzierżawy usługi AD) nie mogą wyświetlać przypisań ról ani nimi zarządzać, nawet jeśli ma przypisaną rolę administratora Synapse.    

## <a name="open-synapse-studio"></a>Otwórz Synapse Studio  

Aby przejrzeć przypisania ról, najpierw [Otwórz Synapse Studio](https://web.azuresynapse.net/) i wybierz obszar roboczy. 

![Logowanie do obszaru roboczego](./media/common/login-workspace.png) 
 
 Po otwarciu obszaru roboczego wybierz pozycję **Zarządzaj** centrum po lewej stronie, rozwiń sekcję **zabezpieczenia** i wybierz pozycję **Kontrola dostępu**. 

 ![Wybierz Access Control w sekcji zabezpieczenia po lewej stronie](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>Przeglądanie przypisań ról obszaru roboczego

Na ekranie kontroli dostępu są wyświetlane wszystkie bieżące przypisania ról dla obszaru roboczego pogrupowane według roli. Każde przypisanie obejmuje nazwę główną, typ podmiotu, rolę i jego zakres.

![Ekran Access Control](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

Jeśli do podmiotu zabezpieczeń jest przypisana ta sama rola w różnych zakresach, zobaczysz wiele przypisań dla podmiotu zabezpieczeń, po jednym dla każdego zakresu.  

Jeśli rola jest przypisana do grupy zabezpieczeń, zobaczysz role jawnie przypisane do grupy, ale nie role dziedziczone z grup nadrzędnych.  

Można filtrować listę według nazwy głównej lub poczty e-mail i wybiórczo filtrować typy obiektów, role i zakresy. Wprowadź nazwę lub alias adresu e-mail w filtrze nazw, aby zobaczyć przypisane do Ciebie role. Tylko administrator Synapse może zmienić role.

>[!Important] 
>Jeśli jesteś bezpośrednio lub pośrednio członkiem grupy, do której przypisano role, możesz mieć uprawnienia, które nie są wyświetlane.

>[!tip]
>Członkostwa w grupach można znaleźć za pomocą Azure Active Directory w Azure Portal.  

Jeśli tworzysz nowy obszar roboczy, a nazwa główna usługi w obszarze roboczym MSI zostanie automatycznie nadana roli administratora Synapse w zakresie obszaru roboczego.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak zarządzać przypisaniami ról RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md).

Dowiedz się [, jaką rolę należy wykonać w celu wykonania określonych zadań](./synapse-workspace-understand-what-role-you-need.md)