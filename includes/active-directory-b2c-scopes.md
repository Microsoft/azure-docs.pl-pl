---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 3ebe1ec4c0292a530e5ef2c754e9b002e931300e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994324"
---
#### <a name="app-registrations"></a>[Rejestracje aplikacji](#tab/app-reg-ga/) 

1. Wybierz pozycję **Rejestracje aplikacji**.
1. Wybierz aplikację *webapi1* , aby otworzyć jej stronę **przeglądu** .
1. W obszarze **Zarządzaj** wybierz pozycję **uwidaczniaj interfejs API**.
1. W polu **Identyfikator URI identyfikatora aplikacji** wybierz łącze **Ustaw** .
1. Zastąp wartość domyślną (GUID) wartością `api` , a następnie wybierz pozycję **Zapisz**. Pełny identyfikator URI jest wyświetlany i powinien być w formacie `https://your-tenant-name.onmicrosoft.com/api` . Gdy aplikacja sieci Web żąda tokenu dostępu dla interfejsu API, powinien dodać ten identyfikator URI jako prefiks dla każdego zakresu zdefiniowanego dla interfejsu API.
1. W obszarze **zakresy zdefiniowane przez ten interfejs API** wybierz pozycję **Dodaj zakres**.
1. Wprowadź następujące wartości, aby utworzyć zakres, który definiuje dostęp do odczytu do interfejsu API, a następnie wybierz pozycję **Dodaj zakres**:
    1. **Nazwa zakresu**: `demo.read`
    1. **Nazwa wyświetlana zgody administratora**: `Read access to demo API`
    1. **Opis zgody administratora**: `Allows read access to the demo API`
1. Wybierz pozycję **Dodaj zakres**, wprowadź następujące wartości, aby dodać zakres, który definiuje dostęp do zapisu do interfejsu API, a następnie wybierz pozycję **Dodaj zakres**:
    1. **Nazwa zakresu**: `demo.write`
    1. **Nazwa wyświetlana zgody administratora**: `Write access to demo API`
    1. **Opis zgody administratora**: `Allows write access to the demo API`

#### <a name="applications-legacy"></a>[Aplikacje (starsze)](#tab/applications-legacy/)

1. Wybierz pozycję **aplikacje (starsza wersja)**.
1. Wybierz aplikację *webapi1* , aby otworzyć jej stronę **Właściwości** .
1. Wybierz pozycję **Opublikowane zakresy**. Opublikowane zakresy mogą służyć do przyznawania aplikacji klienckiej określonych uprawnień do internetowego interfejsu API.
1. W obszarze **zakres** wprowadź tekst `demo.read` i wpisz **polecenie** `Read access to the web API` .
1. W obszarze **zakres** wprowadź tekst `demo.write` i wpisz **polecenie** `Write access to the web API` .
1. Wybierz pozycję **Zapisz**.