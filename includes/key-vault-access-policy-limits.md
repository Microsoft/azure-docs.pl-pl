---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 8c8c9563c954e3d1a84ea2b9f411187d5fb9f226
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025760"
---
Magazyn kluczy obsługuje do 1024 wpisów zasad dostępu, przy czym każdy wpis przyznaje odrębny zestaw uprawnień dla określonego podmiotu zabezpieczeń. Ze względu na to ograniczenie Zalecamy przypisanie zasad dostępu do grup użytkowników, jeśli jest to możliwe, a nie poszczególnych użytkowników. Korzystanie z grup ułatwia zarządzanie uprawnieniami dla wielu osób w organizacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

Aby uzyskać szczegółowe informacje na temat kontroli dostępu Key Vault, zobacz [Azure Key Vault Security: Zarządzanie tożsamościami i dostępem](../articles/key-vault/general/overview-security.md#identity-and-access-management).