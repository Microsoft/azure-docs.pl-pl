---
title: Omówienie usługi Azure Data Lake Analytics
description: Usługa Data Lake Analytics pozwala kierować firmą w oparciu o szczegółowe informacje uzyskane z danych w chmurze w dowolnej skali.
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: overview
ms.date: 06/23/2017
ms.openlocfilehash: f2916b45c04aac3e36e8dfb82a6bb9b332f55286
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449156"
---
# <a name="what-is-azure-data-lake-analytics"></a>Co to jest usługa Azure Data Lake Analytics?

Usługa Azure Data Lake Analytics jest usługą zadań analizy na żądanie, która pozwala uprościć analizowanie danych big data. Zamiast wdrażać, konfigurować i dostosowywać sprzęt, możesz pisać zapytania umożliwiające przekształcanie danych i wyodrębniać wartościowe informacje. Usługa analizy może od razu rozpocząć obsługę zadań w dowolnej skali — należy wybrać ustawienie wskazujące potrzebną moc. Płacisz tylko za uruchomione zadanie, dzięki czemu oszczędzasz pieniądze. 

## <a name="azure-data-lake-analytics-recent-update-information"></a>Informacje o niedawnej aktualizacji Azure Data Lake Analytics

Usługa Azure Data Lake Analytics została zaktualizowana na podstawie aperiodic do określonego celu. Firma Microsoft zapewnia pomoc techniczną dla tej usługi z aktualizacją składników, składnikiem wersji beta wersja zapoznawcza i tak dalej. 

- Aby uzyskać informacje ogólne o najnowszych aktualizacjach, zobacz [nowości w Data Lake Analytics?](data-lake-analytics-whats-new.md).
- Aby uzyskać szczegółowe informacje dotyczące aktualizacji, zapoznaj się z [uwagą do wersji Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).

## <a name="dynamic-scaling"></a>Dynamiczne skalowanie
  
Usługa Data Lake Analytics umożliwia dynamiczną aprowizację zasobów oraz analizowanie terabajtów, a nawet petabajtów danych. Płacisz tylko za użytą moc przetwarzania. Nawet w sytuacjach zwiększania lub zmniejszania rozmiaru przechowywanych danych lub ilości użytych zasobów obliczeniowych nie trzeba ponownie pisać kodu. 

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>Szybsze programowanie, debugowanie i bardziej inteligentne optymalizowanie przy użyciu znanych narzędzi
  
Usługa Data Lake Analytics głęboko integruje się z programem Visual Studio. Aby uruchamiać, debugować i dostrajać kod, możesz używać znajomych narzędzi. Wizualizacje zadań U-SQL umożliwiają sprawdzanie sposobu działania kodu w skali. Dzięki temu można łatwo identyfikować wąskie gardła związane z wydajnością i optymalizować koszty.

## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL: język prosty i znany, zaawansowany i rozszerzalny
  
Usługa Data Lake Analytics obejmuje język zapytań U-SQL, który stanowi rozszerzenie znanego, prostego, deklaratywnego charakteru języka SQL dzięki wszechstronnym możliwościom języka C#. Język U-SQL używa tego samego rozproszonego środowiska uruchomieniowego, na którym działa wewnętrzny magazyn typu data lake firmy Microsoft mieszczący eksabajty danych. Deweloperzy języków SQL i .NET mogą teraz przetwarzać i analizować swoje dane, wykorzystując już posiadane umiejętności.

## <a name="integrates-seamlessly-with-your-it-investments"></a>Bezproblemowa integracja z inwestycjami związanymi z infrastrukturą IT
  
Usługa Data Lake Analytics wykorzystuje istniejące inwestycje w technologie informatyczne do celów obsługi tożsamości, zarządzania i zabezpieczeń. To podejście ułatwia nadzór nad danymi i rozbudowę istniejących aplikacji obsługujących dane. Usługa Data Lake Analytics jest zintegrowana z usługą Active Directory, co umożliwia zarządzanie użytkownikami i udzielanie im uprawnień, oraz zawiera wbudowane funkcje monitorowania i inspekcji.

## <a name="affordable-and-cost-effective"></a>Przystępna cena i niedroga obsługa

Usługa Data Lake Analytics to ekonomiczne rozwiązanie służące do uruchamiania obciążeń związanych z danymi big data. Opłaty są naliczane za poszczególne zadania wykonywane podczas przetwarzania danych. Sprzęt, licencje ani umowy dotyczące pomocy technicznej w zakresie usługi nie są wymagane. System jest automatycznie skalowany w pionie i w poziomie w momencie rozpoczęcia i zakończenia zadania, więc nigdy nie płaci się więcej, niż trzeba. [Dowiedz się więcej na temat kontrolowania kosztów i oszczędzania pieniędzy](https://aka.ms/adlasavemoney).

## <a name="works-with-all-your-azure-data"></a>Współdziałanie z wszystkimi danymi na platformie Azure
  
Data Lake Analytics współpracuje z Azure Data Lake Storage w celu uzyskania najwyższej wydajności, przepływności i przetwarzanie równoległe i współpracuje z obiektami BLOB usługi Azure Storage, Azure SQL Database, Azure Synapse Analytics.

## <a name="in-region-data-residency"></a>Zamieszkania danych w regionie
  
Data Lake Analytics nie przenosi ani nie zapisuje danych klienta z regionu, w którym został wdrożony.


## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z tematem Azure Data Lake Analytics Ostatnia aktualizacja przy użyciu [nowości w programie Azure Data Lake Analytics?](data-lake-analytics-whats-new.md)
* Wprowadzenie do Data Lake Analytics przy użyciu [](data-lake-analytics-get-started-portal.md)  |  [](data-lake-analytics-get-started-powershell.md)  |  [interfejsu wiersza polecenia](data-lake-analytics-get-started-cli.md) Azure Portal Azure PowerShell
* Zarządzanie Azure Data Lake Analytics przy użyciu [Azure Portal](data-lake-analytics-manage-use-portal.md)  |  [Azure PowerShell](data-lake-analytics-manage-use-powershell.md)  |  [interfejsu wiersza polecenia](data-lake-analytics-manage-use-cli.md)  |  [platformy Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md)  |  [Node.js](data-lake-analytics-manage-use-nodejs.md)
* [Jak kontrolować koszty i oszczędzać pieniądze przy użyciu usługi Data Lake Analytics](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
