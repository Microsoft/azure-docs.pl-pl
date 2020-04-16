---
title: Samoobsługowe resetowanie hasła licencji — usługa Azure Active Directory
description: Dowiedz się więcej o różnicach wymagań licencjonowania samoobsługowego resetowania haseł usługi Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ca11af061e37cf4f804ce2d7ceed72a9448294
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393075"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Wymagania dotyczące licencjonowania samoobsługowego resetowania hasła usługi Azure Active Directory

Aby zmniejszyć liczbę wywołań pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się na urządzeniu lub aplikacji, konta użytkowników w usłudze Azure Active Directory (Azure AD) można włączyć w celu samoobsługowego resetowania haseł (SSPR). Funkcje, które składają się na samookapowanie haseł obejmują zmianę hasła, resetowanie, odblokowywanie i zapisywanie zwrotne w katalogu lokalnym. Podstawowe funkcje sspr są dostępne dla usługi Office 365 i wszystkich użytkowników usługi Azure AD bez żadnych kosztów.

W tym artykule opisano różne sposoby samoobsługowego resetowania hasła, które mogą być licencjonowane i używane. Szczegółowe informacje na temat cen i rozliczeń można znaleźć na [stronie cennika usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="compare-editions-and-features"></a>Porównywanie wersji i funkcji

SSPR jest licencjonowany na użytkownika. Aby zachować zgodność, organizacje muszą przypisać odpowiednią licencję swoim użytkownikom.

W poniższej tabeli przedstawiono różne scenariusze samookapła dotyczące zmiany hasła, resetowania lub lokalnego stornowania i które jednostki SKU udostępniają tę funkcję.

| Funkcja | Usługa Azure AD — warstwa Bezpłatna | Office 365 Business Premium | Microsoft 365 Business | Usługa Azure AD Premium P1 lub P2 |
| --- |:---:|:---:|:---:|:---:|
| **Zmiana hasła użytkownika tylko w chmurze**<br />Gdy użytkownik w usłudze Azure AD zna swoje hasło i chce zmienić go na coś nowego. | ● | ● | ● | ● |
| **Resetowanie hasła tylko w chmurze**<br />Gdy użytkownik usługi Azure AD zapomniał hasła i musi go zresetować. | | ● | ● | ● |
| **Hybrydowa zmiana hasła użytkownika lub zresetowanie z zapisem na prem**<br />Gdy użytkownik w usłudze Azure AD, który jest synchronizowany z katalogu lokalnego przy użyciu usługi Azure AD Connect chce zmienić lub zresetować swoje hasło, a także zapisać nowe hasło z powrotem do on-prem. | | | ● | ● |

> [!WARNING]
> Autonomiczne plany licencjonowania usługi Office 365 nie obsługują samowzruszonego rozwiązania SSPR z lokalnym odpisem zwrotnym. Te plany licencjonowania usługi Office 365 wymagają usługi Azure AD Premium P1, Premium P2 lub Microsoft 365 Business, aby ta funkcja działała.

Dodatkowe informacje o licencjonowaniu, w tym koszty, można znaleźć na następujących stronach:

* [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Funkcje i możliwości usługi Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Mobilność w przedsiębiorstwie + bezpieczeństwo](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="enable-group-or-user-based-licensing"></a>Włączanie licencjonowania grupowego lub opartego na użytkownikach

Usługa Azure AD obsługuje licencjonowanie oparte na grupach. Administratorzy mogą zbiorczo przypisywać licencje do grupy użytkowników, zamiast przypisywać je po kolei. Aby uzyskać więcej informacji, zobacz [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses).

Niektóre usługi firmy Microsoft nie są dostępne we wszystkich lokalizacjach. Aby licencja mogła być przypisana do użytkownika, administrator musi określić właściwość **Lokalizacja użycia** użytkownika. Przypisywanie licencji można wykonać w sekcji**Ustawienia** **profilu** >  **użytkownika** > w witrynie Azure portal. *Podczas korzystania z przypisania licencji grupy, każdy użytkownik bez lokalizacji użycia określony dziedziczy lokalizację katalogu.*

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z sspr, wykonaj następujący samouczek:

> [!div class="nextstepaction"]
> [Samouczek: Włączanie samoobsługowego resetowania hasła (SSPR)](tutorial-enable-sspr.md)
