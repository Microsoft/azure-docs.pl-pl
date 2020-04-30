---
title: Wprowadzenie do wbudowanych notesów Jupyter w Azure Cosmos DB (wersja zapoznawcza)
description: Dowiedz się, jak używać wbudowanych notesów Jupyter w Azure Cosmos DB interaktywnie uruchamiać zapytania.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5b320485001d6cbc457d39ef193ed8c57f7161df
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76760287"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Wbudowana obsługa notesów Jupyter w Azure Cosmos DB (wersja zapoznawcza)

Jupyter Notes to aplikacja sieci Web Open Source, która umożliwia tworzenie i udostępnianie dokumentów zawierających kod na żywo, równania, wizualizacje i tekst opisowy. Azure Cosmos DB obsługuje wbudowane notesy Jupyter dla wszystkich interfejsów API, takich jak Cassandra, MongoDB, SQL, Gremlin i Table. Wbudowana obsługa notesów dla wszystkich Azure Cosmos DB interfejsów API i modeli danych pozwala interaktywnie uruchamiać zapytania. Notesy Jupyter są uruchamiane na kontach usługi Azure Cosmos i umożliwiają deweloperom wykonywanie eksploracji danych, czyszczenie danych, Przekształcanie danych, symulacje liczbowe, modelowanie statystyczne, wizualizację danych i uczenie maszynowe.

![Wizualizacje notesów Jupyter w Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

Notesy Jupyter obsługują funkcje Magic, które zwiększają możliwości jądra dzięki obsłudze dodatkowych poleceń. Cosmos Magic to polecenie, które rozszerza możliwości jądra języka Python w notesie Jupyter, dzięki czemu można uruchamiać zapytania interfejsu API usługi Azure Cosmos SQL oprócz Apache Spark. Za pomocą zaawansowanych bibliotek wizualizacji, które są zintegrowane z poleceniami renderowania, można łatwo łączyć zapytania języka Python i interfejsu API SQL w celu wykonywania zapytań i wizualizacji danych.
Azure Portal natywnie integruje środowisko notesu Jupyter z kontami usługi Azure Cosmos, jak pokazano na poniższej ilustracji:

![Obsługa notesów Jupyter w Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Zalety notesów Jupyter

Notesy Jupyter zostały pierwotnie opracowane dla aplikacji do nauki o danych pisanych w języku Python, R. Można je jednak używać na różne sposoby dla różnych rodzajów projektów, takich jak:

* ***Wizualizacje danych:** Notesy Jupyter umożliwiają wizualizowanie danych w postaci notesu udostępnionego, który renderuje zestaw danych jako grafikę. Notes Jupyter umożliwia tworzenie wizualizacji, udostępnianie ich i Zezwalanie na interaktywne wprowadzanie zmian w udostępnionym kodzie i zestawie danych.

* **Udostępnianie kodu:** Usługi, takie jak GitHub, umożliwiają współużytkowanie kodu, ale są w dużym stopniu nieinteraktywny. Za pomocą notesu Jupyter można wyświetlić kod, wykonać go i wyświetlić wyniki bezpośrednio w Azure Portal.

* **Interakcje na żywo z kodem:** Kod notesu Jupyter jest dynamiczny; można go edytować i uruchamiać przyrostowo w czasie rzeczywistym. Notesy mogą również osadzać formanty użytkownika (np. suwaki lub pola wejściowe tekstu), które mogą być używane jako źródła danych wejściowych dla kodu, demonstracji lub weryfikacji koncepcji (POCs).

* **Dokumentacja przykładów kodu i wyników eksploracji danych:** Jeśli masz fragment kodu i chcesz wyjaśnić wiersz po wierszu, jak to działa w Azure Cosmos DB, w czasie rzeczywistym, możesz osadzić go w Jupyter Notebooku. Kod pozostanie w pełni funkcjonalny. Możesz dodać interaktywność wraz z dokumentacją w tym samym czasie.

* **Polecenia Cosmos Magic:** W notesach Jupyter można użyć niestandardowych poleceń Magic dla Azure Cosmos DB, aby ułatwić wykonywanie interaktywnych danych. Na przykład, Magic%% SQL, który umożliwia jeden do zapytania kontenera Cosmos przy użyciu interfejsu API SQL bezpośrednio w notesie.

* **Wszystkie w środowisku jednym miejscu:** Notesy Jupyter łączą kod, tekst sformatowany, obrazy, wideo, animacje, równania matematyczne, wykresy, mapy, interaktywne ilustracje, widżety i graficzne interfejsy użytkownika w jednym dokumencie.

## <a name="components-of-a-jupyter-notebook"></a>Składniki notesu Jupyter

Notesy Jupyter mogą zawierać kilka typów składników, które są zorganizowane w bloki dyskretne:

* **Tekst i HTML:** Zwykły tekst lub tekst opatrzony adnotacją w składni promocji w celu wygenerowania kodu HTML, można wstawić do dokumentu w dowolnym momencie. Style CSS można także umieścić w tekście lub dodać do szablonu używanego do generowania notesu.

* **Kod i dane wyjściowe:** Notesy Jupyter obsługują kod języka Python. Wyniki wykonywanego kodu są wyświetlane bezpośrednio po blokach kodu, a bloki kodu mogą być wykonywane wiele razy w dowolnej kolejności.

* **Wizualizacje:** Grafiki i wykresy można generować z kodu przy użyciu modułów takich jak matplotlib, wykreolić lub bokeh. Podobnie jak w przypadku danych wyjściowych, wizualizacje są wyświetlane obok kodu, który go generuje.

* **Multimedia:** Ponieważ Notes Jupyter jest oparty na technologii sieci Web, można wyświetlić wszystkie typy multimediów obsługiwane na stronie sieci Web. Można je uwzględnić w notesie jako elementy HTML lub można generować je programowo przy użyciu `IPython.display` modułu.

* **Dane:** Dane z kontenerów usługi Azure Cosmos i wyniki zapytań można programowo zaimportować do notesu Jupyter. Na przykład przez dołączenie kodu w notesie do wykonywania zapytań dotyczących danych przy użyciu dowolnego z Cosmos DB interfejsów API lub wbudowanej natywnie Apache Spark.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z wbudowanymi notesami Jupyter w Azure Cosmos DB Zobacz następujące artykuły:

* [Włącz Notesy na koncie usługi Azure Cosmos](enable-notebooks.md)
* [Korzystanie z funkcji i poleceń notesu](use-notebook-features-and-commands.md)



