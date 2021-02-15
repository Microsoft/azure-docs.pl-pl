---
title: 'Szybki Start: analizowanie danych w Azure Data Lake Storage Gen2 przy użyciu Azure Databricks | Microsoft Docs'
description: Dowiedz się, w jaki sposób uruchomić zadanie platformy Spark w usłudze Azure Databricks, korzystając z witryny Azure Portal oraz konta magazynu usługi Azure Data Lake Storage Gen2.
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 06/12/2020
ms.reviewer: jeking
ms.openlocfilehash: 0d712dc0ebe91ea8815adf235e02b8945e0dea84
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518865"
---
# <a name="quickstart-analyze-data-with-databricks"></a>Szybki Start: analizowanie danych z użyciem datakostki

W tym przewodniku szybki start uruchomisz zadanie Apache Spark przy użyciu Azure Databricks, aby przeprowadzić analizę danych przechowywanych na koncie magazynu. W ramach zadania Spark przeanalizujesz dane subskrypcji kanałów radiowych, aby na podstawie danych demograficznych uzyskać szczegółowe informacje dotyczące korzystania z usług płatnych/bezpłatnych.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Konto magazynu, dla którego jest włączona funkcja hierarchicznej przestrzeni nazw. Aby go utworzyć, zobacz [Tworzenie konta magazynu, które ma być używane z Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

* Identyfikator dzierżawy, identyfikator aplikacji i hasło jednostki usługi platformy Azure z przypisaną rolą **współautor danych obiektu blob magazynu**. [Utwórz nazwę główną usługi](../../active-directory/develop/howto-create-service-principal-portal.md).

  > [!IMPORTANT]
  > Przypisz rolę w zakresie konta magazynu Data Lake Storage Gen2. Możesz przypisać rolę do nadrzędnej grupy zasobów lub subskrypcji, ale będzie zgłaszany błąd dotyczący uprawnień do momentu rozpropagowania przypisań roli do konta magazynu.

## <a name="create-an-azure-databricks-workspace"></a>Tworzenie obszaru roboczego usługi Azure Databricks

W tej sekcji utworzysz obszar roboczy usługi Azure Databricks przy użyciu witryny Azure Portal.

1. W Azure Portal wybierz pozycję **Utwórz**  >    >  **Azure Databricks** analizy zasobów.

    ![Datakostki na Azure Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Datakostki na Azure Portal")

2. W obszarze **Usługa Azure Databricks** podaj wartości umożliwiające utworzenie obszaru roboczego usługi Databricks.

    ![Tworzenie obszaru roboczego usługi Azure Databricks](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Tworzenie obszaru roboczego usługi Azure Databricks")

    Podaj następujące wartości:

    |Właściwość  |Opis  |
    |---------|---------|
    |**Nazwa obszaru roboczego**     | Podaj nazwę obszaru roboczego usługi Databricks.        |
    |**Subskrypcja**     | Z listy rozwijanej wybierz subskrypcję platformy Azure.        |
    |**Grupa zasobów**     | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. Grupa zasobów to kontener zawierający powiązane zasoby dla rozwiązania platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md). |
    |**Lokalizacja**     | Wybierz pozycję **Zachodnie stany USA 2**. Jeśli wolisz, możesz wybrać inny region publiczny.        |
    |**Warstwa cenowa**     |  Wybierz warstwę **Standardowa** lub **Premium**. Aby uzyskać więcej informacji o tych warstwach, zobacz [stronę usługi Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Tworzenie konta potrwa kilka minut. Stan operacji można monitorować za pomocą paska postępu znajdującego się u góry.

4. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego**, a następnie pozycję **Utwórz**.

## <a name="create-a-spark-cluster-in-databricks"></a>Tworzenie klastra Spark w usłudze Databricks

1. W witrynie Azure Portal przejdź do utworzonego obszaru roboczego usługi Databricks, a następnie wybierz pozycję **Uruchom obszar roboczy**.

2. Nastąpi przekierowanie do portalu usługi Azure Databricks. W portalu wybierz pozycję **Nowy**  >  **klaster**.

    ![Datakostki na platformie Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Datakostki na platformie Azure")

3. Na stronie **Nowy klaster** podaj wartości, aby utworzyć klaster.

    ![Tworzenie klastra usługi datakosteks Spark na platformie Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Tworzenie klastra usługi datakosteks Spark na platformie Azure")

    Uzupełnij wartości następujących pól i zaakceptuj wartości domyślne w pozostałych polach:

    - Wprowadź nazwę klastra.
     
    - Upewnij się, że pole wyboru **Zakończ po 120 min aktywności** zostało zaznaczone. Podaj czas (w minutach), po jakim działanie klastra ma zostać zakończone, jeśli nie jest używany.

4. Wybierz pozycję **Utwórz klaster**. Po uruchomieniu klastra możesz dołączyć do niego notesy i uruchamiać zadania Spark.

Aby uzyskać więcej informacji na temat tworzenia klastrów, zobacz [Create a Spark cluster in Azure Databricks](/azure/databricks/clusters/create) (Tworzenie klastra Spark w usłudze Azure Databricks).

## <a name="create-notebook"></a>Utwórz Notes

W tej sekcji utworzysz notes w obszarze roboczym usługi Azure Databricks, a następnie uruchomisz fragmenty kodu, aby skonfigurować konto magazynu.

1. W witrynie [Azure Portal](https://portal.azure.com) przejdź do utworzonego obszaru roboczego usługi Azure Databricks, a następnie wybierz pozycję **Uruchom obszar roboczy**.

2. W lewym okienku wybierz pozycję **Obszar roboczy**. Z listy rozwijanej **Obszar roboczy** wybierz pozycję **Utwórz** > **Notes**.

    ![Zrzut ekranu pokazujący sposób tworzenia notesu w kostkach i podświetl opcję menu Utwórz > Notes.](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Tworzenie notesu w kostkach")

3. W oknie dialogowym **Tworzenie notesu** wprowadź nazwę notesu. Jako język wybierz pozycję **Scala**, a następnie wybierz utworzony wcześniej klaster Spark.

    ![Tworzenie notesu w kostkach](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Tworzenie notesu w kostkach")

    Wybierz przycisk **Utwórz**.

4. Skopiuj i wklej następujący blok kodu do pierwszej komórki, ale jeszcze nie uruchamiaj kodu.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```
5. W tym bloku kodu zamień symbole zastępcze `storage-account-name`, `appID`, `password` i `tenant-id` na wartości zebrane podczas tworzenia jednostki usługi. Ustaw `container-name` wartość symbolu zastępczego na dowolną nazwę, którą chcesz nadać kontenerowi.

6. Naciśnij klawisze **SHIFT+ENTER**, aby uruchomić kod w tym bloku.

## <a name="ingest-sample-data"></a>Pozyskiwanie przykładowych danych

Przed przystąpieniem do pracy z tą sekcją musisz zapewnić spełnienie następujących wymagań wstępnych:

Wprowadź następujący kod w komórce notesu:

```bash
%sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json
```

W tej komórce naciśnij klawisze **SHIFT + ENTER**, aby uruchomić kod.

Teraz w nowej komórce pod tą komórką wprowadź następujący kod, zastępując wartości w nawiasach tymi samymi wartościami, których użyto wcześniej:

```python
dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")
```

W tej komórce naciśnij klawisze **SHIFT + ENTER**, aby uruchomić kod.

## <a name="run-a-spark-sql-job"></a>Uruchamianie zadania Spark SQL

Aby uruchomić zadanie Spark SQL w danych, wykonaj poniższe zadania.

1. Uruchom instrukcję SQL, aby utworzyć tabelę tymczasową przy użyciu danych z przykładowego pliku danych JSON, **small_radio_json.json**. W poniższym fragmencie kodu zamień wartości symboli zastępczych na właściwe nazwy kontenera i konta magazynu. Za pomocą utworzonego wcześniej notesu wklej ten fragment w nowej komórce kodu w notesie, a następnie naciśnij klawisze SHIFT+ENTER.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json"
    )
    ```

    Po pomyślnym wykonaniu polecenia wszystkie dane z pliku JSON znajdą się w tabeli w klastrze usługi Databricks.

    Magiczne polecenie języka `%sql` umożliwia uruchamianie kodu SQL z notesu, nawet jeśli notes jest innego typu. Aby uzyskać więcej informacji, zobacz [Mixing languages in a notebook](/azure/databricks/notebooks/notebooks-use#mix-languages) (Łączenie języków w notesie).

2. Przyjrzyjmy się migawce przykładowych danych JSON, aby lepiej zrozumieć uruchamiane zapytanie. Wklej poniższy fragment kodu w komórce kodu i naciśnij klawisze **SHIFT+ENTER**.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. Pojawią się tabelaryczne dane wyjściowe, takie jak pokazano na poniższym zrzucie ekranu (przedstawiono tu tylko niektóre kolumny):

    ![Przykładowe dane JSON](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Przykładowe dane JSON")

    W danych przykładowych jest rejestrowana między innymi płeć odbiorców kanału radiowego (nazwa kolumny **gender** — „płeć”) i informacja o tym, czy subskrypcja jest bezpłatna, czy płatna (nazwa kolumny **level** — „poziom”).

4. Teraz utworzysz reprezentację wizualną tych danych w celu pokazania, ilu użytkowników każdej płci ma konta bezpłatne, a ilu opłaca subskrypcję. U dołu tabelarycznych danych wyjściowych kliknij ikonę **Wykres słupkowy**, a następnie pozycję **Opcje wykresu**.

    ![Utwórz wykres słupkowy](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Utwórz wykres słupkowy")

5. W obszarze **Dostosowywanie wykresu** przeciągnij i upuść wartości, jak pokazano na zrzucie ekranu.

    ![Zrzut ekranu pokazujący ekran dostosowywanie wykresu i wartości, które można przeciągać i upuszczać.](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Dostosuj wykres słupkowy")

    - W obszarze **Klucze** ustaw wartość **gender** (płeć).
    - W obszarze **Grupowania serii** ustaw wartość **level** (poziom).
    - W obszarze **Wartości** ustaw wartość **level** (poziom).
    - W obszarze **Agregacja** ustaw wartość **LICZBA**.

6. Kliknij pozycję **Zastosuj**.

7. Wynikiem będzie reprezentacja wizualna przedstawiona na zrzucie ekranu:

     ![Dostosuj wykres słupkowy](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Dostosuj wykres słupkowy")

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy skończysz pracować z tym artykułem, możesz zakończyć działanie klastra. W obszarze roboczym usługi Azure Databricks wybierz pozycję **Klastry** i znajdź klaster, którego działanie chcesz zakończyć. Przesuń kursor myszy na wielokropek w kolumnie **Akcje** i wybierz ikonę **Przerwij**.

![Zatrzymaj klaster datakostki](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Zatrzymaj klaster datakostki")

Jeśli klaster nie zostanie ręcznie zakończony automatycznie, pod warunkiem, że podczas tworzenia klastra zostanie zaznaczone pole wyboru **Zakończ po \_ \_ minutach braku aktywności** . Jeśli ustawisz tę opcję, klaster zatrzyma się po upłynięciu wyznaczonego okresu nieaktywności.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono klaster Spark w usłudze Azure Databricks i uruchomiono zadanie Spark przy użyciu danych na koncie magazynu z włączoną usługą Data Lake Storage Gen2.

Przejdź do następnego artykułu, aby dowiedzieć się, jak wykonać operację ETL (wyodrębnianie, przekształcanie i ładowanie danych) przy użyciu usługi Azure Databricks.

> [!div class="nextstepaction"]
>[Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse).

- Aby dowiedzieć się, jak importować dane z innych źródeł danych do Azure Databricks, zobacz [źródła danych Spark](/azure/databricks/data/data-sources/).

- Aby dowiedzieć się więcej na temat innych sposobów uzyskiwania dostępu do Azure Data Lake Storage Gen2 z poziomu obszaru roboczego Azure Databricks, zobacz [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2).