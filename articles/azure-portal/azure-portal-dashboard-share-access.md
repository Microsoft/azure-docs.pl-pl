---
title: Udostępnianie pulpitów nawigacyjnych portalu Azure przy użyciu kontroli dostępu opartej na rolach
description: W tym artykule wyjaśniono, jak udostępnić pulpit nawigacyjny w witrynie Azure portal przy użyciu kontroli dostępu opartej na rolach.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 4eef5a9e3f010e19871471d007ff2a0cc24d3834
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461382"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach

Po skonfigurowaniu pulpitu nawigacyjnego można go opublikować i udostępnić innym użytkownikom w organizacji. Zezwalasz innym na wyświetlanie pulpitu nawigacyjnego przy użyciu [kontroli dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md) platformy Azure (RBAC). Przypisywanie użytkownika lub grupy użytkowników do roli. Ta rola określa, czy ci użytkownicy mogą wyświetlać lub modyfikować opublikowany pulpit nawigacyjny.

Wszystkie opublikowane pulpity nawigacyjne są implementowane jako zasoby platformy Azure. Istnieją one jako elementy, którymi można zarządzać w ramach subskrypcji i są zawarte w grupie zasobów. Z punktu widzenia kontroli dostępu pulpity nawigacyjne nie różnią się od innych zasobów, takich jak maszyna wirtualna lub konto magazynu.

> [!TIP]
> Poszczególne kafelki na pulpicie nawigacyjnym wymuszają własne wymagania dotyczące kontroli dostępu na podstawie wyświetlanych zasobów. Możesz udostępniać pulpit nawigacyjny szeroko, chroniąc dane na poszczególnych kafelkach.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Opis kontroli dostępu dla pulpitów nawigacyjnych

Za pomocą kontroli dostępu opartej na rolach (RBAC) można przypisać użytkowników do ról na trzech różnych poziomach zakresu:

* subskrypcja
* grupa zasobów
* zasób

Uprawnienia przypisywane dziedziczą z subskrypcji w dół do zasobu. Opublikowany pulpit nawigacyjny jest zasobem. Użytkownicy mogą już mieć przypisane do ról dla subskrypcji, które mają zastosowanie do opublikowanego pulpitu nawigacyjnego.

Załóżmy, że masz subskrypcję platformy Azure, a różnym członkom zespołu przypisano role *właściciela,* *współautora*lub *czytelnika* dla subskrypcji. Użytkownicy, którzy są właścicielami lub współautorami mogą wyświetlać, wyświetlać, tworzyć, modyfikować lub usuwać pulpity nawigacyjne w ramach subskrypcji. Użytkownicy, którzy są czytelnikami, mogą wyświetlać i wyświetlać pulpity nawigacyjne, ale nie mogą ich modyfikować ani usuwać. Użytkownicy z dostępem do czytnika mogą wprowadzać lokalne zmiany na opublikowanym pulpicie nawigacyjnym, na przykład podczas rozwiązywania problemu, ale nie mogą publikować tych zmian z powrotem na serwerze. Mogą zrobić kopię prywatną pulpitu nawigacyjnego dla siebie.

Można również przypisać uprawnienia do grupy zasobów, która zawiera kilka pulpitów nawigacyjnych lub do poszczególnych pulpitów nawigacyjnych. Na przykład możesz zdecydować, że grupa użytkowników powinna mieć ograniczone uprawnienia w całej subskrypcji, ale większy dostęp do określonego pulpitu nawigacyjnego. Przypisz tych użytkowników do roli dla tego pulpitu nawigacyjnego.

## <a name="publish-dashboard"></a>Publikowanie pulpitu nawigacyjnego

Załóżmy, że konfigurujesz pulpit nawigacyjny, który chcesz udostępnić grupie użytkowników w ramach subskrypcji. W poniższych krokach pokazano, jak udostępnić pulpit nawigacyjny grupie o nazwie Menedżerowie magazynu. Możesz nazwać swoją grupę, co chcesz. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w usłudze Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Przed przypisaniem dostępu należy opublikować pulpit nawigacyjny.

1. Na pulpicie nawigacyjnym wybierz pozycję **Udostępnij**.

    ![wybieranie udziału dla pulpitu nawigacyjnego](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. W **obszarze Udostępnianie + kontrola dostępu**wybierz pozycję **Publikuj**.

    ![publikowanie pulpitu nawigacyjnego](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Domyślnie udostępnianie publikuje pulpit nawigacyjny w grupie zasobów o nazwie **pulpity nawigacyjne**. Aby wybrać inną grupę zasobów, wyczyść to pole wyboru.

Pulpit nawigacyjny został opublikowany. Jeśli uprawnienia dziedziczone z subskrypcji są odpowiednie, nie trzeba nic więcej robić. Inni użytkownicy w organizacji mogą uzyskiwać dostęp do pulpitu nawigacyjnego i modyfikować go na podstawie roli na poziomie subskrypcji.

## <a name="assign-access-to-a-dashboard"></a>Przypisywanie dostępu do pulpitu nawigacyjnego

Można przypisać grupę użytkowników do roli dla tego pulpitu nawigacyjnego.

1. Po opublikowaniu pulpitu nawigacyjnego wybierz opcję **Udostępnij** lub **Cofnij udostępnianie,** aby uzyskać dostęp do **kontroli udostępniania + dostępu**.

1. W **obszarze Udostępnianie + kontrola dostępu**wybierz pozycję **Zarządzaj użytkownikami**.

    ![zarządzanie użytkownikami dla pulpitu nawigacyjnego](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Wybierz **przypisania ról,** aby wyświetlić istniejących użytkowników, którzy są już przypisane do roli dla tego pulpitu nawigacyjnego.

1. Aby dodać nowego użytkownika lub grupę, wybierz pozycję **Dodaj,** a następnie **dodaj przypisanie roli**.

    ![dodawanie użytkownika w celu uzyskania dostępu do pulpitu nawigacyjnego](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Wybierz rolę, która reprezentuje uprawnienia do przyznania. W tym przykładzie wybierz pozycję **Współautor**.

1. Wybierz użytkownika lub grupę, która ma zostać przypisana do roli. Jeśli nie widzisz na liście użytkownika lub grupy, której szukasz, użyj pola wyszukiwania. Lista dostępnych grup zależy od grup utworzonych w usłudze Active Directory.

1. Po zakończeniu dodawania użytkowników lub grup wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać listę ról, zobacz [Wbudowane role zasobów platformy Azure](../role-based-access-control/built-in-roles.md).
* Aby dowiedzieć się więcej o zarządzaniu zasobami, zobacz [Zarządzanie zasobami platformy Azure przy użyciu witryny Azure portal](resource-group-portal.md).

