---
title: 'Rozwiązywanie problemów: w przypadku obszarów roboczych utworzonych przez zestaw SDK nie można uruchomić Synapse Studio'
description: Kroki umożliwiające rozwiązanie obszarów roboczych utworzonych przez zestaw SDK nie można uruchomić Synapse Studio
author: julieMSFT
ms.author: jrasnick
ms.topic: troubleshooting
ms.service: synapse-analytics
ms.subservice: workspace
ms.date: 11/24/2020
ms.openlocfilehash: f5d0aae975d476da47510a1f2fd164cbc5f32945
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96466948"
---
# <a name="troubleshoot-azure-synapse-analytics-workspaces-created-using-sdk"></a>Rozwiązywanie problemów z obszarami roboczymi analizy usługi Azure Synapse

Ten artykuł zawiera kroki rozwiązywania problemów w celu uruchomienia programu Synapse Studio z obszaru roboczego Synapse, który został utworzony za pomocą zestawu SDK (Software Development Kit).


## <a name="prerequisites"></a>Wymagania wstępne

- Synapse obszar roboczy utworzony przy użyciu zestawu SDK

## <a name="workaround"></a>Obejście

Aby uruchomić program Synapse Studio z poziomu obszaru roboczego utworzonego przez zestaw SDK, wykonaj następujące czynności: 
  1.    Utwórz obszar roboczy, uruchamiając `az synapse workspace create` .
  2.    Wyodrębnij identyfikator zarządzanej tożsamości, uruchamiając `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` .
  3.    Przyznaj roli współautor danych obiektów blob magazynu do konta magazynu tożsamości zarządzanej, uruchamiając ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}.`
  4.    Dodaj regułę zapory, uruchamiając ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` .

## <a name="next-steps"></a>Następne kroki

* [Co to jest Azure Synapse](../overview-what-is.md)
* [Wprowadzenie](../get-started.md)
