---
title: Publikowanie wbudowanych aplikacji w systemie Windows Virtual Desktop — Azure
description: Jak publikować wbudowane aplikacje na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7a453ef44a1ede86290f4130a6147eaaad09fa97
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214149"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publikowanie wbudowanych aplikacji w systemie Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość dotyczy wersji 2019, która nie Azure Resource Manager obsługuje obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektami pulpitu wirtualnego systemu Windows wprowadzonymi w ramach aktualizacji wiosną 2020, zobacz [ten artykuł](../publish-apps.md).

W tym artykule przedstawiono sposób publikowania aplikacji w środowisku pulpitu wirtualnego systemu Windows.

## <a name="publish-built-in-apps"></a>Publikowanie wbudowanych aplikacji

Aby opublikować wbudowaną aplikację:

1. Nawiąż połączenie z jedną z maszyn wirtualnych w puli hostów.
2. Pobierz **PackageFamilyName** aplikacji, którą chcesz opublikować, postępując zgodnie z instrukcjami w [tym artykule](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Na koniec Uruchom następujące polecenie cmdlet `<PackageFamilyName>` zastąpione przez **PackageFamilyName** Znalezione w poprzednim kroku:

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Pulpit wirtualny systemu Windows obsługuje tylko publikowanie aplikacji z lokalizacjami instalacji, które zaczynają się od `C:\Program Files\Windows Apps` .

## <a name="update-app-icons"></a>Aktualizowanie ikon aplikacji

Po opublikowaniu aplikacji będzie ona mieć domyślną ikonę aplikacji systemu Windows zamiast zwykłego obrazu ikony. Aby zmienić ikonę ikony na zwykłą, Umieść obraz ikony w udziale sieciowym. Obsługiwane formaty obrazów to PNG, BMP, GIF, JPG, JPEG i ICO.

## <a name="publish-microsoft-edge"></a>Publikuj Microsoft Edge

Proces używany do publikowania przeglądarki Microsoft Edge jest nieco inny niż proces publikowania dla innych aplikacji. Aby opublikować program Microsoft Edge z domyślną stroną główną, Uruchom to polecenie cmdlet:

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge"
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować źródła danych, aby zorganizować, jak aplikacje są wyświetlane dla użytkowników w obszarze [Dostosowywanie źródła dla użytkowników pulpitu wirtualnego systemu Windows](customize-feed-virtual-desktop-users-2019.md).
- Dowiedz się więcej na temat funkcji dołączania aplikacji MSIX w obszarze [Konfiguracja dołączania aplikacji MSIX](../app-attach.md).

