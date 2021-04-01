---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/30/2020
ms.author: glenga
ms.openlocfilehash: f89383c1b7d0ccce82f35d91acbe1ff8c902db29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84448918"
---
::: zone pivot="programming-language-python"  
## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Tworzenie i aktywowanie środowiska wirtualnego

W odpowiednim folderze Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv` . Upewnij się, że używasz języka Python 3,8, 3,7 lub 3,6, który jest obsługiwany przez Azure Functions.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Jeśli środowisko Python nie zainstalowało pakietu venv na dystrybucji systemu Linux, uruchom następujące polecenie:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Wszystkie kolejne polecenia są uruchamiane w tym aktywowanym środowisku wirtualnym.   
::: zone-end
