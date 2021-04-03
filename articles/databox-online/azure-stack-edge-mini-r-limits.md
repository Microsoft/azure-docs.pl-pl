---
title: Azure Stack graniczne ograniczenia R w usłudze Edge | Microsoft Docs
description: Opisuje limity systemowe i zalecane rozmiary dla Azure Stack krawędź mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96467851"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Azure Stack ograniczenia dotyczące krańcowych krawędzi R


Te limity należy wziąć pod uwagę podczas wdrażania i obsługi rozwiązania typu mini R w Azure Stack Edge.

## <a name="azure-stack-edge-service-limits"></a>Limity usługi Azure Stack Edge

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Limity urządzeń z urządzeniami mini R Azure Stack Edge

W poniższej tabeli opisano limity dotyczące urządzenia Azure Stack Edge mini R.

| Opis | Limit|
|---|---:|
|Nie. plików na urządzenie | 100 000 000 <!--check with devs-->|
|Nie. udziałów na kontener | 1|
|Wartość maksymalna. punktów końcowych udostępniania i punktów końcowych REST na urządzenie| 24 |
|Wartość maksymalna. kont magazynu warstwowego na urządzenie| 24|
|Maksymalny rozmiar pliku zapisany w udziale| 500 GB|
|Maksymalna liczba grup zasobów na urządzenie| 800|

## <a name="azure-storage-limits"></a>Limity usługi Azure Storage

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limity rozmiaru i rozmiaru obiektu usługi Azure Storage

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrożenia Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
