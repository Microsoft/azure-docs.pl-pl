---
title: 'Dane wejściowe/wyjściowe usługi sieci Web: odwołanie do modułu'
description: Informacje o modułach usługi sieci Web w programie Azure Machine Learning Designer
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: a62f8aee0bd0a0d2b7009a48e9d5f00ea3c5155f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90883223"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Moduły danych wejściowych i usług sieci Web usługi sieci Web

W tym artykule opisano moduły danych wejściowych i usług sieci Web usługi sieci Web w programie Azure Machine Learning Designer.

Moduł danych wejściowych usługi sieci Web może łączyć się tylko z portem wejściowym z typem **DataFrameDirectory**. Moduł danych wyjściowych usługi sieci Web może być połączony tylko z portu wyjściowego z typem **DataFrameDirectory**. Dwa moduły można znaleźć w drzewie modułów w kategorii **Usługa sieci Web** . 

Moduł danych wejściowych usługi sieci Web wskazuje, gdzie dane użytkownika są wprowadzane do potoku. Moduł danych wyjściowych usługi sieci Web wskazuje, gdzie dane użytkownika są zwracane w potoku w czasie rzeczywistym.

## <a name="how-to-use-web-service-input-and-output"></a>Jak używać danych wejściowych i wyjściowych usługi sieci Web

Gdy tworzysz [potok wnioskowania](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline) w czasie rzeczywistym z potoku szkoleniowego, moduły danych wejściowych i usług sieci Web usługi sieci Web zostaną automatycznie dodane w celu pokazania miejsca, w którym dane użytkownika są wprowadzane do potoku i gdzie są zwracane dane. 

> [!NOTE]
> Automatyczne generowanie potoku w czasie rzeczywistym to oparty na regułach, optymalny proces. Nie istnieje gwarancja poprawności. 

Możesz ręcznie dodać lub usunąć moduły danych wejściowych i usług sieci Web usług internetowych, aby spełniały określone wymagania. Upewnij się, że potok wnioskowania w czasie rzeczywistym ma co najmniej jeden moduł danych wejściowych usługi sieci Web i jeden moduł wyjściowy usługi sieci Web. Jeśli masz wiele modułów danych wejściowych usługi sieci Web lub danych wyjściowych usługi sieci Web, upewnij się, że mają one unikatowe nazwy. Możesz wprowadzić nazwę w prawym panelu modułu.

Możesz również ręcznie utworzyć potok wnioskowania w czasie rzeczywistym, dodając do nieprzesłanego potoku moduły danych wejściowych i usług sieci Web usługi sieci Web.

> [!NOTE]
> Typ potoku zostanie określony podczas pierwszego przesyłania. Pamiętaj, aby dodać moduły danych wejściowych i usług sieci Web usługi sieci Web przed przesłaniem po raz pierwszy.

Poniższy przykład pokazuje, jak ręcznie utworzyć potok wnioskowania w czasie rzeczywistym na podstawie modułu skryptu wykonywania języka Python. 

![Przykład](media/module/web-service-input-output-example.png)
   
Po przesłaniu potoku i zakończeniu przebiegu można wdrożyć punkt końcowy w czasie rzeczywistym.
   
> [!NOTE]
>  W powyższym przykładzie **Wprowadź dane ręcznie** udostępnia schemat danych dla danych wejściowych usługi sieci Web i jest niezbędny do wdrożenia punktu końcowego w czasie rzeczywistym. Ogólnie rzecz biorąc, należy podłączyć moduł lub zestaw danych do portu, w którym jest połączona **Usługa sieci Web** , aby zapewnić schemat danych.
   
## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wdrażaniu punktu końcowego w czasie rzeczywistym](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.