---
title: Inicjowanie obsługi administracyjnej użytkowników w aplikacji Azure AD Gallery trwa kilka godzin
description: Jak dowiedzieć się, dlaczego Inicjowanie obsługi aplikacji może potrwać dłużej niż oczekiwano
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.openlocfilehash: d663962dab058a108faa7d5310a3f2892c345a75
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593900"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Inicjowanie obsługi administracyjnej użytkowników w aplikacji z galerii usługi Azure AD trwa kilka godzin

Po pierwszym włączeniu automatycznej aprowizacji aplikacji cykl początkowy może zająć od 20 minut do kilku godzin, w zależności od rozmiaru katalogu usługi Azure AD i liczby użytkowników w zakresie aprowizacji. 

Kolejne synchronizacje po początkowym cyklu są szybsze, ponieważ usługa aprowizacji przechowuje znaki wodne, które reprezentują stan obu systemów po cyklu początkowym, co poprawia wydajność kolejnych synchronizacji.

## <a name="how-to-improve-provisioning-performance"></a>Jak zwiększyć wydajność aprowizacji

Jeśli cykl początkowy trwa dłużej niż kilka godzin, można wykonać jedną z nich, aby zwiększyć wydajność:

-   **Filtry określania zakresu użytkownika.** Filtry zakresu umożliwiają precyzyjne dostosowywanie danych, które usługa aprowizacji wyodrębnia z usługi Azure AD przez filtrowanie użytkowników na podstawie określonych wartości atrybutów. Aby uzyskać więcej informacji na temat określania zakresu filtrów, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Następne kroki
[Automatyzacja aprowizacji i cofania aprowizacji użytkowników dla aplikacji SaaS przy użyciu usługi Azure Active Directory](user-provisioning.md)

