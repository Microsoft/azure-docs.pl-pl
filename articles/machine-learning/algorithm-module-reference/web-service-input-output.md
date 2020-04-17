---
title: Wejście/wyjście usługi sieci Web
description: Dowiedz się więcej o modułach usługi sieci web w projektancie usługi Azure Machine Learning (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: be940e3ba693270707e22ffc7b9377dbea1df5f8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462089"
---
# <a name="web-service-inputoutput"></a>Wejście/wyjście usługi sieci Web

W tym artykule opisano moduł **wprowadzania usługi sieci Web** i moduł **danych wyjściowych usługi sieci Web** w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Moduł **wprowadzania usługi sieci Web** może łączyć się tylko z portem wejściowym o typie **DataFrameDirectory**. Moduł **wyjścia usługi sieci Web** można podłączyć tylko z portu wyjściowego z **typem DataFrameDirectory**. Dwa moduły można znaleźć w drzewie modułów w kategorii **usługi sieci Web.** 

Moduł **wprowadzania usługi sieci Web** służy do wskazania, gdzie dane użytkownika wprowadza potok i **moduł wyjścia usługi sieci Web** jest używany do wskazania, gdzie dane użytkownika są zwracane w potoku wnioskowania w czasie rzeczywistym.

## <a name="how-to-use-web-service-inputoutput"></a>Jak korzystać z wejścia/wyjścia usługi sieci Web

- Podczas tworzenia potoku wnioskowania w czasie rzeczywistym z potoku szkolenia, **wejście usługi sieci Web** i wyjście usługi sieci **Web** moduł zostanie automatycznie dodany, aby pokazać, gdzie dane użytkownika wprowadza potok i gdzie dane są zwracane. 

    Dowiedz się więcej o [tworzeniu potoku wnioskowania w czasie rzeczywistym](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#create-a-real-time-inference-pipeline).

    > [!NOTE]
    > Automatyczne generowanie potoku wnioskowania w czasie rzeczywistym jest procesem najlepszego wysiłku opartego na regułach, nie ma żadnej gwarancji poprawności. Można ręcznie dodać lub usunąć **moduły wejścia/wyjścia usługi sieci Web,** aby spełnić wymagania. Upewnij się, że istnieje co najmniej jeden moduł **wejściowy usługi sieci Web** i jeden moduł **wyjścia usługi sieci Web** w potoku wnioskowania w czasie rzeczywistym. Jeśli masz wiele modułów **wejścia usługi sieci Web** lub wyjścia usługi sieci **Web,** upewnij się, że mają unikatowe nazwy, które można wprowadzić nazwę w prawym panelu modułu.

- Można również ręcznie utworzyć potok wnioskowania w czasie rzeczywistym, dodając moduły **wejścia usługi sieci Web** i dane **wyjściowe usługi sieci Web** do nieprzesłanego potoku.

    > [!NOTE]
    >  Typ potoku zostanie określony przy pierwszym przesłaniu. Dlatego należy dodać moduł **wprowadzania i** danych **wyjściowych usługi sieci Web** przed przesłaniem po raz pierwszy, jeśli chcesz utworzyć potok wnioskowania w czasie rzeczywistym.

   W poniższym przykładzie pokazano, jak ręcznie utworzyć potok wnioskowania w czasie rzeczywistym z **modułu Wykonywanie skryptu języka Python.** 

   ![Przykład](media/module/web-service-input-output-example.png)
   
   Po przesłaniu potoku i zakończeniu uruchamiania zostanie pomyślnie wdrożony punkt końcowy w czasie rzeczywistym.
   
   > [!NOTE]
   >  W powyższym **przykładzie Wprowadź dane ręcznie** udostępnia schemat danych dla danych wejściowych usługi sieci web i jest niezbędny do wdrażania punktu końcowego w czasie rzeczywistym. Ogólnie rzecz biorąc należy zawsze połączyć moduł lub zestaw danych z portem, który jest połączony **z wejściem usługi sieci Web,** aby zapewnić schemat danych.
   
## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wdrażaniu punktu końcowego w czasie rzeczywistym](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy#deploy-the-real-time-endpoint).
Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning.