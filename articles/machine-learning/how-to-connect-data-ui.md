---
title: Łączenie się z danymi w usługach Storage na platformie Azure
titleSuffix: Azure Machine Learning
description: Twórz magazyny danych i zestawy danych, aby bezpiecznie łączyć się z danymi w usługach Storage na platformie Azure za pomocą programu Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.custom: how-to
ms.openlocfilehash: 116dd65bf04c01f513e196a2f1b37d54aacbf1fe
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91841360"
---
# <a name="connect-to-data-with-the-azure-machine-learning-studio"></a>Łączenie się z danymi za pomocą programu Azure Machine Learning Studio

W tym artykule dowiesz się, jak uzyskać dostęp do danych za pomocą programu [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). Nawiąż połączenie z danymi w usługach Storage na platformie Azure przy użyciu [Azure Machine Learning magazynów](how-to-access-data.md)danych, a następnie Spakuj te dane dla zadań w przepływach [Azure Machine Learning](how-to-create-register-datasets.md)pracy w usłudze

W poniższej tabeli zdefiniowano i podsumowano zalety magazynów danych i zestawów danych. 

|Obiekt|Opis| Korzyści|   
|---|---|---|
|Magazyny danych| Bezpiecznie łącz się z usługą magazynu na platformie Azure, przechowując informacje o połączeniu, takie jak identyfikator subskrypcji i autoryzacja tokenu w [Key Vault](https://azure.microsoft.com/services/key-vault/) skojarzonych z obszarem roboczym | Ponieważ informacje są bezpiecznie przechowywane, należy <br><br> <li> Nie &nbsp; umieszczaj &nbsp; &nbsp; &nbsp; &nbsp; w zagrożeniu poświadczeń uwierzytelniania ani oryginalnych &nbsp; źródeł danych. <li> Nie trzeba już pokodować ich w skryptach.
|Zestawy danych| Tworząc zestaw danych, tworzysz odwołanie do lokalizacji źródła danych wraz z kopią jego metadanych. Z zestawami danych można, <br><br><li> Dostęp do danych podczas uczenia modelu.<li> Udostępnianie danych i współpraca z innymi użytkownikami.<li> Korzystaj z bibliotek typu open source, takich jak Pandas, na potrzeby eksploracji danych. | Ponieważ zestawy danych są opóźnieniem oceniane, a dane pozostają w istniejącej lokalizacji, należy <br><br><li>Przechowywanie pojedynczej kopii danych w magazynie.<li> Nie Powiąż dodatkowego kosztu magazynowania <li> Nie ryzykuj przypadkowego zmiany oryginalnych źródeł danych.<li>Zwiększ szybkość działania przepływu pracy w usłudze ML. 

Aby zrozumieć, w jaki sposób magazyny danych i zestawy danych mieszczą się w ogólnym przepływie pracy Azure Machine Learning, zobacz artykuł [bezpieczny dostęp do](concept-data.md#data-workflow) danych.

Aby uzyskać pierwsze środowisko kodu, zapoznaj się z następującymi artykułami, aby użyć [zestawu SDK języka Python Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true) w programie:
* [Nawiązywanie połączenia z usługami Azure Storage z magazynami danych](how-to-access-data.md). 
* [Utwórz Azure Machine Learning zestawy danych](how-to-create-register-datasets.md). 

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

- Dostęp do [Azure Machine Learning Studio](https://ml.azure.com/).

- Obszar roboczy usługi Azure Machine Learning. [Utwórz obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).

    -  Podczas tworzenia obszaru roboczego kontener obiektów blob platformy Azure i udział plików platformy Azure są automatycznie rejestrowane jako magazyny danych w obszarze roboczym. Są one nazwane `workspaceblobstore` i `workspacefilestore` , odpowiednio. Jeśli magazyn obiektów BLOB jest wystarczający dla Twoich potrzeb, `workspaceblobstore` jest ustawiony jako domyślny magazyn danych i jest już skonfigurowany do użycia. W przeciwnym razie potrzebujesz konta magazynu na platformie Azure z [obsługiwanym typem magazynu](how-to-access-data.md#matrix).
    

## <a name="create-datastores"></a>Tworzenie magazynów danych

Magazyny danych można tworzyć na podstawie [tych rozwiązań usługi Azure Storage](how-to-access-data.md#matrix). **W przypadku nieobsługiwanych rozwiązań magazynu**i zapisania kosztu ruchu wychodzącego w trakcie eksperymentów z sieci, należy [przenieść dane](how-to-access-data.md#move) do obsługiwanego rozwiązania usługi Azure Storage. [Dowiedz się więcej o magazynach](how-to-access-data.md)danych. 



Utwórz nowy magazyn danych w kilku krokach za pomocą programu Azure Machine Learning Studio.

> [!IMPORTANT]
> Jeśli Twoje konto magazynu danych znajduje się w sieci wirtualnej, wymagane są dodatkowe czynności konfiguracyjne, aby upewnić się, że Studio ma dostęp do danych. Aby zapewnić stosowanie odpowiednich czynności konfiguracyjnych, zobacz [izolacja sieci & prywatność](how-to-enable-virtual-network.md#machine-learning-studio) .

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).
1. Wybierz pozycję **magazyny** danych w lewym okienku w obszarze **Zarządzaj**.
1. Wybierz pozycję **+ nowy magazyn**danych.
1. Wypełnij formularz dla nowego magazynu danych. Formularz jest inteligentnie aktualizowany na podstawie wybranych opcji typu usługi Azure Storage i typu uwierzytelniania. Zapoznaj się z [sekcją dostęp do magazynu i uprawnienia](#access-validation) , aby dowiedzieć się, gdzie znaleźć poświadczenia uwierzytelniania potrzebne do wypełnienia tego formularza.

Poniższy przykład pokazuje, jak wygląda formularz podczas tworzenia **magazynu danych obiektów blob platformy Azure**:

![Formularz dla nowego magazynu danych](media/how-to-connect-data-ui/new-datastore-form.png)

## <a name="create-datasets"></a>Tworzenie zestawów danych

Po utworzeniu magazynu danych Utwórz zestaw danych, który będzie współpracujący z danymi. Zestawy danych pakują dane do opóźnieniemego, który można z niego korzystać na potrzeby zadań uczenia maszynowego, takich jak szkolenie. [Dowiedz się więcej o zestawach danych](how-to-create-register-datasets.md).

Istnieją dwa typy zestawów danych, FileDataset i TabularDataset. 
[FileDatasets](how-to-create-register-datasets.md#filedataset) tworzyć odwołania do jednego lub wielu plików lub publicznych adresów URL. W związku z tym [TabularDatasets](how-to-create-register-datasets.md#tabulardataset) dane są reprezentowane w formacie tabelarycznym. 

Poniższe kroki i animacje pokazują, jak utworzyć zestaw danych w programie [Azure Machine Learning Studio](https://ml.azure.com).

> [!Note]
> Zestawy danych utworzone za pomocą Azure Machine Learning Studio są automatycznie rejestrowane w obszarze roboczym.

![Tworzenie zestawu danych przy użyciu interfejsu użytkownika](./media/how-to-connect-data-ui/create-dataset-ui.gif)

Aby utworzyć zestaw danych w programie Studio:
1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/).
1. Wybierz pozycję **zestawy danych** w sekcji **elementy zawartości** okienka po lewej stronie.
1. Wybierz pozycję **Utwórz zestaw danych** , aby wybrać źródło zestawu danych. To źródło może być plikami lokalnymi, magazynem danych, publicznymi adresami URL lub [otwartymi zestawami danych platformy Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).
1. Wybierz **tabelaryczną** lub **plik** dla typu zestawu danych.
1. Wybierz pozycję **dalej** , aby otworzyć formularz **Magazyn danych i wybór pliku** . Na tym formularzu wybierz lokalizację, w której ma zostać zachowany zestaw danych, a także wybierz pliki danych, które mają być używane dla zestawu danych.
    1. Włącz weryfikację pomijania, jeśli dane są w sieci wirtualnej. Dowiedz się więcej o [izolacji i prywatności sieci wirtualnej](how-to-enable-virtual-network.md#machine-learning-studio).
    1. W przypadku tabelarycznych zestawów danych można określić cechę "szeregów czasowych", aby włączyć operacje związane z czasem na tym elemencie dataset. Dowiedz się [, jak dodać cechy szeregów czasowych do zestawu danych](how-to-monitor-datasets.md#studio-dataset).
1. Wybierz pozycję **dalej** , aby wypełnić **Ustawienia, Podgląd** i formularze **schematów** ; są one inteligentnie wypełniane na podstawie typu pliku i można później skonfigurować zestaw danych przed utworzeniem w tych formularzach. 
1. Wybierz pozycję **dalej** , aby przejrzeć formularz **Potwierdź szczegóły** . Sprawdź wybrane opcje i Utwórz opcjonalny profil danych dla zestawu danych. Dowiedz się więcej na temat [profilowania danych](#profile).
1. Wybierz pozycję **Utwórz** , aby zakończyć tworzenie zestawu danych.

<a name="profile"></a>

### <a name="data-profile-and-preview"></a>Profil danych i Podgląd

Po utworzeniu zestawu danych sprawdź, czy można wyświetlić profil i Podgląd w programie Studio, wykonując następujące kroki. 

1. Zaloguj się do [Azure Machine Learning Studio](https://ml.azure.com/)
1. Wybierz pozycję **zestawy danych** w sekcji **elementy zawartości** okienka po lewej stronie.
1. Wybierz nazwę zestawu danych, który chcesz wyświetlić. 
1. Wybierz kartę **Eksploruj** . 
1. Wybierz kartę **Podgląd** lub **profil** . 

![Wyświetlanie profilu zestawu danych i Podgląd](./media/how-to-connect-data-ui/dataset-preview-profile.gif)

Możesz uzyskać szeroką gamę statystyk podsumowujących dla zestawu danych, aby sprawdzić, czy zestaw danych jest gotowy do użycia. W przypadku kolumn nieliczbowych składają się tylko podstawowe dane statystyczne, takie jak minimalna, maksymalna i liczba błędów. W przypadku kolumn liczbowych można także sprawdzić ich statystyczny czas i oszacować quantiles. 

W każdym przypadku profil danych Azure Machine Learning DataSet obejmuje:

>[!NOTE]
> Puste wpisy są wyświetlane dla funkcji o nieistotnych typach.

|Statystyka|Opis
|------|------
|Cecha| Nazwa sumowanej kolumny.
|Profil| Wizualizacja w wierszu oparta na wywnioskowanym typie. Na przykład ciągi, wartości logiczne i daty będą mieć liczby wartości, podczas gdy miejsca dziesiętne (liczbowe) mają przybliżone histogramy. Pozwala to na szybkie zrozumienie dystrybucji danych.
|Dystrybucja typów| Liczba wartości w wierszu dla typów w kolumnie. Wartości null są własnym typem, więc Wizualizacja jest przydatna do wykrywania nieparzystych lub brakujących wartości.
|Typ|Wywnioskowany typ kolumny. Możliwe wartości to: ciągi, wartości logiczne, daty i miejsca dziesiętne.
|Min.| Minimalna wartość kolumny. Puste wpisy są wyświetlane dla funkcji, których typ nie ma własnej kolejności (np. wartości logicznych).
|Maks.| Maksymalna wartość kolumny. 
|Liczba| Łączna liczba brakujących i nieobecnych wpisów w kolumnie.
|Liczba niebrakujących| Liczba wpisów w kolumnie, których nie ma. Puste ciągi i błędy są traktowane jako wartości, więc nie będą wchodzić w skład "niebrakującej liczby".
|Kwantyle| Przybliżone wartości dla każdego quantileu, aby zapewnić rozkład danych.
|Średnia| Średnia arytmetyczna kolumny lub jej średnia.
|Odchylenie standardowe| Pomiar wielkości rozproszenia lub zmienności danych tej kolumny.
|Variance (Wariancja)| Mierzona, jak daleko odłożenie danych z tej kolumny pochodzi z wartości średniej. 
|Skośność| Mierzona, jak różne dane tej kolumny pochodzą z rozkładu normalnego.
|Kurtoza| Mierzona, jak silnie naśladowanie danych tej kolumny jest porównywane z rozkładem normalnym.

## <a name="storage-access-and-permissions"></a>Dostęp do magazynu i uprawnienia

Aby zapewnić bezpieczne łączenie się z usługą Azure Storage, Azure Machine Learning wymaga uprawnień dostępu do odpowiedniego magazynu danych. Ten dostęp zależy od poświadczeń uwierzytelniania używanych do rejestracji magazynu danych.

### <a name="virtual-network"></a>Sieć wirtualna

Jeśli Twoje konto magazynu danych znajduje się w **sieci wirtualnej**, wymagane są dodatkowe czynności konfiguracyjne, aby zapewnić, że Azure Machine Learning ma dostęp do danych. Aby zapewnić stosowanie odpowiednich czynności konfiguracyjnych podczas tworzenia i rejestrowania magazynu danych, należy zapoznać się z tematem [izolacja sieci & prywatności](how-to-enable-virtual-network.md#machine-learning-studio) .  

### <a name="access-validation"></a>Sprawdzanie poprawności dostępu

**W ramach początkowego procesu tworzenia i rejestracji magazynu**danych Azure Machine Learning automatycznie sprawdza, czy istnieje podstawowa usługa magazynu, oraz czy podany przez użytkownika podmiot zabezpieczeń (username, nazwa główna usługi lub token SAS) ma dostęp do określonego magazynu.

**Po utworzeniu magazynu**danych sprawdzanie poprawności jest wykonywane tylko w przypadku metod, które wymagają dostępu do źródłowego kontenera magazynu, a **nie** do pobierania obiektów magazynu danych. Na przykład sprawdzanie poprawności ma miejsce, jeśli chcesz pobrać pliki z magazynu danych; ale jeśli chcesz jedynie zmienić domyślny magazyn danych, walidacja nie następuje.

Aby uwierzytelnić dostęp do podstawowej usługi magazynu, możesz podać klucz konta, tokeny sygnatur dostępu współdzielonego (SAS) lub jednostkę usługi zgodnie z typem magazynu danych, który chcesz utworzyć. [Macierz typ magazynu](how-to-access-data.md#matrix) zawiera listę obsługiwanych typów uwierzytelniania, które odpowiadają każdemu typowi magazynu danych.

Informacje na temat klucza konta, tokenu sygnatury dostępu współdzielonego i nazwy głównej usługi znajdują się w [Azure Portal](https://portal.azure.com).

* Jeśli planujesz użyć klucza konta lub tokenu sygnatury dostępu współdzielonego w celu uwierzytelnienia, wybierz pozycję **konta magazynu** w okienku po lewej stronie i wybierz konto magazynu, które chcesz zarejestrować.
  * Na stronie **Przegląd** znajdują się takie informacje, jak nazwa konta, kontener i nazwa udziału plików.
      1. W przypadku kluczy konta przejdź do pozycji **klucze dostępu** w okienku **Ustawienia** .
      1. W przypadku tokenów SAS przejdź do obszaru **sygnatury dostępu współdzielonego** w okienku **Ustawienia** .

* Jeśli planujesz użyć jednostki [usługi](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) do uwierzytelniania, przejdź do **rejestracje aplikacji** i wybierz aplikację, której chcesz użyć.
    * Odpowiadająca jej Strona **przeglądu** będzie zawierać wymagane informacje, takie jak identyfikator dzierżawy i identyfikator klienta.

> [!IMPORTANT]
> Ze względów bezpieczeństwa może zajść potrzeba zmiany kluczy dostępu dla konta usługi Azure Storage (klucza konta lub tokenu SAS). W takim przypadku należy zsynchronizować nowe poświadczenia z obszarem roboczym i magazynami danych, które są z nim połączone. Dowiedz się, jak [synchronizować zaktualizowane poświadczenia](how-to-change-storage-access-key.md).

### <a name="permissions"></a>Uprawnienia

W przypadku kontenera obiektów blob platformy Azure i Azure Data Lake magazynu generacji 2 Upewnij się, że poświadczenia uwierzytelniania mają dostęp do **czytnika danych obiektu blob magazynu** . Dowiedz się więcej o [czytniku danych BLOB Storage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader). 

## <a name="train-with-datasets"></a>Szkolenie przy użyciu zestawów danych

Używaj Twoich zestawów danych w eksperymentach uczenia maszynowego do uczenia modeli ML. [Dowiedz się więcej na temat uczenia się z zestawami danych](how-to-train-with-datasets.md)

## <a name="next-steps"></a>Następne kroki

* [Przykładowy krok po kroku szkolenia z TabularDatasets i zautomatyzowane Uczenie maszynowe](tutorial-first-experiment-automated-ml.md).

* [Uczenie modelu](how-to-set-up-training-targets.md).

* Aby uzyskać więcej przykładów szkoleniowych dotyczących zestawu danych, zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
