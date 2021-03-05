---
title: Zarządzanie pakietami
description: Dowiedz się, jak dodawać biblioteki używane przez Apache Spark w usłudze Azure Synapse Analytics i zarządzać nimi.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c6d720c3feec29eb32b1cfa9c31ea45839c98ec7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176420"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Zarządzanie bibliotekami Apache Spark w usłudze Azure Synapse Analytics
Biblioteki zapewniają kod wielokrotnego użytku, który może być dołączany do programów lub projektów. 

Może być konieczne zaktualizowanie środowiska puli Apache Spark bezserwerowej z różnych powodów. Na przykład może się okazać, że:
- jedno z podstawowych zależności wydano nową wersję.
- potrzebujesz dodatkowego pakietu do uczenia modelu uczenia maszynowego lub przygotowywania danych.
- znaleziono lepszy pakiet i nie jest już potrzebny starszy pakiet.
- Twój zespół utworzył niestandardowy pakiet, który jest wymagany w puli Apache Spark.

Aby udostępnić innym firmom lub lokalnie skompilowany kod dla aplikacji, możesz zainstalować bibliotekę na jednej z Apache Spark pul lub sesji notesu.
  
## <a name="default-installation"></a>Instalacja domyślna
Apache Spark w usłudze Azure Synapse Analytics ma kompletną instalację Anacondas plus dodatkowe biblioteki. Listę pełnych bibliotek można znaleźć w obszarze [Obsługa wersji Apache Spark](apache-spark-version-support.md). 

Po uruchomieniu wystąpienia platformy Spark te biblioteki zostaną automatycznie uwzględnione. Dodatkowe pakiety można dodać na poziomie puli lub poziomu sesji platformy Spark.

## <a name="workspace-packages"></a>Pakiety obszaru roboczego
Podczas tworzenia niestandardowych aplikacji lub modeli zespół może opracowywać różne artefakty kodu, takie jak pliki kółka lub jar, aby spakować swój kod. 

W programie Synapse pakiety obszaru roboczego mogą być plikami niestandardowymi lub prywatnymi lub jar. Te pakiety można przekazać do obszaru roboczego, a następnie przypisać je do określonej puli platformy Spark. Po przypisaniu te pakiety obszaru roboczego są automatycznie instalowane na wszystkich sesjach puli platformy Spark.

Aby dowiedzieć się więcej na temat zarządzania bibliotekami obszarów roboczych, odwiedź następujące przewodniki:

- [Pakiety obszarów roboczych języka Python (wersja zapoznawcza): ](./apache-spark-manage-python-packages.md#install-wheel-files) Przekaż pliki kółka języka Python jako pakiet obszaru roboczego, a następnie Dodaj te pakiety do określonych bezserwerowych pul Apache Spark.
- [Pakiety robocze Scala/Java (wersja zapoznawcza): ](./apache-spark-manage-scala-packages.md#workspace-packages) Przekaż pliki jar Scala i Java jako pakiet obszaru roboczego, a następnie Dodaj te pakiety do określonych bezserwerowych pul Apache Spark.

## <a name="pool-packages"></a>Pakiety puli
W niektórych przypadkach może być konieczne ujednolicenie zestawu pakietów, które są używane w danej puli Apache Spark. Ta standaryzacja może być przydatna, jeśli te same pakiety są powszechnie instalowane przez wiele osób w zespole. 

Korzystając z możliwości zarządzania pulą usługi Azure Synapse Analytics, można skonfigurować domyślny zestaw bibliotek, które mają być zainstalowane w danej puli bezserwerowej Apache Spark. Te biblioteki są instalowane na [podstawie podstawowego środowiska uruchomieniowego](./apache-spark-version-support.md). 

Obecnie zarządzanie pulą jest obsługiwane tylko dla języka Python. W przypadku języka Python Synapse pule Spark używają Conda do instalowania zależności pakietów języka Python i zarządzania nimi. Podczas określania bibliotek na poziomie puli można teraz dostarczyć requirements.txt lub Environment. yml. Ten plik konfiguracji środowiska jest używany za każdym razem, gdy wystąpienie platformy Spark jest tworzone na podstawie tej puli platformy Spark. 

Aby dowiedzieć się więcej na temat tych funkcji, zapoznaj się z dokumentacją w temacie [Zarządzanie pulą](./apache-spark-manage-python-packages.md#pool-libraries)w języku Python.

> [!IMPORTANT]
> - Jeśli instalowany pakiet jest duży lub zajmuje dużo czasu, ma to wpływ na czas uruchamiania wystąpienia platformy Spark.
> - Zmiana wersji PySpark, Python, Scala/Java, .NET lub Spark nie jest obsługiwana.
> - Instalowanie pakietów z PyPI nie jest obsługiwane w obszarach roboczych z obsługą programu DEP.

## <a name="session-scoped-packages"></a>Pakiety z zakresem sesji
Często podczas przeprowadzania interaktywnej analizy danych lub uczenia maszynowego może się okazać, że chcesz wypróbować nowsze pakiety lub pakiety, które nie są jeszcze dostępne w puli Apache Spark. Zamiast aktualizować konfigurację puli, użytkownicy mogą teraz używać pakietów z zakresem sesji w celu dodawania i aktualizowania zależności sesji oraz zarządzania nimi.

Pakiety z zakresem sesji umożliwiają użytkownikom Definiowanie zależności pakietu na początku sesji. W przypadku instalowania pakietu z zakresem sesji tylko bieżąca sesja ma dostęp do określonych pakietów. W efekcie te pakiety z zakresem sesji nie wpłyną na inne sesje ani zadania korzystające z tej samej puli Apache Spark. Ponadto te biblioteki są instalowane w oparciu o podstawowe środowisko uruchomieniowe i pakiety poziomów puli. 

Te pakiety są automatycznie dodawane do środowiska języka Python. Pakiety nie mogą być wymienione w pliku *requirements.txt* .

Należy zauważyć, że ta metoda obecnie suports tylko `*.whl` pliki. Nie dodawaj żadnych `*.tar.gz` plików do kontenera.

Aby dowiedzieć się więcej na temat zarządzania pakietami z zakresem sesji, odwiedź następujące przewodniki:

- [Pakiety sesji języka Python (wersja zapoznawcza):](./apache-spark-manage-python-packages.md) Na początku sesji Podaj *środowisko Conda. yml* , aby zainstalować dodatkowe pakiety Python ze popularnych repozytoriów. 
- [Pakiety sesji Scala/Java: ](./apache-spark-manage-scala-packages.md) Na początku sesji Podaj listę plików jar do zainstalowania przy użyciu programu `%%configure` .

## <a name="next-steps"></a>Następne kroki
- Wyświetlanie bibliotek domyślnych: [Obsługa wersji Apache Spark](apache-spark-version-support.md)
