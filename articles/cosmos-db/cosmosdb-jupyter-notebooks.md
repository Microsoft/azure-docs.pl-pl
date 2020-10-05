---
title: Wprowadzenie do wbudowanych notesów Jupyter w Azure Cosmos DB (wersja zapoznawcza)
description: Dowiedz się, jak używać wbudowanych notesów Jupyter w Azure Cosmos DB interaktywnie uruchamiać zapytania.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 0299ab1e76e742adb8d2653b8b5d6923aff2f11f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88588160"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Wbudowana obsługa notesów Jupyter w Azure Cosmos DB (wersja zapoznawcza)

Jupyter Notebook to aplikacja internetowa typu open source, która umożliwia tworzenie i udostępnianie dokumentów zawierających kod na żywo, równania, wizualizacje i tekst narracji. 

Azure Cosmos DB wbudowane notesy Jupyter są bezpośrednio zintegrowane z Azure Portal i kontami Azure Cosmos DB, dzięki czemu są wygodne i łatwe w użyciu. Deweloperzy, analityki danych, inżynierowie i analitycy mogą używać znanych Jupyterych notesów do eksplorowania danych, czyszczenia danych, przekształcania danych, symulacji liczbowych, modelowania statystycznego, wizualizacji danych i uczenia maszynowego.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Wizualizacje notesów Jupyter w Azure Cosmos DB":::

Azure Cosmos DB obsługuje zarówno notesy C#, jak i Python dla wszystkich interfejsów API, w tym rdzeń (SQL), Cassandra, Gremlin, Table i API dla MongoDB. Wewnątrz notesu można korzystać z wbudowanych poleceń i funkcji, które ułatwiają tworzenie Azure Cosmos DB zasobów, przekazywanie danych i wykonywanie zapytań i wizualizację danych w Azure Cosmos DB. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Wizualizacje notesów Jupyter w Azure Cosmos DB":::

## <a name="benefits-of-jupyter-notebooks"></a>Zalety notesów Jupyter

Notesy Jupyter zostały pierwotnie opracowane dla aplikacji do nauki o danych pisanych w języku Python i R. Można je jednak używać na różne sposoby dla różnych rodzajów projektów, w tym:

**Wizualizacja danych:** Notesy Jupyter umożliwiają wizualizowanie danych w postaci notesu udostępnionego, który renderuje zestaw danych jako grafikę. Możesz tworzyć wizualizacje, wprowadzać interaktywne zmiany w udostępnionym kodzie i zestawie danych oraz udostępniać wyniki.

**Udostępnianie kodu:** Usługi, takie jak GitHub, umożliwiają współużytkowanie kodu, ale są w dużym stopniu nieinteraktywny. Za pomocą Jupyter Notebook można wyświetlić kod, wykonać go i wyświetlić wyniki bezpośrednio w Azure Portal.

**Interakcje na żywo z kodem:** Kod w Jupyter Notebook jest dynamiczny; można go edytować i uruchamiać aktualizacje przyrostowo w czasie rzeczywistym. Możesz również osadzić kontrolki użytkownika (na przykład suwaki lub pola wejściowe tekstu), które są używane jako źródła danych wejściowych dla kodu, pokazów lub weryfikacji koncepcji (POCs).

**Dokumentacja przykładów kodu i wyników eksploracji danych:** Jeśli masz fragment kodu i chcesz wyjaśnić, jak to działa, możesz osadzić go w Jupyter Notebook. Możesz dodać interaktywność wraz z dokumentacją w tym samym czasie.

**Wbudowane polecenia dla Azure Cosmos DB:** Wbudowane polecenia Magic Azure Cosmos DB ułatwiają współdziałanie z Twoim kontem. Możesz użyć poleceń takich jak%% upload i%% SQL, aby przekazać dane do kontenera i wykonać zapytania przy użyciu [składni interfejsu API SQL](sql-query-getting-started.md). Nie musisz pisać dodatkowego kodu niestandardowego.

**Wszystkie w środowisku jednym miejscu:** Notesy Jupyter łączą kod, tekst sformatowany, obrazy, wideo, animacje, równania matematyczne, wykresy, mapy, interaktywne ilustracje, widżety i graficzne interfejsy użytkownika w jednym dokumencie.

## <a name="components-of-a-jupyter-notebook"></a>Składniki Jupyter Notebook

Notesy Jupyter mogą zawierać kilka typów składników, które są zorganizowane w dyskretne bloki lub komórki:

**Tekst i HTML:** Zwykły tekst lub tekst opatrzony adnotacją w składni promocji w celu wygenerowania kodu HTML, można wstawić do dokumentu w dowolnym momencie. Style CSS można także umieścić w tekście lub dodać do szablonu używanego do generowania notesu.

**Kod i dane wyjściowe:** Notesy Jupyter obsługują kod Python i C#. Wyniki wykonywanego kodu są wyświetlane bezpośrednio po blokach kodu, a bloki kodu mogą być wykonywane wiele razy w dowolnej kolejności.

**Wizualizacje:** Można generować grafiki i wykresy z kodu za pomocą modułów takich jak matplotlib, wykreolić, bokeh i inne. Podobnie jak w przypadku danych wyjściowych, wizualizacje są wyświetlane obok kodu, który go generuje. Podobnie jak w przypadku danych wyjściowych, wizualizacje są wyświetlane obok kodu, który go generuje.

**Multimedia:** Ponieważ notesy Jupyter są oparte na technologii sieci Web, mogą wyświetlać wszystkie typy multimediów obsługiwane przez stronę sieci Web. Można je uwzględnić w notesie jako elementy HTML lub można generować je programowo przy użyciu `IPython.display` modułu.

**Dane:** Możesz importować dane z kontenerów usługi Azure Cosmos lub wyników zapytań do Jupyter Notebook programowo. Użyj wbudowanych poleceń Magic do przekazywania lub wykonywania zapytań dotyczących danych w Azure Cosmos DB. 

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z wbudowanymi notesami Jupyter w Azure Cosmos DB, zobacz następujące artykuły:

* [Włącz Notesy na koncie usługi Azure Cosmos](enable-notebooks.md)
* [Korzystanie z funkcji i poleceń notesu języka Python](use-python-notebook-features-and-commands.md)
* [Korzystanie z funkcji i poleceń notesu języka C#](use-csharp-notebook-features-and-commands.md)
