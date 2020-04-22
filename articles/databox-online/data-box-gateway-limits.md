---
title: Limity bramy usługi Azure Data Box Gateway | Dokumenty firmy Microsoft
description: W tym artykule opisano limity systemowe i zalecane rozmiary bramy usługi Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: alkohli
ms.openlocfilehash: 641d7410e414be2adae2a83840c90a680aedd2fc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683353"
---
# <a name="azure-data-box-gateway-limits"></a>Limity bramy usługi Azure Data Box

Należy wziąć pod uwagę te limity podczas wdrażania i obsługi rozwiązania usługi Microsoft Azure Data Box Gateway. 

## <a name="data-box-gateway-service-limits"></a>Limity usług bramy pola danych

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Limity urządzeń bramy pól danych

W poniższej tabeli opisano limity dla urządzenia Bramy pola danych.

| Opis | Wartość |
|---|---|
|Nie. plików na urządzenie |100 milionów <br> Na każde 25 milionów dodawanych plików (z maksymalnym limitem 100 milionów) należy dodać 2 TB miejsca na dysku, 8 GB pamięci RAM i 4 rdzenie procesora. |
|Nie. udziałów na urządzeniu |24 |
|Nie. udziałów na kontener magazynu platformy Azure |1 |
|Maksymalny rozmiar pliku zapisany w udziale|W przypadku urządzenia wirtualnego o pojemności 2 TB maksymalny rozmiar pliku to 500 GB. <br> Maksymalny rozmiar pliku zwiększa się wraz z rozmiarem dysku danych w poprzednim stosunku, aż osiągnie maksymalnie 5 TB. |

## <a name="azure-storage-limits"></a>Limity magazynu platformy Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Zastrzeżenia dotyczące przekazywania danych

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Limity rozmiaru konta magazynu platformy Azure i rozmiaru obiektu

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Limity rozmiaru obiektu platformy Azure

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrażania usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
