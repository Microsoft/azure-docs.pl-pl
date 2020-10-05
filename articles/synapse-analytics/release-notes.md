---
title: 'Informacje o wersji: Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)'
description: Informacje o wersji usługi Azure Synapse Analytics (obszary robocze — wersja zapoznawcza)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90031674"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Informacje o wersji usługi Azure Synapse Analytics (obszary robocze)

W tym artykule opisano ograniczenia i problemy związane z usługą Azure Synapse Analytics (obszarami roboczymi). Aby uzyskać powiązane informacje, zobacz [co to jest usługa Azure Synapse Analytics (obszary robocze)](overview-what-is.md)

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

- Problem i wpływ na klientów: w przypadku obszarów roboczych utworzonych przez zestaw SDK nie można uruchomić programu Synapse Studio

- Obejście: wykonaj następujące czynności: 
  1.    Utwórz obszar roboczy, uruchamiając `az synapse workspace create` .
  2.    Wyodrębnij identyfikator zarządzanej tożsamości, uruchamiając `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Aby dodać obszar roboczy jako rolę do konta magazynu, należy uruchomić program ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` .
  4.    Dodaj regułę zapory, uruchamiając ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Następne kroki

* [Co to jest Azure Synapse](overview-what-is.md)
* [Wprowadzenie](get-started.md)
* [Często zadawane pytania](overview-faq.md)
