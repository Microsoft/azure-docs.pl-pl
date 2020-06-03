---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: bbbc606497b53a0790d667a63b45101a3c18bbc4
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84297495"
---
#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)**.
1. Wybierz aplikację *webapi1* , aby otworzyć jej stronę **przeglądu** .
1. W obszarze **Zarządzaj**wybierz pozycję **uwidaczniaj interfejs API**.
1. W polu **Identyfikator URI identyfikatora aplikacji**wybierz łącze **Ustaw** .
1. Zastąp wartość domyślną (GUID) wartością `api` , a następnie wybierz pozycję **Zapisz**. Pełny identyfikator URI jest wyświetlany i powinien być w formacie `https://your-tenant-name.onmicrosoft.com/api` . Gdy aplikacja sieci Web żąda tokenu dostępu dla interfejsu API, powinien dodać ten identyfikator URI jako prefiks dla każdego zakresu zdefiniowanego dla interfejsu API.
1. W obszarze **zakresy zdefiniowane przez ten interfejs API**wybierz pozycję **Dodaj zakres**.
1. Wprowadź następujące wartości, aby utworzyć zakres, który definiuje dostęp do odczytu do interfejsu API, a następnie wybierz pozycję **Dodaj zakres**:
    1. **Nazwa zakresu**:`demo.read`
    1. **Nazwa wyświetlana zgody administratora**:`Read access to demo API`
    1. **Opis zgody administratora**:`Allows read access to the demo API`
1. Wybierz pozycję **Dodaj zakres**, wprowadź następujące wartości, aby dodać zakres, który definiuje dostęp do zapisu do interfejsu API, a następnie wybierz pozycję **Dodaj zakres**:
    1. **Nazwa zakresu**:`demo.write`
    1. **Nazwa wyświetlana zgody administratora**:`Write access to demo API`
    1. **Opis zgody administratora**:`Allows write access to the demo API`

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Wybierz pozycję **aplikacje**.
1. Wybierz aplikację *webapi1* , aby otworzyć jej stronę **Właściwości** .
1. Wybierz pozycję **Opublikowane zakresy**. Opublikowane zakresy mogą służyć do przyznawania aplikacji klienckiej określonych uprawnień do internetowego interfejsu API.
1. W obszarze **zakres**wprowadź tekst `demo.read` i wpisz **polecenie** `Read access to the web API` .
1. W obszarze **zakres**wprowadź tekst `demo.write` i wpisz **polecenie** `Write access to the web API` .
1. Wybierz pozycję **Zapisz**.