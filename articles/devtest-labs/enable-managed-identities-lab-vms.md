---
title: Włącz zarządzane tożsamości na maszynach wirtualnych laboratorium w Azure DevTest Labs
description: W tym artykule pokazano, jak właściciel laboratorium może włączyć tożsamości zarządzane przypisane przez użytkownika na maszynach wirtualnych laboratorium.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898437"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Włącz tożsamości zarządzane przypisane przez użytkownika na maszynach wirtualnych laboratorium w Azure DevTest Labs
Jako właściciel laboratorium można włączyć tożsamości zarządzane przypisane przez użytkownika na maszynach wirtualnych laboratorium (VM) w Azure DevTest Labs.

Tożsamość zarządzana może służyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie Azure Active Directory (AD), w tym Key Vault, bez przekazywania poświadczeń w kodzie. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Dzięki tej funkcji użytkownicy laboratorium mogą udostępniać zasoby platformy Azure, takie jak Azure SQL Database w kontekście laboratorium. Uwierzytelnianie do zasobu jest traktowana przez samą tożsamość. Po skonfigurowaniu każda istniejąca/nowo utworzona maszyna wirtualna laboratorium zostanie włączona z tą tożsamością. Użytkownicy laboratorium mogą uzyskać dostęp do zasobów po zalogowaniu się na ich maszynach.

> [!NOTE]
> Można dodać wiele tożsamości zarządzanych przypisanych przez użytkownika, które mają być włączone na maszynach wirtualnych laboratorium.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Aby dodać tożsamość zarządzaną przypisaną przez użytkownika dla maszyn wirtualnych laboratorium, wykonaj następujące kroki:

1. [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika w Twojej subskrypcji](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Przejdź do strony **Konfiguracja i zasady** dla laboratorium.
2. Wybierz pozycję **tożsamość (wersja zapoznawcza)** w menu po lewej stronie.
3. Wybierz kartę **maszyna wirtualna** .
4. Wybierz pozycję **Dodaj** , aby wybrać istniejącą tożsamość z wstępnie wypełnionej listy rozwijanej. 

    ![Przycisk Dodaj tożsamość](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Wybierz istniejącą **tożsamość zarządzaną przez użytkownika** z listy rozwijanej, a następnie wybierz **przycisk OK**. 

    ![Dodaj tożsamość](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Korzystanie z interfejsu API

1.  Po utworzeniu tożsamości Zanotuj identyfikator zasobu tożsamości. Powinien wyglądać podobnie do poniższego przykładu: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Uruchom metodę PUT HTTPS, aby dodać nowy zasób **servicerunner** do laboratorium, jak pokazano w poniższym przykładzie. 

    Zasób modułu uruchamiającego usługi jest zasobem serwera proxy, który służy do zarządzania tożsamościami zarządzanymi w DevTest Labs i ich kontrolowania. Nazwa modułu uruchamiającego usługi może być dowolną prawidłową nazwą, ale zalecamy użycie nazwy zarządzanego zasobu tożsamości.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md).







