---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934552"
---
Magazyn kluczy obsługuje do 1024 wpisów zasad dostępu, przy czym każdy wpis przyznaje odrębny zestaw uprawnień dla określonego podmiotu zabezpieczeń. Ze względu na to ograniczenie Zalecamy przypisanie zasad dostępu do grup użytkowników, jeśli jest to możliwe, a nie poszczególnych użytkowników. Korzystanie z grup ułatwia zarządzanie uprawnieniami dla wielu osób w organizacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do aplikacji i zasobów przy użyciu grup Azure Active Directory](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

Aby uzyskać szczegółowe informacje na temat kontroli dostępu Key Vault, zobacz [Azure Key Vault Security: Zarządzanie tożsamościami i dostępem](../articles/key-vault/general/security-overview.md#identity-management).