---
title: Rozwiązywanie typowych błędów uwierzytelniania, Azure Marketplace
description: Zapewnia pomoc dotyczącą typowych błędów uwierzytelniania podczas korzystania z interfejsów API portal Cloud Partner w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 01af5357e4ae2f4dfb317a0931a8d0bc2b2d54e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89487321"
---
# <a name="troubleshooting-common-authentication-errors"></a>Rozwiązywanie typowych błędów uwierzytelniania

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z usługą i będą nadal działać w centrum partnerskim. Przejście wprowadza niewielkie zmiany. Przejrzyj zmiany wymienione w [dokumentacji interfejsu API Portal Cloud partner](./cloud-partner-portal-api-overview.md) , aby upewnić się, że kod będzie kontynuował pracę po przejściu do Centrum partnerskiego. Interfejsy API CPP powinny być używane tylko dla istniejących produktów, które zostały już zintegrowane przed przejściem do Centrum partnerskiego; nowe produkty powinny używać interfejsów API przekazywania Centrum partnerskiego.

Ten artykuł zawiera informacje o typowych błędach uwierzytelniania podczas korzystania z portal Cloud Partner interfejsów API.

## <a name="unauthorized-error"></a>Nieautoryzowany błąd

Jeśli stale pojawiają się `401 unauthorized` błędy, sprawdź, czy masz prawidłowy token dostępu.  Jeśli jeszcze tego nie zrobiono, Utwórz podstawową aplikację Azure Active Directory (Azure AD) i nazwę główną usługi zgodnie z opisem w temacie [Korzystanie z portalu, aby utworzyć aplikację Azure Active Directory i nazwę główną usługi, które mogą uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md). Następnie do zweryfikowania dostępu Użyj aplikacji lub prostego żądania POST protokołu HTTP.  W celu uzyskania tokenu dostępu zostanie uwzględniony identyfikator dzierżawy, identyfikator aplikacji, identyfikator obiektu i klucz tajny.

## <a name="forbidden-error"></a>Błąd „Niedozwolone”

Jeśli `403 forbidden` wystąpi błąd, upewnij się, że poprawna jednostka usługi została dodana do konta wydawcy w Portal Cloud partner. Postępuj zgodnie z instrukcjami na stronie [wymagania wstępne](./cloud-partner-portal-api-prerequisites.md) , aby dodać nazwę główną usługi do portalu.

Jeśli została dodana poprawna jednostka usługi, sprawdź wszystkie pozostałe informacje. Zwróć szczególną uwagę na identyfikator obiektu wprowadzony w portalu. Na stronie rejestracji aplikacji Azure Active Directory znajdują się dwa identyfikatory obiektów i należy użyć lokalnego identyfikatora obiektu. Poprawną wartość można znaleźć, przechodząc do strony **rejestracje aplikacji** aplikacji i klikając nazwę aplikacji **w obszarze zarządzana aplikacja w katalogu lokalnym**. Spowoduje to przejście do właściwości lokalnych aplikacji, gdzie można znaleźć prawidłowy identyfikator obiektu na stronie **Właściwości** , jak pokazano na poniższej ilustracji. Ponadto upewnij się, że podczas dodawania nazwy głównej usługi i wywołania interfejsu API używasz poprawnego identyfikatora wydawcy.
