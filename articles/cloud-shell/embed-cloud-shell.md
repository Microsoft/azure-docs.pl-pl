---
title: Osadź Azure Cloud Shell | Microsoft Docs
description: Dowiedz się, jak osadzić Azure Cloud Shell.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "60614306"
---
# <a name="embed-azure-cloud-shell"></a>Osadź Azure Cloud Shell

Osadzenie Cloud Shell umożliwia deweloperom i autorom zawartości bezpośrednie otwieranie Cloud Shell z dedykowanego adresu URL [Shell.Azure.com](https://shell.azure.com). Dzięki temu użytkownicy mogą w pełni uzyskać pełne możliwości uwierzytelniania Cloud Shell, narzędzi i aktualnych narzędzi interfejsu wiersza Azure PowerShell polecenia platformy Azure dla użytkowników.

Przycisk o stałym rozmiarze

[![Regularne uruchamianie](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

Przycisk dużego rozmiaru

[![Duże uruchomienie](https://shell.azure.com/images/launchcloudshell@2x.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Porady

Aby zintegrować przycisk uruchamiania Cloud Shell w plikach promocji, Skopiuj następujące elementy:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

KOD HTML do osadzenia Cloud Shell wyskakującej jest poniżej:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Dostosowywanie środowiska

Ustaw określone środowisko powłoki, rozszerzając swój adres URL.

|Środowisko użytkownika   |Adres URL   |
|---|---|
|Ostatnio użyta powłoka   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Następne kroki
[Bash w Cloud Shell Szybki Start](quickstart.md)<br>
[Program PowerShell w Cloud Shell Szybki Start](quickstart-powershell.md)
