---
title: Bezpieczny dostęp do hybrydowej usługi Azure AD | Microsoft Docs
description: W tym artykule opisano rozwiązania partnerskie służące do integrowania starszych aplikacji w chmurze lokalnej, publicznej lub prywatnej z usługą Azure AD. Zabezpiecz starsze aplikacje, łącząc kontrolery dostarczania aplikacji lub sieci z usługą Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 12/18/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 295891afbb0136e0b05bcd49f4045e0e8bcff6e5
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763044"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-app-delivery-controllers-and-networks"></a>Bezpieczny dostęp hybrydowy: Zabezpiecz starsze aplikacje za pomocą kontrolerów dostarczania aplikacji i sieci

Teraz możesz chronić starsze aplikacje do uwierzytelniania lokalnego i w chmurze, łącząc je z usługą Azure AD przy użyciu istniejącego kontrolera dostarczania aplikacji lub sieci. Dzięki temu można przyciągnąć przerwy i wzmocnić stan zabezpieczeń dla wszystkich aplikacji dzięki możliwościom usługi Azure AD, takim jak dostęp warunkowy usługi Azure AD i Azure AD Identity Protection.

Korzystając z istniejącego kontrolera sieci i dostarczania, można łatwo chronić starsze aplikacje, które nadal mają kluczowe znaczenie dla procesów firmy, ale nie można było chronić przed usługą Azure AD. Prawdopodobnie masz już wszystko, czego potrzebujesz, aby rozpocząć ochronę tych aplikacji.

![Obraz przedstawiający bezpieczny dostęp hybrydowy](media/secure-hybrid-access/secure-hybrid-access.png)

Następujący dostawcy oferują wstępnie skompilowane rozwiązania i szczegółowe wskazówki dotyczące integracji z usługą Azure AD.

* [Akamai dostęp do aplikacji (EAA)](../saas-apps/akamai-tutorial.md)
* [Kontroler dostarczania aplikacji Citrix (ADC)](../saas-apps/citrix-netscaler-tutorial.md)
* [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)
* [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
