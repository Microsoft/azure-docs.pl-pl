---
title: Limity Azure Stack krawędzi | Microsoft Docs
description: Dowiedz się więcej o limitach i zalecanych rozmiarach podczas wdrażania i obsługi Azure Stack Edge, w tym limitów usług, limitów urządzeń i limitów magazynu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: e21f5a89d9f1f21eb99071a141794f99c07a8dfa
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079816"
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
