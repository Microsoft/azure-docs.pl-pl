---
title: Interfejsy API dostępu warunkowego i PowerShell — Azure Active Directory
description: Używanie interfejsów API dostępu warunkowego usługi Azure AD i programu PowerShell do zarządzania zasadami, takimi jak kod
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33f3766e02316eccb519bea15246541531750ed1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90008029"
---
# <a name="conditional-access-programmatic-access"></a>Dostęp warunkowy: dostęp programistyczny

Wiele organizacji wyraziło potrzebę zarządzania tak dużą ilością środowisk jak kod, jak to możliwe. Za pomocą Microsoft Graph można traktować zasady dostępu warunkowego, takie jak każdy inny fragment kodu w środowisku.

Microsoft Graph zapewnia jednolity model programistyczny, który może być używany przez organizacje do współpracy z danymi w Microsoft 365, Windows 10 i Enterprise Mobility + Security. Aby uzyskać więcej informacji na temat Microsoft Graph, zapoznaj się z artykułem [omówienie Microsoft Graph](/graph/overview).

![Obraz przedstawiający podstawowe zasoby i relacje, które są częścią grafu](./media/howto-conditional-access-apis/microsoft-graph.png)

Poniższe przykłady są dostępne w nieobsługiwanym zakresie. Te przykłady można wykorzystać jako podstawę dla narzędzi w organizacji. 

Wiele z poniższych przykładów używa narzędzi, takich jak [tożsamości zarządzane](../managed-identities-azure-resources/overview.md), [Logic Apps](../../logic-apps/logic-apps-overview.md), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [zespoły](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/)i [Azure Key Vault](../../key-vault/general/overview.md).

## <a name="configure"></a>Konfigurowanie

### <a name="powershell"></a>Program PowerShell

W przypadku wielu administratorów program PowerShell jest już zrozumiałym narzędziem do obsługi skryptów. Poniższy przykład pokazuje, jak używać [modułu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD) do zarządzania zasadami dostępu warunkowego.

- [Konfigurowanie zasad dostępu warunkowego za pomocą poleceń programu PowerShell usługi Azure AD](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>Interfejs API programu Graph

W tym przykładzie przedstawiono podstawowe opcje tworzenia, odczytu, aktualizacji i usuwania (CRUD) dostępne w interfejsie API programu Dostęp warunkowy. Przykład zawiera również niektóre szablony JSON, których można użyć do utworzenia niektórych przykładowych zasad.

- [Konfigurowanie zasad dostępu warunkowego przy użyciu wywołań interfejsu API Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>Konfigurowanie przy użyciu szablonów

Używanie interfejsów API dostępu warunkowego do wdrażania zasad dostępu warunkowego w środowisku przedprodukcyjnym przy użyciu szablonu.

- [Konfigurowanie zasad dostępu warunkowego przy użyciu szablonów interfejsu API Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>Testowanie

Ten przykład pozwala korzystać z bezpieczniejszej praktyki wdrażania przy użyciu przepływów pracy zatwierdzania, które mogą kopiować zasady dostępu warunkowego z jednego środowiska, takiego jak przedprodukcyjny, do innego, takiego jak środowisko produkcyjne.

- [Podnieś poziom zasad dostępu warunkowego ze środowisk testowych](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>Wdrażanie

Ten przykład zapewnia mechanizm do przeprowadzenia stopniowo przemieszczonych zasad dostępu warunkowego do wypełniania przez użytkownika, co pozwala na wczesne zarządzanie problemami z pomocą techniczną.

- [Wdrażanie zasad dostępu warunkowego w środowiskach produkcyjnych przy użyciu przepływów pracy zatwierdzania](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Monitorowanie

Ten przykład zapewnia mechanizm monitorowania zmian zasad dostępu warunkowego w czasie i może wyzwalać alerty w przypadku zmiany zasad kluczy.

- [Monitoruj wdrożone zasady dostępu warunkowego dla zmian i alertów wyzwalacza](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>Zarządzanie

### <a name="backup-and-restore"></a>Tworzenie kopii zapasowej i przywracanie

Automatyzuj wykonywanie kopii zapasowych i przywracanie zasad dostępu warunkowego za pomocą zatwierdzeń w zespołach przy użyciu tego przykładu.

- [Zarządzanie procesem tworzenia kopii zapasowej i przywracania zasad dostępu warunkowego przy użyciu wywołań interfejsu API Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>Konta dostępu awaryjnego

Wielu administratorów może tworzyć zasady dostępu warunkowego i mogą pamiętać o dodaniu [kont dostępu awaryjnego](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) jako wykluczenia do tych zasad. W tym przykładzie zagwarantujemy, że wszystkie zasady zostaną zaktualizowane w celu uwzględnienia wskazanych kont dostępu awaryjnego.

- [Zarządzanie przypisywaniem kont dostępu awaryjnego do zasad dostępu warunkowego przy użyciu wywołań interfejsu API Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>Planowanie awaryjne

Elementy nie zawsze działają w żądany sposób, gdy jest to konieczne, aby wrócić do stanu, w którym można kontynuować pracę. Poniższy przykład umożliwia przywrócenie zasad do znanego dobrego planu awaryjnego i wyłączenie innych zasad dostępu warunkowego.

- [Zarządzanie aktywacją zasad awaryjnego dostępu warunkowego przy użyciu wywołań interfejsu API Microsoft Graph](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>Udział społeczności

Te przykłady są dostępne w naszym [repozytorium GitHub](https://github.com/Azure-Samples/azure-ad-conditional-access-apis). Mamy przyjemność wspierać wkład społeczności w szczegółowe problemy z usługą GitHub i żądania ściągnięcia.

## <a name="next-steps"></a>Następne kroki

- [Omówienie programu Microsoft Graph](/graph/overview)

- [Interfejs API dostępu warunkowego](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)

- [Interfejs API nazwanej lokalizacji](/graph/api/resources/namedlocation?view=graph-rest-1.0)
