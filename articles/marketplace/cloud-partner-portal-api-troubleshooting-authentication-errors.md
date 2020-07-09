---
title: Rozwiązywanie typowych błędów uwierzytelniania | Portal Azure Marketplace
description: Zapewnia pomoc dotyczącą typowych błędów uwierzytelniania podczas korzystania z portal Cloud Partner interfejsów API.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 8c9bd6dbe54f797090c5f7405ad709db954f8fb9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115472"
---
# <a name="troubleshooting-common-authentication-errors"></a>Rozwiązywanie typowych błędów uwierzytelniania

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Ten artykuł zawiera informacje o typowych błędach uwierzytelniania podczas korzystania z portal Cloud Partner interfejsów API.

## <a name="unauthorized-error"></a>Nieautoryzowany błąd

Jeśli stale pojawiają się `401 unauthorized` błędy, sprawdź, czy masz prawidłowy token dostępu.  Jeśli jeszcze tego nie zrobiono, Utwórz podstawową aplikację Azure Active Directory (Azure AD) i nazwę główną usługi zgodnie z opisem w temacie [Korzystanie z portalu, aby utworzyć aplikację Azure Active Directory i nazwę główną usługi, które mogą uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md). Następnie do zweryfikowania dostępu Użyj aplikacji lub prostego żądania POST protokołu HTTP.  W celu uzyskania tokenu dostępu zostanie uwzględniony identyfikator dzierżawy, identyfikator aplikacji, identyfikator obiektu i klucz tajny, jak pokazano na poniższej ilustracji:

![Rozwiązywanie problemów z błędem 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Błąd „Niedozwolone”

Jeśli `403 forbidden` wystąpi błąd, upewnij się, że poprawna jednostka usługi została dodana do konta wydawcy w Portal Cloud partner.
Postępuj zgodnie z instrukcjami na stronie [wymagania wstępne](./cloud-partner-portal-api-prerequisites.md) , aby dodać nazwę główną usługi do portalu.

Jeśli została dodana poprawna jednostka usługi, sprawdź wszystkie pozostałe informacje. Zwróć szczególną uwagę na identyfikator obiektu wprowadzony w portalu. Na stronie rejestracji aplikacji Azure Active Directory znajdują się dwa identyfikatory obiektów i należy użyć lokalnego identyfikatora obiektu. Poprawną wartość można znaleźć, przechodząc do strony **rejestracje aplikacji** aplikacji i klikając nazwę aplikacji **w obszarze zarządzana aplikacja w katalogu lokalnym**. Spowoduje to przejście do właściwości lokalnych aplikacji, gdzie można znaleźć prawidłowy identyfikator obiektu na stronie **Właściwości** , jak pokazano na poniższej ilustracji. Ponadto upewnij się, że podczas dodawania nazwy głównej usługi i wywołania interfejsu API używasz poprawnego identyfikatora wydawcy.

![Rozwiązywanie problemów z błędem 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
