---
title: Używanie Access Control opartych na rolach dla StorSimple | Microsoft Docs
description: Opisuje sposób korzystania z funkcji kontroli dostępu opartej na rolach (Azure RBAC) w kontekście StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 38500edeca2241bfa9ab093e037af18159994b02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87920419"
---
# <a name="role-based-access-control-for-storsimple"></a>Access Control oparte na rolach dla StorSimple

Ten artykuł zawiera krótki opis sposobu używania kontroli dostępu opartej na rolach (Azure RBAC) na urządzeniu StorSimple. RBAC oferuje precyzyjne zarządzanie dostępem na platformie Azure. Za pomocą RBAC Udziel dostępu StorSimple użytkownikom w celu uzyskania dostępu do swoich zadań, a nie udzielaj innym osobom nieograniczony dostęp. Aby uzyskać więcej informacji na temat podstaw zarządzania dostępem na platformie Azure, zobacz Wprowadzenie do [Access Control opartej na rolach w Azure Portal](../role-based-access-control/overview.md).

Ten artykuł dotyczy urządzeń z serii StorSimple 8000 z uruchomioną aktualizacją 3,0 lub nowszą w Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-roles-for-storsimple"></a>Role platformy Azure dla usługi StorSimple

Kontrolę RBAC można przypisać na podstawie ról. Role zapewniają pewne poziomy uprawnień na podstawie dostępnych zasobów w środowisku. Istnieją dwa typy ról, z których mogą wybierać użytkownicy StorSimple: wbudowane lub niestandardowe.

* **Wbudowane role** — wbudowana rola może być właścicielem, współautorem, czytelnikem lub administratorem dostępu użytkowników. Aby uzyskać więcej informacji, zobacz [wbudowane role dla Access Control opartej na rolach platformy Azure](../role-based-access-control/built-in-roles.md).

* **Role niestandardowe** — jeśli wbudowane role nie odpowiadają Twoim potrzebom, możesz utworzyć role niestandardowe platformy Azure dla StorSimple. Aby utworzyć rolę niestandardową platformy Azure, Zacznij od wbudowanej roli, Edytuj ją, a następnie zaimportuj ją z powrotem w środowisku. Pobieranie i przekazywanie roli jest zarządzane przy użyciu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie ról niestandardowych dla Access Control opartej na rolach](../role-based-access-control/custom-roles.md).

Aby wyświetlić różne role dostępne dla użytkownika urządzenia StorSimple w Azure Portal, przejdź do usługi StorSimple Menedżer urządzeń, a następnie przejdź do pozycji **Kontrola dostępu (IAM) > role**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Utwórz rolę niestandardową dla administratora infrastruktury StorSimple

W poniższym przykładzie zaczynamy od wbudowanego **czytnika** ról, który umożliwia użytkownikom wyświetlanie wszystkich zakresów zasobów, ale nie do ich edytowania ani tworzenia nowych. Następnie ta rola zostanie rozszerzona w celu utworzenia nowej roli niestandardowej administratora infrastruktury StorSimple. Ta rola jest przypisywana do użytkowników, którzy mogą zarządzać infrastrukturą dla urządzeń StorSimple.

1. Uruchom program Windows PowerShell jako administrator.

2. Loguje się do platformy Azure.

    `Connect-AzAccount`

3. Wyeksportuj rolę czytnika jako szablon JSON na komputerze.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Otwórz plik JSON w programie Visual Studio. Zobaczysz, że typowa rola platformy Azure obejmuje trzy główne sekcje, akcje **,** **noactions**i **AssignableScopes**.

    W sekcji **Akcja** zostaną wyświetlone wszystkie dozwolone operacje dla tej roli. Każda akcja jest przypisana z dostawcy zasobów. W przypadku administratora infrastruktury StorSimple Użyj `Microsoft.StorSimple` dostawcy zasobów.

    Użyj programu PowerShell, aby wyświetlić wszystkich dostawców zasobów dostępnych i zarejestrowanych w ramach subskrypcji.

    `Get-AzResourceProvider`

    Możesz również sprawdzić wszystkie dostępne polecenia cmdlet programu PowerShell, aby zarządzać dostawcami zasobów.

    W **sekcjach** noactions są wyświetlane wszystkie akcje ograniczone dla określonej roli platformy Azure. W tym przykładzie żadne akcje nie są ograniczone.
    
    W obszarze **AssignableScopes**są wyświetlane identyfikatory subskrypcji. Upewnij się, że rola platformy Azure zawiera jawny Identyfikator subskrypcji, w której jest używany. Jeśli nie określono prawidłowego identyfikatora subskrypcji, nie będzie można zaimportować roli w ramach subskrypcji.

    Edytuj plik, pamiętając o powyższych kwestiach.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Zaimportuj rolę niestandardową platformy Azure z powrotem do środowiska.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Ta rola powinna teraz pojawić się na liście ról w bloku **Kontrola dostępu** .

![Wyświetlanie ról platformy Azure](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Aby uzyskać więcej informacji, przejdź do [ról niestandardowych](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Przykładowe dane wyjściowe dla tworzenia roli niestandardowej za pomocą programu PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Dodawanie użytkowników do roli niestandardowej

Dostęp udzielany jest w ramach zasobu, grupy zasobów lub subskrypcji, która jest zakresem przypisania roli. Przy udostępnianiu dostępu należy pamiętać, że dostęp udzielony w węźle nadrzędnym jest dziedziczony przez element podrzędny. Aby uzyskać więcej informacji, przejdź do [kontroli dostępu opartej na rolach (Azure RBAC)](../role-based-access-control/overview.md).

1. Przejdź do **kontroli dostępu (IAM)**. Kliknij pozycję **+ Dodaj** w bloku kontrola dostępu.

    ![Dodawanie dostępu do roli platformy Azure](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Wybierz rolę, którą chcesz przypisać, w tym przypadku jest to **administrator infrastruktury StorSimple**.

3. Wybierz użytkownika, grupę lub aplikację w katalogu, którym chcesz udzielić dostępu. Możesz przeszukiwać katalog przy użyciu nazw wyświetlanych, adresów e-mail i identyfikatorów obiektów.

4. Wybierz pozycję **Zapisz** , aby utworzyć przypisanie.

    ![Dodawanie uprawnień do roli platformy Azure](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

**Dodawanie powiadomienia użytkownika** śledzi postęp. Po pomyślnym dodaniu użytkownika zostanie zaktualizowana lista użytkowników w kontroli dostępu.

## <a name="view-permissions-for-the-custom-role"></a>Wyświetl uprawnienia dla roli niestandardowej

Po utworzeniu tej roli można wyświetlić uprawnienia skojarzone z tą rolą w Azure Portal.

1. Aby wyświetlić uprawnienia skojarzone z tą rolą, przejdź do pozycji **Kontrola dostępu (IAM) > role > administratorem infrastruktury StorSimple**. Zostanie wyświetlona lista użytkowników w tej roli.

2. Wybierz administratora infrastruktury StorSimple, a następnie kliknij pozycję **uprawnienia**.

    ![Wyświetl uprawnienia dla roli administratora StorSimple infrastruktura](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Zostaną wyświetlone uprawnienia skojarzone z tą rolą.

    ![Wyświetl użytkowników w roli administratora StorSimple infrastruktura](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Następne kroki

Informacje o sposobie [przypisywania ról niestandardowych dla użytkowników wewnętrznych i zewnętrznych](../role-based-access-control/role-assignments-external-users.md).
