---
title: 'PowerShell: Włączanie polecenia "Przenieś jako własny klucz" (BYOK) TDE'
titleSuffix: Azure SQL Managed Instance
description: Dowiedz się, jak skonfigurować wystąpienie zarządzane usługi Azure SQL, aby rozpocząć korzystanie z funkcji "Transparent Data Encryption BYOK" (TDE) na potrzeby szyfrowania przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, sstein
ms.date: 08/25/2020
ms.openlocfilehash: 34a849fde315b45bdb1df577cf26c91f458abd72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323236"
---
# <a name="transparent-data-encryption-in-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent Data Encryption w wystąpieniu zarządzanym SQL przy użyciu własnego klucza z Azure Key Vault

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Ten przykład skryptu programu PowerShell służy do konfigurowania Transparent Data Encryption (TDE) z kluczem zarządzanym przez klienta dla wystąpienia zarządzanego usługi Azure SQL przy użyciu klucza z Azure Key Vault. Jest to często nazywane scenariuszem "BYOK" dla TDE. Aby dowiedzieć się więcej, zobacz [Azure SQL transparent Data Encryption z kluczem zarządzanym przez klienta](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejące wystąpienie zarządzane. [Aby utworzyć wystąpienie zarządzane, zobacz temat Używanie programu PowerShell](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Korzystanie z programu PowerShell lokalnie lub przy użyciu Azure Cloud Shell wymaga Azure PowerShell 2.3.2 lub nowszej wersji. Jeśli musisz przeprowadzić uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps)lub uruchamianie poniższego przykładowego skryptu w celu zainstalowania modułu dla bieżącego użytkownika:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-scripts"></a>Przykładowe skrypty 

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure PowerShell, zobacz [dokumentację dotyczącą Azure PowerShell](/powershell/azure/).

Dodatkowe przykłady skryptów programu PowerShell dla wystąpienia zarządzanego SQL można znaleźć w [skryptach programu PowerShell wystąpienia zarządzanego Azure SQL](../../database/powershell-script-content-guide.md).
