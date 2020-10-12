---
title: Zestaw Batch Processing Kit dla kontenerów mowy
titleSuffix: Azure Cognitive Services
description: Użyj zestawu Batch Processing, aby skalować żądania kontenerów mowy.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 3cd6febfc774b214a8c1ae8553e6c127c4f452fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319082"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Zestaw Batch Processing Kit dla kontenerów mowy

Użyj zestawu Batch Processing, aby uzupełnić i skalować obciążenia w kontenerach mowy. To narzędzie typu "open source" dostępne jako kontener ułatwia transkrypcję wsadową dla dużej liczby plików audio w dowolnej liczbie lokalnych i opartych na chmurze punktach końcowych kontenerów mowy. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Diagram przedstawiający przykładowy przepływ pracy kontenera zestawu Partia zadań.":::

Kontener zestawu Batch jest dostępny bezpłatnie w witrynie [GitHub](https://github.com/microsoft/batch-processing-kit) i w usłudze   [Docker Hub](https://hub.docker.com/r/batchkit/speech-batch-kit/tags). [Opłaty są naliczane](speech-container-howto.md#billing) tylko za używane kontenery mowy.

| Cecha  | Opis  |
|---------|---------|
| Dystrybucja plików audio wsadowych     | Automatyczne wysyłanie dużej liczby plików do lokalnych lub opartych na chmurze punktów końcowych kontenerów mowy. Pliki mogą znajdować się na dowolnym woluminie zgodnym ze standardem POSIX, w tym z systemem plików sieciowych.       |
| Integracja z zestawem SDK mowy | Przekazuj typowe flagi do zestawu Speech SDK, w tym: n-najlepsze z nich, diarization, język, maskowanie wulgarności.  |
|Tryby uruchamiania     | Uruchom klienta usługi Batch raz, w sposób ciągły w tle lub Utwórz punkty końcowe HTTP dla plików audio.         |
| Odporność na uszkodzenia | Automatycznie ponawiaj próbę i Kontynuuj transkrypcję bez utraty postępu i Odróżniaj, które błędy mogą, i nie może być ponowione. |
| Wykrywanie dostępności punktu końcowego | Jeśli punkt końcowy będzie niedostępny, klient usługi Batch będzie kontynuował jego przepisywania przy użyciu innych punktów końcowych kontenera. Po ponownym udostępnieniu klient będzie automatycznie rozpoczynał korzystanie z punktu końcowego.   |
| Wymiana gorąca punktu końcowego | Dodaj, Usuń lub Modyfikuj punkty końcowe kontenera mowy w czasie wykonywania bez przerywania postępów partii. Aktualizacje są natychmiastowe. |
| Rejestrowanie w czasie rzeczywistym | Rejestrowanie próbnych żądań, sygnatur czasowych i przyczyn niepowodzenia w czasie rzeczywistym przy użyciu plików dziennika zestawu Speech SDK dla każdego pliku dźwiękowego. |

## <a name="get-the-container-image-with-docker-pull"></a>Pobierz obraz kontenera za pomocą `docker pull`

Użyj [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) polecenie, aby pobrać najnowszy kontener zestawu Batch Kit.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego

Klient wsadowy pobiera plik konfiguracji YAML, który określa punkty końcowe kontenera on-Premium. Poniższy przykład może być zapisany `/mnt/my_nfs/config.yaml` w, który jest używany w poniższych przykładach. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

Ten YAML przykład określa trzy kontenery mowy na trzech hostach. Pierwszy host jest określony przez adres IPv4, drugi jest uruchomiony na tej samej maszynie wirtualnej, co klient usługi Batch, a trzeci kontener jest określony przez nazwę hosta DNS innej maszyny wirtualnej. `concurrency`Wartość określa maksymalną liczbę równoczesnych transkrypcji plików, które mogą być uruchamiane w tym samym kontenerze. `rtf`Wartość (współczynnik czasu rzeczywistego) jest opcjonalna i może służyć do dostrajania wydajności.
 
Klient usługi Batch może dynamicznie wykryć, czy punkt końcowy stanie się niedostępny (na przykład z powodu ponownego uruchomienia kontenera lub problemu z siecią) i kiedy znów stanie się dostępny. Żądania transkrypcji nie będą wysyłane do kontenerów, które są niedostępne, a klient będzie kontynuował korzystanie z innych dostępnych kontenerów. Punkty końcowe można dodawać, usuwać lub edytować w dowolnym momencie bez przerywania postępów w usłudze Batch.


## <a name="run-the-batch-processing-container"></a>Uruchamianie kontenera przetwarzania wsadowego
  
> [!NOTE] 
> * Ten przykład używa tego samego katalogu ( `/my_nfs` ) dla pliku konfiguracji oraz katalogów danych wejściowych, wyjściowych i dzienników. Dla tych folderów można użyć katalogów zainstalowanych w środowisku hostowanym lub w systemie plików NFS.
> * Uruchomienie klienta przy użyciu programu `–h` spowoduje wyświetlenie listy dostępnych parametrów wiersza polecenia i ich wartości domyślnych. 


#### <a name="linux"></a>[Linux](#tab/linux)
Użyj polecenia Docker, `run` Aby uruchomić kontener. Spowoduje to uruchomienie interaktywnej powłoki wewnątrz kontenera.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

Aby uruchomić klienta programu Batch:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

Aby uruchomić klienta i kontener w usłudze Batch w pojedynczym poleceniu:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

#### <a name="windows"></a>[Windows](#tab/windows)

Aby uruchomić klienta i kontener w usłudze Batch w pojedynczym poleceniu:

```Docker
docker run --rm -ti -v   c:\my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config  /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config

```

---


Klient zacznie działać. Jeśli plik dźwiękowy został już uzyskanego w poprzednim uruchomieniu, klient automatycznie pominie ten plik. Pliki są wysyłane z automatycznym ponowieniem próby w przypadku wystąpienia błędów przejściowych. można odróżnić między innymi błędy, które mają być ponownie podejmowane przez klienta. Po błędzie transkrypcji klient będzie kontynuował transkrypcję i może ponowić próbę bez utraty postępu.  

## <a name="run-modes"></a>Tryby uruchamiania 

Zestaw Batch Processing Kit oferuje trzy tryby, przy użyciu `--run-mode` parametru.

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` Tryb przekształca pojedynczej partii plików audio (z katalogu wejściowego i opcjonalnej listy plików) do folderu wyjściowego.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Diagram przedstawiający przykładowy przepływ pracy kontenera zestawu Partia zadań.":::

1. Zdefiniuj punkty końcowe kontenera mowy, które będą używane przez klienta programu Batch w `config.yaml` pliku. 
2. Umieść pliki audio na potrzeby transkrypcji w katalogu wejściowym.  
3. Wywołaj kontener w katalogu, który zacznie przetwarzać pliki. Jeśli plik audio został już uzyskanego w poprzednim uruchomieniu z tym samym katalogiem wyjściowym (taka sama nazwa pliku i suma kontrolna), klient pominie ten plik. 
4. Pliki są wysyłane do punktów końcowych kontenera z kroku 1.
5. Dzienniki i dane wyjściowe kontenera mowy są zwracane do określonego katalogu wyjściowego. 

#### <a name="daemon"></a>[Demon](#tab/daemon)

> [!TIP]
> Jeśli w tym samym czasie dodawane są wiele plików, można zwiększyć wydajność, zamiast dodawać je w regularnych odstępach czasu.

`DAEMON` Tryb przekształca istniejące pliki w danym folderze i ciągle przekształca nowe pliki audio w miarę ich dodawania.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Diagram przedstawiający przykładowy przepływ pracy kontenera zestawu Partia zadań.":::

1. Zdefiniuj punkty końcowe kontenera mowy, które będą używane przez klienta programu Batch w `config.yaml` pliku. 
2. Wywołaj kontener w katalogu wejściowym. Klient usługi Batch rozpocznie monitorowanie katalogu dla plików przychodzących. 
3. Skonfiguruj dostarczanie ciągłego przesyłania plików audio do katalogu wejściowego. Jeśli plik audio został już uzyskanego w poprzednim uruchomieniu z tym samym katalogiem wyjściowym (taka sama nazwa pliku i suma kontrolna), klient pominie ten plik. 
4. Gdy zostanie wykryta próba zapisu pliku lub POSIX, kontener zostanie wyzwolony w celu odpowiedzi.
5. Pliki są wysyłane do punktów końcowych kontenera z kroku 1.
6. Dzienniki i dane wyjściowe kontenera mowy są zwracane do określonego katalogu wyjściowego. 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` Tryb to tryb serwera interfejsu API, który zapewnia podstawowy zestaw punktów końcowych HTTP na potrzeby przesłania wsadowego plików audio, sprawdzania stanu i długiego sondowania. Włącza również programistyczne użycie przy użyciu rozszerzenia modułu języka Python lub importowania jako modułu podrzędnego.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="Diagram przedstawiający przykładowy przepływ pracy kontenera zestawu Partia zadań.":::

1. Zdefiniuj punkty końcowe kontenera mowy, które będą używane przez klienta programu Batch w `config.yaml` pliku. 
2. Wyślij żądanie HTTP do jednego z punktów końcowych serwera interfejsu API. 
        
    |Punkt końcowy  |Opis  |
    |---------|---------|
    |`/submit`     | Punkt końcowy do tworzenia nowych żądań wsadowych.        |
    |`/status`     | Punkt końcowy do sprawdzania stanu żądania usługi Batch. Połączenie pozostanie otwarte do momentu zakończenia zadania wsadowego.       |
    |`/watch`     | Punkt końcowy do korzystania z długotrwałego sondowania protokołu HTTP do momentu zakończenia zadania wsadowego.        |

3. Pliki audio są przekazywane z katalogu wejściowego. Jeśli plik audio został już uzyskanego w poprzednim uruchomieniu z tym samym katalogiem wyjściowym (taka sama nazwa pliku i suma kontrolna), klient pominie ten plik. 
4. Jeśli żądanie zostanie wysłane do `/submit` punktu końcowego, pliki są wysyłane do punktów końcowych kontenera z kroku 1.
5. Dzienniki i dane wyjściowe kontenera mowy są zwracane do określonego katalogu wyjściowego. 

---

## <a name="logging"></a>Rejestrowanie

> [!NOTE]
> Klient programu Batch może okresowo zastąpić plik *Run. log* , jeśli jest zbyt duży.

Klient tworzy plik *Run. log* w katalogu określonym przez `-log_folder` argument w `run` poleceniu Docker. Dzienniki są domyślnie przechwytywane na poziomie debugowania. Te same dzienniki są wysyłane do `stdout/stderr` i filtrowane w zależności od `-log_level` argumentu. Ten dziennik jest wymagany tylko w przypadku debugowania lub w przypadku konieczności wysłania śledzenia w celu uzyskania pomocy technicznej. Folder rejestrowania zawiera również dzienniki zestawu Speech SDK dla każdego pliku dźwiękowego.

Katalog wyjściowy określony przez `-output_folder` będzie zawierać *run_summary.jsw*   pliku, który jest okresowo zapisywana co 30 sekund lub po zakończeniu nowych transkrypcji. Tego pliku można użyć do sprawdzenia postępu w trakcie wykonywania zadania wsadowego. Zawiera również dane statystyczne końcowego uruchomienia i końcowy stan każdego pliku po zakończeniu wykonywania zadania wsadowego. Partia jest ukończona, gdy proces ma czyste wyjście. 

## <a name="next-steps"></a>Następne kroki

* [Jak instalować i uruchamiać kontenery](speech-container-howto.md)
