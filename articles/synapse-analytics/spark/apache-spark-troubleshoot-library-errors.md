---
title: Rozwiązywanie problemów z błędami instalacji biblioteki
description: Ten samouczek zawiera omówienie sposobu rozwiązywania problemów z błędami instalacji biblioteki.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 60ea97ea2df271f867febec3fa0f0826a18dbbbf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417456"
---
# <a name="troubleshoot-library-installation-errors"></a>Rozwiązywanie problemów z błędami instalacji biblioteki 
Aby udostępnić innym firmom lub lokalnie skompilowany kod dla aplikacji, można zainstalować bibliotekę na jednym z pul Apache Spark bezserwerowych. Pakiety wymienione w pliku requirements.txt są pobierane z PyPi w momencie uruchamiania puli. Ten plik wymagań jest używany za każdym razem, gdy wystąpienie platformy Spark jest tworzone na podstawie tej puli platformy Spark. Po zainstalowaniu biblioteki dla puli platformy Spark będzie ona dostępna dla wszystkich sesji korzystających z tej samej puli. 

W niektórych przypadkach może się okazać, że biblioteka, którą próbujesz zainstalować, nie jest wyświetlana w puli Apache Spark. Ten przypadek często występuje w przypadku błędu w podanych requirements.txt lub określonych bibliotekach. Jeśli wystąpi błąd w procesie instalacji biblioteki, Pula Apache Spark zostanie przywrócona do bibliotek określonych w środowisku uruchomieniowym Synapse Base.

Celem tego dokumentu jest zapewnienie typowych problemów i ułatwienie debugowania błędów instalacji biblioteki.

## <a name="force-update-your-apache-spark-pool"></a>Wymuś aktualizację puli Apache Spark
Po zaktualizowaniu bibliotek w puli Apache Spark te zmiany zostaną pobrane po ponownym uruchomieniu puli. Jeśli masz aktywne zadania, te zadania będą nadal uruchamiane w pierwotnej wersji puli platformy Spark.

Możesz wymusić zastosowanie zmian, wybierając opcję **Wymuś nowe ustawienia**. To ustawienie spowoduje zakończenie wszystkich bieżących sesji dla wybranej puli platformy Spark. Po zakończeniu sesji należy poczekać na ponowne uruchomienie puli. 

![Dodaj biblioteki języka Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Dodaj biblioteki języka Python")

## <a name="validate-your-permissions"></a>Weryfikowanie uprawnień
Aby zainstalować i zaktualizować biblioteki, musisz mieć uprawnienia **współautora danych obiektów blob magazynu** lub **dane obiektu blob magazynu** na podstawowym koncie magazynu Azure Data Lake Storage Gen2, które jest połączone z obszarem roboczym usługi Azure Synapse Analytics.

Aby sprawdzić, czy masz te uprawnienia, możesz uruchomić następujący kod:

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Jeśli wystąpi błąd, najkorzystniej nie masz wymaganych uprawnień. Aby dowiedzieć się, jak uzyskać wymagane uprawnienia, odwiedź ten dokument: [przypisywanie współautora danych obiektów blob magazynu lub uprawnienia właściciela danych obiektu blob magazynu](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-an-azure-built-in-role).

Ponadto, jeśli używasz potoku, plik MSI obszaru roboczego musi mieć również uprawnienia współautora danych obiektu blob magazynu lub obiektu blob magazynu. Aby dowiedzieć się, jak udzielić Ci tego uprawnienia do tożsamości obszaru roboczego, odwiedź stronę: [Udziel uprawnień do tożsamości zarządzanej przez obszar roboczy](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-requirements-file"></a>Sprawdź plik wymagań
Plik ***requirements.txt*** (dane wyjściowe z polecenia pip Zablokuj) może służyć do uaktualnienia środowiska wirtualnego. Ten plik jest zgodny z formatem opisanym w dokumentacji dotyczącej [blokowania PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) .

Należy pamiętać o następujących ograniczeniach:
   -  Nazwa pakietu PyPI musi być wyświetlana wraz z dokładną wersją. 
   -  Zawartość pliku wymagań nie może zawierać dodatkowych pustych wierszy ani znaków. 
   -  [Środowisko uruchomieniowe Synapse](apache-spark-version-support.md) zawiera zestaw bibliotek, które są wstępnie zainstalowane na każdej puli Apache Spark bezserwerowej. Pakiety, które są wstępnie zainstalowane do podstawowego środowiska uruchomieniowego, nie mogą zostać obniżone. Pakiety mogą być dodawane lub uaktualniane.
   -  Zmiana wersji PySpark, Python, Scala/Java, .NET lub Spark nie jest obsługiwana.

Poniższy fragment kodu przedstawia wymagany format pliku wymagań.

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

## <a name="validate-wheel-files"></a>Weryfikuj pliki kółka
Synapse bezserwerowe pule Apache Spark są oparte na dystrybucji systemu Linux. Przy pobieraniu i instalowaniu plików kół bezpośrednio z programu PyPI należy wybrać wersję utworzoną w systemie Linux i uruchamiać ją w tej samej wersji języka Python co Pula platformy Spark.

>[!IMPORTANT]
>Pakiety niestandardowe mogą być dodawane lub modyfikowane między sesjami. Należy jednak poczekać na ponowne uruchomienie puli i sesji, aby zobaczyć zaktualizowany pakiet.

## <a name="check-for-dependency-conflicts"></a>Sprawdzanie konfliktów zależności
 Ogólnie rzecz biorąc, rozpoznawanie zależności w języku Python może być trudne do zarządzania. Aby ułatwić debugowanie konfliktów zależności lokalnie, możesz utworzyć własne środowisko wirtualne na podstawie środowiska uruchomieniowego Synapse i zweryfikować zmiany.

Aby ponownie utworzyć środowisko i zweryfikować aktualizacje:
 1. [Pobierz](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) szablon, aby lokalnie odtworzyć środowisko uruchomieniowe Synapse. Mogą występować niewielkie różnice między szablonem i rzeczywistym środowiskiem Synapse.
   
 2. Utwórz środowisko wirtualne, wykonując [poniższe instrukcje](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html). To środowisko umożliwia utworzenie izolowanej instalacji języka Python z określoną listą bibliotek. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Służy ``pip install -r <provide your req.txt file>`` do aktualizowania środowiska wirtualnego przy użyciu określonych pakietów. Jeśli instalacja powoduje błąd, może wystąpić konflikt między programem, który jest wstępnie zainstalowany w środowisku uruchomieniowym Synapse Base i co jest określone w pliku wymagań. Te konflikty zależności muszą zostać rozwiązane, aby można było pobrać zaktualizowane biblioteki w puli Apache Spark bezserwerowej.

## <a name="next-steps"></a>Następne kroki
- Wyświetlanie bibliotek domyślnych: [Obsługa wersji Apache Spark](apache-spark-version-support.md)

