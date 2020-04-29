---
title: Wejście/wyjście usługi internetowej
description: Dowiedz się więcej na temat modułów usługi sieci Web w programie Azure Machine Learning Designer (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81462089"
---
# <a name="web-service-inputoutput"></a>Wejście/wyjście usługi internetowej

W tym artykule opisano moduł **danych wejściowych usługi sieci Web** i moduł **danych wyjściowych usługi sieci web** w programie Azure Machine Learning Designer (wersja zapoznawcza).

Moduł **danych wejściowych usługi sieci Web** może łączyć się tylko z portem wejściowym z typem **DataFrameDirectory**. A moduł **danych wyjściowych usługi sieci Web** może być połączony tylko z portu wyjściowego z typem **DataFrameDirectory**. Te dwa moduły można znaleźć w drzewie modułów, w obszarze Kategoria **usługi sieci Web** . 

Moduł **danych wejściowych usługi sieci Web** służy do wskazywania, gdzie dane użytkownika są wprowadzane do potoku, a moduł danych **wyjściowych usługi sieci Web** jest używany do wskazywania, gdzie dane użytkownika są zwracane w potoku wnioskowania w czasie rzeczywistym.

## <a name="how-to-use-web-service-inputoutput"></a>Jak używać danych wejściowych/wyjściowych usługi sieci Web

- Podczas tworzenia potoku do wnioskowania w czasie rzeczywistym z potoku szkoleniowego zostanie automatycznie dodany moduł danych **wejściowych usługi sieci Web** i danych **wyjściowych usługi sieci Web** w celu pokazania miejsca, w którym dane użytkownika są wprowadzane do potoku i miejsca, w którym dane są zwracane. 

    Dowiedz się więcej [na temat tworzenia potoku w czasie rzeczywistym](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > Automatyczne generowanie potoku wnioskowania w czasie rzeczywistym to oparta na regułach procedura optymalna, nie ma gwarancji dla poprawnego działania. Można ręcznie dodawać lub usuwać moduły **danych wejściowych/wyjściowych usługi sieci Web** w celu spełnienia wymagań. Upewnij się, że istnieje co najmniej jeden moduł **danych wejściowych usługi sieci Web** i jeden moduł **wyjściowy usługi sieci Web** w potoku do wnioskowania w czasie rzeczywistym. Jeśli masz wiele modułów **danych wejściowych usługi sieci Web** lub danych **wyjściowych usługi sieci Web** , upewnij się, że mają one unikatowe nazwy, których nazwę możesz wprowadzić w prawym panelu modułu.

- Możesz również ręcznie utworzyć potok wnioskowania w czasie rzeczywistym, dodając do nieprzesłanego potoku moduły **danych wejściowych** i **usług sieci Web** usługi sieci Web.

    > [!NOTE]
    >  Typ potoku zostanie określony podczas pierwszego przesyłania. Dlatego należy dodać moduł danych wejściowych i **usług sieci Web** **usługi sieci** Web przed przesłaniem po raz pierwszy, jeśli chcesz utworzyć potok wnioskowania w czasie rzeczywistym.

   Poniższy przykład pokazuje, jak ręcznie utworzyć potok wnioskowania w czasie rzeczywistym przy użyciu modułu **skryptu języka Python** . 

   ![Przykład](media/module/web-service-input-output-example.png)
   
   Po przesłaniu potoku i zakończeniu przebiegu będzie można wdrożyć punkt końcowy w czasie rzeczywistym.
   
   > [!NOTE]
   >  W powyższym przykładzie **Wprowadź dane ręcznie** udostępnia schemat danych dla danych wejściowych usługi sieci Web i jest niezbędny do wdrożenia punktu końcowego w czasie rzeczywistym. Ogólnie rzecz biorąc, należy połączyć moduł lub zestaw danych z portem, z którym jest połączona **Usługa sieci Web** , aby zapewnić schemat danych.
   
## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wdrażaniu punktu końcowego w czasie rzeczywistym](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.