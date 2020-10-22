---
author: baanders
description: Dołączanie pliku dla samouczków Digital bliźniaczych reprezentacji platformy Azure — Konfigurowanie przykładowego projektu
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: ff44e4ae45f8b7c6bff3685522440ca7118f337e
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92346914"
---
## <a name="configure-the-sample-project"></a>Konfigurowanie przykładowego projektu

Następnie skonfiguruj przykładową aplikację kliencką, która będzie współpracująca z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji.

Przejdź na swoją maszynę do pobranego wcześniej pliku z [*kompleksowej usługi Azure Digital bliźniaczych reprezentacji*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (i rozpakuj go, jeśli jeszcze nie zostało to zrobione).

Po umieszczeniu w folderze przejdź do _AdtSampleApp_. Otwórz _**AdtE2ESample. sln**_ w programie Visual Studio 2019. 

W programie Visual Studio wybierz pozycję _SampleClientApp > **appsettings.jsna** _ pliku, aby otworzyć ją w oknie edycji. Będzie to stanowić wstępnie ustawiony plik JSON z niezbędnymi zmiennymi konfiguracyjnymi do uruchomienia projektu.

W treści pliku Zmień `instanceUrl` adres URL *nazwy hosta* usługi Azure Digital bliźniaczych reprezentacji (z *https://* przed nim, jak pokazano poniżej).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Zapisz i zamknij plik. 

Następnie skonfiguruj *appsettings.jsw* pliku, który ma zostać skopiowany do katalogu wyjściowego podczas kompilowania *SampleClientApp*. W tym celu kliknij prawym przyciskiem myszy *appsettings.jsw* pliku, a następnie wybierz polecenie *właściwości.* W Inspektorze *Właściwości* Zmień wartość właściwości *Kopiuj do katalogu wyjściowego* na *Kopiuj, jeśli nowszy*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Fragment z okna programu Visual Studio z okienkiem Eksplorator rozwiązań z wyróżnioną appsettings.js, a okienko właściwości z właściwością &quot;Kopiuj do katalogu wyjściowego&quot; ma wartość &quot;Kopiuj, jeśli nowszy&quot;" border="false":::

Kontynuuj otwieranie projektu _**AdtE2ESample**_ w programie Visual Studio, aby nadal korzystać z niego w samouczku.

