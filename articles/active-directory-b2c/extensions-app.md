---
title: Aplikacja rozszerzeń w Azure Active Directory B2C | Microsoft Docs
description: Przywracanie B2C-Extensions-App.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: accd32f376c6030900a9f9a3c29547118d0a1a7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388514"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: aplikacja rozszerzeń

Po utworzeniu katalogu Azure AD B2C aplikacja o nazwie `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` zostanie utworzona automatycznie wewnątrz nowego katalogu. Ta aplikacja, nazywana **B2C-Extensions-App**, jest widoczna w *rejestracje aplikacji*. Jest on używany przez usługę Azure AD B2C do przechowywania informacji o użytkownikach i atrybutach niestandardowych. Jeśli aplikacja zostanie usunięta, Azure AD B2C nie będzie działać poprawnie, a Twoje środowisko produkcyjne będzie miało oddziaływać.

> [!IMPORTANT]
> Nie usuwaj B2C-Extensions-App, chyba że planujesz natychmiastowe usunięcie dzierżawy. Jeśli aplikacja pozostanie usunięta przez ponad 30 dni, informacje o użytkowniku zostaną trwale utracone.

## <a name="verifying-that-the-extensions-app-is-present"></a>Weryfikowanie, czy aplikacja rozszerzeń jest obecna

Aby sprawdzić, czy B2C-Extensions-App jest obecna:

1. W ramach dzierżawy Azure AD B2C kliknij pozycję **wszystkie usługi** w menu nawigacji po lewej stronie.
1. Wyszukaj i Otwórz **rejestracje aplikacji**.
1. Wyszukaj aplikację rozpoczynającą się od **B2C-Extensions-App**

## <a name="recover-the-extensions-app"></a>Odzyskaj aplikację rozszerzeń

Jeśli przypadkowo usunięto aplikację B2C-Extensions-App, masz 30 dni na jej odzyskanie. Możesz przywrócić aplikację przy użyciu interfejs API programu Graph:

1. Przejdź do [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Zaloguj się do witryny jako Administrator globalny katalogu Azure AD B2C, do którego chcesz przywrócić usuniętą aplikację. Ten Administrator globalny musi mieć adres e-mail podobny do poniższego: `username@{yourTenant}.onmicrosoft.com` .
1. Wydaj protokół HTTP GET względem adresu URL `https://graph.windows.net/myorganization/deletedApplications` z interfejsem API-Version = 1.6. Ta operacja spowoduje wyświetlenie listy wszystkich aplikacji, które zostały usunięte w ciągu ostatnich 30 dni.
1. Znajdź aplikację na liście, której nazwa zaczyna się od "B2C-Extension-App" i skopiuj jej `objectid` wartość właściwości.
1. Wydaj wpis HTTP na adres URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore` . Zastąp `{OBJECTID}` część adresu URL z `objectid` poprzedniego kroku.

Teraz powinno być możliwe [wyświetlenie przywróconej aplikacji](#verifying-that-the-extensions-app-is-present) w Azure Portal.

> [!NOTE]
> Aplikacja może zostać przywrócona tylko wtedy, gdy została usunięta w ciągu ostatnich 30 dni. Jeśli wystąpiła więcej niż 30 dni, dane zostaną trwale utracone. Aby uzyskać więcej pomocy, Zaproś o pomoc techniczną.
