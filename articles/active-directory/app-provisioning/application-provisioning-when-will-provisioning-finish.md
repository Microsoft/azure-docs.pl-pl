---
title: Inicjowanie obsługi administracyjnej użytkowników w aplikacji Azure AD Gallery trwa kilka godzin
description: Jak dowiedzieć się, dlaczego Inicjowanie obsługi aplikacji może potrwać dłużej niż oczekiwano
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22d19733fbeb162fba55dd732c10e552879c78
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522649"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Inicjowanie obsługi administracyjnej użytkowników w aplikacji z galerii usługi Azure AD trwa kilka godzin

Po pierwszym włączeniu automatycznej aprowizacji aplikacji cykl początkowy może zająć od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie aprowizacji. 

Kolejne synchronizacje po początkowym cyklu są szybsze, ponieważ usługa aprowizacji przechowuje znaki wodne, które reprezentują stan obu systemów po cyklu początkowym, co poprawia wydajność kolejnych synchronizacji.

## <a name="how-to-improve-provisioning-performance"></a>Jak zwiększyć wydajność aprowizacji

Jeśli cykl początkowy trwa dłużej niż kilka godzin, można wykonać jedną z nich, aby zwiększyć wydajność:

-   **Filtry określania zakresu użytkownika.** Filtry zakresu umożliwiają precyzyjne dostosowywanie danych, które usługa aprowizacji wyodrębnia z usługi Azure AD przez filtrowanie użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Następne kroki
[Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)

