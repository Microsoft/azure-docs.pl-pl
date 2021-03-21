---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025442"
---
 Azure Machine Learning Klastry obliczeniowe obsługują również [zarządzane tożsamości](../articles/active-directory/managed-identities-azure-resources/overview.md) w celu uwierzytelniania dostępu do zasobów platformy Azure bez uwzględniania poświadczeń w kodzie. Istnieją dwa typy tożsamości zarządzanych:

* **Tożsamość zarządzana przypisana przez system** jest włączona bezpośrednio w klastrze obliczeniowym Azure Machine Learning. Cykl życia tożsamości przypisanej do systemu jest bezpośrednio powiązany z klastrem obliczeniowym. Jeśli klaster obliczeniowy zostanie usunięty, platforma Azure automatycznie czyści poświadczenia i tożsamość w usłudze Azure AD.
* **Tożsamość zarządzana przypisana przez użytkownika** to autonomiczny zasób platformy Azure dostarczany za pomocą usługi tożsamości zarządzanej platformy Azure. Tożsamość zarządzaną przez użytkownika można przypisać do wielu zasobów i będzie trwała tak długo, jak chcesz.