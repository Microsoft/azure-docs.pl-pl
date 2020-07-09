---
title: dołączanie pliku
description: dołączanie pliku
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75541805"
---
1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com).

1. Uaktualnij obszar roboczy do wersji Enterprise Edition.

    Po uaktualnieniu wszystkie eksperymenty interfejsu wizualizacji zostaną przekonwertowane na wersje robocze potokowe w projektancie.
    
    > [!NOTE]
    > Nie trzeba przeprowadzać uaktualnienia do wersji Enterprise, aby przekonwertować usługi internetowe interfejsu graficznego na punkty końcowe w czasie rzeczywistym.
    
1. Przejdź do sekcji Projektant obszaru roboczego, aby wyświetlić listę wersji roboczych potoku. 
    
    Przekonwertowane usługi sieci Web można znaleźć, przechodząc do **punktów**końcowych w  >  **czasie rzeczywistym**.

1. Wybierz wersję roboczą potoku, aby ją otworzyć.

    Jeśli wystąpił błąd podczas procesu konwersji, zostanie wyświetlony komunikat o błędzie z instrukcjami dotyczącymi rozwiązania problemu. 

### <a name="known-issues"></a>Znane problemy

 Poniżej znajdują się znane problemy dotyczące migracji, które należy rozwiązać ręcznie:

- **Importowanie danych** lub **Eksportowanie modułów danych**
        
    Jeśli w tym eksperymentie masz moduł **Importuj dane** lub **Eksportuj dane** , musisz zaktualizować źródło danych tak, aby korzystało z magazynów danych. Aby dowiedzieć się, jak utworzyć magazyn danych, zapoznaj się z tematem [jak korzystać z usługi Azure Storage](../articles/machine-learning/how-to-access-data.md). Informacje o koncie magazynu w chmurze zostały dodane w komentarzach modułu **Importuj dane** lub **Eksportuj dane** dla wygody użytkownika. 
      