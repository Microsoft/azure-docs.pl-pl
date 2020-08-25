---
title: Konfigurowanie modułów IoT Edge w usłudze Azure SQL Edge
description: W drugiej części tego samouczka dotyczącego usługi Azure SQL Edge na potrzeby przewidywania zanieczyszczeń żelaza można skonfigurować IoT Edge modułów i połączeń.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a4087ef56712e098443009bd0457029394ea7b51
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "84235036"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Konfigurowanie modułów IoT Edge i połączeń

W drugiej części tego samouczka z trzema częściami w celu przewidywania zanieczyszczeń żelaza w usłudze Azure SQL Edge skonfigurujesz następujące moduły IoT Edge:

- Usługa Azure SQL Edge
- Moduł IoT Edge generatora danych

## <a name="create-azure-stream-analytics-module"></a>Utwórz moduł Azure Stream Analytics

Utwórz moduł Azure Stream Analytics, który będzie używany w tym samouczku. Aby dowiedzieć się więcej o korzystaniu z zadań przesyłania strumieniowego w programie SQL Edge, zobacz [Używanie zadań przesyłania strumieniowego w programie SQL Edge](stream-analytics.md).

Po utworzeniu zadania Azure Stream Analytics przy użyciu środowiska hostingu ustawionego jako brzegowe Skonfiguruj dane wejściowe i wyjściowe samouczka.

1. Aby utworzyć **dane wejściowe**, kliknij pozycję **+ Dodaj dane wejściowe strumienia**. Wypełnij sekcję Szczegóły, używając następujących informacji:

   Pole|Wartość
   -----|-----
   Format serializacji zdarzeń|JSON
   Encoding|UTF-8
   Typ kompresji zdarzenia|Brak

2. Aby utworzyć **dane wyjściowe**, kliknij pozycję **+ dodaj** i wybierz SQL Database. Wypełnij sekcję Szczegóły, korzystając z poniższych informacji.

   > [!NOTE]
   > W przypadku wdrażania modułu programu SQL Edge w sekcji **"wdrażanie modułu usługi Azure SQL Edge"** należy określić hasło określone w tej sekcji.

   Pole|Wartość
   -----|-----
   baza danych|IronOreSilicaPrediction
   Nazwa serwera|TCP:., 1433
   Nazwa użytkownika|sa
   Hasło|Określ silne hasło
   Tabela|IronOreMeasurements1

3. Przejdź do sekcji **zapytanie** i skonfiguruj zapytanie w następujący sposób:

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. W obszarze **Konfiguracja**wybierz pozycję **Publikuj**, a następnie wybierz przycisk **Publikuj** . Zapisz identyfikator URI sygnatury dostępu współdzielonego do użycia z modułem SQL Database Edge.

## <a name="specify-container-registry-credentials"></a>Określ poświadczenia rejestru kontenera

Należy określić poświadczenia dla obrazów modułu hostingu rejestrów kontenerów. Można je znaleźć w rejestrze kontenerów, który został utworzony w grupie zasobów. Przejdź do sekcji **klucze dostępu** . Zanotuj następujące pola:

- Nazwa rejestru
- Serwer logowania
- Nazwa użytkownika
- Hasło

Teraz określ poświadczenia kontenera w module IoT Edge.

1. Przejdź do centrum IoT Hub utworzonego w grupie zasobów.

2. W sekcji **IoT Edge** w obszarze **Automatyczne zarządzanie urządzeniami**kliknij pozycję **Identyfikator urządzenia**. W tym samouczku identyfikator jest `IronOrePredictionDevice` .

3. Wybierz sekcję **Ustawianie modułów** .

4. W obszarze **Container Registry poświadczenia**wprowadź następujące wartości:

   _Pole_|_Wartość_
   -------|-------
   Nazwa|Nazwa rejestru
   Adres|Serwer logowania
   Nazwa użytkownika|Nazwa użytkownika
   Hasło|Hasło
  
## <a name="deploy-the-data-generator-module"></a>Wdrażanie modułu generatora danych

1. W sekcji **IoT Edge modułów** kliknij pozycję **+ Dodaj** i wybierz pozycję **IoT Edge module**.

2. Podaj nazwę modułu IoT Edge i identyfikator URI obrazu.
   Identyfikator URI obrazu można znaleźć w rejestrze kontenerów w grupie zasobów. Wybierz sekcję **repozytoria** w obszarze **usługi**. Na potrzeby tego samouczka wybierz repozytorium o nazwie `silicaprediction` . Wybierz odpowiedni tag. Identyfikator URI obrazu będzie miał format:

   *serwer logowania containerregistry* / *Nazwa repozytorium*:*nazwa tagu*

   Na przykład:

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. Kliknij przycisk **Dodaj**.

## <a name="deploy-the-azure-sql-edge-module"></a>Wdrażanie modułu usługi Azure SQL Edge

1. Wdróż moduł usługi Azure SQL Edge, wykonując czynności opisane w temacie [wdrażanie usługi Azure SQL Edge (wersja zapoznawcza)](https://docs.microsoft.com/azure/azure-sql-edge/deploy-portal).

2. Na stronie **Określanie trasy** dla **Ustawienia moduły** Określ trasy dla modułu, aby IoT Edge komunikację z centrum w następujący sposób. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   Na przykład:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. Upewnij się, że w ustawieniach **przędzy modułu** są aktualizowane parametry sqlpackage i ASAJonInfo z odpowiednimi adresami URL sygnatury dostępu współdzielonego zapisanymi wcześniej w samouczku.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>Następne kroki

- [Wdrażanie modelu ML w usłudze Azure SQL Edge przy użyciu ONNX](tutorial-run-ml-model-on-sql-edge.md)
