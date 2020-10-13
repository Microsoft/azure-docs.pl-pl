---
title: plik dołączany
description: plik dołączany
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147475"
---
 Azure Machine Learning Klastry obliczeniowe obsługują również [zarządzane tożsamości](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) w celu uwierzytelniania dostępu do zasobów platformy Azure bez uwzględniania poświadczeń w kodzie. Istnieją dwa typy tożsamości zarządzanych:

* **Tożsamość zarządzana przypisana przez system** jest włączona bezpośrednio w klastrze obliczeniowym Azure Machine Learning. Cykl życia tożsamości przypisanej do systemu jest bezpośrednio powiązany z klastrem obliczeniowym. Jeśli klaster obliczeniowy zostanie usunięty, platforma Azure automatycznie czyści poświadczenia i tożsamość w usłudze Azure AD.
* **Tożsamość zarządzana przypisana przez użytkownika** to autonomiczny zasób platformy Azure dostarczany za pomocą usługi tożsamości zarządzanej platformy Azure. Tożsamość zarządzaną przez użytkownika można przypisać do wielu zasobów i będzie trwała tak długo, jak chcesz.