---
title: Publikowanie wbudowanych aplikacji w systemie Windows Virtual Desktop (klasyczny) — Azure
description: Jak publikować aplikacje wbudowane w systemie Windows Virtual Desktop (klasyczny).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1b179ac555ea86aff381c1217e834b8d0aa85e8c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561708"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop-classic"></a>Publikowanie wbudowanych aplikacji w systemie Windows Virtual Desktop (klasyczny)

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows (klasycznego), który nie obsługuje Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli próbujesz zarządzać Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows, zobacz [ten artykuł](../publish-apps.md).

W tym artykule przedstawiono sposób publikowania aplikacji w środowisku pulpitu wirtualnego systemu Windows.

## <a name="publish-built-in-apps"></a>Publikowanie wbudowanych aplikacji

Aby opublikować wbudowaną aplikację:

1. Nawiąż połączenie z jedną z maszyn wirtualnych w puli hostów.
2. Pobierz **PackageFamilyName** aplikacji, którą chcesz opublikować, postępując zgodnie z instrukcjami w [tym artykule](/powershell/module/appx/get-appxpackage).
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

