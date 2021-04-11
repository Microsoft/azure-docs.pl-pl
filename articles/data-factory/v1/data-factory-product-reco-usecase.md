---
title: Data Factory przypadku użycia — zalecenia dotyczące produktu
description: Dowiedz się więcej na temat przypadku użycia zaimplementowanego przy użyciu Azure Data Factory wraz z innymi usługami.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 44c66f5fa89e7293667c930bfd1720d72dc26bf4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785074"
---
# <a name="use-case---product-recommendations"></a>Przypadek użycia — zalecenia dotyczące produktu
Azure Data Factory jest jedną z wielu usług służących do implementowania Cortana Intelligence Suite akceleratorów rozwiązań.  Aby uzyskać szczegółowe informacje o tym zestawie, zobacz stronę [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) . W tym dokumencie opisano typowy przypadek użycia, który użytkownicy platformy Azure już rozwiązany i zaimplementowali przy użyciu Azure Data Factory i innych usług składników Cortana Intelligence.

## <a name="scenario"></a>Scenariusz
Sprzedawcy detaliczni zwykle chcą zachęcić klientów do kupowania produktów przez przedstawienie im produktów, które najprawdopodobniej interesują się, i z tego powodu najprawdopodobniej kupimy. Aby to osiągnąć, detaliczne sprzedawcy muszą dostosować środowisko online użytkownika, korzystając z spersonalizowanych zaleceń dla danego użytkownika. Te spersonalizowane zalecenia są podejmowane w oparciu o bieżące i historyczne dane dotyczące zachowań zakupów, informacje o produktach, nowo wprowadzone marki oraz dane dotyczące produktów i segmentacji klientów.  Ponadto mogą zapewnić zalecenia dotyczące produktu użytkownika w oparciu o analizę ogólnego zachowania użytkowania ze wszystkich użytkowników połączonych.

Celem tych sprzedawców detalicznych jest zoptymalizowanie pod kątem konwersji typu "kliknięcie do sprzedaży" i zdobycie większego przychodu sprzedaży.  Osiągają one tę konwersję, dostarczając kontekstowe zalecenia dotyczące produktu oparte na zachowaniu w zależności od zainteresowań i akcji klienta. W przypadku tego przypadku użycia detalicznych detalistów używamy jako przykładu firm, które chcą zoptymalizować dla swoich klientów. Jednak te zasady mają zastosowanie do wszystkich firm, które chcą angażować swoich klientów w swoje rzeczy i usługi, a także zwiększyć możliwości kupowania klientów przy użyciu spersonalizowanych zaleceń dotyczących produktów.

## <a name="challenges"></a>Wyzwania
Istnieje wiele wyzwań, które są dostępne dla detalistów internetowych podczas próby wdrożenia tego typu przypadku użycia. 

Najpierw dane o różnych rozmiarach i kształtach muszą być pozyskiwane z wielu źródeł danych, zarówno lokalnych, jak i w chmurze. Te dane obejmują dane produktu, historyczne dane dotyczące zachowań klienta oraz dane użytkowników, gdy użytkownik przegląda witrynę sieci sprzedaży online. 

Druga, spersonalizowane zalecenia dotyczące produktu muszą być odpowiednio i dokładne, obliczane i przewidywalne. Oprócz zachowania produktu, marki i klienta oraz danych przeglądarki, sprzedawcy online muszą również uwzględniać opinie klientów na temat zakupów w przeszłości, aby wyznaczyć najlepsze zalecenia dotyczące produktu dla użytkownika. 

Po trzecie zalecenia muszą mieć natychmiastowy dostęp do użytkownika w celu zapewnienia bezproblemowego przeglądania i kupowania oraz zapewnienia najnowszych i odpowiednich zaleceń. 

Poza tym detaliści muszą mierzyć skuteczność podejścia przez śledzenie całkowitej liczby sukcesów sprzedaży do samosprzedaży i rozliczeń, które można zastosować, i dostosować je do przyszłych zaleceń.

## <a name="solution-overview"></a>Omówienie rozwiązania
Ten przykładowy przypadek użycia został rozwiązany i wdrożony przez prawdziwych użytkowników platformy Azure przy użyciu Azure Data Factory i innych usług składników Cortana Intelligence, w tym [HDInsight](https://azure.microsoft.com/services/hdinsight/) i [Power BI](https://powerbi.microsoft.com/).

Detaliczny detalista używa magazynu obiektów blob platformy Azure, lokalnego SQL Server, Azure SQL Database i składni danych relacyjnych jako opcji przechowywania danych w ramach przepływu pracy.  Magazyn obiektów BLOB zawiera informacje o klientach, dane dotyczące zachowania klienta i dane o produkcie. Dane o produkcie zawierają informacje o marce produktu i katalog produktów przechowywane lokalnie w usłudze Azure Synapse Analytics. 

Wszystkie dane są łączone i przekazywane do systemu rekomendacji produktów w celu dostarczenia spersonalizowanych zaleceń w oparciu o zainteresowania i akcje klienta, podczas gdy użytkownik przegląda produkty w katalogu w witrynie sieci Web. Klienci widzą również produkty, które są związane z tym produktem, na podstawie ogólnych wzorców użycia witryny sieci Web, które nie są powiązane z żadnym użytkownikiem.

![Diagram przypadków użycia](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabajty nieprzetworzonych plików dziennika sieci Web są generowane codziennie od witryny internetowej sprzedawcy detalicznego w postaci plików z częściową strukturą. Pierwotne pliki dziennika sieci Web oraz informacje o klientach i katalogach produktów są regularnie pozyskiwane w usłudze Azure Blob Storage za pomocą Data Factory globalnie wdrożonych przepływów danych jako usługi. Surowe pliki dziennika dla tego dnia są podzielone na partycje (według roku i miesiąca) w usłudze BLOB Storage w celu przechowywania długoterminowego.  [Usługa Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) służy do partycjonowania nieprzetworzonych plików dziennika w magazynie obiektów blob i przetwarzania pozyskanych dzienników na dużą skalę przy użyciu skryptów Hive i świńskiej. Podzielone na partycje dane dzienników sieci Web są następnie przetwarzane w celu wyodrębnienia wymaganych wejść dla systemu rekomendacji usługi Machine Learning w celu wygenerowania spersonalizowanych zaleceń dotyczących produktów.

System rekomendacji używany na potrzeby uczenia maszynowego w tym przykładzie jest platformą rekomendacji "Open Source Machine Learning" z usługi [Apache Mahout](https://mahout.apache.org/).  Do scenariusza można zastosować dowolny [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) lub niestandardowy model.  Model Mahout służy do przewidywania podobieństwa między elementami w witrynie sieci Web na podstawie ogólnych wzorców użycia oraz do generowania spersonalizowanych rekomendacji na podstawie poszczególnych użytkowników.

Na koniec zestaw wyników spersonalizowanych rekomendacji dotyczących produktów jest przenoszony do składni danych relacyjnych w celu użycia przez witrynę sieci Web sprzedawcy detalicznego.  Zestaw wyników może być również dostępny bezpośrednio z usługi BLOB Storage przez inną aplikację lub przeniesiony do dodatkowych magazynów dla innych odbiorców i przypadków użycia.

## <a name="benefits"></a>Korzyści
Przez optymalizację strategii zaleceń dotyczących produktów i dostosowanie jej do celów gospodarczych, rozwiązanie spełnia cele marketingowe i docelowe sprzedaży w trybie online. Ponadto mogli operacjonalizować i zarządzać przepływem pracy zaleceń dotyczących produktu w sposób efektywny, niezawodny i ekonomiczny. Podejście ułatwiające aktualizowanie modelu i dostosowanie jego skuteczności w oparciu o miary sukcesów operacji kliknięcia i konwersji sprzedaży. Korzystając z Azure Data Factory, mogli porzucić czasochłonne i kosztowne ręczne zarządzanie zasobami w chmurze oraz przejść na żądanie zarządzania zasobami w chmurze. W związku z tym mogły zaoszczędzić czas, pieniądze i skrócić czas wdrożenia rozwiązania. Widoki i kondycja usługi operacyjnej danych były łatwe do wizualizacji i rozwiązywania problemów przy użyciu intuicyjnego interfejsu użytkownika Data Factory monitorowania i zarządzania dostępnego z Azure Portal. Rozwiązanie może być teraz zaplanowane i zarządzane, aby gotowe dane były niezawodne i dostarczane do użytkowników, a zależności między danymi i przetwarzaniem są automatycznie zarządzane bez interwencji człowieka.

Dzięki udostępnieniu tego spersonalizowanego środowiska sprzedaży detalicznej sprzedaż w trybie online stworzyła bardziej konkurencyjne, atrakcyjne środowisko klienta i w związku z tym zwiększają sprzedaż i ogólną zadowolenie klientów.

