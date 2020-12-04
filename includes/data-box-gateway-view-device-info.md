---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: ca060e75e50a3e2327fc0516c3cfc9550afbf90f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582763"
---
1. [Nawiąż połączenie z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. `Get-HcsApplianceInfo`Aby uzyskać informacje dotyczące urządzenia, użyj programu.

    W poniższym przykładzie pokazano użycie tego polecenia cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Poniżej przedstawiono tabelę podsumowującą niektóre ważne informacje o urządzeniu:

    | Parametr | Opis |
    |-----------|-------------|
    | FriendlyName                   | Przyjazna nazwa urządzenia skonfigurowana za pomocą lokalnego interfejsu użytkownika sieci Web podczas wdrażania urządzenia. Domyślną przyjazną nazwą jest numer seryjny urządzenia.  |
    | SerialNumber                   | Numer seryjny urządzenia jest unikatowym numerem przypisanym w fabryce.                                                                             |
    | Model                          | Model urządzenia. Model jest wirtualny dla Data Box Gateway.                   |
    | FriendlySoftwareVersion        | Przyjazny ciąg, który odpowiada wersji oprogramowania urządzenia. W przypadku systemu z systemem w wersji zapoznawczej przyjazna wersja oprogramowania byłaby Data Box Edge 1902. |
    | HcsVersion                     | Wersja oprogramowania magazynu HCS uruchomiona na urządzeniu. Na przykład magazynu HCS wersja oprogramowania odpowiadająca Data Box Edge 1902 to 1.4.771.324.            |
    | LocalCapacityInMb              | Całkowita pojemność lokalnego urządzenia w megabitach.                                                                                                        |
    | IsRegistered                   | Ta wartość wskazuje, czy urządzenie jest aktywowane z usługą.                                                                                         |


