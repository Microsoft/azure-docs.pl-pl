---
title: Jak usunąć dostęp użytkownika do aplikacji w Azure Active Directory
description: Informacje na temat usuwania dostępu użytkownika do aplikacji w Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: kenwith
ms.openlocfilehash: e6a6c00811a7b87156802897db62a4a10130f130
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257359"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Jak usunąć dostęp użytkownika do aplikacji

Ten artykuł pomaga zrozumieć, jak usunąć dostęp użytkownika do aplikacji.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chcę usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć przypisanie użytkownika lub grupy do aplikacji, postępuj zgodnie z instrukcjami podanymi w temacie [usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa w Azure Active Directory](./assign-user-or-group-access-portal.md) artykule.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chcę wyłączyć dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkownika do aplikacji, wykonaj kroki opisane w artykule [wyłączanie logowania użytkownika dla aplikacji przedsiębiorstwa w Azure Active Directory](./disable-user-sign-in-portal.md) .

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę całkowicie usunąć aplikację

[Seria szybkiego startu w zarządzaniu aplikacjami](delete-application-portal.md) obejmuje wskazówki dotyczące usuwania aplikacji z dzierżawy Azure Active Directory.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcę wyłączyć wszystkie przyszłe operacje wyrażania zgody użytkownika na dowolną aplikację

Wyłączenie zgody użytkownika na cały katalog uniemożliwi użytkownikom końcowym przesyłanie ich do żadnej aplikacji. Administratorzy mogą w dalszym ciągu wyrazić zgodę w imieniu użytkownika. Aby uzyskać więcej informacji o zgodzie aplikacji i o tym, dlaczego możesz lub nie chcieć tego zrobić, przeczytaj artykuł [Informacje o zgodzie użytkownika i administratora](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent). Zobacz również [uprawnienia i zgody](../develop/v2-permissions-and-consent.md).

Aby **wyłączyć wszystkie przyszłe operacje wyrażania zgody użytkowników w całym katalogu**, wykonaj następujące instrukcje:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** 

3.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji.

5.  Kliknij pozycję **Ustawienia użytkownika**.

6.  Ustawienie **Użytkownicy mogą zezwalać aplikacjom na dostęp do danych firmowych w ich imieniu** , przełączać się do **nie** i klikać przycisk Zapisz.


## <a name="next-steps"></a>Następne kroki

[Zarządzanie dostępem do aplikacji](what-is-access-management.md)