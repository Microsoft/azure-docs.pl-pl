---
author: baanders
description: Dołącz plik, aby przekazać model do wystąpienia usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 4/1/2021
ms.author: baanders
ms.openlocfilehash: add49cabaece1187cb9bcda3a94c92feb08b2439
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304305"
---
Model wygląda następująco:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Aby **przekazać ten model do wystąpienia bliźniaczych reprezentacji**, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, które przekazuje powyższy model jako wbudowany kod JSON. Polecenie można uruchomić w [Azure Cloud Shell](../articles/cloud-shell/overview.md) w przeglądarce (Użyj środowiska **bash** ) lub na maszynie, jeśli interfejs wiersza polecenia został [zainstalowany lokalnie](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```