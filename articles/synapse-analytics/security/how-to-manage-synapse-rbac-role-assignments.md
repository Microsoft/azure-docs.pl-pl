---
title: Jak zarządzać przypisaniami RBAC Synapse w programie Synapse Studio
description: W tym artykule opisano sposób przypisywania i odwoływania ról RBAC Synapse do podmiotów zabezpieczeń usługi AAD
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: a4016751944e5b7ec5d32dc586e9034db99c9d73
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523560"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Jak zarządzać przypisaniami ról RBAC Synapse w programie Synapse Studio

Synapse RBAC używa ról do przypisywania uprawnień użytkownikom, grupom i innym podmiotom zabezpieczeń, aby umożliwić dostęp do zasobów Synapse i artefaktów kodu oraz korzystanie z nich.  [Dowiedz się więcej](./synapse-workspace-synapse-rbac.md)

W tym artykule pokazano, jak dodać i usunąć przypisania roli RBAC Synapse.

>[!Note]
>- Aby zarządzać przypisaniami ról RBAC Synapse, należy mieć rolę Administrator Synapse w obszarze roboczym lub w zakresie niższym poziomów, który obejmuje obiekty, którymi chcesz zarządzać. Jeśli jesteś administratorem Synapse w obszarze roboczym, możesz udzielić dostępu do wszystkich obiektów w obszarze roboczym. 
>- Aby ułatwić odzyskanie dostępu do obszaru roboczego w przypadku, gdy nie są przypisane żadne Synapse Administratorzy, użytkownicy z uprawnieniami do zarządzania przypisaniami ról funkcji **RBAC platformy Azure** w obszarze roboczym mogą również zarządzać przypisaniami ról **RBAC Synapse** , umożliwiając dodawanie administratorów Synapse lub innych przypisań ról w Synapse RBAC.
>- Dostęp do pul SQL jest zarządzany przy użyciu uprawnień SQL.  Z wyjątkiem ról administratora programu Synapse i Synapse, Synapse role RBAC nie udzielają dostępu do pul SQL.

>[!important]
>- Zmiany wprowadzone w przypisaniach ról RBAC Synapse mogą zająć od 2-5 minut. 
>- Jeśli zarządzasz uprawnieniami Synapse RBAC przez modyfikację członkostwa w grupach zabezpieczeń, wówczas zmiany członkostwa są zarządzane przy użyciu Azure Active Directory.  Zmiany członkostwa w grupie mogą zająć kilka minut.

## <a name="open-synapse-studio"></a>Otwórz Synapse Studio  

Aby przypisać rolę do użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej, najpierw [Otwórz Synapse Studio](https://web.azuresynapse.net/) i wybierz obszar roboczy. 

![Logowanie do obszaru roboczego](./media/common/login-workspace.png) 
 
 Po otwarciu obszaru roboczego rozwiń sekcję **zabezpieczenia** po lewej stronie i wybierz pozycję **Kontrola dostępu**. 

 ![Wybierz Access Control w sekcji zabezpieczenia po lewej stronie](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

Ekran kontrola dostępu zawiera listę bieżących przypisań ról.  Można filtrować listę według nazwy głównej lub poczty e-mail, a także wybiórczo filtrować typy obiektów, role lub zakresy. Na tym ekranie można dodawać lub usuwać przypisania ról.  

## <a name="add-a-synapse-role-assignment"></a>Dodawanie przypisania roli Synapse

Na ekranie kontroli dostępu wybierz pozycję **+ Dodaj** , aby utworzyć nowe przypisanie roli.

![Kliknij pozycję + Dodaj, aby utworzyć nowe przypisanie roli](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

Na karcie Dodaj przypisanie roli można utworzyć przypisania ról w zakresie obszaru roboczego lub zakresu elementów obszaru roboczego. 

## <a name="add-workspace-scoped-role-assignment"></a>Dodawanie przypisania roli z zakresem obszaru roboczego

Najpierw wybierz **obszar roboczy** jako zakres, a następnie wybierz **rolę Synapse RBAC**.  Wybierz **podmioty zabezpieczeń** , do których ma zostać przypisana rola, a następnie utwórz przypisania ról. 

![Dodawanie przypisania roli obszaru roboczego — Wybieranie roli](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

Przypisana rola będzie miała zastosowanie do wszystkich odpowiednich obiektów w obszarze roboczym.

## <a name="add-workspace-item-scoped-role-assignment"></a>Dodaj przypisanie roli do zakresu elementu roboczego

Aby przypisać rolę w zakresie bardziej szczegółowym, wybierz pozycję **element obszaru roboczego** jako zakres, a następnie wybierz **Typ elementu** zakresu.       

![Dodaj przypisanie roli elementu roboczego — wybierz typ elementu](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Wybierz konkretny **element** , który ma być używany jako zakres, a następnie wybierz **rolę** , która ma zostać przypisana z listy rozwijanej.  Lista rozwijana zawiera tylko te role, które są prawidłowe dla wybranego typu elementu. [Dowiedz się więcej](https://go.microsoft.com/fwlink/?linkid=2148306).  

![Dodaj przypisanie roli elementu roboczego — wybierz rolę](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
Następnie **Wybierz elementy główne,** do których ma zostać przypisana rola.  Można iteracyjnie wybrać wiele podmiotów zabezpieczeń.  Wybierz pozycję **Zastosuj** , aby utworzyć przypisania ról.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Usuwanie przypisania roli RBAC Synapse

Aby odwołać dostęp do Synapse RBAC, należy usunąć odpowiednie przypisania ról.  Na ekranie kontroli dostępu Użyj filtrów, aby zlokalizować przypisania ról, które mają zostać usunięte.  Sprawdź przypisania ról, a następnie wybierz pozycję **Usuń dostęp**.   

![Usuwanie przypisania roli w celu usunięcia dostępu](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Należy pamiętać, że zmiany w przypisaniach ról będą obowiązywać od 2-5 minut.   

## <a name="next-steps"></a>Następne kroki

[Informacje o rolach Synapse RBAC wymaganych do wykonywania typowych zadań](./synapse-workspace-understand-what-role-you-need.md) 
