---
title: dołączanie pliku
description: dołączanie pliku
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147475"
---
 Azure Machine Learning Klastry obliczeniowe obsługują również [zarządzane tożsamości](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania dostępu do zasobów platformy Azure bez uwzględniania poświadczeń w kodzie. Istnieją dwa typy tożsamości zarządzanych:

* **Tożsamość zarządzana przypisana przez system** jest włączona bezpośrednio w klastrze obliczeniowym Azure Machine Learning. Cykl życia tożsamości przypisanej do systemu jest bezpośrednio powiązany z klastrem obliczeniowym. Jeśli klaster obliczeniowy zostanie usunięty, platforma Azure automatycznie czyści poświadczenia i tożsamość w usłudze Azure AD.
* **Tożsamość zarządzana przypisana przez użytkownika** to autonomiczny zasób platformy Azure dostarczany za pomocą usługi tożsamości zarządzanej platformy Azure. Tożsamość zarządzaną przez użytkownika można przypisać do wielu zasobów i będzie trwała tak długo, jak chcesz.