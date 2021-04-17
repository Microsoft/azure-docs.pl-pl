---
title: Zarządzanie bibliotekami języka Python dla Apache Spark
description: Dowiedz się, jak dodawać biblioteki języka Python używane przez program Apache Spark i zarządzać nimi w Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 1d64233fc477ec25f91bb73c744b10210571df41
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588347"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Zarządzanie bibliotekami języka Python dla Apache Spark w Azure Synapse Analytics

Biblioteki zapewniają kod wielokrotnego użytku, który można uwzględnić w programach lub projektach. 

Może być konieczne zaktualizowanie środowiska puli Apache Spark serwera z różnych powodów. Na przykład może się okazać, że:
- Jedna z podstawowych zależności właśnie opublikowała nową wersję.
- potrzebujesz dodatkowego pakietu do trenowania modelu uczenia maszynowego lub przygotowywania danych.
- Znaleziono lepszy pakiet i starszy pakiet nie jest już potrzebny.

Aby udostępnić aplikacjom kod innej firmy lub lokalnie, możesz zainstalować bibliotekę w jednej z bez serwera Apache Spark pul lub sesji notesu. W tym artykule opisano, jak zarządzać bibliotekami języka Python w ramach puli Apache Spark serwera.

## <a name="default-installation"></a>Instalacja domyślna
Apache Spark w Azure Synapse Analytics ma pełny zestaw bibliotek dla typowych zadań inżynierii danych, przygotowywania danych, uczenia maszynowego i wizualizacji danych. Pełną listę bibliotek można znaleźć na stronie [Apache Spark wersji systemu](apache-spark-version-support.md). 

Po uruchamianiu wystąpienia platformy Spark te biblioteki zostaną automatycznie dołączone. Dodatkowe pakiety języka Python i niestandardowe można dodawać na poziomie puli i sesji platformy Spark.

## <a name="pool-libraries"></a>Biblioteki puli
Po zidentyfikowaniu bibliotek języka Python, których chcesz użyć dla aplikacji Spark, możesz zainstalować je w puli platformy Spark. Biblioteki na poziomie puli są dostępne dla wszystkich notesów i zadań uruchomionych w puli.

Istnieją dwa podstawowe sposoby instalowania biblioteki w klastrze:
-  Zainstaluj bibliotekę obszarów roboczych, która została przesłana jako pakiet obszaru roboczego.
-  Podaj *specyfikacjęrequirements.txt* *environment.yml lub Conda environment.yml,* aby instalować pakiety z repozytoriów, takich jak PyPI, Conda-Forge i nie tylko.

> [!IMPORTANT]
> - Jeśli instalowany pakiet jest duży lub instalacja zajmuje dużo czasu, ma to wpływ na czas uruchamiania wystąpienia platformy Spark.
> - Zmiana wersji PySpark, Python, Scala/Java, .NET lub Spark nie jest obsługiwana.
> - Instalowanie pakietów z repozytoriów zewnętrznych, takich jak PyPI, Conda-Forge lub domyślne kanały Conda, nie jest obsługiwane w obszarach roboczych z włączoną obsługą programu DEP.

### <a name="install-python-packages"></a>Instalowanie pakietów języka Python
Pakiety języka Python można instalować z repozytoriów, takich jak PyPI i Conda-Forge, udostępniając plik specyfikacji środowiska. 

#### <a name="environment-specification-formats"></a>Formaty specyfikacji środowiska

##### <a name="pip-requirementstxt"></a>Tryb requirements.txt PIP
Plik *requirements.txt* (dane wyjściowe z polecenia ) może `pip freeze` służyć do uaktualniania środowiska. Po zaktualizowania puli pakiety wymienione w tym pliku są pobierane z pliku PyPI. Pełne zależności są następnie buforowane i zapisywane w celu późniejszego ponownego użycia puli. 

Poniższy fragment kodu przedstawia format pliku wymagań. Zostanie wymieniona nazwa pakietu PyPI wraz z dokładną wersją. Ten plik jest zgodny z formatem opisanym w [dokumentacji referencyjnej dotyczącej blokowania programu pip.](https://pip.pypa.io/en/stable/reference/pip_freeze/) 

Ten przykład przypina określoną wersję. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>Format YML (wersja zapoznawcza)
Ponadto można również udostępnić plik *environment.yml* w celu zaktualizowania środowiska puli. Pakiety wymienione w tym pliku są pobierane z domyślnych kanałów Conda, Conda-Forge i PyPI. Możesz określić inne kanały lub usunąć kanały domyślne przy użyciu opcji konfiguracji.

W tym przykładzie określono kanały i zależności Conda/PyPI. 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
Aby uzyskać szczegółowe informacje na temat tworzenia środowiska na podstawie tego pliku environment.yml, zobacz Creating an environment from an environment.yml file (Tworzenie środowiska na podstawie pliku [environment.yml).](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment
)

#### <a name="update-python-packages"></a>Aktualizowanie pakietów języka Python
Po zidentyfikowaniu pliku specyfikacji środowiska lub zestawu bibliotek, które chcesz zainstalować w puli platformy Spark, możesz zaktualizować biblioteki puli platformy Spark, przechodząc do programu Azure Synapse Studio lub Azure Portal. W tym miejscu możesz podać specyfikację środowiska i wybrać biblioteki obszarów roboczych do zainstalowania. 

Po zapisaniu zmian zadanie platformy Spark uruchamia instalację i buforuje wynikowe środowisko do późniejszego użycia. Po zakończeniu zadania nowe zadania platformy Spark lub sesje notesu będą używać zaktualizowanych bibliotek puli. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Zarządzanie pakietami z programu Azure Synapse Studio lub Azure Portal
Bibliotekami puli platformy Spark można zarządzać za pomocą programu Azure Synapse Studio lub Azure Portal. 

Aby zaktualizować lub dodać biblioteki do puli platformy Spark:
1. Przejdź do swojego Azure Synapse Analytics roboczego z Azure Portal.

    Jeśli aktualizujesz z **Azure Portal**:

    - W sekcji **Zasoby synapse** wybierz **kartę pul Apache Spark i** wybierz pulę platformy Spark z listy.
     
    - Wybierz pozycję **Pakiety** w **sekcji Ustawienia** puli platformy Spark.
  
    ![Zrzut ekranu przedstawiający przycisk przekazywania pliku konfiguracji środowiska.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Dodawanie bibliotek języka Python")
   
    Jeśli aktualizujesz z **Synapse Studio**:
    - Wybierz **pozycję Zarządzaj** z głównego panelu nawigacyjnego, a następnie wybierz pozycję Apache Spark **pule.**

    - Wybierz **sekcję Pakiety** dla określonej puli platformy Spark.
    ![Zrzut ekranu przedstawiający opcję przekazywania konfiguracji środowiska z programu Studio.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Dodawanie bibliotek języka Python z programu Studio")
   
2. Przekaż plik konfiguracji środowiska przy użyciu selektora plików w  **sekcji Pakiety** na stronie.
3. Możesz również wybrać dodatkowe pakiety **obszarów roboczych do** dodania do puli. 
4. Po zapisaniu zmian zostanie wyzwolone zadanie systemowe instalowania i buforowania określonych bibliotek. Ten proces pomaga skrócić ogólny czas uruchamiania sesji. 
5. Po pomyślnym ukończeniu zadania wszystkie nowe sesje przejmą zaktualizowane biblioteki puli.

> [!IMPORTANT]
> Wybranie opcji Wymuszanie **nowych ustawień** spowoduje zakończenie wszystkich bieżących sesji dla wybranej puli platformy Spark. Po zakończeniu sesji musisz poczekać na ponowne uruchomienie puli. 
>
> Jeśli to ustawienie nie jest zaznaczone, musisz poczekać na zakończenie bieżącej sesji platformy Spark lub zatrzymać ją ręcznie. Po zakończeniu sesji należy pozwolić na ponowne uruchomienie puli.


##### <a name="track-installation-progress-preview"></a>Śledzenie postępu instalacji (wersja zapoznawcza)
Zadanie platformy Spark zarezerwowane dla systemu jest inicjowane za każdym razem, gdy pula jest aktualizowana przy użyciu nowego zestawu bibliotek. To zadanie platformy Spark pomaga monitorować stan instalacji biblioteki. Jeśli instalacja nie powiedzie się z powodu konfliktów biblioteki lub innych problemów, pula platformy Spark zostanie przywrócona do stanu poprzedniego lub domyślnego. 

Ponadto użytkownicy mogą również sprawdzać dzienniki instalacji, aby zidentyfikować konflikty zależności lub zobaczyć, które biblioteki zostały zainstalowane podczas aktualizacji puli.

Aby wyświetlić te dzienniki:
1. Przejdź do listy aplikacji platformy Spark na **karcie** Monitorowanie. 
2. Wybierz systemowe zadanie aplikacji Spark odpowiadające aktualizacji puli. Te zadania systemowe są uruchamiane pod *tytułem SystemReservedJob-LibraryManagement.*
   ![Zrzut ekranu przedstawiający zadanie biblioteki zarezerwowanej systemu.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Wyświetlanie zadania biblioteki systemowej")
3. Przełącz się, aby **wyświetlić dzienniki** sterownika **i stdout.** 
4. W wynikach zobaczysz dzienniki związane z instalacją zależności.
    ![Zrzut ekranu przedstawiający wyniki zadania biblioteki zarezerwowanej systemu.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Wyświetlanie postępu zadania biblioteki systemowej")

## <a name="install-wheel-files"></a>Instalowanie plików wheel
Pliki wheel języka Python są częstym sposobem pakowania bibliotek języka Python. W Azure Synapse Analytics użytkownicy mogą przekazywać pliki wheel do dobrze znanej lokalizacji konta usługi Azure Data Lake Storage lub przekazywać przy użyciu interfejsu pakietów Azure Synapse Workspace.

### <a name="workspace-packages-preview"></a>Pakiety obszarów roboczych (wersja zapoznawcza)
Pakiety obszarów roboczych mogą być niestandardowymi lub prywatnymi plikami wheel. Możesz przekazać te pakiety do obszaru roboczego, a następnie przypisać je do określonej puli platformy Spark.

Aby dodać pakiety obszarów roboczych:
1. Przejdź do karty **Zarządzanie**  >  **pakietami obszarów roboczych.**
2. Przekaż pliki wheel przy użyciu selektora plików.
3. Po przesłaniu plików do Azure Synapse roboczego można dodać te pakiety do danej puli Apache Spark zasobów.

![Zrzut ekranu przedstawiający pakiety obszarów roboczych.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Wyświetlanie pakietów obszarów roboczych")

>[!WARNING]
>- W Azure Synapse pula Apache Spark może korzystać z bibliotek niestandardowych, które są przekazywane jako pakiety obszaru roboczego lub przekazywane w ramach dobrze znanej Azure Data Lake Storage danych. Jednak obu tych opcji nie można używać jednocześnie w tej samej Apache Spark puli. Jeśli pakiety są dostarczane przy użyciu obu metod, zostaną zainstalowane tylko pliki wheel określone na liście pakietów obszaru roboczego. 
>
>- Gdy pakiety obszarów roboczych (wersja zapoznawcza) są używane do instalowania pakietów w danej puli Apache Spark, istnieje ograniczenie, że nie można już określać pakietów przy użyciu ścieżki konta magazynu w tej samej puli.  

### <a name="storage-account"></a>Konto magazynu
Niestandardowe pakiety wheel można zainstalować w puli Apache Spark, przesyłając wszystkie pliki wheel do konta usługi Azure Data Lake Storage (Gen2) połączonego z obszarem roboczym usługi Synapse. 

Pliki powinny zostać przekazane do następującej ścieżki w domyślnym kontenerze konta magazynu: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> W niektórych przypadkach może być konieczne utworzenie ścieżki pliku na podstawie powyższej struktury, jeśli jeszcze nie istnieje. Na przykład może być konieczne dodanie ```python``` folderu w folderze, jeśli jeszcze nie ```libraries``` istnieje.

> [!IMPORTANT]
> Aby zainstalować biblioteki niestandardowe przy użyciu metody usługi Azure DataLake Storage, musisz mieć uprawnienia Współautor danych obiektu blob usługi **Storage** lub Właściciel danych obiektu **blob** magazynu na podstawowym koncie magazynu Gen2 połączonym z obszarem roboczym usługi Azure Synapse Analytics.


## <a name="session-scoped-packages-preview"></a>Pakiety w zakresie sesji (wersja zapoznawcza)
Oprócz pakietów na poziomie puli można również określić biblioteki w zakresie sesji na początku sesji notesu.  Biblioteki o zakresie sesji umożliwiają określanie niestandardowych środowisk Języka Python i korzystanie z nich w ramach sesji notesu. 

W przypadku korzystania z bibliotek o zakresie sesji należy pamiętać o następujących kwestiach:
   - Podczas instalowania bibliotek o zakresie sesji tylko bieżący notes ma dostęp do określonych bibliotek. 
   - Te biblioteki nie będą miały wpływu na inne sesje lub zadania korzystające z tej samej puli platformy Spark. 
   - Te biblioteki są instalowane na podstawie podstawowego środowiska uruchomieniowego i bibliotek na poziomie puli. 
   - Biblioteki notesów będą miały najwyższy priorytet.

Aby określić pakiety w zakresie sesji:
1.  Przejdź do wybranej puli platformy Spark i upewnij się, że włączono biblioteki na poziomie sesji.  To ustawienie można włączyć, przechodząc do karty  >  **Zarządzanie pakietami Apache Spark puli.**  >   Włącz pakiety ![sesji.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Włączanie pakietów sesji")
2.  Po zastosowaniu tego ustawienia możesz otworzyć notes i wybrać pozycję **Konfiguruj pakiety** >  **sesji.**
  ![Określ pakiety sesji.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Aktualizowanie konfiguracji sesji")
3.  W tym miejscu możesz przekazać plik *environment.yml* środowiska Conda, aby zainstalować lub uaktualnić pakiety w ramach sesji. Po uruchomieniu sesji zostaną zainstalowane określone biblioteki. Po zakończeniu sesji te biblioteki nie będą już dostępne, ponieważ są specyficzne dla Twojej sesji.

## <a name="verify-installed-libraries"></a>Weryfikowanie zainstalowanych bibliotek
Aby sprawdzić, czy poprawne wersje poprawnych bibliotek są zainstalowane z PyPI, uruchom następujący kod:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
W niektórych przypadkach w celu wyświetlenia wersji pakietu ze strony Conda może być konieczne sprawdzenie wersji pakietu osobno.

## <a name="next-steps"></a>Następne kroki
- Wyświetlanie bibliotek domyślnych: [obsługa Apache Spark wersji](apache-spark-version-support.md)
- Rozwiązywanie problemów z błędami instalacji biblioteki: [Rozwiązywanie problemów z biblioteką](apache-spark-troubleshoot-library-errors.md)
- Tworzenie prywatnego kanału Conda przy użyciu konta Azure Data Lake Storage: [kanały prywatne Conda](./spark/../apache-spark-custom-conda-channel.md)
