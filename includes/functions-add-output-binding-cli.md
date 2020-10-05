---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673344"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Dodawanie definicji powiązania danych wyjściowych do funkcji

Chociaż funkcja może mieć tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych, które umożliwiają łączenie się z innymi usługami i zasobami platformy Azure bez konieczności pisania niestandardowego kodu integracji. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Te powiązania należy zadeklarować w *function.js* pliku w folderze funkcji. Z poprzedniego przewodnika Szybki Start *function.js* w pliku w folderze *HttpExample* zawiera dwa powiązania w `bindings` kolekcji:  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Każde powiązanie ma co najmniej typ, kierunek i nazwę. W powyższym przykładzie pierwsze powiązanie jest typu `httpTrigger` z kierunkiem `in` . Dla `in` kierunku `name` określa nazwę parametru wejściowego, który jest wysyłany do funkcji po wywołaniu przez wyzwalacz.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Drugie powiązanie w kolekcji ma nazwę `res` . To `http` powiązanie jest powiązaniem wyjściowym ( `out` ), które jest używane do zapisywania odpowiedzi HTTP. 

Aby zapisać do kolejki usługi Azure Storage przy użyciu tej funkcji, Dodaj `out` powiązanie typu `queue` z nazwą `msg` , jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Drugie powiązanie w kolekcji jest typu `http` z kierunkiem `out` , a w takim przypadku specjalna `name` `$return` wartość wskazuje, że to powiązanie używa wartości zwracanej funkcji zamiast dostarczać parametr wejściowy.

Aby zapisać do kolejki usługi Azure Storage przy użyciu tej funkcji, Dodaj `out` powiązanie typu `queue` z nazwą `msg` , jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Drugie powiązanie w kolekcji ma nazwę `res` . To `http` powiązanie jest powiązaniem wyjściowym ( `out` ), które jest używane do zapisywania odpowiedzi HTTP. 

Aby zapisać do kolejki usługi Azure Storage przy użyciu tej funkcji, Dodaj `out` powiązanie typu `queue` z nazwą `msg` , jak pokazano w poniższym kodzie:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
W tym przypadku `msg` jest przyznany do funkcji jako argument wyjściowy. Dla `queue` typu należy również określić nazwę kolejki w `queueName` i podać *nazwę* połączenia usługi Azure Storage (z *local.settings.json*) w temacie `connection` . 
::: zone-end  
