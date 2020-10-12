---
title: plik dołączany
description: plik dołączany
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "70050477"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

W tym artykule opisano tworzenie usługi zaplecza, która dodaje odpowiednie właściwości do sznurka urządzenia, a następnie wysyła zapytanie do rejestru tożsamości w celu znalezienia wszystkich urządzeń z zgłoszonymi właściwościami, które zostały odpowiednio zaktualizowane. Twoja usługa wymaga uprawnień do **nawiązania połączenia z usługą** w celu zmodyfikowania odpowiednich właściwości sznurka urządzenia i wymaga uprawnień do **odczytu rejestru** w celu zbadania rejestru tożsamości. Nie ma domyślnych zasad dostępu współdzielonego, które zawierają tylko te dwa uprawnienia, więc należy ją utworzyć.
