---
title: Azure Stack graniczny limit Pro | Microsoft Docs
description: Dowiedz się więcej na temat limitów i zalecanych rozmiarów podczas wdrażania i obsługiwania Azure Stack brzegowej usługi Pro, w tym limity usług, limity urządzeń i limity magazynu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91992762"
---
# <a name="azure-stack-edge-pro-limits"></a>Limity urządzenia Azure Stack Edge Pro

Te limity należy wziąć pod uwagę podczas wdrażania i obsługi rozwiązania Microsoft Azure Stack EDGE Pro. 

## <a name="azure-stack-edge-service-limits"></a>Limity usługi Azure Stack Edge

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Limity urządzeń Azure Stack Edge

W poniższej tabeli opisano limity dla urządzenia z Azure Stack Edge. 

W poniższej tabeli opisano limity dotyczące Azure Stack urządzenia brzegowego.

| Opis | Wartość |
|---|---|
|Nie. plików na urządzenie |100 000 000 |
|Nie. udziałów na kontener |1 |
|Wartość maksymalna. punktów końcowych udostępniania i punktów końcowych REST na urządzenie| 24 |
|Wartość maksymalna. kont magazynu warstwowego na urządzenie| 24|
|Maksymalny rozmiar pliku zapisany w udziale| 5 TB |
|Maksymalna liczba grup zasobów na urządzenie| 800 |

## <a name="azure-storage-limits"></a>Limity usługi Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limity rozmiaru i rozmiaru obiektu usługi Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do wdrożenia Azure Stack EDGE Pro](azure-stack-edge-deploy-prep.md)
