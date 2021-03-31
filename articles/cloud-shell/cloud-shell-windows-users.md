---
title: Azure Cloud Shell dla użytkowników systemu Windows | Microsoft Docs
description: Przewodnik dla użytkowników, którzy nie znają systemów Linux
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 8cc1934cc97ecf821de8644dda45d867b3ca3e85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102508950"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>Program PowerShell w usłudze Azure Cloud Shell dla użytkowników systemu Windows

W maju 2018 zmiany zostały [ogłoszone](https://azure.microsoft.com/blog/pscloudshellrefresh/) w programie PowerShell w Azure Cloud Shell.
Środowisko PowerShell w Azure Cloud Shell teraz uruchamia program [PowerShell Core 6](https://github.com/powershell/powershell) w środowisku systemu Linux.
W przypadku tej zmiany mogą wystąpić pewne różnice w działaniu programu PowerShell w Cloud Shell porównaniu z oczekiwaniami w środowisku programu Windows PowerShell.

## <a name="file-system-case-sensitivity"></a>Rozróżnianie wielkości liter systemu plików

W systemie plików w systemie Windows nie jest rozróżniana wielkość liter, a w systemie Linux system plików uwzględnia wielkość liter.
Wcześniej `file.txt` i `FILE.txt` zostały uznane za ten sam plik, ale teraz są uważane za różne pliki.
W systemie plików należy użyć odpowiedniej wielkości liter `tab-completing` .
W określonych środowiskach programu PowerShell, takich jak `tab-completing` nazwy poleceń cmdlet, parametry i wartości, nie jest rozróżniana wielkość liter.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Aliasy programu Windows PowerShell a narzędzia systemu Linux

Niektóre istniejące aliasy programu PowerShell mają takie same nazwy jak wbudowane polecenia systemu Linux, takie jak,,, `cat` `ls` `sort` `sleep` itd. W programie PowerShell Core 6 aliasy, które kolidują z wbudowanymi poleceniami systemu Linux, zostały usunięte.
Poniżej znajdują się popularne aliasy, które zostały usunięte, a także ich równoważne polecenia:  

|Usunięty alias   |Równoważne polecenie   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>Utrwalanie $HOME

Wcześniej użytkownicy mogą utrwalać skrypty i inne pliki na dysku w chmurze.
Teraz katalog $HOME użytkownika jest również utrwalany między sesjami.

## <a name="powershell-profile"></a>Profil programu PowerShell

Domyślnie profil programu PowerShell użytkownika nie jest tworzony.
Aby utworzyć profil, Utwórz `PowerShell` katalog w obszarze `$HOME/.config` .

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

W obszarze `$HOME/.config/PowerShell` można utworzyć pliki profilów — `profile.ps1` i/lub `Microsoft.PowerShell_profile.ps1` .

## <a name="whats-new-in-powershell-core-6"></a>Co nowego w programie PowerShell Core 6

Aby uzyskać więcej informacji na temat Nowości w programie PowerShell Core 6, zapoznaj się z dokumentacją programu [PowerShell](/powershell/scripting/whats-new/what-s-new-in-powershell-70) i [wprowadzenie za pomocą wpisu w blogu programu PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) .