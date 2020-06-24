---
title: Inicjowanie obsługi administracyjnej użytkowników w aplikacji Azure AD Gallery trwa kilka godzin
description: Jak dowiedzieć się, dlaczego Inicjowanie obsługi aplikacji może potrwać dłużej niż oczekiwano
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: f0f572e5153b6e79bf7731d0d57b66e601a0054c
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84781977"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Inicjowanie obsługi administracyjnej użytkowników w aplikacji z galerii usługi Azure AD trwa kilka godzin

Po pierwszym włączeniu automatycznej aprowizacji aplikacji cykl początkowy może zająć od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie aprowizacji. 

Kolejne synchronizacje po początkowym cyklu są szybsze, ponieważ usługa aprowizacji przechowuje znaki wodne, które reprezentują stan obu systemów po cyklu początkowym, co poprawia wydajność kolejnych synchronizacji.

## <a name="how-to-improve-provisioning-performance"></a>Jak zwiększyć wydajność aprowizacji

Jeśli cykl początkowy trwa dłużej niż kilka godzin, można wykonać jedną z nich, aby zwiększyć wydajność:

-   **Filtry określania zakresu użytkownika.** Filtry zakresu umożliwiają precyzyjne dostosowywanie danych, które usługa aprowizacji wyodrębnia z usługi Azure AD przez filtrowanie użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Następne kroki
[Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)

