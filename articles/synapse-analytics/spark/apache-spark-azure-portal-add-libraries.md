---
title: Dodawanie bibliotek Apache Spark i zarządzanie nimi w usłudze Azure Synapse Analytics
description: Dowiedz się, jak dodawać biblioteki używane przez Apache Spark w usłudze Azure Synapse Analytics i zarządzać nimi.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 60cbb269693b23b658b8e28f90383814d6657661
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194335"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Dodawanie bibliotek Apache Spark i zarządzanie nimi w usłudze Azure Synapse Analytics

Apache Spark zależy od wielu bibliotek, aby zapewnić funkcjonalność. Te biblioteki można rozszerzyć lub wymienić na dodatkowe biblioteki lub zaktualizowane wersje starszych wersji.

Pakiety języka Python można dodać na poziomie puli Spark (wersja zapoznawcza), a pakiety oparte na formacie jar można dodać na poziomie definicji zadania platformy Spark.

## <a name="adding-or-updating-python-libraries"></a>Dodawanie lub aktualizowanie bibliotek języka Python

Apache Spark w usłudze Azure Synapse Analytics ma kompletną instalację Anacondas plus dodatkowe biblioteki. Listę pełnych bibliotek można znaleźć w obszarze [Obsługa wersji Apache Spark](apache-spark-version-support.md).

Po uruchomieniu wystąpienia platformy Spark nowe środowisko wirtualne jest tworzone przy użyciu tej instalacji jako podstawy. Ponadto plik *requirements.txt* (dane wyjściowe `pip freeze` polecenia) może służyć do uaktualnienia środowiska wirtualnego. Pakiety wymienione w tym pliku do zainstalowania lub uaktualnienia są pobierane z PyPi w momencie uruchomienia klastra. Ten plik wymagań jest używany za każdym razem, gdy wystąpienie platformy Spark jest tworzone na podstawie tej puli platformy Spark.

> [!IMPORTANT]
>
> - Jeśli instalowany pakiet jest duży lub zajmuje dużo czasu, ma to wpływ na czas uruchamiania wystąpienia platformy Spark.
> - Pakiety, które wymagają obsługi kompilatora w czasie instalacji, takie jak w przypadku programu, nie są obsługiwane.
> - Pakiety nie mogą być obniżane, tylko dodawane lub uaktualniane.

### <a name="requirements-format"></a>Format wymagań

Poniższy fragment kodu przedstawia format pliku wymagań. Nazwa pakietu PyPi jest wyświetlana wraz z dokładną wersją. Ten plik jest zgodny z formatem opisanym w dokumentacji dotyczącej [blokowania PIP](https://pip.pypa.io/en/stable/reference/pip_freeze/) . Ten przykład przypina określoną wersję. W tym pliku można także określić wersje "nie większe niż" i "mniejsze niż".

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Interfejs użytkownika biblioteki języka Python

Interfejs użytkownika służący do dodawania bibliotek znajduje się na karcie **Ustawienia dodatkowe** na stronie **Tworzenie puli Apache Spark** na Azure Portal.

Przekaż plik konfiguracyjny środowiska przy użyciu selektora plików w sekcji **pakiety** na stronie.

![Dodaj biblioteki języka Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Dodaj biblioteki języka Python")

### <a name="verifying-installed-libraries"></a>Weryfikowanie zainstalowanych bibliotek

Aby sprawdzić, czy są zainstalowane poprawne wersje odpowiednich bibliotek, uruchom następujący kod

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Dokumentacja Apache Spark](https://spark.apache.org/docs/2.4.4/)
