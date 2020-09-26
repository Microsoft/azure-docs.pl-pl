---
title: Przykład inspekcji i zaawansowanej ochrony przed zagrożeniami w interfejsie wiersza polecenia — Azure SQL Database
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure służący do konfigurowania inspekcji i zaawansowanej ochrony przed zagrożeniami w Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: azurecli
ms.topic: sample
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 6791691be976e541c6400e89dde8f892fe50c6dc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336177"
---
# <a name="use-cli-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>Użyj interfejsu wiersza polecenia, aby skonfigurować inspekcję SQL Database i zaawansowaną ochronę przed zagrożeniami

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure konfiguruje inspekcję SQL Database i zaawansowaną ochronę przed zagrożeniami.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.sh "Configure auditing and threat detection")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowe odwołanie

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Opis |
|---|---|
| [AZ SQL DB Audit-Policy](/cli/azure/sql/db/audit-policy) | Ustawia zasady inspekcji dla bazy danych. |
| [AZ SQL DB Threat-Policy](/cli/azure/sql/db/threat-policy) | Ustawia zasady zaawansowanej ochrony przed zagrożeniami w bazie danych. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../../azure-sql/database/az-cli-script-samples-content-guide.md).
