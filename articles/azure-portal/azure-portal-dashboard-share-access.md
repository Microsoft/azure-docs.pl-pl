---
title: Udostępnianie pulpitów nawigacyjnych Azure Portal przy użyciu Role-Based Access Control
description: W tym artykule opisano sposób udostępniania pulpitu nawigacyjnego w Azure Portal przy użyciu Role-Based Access Control.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 89c2b90b12a4fc16dd48571c00ff46dc62168d42
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077885"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Udostępnianie pulpitów nawigacyjnych platformy Azure przy użyciu kontroli dostępu opartej na rolach

Po skonfigurowaniu pulpitu nawigacyjnego można go opublikować i udostępnić innym użytkownikom w organizacji. Możesz zezwolić innym osobom na wyświetlanie pulpitu nawigacyjnego za pomocą [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Przypisz użytkownika lub grupę użytkowników do roli. Ta rola określa, czy użytkownicy mogą wyświetlać i modyfikować opublikowane pulpity nawigacyjne.

Wszystkie opublikowane pulpity nawigacyjne są implementowane jako zasoby platformy Azure. Istnieją one jako elementy do zarządzania w ramach subskrypcji i są zawarte w grupie zasobów. Z perspektywy kontroli dostępu pulpity nawigacyjne nie różnią się od innych zasobów, takich jak maszyna wirtualna lub konto magazynu.

> [!TIP]
> Poszczególne kafelki na pulpicie nawigacyjnym wymuszają własne wymagania dotyczące kontroli dostępu w zależności od zasobów, które są wyświetlane. Pulpit nawigacyjny można udostępnić w szerokim czasie, jednocześnie chroniąc dane na poszczególnych kafelkach.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Informacje o kontroli dostępu dla pulpitów nawigacyjnych

Za pomocą Role-Based Access Control (RBAC) można przypisywać użytkowników do ról na trzech różnych poziomach zakresu:

* subskrypcja
* grupa zasobów
* zasób

Przypisane uprawnienia dziedziczą z subskrypcji do zasobu. Opublikowany pulpit nawigacyjny jest zasobem. Być może masz już przypisanych użytkowników do ról dla subskrypcji, która jest stosowana dla opublikowanego pulpitu nawigacyjnego.

Załóżmy, że masz subskrypcję platformy Azure i różnym członkom zespołu przypisano role *właściciela*, *współautora*lub *czytelnika* subskrypcji. Użytkownicy będący właścicielami lub współautorami mogą wyświetlać, przeglądać, tworzyć, modyfikować i usuwać pulpity nawigacyjne w ramach subskrypcji. Użytkownicy, którzy są czytelnicy mogą wyświetlać listy pulpitów nawigacyjnych, ale nie mogą ich modyfikować ani usuwać. Użytkownicy z dostępem do czytnika mogą wprowadzać zmiany lokalne do opublikowanego pulpitu nawigacyjnego, na przykład w przypadku rozwiązywania problemu, ale nie mogą publikować tych zmian z powrotem na serwerze. Mogą oni utworzyć prywatną kopię pulpitu nawigacyjnego.

Można również przypisać uprawnienia do grupy zasobów zawierającej kilka pulpitów nawigacyjnych lub do poszczególnych pulpitów nawigacyjnych. Na przykład można zdecydować, że grupa użytkowników powinna mieć ograniczone uprawnienia w ramach subskrypcji, ale większy dostęp do określonego pulpitu nawigacyjnego. Przypisz tych użytkowników do roli dla tego pulpitu nawigacyjnego.

## <a name="publish-dashboard"></a>Publikowanie pulpitu nawigacyjnego

Załóżmy, że zostanie skonfigurowany pulpit nawigacyjny, który ma być współużytkowany z grupą użytkowników w ramach subskrypcji. Poniższe kroki pokazują, jak udostępnić pulpit nawigacyjny do grupy o nazwie menedżerowie magazynu. Możesz nazwać swoją grupę. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami w Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Przed przypisaniem dostępu należy opublikować pulpit nawigacyjny.

1. Na pulpicie nawigacyjnym wybierz pozycję **Udostępnij**.

    ![Wybieranie udziału dla pulpitu nawigacyjnego](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. W obszarze **udostępnianie i kontrola dostępu**wybierz pozycję **Publikuj**.

    ![Publikowanie pulpitu nawigacyjnego](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Domyślnie udostępnianie publikuje pulpit nawigacyjny w grupie zasobów o nazwie **pulpity nawigacyjne**. Aby wybrać inną grupę zasobów, usuń zaznaczenie pola wyboru.

Pulpit nawigacyjny jest teraz opublikowany. Jeśli uprawnienia Odziedziczone z subskrypcji są odpowiednie, nie musisz robić nic więcej. Inni użytkownicy w organizacji mogą uzyskiwać dostęp do pulpitu nawigacyjnego i modyfikować go na podstawie ich roli na poziomie subskrypcji.

## <a name="assign-access-to-a-dashboard"></a>Przypisywanie dostępu do pulpitu nawigacyjnego

Możesz przypisać grupę użytkowników do roli dla tego pulpitu nawigacyjnego.

1. Po opublikowaniu pulpitu nawigacyjnego wybierz opcję **Udostępnij** lub Anuluj **udostępnianie** , aby uzyskać dostęp do **udostępniania i kontroli dostępu**.

1. W obszarze **udostępnianie i kontrola dostępu**wybierz pozycję **Zarządzaj użytkownikami**.

    ![Zarządzanie użytkownikami pulpitu nawigacyjnego](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Wybierz **przypisania ról** , aby wyświetlić istniejących użytkowników, którym przypisano już rolę dla tego pulpitu nawigacyjnego.

1. Aby dodać nowego użytkownika lub grupę, wybierz pozycję **Dodaj** , a następnie **Dodaj przypisanie roli**.

    ![Dodawanie użytkownika w celu uzyskania dostępu do pulpitu nawigacyjnego](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Wybierz rolę reprezentującą uprawnienia do udzielenia. Na potrzeby tego przykładu wybierz opcję **współautor**.

1. Wybierz użytkownika lub grupę, która ma zostać przypisana do roli. Jeśli na liście nie widzisz użytkownika lub grupy, której szukasz, użyj pola wyszukiwania. Lista dostępnych grup zależy od grup utworzonych w Active Directory.

1. Po zakończeniu dodawania użytkowników lub grup wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

* Listę ról można znaleźć [w temacie Role wbudowane platformy Azure](../role-based-access-control/built-in-roles.md).
* Aby dowiedzieć się więcej o zarządzaniu zasobami, zobacz [Zarządzanie zasobami platformy Azure przy użyciu Azure Portal](../azure-resource-manager/management/manage-resources-portal.md).