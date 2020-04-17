---
title: Aplikacje lub akcje w chmurze w zasadach dostępu warunkowego — usługa Azure Active Directory
description: Co to są aplikacje lub akcje w chmurze w zasadach dostępu warunkowego usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b15b022726c09ccbaf9674775d114c8dd1916e1d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457301"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Dostęp warunkowy: aplikacje lub akcje w chmurze

Aplikacje lub akcje w chmurze są kluczowym sygnałem w zasadach dostępu warunkowego. Zasady dostępu warunkowego umożliwiają administratorom przypisywanie formantów do określonych aplikacji lub akcji.

- Administratorzy mogą wybierać spośród listy aplikacji, które zawierają wbudowane aplikacje firmy Microsoft i wszystkie [zintegrowane aplikacje usługi Azure AD,](../manage-apps/what-is-application-management.md) w tym galerię, niegalestrętę i aplikacje publikowane za pośrednictwem [serwera proxy aplikacji](../manage-apps/what-is-application-proxy.md).
- Administratorzy mogą zdefiniować zasady nie na podstawie aplikacji w chmurze, ale na akcji użytkownika. Jedyną obsługiwana akcja to Zarejestruj informacje o zabezpieczeniach (wersja zapoznawcza), umożliwiając dostęp warunkowy wymuszanie kontroli wokół [połączonego środowiska rejestracji informacji o zabezpieczeniach.](../authentication/howto-registration-mfa-sspr-combined.md)

![Definiowanie zasad dostępu warunkowego i określanie aplikacji w chmurze](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Aplikacje w chmurze firmy Microsoft

Wiele z istniejących aplikacji w chmurze firmy Microsoft znajduje się na liście aplikacji, które można wybrać. 

Administratorzy mogą przypisać zasady dostępu warunkowego do następujących aplikacji w chmurze firmy Microsoft. Niektóre aplikacje, takie jak Office 365 (wersja zapoznawcza) i Microsoft Azure Management zawierają wiele powiązanych aplikacji lub usług podrzędnych. Poniższa lista nie jest wyczerpująca i może ulec zmianie.

- [Office 365 (wersja zapoznawcza)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Usługi Azure SQL Database i Data Warehouse](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- [Ochrona informacji platformy Microsoft Azure](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure Management](#microsoft-azure-management)
- Zarządzanie subskrypcjami platformy Microsoft Azure
- Microsoft Cloud App Security
- Portal kontroli dostępu do narzędzi handlowych firmy Microsoft
- Usługa uwierzytelniania narzędzi firmy Microsoft Commerce Tools
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Rejestracja usługi Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Planista firmy Microsoft
- Microsoft PowerApps
- Wyszukiwanie w usłudze Microsoft w usłudze Bing
- Microsoft StaffHub
- Usługa Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Usługa Power BI
- Project Online
- Skype dla firm Online
- Wirtualna sieć prywatna (VPN)
- Zaawansowana ochrona przed zagrożeniami w usłudze Windows Defender

### <a name="office-365-preview"></a>Office 365 (wersja zapoznawcza)

Usługa Office 365 zapewnia usługi zwiększające produktywność i współpracę w chmurze, takie jak Exchange, SharePoint i Microsoft Teams. Usługi w chmurze usługi Office 365 są głęboko zintegrowane, aby zapewnić płynne i oparte na współpracy środowisko. Ta integracja może powodować zamieszanie podczas tworzenia zasad, ponieważ niektóre aplikacje, takie jak Microsoft Teams, mają zależności od innych, takich jak SharePoint lub Exchange.

Aplikacja usługi Office 365 (wersja zapoznawcza) umożliwia kierowanie tych usług naraz. Zalecamy używanie nowej aplikacji usługi Office 365 (w wersji zapoznawczej), zamiast kierowania na poszczególne aplikacje w chmurze. Kierowanie tej grupy aplikacji pomaga uniknąć problemów, które mogą pojawić się z powodu niespójnych zasad i zależności.

Administratorzy mogą wykluczyć określone aplikacje z zasad, jeśli chcą, dołączając aplikację usługi Office 365 (wersja zapoznawcza) i wykluczając wybrane przez siebie aplikacje w zasadach.

Kluczowe aplikacje zawarte w aplikacji klienckiej usługi Office 365 (w wersji zapoznawczej):

   - Microsoft Flow
   - Microsoft Forms
   - Usługa Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Usługa wyszukiwania usługi Office 365
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - Skype dla firm Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure Management

Aplikacja Microsoft Azure Management zawiera wiele podstawowych usług. 

   - Azure Portal
   - Dostawca usługi Azure Resource Manager
   - Klasyczne interfejsy API modelu wdrażania
   - Azure PowerShell
   - Portal administratora subskrypcji programu Visual Studio
   - Azure DevOps
   - Portal usługi Azure Data Factory

> [!NOTE]
> Aplikacja Microsoft Azure Management ma zastosowanie do usługi Azure PowerShell, która wywołuje interfejs API usługi Azure Resource Manager. Nie ma zastosowania do programu Azure AD PowerShell, który wywołuje microsoft graph.

## <a name="other-applications"></a>Inne zastosowania

Oprócz aplikacji firmy Microsoft administratorzy mogą dodawać dowolną aplikację zarejestrowaną w usłudze Azure AD do zasad dostępu warunkowego. Aplikacje te mogą obejmować: 

- Aplikacje publikowane za pośrednictwem serwera [proxy aplikacji usługi Azure AD](../manage-apps/what-is-application-proxy.md)
- [Aplikacje dodane z galerii](../manage-apps/add-application-portal.md)
- [Aplikacje niestandardowe nie w galerii](../manage-apps/add-non-gallery-app.md)
- [Starsze aplikacje publikowane za pośrednictwem kontrolerów dostarczania aplikacji i sieci](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Akcje użytkownika

Akcje użytkownika to zadania, które mogą być wykonywane przez użytkownika. Jedyną aktualnie obsługiwaną akcją jest **Rejestrowanie informacji o zabezpieczeniach,** która umożliwia zasadom dostępu warunkowego wymuszanie, gdy użytkownicy, którzy są włączeni do połączonej próby rejestracji, rejestrują swoje informacje zabezpieczające. Więcej informacji można znaleźć w artykule, [Połączone rejestracji informacji o bezpieczeństwie](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Następne kroki

- [Dostęp warunkowy: Warunki](concept-conditional-access-conditions.md)

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)
- [Zależności aplikacji klienckich](service-dependencies.md)
