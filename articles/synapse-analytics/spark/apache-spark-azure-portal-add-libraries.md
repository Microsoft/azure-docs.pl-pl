---
title: Dodawanie bibliotek dla platformy Apache Spark i zarządzanie nimi w usłudze Azure Synapse Analytics
description: Dowiedz się, jak dodawać biblioteki używane przez apache spark i zarządzać nimi w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 80414ccd6d5797614dd15bd61af8f37b3d2be05c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870367"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Dodawanie bibliotek dla platformy Apache Spark i zarządzanie nimi w usłudze Azure Synapse Analytics

Apache Spark zależy od wielu bibliotek, aby zapewnić funkcjonalność. Biblioteki te można rozszerzyć lub zastąpić dodatkowymi bibliotekami lub zaktualizowanymi wersjami starszych.

Pakiety języka Python można dodawać na poziomie puli platformy Spark (wersja zapoznawcza), a pakiety oparte na plikach .jar mogą być dodawane na poziomie definicji zadania platformy Spark.

## <a name="adding-or-updating-python-libraries"></a>Dodawanie lub aktualizowanie bibliotek języka Python

Apache Spark w usłudze Azure Synapse Analytics ma pełną instalację Anacondas oraz dodatkowe biblioteki. Pełna lista bibliotek można znaleźć w [obsłudze wersji Apache Spark](apache-spark-version-support.md).

Po uruchomieniu spark wystąpienie, nowe środowisko wirtualne jest tworzony przy użyciu tej instalacji jako podstawy. Ponadto plik *requirements.txt* (dane wyjściowe z `pip freeze` polecenia) może służyć do uaktualniania środowiska wirtualnego. Pakiety wymienione w tym pliku do zainstalowania lub uaktualnienia są pobierane z PyPi w momencie uruchamiania klastra. Ten plik wymagań jest używany za każdym razem, gdy wystąpienie platformy Spark jest tworzone z tej puli platformy Spark.

> [!IMPORTANT]
>
> - Jeśli pakiet, który instalujesz jest duży lub zajmuje dużo czasu, aby zainstalować, ma to wpływ na czas uruchamiania wystąpienia platformy Spark.
> - Pakiety, które wymagają obsługi kompilatora w czasie instalacji, takich jak GCC, nie są obsługiwane.
> - Pakietów nie można obniżać, tylko dodawać ani uaktualniać.

### <a name="requirements-format"></a>Format wymagań

Poniższy fragment kodu pokazuje format pliku wymagań. Nazwa pakietu PyPi jest wyświetlana wraz z dokładną wersją. Ten plik jest zgodny z formatem opisanym w dokumentacji referencyjnej [pip freeze.](https://pip.pypa.io/en/stable/reference/pip_freeze/) W tym przykładzie przypina określoną wersję. Można również określić "nie większe niż" i "mniej niż" wersje w tym pliku.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Interfejs użytkownika biblioteki Języka Python

Interfejs użytkownika dodawania bibliotek znajduje się na karcie **Ustawienia dodatkowe** na stronie puli Tworzenie platformy **Spark apache** w witrynie Azure portal.

Przekaż plik konfiguracji środowiska za pomocą selektora plików w sekcji **Pakiety** na stronie.

![Dodawanie bibliotek języka Python](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Dodawanie bibliotek języka Python")

### <a name="verifying-installed-libraries"></a>Weryfikowanie zainstalowanych bibliotek

Aby sprawdzić, czy są zainstalowane poprawne wersje poprawnych bibliotek, uruchom następujący kod

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Następne kroki

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark Dokumentacja](https://spark.apache.org/docs/2.4.4/)
