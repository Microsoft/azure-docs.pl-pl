---
title: Limity Azure Data Box Gateway | Microsoft Docs
description: Opisuje limity systemowe i zalecane rozmiary Data Box Gateway Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 15b01f92fe0d39d099c10c7c086790a4dbb91379
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582662"
---
# <a name="azure-data-box-gateway-limits"></a>Limity Azure Data Box Gateway

Te limity należy wziąć pod uwagę podczas wdrażania i obsługi rozwiązania Data Box Gateway Microsoft Azure.

## <a name="data-box-gateway-service-limits"></a>Limity usługi Data Box Gateway

[!INCLUDE [data-box-gateway-service-limits](../../includes/data-box-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway limitów urządzeń

W poniższej tabeli opisano limity dotyczące urządzenia Data Box Gateway.

| Opis | Wartość |
|---|---|
|Nie. plików na urządzenie |100 000 000 <br> Dla każdego dodawanego pliku 25 000 000 (z maksymalnym limitem o 100 000 000) należy dodać 2 TB miejsca na dysku, 8 GB pamięci RAM i 4 rdzenie procesora CPU. |
|Nie. udziałów na urządzenie |24 |
|Nie. udziałów na kontener usługi Azure Storage |1 |
|Maksymalny rozmiar pliku zapisany w udziale|W przypadku urządzenia wirtualnego 2 TB maksymalny rozmiar pliku to 500 GB. <br> Maksymalny rozmiar pliku zwiększa się wraz z rozmiarem dysku danych w powyższym współczynniku, dopóki nie osiągnie on maksymalnie 5 TB. |

## <a name="azure-storage-limits"></a>Limity usługi Azure Storage

[!INCLUDE [data-box-gateway-storage-limits](../../includes/data-box-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych

[!INCLUDE [data-box-gateway-storage-data-upload-caveats](../../includes/data-box-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limity rozmiaru i rozmiaru obiektu usługi Azure Storage

[!INCLUDE [data-box-gateway-storage-acct-limits](../../includes/data-box-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektów platformy Azure

[!INCLUDE [data-box-gateway-storage-object-limits](../../includes/data-box-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrażania usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
