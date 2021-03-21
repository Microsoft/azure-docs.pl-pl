---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493953"
---
Visual Studio Code pozwala dodawać powiązania do function.jspliku przy użyciu dogodnego zestawu kolejnych wierszy. 

Aby dodać powiązanie, Otwórz paletę poleceń (F1) i wpisz **Azure Functions: Dodaj powiązanie...**, wybierz funkcję dla nowego powiązania, a następnie postępuj zgodnie z monitami, które różnią się w zależności od typu powiązania dodawanego do funkcji. 

Poniżej przedstawiono przykładowe monity o zdefiniowanie nowego powiązania danych wyjściowych magazynu:

| Monit | Wartość | Opis |
| -------- | ----- | ----------- |
| **Wybierz kierunek powiązania** | `out` | Powiązanie jest powiązaniem wyjściowym. |
| **Wybieranie powiązania z kierunkiem** | `Azure Queue Storage` | Powiązanie to powiązanie kolejki usługi Azure Storage. |
| **Nazwa używana do identyfikowania tego powiązania w kodzie** | `msg` | Nazwa, która identyfikuje parametr powiązania przywoływany w kodzie. |
| **Kolejka, do której zostanie wysłany komunikat** | `outqueue` | Nazwa kolejki, w której ma zostać zapisywany powiązania. Gdy *Kolejka* nie istnieje, powiązanie tworzy je przy pierwszym użyciu. |
| **Wybierz ustawienie z "local.settings.json"** | `MyStorageConnection` | Nazwa ustawienia aplikacji, które zawiera parametry połączenia dla konta magazynu. `AzureWebJobsStorage`Ustawienie zawiera parametry połączenia dla konta magazynu utworzonego za pomocą aplikacji funkcji. |

Możesz również kliknąć prawym przyciskiem myszy (Ctrl + kliknięcie na macOS) bezpośrednio na **function.jsw** pliku w folderze funkcji, wybierz pozycję **Dodaj powiązanie** i wykonaj te same monity.

W tym przykładzie następujące powiązanie jest dodawane do `bindings` tablicy w function.jsw pliku:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```