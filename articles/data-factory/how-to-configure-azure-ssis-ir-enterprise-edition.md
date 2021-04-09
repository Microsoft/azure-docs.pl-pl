---
title: Udostępnianie wersji Enterprise Edition dla Azure-SSIS Integration Runtime
description: W tym artykule opisano funkcje wersji Enterprise Edition dotyczące Azure-SSIS Integration Runtime i sposobu ich aprowizacji
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: f700729c2d059648b1c3a7e451526aefcb436818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387550"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Udostępnianie wersji Enterprise Edition dla Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Wydanie Enterprise Azure-SSIS Integration Runtime umożliwia korzystanie z następujących funkcji zaawansowanych i Premium:
-   Składniki przechwytywania zmian danych
-   Łączniki Oracle, Teradata i SAP BW
-   Łączniki i przekształcenia usługi SQL Server Analysis Services (SSAS) i Azure Analysis Services (AAS)
-   Przekształceń rozmytego grupowania i wyszukiwania rozmytego
-   Przekształceń okresu ekstrakcji i wyszukiwania terminów

Niektóre z tych funkcji wymagają zainstalowania dodatkowych składników w celu dostosowania Azure-SSIS IR. Aby uzyskać więcej informacji na temat instalowania dodatkowych składników, zobacz [Konfiguracja niestandardowa środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Funkcje przedsiębiorstwa

| **Funkcje przedsiębiorstwa** | **Znajduje** |
|---|---|
| Składniki przechwytywania zmian | Źródło przechwytywania zmian, zadanie sterowania i przekształcenie rozdzielacza są preinstalowane w Azure-SSIS IR Enterprise Edition. Aby połączyć się z bazą danych Oracle, należy również zainstalować projektanta i usługę przechwytywania zmian oprogramowania na innym komputerze. |
| Łączniki Oracle | Menedżer połączeń Oracle, źródło i miejsce docelowe są preinstalowane na Azure-SSIS IR Enterprise Edition. Konieczne jest również zainstalowanie sterownika programu Oracle Calling Interface (OCI) i w razie potrzeby skonfigurowanie na Azure-SSIS IR środowiska transportowego Oracle Transport Network (TNS). Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Łączniki programu Teradata | Należy zainstalować Menedżera połączeń programu Teradata, źródło i lokalizację docelową, a także sterownik API programu Teradata Parallel Transporting (TPT), a w Azure-SSIS IR Enterprise Edition. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Łączniki SAP BW | Menedżer połączeń SAP BW, źródło i miejsce docelowe są preinstalowane na Azure-SSIS IR Enterprise Edition. Należy również zainstalować sterownik SAP BW na Azure-SSIS IR. Te łączniki obsługują SAP BW 7,0 lub wcześniejszych wersji. Aby nawiązać połączenie z nowszymi wersjami SAP BW lub innych produktów SAP, można zakupić i zainstalować łączniki SAP od niezależnych dostawców oprogramowania na Azure-SSIS IR. Aby uzyskać więcej informacji na temat instalowania dodatkowych składników, zobacz [Konfiguracja niestandardowa środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Składniki Analysis Services               | W systemie Azure-SSIS IR Enterprise Edition są wstępnie zainstalowane docelowe szkolenia modelu wyszukiwania danych, miejsce docelowe przetwarzania wymiarów oraz miejsce docelowe przetwarzania partycji, a także transformacja zapytań wyszukiwania danych. Wszystkie te składniki obsługują SQL Server Analysis Services (SSAS), ale tylko miejsce docelowe przetwarzania partycji obsługuje Azure Analysis Services (AAS). Aby nawiązać połączenie z usługą SSAS, należy również [skonfigurować poświadczenia uwierzytelniania systemu Windows w programie SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Oprócz tych składników, Analysis Services wykonywanie zadania DDL, zadanie przetwarzania Analysis Services i zadanie zapytania wyszukiwania danych również są wstępnie instalowane w Azure-SSIS IR Standard/Enterprise Edition. |
| Przekształceń rozmytego grupowania i wyszukiwania rozmytego  | Przekształcenia rozmyte grupowania i wyszukiwania rozmytego są wstępnie zainstalowane w Azure-SSIS IR Enterprise Edition. Te składniki obsługują zarówno SQL Server, jak i Azure SQL Database do przechowywania danych referencyjnych. |
| Przekształceń okresu ekstrakcji i wyszukiwania terminów | Okresy ekstrakcji i wyszukiwania terminów są wstępnie instalowane w Azure-SSIS IR Enterprise Edition. Te składniki obsługują zarówno SQL Server, jak i Azure SQL Database do przechowywania danych referencyjnych. |

## <a name="instructions"></a>Instrukcje

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Pobierz i zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps).

2.  Podczas aprowizacji lub zmiany konfiguracji Azure-SSIS IR przy użyciu programu PowerShell należy uruchomić polecenie `Set-AzDataFactoryV2IntegrationRuntime` with **Enterprise** jako wartość parametru **Edition** przed rozpoczęciem Azure-SSIS IR. Oto przykładowy skrypt:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Następne kroki

-   [Konfiguracja niestandardowa środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Jak opracowywać płatne lub licencjonowane składniki niestandardowe dla środowiska Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)