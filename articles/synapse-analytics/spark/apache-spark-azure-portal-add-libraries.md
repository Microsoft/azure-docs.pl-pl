---
title: Zarządzanie bibliotekami Apache Spark w usłudze Azure Synapse Analytics
description: Dowiedz się, jak dodawać biblioteki używane przez Apache Spark w usłudze Azure Synapse Analytics i zarządzać nimi.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: euang
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: fbcc7ffbde49acfd9afc180418d618060eb923c1
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313539"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Zarządzanie bibliotekami Apache Spark w usłudze Azure Synapse Analytics

Biblioteki zapewniają kod wielokrotnego użytku, który może być dołączany do programów lub projektów. Aby udostępnić innym firmom lub lokalnie skompilowany kod dla aplikacji, możesz zainstalować bibliotekę na jednym z pul Apache Spark bezserwerowych (wersja zapoznawcza). Po zainstalowaniu biblioteki dla puli platformy Spark będzie ona dostępna dla wszystkich sesji korzystających z tej samej puli. 

## <a name="default-installation"></a>Instalacja domyślna
Apache Spark w usłudze Azure Synapse Analytics ma kompletną instalację Anacondas plus dodatkowe biblioteki. Listę pełnych bibliotek można znaleźć w obszarze [Obsługa wersji Apache Spark](apache-spark-version-support.md). 

Po uruchomieniu wystąpienia platformy Spark te biblioteki zostaną automatycznie uwzględnione. Do poziomu puli Spark (wersja zapoznawcza) można dodać dodatkowe skompilowane i niestandardowe pakiety w języku Python i.


## <a name="manage-python-packages"></a>Zarządzanie pakietami języka Python
Po zidentyfikowaniu bibliotek, które mają być używane dla aplikacji platformy Spark, można je zainstalować w puli platformy Spark (wersja zapoznawcza). 

 Plik *requirements.txt* (dane wyjściowe `pip freeze` polecenia) może służyć do uaktualnienia środowiska wirtualnego. Pakiety wymienione w tym pliku do zainstalowania lub uaktualnienia są pobierane z PyPi w momencie uruchamiania puli. Ten plik wymagań jest używany za każdym razem, gdy wystąpienie platformy Spark jest tworzone na podstawie tej puli platformy Spark.

> [!IMPORTANT]
> - Jeśli instalowany pakiet jest duży lub zajmuje dużo czasu, ma to wpływ na czas uruchamiania wystąpienia platformy Spark.
> - Pakiety, które wymagają obsługi kompilatora w czasie instalacji, takie jak w przypadku programu, nie są obsługiwane.
> - Pakiety nie mogą być obniżane, tylko dodawane lub uaktualniane.

### <a name="requirements-format"></a>Format wymagań

Poniższy fragment kodu przedstawia format pliku wymagań. Nazwa pakietu PyPi jest wyświetlana wraz z dokładną wersją. Ten plik jest zgodny z formatem opisanym w dokumentacji dotyczącej [blokowania PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . Ten przykład przypina określoną wersję. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Zainstaluj pakiety języka Python
Podczas opracowywania aplikacji platformy Spark może się okazać, że trzeba zaktualizować istniejące lub zainstalować nowe biblioteki. Biblioteki można aktualizować podczas tworzenia puli lub po niej.

#### <a name="install-packages-during-pool-creation"></a>Zainstaluj pakiety podczas tworzenia puli
Aby zainstalować biblioteki w puli platformy Spark (wersja zapoznawcza) podczas tworzenia puli:
   
1. Przejdź do obszaru roboczego usługi Azure Synapse Analytics z poziomu Azure Portal.
   
2. Wybierz pozycję **Utwórz pulę Apache Spark** a następnie wybierz kartę **Ustawienia dodatkowe** . 
   
3. Przekaż plik konfiguracyjny środowiska przy użyciu selektora plików w sekcji **pakiety** na stronie. 
   
    ![Dodawanie bibliotek języka Python podczas tworzenia puli](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Dodaj biblioteki języka Python")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Instalowanie pakietów z obszaru roboczego Synapse
Aby zaktualizować lub dodać dodatkowe biblioteki do puli platformy Spark (wersja zapoznawcza) z portalu usługi Azure Synapse Analytics:

1.  Przejdź do obszaru roboczego usługi Azure Synapse Analytics z poziomu Azure Portal.
   
2.  Uruchom obszar roboczy usługi Azure Synapse Analytics z poziomu Azure Portal.

3.  Wybierz pozycję **Zarządzaj** w głównym panelu nawigacyjnym, a następnie wybierz pozycję **Pule Apache Spark**.
   
4. Wybierz jedną pulę platformy Spark i Przekaż plik konfiguracyjny środowiska przy użyciu selektora plików w sekcji  **pakiety** na stronie.

    ![Dodawanie bibliotek języka Python w programie Synapse](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png)
   
#### <a name="install-packages-from-the-azure-portal"></a>Zainstaluj pakiety z Azure Portal
Aby zainstalować bibliotekę w puli platformy Spark (wersja zapoznawcza) bezpośrednio z Azure Portal:
   
 1. Przejdź do obszaru roboczego usługi Azure Synapse Analytics z poziomu Azure Portal.
   
 2. W sekcji **zasoby Synapse** wybierz kartę **Pule Apache Spark** i wybierz z listy pulę platformy Spark.
   
 3. Wybierz pozycję **pakiety** z sekcji **Ustawienia** w puli platformy Spark. 

 4. Przekaż plik konfiguracyjny środowiska przy użyciu selektora plików.

    ![Zrzut ekranu, który podświetla przycisk pliku konfiguracji środowiska przekazywania.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Dodaj biblioteki języka Python")

### <a name="verify-installed-libraries"></a>Weryfikowanie zainstalowanych bibliotek

Aby sprawdzić, czy są zainstalowane poprawne wersje odpowiednich bibliotek, uruchom następujący kod

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```
### <a name="update-python-packages"></a>Aktualizowanie pakietów języka Python
Pakiety mogą być dodawane lub modyfikowane w dowolnym momencie między sesjami. Po przekazaniu nowego pliku konfiguracji pakietu spowoduje to zastąpienie istniejących pakietów i wersji.  

Aby zaktualizować lub odinstalować bibliotekę:
1. Przejdź do obszaru roboczego usługi Azure Synapse Analytics. 

2. Za pomocą Azure Portal lub obszaru roboczego Azure Synapse wybierz **pulę Apache Spark** , którą chcesz zaktualizować.

3. Przejdź do sekcji **Packages** i przekaż nowy plik konfiguracyjny środowiska
   
4. Po zapisaniu zmian konieczne będzie zakończenie aktywnych sesji i ponowne uruchomienie puli. Opcjonalnie można wymusić zakończenie aktywnych sesji, zaznaczając pole wyboru, aby **wymusić nowe ustawienia**.

    ![Dodaj biblioteki języka Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Dodaj biblioteki języka Python")
   

> [!IMPORTANT]
> Wybierając opcję **Wymuś nowe ustawienia** , zostaną zakończone wszystkie bieżące sesje dla wybranej puli platformy Spark. Po zakończeniu sesji należy poczekać na ponowne uruchomienie puli. 
>
> Jeśli to ustawienie nie jest zaznaczone, trzeba będzie poczekać na zakończenie bieżącej sesji platformy Spark lub zatrzymać ją ręcznie. Po zakończeniu sesji należy pozwolić na ponowne uruchomienie puli. 


## <a name="manage-a-python-wheel"></a>Zarządzanie kołem języka Python

### <a name="install-a-custom-wheel-file"></a>Instalowanie pliku kółka niestandardowego
Niestandardowe pakiety wbudowanego kółka można zainstalować w puli Apache Spark, przekazując wszystkie pliki kółka do konta Azure Data Lake Storage (Gen2), które jest połączone z obszarem roboczym Synapse. 

Pliki powinny zostać przekazane do następującej ścieżki w domyślnym kontenerze konta magazynu: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!IMPORTANT]
>Pakiety niestandardowe mogą być dodawane lub modyfikowane między sesjami. Należy jednak poczekać na ponowne uruchomienie puli i sesji, aby zobaczyć zaktualizowany pakiet.

## <a name="next-steps"></a>Następne kroki
- Wyświetlanie bibliotek domyślnych: [Obsługa wersji Apache Spark](apache-spark-version-support.md)
