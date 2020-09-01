---
title: Korzystanie z certyfikatów — Azure Batch
description: Używanie certyfikatów do włączania uwierzytelniania aplikacji
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 5f7d547ad549b2747ae41182ee4058b001d9e78a
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146389"
---
# <a name="using-certificates-with-batch"></a>Używanie certyfikatów z usługą Batch

Obecnie głównym powodem korzystania z certyfikatów z usługą Batch jest to, że aplikacje działają w pulach, które muszą uwierzytelniać się w punkcie końcowym. 

Jeśli nie masz jeszcze certyfikatu, możesz utworzyć certyfikat z podpisem własnym za pomocą `makecert` narzędzia wiersza polecenia.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Ręczne przekazywanie certyfikatów za pomocą Azure Portal

1. Na koncie usługi Batch, do którego chcesz przekazać certyfikat, wybierz pozycję **Certyfikaty** , a następnie wybierz pozycję **Dodaj**. 

2. Przekaż certyfikat z rozszerzeniem PFX lub CER. 

Po przekazaniu certyfikat zostanie dodany do listy certyfikatów i można zweryfikować odcisk palca.

Teraz podczas tworzenia puli wsadowej można przejść do certyfikatów w puli i przypisać certyfikat przekazany do tej puli.

## <a name="next-steps"></a>Następne kroki

Partia ma interfejs API certyfikatów, [AZ Batch Certificate Create](/cli/azure/batch/certificate)

Aby uzyskać informacje na temat korzystania z Key Vault, zobacz [bezpieczny dostęp Key Vault za pomocą usługi Batch](credential-access-key-vault.md).
