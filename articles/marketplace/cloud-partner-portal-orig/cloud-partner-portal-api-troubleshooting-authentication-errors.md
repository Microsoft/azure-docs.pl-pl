---
title: Rozwiązywanie typowych błędów uwierzytelniania | Portal Azure Marketplace
description: Zapewnia pomoc dotyczącą typowych błędów uwierzytelniania podczas korzystania z portal Cloud Partner interfejsów API.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: d8fd1eb4bef987b4a8605e4be780512a914ec8b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255995"
---
# <a name="troubleshooting-common-authentication-errors"></a>Rozwiązywanie typowych błędów uwierzytelniania

> [!NOTE]
> Interfejsy API portal Cloud Partner są zintegrowane z centrum partnerskim i będą nadal działały po przeprowadzeniu migracji ofert do Centrum partnerskiego. W ramach integracji wprowadzono niewielkie zmiany. Przejrzyj zmiany wymienione w [Portal Cloud partner dokumentacja interfejsu API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , aby upewnić się, że kod będzie nadal działał po migracji do Centrum partnerskiego.

Ten artykuł zawiera informacje o typowych błędach uwierzytelniania podczas korzystania z portal Cloud Partner interfejsów API.

## <a name="unauthorized-error"></a>Nieautoryzowany błąd

Jeśli stale pojawiają `401 unauthorized` się błędy, sprawdź, czy masz prawidłowy token dostępu.  Jeśli jeszcze tego nie zrobiono, Utwórz podstawową aplikację Azure Active Directory (Azure AD) i nazwę główną usługi zgodnie z opisem w temacie [Korzystanie z portalu, aby utworzyć aplikację Azure Active Directory i nazwę główną usługi, które mogą uzyskiwać dostęp do zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Następnie do zweryfikowania dostępu Użyj aplikacji lub prostego żądania POST protokołu HTTP.  W celu uzyskania tokenu dostępu zostanie uwzględniony identyfikator dzierżawy, identyfikator aplikacji, identyfikator obiektu i klucz tajny, jak pokazano na poniższej ilustracji:

![Rozwiązywanie problemów z błędem 401](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-401-error.jpg)


## <a name="forbidden-error"></a>Błąd „Niedozwolone”

Jeśli `403 forbidden` wystąpi błąd, upewnij się, że poprawna jednostka usługi została dodana do konta wydawcy w Portal Cloud partner.
Postępuj zgodnie z instrukcjami na stronie [wymagania wstępne](./cloud-partner-portal-api-prerequisites.md) , aby dodać nazwę główną usługi do portalu.

Jeśli została dodana poprawna jednostka usługi, sprawdź wszystkie pozostałe informacje. Zwróć szczególną uwagę na identyfikator obiektu wprowadzony w portalu. Na stronie rejestracji aplikacji Azure Active Directory znajdują się dwa identyfikatory obiektów i należy użyć lokalnego identyfikatora obiektu. Poprawną wartość można znaleźć, przechodząc do strony **rejestracje aplikacji** aplikacji i klikając nazwę aplikacji **w obszarze zarządzana aplikacja w katalogu lokalnym**. Spowoduje to przejście do właściwości lokalnych aplikacji, gdzie można znaleźć prawidłowy identyfikator obiektu na stronie **Właściwości** , jak pokazano na poniższej ilustracji. Ponadto upewnij się, że podczas dodawania nazwy głównej usługi i wywołania interfejsu API używasz poprawnego identyfikatora wydawcy.

![Rozwiązywanie problemów z błędem 403](./media/cloud-partner-portal-api-troubleshooting-authentication-errors/troubleshooting-403-error.jpg)
