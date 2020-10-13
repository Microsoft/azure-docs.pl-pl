---
title: Korzystanie z tożsamości zarządzanych przez platformę Azure do tworzenia środowisk w DevTest Labs | Microsoft Docs
description: Dowiedz się, jak używać tożsamości zarządzanych na platformie Azure, aby wdrażać środowiska w laboratorium w Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e5bac4210afee6db1c7617dac1cd6d2ff9149439
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88718984"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Używanie tożsamości zarządzanych przez platformę Azure do wdrażania środowisk w laboratorium 

Jako właściciel laboratorium możesz użyć tożsamości zarządzanej, aby wdrażać środowiska w laboratorium. Ta funkcja jest przydatna w scenariuszach, w których środowisko zawiera lub ma odwołania do zasobów platformy Azure, takich jak magazyny kluczy, udostępnione Galerie obrazów i sieci spoza grupy zasobów środowiska. Umożliwia tworzenie środowisk piaskownicy, które nie są ograniczone do grupy zasobów tego środowiska.

> [!NOTE]
> Obecnie obsługiwana jest pojedyncza tożsamość przypisana przez użytkownika dla laboratorium. 

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz, Wyświetl, Usuń lub Przypisz rolę do zarządzanej tożsamości przypisanej przez użytkownika przy użyciu Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Upewnij się, że zarządzana tożsamość została utworzona w tym samym regionie i w ramach subskrypcji. Tożsamość zarządzana nie musi znajdować się w tej samej grupie zasobów.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal

W tej sekcji jako właściciel laboratorium Użyj Azure Portal, aby dodać tożsamość zarządzaną przez użytkownika do laboratorium. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wyszukaj **DevTest Labs**.
1. Z listy laboratoriów wybierz odpowiednie laboratorium.
1. Wybierz pozycję **Konfiguracja i zasady**  ->  **tożsamość (wersja zapoznawcza)**. 
1. Aby dodać tożsamość przypisaną do użytkownika, wybierz kartę **przypisane przez użytkownika** .
1. Naciśnij przycisk **Dodaj** .
1. Wybierz z listy rozwijanej istniejącego użytkownika i/lub dostęp do niego.
 
    ![Dodawanie tożsamości zarządzanej przez użytkownika](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Naciśnij pozycję **Zapisz** w górnej części strony.

    Po zapisaniu laboratorium będzie używać tej tożsamości podczas wdrażania wszystkich środowisk laboratoryjnych. Możesz również uzyskać dostęp do zasobu tożsamości na platformie Azure, wybierając tożsamość z listy. 

Właściciel laboratorium nie musi wykonywać żadnych specjalnych czynności podczas wdrażania środowiska, o ile tożsamość dodana do laboratorium ma uprawnienia do zasobów zewnętrznych, do których środowisko musi uzyskać dostęp. 

Aby zmienić tożsamość zarządzaną przez użytkownika, która została przypisana do laboratorium, najpierw usuń tożsamość dołączoną do laboratorium, a następnie Dodaj kolejną do laboratorium. Aby usunąć tożsamość dołączoną do laboratorium, wybierz pozycję **... (wielokropek)**, a następnie kliknij przycisk **Usuń**. 

## <a name="use-api"></a>Korzystanie z interfejsu API

1. Po utworzeniu tożsamości Zanotuj identyfikator zasobu tej tożsamości. Powinien wyglądać podobnie do poniższego przykładu: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Wykonaj metodę PUT https, aby dodać nowy `ServiceRunner` zasób do laboratorium podobnego do poniższego przykładu. Zasób modułu uruchamiającego usługi jest zasobem serwera proxy, który służy do zarządzania tożsamościami zarządzanymi w DevTest Labs i ich kontrolowania. Nazwa modułu uruchamiającego usługi może być dowolną prawidłową nazwą, ale zalecamy użycie nazwy zarządzanego zasobu tożsamości. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Oto przykład: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Po dodaniu tożsamości przypisanej przez użytkownika do laboratorium usługa Azure DevTest Labs będzie używać jej podczas wdrażania Azure Resource Manager środowiska. Na przykład, jeśli potrzebujesz szablonu Menedżer zasobów, aby uzyskać dostęp do zewnętrznego obrazu galerii obrazów udostępnionych, upewnij się, że tożsamość dodana do laboratorium ma minimalne wymagane uprawnienia do zasobu galerii obrazów udostępnionych. 
