---
title: Limity Azure Data Box Gateway | Microsoft Docs
description: Opisuje limity systemowe i zalecane rozmiary Data Box Gateway Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: d160b0ead1ceb8c41bede20cb389a360d24258f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82561363"
---
# <a name="azure-data-box-gateway-limits"></a>Limity Azure Data Box Gateway

Te limity należy wziąć pod uwagę podczas wdrażania i obsługi rozwiązania Data Box Gateway Microsoft Azure.

## <a name="data-box-gateway-service-limits"></a>Limity usługi Data Box Gateway

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway limitów urządzeń

W poniższej tabeli opisano limity dotyczące urządzenia Data Box Gateway.

| Opis | Wartość |
|---|---|
|Nie. plików na urządzenie |100 000 000 <br> Dla każdego dodawanego pliku 25 000 000 (z maksymalnym limitem o 100 000 000) należy dodać 2 TB miejsca na dysku, 8 GB pamięci RAM i 4 rdzenie procesora CPU. |
|Nie. udziałów na urządzenie |24 |
|Nie. udziałów na kontener usługi Azure Storage |1 |
|Maksymalny rozmiar pliku zapisany w udziale|W przypadku urządzenia wirtualnego 2 TB maksymalny rozmiar pliku to 500 GB. <br> Maksymalny rozmiar pliku zwiększa się wraz z rozmiarem dysku danych w powyższym współczynniku, dopóki nie osiągnie on maksymalnie 5 TB. |

## <a name="azure-storage-limits"></a>Limity usługi Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limity rozmiaru i rozmiaru obiektu usługi Azure Storage

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrażania usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
