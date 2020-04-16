---
title: Informacje o wersji
description: Informacje o wersji usługi Azure Synapse Analytics (obszary robocze)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c1b5b9ac5d7c3f04dd3ae2e843425a5ead0d4c07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423859"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Informacje o wersji usługi Azure Synapse Analytics (wersja zapoznawcza)

W tym artykule opisano ograniczenia i problemy z usługą Azure Synapse Analytics (obszary robocze). Aby uzyskać powiązane informacje, zobacz [Co to jest usługa Azure Synapse Analytics (obszary robocze)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (obszary robocze) 

### <a name="azure-synapse-cli"></a>Interfejsu wiersza polecenia synapse platformy Azure

- Problem i wpływ na klienta: obszary robocze utworzone przez SDK nie mogą uruchamiać synapse studio

- Obejście: Wykonaj następujące kroki: 
  1.    Utwórz obszar `az synapse workspace create`roboczy, uruchamiając 2.    Wyodrębnianie identyfikatora tożsamości zarządzanej przez uruchomienie`$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`
  3.    Dodawanie obszaru roboczego jako roli do konta magazynu przez uruchomienie` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`
  4.    Dodaj regułę zapory, uruchamiając` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `

## <a name="next-steps"></a>Następne kroki

* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z Synapse Studio](quickstart-synapse-studio.md)
* [Tworzenie puli SQL](quickstart-create-sql-pool.md)
* [Korzystanie z języka SQL na żądanie](quickstart-sql-on-demand.md)
* [Tworzenie puli platformy Spark Apache](quickstart-create-apache-spark-pool.md)