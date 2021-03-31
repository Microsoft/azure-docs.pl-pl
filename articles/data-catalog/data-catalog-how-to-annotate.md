---
title: Jak dodawać adnotacje do źródeł danych w Azure Data Catalog
description: Artykuł zawiera instrukcje dotyczące sposobu dodawania adnotacji do zasobów danych w Azure Data Catalog, w tym przyjaznych nazw, tagów, opisów i ekspertów.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 89fa1b542c54fc962f8c12a2142884c677afb259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674652"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Jak dodawać adnotacje do źródeł danych w Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Wprowadzenie

**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i odnajdywania źródeł danych w przedsiębiorstwie. Innymi słowy, Data Catalog to pomoc dla osób, które odnajdują i korzystają ze źródeł danych oraz pomagają organizacjom w uzyskaniu większej korzyści z istniejących danych. Gdy źródło danych jest zarejestrowane w Data Catalog, jego metadane są kopiowane i indeksowane przez usługę, ale wątek nie kończy w tym miejscu. Data Catalog umożliwia użytkownikom dostarczenie własnych metadanych opisowych, takich jak opisy i Tagi, aby uzupełnić metadane wyodrębnione ze źródła danych, a źródło danych było bardziej zrozumiałe dla większej liczby osób.

## <a name="annotation-and-crowdsourcing"></a>Adnotacja i crowdsourcing
Wszyscy mają opinię. Jest to dobry efekt.
Data Catalog uznaje, że różni użytkownicy mają różne perspektywy dotyczące źródeł danych przedsiębiorstwa, a każda z tych perspektyw może być cenna. Poniżej przedstawiono przykładowy scenariusz:

* Administrator systemu wie o umowie dotyczącej poziomu usług dla serwerów lub usług, które obsługują źródło danych.
* Administrator bazy danych wie o harmonogramie tworzenia kopii zapasowych dla każdej bazy danych i dozwolonych oknach przetwarzania ETL.
* Właściciel systemu zna proces uzyskiwania dostępu do źródła danych przez użytkowników.
* Steward danych wie, jak zasoby i atrybuty w źródle danych są mapowane na model danych przedsiębiorstwa.
* Analityk wie, jak dane są używane w kontekście procesów biznesowych, które są przez nie obsługiwane.

Każda z tych perspektyw jest cenna, a Data Catalog używa podejścia crowdsourcing do metadanych, które umożliwiają przechwycenie każdego z nich i użycie go do zapewnienia kompletnego obrazu zarejestrowanych źródeł danych. Za pomocą portalu Data Catalog każdy użytkownik może dodawać i edytować własne adnotacje, jednocześnie umożliwiając wyświetlanie adnotacji udostępnianych przez innych użytkowników.

## <a name="different-types-of-annotations"></a>Różne typy adnotacji
Data Catalog obsługuje następujące typy adnotacji:

| Adnotacja | Uwagi |
| --- | --- |
| Przyjazna nazwa |Przyjazne nazwy można dostarczyć na poziomie zasobów danych, aby ułatwić zrozumienie zasobów danych. Przyjazne nazwy są najbardziej przydatne, gdy nazwa podstawowego obiektu to tajemnicze, skrócona lub w inny sposób niezrozumiały dla użytkowników. |
| Opis |Opisy można podać na poziomach zasobów danych i atrybutów/kolumn. Opisy to bezpłatne, krótkie adnotacje tekstu, które opisują perspektywę użytkownika danych lub jej użycia. |
| Tagi (tagi użytkownika) |Tagi mogą być dostarczane na poziomach zasobów danych i atrybutów/kolumn. Tagi użytkownika to etykiety zdefiniowane przez użytkownika, które mogą być używane do kategoryzowania zasobów lub atrybutów danych. |
| Tagi (Tagi słownika) |Tagi mogą być dostarczane na poziomach zasobów danych i atrybutów/kolumn. Tagi słownika są zdefiniowanymi centralnie terminami, które mogą służyć do kategoryzowania zasobów danych lub atrybutów przy użyciu wspólnej taksonomii biznesowej. Aby uzyskać więcej informacji, zobacz [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Konfigurowanie słownika biznesowego na potrzeby znakowania zarządzanego). |
| Ekspert |Eksperci mogą być dostarczani na poziomie zasobów danych. Eksperci identyfikują użytkowników lub grupy z perspektywami eksperckimi dotyczącymi danych i mogą traktować jako punkty kontaktu dla użytkowników, którzy odnajdują zarejestrowane źródła danych i mają pytania, na które nie odpowiadają istniejące adnotacje. |
| Żądanie dostępu |Informacje o dostępie do żądania można podać na poziomie zasobu danych. Te informacje są przeznaczone dla użytkowników, którzy odnajdują źródło danych, które nie mają jeszcze uprawnień dostępu. Użytkownicy mogą wprowadzić adres e-mail użytkownika lub grupy, który udziela dostępu, adres URL procesu lub narzędzia, do którego użytkownicy muszą uzyskać dostęp, lub mogą wprowadzić sam proces jako tekst. |
| Dokumentacja |Dokumentację można podać na poziomie zasobu danych. Dokumentacja zasobów to informacje w postaci tekstu sformatowanego, które mogą zawierać linki i obrazy oraz informacje, które nie są przekazywane przez opisy i Tagi. |

## <a name="annotating-multiple-assets"></a>Dodawanie adnotacji do wielu zasobów
W przypadku wybrania wielu zasobów danych w portalu Data Catalog użytkownicy mogą dodawać adnotacje do wszystkich wybranych zasobów w ramach jednej operacji. Adnotacje będą dotyczyć wszystkich wybranych zasobów, dzięki czemu można łatwo wybrać i zapewnić spójny opis oraz zestawy tagów i ekspertów dla powiązanych zasobów danych.

> [!NOTE]
> Przy rejestrowaniu zasobów danych za pomocą narzędzia rejestracji źródła danych Data Catalog można również dostarczyć Tagi i eksperci.
>
>

W przypadku wybrania wielu tabel i widoków w portalu Data Catalog będą wyświetlane tylko kolumny, dla których wszystkie wybrane zasoby danych są wspólne. Dzięki temu użytkownicy mogą podawać Tagi i opisy dla wszystkich kolumn o tej samej nazwie dla wszystkich wybranych zasobów.

## <a name="annotations-and-discovery"></a>Adnotacje i odnajdywanie
Podobnie jak metadane wyodrębnione ze źródła danych podczas rejestracji są dodawane do indeksu wyszukiwania Data Catalog, metadane dostarczone przez użytkownika również są indeksowane. Oznacza to, że nie tylko adnotacje ułatwiają użytkownikom zrozumienie wykrywanych przez nich danych. adnotacje ułatwiają użytkownikom odnajdywanie zasobów danych z adnotacjami przez wyszukiwanie przy użyciu tych warunków.

## <a name="summary"></a>Podsumowanie
Zarejestrowanie źródła danych za pomocą Data Catalog umożliwia odnajdywanie danych przez kopiowanie strukturalnych i opisowych metadanych ze źródła danych do usługi katalogowej. Po zarejestrowaniu źródła danych użytkownicy mogą podawać adnotacje, aby ułatwić ich odnajdywanie i zrozumienie w portalu Data Catalog.

## <a name="see-also"></a>Zobacz też
* [Zacznij korzystać z](data-catalog-get-started.md) samouczka Azure Data Catalog, aby uzyskać szczegółowe informacje na temat sposobu dodawania adnotacji do źródeł danych.
