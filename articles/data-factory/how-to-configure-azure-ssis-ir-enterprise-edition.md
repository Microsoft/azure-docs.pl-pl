---
title: Aprowizuj wersję Enterprise Edition dla środowiska wykonawczego integracji azure-SSIS
description: W tym artykule opisano funkcje programu Enterprise Edition dla środowiska wykonawczego integracji platformy Azure-SSIS oraz sposób jego aprowizowania
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: a0e35a309fd30a36263b498b6a8e74a312004a99
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606155"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Aprowizuj wersję Enterprise Edition dla środowiska wykonawczego integracji azure-SSIS

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Enterprise Edition środowiska wykonawczego integracji Azure-SSIS umożliwia korzystanie z następujących zaawansowanych i premium funkcji:
-   Zmienianie składników przechwytywania danych (CDC)
-   Złącza Oracle, Teradata i SAP BW
-   Łączniki i przekształcenia usług SQL Server Analysis Services (SSAS) i usługi Azure Analysis Services (AAS)
-   Rozmyte grupowanie i rozmyte transformacje wyszukiwania
-   Wyodrębnianie terminów i przekształcenia wyszukiwania terminów

Niektóre z tych funkcji wymagają zainstalowania dodatkowych składników w celu dostosowania identyfikatora Azure-SSIS IR. Aby uzyskać więcej informacji na temat instalowania dodatkowych składników, zobacz [Ustawienia niestandardowe dla środowiska wykonawczego integracji platformy Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Funkcje dla przedsiębiorstw

| **Funkcje przedsiębiorstwa** | **Opisy** |
|---|---|
| Komponenty CDC | Cdc Source, Control Task i Splitter Transformation są preinstalowane w usłudze Azure-SSIS IR Enterprise Edition. Aby połączyć się z oracle, należy również zainstalować CDC Designer i usługi na innym komputerze. |
| Złącza Oracle | Oracle Connection Manager, Source i Destination są preinstalowane w usłudze Azure-SSIS IR Enterprise Edition. Należy również zainstalować sterownik Oracle Call Interface (OCI) i w razie potrzeby skonfigurować Oracle Transport Network Substrate (TNS), na platformie Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Złącza Teradata | Musisz zainstalować Menedżera połączeń Teradata, Źródło i Miejsce docelowe, a także interfejs API Teradata Parallel Transporter (TPT) i sterownik Teradata ODBC w usłudze Azure-SSIS IR Enterprise Edition. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Złącza SAP BW | Menedżer połączeń SAP BW, źródło i miejsce docelowe są preinstalowane w usłudze Azure-SSIS IR Enterprise Edition. Należy również zainstalować sterownik SAP BW na platformie Azure-SSIS IR. Te łączniki obsługują sap BW 7.0 lub wcześniejszych wersji. Aby połączyć się z nowszymi wersjami sap BW lub innymi produktami SAP, można zakupić i zainstalować łączniki SAP od innych dostawców oprogramowania na platformie Azure-SSIS IR. Aby uzyskać więcej informacji na temat instalowania dodatkowych składników, zobacz [Ustawienia niestandardowe dla środowiska wykonawczego integracji platformy Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Składniki usług Analysis Services               | Miejsce docelowe szkolenia modelu wyszukiwanie danych, miejsce docelowe przetwarzania wymiarów i miejsce docelowe przetwarzania partycji, a także transformacja zapytań wyszukiwania danych są preinstalowane w usłudze Azure-SSIS IR Enterprise Edition. Wszystkie te składniki obsługują usługi SQL Server Analysis Services (SSAS), ale tylko miejsce docelowe przetwarzania partycji obsługuje usługi Azure Analysis Services (AAS). Aby połączyć się z SSAS, należy również [skonfigurować poświadczenia uwierzytelniania systemu Windows w SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Oprócz tych składników usługi Analysis Services wykonują zadanie DDL, zadanie przetwarzania usług Analysis Services i zadanie kwerendy wyszukiwanie danych są również preinstalowane w usłudze Azure-SSIS IR Standard/Enterprise Edition. |
| Rozmyte grupowanie i rozmyte transformacje wyszukiwania  | Przekształcenia grupowania rozmytego i wyszukiwania rozmytego są preinstalowane w usłudze Azure-SSIS IR Enterprise Edition. Te składniki obsługują zarówno SQL Server, jak i usługę Azure SQL Database do przechowywania danych referencyjnych. |
| Wyodrębnianie terminów i przekształcenia wyszukiwania terminów | Transformacje wyodrębniania terminów i wyszukiwania terminów są preinstalowane w usłudze Azure-SSIS IR Enterprise Edition. Te składniki obsługują zarówno SQL Server, jak i usługę Azure SQL Database do przechowywania danych referencyjnych. |

## <a name="instructions"></a>Instrukcje

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Pobierz i zainstaluj [program Azure PowerShell](/powershell/azure/install-az-ps).

2.  Podczas inicjowania obsługi administracyjnej lub ponownego konfigurowania usługi `Set-AzDataFactoryV2IntegrationRuntime` Azure-SSIS IR za pomocą programu PowerShell uruchom z **enterprise** jako wartość dla **parametru Edition** przed uruchomieniem usługi Azure-SSIS IR. Oto przykładowy skrypt:

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

-   [Niestandardowa konfiguracja środowiska wykonawczego integracji azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Jak opracować płatne lub licencjonowane składniki niestandardowe dla środowiska wykonawczego integracji azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)
