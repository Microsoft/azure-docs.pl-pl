---
title: Ochrona danych w Azure Stream Analytics
description: W tym artykule wyjaśniono, jak szyfrować dane prywatne używane przez zadanie Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: e823322803958f092cee3b6d77e6a0ca7bc6e3f2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074254"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Ochrona danych w Azure Stream Analytics 

Azure Stream Analytics to w pełni zarządzana platforma jako usługa, która umożliwia tworzenie potoków analizy w czasie rzeczywistym. Wszystkie duże funkcje, takie jak inicjowanie obsługi klastrów, skalowanie węzłów do użycia i zarządzanie wewnętrznymi punktami kontrolnymi, są zarządzane w tle.

## <a name="private-data-assets-that-are-stored"></a>Prywatne zasoby danych, które są przechowywane

Azure Stream Analytics utrzymuje następujące metadane i dane, aby można było je uruchomić: 

* Definicja zapytania i powiązane z nimi konfiguracje  

* Funkcje zdefiniowane przez użytkownika lub agregacje  

* Punkty kontrolne potrzebne przez środowisko uruchomieniowe Stream Analytics

* Migawki danych referencyjnych 

* Szczegóły połączenia zasobów używanych przez zadanie Stream Analytics

Aby dowiedzieć się, jak zapewnić zgodność z wymaganiami dotyczącymi zgodności w dowolnych sektorach lub środowiskach objętych regulacją, możesz przeczytać więcej na temat [ofert zgodności firmy Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="in-region-data-residency"></a>In-Region zamieszkania danych
Azure Stream Analytics przechowuje dane klienta i inne metadane opisane powyżej. Dane klienta są domyślnie przechowywane przez Azure Stream Analytics w jednym regionie, więc ta usługa automatycznie spełnia wymagania dotyczące miejsca zamieszkania danych regionu, w tym te określone w [Centrum zaufania](https://azuredatacentermap.azurewebsites.net/).
Ponadto możesz wybrać przechowywanie wszystkich zasobów danych (danych klienta i innych metadanych) związanych z zadaniem usługi Stream Analytics w jednym regionie, szyfrując je na wybranym koncie magazynu.

## <a name="encrypt-your-data"></a>Szyfrowanie danych

Stream Analytics automatycznie korzysta z najlepszych w swojej klasie standardów szyfrowania w celu zaszyfrowania i zabezpieczenia danych. Możesz po prostu zaufać Stream Analytics, aby bezpiecznie przechowywać wszystkie dane, dzięki czemu nie musisz martwić się o zarządzanie infrastrukturą.

Jeśli chcesz używać kluczy zarządzanych przez klienta (CMK) do szyfrowania danych, możesz użyć własnego konta magazynu (ogólnego przeznaczenia w wersji 1 lub v2) do przechowywania wszystkich prywatnych zasobów danych, które są wymagane przez środowisko uruchomieniowe Stream Analytics. Twoje konto magazynu może być zaszyfrowane zgodnie z potrzebami. Żaden z Twoich prywatnych zasobów danych nie jest trwale przechowywany przez infrastrukturę Stream Analytics. 

To ustawienie należy skonfigurować w momencie Stream Analytics tworzenia zadania i nie można go modyfikować w cyklu życia zadania. Nie zaleca się modyfikowania ani usuwania magazynu używanego przez Stream Analytics. Usunięcie konta magazynu spowoduje trwałe usunięcie wszystkich prywatnych zasobów danych, co spowoduje niepowodzenie zadania. 

Nie można zaktualizować ani obrócić kluczy do konta magazynu przy użyciu portalu Stream Analytics. Klucze można aktualizować za pomocą interfejsów API REST.


### <a name="configure-storage-account-for-private-data"></a>Skonfiguruj konto magazynu dla prywatnych danych 


Zaszyfruj swoje konto magazynu, aby zabezpieczyć wszystkie dane, i jawnie wybierz lokalizację prywatnych danych. 

Aby dowiedzieć się, jak zapewnić zgodność z wymaganiami dotyczącymi zgodności w dowolnych sektorach lub środowiskach objętych regulacją, możesz przeczytać więcej na temat [ofert zgodności firmy Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 



Wykonaj poniższe kroki, aby skonfigurować konto magazynu dla prywatnych zasobów danych. Ta konfiguracja została wykonana z zadania Stream Analytics, a nie z konta magazynu.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** . 

1. Wybierz pozycję **Analiza**   >  **Stream Analytics zadania**   z listy wyników. 

1. Wypełnij stronę Stream Analytics zadania, aby uzyskać niezbędne szczegóły, takie jak nazwa, region i skala. 

1. Zaznacz pole wyboru informujące o *zabezpieczeniu wszystkich prywatnych zasobów danych wymaganych przez to zadanie na koncie magazynu* .

1. Wybierz konto magazynu z subskrypcji. Należy pamiętać, że tego ustawienia nie można modyfikować w całym cyklu życia zadania. 

   ![Ustawienia konta prywatnego magazynu danych](./media/data-protection/storage-account-create.png)

## <a name="private-data-assets-that-are-stored"></a>Prywatne zasoby danych, które są przechowywane

Wszystkie prywatne dane, które są wymagane do utrwalenia przez Stream Analytics są przechowywane na koncie magazynu. Przykłady prywatnych zasobów danych obejmują: 

* Zapytania, które zostały utworzone i powiązane z nimi konfiguracje  

* Funkcje zdefiniowane przez użytkownika 

* Punkty kontrolne potrzebne przez środowisko uruchomieniowe Stream Analytics

* Migawki danych referencyjnych 

Są również przechowywane szczegóły połączenia zasobów, które są używane przez zadanie Stream Analytics. Zaszyfruj konto magazynu, aby zabezpieczyć wszystkie Twoje dane. 

Aby dowiedzieć się, jak zapewnić zgodność z wymaganiami dotyczącymi zgodności w dowolnych sektorach lub środowiskach objętych regulacją, możesz przeczytać więcej na temat [ofert zgodności firmy Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). 

## <a name="enables-data-residency"></a>Włącza dane w miejscu zamieszkania 
Korzystając z tej funkcji, można wymusić wszelkie wymagania dotyczące miejsca zamieszkania danych, które mogą być wymagane przez podanie konta magazynu odpowiednio.

## <a name="known-issues"></a>Znane problemy
Istnieje znany problem polegający na tym, że zadanie korzystające z klucza zarządzanego przez klienta działa w niepowodzeń podczas korzystania z tożsamości zarządzanej do uwierzytelniania w dowolnych danych wejściowych lub wyjściowych. 

## <a name="next-steps"></a>Następne kroki

* [Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md)
* [Informacje o danych wejściowych dla Azure Stream Analytics](stream-analytics-add-inputs.md)
* [Pojęcia dotyczące punktów kontrolnych i powtarzania w zadaniach Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md)
* [Używanie danych referencyjnych do wyszukiwania w Stream Analytics](stream-analytics-use-reference-data.md)
