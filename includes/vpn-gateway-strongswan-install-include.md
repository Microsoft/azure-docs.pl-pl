---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2d0e171807985deaebe8faa625d6b767c2a3efd7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86218453"
---
Następująca konfiguracja została użyta w poniższych krokach:

- Komputer: Ubuntu Server 18,04
- Zależności: klient strongswan


Użyj następujących poleceń, aby zainstalować wymaganą konfigurację klient strongswan:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Użyj następującego polecenia, aby zainstalować interfejs wiersza polecenia platformy Azure:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Dodatkowe instrukcje dotyczące instalowania interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
