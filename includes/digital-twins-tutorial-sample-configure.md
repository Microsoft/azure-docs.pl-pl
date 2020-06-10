---
author: baanders
description: Dołączanie pliku dla samouczków Digital bliźniaczych reprezentacji platformy Azure — Konfigurowanie przykładowego projektu
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: f7b3bf347b15ebad2403f3aa64c0f3fd6c4be052
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613536"
---
## <a name="configure-the-sample-project"></a>Konfigurowanie przykładowego projektu

Następnie skonfiguruj przykładową aplikację kliencką, która będzie współpracująca z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Jeśli nie pobrano jeszcze przykładowego projektu, Pobierz go teraz, [pobierając repozytorium przykładów cyfrowych bliźniaczych reprezentacji na platformie Azure jako plik zip](https://github.com/Azure-Samples/digital-twins-samples/archive/master.zip). 

Przejdź do pobranego pliku na maszynie i rozpakuj go.

Po umieszczeniu wewnątrz folderu rozpakowanego przejdź do _Digital-bliźniaczych reprezentacji-Samples-Master/AdtSampleApp/_. Otwórz _**AdtE2ESample. sln**_ w programie Visual Studio 2019. 

W programie Visual Studio użyj okienka *Eksplorator rozwiązań* , aby utworzyć kopię pliku _SampleClientApp > **ServiceConfig. JSON. Template** _ (możesz użyć menu z prawym przyciskiem myszy w celu skopiowania i wklejenia). Zmień nazwę *pliku ServiceConfig. JSON*. Będzie to stanowić wstępnie ustawiony plik JSON z niezbędnymi zmiennymi konfiguracyjnymi do uruchomienia projektu.

Wybierz nowy plik, aby otworzyć go w oknie edycji. Zmień wartość `tenantId` na *Identyfikator katalogu*, `clientId` *Identyfikator aplikacji*i `instanceUrl` adres URL *nazwy hosta* usługi Azure Digital bliźniaczych reprezentacji (z *https://* przed nim, jak pokazano poniżej).

```json
{
  "tenantId": "<your-directory-ID>",
  "clientId": "<your-application-ID>",
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Zapisz i zamknij plik. 

Następnie skonfiguruj plik *ServiceConfig. JSON* , który ma zostać skopiowany do katalogu wyjściowego podczas kompilowania *SampleClientApp*. W tym celu kliknij prawym przyciskiem myszy plik *ServiceConfig. JSON* , a następnie wybierz polecenie *właściwości.* W Inspektorze *Właściwości* Zmień wartość właściwości *Kopiuj do katalogu wyjściowego* na *Kopiuj, jeśli nowszy*.

:::image type="content" source="../articles/digital-twins/media/include-tutorial/copy-config.png" alt-text="Fragment z okna programu Visual Studio przedstawiający okienko Eksplorator rozwiązań z wyróżnionym elementem ServiceConfig. JSON oraz okienkiem właściwości z właściwością "Kopiuj do katalogu wyjściowego" na wartość "Kopiuj, jeśli nowszy"" border="false":::

Kontynuuj otwieranie projektu _**AdtE2ESample**_ w programie Visual Studio, aby nadal korzystać z niego w samouczku.

