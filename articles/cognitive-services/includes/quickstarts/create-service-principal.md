---
title: Tworzenie jednostki usługi platformy Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: dc5297a3abb913c7bef7c1669fa319d01a0c0203
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947395"
---
## <a name="create-an-azure-service-principal"></a>Tworzenie jednostki usługi platformy Azure

Aby aplikacja mogła korzystać z Twojego konta platformy Azure, do zarządzania uprawnieniami potrzebna jest jednostka usługi platformy Azure. Postępuj zgodnie z instrukcjami w temacie [Tworzenie jednostki usługi platformy Azure](/powershell/azure/create-azure-service-principal-azureps?viewFallbackFrom=azps-3.3.0).

Podczas tworzenia nazwy głównej usługi zobaczysz, że będzie ona mieć wartość klucza tajnego, identyfikator i identyfikator aplikacji. Zapisz identyfikator aplikacji i klucz tajny w tymczasowej lokalizacji dla dalszych kroków.
