---
title: Zrozumienie biznesowe w procesie nauki danych zespołu
description: Cele, zadania i elementy dostarczane na potrzeby rozwoju biznesowego dla projektów analizy danych w procesie nauki o danych zespołowych.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76710333"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Informacje o firmie dotyczące etapów cyklu życia zespołu ds. analizy danych

W tym artykule opisano cele, zadania i elementy dostarczane powiązane z etapem poświęconym zrozumieniu biznesowym procesu nauki o danych zespołowych (przetwarzania TDSP). Ten proces zapewnia Zalecany cykl życia, którego można użyć do struktury projektów analizy danych. Cykl życia przedstawia główne etapy, które są zwykle wykonywane przez projekty, często iteracyjnie:

   1. **Poznawanie firmy**
   2. **Pozyskiwanie danych i ich analiza**
   3. **Modelowanie**
   4. **Wdrożenie**
   5. **Akceptacja klienta**

Oto wizualna reprezentacja cyklu życia przetwarzania TDSP: 

![Cykl życia przetwarzania TDSP](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Cele
* Określ zmienne kluczowe, które mają służyć jako elementy docelowe modelu i których powiązane metryki są używane, określają powodzenie projektu.
* Zidentyfikuj odpowiednie źródła danych, do których firma ma dostęp, lub którego ma dotyczyć.

## <a name="how-to-do-it"></a>Jak to zrobić
W tym etapie są uwzględnione dwa podstawowe zadania: 

   * **Zdefiniuj cele**: Współpracuj z klientem i innymi uczestnikami projektu, aby zrozumieć i zidentyfikować problemy biznesowe. Formułowanie pytań, które definiują cele biznesowe, które mogą być ukierunkowane przez techniki analizy danych.
   * **Identyfikowanie źródeł danych**: Znajdź odpowiednie dane, które pomagają odpowiedzieć na pytania, które definiują cele projektu.

### <a name="define-objectives"></a>Zdefiniuj cele
1. Centralnym celem tego kroku jest zidentyfikowanie kluczowych zmiennych firmowych, które muszą być przewidywane w analizie. Nazywamy te zmienne jako *elementy docelowe modelu*i używamy metryk skojarzonych z nimi w celu ustalenia sukcesu projektu. Dwa przykłady takich celów to prognozy sprzedaży lub prawdopodobieństwo, że porządek jest fałszywy.

2. Zdefiniuj cele projektu przez zaproszenie i uściślenie pytań "Ostre", które są istotne, specyficzne i jednoznaczne. Nauka danych to proces, który używa nazw i numerów, aby odpowiedzieć na takie pytania. Zazwyczaj używasz analizy danych lub uczenia maszynowego, aby odpowiedzieć na pięć typów pytań:
 
   * Jak dużo lub ile? ubytk
   * Którą kategorię? zmianę
   * Którą grupę? usługę
   * Czy to brzmienia? (wykrywanie anomalii)
   * Którą opcję należy wykonać? zaleca

   Ustal, które pytania są zadawane i jak odpowiadamy na to cele biznesowe.

3. Zdefiniuj zespół projektu, określając role i obowiązki jego członków. Opracowywanie planu punktów kontrolnych wysokiego poziomu, które można wykonać w trakcie odnajdywania większej ilości informacji. 

4. Zdefiniuj metryki sukcesu. Na przykład możesz chcieć zrealizować prognozowanie zmian klientów. Do końca tego projektu trzeciego miesiąca wymagana jest współczynnik dokładności "x". Dzięki tym danym można zaoferować promocje klientom w celu zmniejszenia liczby zmian. Metryki muszą być **inteligentne**: 

   * Pecific **S** 
   * Easurable **M**
   * **Chievable** 
   * Elevant **R** 
   * **T**— powiązane z edytorem IME 

### <a name="identify-data-sources"></a>Identyfikowanie źródeł danych
Zidentyfikuj źródła danych zawierające znane przykłady odpowiedzi na Twoje ostre pytania. Poszukaj następujących danych:

* Dane istotne dla pytania. Czy istnieją miary elementów docelowych i funkcji, które są powiązane z elementem docelowym?
* Dane stanowiące dokładną miarę obiektu docelowego modelu oraz interesujące Cię funkcje.

Na przykład może się okazać, że istniejące systemy muszą zbierać i rejestrować dodatkowe dane w celu rozwiązania problemu i osiągnięcia celów projektu. W takiej sytuacji warto szukać zewnętrznych źródeł danych lub zaktualizować systemy w celu zbierania nowych danych.

## <a name="artifacts"></a>Artifacts
Oto elementy dostarczane na tym etapie:

   * [Dokument czarteru](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): w definicji struktury projektu przetwarzania TDSP jest dostępny standardowy szablon. Dokument czarteru jest dokumentem żywymi. Uaktualniasz szablon w projekcie podczas wykonywania nowych odkrycia i jako zmiany wymagań firmy. Klucz ma na celu iterację w tym dokumencie i dodanie większej liczby szczegółów w trakcie procesu odnajdywania. Utrzymuj klientowi i innym uczestnikom zaangażowanym w wprowadzanie zmian i jasno poinformuj ich o przyczynach zmian.  
   * [Źródła danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): sekcja **nieprzetworzone źródła danych** w raporcie **definicje danych** , która znajduje się w folderze **raportów danych** projektu przetwarzania TDSP, zawiera źródła danych. Ta sekcja określa lokalizację początkową i docelową dla danych pierwotnych. W późniejszych etapach przedstawiono dodatkowe szczegóły, takie jak skrypty do przenoszenia danych do środowiska analitycznego.  
   * [Słowniki danych](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): ten dokument zawiera opisy danych dostarczanych przez klienta. Opisy te zawierają informacje o schemacie (typy danych i informacje o regułach walidacji, jeśli istnieją) oraz diagramach relacji jednostki, jeśli są dostępne.

## <a name="next-steps"></a>Następne kroki

Poniżej przedstawiono linki do poszczególnych etapów cyklu życia przetwarzania TDSP:

   1. [Poznawanie firmy](lifecycle-business-understanding.md)
   2. [Pozyskiwanie danych i ich analiza](lifecycle-data.md)
   3. [Modelowanie](lifecycle-modeling.md)
   4. [Wdrożenie](lifecycle-deployment.md)
   5. [Akceptacja klienta](lifecycle-acceptance.md)

Oferujemy pełne instruktaże, które pokazują wszystkie kroki procesu dla konkretnych scenariuszy. [Przykładowy artykuł instruktażowy](walkthroughs.md) zawiera listę scenariuszy z linkami i opisami miniatur. Instruktaże ilustrują sposób łączenia chmur, narzędzi lokalnych i usług do przepływu pracy lub potoku w celu utworzenia inteligentnej aplikacji. 
