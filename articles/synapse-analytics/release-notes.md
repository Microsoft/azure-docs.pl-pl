---
title: 'Informacje o wersji: Azure Synapse Analytics (obszary robocze)'
description: Informacje o wersji usługi Azure Synapse Analytics (obszary robocze)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: add5c89e83f33980803bf571239023859653c4f1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059613"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Informacje o wersji usługi Azure Synapse Analytics (wersja zapoznawcza)

W tym artykule opisano ograniczenia i problemy związane z usługą Azure Synapse Analytics (obszarami roboczymi). Aby uzyskać powiązane informacje, zobacz [co to jest usługa Azure Synapse Analytics (obszary robocze)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (obszary robocze) 

### <a name="azure-synapse-cli"></a>Interfejs wiersza polecenia platformy Azure Synapse

- Problem i wpływ na klientów: w przypadku obszarów roboczych utworzonych przez zestaw SDK nie można uruchomić programu Synapse Studio

- Obejście: wykonaj następujące czynności: 
  1.    Utwórz obszar roboczy, uruchamiając `az synapse workspace create` .
  2.    Wyodrębnij identyfikator zarządzanej tożsamości, uruchamiając `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Aby dodać obszar roboczy jako rolę do konta magazynu, należy uruchomić program ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Dodaj regułę zapory, uruchamiając ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Następne kroki

* [Tworzenie obszaru roboczego](quickstart-create-workspace.md)
* [Korzystanie z programu Synapse Studio](quickstart-synapse-studio.md)
* [Tworzenie puli SQL](quickstart-create-sql-pool-portal.md)
* [Korzystanie z bazy danych SQL na żądanie](quickstart-sql-on-demand.md)
* [Tworzenie puli Apache Spark](quickstart-create-apache-spark-pool-portal.md)