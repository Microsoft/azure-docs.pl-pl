---
title: Wdrażanie modelu ML w usłudze Azure SQL Edge przy użyciu ONNX
description: W trzeciej części tego przykładowego samouczka usługi Azure SQL Edge na potrzeby przewidywania zanieczyszczeń żelaza można uruchamiać modele uczenia maszynowego ONNX w usłudze SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93422199"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Wdrażanie modelu ML w usłudze Azure SQL Edge przy użyciu ONNX 

W trzeciej części tego samouczka z trzema częściami w celu przewidywania zanieczyszczeń żelaza w usłudze Azure SQL Edge:

1. Użyj Azure Data Studio, aby nawiązać połączenie z SQL Database w wystąpieniu usługi Azure SQL Edge.
2. Przewidywanie zanieczyszczeń żelaza z ONNX w usłudze Azure SQL Edge.

## <a name="key-components"></a>Najważniejsze składniki

1. Rozwiązanie używa domyślnie 500 milisekund między każdą wiadomością wysłaną do centrum brzegowego. Można to zmienić w pliku **program. cs** 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. Rozwiązanie wygenerowało komunikat z następującymi atrybutami. Dodaj lub usuń atrybuty zgodnie z wymaganiami. 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Łączenie się z SQL Database w wystąpieniu usługi Azure SQL Edge w celu uczenia, wdrożenia i przetestowania modelu ML

1. Otwórz narzędzie Azure Data Studio.

2. Na karcie **Zapraszamy** Rozpocznij nowe połączenie z następującymi szczegółami:

   |_Pole_|_Wartość_|
   |-------|-------|
   |Typ połączenia| Microsoft SQL Server|
   |Serwer|Publiczny adres IP wymieniony na maszynie wirtualnej, który został utworzony dla tej wersji demonstracyjnej|
   |Nazwa użytkownika|sa|
   |Hasło|Silne hasło użyte podczas tworzenia wystąpienia usługi Azure SQL Edge|
   |baza danych|Domyślne|
   |Server group (Grupa serwerów)|Domyślne|
   |Nazwa (opcjonalnie)|Podaj nazwę opcjonalną|

3. Kliknij przycisk **Połącz** .

4. W sekcji **plik** otwórz **/deploymentscripts/MiningProcess_ONNX. jpynb** z folderu, w którym Sklonowano pliki projektu na komputerze.

5. Ustaw jądro na Python 3.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat korzystania z modeli ONNX w usłudze Azure SQL Edge, zobacz temat [Uczenie maszynowe i AI z ONNX w programie SQL Edge](onnx-overview.md).