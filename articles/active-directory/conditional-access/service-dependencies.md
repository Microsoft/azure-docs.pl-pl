---
title: Zależności usługi dostępu warunkowego — Azure Active Directory
description: Dowiedz się, w jaki sposób warunki są używane w Azure Active Directory dostęp warunkowy, aby wyzwolić zasady.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6592add5e33ba240c0f1d9fdbd23d82adfe5229
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258612"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Co to są zależności usługi w Azure Active Directory dostęp warunkowy? 

Za pomocą zasad dostępu warunkowego można określić wymagania dostępu do witryn sieci Web i usług. Na przykład wymagania dostępu mogą obejmować wymaganie uwierzytelniania wieloskładnikowego (MFA) lub [urządzeń zarządzanych](require-managed-devices.md). 

W przypadku bezpośredniego dostępu do witryny lub usługi, wpływ powiązanych zasad jest zwykle łatwy do oceny. Jeśli na przykład masz zasady, które wymagają uwierzytelniania wieloskładnikowego (MFA) dla usługi SharePoint Online, uwierzytelnianie wieloskładnikowe jest wymuszane dla każdego logowania do portalu sieci Web programu SharePoint. Nie zawsze jest to jednak proste, aby ocenić wpływ zasad, ponieważ istnieją aplikacje w chmurze z zależnościami od innych aplikacji w chmurze. Na przykład firma Microsoft Teams może zapewnić dostęp do zasobów w usłudze SharePoint Online. W związku z tym podczas uzyskiwania dostępu do programu Microsoft Teams w naszym bieżącym scenariuszu podlegają one również zasadom MFA programu SharePoint. 

> [!TIP]
> Korzystanie z aplikacji [pakietu office 365](concept-conditional-access-cloud-apps.md#office-365) będzie ukierunkowane na wszystkie aplikacje pakietu Office, aby uniknąć problemów z zależnościami między usługą w stosie pakietu Office.

## <a name="policy-enforcement"></a>Wymuszanie zasad 

Jeśli masz skonfigurowaną zależność usługi, zasady mogą być stosowane przy użyciu wczesnego lub późnego wymuszania. 

- **Wczesne wymuszanie zasad** przed uzyskaniem dostępu do aplikacji wywołującej musi spełniać zależne zasady usługi. Na przykład użytkownik musi spełnić zasady programu SharePoint przed zalogowaniem się do usługi MS Teams. 
- **Wymuszanie zasad z późnym** wiązaniem jest wykonywane po zalogowaniu się użytkownika do aplikacji wywołującej. Wymuszanie jest odroczone do podczas wywoływania żądań aplikacji, tokenu usługi podrzędnej. Przykładami mogą być MS Teams uzyskujący dostęp do planisty i Office.com dostępu do programu SharePoint. 

Na poniższym diagramie przedstawiono zależności usługi MS Teams. Pełne strzałki wskazują na wczesny zakres wymuszania kreskowaną strzałkę dla usługi Planner wskazuje wymuszanie z późnym wiązaniem. 

![Zależności usługi MS Teams](./media/service-dependencies/01.png)

Najlepszym rozwiązaniem jest skonfigurowanie w miarę możliwości wspólnych zasad w ramach powiązanych aplikacji i usług. Posiadanie spójnej stan zabezpieczeń zapewnia najlepsze środowisko użytkownika. Na przykład ustawienie wspólnych zasad w usłudze Exchange Online, SharePoint Online, Microsoft Teams i Skype dla firm znacznie zmniejsza nieoczekiwane komunikaty, które mogą wynikać z różnych zasad stosowanych do usług podrzędnych. 

Znakomitym sposobem osiągnięcia tego działania w aplikacjach w stosie pakietu Office jest korzystanie z [aplikacji pakietu office 365](concept-conditional-access-cloud-apps.md#office-365) zamiast określania poszczególnych aplikacji.

W poniższej tabeli wymieniono dodatkowe zależności usługi, w przypadku których aplikacje klienckie muszą być zgodne  

| Aplikacje klienckie         | Usługa podrzędny                          | Tytułu |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Zarządzanie Microsoft Azure (Portal i interfejs API) | Wczesna granica |
| Microsoft — Klasa | Exchange                                    | Wczesna granica |
|                     | Sharepoint                                  | Wczesna granica |
| Microsoft Teams     | Exchange                                    | Wczesna granica |
|                     | Planista firmy Microsoft                                  | Późne wiązanie  |
|                     | Usługa Microsoft Stream                            | Późne wiązanie  |
|                     | Sharepoint                                  | Wczesna granica |
|                     | Skype dla firm Online                   | Wczesna granica |
| Portal Office       | Exchange                                    | Późne wiązanie  |
|                     | Sharepoint                                  | Późne wiązanie  |
| Grupy programu Outlook      | Exchange                                    | Wczesna granica |
|                     | Sharepoint                                  | Wczesna granica |
| PowerApps           | Zarządzanie Microsoft Azure (Portal i interfejs API) | Wczesna granica |
|                     | Windows Azure Active Directory              | Wczesna granica |
| Projekt             | Dynamics CRM                                | Wczesna granica |
| Skype dla firm  | Exchange                                    | Wczesna granica |
| Visual Studio       | Zarządzanie Microsoft Azure (Portal i interfejs API) | Wczesna granica |
| Microsoft Forms     | Exchange                                    | Wczesna granica |
|                     | Sharepoint                                  | Wczesna granica |
| Microsoft To-Do     | Exchange                                    | Wczesna granica |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zaimplementować dostęp warunkowy w środowisku, zapoznaj się z tematem [Planowanie wdrożenia dostępu warunkowego w Azure Active Directory](plan-conditional-access.md).
