---
title: 'ML Studio (klasyczny): Migruj do zestawu danych Azure Machine Learning-Rebuild'
description: Kompiluj ponownie Studio (klasyczne) zestawy danych w projektancie Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 4c04dd5a2b41b3db54b20c9e514767453951cc35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565046"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Migrowanie zestawu danych programu Studio (klasycznego) do Azure Machine Learning

W tym artykule dowiesz się, jak przeprowadzić migrację zestawu danych programu Studio (klasycznego) do Azure Machine Learning. Aby uzyskać więcej informacji na temat migrowania z programu Studio (klasyczny), zobacz [artykuł Omówienie migracji](migrate-overview.md).

Istnieją trzy opcje migracji zestawu danych do Azure Machine Learning. Przeczytaj każdą z sekcji, aby określić, która opcja jest Najlepsza dla danego scenariusza.


|Gdzie są dane? | Opcja migracji  |
|---------|---------|
|W programie Studio (klasyczny)     |  Opcja 1. [Pobierz zestaw danych z programu Studio (klasyczny) i przekaż go do Azure Machine Learning](#download-the-dataset-from-studio-classic).      |
|Magazyn w chmurze     | Opcja 2: [rejestrowanie zestawu danych ze źródła w chmurze](#import-data-from-cloud-sources). <br><br>  Opcja 3: [Użyj modułu Importuj dane, aby pobrać dane ze źródła w chmurze](#import-data-from-cloud-sources).        |

> [!NOTE]
> Azure Machine Learning obsługuje również [przepływy pracy dotyczące kodu](../how-to-create-register-datasets.md) i tworzenia zestawów danych. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Obszar roboczy usługi Azure Machine Learning. [Utwórz obszar roboczy Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Zestaw danych programu Studio (klasyczny) do migracji.


## <a name="download-the-dataset-from-studio-classic"></a>Pobierz zestaw danych z programu Studio (klasyczny)

Najprostszym sposobem migrowania zestawu danych programu Studio (klasycznego) do Azure Machine Learning jest pobranie zestawu danych i zarejestrowanie go w Azure Machine Learning. Spowoduje to utworzenie nowej kopii zestawu danych i przekazanie jej do Azure Machine Learning magazyn danych.

Możesz bezpośrednio pobrać następujące typy zestawów danych (klasycznego).

* Zwykły tekst (. txt)
* Wartości rozdzielane przecinkami (CSV) z nagłówkiem (CSV) lub bez (.nh.csv)
* Wartości rozdzielane tabulatorami (TSV) z nagłówkiem (. tsv) lub bez (. NH. tsv)
* Plik programu Excel
* Plik zip (zip)

Aby bezpośrednio pobrać zestawy danych:
1. Przejdź do obszaru roboczego programu Studio (klasycznego) ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Na pasku nawigacyjnym po lewej stronie wybierz kartę **zestawy danych** .
1. Wybierz zestawy danych, które chcesz pobrać.
1. Na dolnym pasku akcji wybierz pozycję **Pobierz**.

    ![Zrzut ekranu przedstawiający sposób pobierania zestawu danych w programie Studio (klasyczny)](./media/migrate-register-dataset/download-dataset.png)

W przypadku następujących typów danych należy użyć modułu **Konwertuj do woluminu CSV** w celu pobrania zestawów danych.

* SVMLight danych (. SVMLight) 
* Dane w formacie pliku relacji atrybutów (ARFF) (. ARFF) 
* Obiekt R lub plik obszaru roboczego (. RData
* Typ zestawu danych (. Data). Typ zestawu danych to Studio (klasyczny) wewnętrzny typ danych dla danych wyjściowych modułu.

Aby skonwertować zestaw danych do pliku CSV i pobrać wyniki:

1. Przejdź do obszaru roboczego programu Studio (klasycznego) ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Utwórz nowy eksperyment.
1. Przeciągnij i upuść zestaw danych, który chcesz pobrać na kanwę.
1. Dodaj moduł **konwersji do woluminu CSV** .
1. Połącz port wejściowy **Convert to CSV** z portem wyjściowym zestawu danych.
1. Uruchom eksperyment.
1. Kliknij prawym przyciskiem myszy moduł **Konwertuj do woluminu CSV** .
1. Wybierz pozycję Pobierz **zestaw danych wyników**  >  .

    ![Zrzut ekranu przedstawiający sposób konfigurowania potoku konwersji do woluminu CSV](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Przekaż zestaw danych do Azure Machine Learning

Po pobraniu pliku danych można zarejestrować zestaw danych w Azure Machine Learning:

1. Przejdź do Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. W okienku nawigacji po lewej stronie wybierz kartę **zestawy danych** .
1. Wybierz pozycję **Utwórz zestaw danych**  >  **z plików lokalnych**.
    ![Zrzut ekranu przedstawiający kartę zestawy danych i przycisk służący do tworzenia pliku lokalnego](./media/migrate-register-dataset/register-dataset.png)
1. Wprowadź nazwę i opis.
1. W obszarze **Typ zestawu danych** wybierz pozycję **tabelaryczna**.

    > [!NOTE]
    > Możesz również przekazać pliki ZIP jako zestawy danych. Aby przekazać plik ZIP, wybierz pozycję **plik** dla **typu zestawu danych**.

1. **W obszarze magazyn danych i wybór plików** wybierz magazyn, do którego chcesz przekazać plik DataSet.

    Domyślnie Azure Machine Learning przechowuje zestaw danych w domyślnym obszarze roboczym elementu BlobStore. Aby uzyskać więcej informacji na temat magazynów danych, zobacz [nawiązywanie połączenia z usługami magazynu](../how-to-access-data.md).

1. Ustaw ustawienia i schemat dla zestawu danych. Następnie potwierdź ustawienia.

## <a name="import-data-from-cloud-sources"></a>Importuj dane ze źródeł w chmurze

Jeśli Twoje dane są już w usłudze magazynu w chmurze, a dane mają być przechowywane w lokalizacji natywnej. Można użyć jednej z następujących opcji:

|Metoda pozyskiwania|Opis|
|---| --- |
|Rejestrowanie zestawu danych Azure Machine Learning|Pozyskiwanie danych ze źródeł danych lokalnych i online (BLOB, ADLS Gen1, ADLS Gen2, udział plików, baza danych SQL). <br><br>Tworzy odwołanie do źródła danych, które jest opóźnieniem oceniane w czasie wykonywania. Użyj tej opcji, jeśli chcesz wielokrotnie uzyskać dostęp do tego zestawu danych i chcesz włączyć zaawansowane funkcje danych, takie jak przechowywanie wersji i monitorowanie danych.
|Importuj moduł danych|Pozyskiwanie danych ze źródeł danych online (BLOB, ADLS Gen1, ADLS Gen2, udział plików, baza danych SQL). <br><br> Zestaw danych jest zaimportowany tylko do bieżącego uruchomienia potoku projektanta.


>[!Note]
> Studio (klasyczny) użytkownicy powinni zauważyć, że następujące źródła w chmurze nie są natywnie obsługiwane w Azure Machine Learning:
> - Zapytanie programu Hive
> - Tabela platformy Azure
> - Azure Cosmos DB
> - SQL Database lokalne
>
> Zalecamy, aby użytkownicy przeprowadzili migrację swoich danych do obsługiwanych usług magazynu przy użyciu Azure Data Factory.  

### <a name="register-an-azure-machine-learning-dataset"></a>Rejestrowanie zestawu danych Azure Machine Learning

Wykonaj następujące kroki, aby zarejestrować zestaw danych w celu Azure Machine Learning z usługi w chmurze: 

1. [Utwórz magazyn](../how-to-connect-data-ui.md#create-datastores)danych, który łączy usługę magazynu w chmurze z obszarem roboczym Azure Machine Learning. 

1. [Zarejestruj zestaw danych](../how-to-connect-data-ui.md#create-datasets). W przypadku migrowania zestawu danych programu Studio (klasycznego) wybierz ustawienie **Tabelaryczny** zestaw danych.

Po zarejestrowaniu zestawu danych w Azure Machine Learning można go używać w projektancie:
 
1. Utwórz nową wersję roboczą potoku projektanta.
1. W palecie modułów po lewej stronie rozwiń sekcję **zestawy danych** .
1. Przeciągnij zarejestrowany zestaw danych na kanwę. 

### <a name="use-the-import-data-module"></a>Korzystanie z modułu Importuj dane

Wykonaj następujące kroki, aby zaimportować dane bezpośrednio do potoku projektanta:

1. [Utwórz magazyn](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores)danych, który łączy usługę magazynu w chmurze z obszarem roboczym Azure Machine Learning. 

Po utworzeniu magazynu danych można użyć modułu [**Importuj dane**](../algorithm-module-reference/import-data.md) w projektancie, aby pozyskać z niego dane:

1. Utwórz nową wersję roboczą potoku projektanta.
1. W palecie modułów po lewej stronie Znajdź moduł **Importuj dane** i przeciągnij go na kanwę.
1. Wybierz moduł **Importuj dane** i Użyj ustawień w prawym panelu, aby skonfigurować źródło danych.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób migrowania zestawu danych programu Studio (klasycznego) do Azure Machine Learning. Następnym krokiem jest [odbudowanie potoku szkolenia Studio (klasycznego)](migrate-rebuild-experiment.md).


Zapoznaj się z innymi artykułami z serii migracji programu Studio (klasycznej):

1. [Omówienie migracji](migrate-overview.md).
1. **Migrowanie zestawów danych**.
1. [Kompiluj potok szkoleniowy Studio (klasyczny)](migrate-rebuild-experiment.md).
1. [Kompiluj ponownie usługę sieci Web programu Studio (klasyczna)](migrate-rebuild-web-service.md).
1. [Zintegruj usługę sieci web Azure Machine Learning z aplikacjami klienckimi](migrate-rebuild-integrate-with-client-app.md).
1. [Migruj skrypt wykonania skryptu języka R](migrate-execute-r-script.md).
