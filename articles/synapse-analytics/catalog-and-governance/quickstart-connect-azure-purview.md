---
title: Łączenie konta usługi Azure Purview 
description: Łączenie konta usługi Azure Purview z obszarem roboczym usługi Synapse.
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ad9250d022f02db06c9c5251c1fccfbfb730af67
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567880"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Szybki start: łączenie konta usługi Azure Purview z obszarem roboczym usługi Synapse 


W tym przewodniku Szybki start zarejestrujesz konto usługi Azure Purview w obszarze roboczym usługi Synapse. To połączenie umożliwia odnajdywanie zasobów usługi Azure Purview i interakcję z nimi za pośrednictwem funkcji usługi Synapse. 

W ujmie synapse można wykonać następujące zadania: 
- Użyj pola wyszukiwania u góry, aby znaleźć zasoby Purview oparte na słowach kluczowych 
- Informacje o danych opartych na metadanych, pozytywu i adnotacjach 
- Łączenie tych danych z obszarem roboczym za pomocą połączonych usług lub zestawów danych integracji 
- Analizowanie tych zestawów danych za pomocą Apache Spark, Synapse SQL i Przepływ danych 

## <a name="prerequisites"></a>Wymagania wstępne 
- [Konto usługi Azure Purview](../../purview/create-catalog-portal.md) 
- [Obszar roboczy synapse](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Logowanie do obszaru roboczego synapse 

Przejdź do  [https://web.azuresynapse.net](https://web.azuresynapse.net) witryny i zaloguj się do obszaru roboczego. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Uprawnienia do łączenia konta usługi Azure Purview 

- Aby połączyć konto usługi Azure Purview z obszarem roboczym usługi Synapse, potrzebujesz roli Współautor w obszarze roboczym usługi Synapse z usługi Azure Portal IAM i potrzebujesz dostępu do tego konta usługi Azure Purview.  Aby uzyskać więcej informacji, zobacz [Azure Purview permissions (Uprawnienia usługi Azure Purview).](../../purview/catalog-permissions.md)

## <a name="connect-an-azure-purview-account"></a>Łączenie konta usługi Azure Purview  

- W obszarze roboczym usługi Synapse przejdź do obszaru **Zarządzanie**  ->  **usługą Azure Purview.** Wybierz **pozycję Połącz z kontem programu Purview.** 
- Możesz wybrać pozycję **Z subskrypcji platformy Azure** lub ręcznie wprowadzić **wartość**. **W subskrypcji platformy Azure** możesz wybrać konto, do których masz dostęp. 
- Po na połączeniu powinno być możliwe wyświetlanie nazwy konta purview na karcie **Konto usługi Azure Purview.** 
- Aby wyszukać dane, możesz użyć paska wyszukiwania w górnej części obszaru roboczego synapse. 

## <a name="nextsteps"></a>Następne kroki 

[Rejestrowanie i skanowanie zasobów Azure Synapse usłudze Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)

[Odkrywanie, łączenie i eksplorowanie danych w usłudze Synapse przy użyciu usługi Azure Purview](how-to-discover-connect-analyze-azure-purview.md)   
