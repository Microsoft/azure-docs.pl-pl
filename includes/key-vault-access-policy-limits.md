---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: c74f2543e96087378741d6388b7c27dc4d05ddc8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89381041"
---
Magazyn kluczy obsługuje do 1024 wpisów zasad dostępu, przy czym każdy wpis przyznaje odrębny zestaw uprawnień dla określonego podmiotu zabezpieczeń. Ze względu na to ograniczenie Zalecamy przypisanie zasad dostępu do grup użytkowników, jeśli jest to możliwe, a nie poszczególnych użytkowników. Korzystanie z grup ułatwia zarządzanie uprawnieniami dla wielu osób w organizacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup Azure Active Directory](/azure/active-directory/fundamentals/active-directory-manage-groups)

Aby uzyskać szczegółowe informacje na temat kontroli dostępu Key Vault, zobacz [Azure Key Vault Security: Zarządzanie tożsamościami i dostępem](/azure/key-vault/general/overview-security#identity-and-access-management). 
