---
title: Zarządzanie bibliotekami języka Python dla Apache Spark
description: Dowiedz się, jak dodawać biblioteki języka Python używane przez Apache Spark w usłudze Azure Synapse Analytics i zarządzać nimi.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4bb323e0e8f72456b6a522ede9a98d193e1c3c7e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098778"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Zarządzanie bibliotekami języka Python dla Apache Spark w usłudze Azure Synapse Analytics

Biblioteki zapewniają kod wielokrotnego użytku, który może być dołączany do programów lub projektów. 

Może być konieczne zaktualizowanie środowiska puli Apache Spark bezserwerowej z różnych powodów. Na przykład może się okazać, że:
- jeden z podstawowych zależności właśnie wydał nową wersję.
- potrzebujesz dodatkowego pakietu do uczenia modelu uczenia maszynowego lub przygotowywania danych.
- znaleziono lepszy pakiet i nie jest już potrzebny starszy pakiet.

Aby udostępnić innym firmom lub lokalnie skompilowany kod dla aplikacji, możesz zainstalować bibliotekę na jednej z Apache Spark pul lub sesji notesu. W tym artykule omówiono sposób zarządzania bibliotekami języka Python w puli Apache Spark bezserwerowej.

## <a name="default-installation"></a>Instalacja domyślna
Apache Spark w usłudze Azure Synapse Analytics zawiera pełen zestaw bibliotek dla typowych zadań dotyczących inżynierii danych, przygotowywania danych, uczenia maszynowego i wizualizacji danych. Listę pełnych bibliotek można znaleźć w obszarze [Obsługa wersji Apache Spark](apache-spark-version-support.md). 

Po uruchomieniu wystąpienia platformy Spark te biblioteki zostaną automatycznie uwzględnione. W puli platformy Spark i na poziomie sesji można dodać dodatkowe pakiety Python i utworzone przez użytkownika.

## <a name="pool-libraries"></a>Biblioteki puli
Po zidentyfikowaniu bibliotek języka Python, których chcesz użyć dla aplikacji platformy Spark, możesz zainstalować je w puli platformy Spark. Biblioteki na poziomie puli są dostępne dla wszystkich notesów i zadań uruchomionych w tej puli.

Istnieją dwa podstawowe sposoby instalowania biblioteki w klastrze:
-  Zainstaluj bibliotekę obszarów roboczych, która została przekazana jako pakiet obszaru roboczego.
-  Podaj *requirements.txt* lub *środowisko Conda. yml* specyfikację środowiska, aby zainstalować pakiety z repozytoriów, takich jak PyPI, Conda-fałszerstwo i nie tylko.

> [!IMPORTANT]
> - Jeśli instalowany pakiet jest duży lub zajmuje dużo czasu, ma to wpływ na czas uruchamiania wystąpienia platformy Spark.
> - Zmiana wersji PySpark, Python, Scala/Java, .NET lub Spark nie jest obsługiwana.
> - Instalowanie pakietów z zewnętrznych repozytoriów, takich jak PyPI, Conda-podrabianie, lub domyślnych kanałów Conda nie jest obsługiwane w obszarze roboczym obsługującym program DEP.

### <a name="install-python-packages"></a>Zainstaluj pakiety języka Python
Pakiety języka Python można instalować z repozytoriów, takich jak PyPI i Conda-Forge, dostarczając plik specyfikacji środowiska. 

#### <a name="environment-specification-formats"></a>Formaty specyfikacji środowiska

##### <a name="pip-requirementstxt"></a>requirements.txt PIP
Plik *requirements.txt* (dane wyjściowe `pip freeze` polecenia) może służyć do uaktualnienia środowiska. Po zaktualizowaniu puli pakiety wymienione w tym pliku są pobierane z PyPI. Pełne zależności są następnie buforowane i zapisywane do późniejszego ponownego wykorzystania puli. 

Poniższy fragment kodu przedstawia format pliku wymagań. Nazwa pakietu PyPI jest wyświetlana wraz z dokładną wersją. Ten plik jest zgodny z formatem opisanym w dokumentacji dotyczącej [blokowania PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . 

Ten przykład przypina określoną wersję. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>Format YML (wersja zapoznawcza)
Ponadto można również udostępnić plik *Environment. yml* , aby zaktualizować środowisko puli. Pakiety wymienione w tym pliku są pobierane z domyślnych kanałów Conda, Conda-fałszerstw i PyPI. Możesz określić inne kanały lub usunąć domyślne kanały przy użyciu opcji konfiguracji.

Ten przykład określa zależności kanałów i Conda/PyPI. 

```
name: stats2
channels:
  - defaults
dependencies:
  - bokeh=0.9.2
  - numpy=1.9.*
  - flask
  - pip:
    - matplotlib
```
Aby uzyskać szczegółowe informacje na temat tworzenia środowiska z pliku Environment. yml, zobacz [Tworzenie środowiska z pliku Environment. yml](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually).

#### <a name="update-python-packages"></a>Aktualizowanie pakietów języka Python
Po zidentyfikowaniu pliku specyfikacji środowiska lub zestawu bibliotek, które mają zostać zainstalowane w puli platformy Spark, można zaktualizować biblioteki platformy Spark, przechodząc do usługi Azure Synapse Studio lub Azure Portal. W tym miejscu można podać specyfikację środowiska i wybrać biblioteki obszarów roboczych do zainstalowania. 

Po zapisaniu zmian zadanie Spark uruchomi instalację i buforuje wynikowe środowisko do późniejszego ponownego użycia. Po zakończeniu zadania nowe zadania platformy Spark lub sesje notesu będą używały zaktualizowanych bibliotek puli. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Zarządzanie pakietami z poziomu usługi Azure Synapse Studio lub Azure Portal
Bibliotekami puli platformy Spark można zarządzać przy użyciu usługi Azure Synapse Studio lub Azure Portal. 

Aby zaktualizować lub dodać biblioteki do puli platformy Spark:
1. Przejdź do obszaru roboczego usługi Azure Synapse Analytics z poziomu Azure Portal.

    Jeśli aktualizujesz **Azure Portal**:

    - W sekcji **zasoby Synapse** wybierz kartę **Pule Apache Spark** i wybierz z listy pulę platformy Spark.
     
    - Wybierz **pakiety** z sekcji **Ustawienia** w puli platformy Spark.
  
    ![Zrzut ekranu, który podświetla przycisk pliku konfiguracji środowiska przekazywania.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Dodaj biblioteki języka Python")
   
    Jeśli aktualizujesz **Synapse Studio**:
    - Wybierz pozycję **Zarządzaj** w głównym panelu nawigacyjnym, a następnie wybierz pozycję **Pule Apache Spark**.

    - Wybierz sekcję **pakiety** dla określonej puli platformy Spark.
    ![Zrzut ekranu, który wyróżnia opcję konfiguracji środowiska przekazywania z Studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Dodawanie bibliotek języka Python z programu Studio")
   
2. Przekaż plik konfiguracyjny środowiska przy użyciu selektora plików w sekcji  **pakiety** na stronie.
3. Możesz również wybrać dodatkowe **pakiety obszarów roboczych** , które mają zostać dodane do puli. 
4. Po zapisaniu zmian zadanie systemowe zostanie wyzwolone w celu zainstalowania i przebuforowania określonych bibliotek. Ten proces ułatwia skrócenie czasu uruchamiania sesji. 
5. Po pomyślnym zakończeniu zadania wszystkie nowe sesje będą pobierać zaktualizowane biblioteki puli.

> [!IMPORTANT]
> Wybierając opcję **Wymuś nowe ustawienia**, zostaną zakończone wszystkie bieżące sesje dla wybranej puli platformy Spark. Po zakończeniu sesji należy poczekać na ponowne uruchomienie puli. 
>
> Jeśli to ustawienie nie jest zaznaczone, trzeba będzie poczekać na zakończenie bieżącej sesji platformy Spark lub zatrzymać ją ręcznie. Po zakończeniu sesji należy pozwolić na ponowne uruchomienie puli.


##### <a name="track-installation-progress-preview"></a>Śledź postęp instalacji (wersja zapoznawcza)
Zarezerwowane przez system zadanie Spark jest inicjowane za każdym razem, gdy pula jest aktualizowana przy użyciu nowego zestawu bibliotek. To zadanie platformy Spark pomaga monitorować stan instalacji biblioteki. Jeśli instalacja nie powiedzie się z powodu konfliktów biblioteki lub innych problemów, Pula platformy Spark powróci do stanu poprzedniego lub domyślnego. 

Ponadto użytkownicy mogą również sprawdzić dzienniki instalacji, aby zidentyfikować konflikty zależności lub zobaczyć, które biblioteki zostały zainstalowane podczas aktualizacji puli.

Aby wyświetlić te dzienniki:
1. Przejdź do listy aplikacji platformy Spark na karcie **monitorowanie** . 
2. Wybierz zadanie aplikacji system Spark, które odpowiada aktualizacji puli. Te zadania systemowe są uruchamiane w tytule *SystemReservedJob-LibraryManagement* .
   ![Zrzut ekranu, który podświetla zadanie zastrzeżonej biblioteki systemowej.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Wyświetl zadanie biblioteki systemowej")
3. Przełącz, aby wyświetlić **sterowniki** i dzienniki **stdout** . 
4. W wynikach zostaną wyświetlone dzienniki powiązane z instalacją zależności.
    ![Zrzut ekranu przedstawiający wyniki zadań zarezerwowanych dla biblioteki systemowej.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Wyświetl postęp zadania biblioteki systemowej")

## <a name="install-wheel-files"></a>Zainstaluj pliki kółka
Pliki kółka języka Python są typowym sposobem tworzenia pakietów bibliotek języka Python. W ramach usługi Azure Synapse Analytics użytkownicy mogą przekazywać pliki kół do dobrze znanej lokalizacji konta Azure Data Lake Storage lub przekazywania przy użyciu interfejsu pakietów obszaru roboczego usługi Azure Synapse.

### <a name="workspace-packages-preview"></a>Pakiety obszaru roboczego (wersja zapoznawcza)
Pakiety obszaru roboczego mogą być plikami niestandardowego lub prywatnym kółkiem. Te pakiety można przekazać do obszaru roboczego, a następnie przypisać je do określonej puli platformy Spark.

Aby dodać pakiety obszaru roboczego:
1. Przejdź do karty **Zarządzanie**  >  **pakietami obszarów roboczych** .
2. Przekaż pliki kółka przy użyciu selektora plików.
3. Po przekazaniu plików do obszaru roboczego usługi Azure Synapse można dodać te pakiety do danej puli Apache Spark.

![Zrzut ekranu, który wyróżnia pakiety obszaru roboczego.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Wyświetl pakiety obszaru roboczego")

### <a name="storage-account"></a>Konto magazynu
W puli Apache Spark można zainstalować pakiety kółka utworzone niestandardowo przez przekazanie wszystkich plików koła do konta Azure Data Lake Storage (Gen2), które jest połączone z obszarem roboczym Synapse. 

Pliki powinny zostać przekazane do następującej ścieżki w domyślnym kontenerze konta magazynu: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

Może być konieczne dodanie ```python``` folderu w ```libraries``` folderze, jeśli jeszcze nie istnieje.

> [!IMPORTANT]
> Aby zainstalować biblioteki niestandardowe przy użyciu metody magazynu usługi Azure datalake, musisz mieć uprawnienia **współautora danych obiektów blob magazynu** lub **dane obiektu blob magazynu** na podstawowym koncie magazynu Gen2, które jest połączone z obszarem roboczym usługi Azure Synapse Analytics.

>[!WARNING]
> Podczas udostępniania niestandardowych plików kół użytkownicy nie mogą podawać plików kółka zarówno na koncie magazynu, jak i w interfejsie biblioteki obszaru roboczego. Jeśli są podane oba te elementy, zostaną zainstalowane tylko pliki kółka określone na liście pakiety obszaru roboczego. 

## <a name="session-scoped-packages-preview"></a>Pakiety z zakresem sesji (wersja zapoznawcza)
Oprócz pakietów poziomu puli można także określić biblioteki o zakresie sesji na początku sesji notesu.  Biblioteki o zakresie sesji umożliwiają Określanie niestandardowych środowisk Python i używanie ich w ramach sesji notesu. 

W przypadku korzystania z bibliotek o zakresie sesji należy pamiętać o następujących kwestiach:
   - W przypadku instalowania bibliotek z zakresem sesji tylko bieżący Notes ma dostęp do określonych bibliotek. 
   - Te biblioteki nie wpłyną na inne sesje ani zadania korzystające z tej samej puli platformy Spark. 
   - Te biblioteki są instalowane w oparciu o podstawowe biblioteki środowiska uruchomieniowego i na poziomie puli. 
   - Biblioteki notesów będą mieć najwyższy priorytet.

Aby określić pakiety z zakresem sesji:
1.  Przejdź do wybranej puli platformy Spark i upewnij się, że włączono biblioteki na poziomie sesji.  To ustawienie można włączyć, przechodząc do karty **Zarządzanie**  >    >  **pakietami** puli Apache Spark. ![Włącz pakiety sesji.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Włącz pakiety sesji")
2.  Po zastosowaniu tego ustawienia możesz otworzyć Notes i wybrać pozycję **Konfiguruj** >  **pakiety** sesji.
  ![Określ pakiety sesji.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Aktualizowanie konfiguracji sesji")
3.  W tym miejscu możesz przekazać plik Conda *środowiska. yml* , aby zainstalować lub uaktualnić pakiety w ramach sesji. Po rozpoczęciu sesji zostaną zainstalowane określone biblioteki. Po zakończeniu sesji te biblioteki nie będą już dostępne, ponieważ są one specyficzne dla danej sesji.

## <a name="verify-installed-libraries"></a>Weryfikowanie zainstalowanych bibliotek
Aby sprawdzić, czy poprawne wersje odpowiednich bibliotek są zainstalowane z PyPI, uruchom następujący kod:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
W niektórych przypadkach w celu wyświetlenia wersji pakietu z Conda może być konieczne sprawdzenie wersji pakietu osobno.

## <a name="next-steps"></a>Następne kroki
- Wyświetlanie bibliotek domyślnych: [Obsługa wersji Apache Spark](apache-spark-version-support.md)
- Rozwiązywanie problemów z błędami instalacji biblioteki: [Rozwiązywanie problemów z bibliotekami](apache-spark-troubleshoot-library-errors.md)
- Tworzenie prywatnego kanału Conda przy użyciu konta Azure Data Lake Storage: [Conda Private Channels](./spark/../apache-spark-custom-conda-channel.md)
