---
title: Azure Stack krawędzi — limity Pro R | Microsoft Docs
description: Opisuje limity systemowe i zalecane rozmiary dla Azure Stack EDGE Pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467632"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Azure Stack krawędź — limity Pro R

Te limity należy wziąć pod uwagę podczas wdrażania i obsługiwania rozwiązania Azure Stack EDGE Pro R.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Limity usługi Azure Stack Edge w usłudze Pro R

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Limity urządzeń Azure Stack EDGE Pro R

W poniższej tabeli opisano limity dotyczące urządzenia Azure Stack EDGE Pro R.

| Opis | Wartość |
|---|---|
|Nie. plików na urządzenie |100 000 000 |
|Nie. udziałów na kontener |1 |
|Wartość maksymalna. punktów końcowych udostępniania i punktów końcowych REST na urządzenie| 24 |
|Wartość maksymalna. kont magazynu warstwowego na urządzenie| 24|
|Maksymalny rozmiar pliku zapisany w udziale| 5 TB |
|Maksymalna liczba grup zasobów na urządzenie| 800 |

## <a name="azure-storage-limits"></a>Limity usługi Azure Storage

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limity rozmiaru i rozmiaru obiektu usługi Azure Storage

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Następne kroki

- [Przygotowanie do wdrożenia Azure Stack EDGE Pro R](azure-stack-edge-pro-r-deploy-prep.md)
