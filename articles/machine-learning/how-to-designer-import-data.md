---
title: Importowanie danych do projektanta
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak importować dane do projektanta Azure Machine Learning przy użyciu Azure Machine Learning zestawów danych i modułu Importuj dane.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: a2cc0840b7ba4b26cf9f5b1219fc189230870774
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739862"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Importowanie danych do programu Azure Machine Learning Designer

W tym artykule dowiesz się, jak zaimportować własne dane do projektanta w celu utworzenia niestandardowych rozwiązań. Istnieją dwa sposoby importowania danych do projektanta: 

* **Azure Machine Learning zestawy danych** — umożliwia rejestrowanie [zestawów danych](concept-data.md#datasets) w programie Azure Machine Learning, aby umożliwić zaawansowane funkcje, które ułatwiają zarządzanie danymi.
* **Importuj moduł danych** — Użyj modułu [Import danych](algorithm-module-reference/import-data.md) , aby uzyskać bezpośredni dostęp do danych ze źródeł DataSources.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Korzystanie z Azure Machine Learning zestawów danych

Zalecamy użycie [zestawów](concept-data.md#datasets) danych w celu zaimportowania ich do projektanta. Po zarejestrowaniu zestawu danych można w pełni korzystać z zaawansowanych funkcji danych, takich jak [przechowywanie wersji i śledzenie](how-to-version-track-datasets.md) i [monitorowanie danych](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Rejestrowanie zestawu danych

Istnieje możliwość rejestrowania istniejących zestawów danych [programowo za pomocą zestawu SDK](how-to-create-register-datasets.md#datasets-sdk) lub [wizualizacji w programie Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).

Możesz również zarejestrować dane wyjściowe dla dowolnego modułu projektanta jako zestaw danych.

1. Wybierz moduł, który wyprowadza dane, które chcesz zarejestrować.

1. W okienku właściwości wybierz pozycję dane **wyjściowe + dzienniki**  >  **zarejestruj zestaw danych**.

    ![Zrzut ekranu przedstawiający sposób nawigowania do opcji Zarejestruj zestaw danych](media/how-to-designer-import-data/register-dataset-designer.png)

Jeśli dane wyjściowe modułu są w formacie tabelarycznym, należy wybrać zarejestrowanie danych wyjściowych jako **zestawu danych** lub **tabelarycznego zestawu danych**.

 - **Zestaw danych plików** rejestruje folder wyjściowy modułu jako zestaw danych pliku. Folder wyjściowy zawiera plik danych i meta pliki używane wewnętrznie przez projektanta. Wybierz tę opcję, jeśli chcesz nadal używać zarejestrowanego zestawu danych w projektancie. 

 - **Tabelaryczny zestaw** danych rejestruje tylko plik danych wyjściowych modułu jako tabelaryczny zestaw danych. Ten format jest łatwo używany przez inne narzędzia, na przykład w zautomatyzowanym Machine Learning lub w zestawie Python SDK. Wybierz tę opcję, jeśli planujesz używać zarejestrowanego zestawu danych poza projektantem.  
 

### <a name="use-a-dataset"></a>Korzystanie z zestawu danych

Zarejestrowane zestawy danych można znaleźć w palecie modułów w obszarze **zestawy danych**. Aby użyć zestawu danych, przeciągnij i upuść go na kanwie potoku. Następnie połącz port wyjściowy zestawu danych z innymi modułami na kanwie. 

Jeśli zarejestrujesz plik DataSet, typem portu wyjściowego zestawu danych jest **AnyDirectory**. Jeśli rejestrujesz tabelaryczny zestaw danych, typ portu wyjściowego zestawu danych, jeśli **DataFrameDirectory**. Należy pamiętać, że jeśli port wyjściowy zestawu danych zostanie połączony z innymi modułami w projektancie, typ portu zestawów danych i modułów musi być wyrównany.

![Zrzut ekranu przedstawiający lokalizację zapisanych zestawów danych w palecie projektanta](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> Projektant obsługuje [przechowywanie wersji zestawu danych](how-to-version-track-datasets.md). Określ wersję zestawu danych w panelu Właściwości modułu DataSet.

### <a name="limitations"></a>Ograniczenia 

- Obecnie można wizualizować tylko tabelaryczny zestaw danych w projektancie. Po zarejestrowaniu zestawu danych pliku poza projektantem nie można go wizualizować na kanwie projektanta.
- Zestaw danych jest przechowywany w sieci wirtualnej (VNet). Jeśli chcesz wizualizować, musisz włączyć zarządzanie tożsamościami obszaru roboczego dla magazynu danych.
    1. Przejdź do powiązanego magazynu danych, a następnie kliknij przycisk **Aktualizuj** poświadczenia 
     :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="Aktualizuj"::: poświadczenia
    1. Wybierz pozycję **tak** , aby włączyć tożsamość zarządzaną w obszarze roboczym.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Włącz tożsamość zarządzaną w obszarze roboczym":::

## <a name="import-data-using-the-import-data-module"></a>Importowanie danych przy użyciu modułu Importuj dane

Chociaż zalecamy używanie zestawów danych do importowania danych, można również użyć modułu [Importuj dane](algorithm-module-reference/import-data.md) . Moduł Importuj dane Pomija rejestrowanie zestawu danych w Azure Machine Learning i importuje dane bezpośrednio z [magazynu](concept-data.md#datastores) danych lub adresu URL http.

Aby uzyskać szczegółowe informacje na temat korzystania z modułu Importuj dane, zobacz [stronę Importowanie danych źródłowych](algorithm-module-reference/import-data.md).

> [!NOTE]
> Jeśli zestaw danych zawiera zbyt wiele kolumn, może wystąpić następujący błąd: "Walidacja nie powiodła się z powodu ograniczenia rozmiaru". Aby tego uniknąć, [zarejestruj zestaw danych w interfejsie DataSets](how-to-connect-data-ui.md#create-datasets).

## <a name="supported-sources"></a>Obsługiwane źródła

Ta sekcja zawiera listę źródeł danych obsługiwanych przez projektanta. Dane wchodzą w skład projektanta z magazynu danych lub z [tabelarycznego elementu DataSet](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Źródła danych
Listę obsługiwanych źródeł danych można znaleźć [w temacie Access Data (dostęp do usługi Azure Storage](how-to-access-data.md#supported-data-storage-service-types)).

### <a name="tabular-dataset-sources"></a>Tabelaryczne źródła danych

Projektant obsługuje tabelaryczne zestawy danych utworzone na podstawie następujących źródeł:
 * Rozdzielane pliki
 * Pliki JSON
 * Pliki Parquet
 * Zapytania SQL

## <a name="data-types"></a>Typy danych

Projektant wewnętrznie rozpoznaje następujące typy danych:

* Ciąg
* Liczba całkowita
* Liczba dziesiętna
* Wartość logiczna
* Date

Projektant używa wewnętrznego typu danych do przekazywania danych między modułami. Dane można jawnie przekonwertować na format tabeli danych przy użyciu modułu [Konwertuj na zestaw](algorithm-module-reference/convert-to-dataset.md) danych. Każdy moduł, który akceptuje formaty inne niż format wewnętrzny, przekonwertuje dane w trybie dyskretnym przed przekazaniem go do następnego modułu.

## <a name="data-constraints"></a>Ograniczenia danych

Moduły w projektancie są ograniczone przez rozmiar obiektu docelowego obliczeń. W przypadku większych zestawów danych należy użyć większego Azure Machine Learning zasobów obliczeniowych. Aby uzyskać więcej informacji na temat Azure Machine Learning obliczeń, zobacz [co to są cele obliczeń w Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Dostęp do danych w sieci wirtualnej

Jeśli obszar roboczy znajduje się w sieci wirtualnej, musisz wykonać dodatkowe czynności konfiguracyjne, aby wizualizować dane w projektancie. Aby uzyskać więcej informacji na temat korzystania z magazynów danych i zestawów w sieci wirtualnej, zobacz [Korzystanie z programu Azure Machine Learning Studio w sieci wirtualnej platformy Azure](how-to-enable-studio-virtual-network.md).

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z tematami dotyczącymi projektanta w tym [samouczku: przewidywanie ceny za aplikacje dla urządzeń przenośnych za pomocą projektanta](tutorial-designer-automobile-price-train-score.md).
