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
ms.openlocfilehash: 006abf62c605c2ca34fd1adeadee8e29ae0fb8fb
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588313"
---
# <a name="troubleshoot-library-installation-errors"></a>Rozwiązywanie problemów z błędami instalacji biblioteki 
Aby udostępnić aplikacjom kod innej firmy lub lokalnie, możesz zainstalować bibliotekę w jednej z bez serwera Apache Spark pul. Pakiety wymienione w pliku requirements.txt są pobierane z pliku PyPi podczas uruchamiania puli. Ten plik wymagań jest używany za każdym razem, gdy na podstawie tej puli platformy Spark zostanie utworzone wystąpienie platformy Spark. Po zainstalowaniu biblioteki dla puli platformy Spark jest ona dostępna dla wszystkich sesji korzystających z tej samej puli. 

W niektórych przypadkach może się okazać, że biblioteka nie jest wyświetlana w Apache Spark puli. Ten przypadek często występuje, gdy występuje błąd w podanej requirements.txt lub określonych bibliotekach. Gdy w procesie instalacji biblioteki wystąpi błąd, pula Apache Spark zostanie przywrócona do bibliotek określonych w bazowym środowisku uruchomieniowym usługi Synapse.

Celem tego dokumentu jest zapewnienie typowych problemów i pomoc w debugowaniu błędów instalacji biblioteki.

## <a name="force-update-your-apache-spark-pool"></a>Wymuszanie aktualizacji Apache Spark puli
Po zaktualizowaniu bibliotek w puli Apache Spark zmiany te zostaną odebrane po ponownym uruchomieniu puli. Jeśli masz aktywne zadania, te zadania będą nadal działać w oryginalnej wersji puli spark.

Możesz wymusić zastosowanie zmian, wybierając opcję Wymusz **nowe ustawienia.** To ustawienie spowoduje zakończenie wszystkich bieżących sesji dla wybranej puli platformy Spark. Po zakończeniu sesji musisz poczekać na ponowne uruchomienie puli. 

![Dodawanie bibliotek języka Python](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Dodawanie bibliotek języka Python")

## <a name="track-installation-progress"></a>Śledzenie postępu instalacji
Zadanie platformy Spark zarezerwowane dla systemu jest rozpoczynane za każdym razem, gdy pula jest aktualizowana przy użyciu nowego zestawu bibliotek. To zadanie platformy Spark pomaga monitorować stan instalacji biblioteki. Jeśli instalacja nie powiedzie się z powodu konfliktów biblioteki lub innych problemów, pula platformy Spark powróci do stanu poprzedniego lub domyślnego. 

Ponadto użytkownicy mogą również sprawdzać dzienniki instalacji, aby zidentyfikować konflikty zależności lub zobaczyć, które biblioteki zostały zainstalowane podczas aktualizacji puli.

Aby wyświetlić te dzienniki:
1. Przejdź do listy aplikacji platformy Spark na **karcie** Monitorowanie. 
2. Wybierz systemowe zadanie aplikacji Spark odpowiadające aktualizacji puli. Te zadania systemowe są uruchamiane pod *tytułem SystemReservedJob-LibraryManagement.*
   ![Zrzut ekranu przedstawiający zadanie biblioteki zarezerwowanej systemu.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Wyświetlanie zadania biblioteki systemowej")
3. Przełącz się, aby **wyświetlić dzienniki** sterownika **i stdout.** 
4. W wynikach zobaczysz dzienniki związane z instalacją pakietów.
    ![Zrzut ekranu przedstawiający wyniki zadania biblioteki zarezerwowanej systemu.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Wyświetlanie postępu zadania biblioteki systemowej")

## <a name="validate-your-permissions"></a>Weryfikowanie uprawnień
Aby zainstalować i zaktualizować biblioteki, musisz mieć uprawnienia Współautor danych obiektu blob usługi **Storage** lub Właściciel danych obiektu blob usługi **Storage** na podstawowym koncie usługi Azure Data Lake Storage Gen2 Storage połączonym z Azure Synapse Analytics roboczym.

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
Jeśli wystąpi błąd, prawdopodobnie brakuje wymaganych uprawnień. Aby dowiedzieć się, jak uzyskać wymagane uprawnienia, zapoznaj się z dokumentem: Assign Storage Blob Data Contributor or Storage Blob Data Owner permissions (Przypisywanie współautora danych obiektu blob magazynu lub właściciela danych [obiektu blob usługi Storage).](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)

Ponadto jeśli uruchamiasz potok, to usługa MSI obszaru roboczego musi mieć również uprawnienia Właściciel danych obiektu blob magazynu lub Współautor danych obiektu blob magazynu. Aby dowiedzieć się, jak udzielić tożsamości obszaru roboczego tego uprawnienia, odwiedź stronę: [Udzielanie uprawnień tożsamości zarządzanej obszaru roboczego.](../security/how-to-grant-workspace-managed-identity-permissions.md)

## <a name="check-the-environment-configuration-file"></a>Sprawdzanie pliku konfiguracji środowiska
Plik konfiguracji środowiska może służyć do uaktualniania środowiska Conda. Te dopuszczalne formaty plików do zarządzania pulą języka Python są wymienione [tutaj.](./apache-spark-manage-python-packages.md)

Należy pamiętać o następujących ograniczeniach:
   -  Zawartość pliku wymagań nie może zawierać dodatkowych pustych wierszy ani znaków. 
   -  Środowisko [uruchomieniowe usługi Synapse](apache-spark-version-support.md) zawiera zestaw bibliotek, które są wstępnie zainstalowane w każdej bez serwera Apache Spark puli. Pakietów wstępnie zainstalowanych w bazowym środowisku uruchomieniowym nie można usunąć ani odinstalować.
   -  Zmiana wersji PySpark, Python, Scala/Java, .NET lub Spark nie jest obsługiwana.
   -  Biblioteki o zakresie sesji języka Python akceptują tylko pliki z rozszerzeniem YML.

## <a name="validate-wheel-files"></a>Weryfikowanie plików wheel
Pule aplikacji Apache Spark Synapse są oparte na dystrybucji systemu Linux. Podczas pobierania i instalowania plików Wheel bezpośrednio z pliku PyPI należy wybrać wersję, która jest zbudowana w systemie Linux i działa w tej samej wersji języka Python co pula spark.

>[!IMPORTANT]
>Pakiety niestandardowe można dodawać lub modyfikować między sesjami. Należy jednak poczekać na ponowne uruchomienie puli i sesji, aby zobaczyć zaktualizowany pakiet.

## <a name="check-for-dependency-conflicts"></a>Sprawdzanie konfliktów zależności
 Ogólnie rzecz biorąc, rozwiązanie zależności języka Python może być trudne do zarządzania. Aby ułatwić lokalne debugowanie konfliktów zależności, możesz utworzyć własne środowisko wirtualne na podstawie środowiska uruchomieniowego usługi Synapse i zweryfikować zmiany.

Aby ponownie utworzyć środowisko i zweryfikować aktualizacje:
 1. [Pobierz](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) szablon, aby lokalnie odtworzyć środowisko uruchomieniowe usługi Synapse. Mogą wystąpić niewielkie różnice między szablonem a rzeczywistym środowiskiem usługi Synapse.
   
 2. Utwórz środowisko wirtualne, korzystając z [poniższych instrukcji.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment) To środowisko umożliwia utworzenie izolowanej instalacji języka Python z określoną listą bibliotek. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Użyj ``pip install -r <provide your req.txt file>`` , aby zaktualizować środowisko wirtualne przy użyciu określonych pakietów. Jeśli instalacja powoduje błąd, może wystąpić konflikt między tym, co jest wstępnie zainstalowane w bazowym środowisku uruchomieniowym usługi Synapse, a tym, co jest określone w podanym pliku wymagań. Te konflikty zależności muszą zostać rozwiązane w celu uzyskania zaktualizowanych bibliotek w puli Apache Spark serwera.

>[!IMPORTANT]
>Problemy mogą być arrise podczas używania narzędzia pip i środowiska conda razem. W przypadku łączenia pip i conda najlepiej postępować zgodnie z tymi [zalecanymi najlepszymi rozwiązaniami.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)

## <a name="next-steps"></a>Następne kroki
- Wyświetlanie bibliotek domyślnych: [obsługa Apache Spark wersji](apache-spark-version-support.md)
