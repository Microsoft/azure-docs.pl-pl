---
title: Instalowanie narzędzi klienckich
description: Zainstaluj azdata, polecenia kubectl, interfejs wiersza polecenia platformy Azure, PSQL, Azure Data Studio (testerzy) i rozszerzenie ARC dla Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ecf0f54913f980d879b562eb4aa8063acf6c4772
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320251"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Instalowanie narzędzi klienta na potrzeby wdrażania usług danych z obsługą usługi Azure Arc i zarządzania nimi

> [!IMPORTANT]
> Jeśli aktualizujesz do nowej wersji comiesięcznej, pamiętaj, aby zaktualizować ją do najnowszej wersji Azure Data Studio, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Narzędzia oraz [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] rozszerzeń i usługi Azure Arc dla Azure Data Studio.

Ten dokument zawiera szczegółowe instrukcje dotyczące instalowania [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , Azure Data Studio, interfejsu wiersza polecenia platformy Azure (az) i narzędzia interfejsu wiersza polecenia Kubernetes (polecenia kubectl) na komputerze klienckim.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Narzędzia do tworzenia usług danych z obsługą usługi Azure Arc i zarządzania nimi 

W poniższej tabeli wymieniono typowe narzędzia wymagane do tworzenia usług danych z obsługą usługi Azure Arc i zarządzania nimi oraz sposobu instalowania tych narzędzi:

| Narzędzie | Wymagane | Opis | Instalacja |
|---|---|---|---|
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] | Tak | Narzędzie wiersza polecenia do instalowania klastra danych Big Data i zarządzania nim. [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Program udostępnia również narzędzie wiersza polecenia do nawiązywania połączenia z usługą Azure SQL i wystąpieniami SQL Server oraz serwerów Postgres przy użyciu poleceń `azdata sql query` (uruchamianie pojedynczego zapytania z wiersza polecenia), `azdata sql shell` (powłoka interaktywna) `azdata postgres query` i `azdata postgres shell` . | [Instalacja](/sql/azdata/install/deploy-install-azdata?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json) |
| Azure Data Studio | Tak | Bogate środowisko narzędzia do łączenia się z różnymi bazami danych, w tym Azure SQL, SQL Server, PostrgreSQL i MySQL. Rozszerzenia do Azure Data Studio zapewniają środowisko administracyjne dla usług danych z obsługą usługi Azure Arc. | [Instalacja](/sql/azure-data-studio/download-azure-data-studio) |
| [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] rozszerzenie Azure Data Studio | Tak | Rozszerzenie dla Azure Data Studio, które zostanie zainstalowane, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Jeśli jeszcze go nie masz.| Zainstaluj z galerii rozszerzeń w Azure Data Studio.|
| Rozszerzenie Azure ARC dla Azure Data Studio | Tak | Rozszerzenie dla Azure Data Studio, które udostępnia środowisko zarządzania dla usług danych z obsługą usługi Azure Arc. Istnieje zależność od [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] rozszerzenia Azure Data Studio. | Zainstaluj z galerii rozszerzeń w Azure Data Studio.|
| Rozszerzenie PostgreSQL w Azure Data Studio | Nie | Rozszerzenie PostgreSQL dla Azure Data Studio, które oferuje funkcje zarządzania dla PostgreSQL. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Zainstaluj z galerii rozszerzeń w Azure Data Studio.|
| Interfejs wiersza polecenia platformy Azure (az)<sup>1</sup> | Tak | Nowoczesny interfejs wiersza polecenia do zarządzania usługami platformy Azure. Używany z wdrożeniami AKS oraz do przekazywania danych dotyczących spisu i rozliczeń usługi Data Services usługi Azure Arc na platformę Azure. ([Więcej informacji](/cli/azure/?view=azure-cli-latest&preserve-view=true)). | [Instalacja](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) |
| Interfejs wiersza polecenia Kubernetes (polecenia kubectl)<sup>2</sup> | Tak | Narzędzie wiersza polecenia do zarządzania klastrem Kubernetes ([więcej informacji](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [System Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| System [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| zwinięcie <sup>3</sup> | Wymagane w przypadku niektórych przykładowych skryptów. | Narzędzie wiersza polecenia do transferowania danych za pomocą adresów URL. | [System Windows](https://curl.haxx.se/windows/) \| Linux: Instalowanie pakietu zwinięcie |
| OC | Wymagane w przypadku wdrożeń Red Hat OpenShift i Azure RedHat OpenShift. |`oc` jest interfejsem wiersza polecenia Open Shift (CLI). | [Instalowanie interfejsu wiersza polecenia](https://docs.openshift.com/container-platform/4.4/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> musisz używać interfejsu wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom, `az --version` Aby znaleźć wersję, jeśli jest to konieczne.

<sup>2</sup> należy użyć `kubectl` wersji 1,13 lub nowszej. Ponadto wersja programu `kubectl` powinna być plus lub minus jedną wersją pomocniczą klastra Kubernetes. Jeśli chcesz zainstalować określoną wersję na `kubectl` kliencie, zobacz [Instalowanie `kubectl` plików binarnych za pośrednictwem programu zwinięcie](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (w systemie Windows 10, używanie cmd.exe, a nie do uruchamiania zawieszania w programie Windows PowerShell.

<sup>3</sup> Jeśli używasz programu PowerShell, zwinięcie jest aliasem polecenia cmdlet Invoke-WebRequest.

## <a name="next-steps"></a>Następne kroki

[Utwórz kontroler danych usługi Azure Arc](create-data-controller.md)