---
author: baanders
description: Dołączanie pliku dla samouczków Digital bliźniaczych reprezentacji platformy Azure — Konfigurowanie przykładowego projektu
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: d442c5782af1f55cd5d894823945d517dbc48091
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92323594"
---
## <a name="configure-the-sample-project"></a>Konfigurowanie przykładowego projektu

Następnie skonfiguruj przykładową aplikację kliencką, która będzie współpracująca z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji.

Przejdź na swoją maszynę do pobranego wcześniej pliku z [*kompleksowej usługi Azure Digital bliźniaczych reprezentacji*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (i rozpakuj go, jeśli jeszcze nie zostało to zrobione).

Po umieszczeniu w folderze przejdź do _AdtSampleApp_. Otwórz _**AdtE2ESample. sln**_ w programie Visual Studio 2019. 

W programie Visual Studio użyj okienka *Eksplorator rozwiązań* , aby utworzyć kopię _serviceConfig.js> SampleClientApp **na. Plik szablonu** _ (możesz użyć menu z prawym przyciskiem myszy w celu skopiowania i wklejenia). Zmień nazwę *serviceConfig.jskopii na*. Będzie to stanowić wstępnie ustawiony plik JSON z niezbędnymi zmiennymi konfiguracyjnymi do uruchomienia projektu.

Wybierz *serviceConfig.jsna* pliku, aby otworzyć go w oknie edycji. Zmień wartość `instanceUrl` na adres URL *nazwy hosta* usługi Azure Digital bliźniaczych reprezentacji (z *https://* przed nim, jak pokazano poniżej).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```



Zapisz i zamknij plik. 

Następnie skonfiguruj *serviceConfig.jsw* pliku, który ma zostać skopiowany do katalogu wyjściowego podczas kompilowania *SampleClientApp*. W tym celu kliknij prawym przyciskiem myszy *serviceConfig.jsw* pliku, a następnie wybierz polecenie *właściwości.* W Inspektorze *Właściwości* Zmień wartość właściwości *Kopiuj do katalogu wyjściowego* na *Kopiuj, jeśli nowszy*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Fragment z okna programu Visual Studio z okienkiem Eksplorator rozwiązań z wyróżnioną serviceConfig.js, a okienko właściwości z właściwością &quot;Kopiuj do katalogu wyjściowego&quot; ma wartość &quot;Kopiuj, jeśli nowszy&quot;" border="false":::

Kontynuuj otwieranie projektu _**AdtE2ESample**_ w programie Visual Studio, aby nadal korzystać z niego w samouczku.

