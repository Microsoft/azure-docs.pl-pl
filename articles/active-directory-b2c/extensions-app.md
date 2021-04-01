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
ms.openlocfilehash: dc536fa4292d794e8d89a2564ad10a3c10dd0a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94560864"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: aplikacja rozszerzeń

Po utworzeniu katalogu Azure AD B2C aplikacja o nazwie `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` zostanie utworzona automatycznie wewnątrz nowego katalogu. Ta aplikacja, nazywana **B2C-Extensions-App**, jest widoczna w *rejestracje aplikacji*. Jest on używany przez usługę Azure AD B2C do przechowywania informacji o użytkownikach i atrybutach niestandardowych. Jeśli ta aplikacja zostanie usunięta, usługa Azure AD B2C nie będzie działać poprawnie, co będzie miało wpływ na Twoje środowisko produkcyjne.

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
1. Znajdź aplikację na liście, której nazwa zaczyna się od "B2C-Extensions-App" i skopiuj jej `objectid` wartość właściwości.
1. Wydaj wpis HTTP na adres URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore` . Zastąp `{OBJECTID}` część adresu URL z `objectid` poprzedniego kroku.

Teraz powinno być możliwe [wyświetlenie przywróconej aplikacji](#verifying-that-the-extensions-app-is-present) w Azure Portal.

> [!NOTE]
> Aplikacja może zostać przywrócona tylko wtedy, gdy została usunięta w ciągu ostatnich 30 dni. Jeśli wystąpiła więcej niż 30 dni, dane zostaną trwale utracone. Aby uzyskać więcej pomocy, Zaproś o pomoc techniczną.
