---
title: Szybki Start — tworzenie Azure Analysis Services przy użyciu Azure Analysis Services programu PowerShell | Microsoft Docs
description: W tym przewodniku szybki start opisano sposób tworzenia serwera Azure Analysis Services przy użyciu programu PowerShell
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 737649538aaf82352e27aec6220b13ba355a7a82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89229348"
---
# <a name="quickstart-create-a-server---powershell"></a>Szybki start: tworzenie serwera — PowerShell

W tym przewodniku Szybki start opisano, jak utworzyć serwer usługi Azure Analysis Services przy użyciu programu PowerShell z poziomu wiersza polecenia w ramach subskrypcji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Subskrypcja platformy Azure**: Przejdź do [bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) , aby utworzyć konto.
- **Azure Active Directory**: subskrypcja musi być skojarzona z dzierżawą usługi Azure Active Directory i musisz mieć konto w tym katalogu. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).
- Zainstalowanie programu **Azure PowerShell**. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `Get-Module -ListAvailable Az`. Aby przeprowadzić instalację lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="import-azanalysisservices-module"></a>Importowanie modułu Az.AnalysisServices

Aby utworzyć serwer w ramach subskrypcji, należy użyć modułu [Az.AnalysisServices](/powershell/module/az.analysisservices). Załaduj moduł Az.AnalysisServices do sesji programu PowerShell.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure przy użyciu polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[Grupa zasobów platformy Azure](../azure-resource-manager/management/overview.md) to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy. Podczas tworzenia serwera musisz podać grupę zasobów w ramach subskrypcji. Jeśli nie masz jeszcze grupy zasobów, możesz ją utworzyć teraz przy użyciu polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `myResourceGroup` w regionie Zachodnie stany USA.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Tworzenie serwera

Utwórz nowy serwer przy użyciu polecenia [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver). W poniższym przykładzie utworzono serwer o nazwie myServer w grupie myResourceGroup w regionie Zachodnie stany USA w warstwie D1 (bezpłatnej) i określono użytkownika philipc@adventureworks.com jako administratora serwera.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć serwer z subskrypcji przy użyciu polecenia [Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver). Jeśli będziesz korzystać z innych przewodników Szybki start i samouczków w tej kolekcji, nie usuwaj serwera. Poniższy przykład obejmuje usuwanie serwera utworzonego w poprzednim kroku.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono, jak utworzyć serwer w ramach subskrypcji platformy Azure przy użyciu programu PowerShell. Teraz, po utworzeniu serwera, możesz go zabezpieczyć, konfigurując (opcjonalną) zaporę serwera. Możesz też dodać do serwera podstawowy przykładowy model danych bezpośrednio w portalu. Przykładowy model służy do zdobywania wiedzy na temat konfigurowania ról modelowej bazy danych oraz testowania połączeń klienckich. Aby dowiedzieć się więcej, przejdź do samouczka dotyczącego dodawania przykładowego modelu.

> [!div class="nextstepaction"]
> [Szybki start: Konfigurowanie zapory serwera — Portal](analysis-services-qs-firewall.md)      