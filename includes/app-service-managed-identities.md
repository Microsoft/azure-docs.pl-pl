---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649089"
---
Zarządzana tożsamość z usługi Azure Active Directory (Azure AD) umożliwia aplikacji łatwe uzyskiwanie dostępu do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault. Tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowizacji ani rotacji żadnych wpisów tajnych. Aby uzyskać więcej informacji na temat tożsamości zarządzanych w usłudze Azure AD, zobacz [zarządzane tożsamości dla zasobów platformy Azure](../articles/active-directory/managed-identities-azure-resources/overview.md).

Aplikacja może mieć przyznane dwa typy tożsamości:

- **Tożsamość przypisana do systemu** jest powiązana z aplikacją i jest usuwana, jeśli aplikacja zostanie usunięta. Aplikacja może mieć tylko jedną tożsamość przypisaną do systemu.
- **Tożsamość przypisana przez użytkownika** to autonomiczny zasób platformy Azure, który można przypisać do aplikacji. Aplikacja może mieć wiele tożsamości przypisanych do użytkownika.