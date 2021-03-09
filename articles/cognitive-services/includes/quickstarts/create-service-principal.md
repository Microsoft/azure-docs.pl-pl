---
title: Tworzenie jednostki usługi platformy Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 2f60759fbbae2c0f712a475ef397ca260e1f0415
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473712"
---
## <a name="create-an-azure-service-principal"></a>Tworzenie jednostki usługi platformy Azure

Aby aplikacja mogła korzystać z Twojego konta platformy Azure, do zarządzania uprawnieniami potrzebna jest jednostka usługi platformy Azure. Postępuj zgodnie z instrukcjami w temacie [Tworzenie jednostki usługi platformy Azure](/powershell/azure/create-azure-service-principal-azureps).

Podczas tworzenia nazwy głównej usługi zobaczysz, że będzie ona mieć wartość klucza tajnego, identyfikator i identyfikator aplikacji. Zapisz identyfikator aplikacji i klucz tajny w tymczasowej lokalizacji dla dalszych kroków.
