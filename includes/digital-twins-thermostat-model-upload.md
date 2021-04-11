---
author: baanders
description: Dołącz plik, aby przekazać model do wystąpienia usługi Azure Digital bliźniaczych reprezentacji
ms.service: digital-twins
ms.topic: include
ms.date: 3/23/2021
ms.author: baanders
ms.openlocfilehash: 987409f070f34f0fd9ee212fab8cc57e889e0146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950612"
---
Model wygląda następująco:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Aby **przekazać ten model do wystąpienia bliźniaczych reprezentacji**, uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, które przekazuje powyższy model jako wbudowany kod JSON. Można uruchomić polecenie w [Azure Cloud Shell](../articles/cloud-shell/overview.md) w przeglądarce lub na maszynie, jeśli interfejs wiersza polecenia jest [zainstalowany lokalnie](/cli/azure/install-azure-cli).

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

> [!Note]
> Jeśli używasz Cloud Shell w środowisku programu PowerShell, może być konieczne wypróbowanie znaków cudzysłowu w wbudowanych polach JSON, aby ich wartości były analizowane prawidłowo. Oto polecenie przekazanie modelu z tą modyfikacją:
>
> Przekaż model:
> ```azurecli-interactive
> az dt model create --models '{  \"@id\": \"dtmi:contosocom:DigitalTwins:Thermostat;1\",  \"@type\": \"Interface\",  \"@context\": \"dtmi:dtdl:context;2\",  \"contents\": [    {      \"@type\": \"Property\",      \"name\": \"Temperature\",      \"schema\": \"double\"    }  ]}' -n {digital_twins_instance_name}
> ```