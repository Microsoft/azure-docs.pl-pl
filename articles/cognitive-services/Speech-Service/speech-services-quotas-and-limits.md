---
title: Przydziały i limity usługi Mowa
titleSuffix: Azure Cognitive Services
description: Krótki przewodnik, szczegółowy opis i najlepsze rozwiązania dotyczące przydziałów i limitów związanych z usługą Azure poznawcze usługi rozpoznawania mowy
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: alexeyo
ms.openlocfilehash: a304628e05054124fde6ffe5c2b63177991d8cfd
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345401"
---
# <a name="speech-services-quotas-and-limits"></a>Przydziały i limity usługi Mowa

Ten artykuł zawiera krótkie informacje i **szczegółowy opis** przydziałów i limitów związanych z usługą Azure poznawcze usługi rozpoznawania mowy dla wszystkich [warstw cenowych](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Zawiera również kilka najlepszych rozwiązań, aby uniknąć ograniczania żądań. 

## <a name="quotas-and-limits-quick-reference"></a>Przydziały i limity — Krótki przewodnik
Przechodzenie do [przydziałów i ograniczeń zamiany tekstu na mowę](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>Przydziały i limity funkcji zamiany mowy na tekst dla zasobu usługi Mowa
W tabelach poniżej parametrów bez "regulowanego" wiersz **nie** jest dostosowywany do wszystkich warstw cenowych.

#### <a name="online-transcription"></a>Transkrypcja online

| limit przydziału | Bezpłatna (F0)<sup>1</sup> | Standardowa (S0) |
|--|--|--|
| **Limit liczby żądań współbieżnych (modele podstawowe i niestandardowe)** | 1 | 20 (wartość domyślna) |
| Wraz | Nr<sup>2</sup> | Tak<sup>2</sup> |

#### <a name="batch-transcription"></a>Transkrypcja partii
| limit przydziału | Bezpłatna (F0)<sup>1</sup> | Standardowa (S0) |
|--|--|--|
| Limit interfejsu API REST | Transkrypcja usługi Batch nie jest dostępna dla F0 | 300 żądań na minutę |
| Maksymalny rozmiar pliku wejściowego audio | Brak | 1 GB |
| Maksymalny rozmiar wejściowego obiektu BLOB (może zawierać więcej niż jeden plik, na przykład w archiwum zip; Pamiętaj o powyższym limicie rozmiaru pliku) | Brak | 2,5 GB |
| Maksymalny rozmiar kontenera obiektów BLOB | Brak | 5 GB |
| Maksymalna liczba obiektów BLOB na kontener | Brak | 10 000 |
| Maksymalna liczba plików na żądanie transkrypcji (w przypadku korzystania z wielu adresów URL zawartości jako dane wejściowe) | Brak | 1000  |
| Maksymalna liczba uruchomionych jednocześnie zadań | Brak | 2000  |

#### <a name="model-customization"></a>Dostosowywanie modelu
| limit przydziału | Bezpłatna (F0)<sup>1</sup> | Standardowa (S0) |
|--|--|--|
| Limit interfejsu API REST | 300 żądań na minutę | 300 żądań na minutę |
| Maksymalna liczba zestawów danych mowy | 2 | 500 |
| Maksymalny rozmiar pliku zestawu danych akustycznych na potrzeby importowania danych | 2 GB | 2 GB |
| Maksymalny rozmiar pliku zestawu danych języka na potrzeby importowania danych | 200 MB | 1,5 GB |
| Maksymalny rozmiar pliku zestawu danych wymowy na potrzeby importowania danych | 1 KB | 1 MB |
| Maksymalny rozmiar tekstu podczas używania `text` parametru w żądaniu interfejsu API [tworzenia modelu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) | 200 KB | 500 KB |

<sup>1</sup> dla warstwy cenowej **bezpłatna (F0)** Zobacz również miesięczne diety na [stronie cennika](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>2</sup> zapoznaj się z [dodatkowymi wyjaśnieniami](#detailed-description-quota-adjustment-and-best-practices), [najlepszymi rozwiązaniami](#general-best-practices-to-mitigate-throttling-during-autoscaling)i [instrukcjami dotyczącymi dostosowywania](#speech-to-text-increasing-online-transcription-concurrent-request-limit).<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>Przydziały zamiany tekstu na mowę i limity dla zasobu mowy
W tabeli poniżej parametrów bez "regulowanego" wiersz **nie** jest dostosowywany do wszystkich warstw cenowych.

| limit przydziału | Bezpłatna (F0)<sup>3</sup> | Standardowa (S0) |
|--|--|--|
| **Maksymalna liczba transakcji na sekundę (TPS) dla głosów standardowych i neuronowych** | 200<sup>4</sup> | 200<sup>4</sup> |  |
| **Limit współbieżnych żądań dla niestandardowego głosu** |  |  |
| Wartość domyślna | 10 | 10 |
| Wraz | Nr<sup>5</sup> | Tak<sup>5</sup> |
| **Przydziały dotyczące protokołu HTTP** |  |
| Maksymalna długość audio wystawiona na żądanie | 10 min | 10 min |
| Maksymalna liczba unikatowych `<voice>` tagów w SSML | 50 | 50 |
| **Przydziały określone dla protokołu WebSocket** |  |  |
|Maksymalna długość audio wyprodukowanych na drodze | 10 min | 10 min |
|Maksymalny rozmiar komunikatu SSML na przekształcenie |64 KB |64 KB |
| **Limit interfejsu API REST** | 20 żądań na minutę | 25 żądań na 5 sekund |


<sup>3</sup> dla warstwy cenowej **bezpłatna (F0)** Zobacz również miesięczne diety na [stronie cennika](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>4</sup> zapoznaj się z [dodatkowymi wyjaśnieniami](#detailed-description-quota-adjustment-and-best-practices) i [najlepszymi rozwiązaniami](#general-best-practices-to-mitigate-throttling-during-autoscaling).<br/>
<sup>5</sup> zapoznaj się z [dodatkowymi wyjaśnieniami](#detailed-description-quota-adjustment-and-best-practices), [najlepszymi rozwiązaniami](#general-best-practices-to-mitigate-throttling-during-autoscaling)i [instrukcjami dotyczącymi dostosowywania](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice).<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Szczegółowy opis, dopasowanie przydziału i najlepsze rozwiązania
Przed zażądaniem zwiększenia limitu przydziału (jeśli ma to zastosowanie) Upewnij się, że jest to konieczne. Usługa mowy korzysta z technologii skalowania automatycznego w celu przeprowadzenia wymaganych zasobów obliczeniowych w trybie "na żądanie" i w tym samym czasie, aby zapewnić niską koszty klienta, nie zapewniając nadmiernej ilości sprzętu. Za każdym razem, gdy aplikacja otrzymuje kod odpowiedzi 429 ("zbyt wiele żądań"), podczas gdy obciążenie przekracza zdefiniowane limity (zobacz [przydziały i limity szybkiego odwołania](#quotas-and-limits-quick-reference)) najbardziej prawdopodobnym wyjaśnieniem jest to, że usługa jest skalowana do popytu i nie osiągnęła jeszcze wymaganej skali, a tym samym nie ma wystarczającej ilości zasobów, aby obsłużyć żądanie. Ten stan jest zwykle przejściowy i nie powinien być ostatni długi.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Ogólne najlepsze rozwiązania pozwalające ograniczyć ograniczenie przepustowości podczas skalowania automatycznego
Aby zminimalizować problemy związane z ograniczaniem wydajności (kod odpowiedzi 429), zalecamy użycie następujących technik:
- Implementowanie logiki ponawiania w aplikacji
- Unikaj ostrych zmian obciążenia. Stopniowe zwiększanie obciążenia <br/>
*Przykład.* Twoja aplikacja używa zamiany tekstu na mowę, a bieżące obciążenie to 5 TPS (liczba transakcji na sekundę). Kolejna sekunda zwiększy obciążenie do 20 TPS (to znaczy cztery razy więcej). Usługa natychmiast rozpoczyna skalowanie w górę w celu zaspokojenia nowego obciążenia, ale prawdopodobnie nie będzie w stanie wykonać go w ciągu sekundy, dlatego niektóre żądania otrzymają kod odpowiedzi 429.   
- Testuj różne wzorce wzrostu obciążenia
  - Zobacz [przykład zamiany mowy na tekst](#speech-to-text-example-of-a-workload-pattern-best-practice)
- Tworzenie dodatkowych zasobów mowy w tym samym lub różnych regionach i dystrybuowanie obciążeń między nimi przy użyciu techniki "Round Robin". Jest to szczególnie ważne w przypadku parametru **Text-to-Speech TPS (liczba transakcji na sekundę)** , który jest ustawiony jako 200 dla zasobu mowy i nie można go dostosować  

W następnych sekcjach opisano konkretne przypadki dostosowywania przydziałów.<br/>
Przejdź do [zamiany tekstu na mowę. Zwiększanie współbieżnych limitów żądań transkrypcji dla niestandardowego głosu](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>Zamiana mowy na tekst: zwiększanie współbieżnych żądań transkrypcji online
Domyślnie liczba współbieżnych żądań jest ograniczona do 20 dla zasobu mowy (model podstawowy) lub na niestandardowy punkt końcowy (model niestandardowy). W przypadku warstwy cenowej standardowa można zwiększyć tę kwotę. Przed przesłaniem żądania upewnij się, że znasz materiał w [tej sekcji](#detailed-description-quota-adjustment-and-best-practices) i zapoznaj się z [najlepszymi rozwiązaniami](#general-best-practices-to-mitigate-throttling-during-autoscaling).

Zwiększenie limitu współbieżnych **żądań nie wpływa bezpośrednio na** koszty. Usługi mowy korzystają z modelu "płatność tylko za rzeczywiste użycie". Limit definiuje, jak wysoka wydajność może być skalowana przed rozpoczęciem ograniczania żądań.

Współbieżne limity żądań dla modeli **podstawowych** i **niestandardowych** muszą zostać dostosowane **osobno**.

Istniejąca wartość parametru limitu współbieżnych żądań **nie** jest widoczna za pośrednictwem Azure Portal, Command-Line narzędzi lub żądań interfejsu API. Aby zweryfikować istniejącą wartość, Utwórz żądanie pomocy technicznej platformy Azure.

>[!NOTE]
>[Kontenery mowy](speech-container-howto.md) nie wymagają zwiększenia liczby równoczesnych żądań, ponieważ kontenery są ograniczone tylko przez procesory, w których są hostowane sprzęt.

#### <a name="have-the-required-information-ready"></a>Przygotuj wymagane informacje:
- Dla **modelu podstawowego** :
  - Identyfikator zasobu mowy
  - Region (Region)
- Dla **modelu niestandardowego** : 
  - Region (Region)
  - Identyfikator niestandardowego punktu końcowego

- **Jak uzyskać informacje (model podstawowy)** :  
  - Przejdź do [Azure Portal](https://portal.azure.com/)
  - Wybierz zasób mowy, dla którego chcesz zwiększyć limit żądań współbieżności
  - Wybierz *Właściwości* (Grupa *zarządzania zasobami* ) 
  - Skopiuj i Zapisz wartości następujących pól:
    - **Identyfikator zasobu**
    - **Lokalizacja** (region punktu końcowego)

- **Jak uzyskać informacje (model niestandardowy)** :
  - Przejdź do portalu [mowy Studio](https://speech.microsoft.com/)
  - Zaloguj się w razie potrzeby
  - Przejdź do Custom Speech
  - Wybierz projekt
  - Przejdź do *wdrożenia*
  - Wybierz wymagany punkt końcowy
  - Skopiuj i Zapisz wartości następujących pól:
    - **Region usługi** (region punktu końcowego)
    - **Identyfikator punktu końcowego**
  
#### <a name="create-and-submit-support-request"></a>Utwórz i prześlij żądanie pomocy technicznej
Zainicjuj zwiększenie limitu współbieżnych żądań dla zasobu lub w razie potrzeby Sprawdź dzisiejszy limit przez przesłanie żądania pomocy technicznej:

- Upewnij się, że masz [wymagane informacje](#have-the-required-information-ready)
- Przejdź do [Azure Portal](https://portal.azure.com/)
- Wybierz zasób mowy, dla którego chcesz zwiększyć (lub sprawdzić) limit żądań współbieżności
- Wybierz pozycję *nowe żądanie obsługi* ( *Pomoc techniczna + grupa rozwiązywania problemów* ) 
- Zostanie wyświetlone nowe okno z automatycznie wypełnionymi informacjami o subskrypcji platformy Azure i zasobach platformy Azure
- Wprowadź *Podsumowanie* (na przykład "Zwiększ limit żądań współbieżności monitora STT")
- W obszarze *typ problemu* wybierz pozycję "problemy z przydziałem lub subskrypcją"
- W przypadku *wystąpienia problemu* pojawiły się wybierz opcję:
  - "Przydział lub współbieżne żądania zwiększają się" — dla żądania zwiększenia
  - "Sprawdzanie poprawności przydziału lub użycia" w celu sprawdzenia istniejącego limitu
- Kliknij przycisk *Dalej: rozwiązania*
- Kontynuuj, aby kontynuować tworzenie żądania
- Na karcie *szczegóły* wprowadź wartość w polu *Opis* :
  - należy pamiętać, że żądanie dotyczy przydziału **zamiany mowy na tekst**
  - Model **podstawowy** lub **niestandardowy**
  - Informacje o zasobach platformy Azure [zebrane przed](#have-the-required-information-ready) 
  - Ukończ wprowadzanie wymaganych informacji, a następnie kliknij przycisk *Utwórz* na karcie *Przegląd + tworzenie*
  - Zanotuj numer żądania pomocy technicznej w obszarze Azure Portal powiadomienia. Wkrótce skontaktujemy się z Tobą w celu dalszej obróbki

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>Zamiana mowy na tekst: przykład najlepszego rozwiązania wzorca obciążenia
W tym przykładzie przedstawiono podejście, które zalecamy, aby zmniejszyć możliwe ograniczenie przepustowości z powodu [trwającego skalowania](#detailed-description-quota-adjustment-and-best-practices)automatycznego. Nie jest to "dokładne przepis", ale tylko szablon, który Zapraszamy do wykonania i dostosowania w razie potrzeby.

Załóżmy, że zasób mowy ma limit żądań współbieżnych ustawiony na 300. Rozpocznij obciążenie z 20 współbieżnych połączeń i zwiększ obciążenie przez 20 jednoczesnych połączeń co 1,5 – 2 minut. Kontroluj odpowiedzi usługi i Implementuj logikę, która powraca (zmniejsza obciążenie), jeśli otrzymasz zbyt wiele kodów odpowiedzi 429. Następnie ponów próbę w przypadku wzorca 1-2-4-4 minut. (To jest ponowienie przyrostu obciążenia w 1 min, jeśli nadal nie działa, a następnie w 2 min itd.)

Ogólnie rzecz biorąc, zdecydowanie zaleca się przetestowanie obciążenia i wzorców obciążenia przed przejściem do środowiska produkcyjnego.

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>Zamiana tekstu na mowę: zwiększenie liczby równoczesnych żądań transkrypcji dla niestandardowego głosu
Domyślnie liczba współbieżnych żądań dla niestandardowego punktu końcowego głosu jest ograniczona do 10. W przypadku warstwy cenowej standardowa można zwiększyć tę kwotę. Przed przesłaniem żądania upewnij się, że znasz materiał w [tej sekcji](#detailed-description-quota-adjustment-and-best-practices) i zapoznaj się z [najlepszymi rozwiązaniami](#general-best-practices-to-mitigate-throttling-during-autoscaling).

Zwiększenie limitu współbieżnych **żądań nie wpływa bezpośrednio na** koszty. Usługi mowy korzystają z modelu "płatność tylko za rzeczywiste użycie". Limit definiuje, jak wysoka wydajność może być skalowana przed rozpoczęciem ograniczania żądań.

Istniejąca wartość parametru limitu współbieżnych żądań **nie** jest widoczna za pośrednictwem Azure Portal, Command-Line narzędzi lub żądań interfejsu API. Aby zweryfikować istniejącą wartość, Utwórz żądanie pomocy technicznej platformy Azure.

>[!NOTE]
>[Kontenery mowy](speech-container-howto.md) nie wymagają zwiększenia liczby równoczesnych żądań, ponieważ kontenery są ograniczone tylko przez procesory, w których są hostowane sprzęt.

#### <a name="prepare-the-required-information"></a>Przygotuj wymagane informacje:
Aby można było utworzyć żądanie zwiększenia, należy podać region wdrożenia i niestandardowy identyfikator punktu końcowego. Aby to zrobić, wykonaj następujące czynności: 

- Przejdź do portalu [mowy Studio](https://speech.microsoft.com/)
- Zaloguj się w razie potrzeby
- Przejdź do *niestandardowego głosu*
- Wybierz projekt
- Przejdź do *wdrożenia*
- Wybierz wymagany punkt końcowy
- Skopiuj i Zapisz wartości następujących pól:
    - **Region usługi** (region punktu końcowego)
    - **Identyfikator punktu końcowego**
  
#### <a name="create-and-submit-support-request"></a>Utwórz i prześlij żądanie pomocy technicznej
Zainicjuj zwiększenie limitu współbieżnych żądań dla zasobu lub w razie potrzeby Sprawdź dzisiejszy limit przez przesłanie żądania pomocy technicznej:

- Upewnij się, że masz [wymagane informacje](#prepare-the-required-information)
- Przejdź do [Azure Portal](https://portal.azure.com/)
- Wybierz zasób mowy, dla którego chcesz zwiększyć (lub sprawdzić) limit żądań współbieżności
- Wybierz pozycję *nowe żądanie obsługi* ( *Pomoc techniczna + grupa rozwiązywania problemów* ) 
- Zostanie wyświetlone nowe okno z automatycznie wypełnionymi informacjami o subskrypcji platformy Azure i zasobach platformy Azure
- Wprowadź *Podsumowanie* (na przykład "Zwiększ limit żądań współbieżności dla niestandardowego punktu końcowego")
- W obszarze *typ problemu* wybierz pozycję "problemy z przydziałem lub subskrypcją"
- W przypadku *wystąpienia problemu* pojawiły się wybierz opcję:
  - "Przydział lub współbieżne żądania zwiększają się" — dla żądania zwiększenia
  - "Sprawdzanie poprawności przydziału lub użycia" w celu sprawdzenia istniejącego limitu
- Kliknij przycisk *Dalej: rozwiązania*
- Kontynuuj, aby kontynuować tworzenie żądania
- Na karcie *szczegóły* wprowadź wartość w polu *Opis* :
  - Uwaga, że żądanie dotyczy przydziału **zamiany tekstu na mowę**
  - Informacje o zasobach platformy Azure [zebrane przed](#prepare-the-required-information) 
  - Ukończ wprowadzanie wymaganych informacji, a następnie kliknij przycisk *Utwórz* na karcie *Przegląd + tworzenie*
  - Zanotuj numer żądania pomocy technicznej w obszarze Azure Portal powiadomienia. Wkrótce skontaktujemy się z Tobą w celu dalszej obróbki

