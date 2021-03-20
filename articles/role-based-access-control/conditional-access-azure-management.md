---
title: Zarządzanie dostępem do zarządzania platformą Azure przy użyciu dostępu warunkowego w usłudze Azure AD
description: Dowiedz się więcej o korzystaniu z dostępu warunkowego w usłudze Azure AD do zarządzania dostępem do zarządzania platformą Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83758779"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Zarządzanie dostępem do zarządzania platformą Azure przy użyciu dostępu warunkowego

> [!CAUTION]
> Upewnij się, że rozumiesz, jak działa dostęp warunkowy przed skonfigurowaniem zasad zarządzania dostępem do usługi Azure Management. Upewnij się, że nie utworzysz warunków, które mogą blokować własny dostęp do portalu.

Dostęp warunkowy w usłudze Azure Active Directory (Azure AD) kontroluje dostęp do aplikacji w chmurze na podstawie określonych warunków, które określisz. Aby zezwolić na dostęp, należy utworzyć zasady dostępu warunkowego, które zezwalają na dostęp lub blokują je w zależności od tego, czy są spełnione wymagania zasad. 

Zazwyczaj dostęp warunkowy służy do kontrolowania dostępu do aplikacji w chmurze. Można również skonfigurować zasady umożliwiające kontrolowanie dostępu do usługi Azure Management w oparciu o określone warunki (takie jak ryzyko związane z logowaniem, lokalizacja lub urządzenie) oraz wymuszanie wymagań, takich jak uwierzytelnianie wieloskładnikowe.

Aby utworzyć zasady zarządzania platformą Azure, wybierz pozycję **zarządzanie Microsoft Azure** w obszarze **aplikacje w chmurze** podczas wybierania aplikacji, do której mają zostać zastosowane zasady.

![Dostęp warunkowy do zarządzania platformy Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Utworzone zasady mają zastosowanie do wszystkich punktów końcowych zarządzania platformy Azure, w tym następujących:

- Azure Portal
- Dostawca Azure Resource Manager
- Klasyczne interfejsy API zarządzania usługami
- Azure PowerShell
- Portal administratora subskrypcji programu Visual Studio
- Azure DevOps
- Portal Azure Data Factory

Należy zauważyć, że zasady dotyczą Azure PowerShell, które wywołuje interfejs API Azure Resource Manager. Nie dotyczy to [programu PowerShell usługi Azure AD](/powershell/azure/active-directory/install-adv2), który wywołuje Microsoft Graph.

Aby uzyskać więcej informacji na temat konfigurowania przykładowych zasad w celu włączenia dostępu warunkowego do zarządzania Microsoft Azure, zobacz [dostęp warunkowy w artykule: Wymagaj uwierzytelniania wieloskładnikowego dla zarządzania Azure](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).
