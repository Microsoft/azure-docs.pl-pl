---
title: Odporność na zarządzanie tożsamościami i dostępem klientów przy użyciu Azure AD B2C | Microsoft Docs
description: Metody tworzenia odporności w zarządzaniu tożsamościami i dostępem klientów przy użyciu Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b40918db03c260f899c36d306c892b787cc6371c
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724895"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>Tworzenie odporności w ramach zarządzania tożsamościami i dostępem klientów przy użyciu Azure Active Directory B2C

[Azure Active Directory (AD) B2C](../../active-directory-b2c/overview.md) to platforma zarządzania tożsamościami i dostępem klientów (CIAM), która umożliwia pomyślne uruchamianie krytycznych aplikacji dla klientów. Mamy wiele wbudowanych funkcji [odporności](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) , które zaprojektowano, aby ułatwić naszym potrzebom skalowanie usług i zwiększyć odporność na awarie. Ponadto podczas uruchamiania aplikacji o krytycznym znaczeniu należy wziąć pod uwagę różne elementy projektu i konfiguracji w aplikacji, a także sposób konfigurowania aplikacji w ramach Azure AD B2C, aby zapewnić odporny na awarie lub scenariusze niepowodzeń. W tym artykule omówiono niektóre najlepsze rozwiązania, które ułatwiają zwiększenie odporności.

Usługa odporna na błędy jest taka, która kontynuuje działanie Pomimo przerw w działaniu. Możesz pomóc zwiększyć odporność usługi przez:

- Informacje o wszystkich składnikach

- eliminowanie pojedynczych punktów awarii

- Izolowanie niezakończonych składników w celu ograniczenia ich wpływu

- zapewnianie nadmiarowości przy użyciu mechanizmów szybkiego trybu failover i ścieżek odzyskiwania

Podczas opracowywania aplikacji zalecamy rozważenie [zwiększenia odporności uwierzytelniania i autoryzacji w aplikacjach](resilience-app-development-overview.md) za pomocą składników tożsamości rozwiązania. W tym artykule podjęto próbę rozwiązania ulepszeń dotyczących odporności specyficznych dla aplikacji Azure AD B2C. Nasze zalecenia są pogrupowane według funkcji CIAM.

![Ilustracja przedstawia składniki CIAM ](media/resilience-b2c/high-level-components.png) w kolejnych sekcjach. przeprowadzimy Cię przez proces tworzenia odporności w następujących obszarach:

- [Środowisko użytkownika końcowego](resilient-end-user-experience.md): Włącz plan rezerwowy dla przepływu uwierzytelniania i ograniczanie potencjalnego wpływu działania usługi uwierzytelniania Azure AD B2C.

- [Interfejsy z procesami zewnętrznymi](resilient-external-processes.md): Tworzenie odporności aplikacji i interfejsów przez odzyskiwanie po błędach.  

- [Najlepsze rozwiązania dla deweloperów](resilience-b2c-developer-best-practices.md): Unikaj delikatności ze względu na typowe problemy z zasadami niestandardowymi i poprawiaj obsługę błędów w obszarach, takich jak interakcje z weryfikatorami oświadczeń, aplikacje innych firm i interfejsy API REST.

- [Monitorowanie i analiza](resilience-with-monitoring-alerting.md): ocenianie kondycji usługi przez monitorowanie kluczowych wskaźników i wykrywanie błędów i zakłócenia wydajności za pomocą alertów.

- [Tworzenie odporności w infrastrukturze uwierzytelniania](resilience-in-infrastructure.md)

- [Zwiększanie odporności uwierzytelniania i autoryzacji w aplikacjach](resilience-app-development-overview.md)

Obejrzyj ten film wideo, aby dowiedzieć się, jak tworzyć odporne i skalowalne przepływy przy użyciu Azure AD B2C.
>[!Video https://www.youtube.com/embed/8f_Ozpw9yTs]
