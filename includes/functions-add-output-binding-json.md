---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "78191064"
---
Atrybuty powiązań są definiowane bezpośrednio w function.jspliku. W zależności od typu powiązania mogą być wymagane dodatkowe właściwości. [Konfiguracja wyjściowa kolejki](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) opisuje pola wymagane dla powiązania kolejki usługi Azure Storage. Rozszerzenie ułatwia dodawanie powiązań do function.jspliku. 

Aby utworzyć powiązanie, kliknij prawym przyciskiem myszy (Ctrl + kliknięcie na macOS) `function.json` plik w folderze HttpTrigger i wybierz polecenie **Dodaj powiązanie...**. Postępuj zgodnie z monitami, aby zdefiniować następujące właściwości powiązań dla nowego powiązania:

| Monit | Wartość | Opis |
| -------- | ----- | ----------- |
| **Wybierz kierunek powiązania** | `out` | Powiązanie jest powiązaniem wyjściowym. |
| **Wybierz powiązanie z kierunkiem...** | `Azure Queue Storage` | Powiązanie to powiązanie kolejki usługi Azure Storage. |
| **Nazwa używana do identyfikowania tego powiązania w kodzie** | `msg` | Nazwa, która identyfikuje parametr powiązania przywoływany w kodzie. |
| **Kolejka, do której zostanie wysłany komunikat** | `outqueue` | Nazwa kolejki, w której ma zostać zapisywany powiązania. Gdy *Kolejka* nie istnieje, powiązanie tworzy je przy pierwszym użyciu. |
| **Wybierz ustawienie z "local.setting.json"** | `AzureWebJobsStorage` | Nazwa ustawienia aplikacji, które zawiera parametry połączenia dla konta magazynu. `AzureWebJobsStorage`Ustawienie zawiera parametry połączenia dla konta magazynu utworzonego za pomocą aplikacji funkcji. |

Powiązanie jest dodawane do `bindings` tablicy w function.jsna, który powinien wyglądać następująco:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::