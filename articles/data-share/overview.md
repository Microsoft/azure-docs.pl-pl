---
title: Co to jest usługa Azure Data Share?
description: Uzyskaj informacje na temat udostępniania danych w sposób zwykły i bezpieczny dla wielu klientów i partnerów przy użyciu udziału danych platformy Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: overview
ms.date: 07/30/2020
ms.openlocfilehash: e80ea37409f7b5beb48ba7b8e96aeeda9a5836e5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89433436"
---
# <a name="what-is-azure-data-share"></a>Co to jest usługa Azure Data Share?

W dzisiejszym świecie dane są wyświetlane jako kluczowy zasób strategiczny, który wiele organizacji musi po prostu i bezpiecznie udostępnić klientom i partnerom. Istnieje wiele sposobów, w których obecnie klienci wykonują te czynności, w tym za pośrednictwem protokołu FTP, poczty e-mail i interfejsów API, aby nazwać kilka. Organizacje mogą łatwo utracić śledzenie osób, w których udostępnili swoje dane. Udostępnianie danych przy użyciu protokołu FTP lub w ramach infrastruktury interfejsu API jest często kosztowne do aprowizacji i administrowania. Istnieją narzuty związane z zarządzaniem za pomocą tych metod udostępniania na dużą skalę. 

Wiele organizacji musi być odpowiedzialnych za udostępnione dane. Oprócz odpowiedzialności wiele organizacji chce mieć w prosty sposób kontrolować i monitorować wszystkie współużytkowanie danych oraz zarządzać nimi. W dzisiejszym świecie, w którym oczekuje się, że dane będą nadal wzrastać w wykładniczym tempie, organizacje muszą mieć prosty sposób udostępniania danych Big Data. Klienci wymagają najnowszych danych, aby mieć pewność, że będą oni mogli uzyskiwać szczegółowe informacje.

Udział danych platformy Azure umożliwia organizacjom proste i bezpieczne udostępnianie danych wielu klientom i partnerom. Wystarczy kilka kliknięć, aby zainicjować nowe konto udostępniania danych, dodać zestawy danych i zaprosić klientów i partnerów do udziału danych. Dostawcy danych zawsze kontrolują udostępnione dane. Udział danych platformy Azure ułatwia zarządzanie i monitorowanie danych, które zostały udostępnione, kiedy i przez kogo. 

Dostawca danych może zachować kontrolę nad sposobem obsługi ich danych, określając warunki użytkowania ich udziału danych. Uzyskanie danych jest uzależnione od tego, czy konsument danych zaakceptował te warunki. Dostawcy danych mogą określać częstotliwość, z jaką konsumenci danych będą otrzymywać aktualizacje. Dostawca danych może w dowolnym momencie odwołać dostęp do nowych aktualizacji. 

Udział danych platformy Azure ułatwia zwiększenie wglądu w dane, ułatwiając łączenie danych od innych firm w celu wzbogacania scenariuszy analizy i AI. Korzystaj z możliwości narzędzi analitycznych platformy Azure, aby przygotować, przetwarzać i analizować dane udostępnione za pomocą udziału danych platformy Azure. 

Dostawca danych i odbiorca danych muszą mieć subskrypcję platformy Azure, aby udostępniać i odbierać dane. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="scenarios-for-azure-data-share"></a>Scenariusze dotyczące udziału danych platformy Azure

Udział danych platformy Azure może być używany w wielu różnych branżach. Na przykład sprzedawca detaliczny może chcieć udostępnić ostatni punkt danych sprzedaży swoim dostawcom. Korzystając z udziału danych platformy Azure, sprzedawca detaliczny może skonfigurować udział danych zawierający punkt danych sprzedaży dla wszystkich dostawców i dzielić się sprzedażą co godzinę lub codziennie. 

Udział danych platformy Azure może być również używany do ustanawiania rynku danych dla konkretnej branży. Na przykład organizacja rządowa lub instytucja badawcza, która regularnie udostępnia dane anonimowe na temat wzrostu populacji innym podmiotom. 

Innym przypadkiem użycia usługi Azure Data Share jest ustanowienie konsorcjum danych. Na przykład niektóre różne instytucje badawcze mogą udostępniać dane za pomocą jednej zaufanej treści. Dane są analizowane, agregowane lub przetwarzane przy użyciu narzędzi analitycznych platformy Azure, a następnie udostępniane zainteresowanym stronom. 

## <a name="how-it-works"></a>Jak to działa

Udział danych platformy Azure oferuje obecnie udostępnianie oparte na migawce i udostępnianie w miejscu. 

W ramach udostępniania opartego na migawce dane są przenoszone z subskrypcji platformy Azure dostawcy danych i gruntów w ramach subskrypcji platformy Azure klienta danych. Jako dostawca danych zastrzegasz udział danych i zapraszasz odbiorców do udziału danych. Odbiorcy danych otrzymują zaproszenie do udziału danych za pośrednictwem poczty e-mail. Gdy odbiorca danych zaakceptuje zaproszenie, może wyzwolić pełną migawkę danych, które zostały do nich udostępnione. Te dane są odbierane na koncie magazynu odbiorcy danych. Odbiorcy danych mogą odbierać regularne, przyrostowe aktualizacje danych, które są do nich udostępniane, aby zawsze miały najnowszą wersję danych. 

Dostawcy danych mogą oferować klientom danych przyrostowe aktualizacje danych, które są im udostępniane przez harmonogram migawek. Harmonogramy migawek są oferowane co godzinę lub codziennie. Gdy odbiorca danych akceptuje i konfiguruje udział danych, może subskrybować harmonogram migawek. Jest to przydatne w scenariuszach, w których dane udostępnione są regularnie aktualizowane, a odbiorca danych wymaga najnowszych danych. 

![przepływ udostępniania danych](media/data-share-flow.png)

Gdy odbiorca danych akceptuje udział danych, może odbierać dane w wybranym przez siebie magazynie danych. Na przykład jeśli dostawca danych udostępnia dane za pomocą usługi Azure Blob Storage, odbiorca danych może odbierać te dane w Azure Data Lake Store. Podobnie, jeśli dostawca danych udostępnia dane z usługi Azure Synapse Analytics (dawniej SQL Data Warehouse), odbiorca danych może zdecydować, czy chcą otrzymywać dane do Azure Data Lake Store, Azure SQL Database czy analizy Synapse Azure. W przypadku udostępniania z poziomu źródeł opartych na języku SQL odbiorca danych może również wybrać, czy mają oni odbierać dane w Parquet czy CSV. 

W przypadku udostępniania w miejscu dostawcy danych mogą udostępniać dane, w których znajdują się bez kopiowania danych. Po ustanowieniu relacji udostępniania za pomocą przepływu zaproszenia zostanie utworzone łącze symboliczne między źródłowym magazynem danych dostawcy danych a docelowym magazynem danych odbiorcy danych. Konsument danych może odczytywać i wysyłać zapytania dotyczące danych w czasie rzeczywistym przy użyciu własnego magazynu danych. Zmiany w źródłowym magazynie danych są natychmiast dostępne dla konsumenta danych. Udostępnianie w miejscu jest obecnie dostępne w wersji zapoznawczej dla usługi Azure Eksplorator danych.

## <a name="key-capabilities"></a>Najważniejsze możliwości

Udział danych platformy Azure umożliwia dostawcom danych:

* Udostępnianie danych z listy [obsługiwanych magazynów danych](supported-data-stores.md) klientom i partnerom spoza organizacji

* Śledź użytkowników, którym udostępniono Twoje dane

* Wybór migawki lub udostępnianie w miejscu

* Jak często odbiorcy danych otrzymują aktualizacje danych

* Zezwól klientom na ściąganie najnowszej wersji danych zgodnie z potrzebami lub Zezwól im na automatyczne otrzymywanie zmian przyrostowych danych z interwałem zdefiniowanym przez użytkownika

Udział danych platformy Azure umożliwia użytkownikom danych: 

* Wyświetl opis typu danych, które są udostępniane

* Wyświetl warunki użytkowania danych

* Akceptowanie lub odrzucanie zaproszenia udziału danych platformy Azure

* Akceptuj dane udostępnione Tobie w [obsługiwanym magazynie danych](supported-data-stores.md).

* Wyzwól pełną lub przyrostową migawkę udziału danych, który organizacja udostępniła Tobie

* Zasubskrybuj udział danych, aby otrzymać najnowszą kopię danych za pomocą migawki przyrostowej

Wszystkie najważniejsze możliwości wymienione powyżej są obsługiwane za pośrednictwem Azure Portal lub za pośrednictwem interfejsów API REST. Aby uzyskać więcej informacji na temat korzystania z usługi Azure Data Share za pośrednictwem interfejsów API REST, zapoznaj się z dokumentacją referencyjną. 

## <a name="supported-regions"></a>Obsługiwane regiony

Listę regionów świadczenia usługi Azure, które udostępniają udział danych platformy Azure, można znaleźć na stronie [produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=data-share) i wyszukać udział danych platformy Azure. 

Udział danych platformy Azure nie przechowuje kopii samych danych. Dane są przechowywane w magazynie danych, który jest udostępniany. Na przykład, jeśli producent danych przechowuje swoje dane na koncie Azure Data Lake Store zlokalizowanym w regionie zachodnie stany USA, w którym są przechowywane dane. Jeśli udostępniają dane za pomocą konta usługi Azure Storage znajdującego się w regionie Europa Zachodnia za pośrednictwem migawki, zazwyczaj dane są przekazywane bezpośrednio do konta usługi Azure Storage znajdującego się w regionie Europa Zachodnia.

Usługa udziału danych platformy Azure nie musi być dostępna w Twoim regionie, aby można było korzystać z usługi. Jeśli na przykład dane są przechowywane na koncie usługi Azure Storage znajdującym się w regionie, w którym udział danych platformy Azure nie jest jeszcze dostępny, można nadal korzystać z usługi do udostępniania danych. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .
