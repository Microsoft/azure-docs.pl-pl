---
title: Sprawdzanie metryk przepływności i opóźnień dla konta magazynu w witrynie Azure Portal | Microsoft Docs
description: Dowiedz się, w jaki sposób sprawdzić metryki przepływności i opóźnień dla konta magazynu w portalu.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 708a2369e5ba21f47f3d3fea509654b0385363f7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912318"
---
# <a name="verify-throughput-and-latency-metrics-for-a-storage-account"></a>Sprawdzanie metryk przepływności i opóźnień dla konta magazynu

Ten samouczek to czwarta i ostatnia część serii. W poprzednich samouczkach omówiono, w jaki sposób przekazywać duże ilości danych losowych na konto usługi Azure Storage i pobierać je. W tym samouczku dowiesz się, jak korzystać z metryk w celu sprawdzania przepustowości i opóźnienia w witrynie Azure Portal.

Część czwarta serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie wykresów na portalu Azure Portal
> * Weryfikowanie metryk przepływności i opóźnienia

[Metryki usługi Azure Storage](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) używają usługi Azure Monitor, aby zapewnić spójny widok wydajności i dostępności konta magazynu.

## <a name="configure-metrics"></a>Konfigurowanie metryk

Przejdź do pozycji **Metryki (wersja zapoznawcza)** w obszarze **USTAWIENIA** na koncie magazynu.

Wybierz pozycję Blob z listy rozwijanej **USŁUGA PODRZĘDNA**.

W obszarze **METRYKA** wybierz jedną z metryk z poniższej tabeli:

Następujące metryki umożliwiają sprawdzenie opóźnienia i przepływności aplikacji. Metryki skonfigurowane w portalu przedstawiają wartości średnie z 1 minuty. Jeśli transakcja zakończyła się w połowie minuty, że dane minutowe są średnio średnie. W aplikacji czas operacji przekazywania i pobierania został zmierzony, a rzeczywisty czas, jaki zajęło przekazanie i pobranie plików, został podany w danych wyjściowych. Tych informacji można użyć w połączeniu z metrykami w portalu, aby uzyskać pełny obraz przepływności.

|Metric|Definicja|
|---|---|
|**Opóźnienie E2E dla powodzenia**|Średnie całkowite opóźnienie dla żądań zakończonych powodzeniem kierowanych do usługi magazynu lub określonej operacji interfejsu API. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|
|**Opóźnienie serwera sukcesu**|Średni czas potrzebny na przetworzenie żądania zakończonego powodzeniem przez usługę Azure Storage. Ta wartość nie obejmuje opóźnienia sieci określonego przez wartość Opóźnienie E2E dla powodzenia. |
|**Transakcje**|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone zarówno powodzeniem, jak i niepowodzeniem, a także żądania, które wywołały błędy. W tym przykładzie rozmiar bloku został ustawiony na 100 MB. W tym przypadku każdy blok 100 MB jest traktowany jako jedna transakcja.|
|**Ruch przychodzący**|Ilość danych przychodzących. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure. |
|**Ruch wychodzący**|Ilość danych wychodzących. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu. |

Wybierz opcję **Ostatnie 24 godziny (automatycznie)** obok pozycji **Czas**. Wybierz opcję **Ostatnia godzina** i **Minuta** w obszarze **Stopień szczegółowości czasu**, a następnie kliknij przycisk **Zastosuj**.

![Metryki konta magazynu](./media/storage-blob-scalable-app-verify-metrics/figure1.png)

Wykresy mogą mieć przypisaną więcej niż jedną metrykę, ale przypisanie więcej niż jednej metryki wyłącza opcję grupowania według wymiarów.

## <a name="dimensions"></a>Wymiary

[Wymiary](./monitor-blob-storage-reference.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#metrics-dimensions) umożliwiają głębszą analizy wykresów i uzyskanie bardziej szczegółowych informacji. Różne metryki mają różne wymiary. Jednym z dostępnych wymiarów jest **Nazwa interfejsu API**. Ten wymiar dzieli wykres na poszczególne wywołania interfejsu API. Pierwszy obraz poniżej przedstawia przykładowy wykres łącznej liczby transakcji dla konta magazynu. Drugi obraz pokazuje ten sam wykres, ale z wybranym wymiarem Nazwa interfejsu API. Jak widać, wymieniona jest każda transakcja, co daje więcej informacji na temat liczby utworzonych wywołań według nazwy interfejsu API.

![Metryki konta magazynu — transakcje bez wymiaru](./media/storage-blob-scalable-app-verify-metrics/transactionsnodimensions.png)

![Metryki konta magazynu — transakcje](./media/storage-blob-scalable-app-verify-metrics/transactions.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć. W tym celu wybierz grupę zasobów maszyny wirtualnej i kliknij pozycję Usuń.

## <a name="next-steps"></a>Następne kroki

W czwartej części serii zawarto informacje na temat wyświetlania metryk. Opisano między innymi następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie wykresów na portalu Azure Portal
> * Weryfikowanie metryk przepływności i opóźnienia

Kliknij ten link, aby wyświetlić wstępnie skompilowane przykłady magazynu.

> [!div class="nextstepaction"]
> [Azure storage script samples (Przykładowe skrypty dla usługi Azure Storage)](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-blob-scalable-app-download-files.md