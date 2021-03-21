---
title: 'ML Studio (klasyczny): Migruj do skryptu Azure Machine Learning-Execute R'
description: Kompiluj ponownie Studio (klasyczne) wykonywanie modułów skryptu języka R do uruchomienia na Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: ac9ad296029451d624345d8b3bb365d881ba9a84
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565031"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>Migrowanie wykonywania modułów skryptu języka R w programie Studio (wersja klasyczna)

W tym artykule dowiesz się, jak ponownie skompilować moduł **skryptu języka R** programu Studio (klasyczny) w Azure Machine Learning.

Aby uzyskać więcej informacji na temat migrowania z programu Studio (klasyczny), zobacz [artykuł Omówienie migracji](migrate-overview.md).

## <a name="execute-r-script"></a>Wykonywanie skryptu języka R

Azure Machine Learning Designer działa teraz w systemie Linux. Studio (klasyczny) działa w systemie Windows. Ze względu na zmianę platformy należy dostosować **wykonywanie skryptu języka R** podczas migracji, w przeciwnym razie potok zakończy się niepowodzeniem.

Aby przeprowadzić migrację modułu **wykonywania skryptu języka R** z programu Studio (klasycznego), należy zamienić `maml.mapInputPort` `maml.mapOutputPort` interfejsy i z funkcją Standard.

Poniższa tabela zawiera podsumowanie zmian w module skryptu języka R:

|Cecha|Studio (klasyczna)|Projektant usługi Azure Machine Learning|
|---|---|---|
|Interfejs skryptu|`maml.mapInputPort` i `maml.mapOutputPort`|Interfejs funkcji|
|Platforma|Windows|Linux|
|Dostęp do Internetu |Nie|Tak|
|Pamięć|14 GB|Zależne od jednostki SKU obliczeń|

### <a name="how-to-update-the-r-script-interface"></a>Jak zaktualizować interfejs skryptu języka R

Poniżej przedstawiono zawartość przykładowego wykonania modułu **skryptu języka R** w programie Studio (klasyczny):
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

Poniżej przedstawiono zaktualizowaną zawartość projektanta. Zwróć uwagę, że `maml.mapInputPort` i zostały `maml.mapOutputPort` zastąpione interfejsem funkcji standardowej `azureml_main` . 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
Aby uzyskać więcej informacji, zobacz [Informacje o module Execute skryptu języka R](../algorithm-module-reference/execute-r-script.md)dla projektanta.

### <a name="install-r-packages-from-the-internet"></a>Instalowanie pakietów języka R z Internetu

Program Azure Machine Learning Designer umożliwia instalowanie pakietów bezpośrednio z CRAN.

Jest to udoskonalenie w porównaniu z Studio (klasyczne). Ponieważ Studio (klasyczne) działa w środowisku piaskownicy bez dostępu do Internetu, należy przekazać skrypty w pakiecie zip w celu zainstalowania większej liczby pakietów. 

Użyj poniższego kodu, aby zainstalować pakiety CRAN w module **wykonywania skryptu języka R** projektanta:
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób migrowania wykonywania modułów skryptów języka R do Azure Machine Learning.

Zapoznaj się z innymi artykułami z serii migracji programu Studio (klasycznej):

1. [Omówienie migracji](migrate-overview.md).
1. [Migrowanie zestawu danych](migrate-register-dataset.md).
1. [Kompiluj potok szkoleniowy Studio (klasyczny)](migrate-rebuild-experiment.md).
1. [Kompiluj ponownie usługę sieci Web programu Studio (klasyczna)](migrate-rebuild-web-service.md).
1. [Zintegruj usługę sieci web Azure Machine Learning z aplikacjami klienckimi](migrate-rebuild-integrate-with-client-app.md).
1. **Migruj moduły Execute skryptu języka R**.