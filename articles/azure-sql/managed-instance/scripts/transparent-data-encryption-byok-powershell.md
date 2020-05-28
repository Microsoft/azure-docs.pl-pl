---
title: 'PowerShell: Włączanie polecenia "Przenieś jako własny klucz" (BYOK) TDE'
titleSuffix: Azure SQL Managed Instance
description: Dowiedz się, jak skonfigurować wystąpienie zarządzane usługi Azure SQL, aby rozpocząć korzystanie z funkcji "Transparent Data Encryption BYOK" (TDE) na potrzeby szyfrowania przy użyciu programu PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: cff98c0acfe06a9dbf9e3d7c7dae1b2411823d28
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051794"
---
# <a name="transparent-data-encryption-tde-in-a-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transparent Data Encryption (TDE) w wystąpieniu zarządzanym SQL przy użyciu własnego klucza z Azure Key Vault
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Ten przykład skryptu programu PowerShell służy do konfigurowania Transparent Data Encryption (TDE) z kluczem zarządzanym przez klienta dla wystąpienia zarządzanego usługi Azure SQL przy użyciu klucza z Azure Key Vault. Jest to często określane jako Bring Your Own Key scenariusz dla TDE. Aby dowiedzieć się więcej na temat TDE z kluczem zarządzanym przez klienta, zobacz [TDE Bring Your Own Key do usługi Azure SQL](../../database/transparent-data-encryption-byok-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejące wystąpienie zarządzane SQL. Zobacz [Używanie programu PowerShell do tworzenia wystąpienia zarządzanego Azure SQL](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Korzystanie z programu PowerShell lokalnie lub przy użyciu Azure Cloud Shell wymaga AZ PowerShell 2.3.2 lub nowszej wersji. Jeśli musisz przeprowadzić uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps)lub uruchamianie poniższego przykładowego skryptu w celu zainstalowania modułu dla bieżącego użytkownika:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.

## <a name="sample-scripts"></a>Przykładowe skrypty

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Dodatkowe przykłady skryptów programu PowerShell wystąpienia zarządzanego SQL można znaleźć w [skryptach programu PowerShell wystąpienia zarządzanego Azure SQL](../../database/powershell-script-content-guide.md).
