---
title: Łączenie konta usługi Azure kontrolą 
description: Połącz konto usługi Azure kontrolą z obszarem roboczym Synapse.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f85a76a7ce01c11255f54c6b956088f9328dfc9f
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918736"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Szybki Start: Łączenie konta usługi Azure kontrolą z obszarem roboczym Synapse 

> [!IMPORTANT]
> Integracja między usługą Azure Synapse Analytics a usługą Azure kontrolą jest obecnie w wersji zapoznawczej. Jeśli chcesz wypróbować usługę Azure kontrolą w Synapse, skontaktuj się z przedstawicielem handlowym firmy Microsoft.

W tym przewodniku szybki start będziesz rejestrować konto usługi Azure kontrolą w obszarze roboczym Synapse. To połączenie umożliwia odnajdywanie zasobów usługi Azure kontrolą i współdziałanie z nimi za pomocą funkcji Synapse. 

W programie Synapse można wykonywać następujące zadania: 
- Użyj pola wyszukiwania u góry, aby znaleźć kontrolą zasoby na podstawie słów kluczowych 
- Informacje na temat metadanych, elementów zależnych, adnotacji 
- Łączenie tych danych z obszarem roboczym za pomocą połączonych usług lub zestawów danych integracji 
- Analizowanie tych zestawów danych za pomocą Synapse Apache Spark, Synapse SQL i przepływu danych 

## <a name="prerequisites"></a>Wymagania wstępne 
- [Konto usługi Azure kontrolą](../../purview/create-catalog-portal.md) 
- [Obszar roboczy Synapse](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Logowanie się do obszaru roboczego Synapse 

Przejdź do https://web.azuresynapse.net obszaru roboczego i zaloguj się do niego. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Uprawnienia do nawiązywania połączenia z kontem usługi Azure kontrolą 

- Aby można było połączyć konto usługi Azure kontrolą z obszarem roboczym Synapse, potrzebna jest rola **współautor** w obszarze roboczym Synapse z Azure portal usługi IAM i będzie potrzebny dostęp do tego konta kontrolą platformy Azure.

## <a name="connect-an-azure-purview-account"></a>Łączenie konta usługi Azure kontrolą  

- W obszarze roboczym Synapse przejdź do pozycji **Zarządzaj**  ->  **usługą Azure kontrolą**. Wybierz pozycję **Połącz z kontem kontrolą**. 
- Możesz wybrać jedną **z subskrypcji platformy Azure** lub **wprowadzić ją ręcznie**. **W ramach subskrypcji platformy Azure** możesz wybrać konto, do którego masz dostęp. 
- Po nawiązaniu połączenia powinna być widoczna nazwa konta kontrolą na karcie **Azure kontrolą Account**. 
- Możesz użyć paska wyszukiwania w górnej części obszaru roboczego Synapse, aby wyszukać dane. 

## <a name="nextsteps"></a>Następne kroki 

[Rejestrowanie i skanowanie zasobów usługi Azure Synapse na platformie Azure kontrolą](../../purview/register-scan-azure-synapse-analytics.md)

[Odkrywanie, łączenie i Eksplorowanie danych w programie Synapse przy użyciu usługi Azure kontrolą](how-to-discover-connect-analyze-azure-purview.md)   