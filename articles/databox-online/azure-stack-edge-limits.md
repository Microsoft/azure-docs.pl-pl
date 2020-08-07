---
title: Limity Azure Stack krawędzi | Microsoft Docs
description: Dowiedz się więcej o limitach i zalecanych rozmiarach podczas wdrażania i obsługi Azure Stack Edge, w tym limitów usług, limitów urządzeń i limitów magazynu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 8bbcf3f61121813b91cb951809992d10977a640c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922714"
---
# <a name="azure-stack-edge-limits"></a>Limity rozwiązania Azure Stack Edge

Te limity należy wziąć pod uwagę podczas wdrażania i obsługi rozwiązania Microsoft Azure Stack Edge. 

## <a name="azure-stack-edge-service-limits"></a>Limity usługi Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-device-limits"></a>Azure Stack limitów urządzeń

W poniższej tabeli opisano limity dotyczące Azure Stack urządzenia brzegowego. 

| Opis | Wartość |
|---|---|
|Nie. plików na urządzenie |100 000 000 |
|Nie. udziałów na urządzenie |24 |
|Nie. udziałów na kontener |1 |
|Maksymalny rozmiar pliku zapisany w udziale| 5 TB |

## <a name="azure-storage-limits"></a>Limity usługi Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limity rozmiaru i rozmiaru obiektu usługi Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrożenia Azure Stack Edge](azure-stack-edge-deploy-prep.md)
