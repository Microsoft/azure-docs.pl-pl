---
title: Moduł programu PowerShell dla Azure Lab Services | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące modułu programu PowerShell, który ułatwia zarządzanie artefaktami w Azure Lab Services.
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
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73609400"
---
# <a name="azlabservices-powershell-module-preview"></a>Moduł programu PowerShell Az.LabServices (wersja zapoznawcza)
AZ. LabServices to moduł programu PowerShell, który upraszcza zarządzanie usługami laboratorium platformy Azure. Udostępnia funkcje umożliwiające tworzenie, wysyłanie zapytań, aktualizowanie i usuwanie kont laboratorium, laboratoriów, maszyn wirtualnych i obrazów. Aby uzyskać więcej informacji na temat tego modułu, zobacz [stronę główną AZ. LabServices w witrynie GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Ten moduł jest w **wersji zapoznawczej**. 

## <a name="example-command"></a>Przykładowe polecenie
Oto przykład użycia polecenia programu PowerShell, aby zatrzymać wszystkie uruchomione maszyny wirtualne we wszystkich laboratoriach.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Wprowadzenie
1. Zainstaluj [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) , jeśli nie istnieje na Twojej maszynie. 
2. Pobierz [AZ. LabServices. PSM1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) do komputera.
3. Zaimportuj moduł:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Uruchom następujące polecenie, aby wyświetlić listę wszystkich laboratoriów w ramach subskrypcji.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Następne kroki
Zobacz [stronę główną AZ. LabServices w witrynie GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
