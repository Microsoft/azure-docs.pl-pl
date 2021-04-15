---
title: Jak usunąć dostęp użytkownika do aplikacji w programie Azure Active Directory
description: Dowiedz się, jak usunąć dostęp użytkownika do aplikacji w Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: iangithinji
ms.openlocfilehash: 958abc5f9be443d66037a6d9fe8d8779e6e37e0e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379591"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Jak usunąć dostęp użytkownika do aplikacji

Ten artykuł ułatwia zrozumienie sposobu usuwania dostępu użytkownika do aplikacji.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chcę usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć przypisanie użytkownika lub grupy do aplikacji, wykonaj kroki opisane w artykule Usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa w Azure Active Directory [aplikacji.](./assign-user-or-group-access-portal.md)

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chcę wyłączyć cały dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkowników do aplikacji, wykonaj kroki opisane w artykule Wyłączanie logowania użytkowników dla aplikacji przedsiębiorstwa w Azure Active Directory [aplikacji.](./disable-user-sign-in-portal.md)

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę całkowicie usunąć aplikację

Seria [przewodników Szybki start dotyczących zarządzania aplikacją](delete-application-portal.md) zawiera wskazówki dotyczące usuwania aplikacji z Azure Active Directory dzierżawy.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>Chcę wyłączyć wszystkie przyszłe operacje wyrażania zgody przez użytkownika dla dowolnej aplikacji

Wyłączenie zgody użytkownika dla całego katalogu uniemożliwia użytkownikom końcowych wyrażanie zgody na dowolną aplikację. Administratorzy nadal mogą wyrazić zgodę w imieniu użytkownika. Aby uzyskać więcej informacji na temat zgody aplikacji i powodów, dla których możesz tego nie chcieć zrobić, przeczytaj Opis zgody użytkownika [i administratora.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent) Zobacz też Uprawnienia [i zgoda](../develop/v2-permissions-and-consent.md).

Aby **wyłączyć wszystkie przyszłe operacje wyrażania zgody przez** użytkownika w całym katalogu, wykonaj następujące instrukcje:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwieranie **Azure Active Directory rozszerzenia** 

3.  Kliknij **pozycję Aplikacje dla przedsiębiorstw** w menu nawigacji.

5.  Kliknij **pozycję Ustawienia użytkownika.**

6.  Ustaw przełącznik **Użytkownicy mogą zezwalać aplikacjom na dostęp** do danych firmy w ich imieniu na wartość **Nie** i kliknij przycisk Zapisz.


## <a name="next-steps"></a>Następne kroki

[Zarządzanie dostępem do aplikacji](what-is-access-management.md)