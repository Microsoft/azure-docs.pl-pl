---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013128"
---
Metoda DefaultAzureCredential w naszej aplikacji opiera się na trzech zmiennych środowiskowych: `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` i `AZURE_TENANT_ID` . Ustaw te zmienne na wartości clientId, clientSecret i tenantId, które zostały zwrócone w kroku "Tworzenie jednostki usługi" przy użyciu `export VARNAME=VALUE` formatu. (Ta metoda ustawia tylko zmienne dla bieżącej powłoki i procesów utworzonych na podstawie powłoki; aby trwale dodać te zmienne do środowiska, Edytuj `/etc/environment ` plik). 

Należy również zapisać nazwę magazynu kluczy jako zmienną środowiskową o nazwie `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````
