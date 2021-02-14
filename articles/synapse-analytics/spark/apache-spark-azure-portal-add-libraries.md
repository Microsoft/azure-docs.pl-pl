---
title: Zarządzanie bibliotekami Apache Spark
description: Dowiedz się, jak dodawać biblioteki używane przez Apache Spark w usłudze Azure Synapse Analytics i zarządzać nimi.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 0458fb8b140166b7bdf0fc0df41dbb207fdce3c9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518525"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Zarządzanie bibliotekami Apache Spark w usłudze Azure Synapse Analytics

Biblioteki zapewniają kod wielokrotnego użytku, który może być dołączany do programów lub projektów. Aby udostępnić innym firmom lub lokalnie skompilowany kod dla aplikacji, można zainstalować bibliotekę na jednym z pul Apache Spark bezserwerowych. Po zainstalowaniu biblioteki dla puli platformy Spark będzie ona dostępna dla wszystkich sesji korzystających z tej samej puli. 

## <a name="before-you-begin"></a>Zanim rozpoczniesz
- Aby zainstalować i zaktualizować biblioteki, musisz mieć uprawnienia **współautora danych obiektów blob magazynu** lub **dane obiektu blob magazynu** na podstawowym koncie magazynu Gen2, które jest połączone z obszarem roboczym usługi Azure Synapse Analytics.
  
## <a name="default-installation"></a>Instalacja domyślna
Apache Spark w usłudze Azure Synapse Analytics ma kompletną instalację Anacondas plus dodatkowe biblioteki. Listę pełnych bibliotek można znaleźć w obszarze [Obsługa wersji Apache Spark](apache-spark-version-support.md). 

Po uruchomieniu wystąpienia platformy Spark te biblioteki zostaną automatycznie uwzględnione. Do poziomu puli Spark można dodać dodatkowe pakiety Python i utworzone przez użytkownika.


## <a name="manage-python-packages"></a>Zarządzanie pakietami języka Python
Po zidentyfikowaniu bibliotek, które mają być używane dla aplikacji platformy Spark, można je zainstalować w puli platformy Spark. 

 Plik *requirements.txt* (dane wyjściowe `pip freeze` polecenia) może służyć do uaktualnienia środowiska wirtualnego. Pakiety wymienione w tym pliku do zainstalowania lub uaktualnienia są pobierane z PyPI w momencie uruchamiania puli. Ten plik wymagań jest używany za każdym razem, gdy wystąpienie platformy Spark jest tworzone na podstawie tej puli platformy Spark.

> [!IMPORTANT]
> - Jeśli instalowany pakiet jest duży lub zajmuje dużo czasu, ma to wpływ na czas uruchamiania wystąpienia platformy Spark.
> - Pakiety wymagające obsługi kompilatora w momencie instalacji, takie jak GCC, nie są obsługiwane.
> - Pakiety nie mogą być obniżane, tylko dodawane lub uaktualniane.
> - Zmiana wersji PySpark, Python, Scala/Java, .NET lub Spark nie jest obsługiwana.
> - Instalowanie pakietów z PyPI nie jest obsługiwane w obszarach roboczych z obsługą programu DEP.


### <a name="requirements-format"></a>Format wymagań

Poniższy fragment kodu przedstawia format pliku wymagań. Nazwa pakietu PyPi jest wyświetlana wraz z dokładną wersją. Ten plik jest zgodny z formatem opisanym w dokumentacji dotyczącej [blokowania PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . Ten przykład przypina określoną wersję. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Zainstaluj pakiety języka Python
Podczas opracowywania aplikacji platformy Spark może się okazać, że trzeba zaktualizować istniejące lub zainstalować nowe biblioteki. Biblioteki można aktualizować podczas tworzenia puli lub po niej.

> [!IMPORTANT]
> Aby zainstalować biblioteki, musisz mieć uprawnienia współautora danych obiektów blob lub magazyn danych obiektów blob magazynu na podstawowym koncie magazynu Gen2 połączonym z obszarem roboczym Synapse.

#### <a name="install-packages-during-pool-creation"></a>Zainstaluj pakiety podczas tworzenia puli
Aby zainstalować biblioteki w puli platformy Spark podczas tworzenia puli:
   
1. Przejdź do obszaru roboczego usługi Azure Synapse Analytics z poziomu Azure Portal.
   
2. Wybierz pozycję **Utwórz pulę Apache Spark** a następnie wybierz kartę **Ustawienia dodatkowe** . 
   
3. Przekaż plik konfiguracyjny środowiska przy użyciu selektora plików w sekcji **pakiety** na stronie. 
   
    ![Dodawanie bibliotek języka Python podczas tworzenia puli](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Dodaj biblioteki języka Python")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Instalowanie pakietów z obszaru roboczego Synapse
Aby zaktualizować lub dodać więcej bibliotek do puli platformy Spark z portalu usługi Azure Synapse Analytics:

1.  Przejdź do obszaru roboczego usługi Azure Synapse Analytics z poziomu Azure Portal.
   
2.  Uruchom obszar roboczy usługi Azure Synapse Analytics z poziomu Azure Portal.

3.  Wybierz pozycję **Zarządzaj** w głównym panelu nawigacyjnym, a następnie wybierz pozycję **Pule Apache Spark**.
   
4. Wybierz jedną pulę platformy Spark i Przekaż plik konfiguracyjny środowiska przy użyciu selektora plików w sekcji  **pakiety** na stronie.

    ![Dodawanie bibliotek języka Python w programie Synapse](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png)
   
#### <a name="install-packages-from-the-azure-portal"></a>Zainstaluj pakiety z Azure Portal
Aby zainstalować bibliotekę w puli platformy Spark bezpośrednio z Azure Portal:
   
 1. Przejdź do obszaru roboczego usługi Azure Synapse Analytics z poziomu Azure Portal.
   
 2. W sekcji **zasoby Synapse** wybierz kartę **Pule Apache Spark** i wybierz z listy pulę platformy Spark.
   
 3. Wybierz pozycję **pakiety** z sekcji **Ustawienia** w puli platformy Spark. 

 4. Przekaż plik konfiguracyjny środowiska przy użyciu selektora plików.

    ![Zrzut ekranu, który podświetla przycisk pliku konfiguracji środowiska przekazywania.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Dodaj biblioteki języka Python")

### <a name="verify-installed-libraries"></a>Weryfikowanie zainstalowanych bibliotek

Aby sprawdzić, czy są zainstalowane poprawne wersje odpowiednich bibliotek, uruchom następujący kod

```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
### <a name="update-python-packages"></a>Aktualizowanie pakietów języka Python
Pakiety mogą być dodawane lub modyfikowane w dowolnym momencie między sesjami. Nowy plik konfiguracji pakietu spowoduje zastąpienie istniejących pakietów i wersji.  

Aby zaktualizować lub odinstalować bibliotekę:
1. Przejdź do obszaru roboczego usługi Azure Synapse Analytics. 

2. Za pomocą Azure Portal lub obszaru roboczego Azure Synapse wybierz **pulę Apache Spark** , którą chcesz zaktualizować.

3. Przejdź do sekcji **Packages** i przekaż nowy plik konfiguracyjny środowiska
   
4. Po zapisaniu zmian konieczne będzie zakończenie aktywnych sesji i ponowne uruchomienie puli. Opcjonalnie można wymusić zakończenie aktywnych sesji, zaznaczając pole wyboru, aby **wymusić nowe ustawienia**.

    ![Dodaj biblioteki języka Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Dodaj biblioteki języka Python")
   

> [!IMPORTANT]
> Wybierając opcję **Wymuś nowe ustawienia**, zostaną zakończone wszystkie bieżące sesje dla wybranej puli platformy Spark. Po zakończeniu sesji należy poczekać na ponowne uruchomienie puli. 
>
> Jeśli to ustawienie nie jest zaznaczone, trzeba będzie poczekać na zakończenie bieżącej sesji platformy Spark lub zatrzymać ją ręcznie. Po zakończeniu sesji należy pozwolić na ponowne uruchomienie puli. 


## <a name="manage-a-python-wheel"></a>Zarządzanie kołem języka Python

### <a name="install-a-custom-wheel-file"></a>Instalowanie pliku kółka niestandardowego
W puli Apache Spark można zainstalować pakiety kółka utworzone niestandardowo przez przekazanie wszystkich plików koła do konta Azure Data Lake Storage (Gen2), które jest połączone z obszarem roboczym Synapse. 

Pliki powinny zostać przekazane do następującej ścieżki w domyślnym kontenerze konta magazynu: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

Może być konieczne dodanie ```python``` folderu w ```libraries``` folderze, jeśli jeszcze nie istnieje.

>[!IMPORTANT]
>Pakiety niestandardowe mogą być dodawane lub modyfikowane między sesjami. Należy jednak poczekać na ponowne uruchomienie puli i sesji, aby zobaczyć zaktualizowany pakiet.

## <a name="next-steps"></a>Następne kroki
- Wyświetlanie bibliotek domyślnych: [Obsługa wersji Apache Spark](apache-spark-version-support.md)
