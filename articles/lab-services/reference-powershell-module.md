---
title: Moduł programu PowerShell dla Azure Lab Services | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące modułu programu PowerShell, który ułatwia zarządzanie artefaktami w Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6f5809dcf981a1ecf30cda06af03d2b8a06d3694
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078826"
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
1. Zainstaluj [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) , jeśli nie istnieje na Twojej maszynie. 
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
