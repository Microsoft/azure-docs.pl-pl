---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76021104"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Skojarz konto usługi Azure Storage z IoT Hub

Ponieważ aplikacja symulowanego urządzenia przekazuje plik do obiektu BLOB, musisz mieć konto [usługi Azure Storage](../articles/storage/common/storage-account-create.md) skojarzone z Twoim centrum IoT Hub. Po skojarzeniu konta usługi Azure Storage z usługą IoT Hub usługa IoT Hub generuje identyfikator URI sygnatury dostępu współdzielonego. Urządzenie może używać tego identyfikatora URI sygnatury dostępu współdzielonego do bezpiecznego przekazywania pliku do kontenera obiektów BLOB. Usługa IoT Hub i zestawy SDK urządzeń koordynują proces generujący identyfikator URI sygnatury dostępu współdzielonego i udostępnia urządzenie do użycia w celu przekazywania pliku.

Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie przekazywania plików przy użyciu Azure Portal](../articles/iot-hub/iot-hub-configure-file-upload.md). Upewnij się, że kontener obiektów BLOB jest skojarzony z Centrum IoT Hub i że powiadomienia o plikach są włączone.

![Włącz powiadomienia dotyczące plików w portalu](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
