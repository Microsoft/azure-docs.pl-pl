---
title: Jak usunąć dostęp użytkownika do aplikacji | Microsoft Docs
description: Informacje o usuwaniu dostępu użytkownika do aplikacji
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/17/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f9626c256755e2fce81b593d95b8680f4bb55ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763163"
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>Jak usunąć dostęp użytkownika do aplikacji

Ten artykuł pomaga zrozumieć, jak usunąć dostęp użytkownika do aplikacji.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>Chcę usunąć przypisanie określonego użytkownika lub grupy do aplikacji

Aby usunąć przypisanie użytkownika lub grupy do aplikacji, postępuj zgodnie z instrukcjami podanymi w temacie [usuwanie przypisania użytkownika lub grupy z aplikacji przedsiębiorstwa w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) artykule.

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>Chcę wyłączyć dostęp do aplikacji dla każdego użytkownika

Aby wyłączyć wszystkie logowania użytkownika do aplikacji, wykonaj kroki opisane w artykule [wyłączanie logowania użytkownika dla aplikacji przedsiębiorstwa w Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) .

## <a name="i-want-to-delete-an-application-entirely"></a>Chcę całkowicie usunąć aplikację

Aby **usunąć aplikację**, wykonaj następujące instrukcje:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji po lewej stronie Azure Active Directory.

5. Kliknij pozycję **wszystkie aplikacje** , aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być wyświetlana w tym miejscu, użyj kontrolki **filtru** w górnej części **listy wszystkie aplikacje** i ustaw opcję **Pokaż** na **wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz usunąć.

7. Po załadowaniu aplikacji kliknij przycisk **Usuń** ikonę w okienku **Przegląd** górnej aplikacji.

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
