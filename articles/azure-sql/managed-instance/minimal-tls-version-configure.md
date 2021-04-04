---
title: Skonfiguruj minimalne wystąpienie zarządzane przez protokół TLS
description: Dowiedz się, jak skonfigurować minimalną wersję protokołu TLS dla wystąpienia zarządzanego
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788404"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Konfigurowanie minimalnej wersji protokołu TLS w usłudze Azure SQL Managed Instance
Ustawienie wersji minimalnej [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) umożliwia klientom kontrolowanie wersji protokołu TLS używanej przez swoje wystąpienie zarządzane usługi Azure SQL.

Obecnie obsługujemy protokół TLS w wersjach 1.0, 1.1 i 1.2. Ustawienie minimalnej wersji protokołu TLS zapewnia obsługę kolejnych, nowszych wersji protokołu TLS. Na przykład wybranie wersji TLS większej niż 1,1. oznacza, że akceptowane będą tylko połączenia nawiązywane za pomocą protokołu TLS 1.1 i 1.2, a protokół TLS 1.0 będzie odrzucany. Zalecamy ustawienie minimalnej wersji protokołu TLS na 1.2, ponieważ zawiera ona poprawki dla luk w zabezpieczeniach znalezionych w poprzednich wydaniach i jest najwyższą wersją protokołu TLS obsługiwaną w usłudze Azure SQL Managed Instance. Wcześniej należy jednak przeprowadzić testy potwierdzające zgodność aplikacji z tą wersją.

W przypadku klientów mających aplikacje korzystające ze starszych wersji protokołu TLS zalecamy ustawienie minimalnej wersji protokołu TLS zgodnie z wymaganiami aplikacji. W przypadku klientów korzystających z aplikacji do łączenia się z nieszyfrowanym połączeniem nie zaleca się stosowania żadnej minimalnej wersji protokołu TLS. 

Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące protokołu TLS dotyczące SQL Database łączności](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Po ustawieniu minimalnej wersji protokołu TLS próby logowania z klientów korzystających ze starszej wersji protokołu TLS niż minimalna wersja protokołu TLS serwera zakończą się niepowodzeniem z powodu następującego błędu:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Ustawianie minimalnej wersji protokołu TLS za pośrednictwem programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne. Poniższy skrypt wymaga [modułu Azure PowerShell](/powershell/azure/install-az-ps).

Poniższy skrypt programu PowerShell przedstawia sposób `Get` i `Set` Właściwość **minimalnej wersji protokołu TLS** na poziomie wystąpienia:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Ustawianie minimalnej wersji protokołu TLS za pośrednictwem interfejsu wiersza polecenia platformy Azure

> [!IMPORTANT]
> Wszystkie skrypty w tej sekcji wymagają [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Interfejs wiersza polecenia platformy Azure w powłoce bash

Poniższy skrypt interfejsu wiersza polecenia przedstawia sposób zmiany ustawienia **minimalnej wersji protokołu TLS** w bash Shell:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```