---
title: Wykonywanie zapytań dotyczących danych Avro przy użyciu Azure Data Lake Analytics | Microsoft Docs
description: Użyj właściwości treści wiadomości, aby skierować dane telemetryczne urządzenia do magazynu obiektów blob i wysyłać zapytania do danych formatu Avro, które są zapisywane w usłudze BLOB Storage.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 3cfe75edcf338f5248baf396147a5b77803fbfb3
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655938"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Wykonywanie zapytań dotyczących danych Avro przy użyciu Azure Data Lake Analytics

W tym artykule omówiono sposób wykonywania zapytań dotyczących danych Avro w celu wydajnej trasy komunikatów z usługi Azure IoT Hub do usług platformy Azure. Funkcja [routingu komunikatów](iot-hub-devguide-messages-d2c.md) umożliwia filtrowanie danych przy użyciu zaawansowanych zapytań na podstawie właściwości wiadomości, treści wiadomości, znaczników sznurów urządzeń i właściwości z sznurów urządzenia. Aby dowiedzieć się więcej na temat możliwości wykonywania zapytań w obszarze Routing wiadomości, zobacz artykuł dotyczący [składni zapytania dotyczącego routingu komunikatów](iot-hub-devguide-routing-query-syntax.md).

Wyzwanie zostało spowodowane tym, że usługa Azure IoT Hub kieruje komunikaty do usługi Azure Blob Storage, domyślnie IoT Hub zapisuje zawartość w formacie Avro, który ma Właściwość Body komunikatu i Właściwość Message. Format Avro nie jest używany dla żadnych innych punktów końcowych. Chociaż format Avro jest doskonały w przypadku przechowywania danych i komunikatów, jest wyzwaniem do korzystania z niego do wykonywania zapytań dotyczących danych. W porównaniu, format JSON lub CSV jest znacznie łatwiejszy do wykonywania zapytań dotyczących danych. IoT Hub teraz obsługuje zapisywanie danych do magazynu obiektów BLOB w formacie JSON oraz AVRO.

Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Azure Storage jako punktu końcowego routingu](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).

Aby rozwiązać nierelacyjne potrzeby dużych ilości danych i formaty i przezwyciężyć to wyzwanie, można użyć wielu wzorców danych Big Data do przekształcania i skalowania danych. Jeden z wzorców "płatność za zapytanie" jest Azure Data Lake Analytics, który jest fokusem tego artykułu. Mimo że można łatwo wykonać zapytanie w usłudze Hadoop lub innych rozwiązaniach, Data Lake Analytics jest często lepiej dopasowane do podejścia "płatność za zapytanie".

Istnieje "Ekstraktor" dla Avro w języku U-SQL. Aby uzyskać więcej informacji, zobacz [przykład U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Wykonywanie zapytań i eksportowanie danych Avro do pliku CSV

Ta sekcja zawiera kwerendy dotyczące Avro danych i eksportowania ich do pliku CSV w usłudze Azure Blob Storage, chociaż można łatwo umieścić dane w innych repozytoriach lub magazynach danych.

1. Skonfiguruj IoT Hub platformy Azure, aby kierować dane do punktu końcowego usługi Azure Blob Storage za pomocą właściwości w treści komunikatu w celu wybrania komunikatów.

   ![Sekcja "niestandardowe punkty końcowe"](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Reguły routingu](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Aby uzyskać więcej informacji na temat ustawień tras i niestandardowych punktów końcowych, zobacz [Routing komunikatów dla Centrum IoT](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Upewnij się, że urządzenie ma kodowanie, typ zawartości i dane niepotrzebne we właściwościach lub treści wiadomości, jak określono w dokumentacji produktu. Po wyświetleniu tych atrybutów w Device Explorer, jak pokazano poniżej, można sprawdzić, czy zostały one ustawione prawidłowo.

   ![Okienko dane centrum zdarzeń](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Skonfiguruj wystąpienie Azure Data Lake Store i wystąpienie Data Lake Analytics. Usługa Azure IoT Hub nie kieruje do wystąpienia Data Lake Store, ale wystąpienie Data Lake Analytics wymaga jednego z nich.

   ![Wystąpienia Data Lake Store i Data Lake Analytics](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. W Data Lake Analytics Skonfiguruj magazyn obiektów blob platformy Azure jako dodatkowy magazyn, który jest tym samym magazynem obiektów blob, do którego usługa Azure IoT Hub kieruje dane.

   ![Okienko "źródła danych"](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Zgodnie z opisem w [przykładzie U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)potrzebne są cztery pliki dll. Przekaż te pliki do lokalizacji w wystąpieniu Data Lake Store.

   ![Cztery przekazane pliki DLL](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. W programie Visual Studio Utwórz projekt U-SQL.

   ![Tworzenie projektu U-SQL](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Wklej zawartość następującego skryptu do nowo utworzonego pliku. Zmodyfikuj trzy wyróżnione sekcje: konto Data Lake Analytics, skojarzone ścieżki pliku DLL i poprawna ścieżka do konta magazynu.

   ![Trzy sekcje do zmodyfikowania](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   Rzeczywisty skrypt U-SQL dla prostych danych wyjściowych do pliku CSV:

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Uruchomienie następującego skryptu zajęło Data Lake Analytics pięć minut, który został ograniczony do 10 jednostek analitycznych i przetworzył 177 plików. Wynik jest wyświetlany w danych wyjściowych pliku CSV, który jest wyświetlany na poniższym obrazie:

    ![Wyniki wyjściowe do pliku CSV](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Dane wyjściowe konwertowane do pliku CSV](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Aby przeanalizować kod JSON, przejdź do kroku 8.

8. Większość wiadomości IoT jest w formacie pliku JSON. Dodając następujące wiersze, można przeanalizować komunikat do pliku JSON, który umożliwia dodanie klauzul WHERE i wyprowadza tylko potrzebne dane.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                message["event"] AS msgevent,
                message["object"] AS msgobject,
                message["status"] AS msgstatus,
                message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    W danych wyjściowych zostanie wyświetlona kolumna dla każdego elementu w `SELECT` poleceniu.

    ![Dane wyjściowe pokazujące kolumnę dla każdego elementu](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia zapytań dotyczących danych Avro w celu wydajnej trasy komunikatów z usługi Azure IoT Hub do usług platformy Azure.

Aby zapoznać się z przykładami kompletnych rozwiązań, które używają IoT Hub, zobacz [dokumentację akceleratorów rozwiązań usługi Azure IoT](../iot-accelerators/index.yml).

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą IoT Hub, zobacz [przewodnik dewelopera IoT Hub](iot-hub-devguide.md).

Aby dowiedzieć się więcej na temat routingu wiadomości w IoT Hub, zobacz [wysyłanie i odbieranie komunikatów przy użyciu IoT Hub](iot-hub-devguide-messaging.md).
