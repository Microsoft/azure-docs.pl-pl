---
title: Rozpoczynanie pracy z usługą Azure AD w projektach programu Visual Studio WebApi
description: Jak rozpocząć korzystanie z Azure Active Directory w projektach WebApi po nawiązaniu połączenia z usługą Azure AD lub utworzeniu jej przy użyciu usług połączonych programu Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: c76aabbfce02d7e2bd8361dc99b4ba20d1f62f15
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114493"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Wprowadzenie do Azure Active Directory (projekty WebApi)

> [!div class="op_single_selector"]
> - [Wprowadzenie](vs-active-directory-webapi-getting-started.md)
> - [Co się stało](vs-active-directory-webapi-what-happened.md)

Ten artykuł zawiera dodatkowe wskazówki po dodaniu Active Directory do projektu ASP.NET WebAPI za pomocą polecenia **project > Connect Services** w programie Visual Studio. Jeśli usługa nie została jeszcze dodana do projektu, możesz to zrobić w dowolnym momencie.

Zobacz, [co się stało z moim projektem WebAPI?](vs-active-directory-webapi-what-happened.md) dla zmian wprowadzonych w projekcie podczas dodawania połączonej usługi.

## <a name="requiring-authentication-to-access-controllers"></a>Wymaganie uwierzytelniania do kontrolerów dostępu

Wszystkie kontrolery w projekcie zostały wbudowane przy użyciu `[Authorize]` atrybutu. Ten atrybut wymaga uwierzytelnienia użytkownika przed uzyskaniem dostępu do interfejsów API zdefiniowanych przez te kontrolery. Aby umożliwić dostęp do kontrolera anonimowo, Usuń ten atrybut z kontrolera. Jeśli chcesz ustawić uprawnienia na bardziej szczegółowym poziomie, zastosuj atrybut do każdej metody wymagającej autoryzacji zamiast zastosowania jej do klasy kontrolera.

## <a name="next-steps"></a>Następne kroki

- [Scenariusze uwierzytelniania dla Azure Active Directory](./authentication-vs-authorization.md)
- [dodawanie logowania przy użyciu konta Microsoft do aplikacji internetowej ASP.NET](quickstart-v2-aspnet-webapp.md)