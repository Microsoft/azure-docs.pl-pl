---
title: Publikowanie wbudowanych aplikacji w systemie Windows Virtual Desktop — Azure
description: Jak publikować wbudowane aplikacje na pulpicie wirtualnym systemu Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 68db8e9d50dff0bb4580476bae990374830a2a1f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445826"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publikowanie wbudowanych aplikacji w systemie Windows Virtual Desktop

>[!IMPORTANT]
>Ta zawartość dotyczy pulpitu wirtualnego systemu Windows z Azure Resource Manager obiektów pulpitu wirtualnego systemu Windows. Jeśli używasz pulpitu wirtualnego systemu Windows (klasycznego) bez Azure Resource Manager obiektów, zobacz [ten artykuł](./virtual-desktop-fall-2019/publish-apps-2019.md).

W tym artykule przedstawiono sposób publikowania aplikacji w środowisku pulpitu wirtualnego systemu Windows.

## <a name="publish-built-in-apps"></a>Publikowanie wbudowanych aplikacji

Aby opublikować wbudowaną aplikację:

1. Nawiąż połączenie z jedną z maszyn wirtualnych w puli hostów.
2. Pobierz **PackageFamilyName** aplikacji, którą chcesz opublikować, postępując zgodnie z instrukcjami w [tym artykule](/powershell/module/appx/get-appxpackage).
3. Na koniec Uruchom następujące polecenie cmdlet `<PackageFamilyName>` zastąpione przez **PackageFamilyName** Znalezione w poprzednim kroku:

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Pulpit wirtualny systemu Windows obsługuje tylko publikowanie aplikacji z lokalizacjami instalacji, które zaczynają się od `C:\Program Files\WindowsApps` .

## <a name="update-app-icons"></a>Aktualizowanie ikon aplikacji

Po opublikowaniu aplikacji będzie ona mieć domyślną ikonę aplikacji systemu Windows zamiast zwykłego obrazu ikony. Aby zmienić ikonę ikony na zwykłą, Umieść obraz ikony w udziale sieciowym. Obsługiwane formaty obrazów to PNG, BMP, GIF, JPG, JPEG i ICO.

## <a name="publish-microsoft-edge"></a>Publikuj Microsoft Edge

Proces używany do publikowania przeglądarki Microsoft Edge jest nieco inny niż proces publikowania dla innych aplikacji. Aby opublikować program Microsoft Edge z domyślną stroną główną, Uruchom to polecenie cmdlet:

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak skonfigurować źródła danych, aby zorganizować, jak aplikacje są wyświetlane dla użytkowników w obszarze [Dostosowywanie źródła dla użytkowników pulpitu wirtualnego systemu Windows](customize-feed-for-virtual-desktop-users.md).
- Dowiedz się więcej na temat funkcji dołączania aplikacji MSIX w obszarze [Konfiguracja dołączania aplikacji MSIX](app-attach.md).

