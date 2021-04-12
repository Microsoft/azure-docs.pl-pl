---
title: Diagnozowanie problemów z wydajnością grafiki Pulpit zdalny — Azure
description: W tym artykule opisano, jak używać liczników grafiki RemoteFX w sesjach protokołu pulpitu zdalnego do diagnozowania problemów z wydajnością za pomocą grafiki na pulpicie wirtualnym systemu Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: fc1bb9d6dbdb024fba6544c25951ba94384132b0
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445740"
---
# <a name="diagnose-graphics-performance-issues-in-remote-desktop"></a>Diagnozowanie problemów z wydajnością grafiki w programie Pulpit zdalny

Aby zdiagnozować problemy z jakością środowiska w sesjach zdalnych, liczniki zostały podane w sekcji grafiki funkcji RemoteFX monitora wydajności. Ten artykuł ułatwia identyfikowanie i rozwiązywanie wąskich gardeł wydajności związanych z grafikią podczas sesji Remote Desktop Protocol (RDP) przy użyciu tych liczników.

## <a name="find-your-remote-session-name"></a>Znajdź nazwę sesji zdalnej

Do identyfikacji liczników wydajności grafiki potrzebna jest nazwa sesji zdalnej. Postępuj zgodnie z instrukcjami w tej sekcji, aby zidentyfikować wystąpienie każdego licznika.

1. Otwórz wiersz polecenia systemu Windows z sesji zdalnej.
2. Uruchom polecenie **qwinsta** i Znajdź nazwę sesji.
    - Jeśli sesja jest hostowana na maszynie wirtualnej z obsługą wielu sesji (VM): wystąpienie każdego licznika jest sufiksem o tej samej liczbie, która ma sufiks nazwy sesji, na przykład "RDP-TCP 37".
    - Jeśli sesja jest hostowana na maszynie wirtualnej obsługującej wirtualne jednostki przetwarzania grafiki (vGPU): wystąpienie każdego licznika jest przechowywane na serwerze, a nie na maszynie wirtualnej. Wystąpienia liczników zawierają nazwę maszyny wirtualnej zamiast numeru w nazwie sesji, na przykład "Win8 Enterprise VM".

>[!NOTE]
> Chociaż liczniki mają funkcję RemoteFX w swoich nazwach, obejmują również grafikę pulpitu zdalnego w scenariuszach vGPU.

## <a name="access-performance-counters"></a>Dostęp do liczników wydajności

Po ustaleniu nazwy sesji zdalnej postępuj zgodnie z tymi instrukcjami, aby zebrać liczniki wydajności grafiki RemoteFX dla sesji zdalnej.

1. Wybierz kolejno pozycje **Uruchom**  >  **Narzędzia administracyjne**  >  **Monitor wydajności**.
2. W oknie dialogowym **Monitor wydajności** rozwiń węzeł **Narzędzia do monitorowania**, wybierz pozycję **Monitor wydajności**, a następnie wybierz pozycję **Dodaj**.
3. W oknie dialogowym **Dodawanie liczników** z listy **dostępne liczniki** rozwiń sekcję dotyczącą grafiki RemoteFX.
4. Wybierz liczniki do monitorowania.
5. Na liście **wystąpienia wybranego obiektu** wybierz określone wystąpienia do monitorowania dla wybranych liczników, a następnie wybierz pozycję **Dodaj**. Aby zaznaczyć wszystkie dostępne wystąpienia liczników, zaznacz opcję **wszystkie wystąpienia**.
6. Po dodaniu liczników wybierz **przycisk OK**.

Wybrane liczniki wydajności będą wyświetlane na ekranie monitora wydajności.

>[!NOTE]
>Każda aktywna sesja na hoście ma własne wystąpienie każdego licznika wydajności.

## <a name="diagnose-issues"></a>Diagnozowanie problemów

Problemy z wydajnością związane z grafikią zwykle dzielą się na cztery kategorie:

- Niska szybkość klatek
- Miejsca losowe
- Duże opóźnienie wejścia
- Niska jakość ramki

### <a name="addressing-low-frame-rate-random-stalls-and-high-input-latency"></a>Rozwiązywanie niskich szybkości klatek, losowych miejsc i dużych opóźnień wejścia

Najpierw sprawdź ramki wyjściowe/sekundę. Mierzy ona liczbę ramek udostępnianych klientowi. Jeśli ta wartość jest mniejsza od licznika ramki wejściowe/sekundę, ramki są pomijane. Aby zidentyfikować wąskie gardło, należy użyć klatek pominiętych/sekundę.

Istnieją trzy typy klatek pominiętych/sekund:

- Pominięte klatki/sekundę (niewystarczające zasoby serwera)
- Pominięte klatki/sekundę (niewystarczające zasoby sieciowe)
- Pominięte klatki/sekundę (niewystarczające zasoby klienta)

Wysoka wartość dla wszystkich klatek pominiętych/sekund oznacza, że problem jest związany z zasobem śledzonym przez licznik. Na przykład jeśli klient nie dekoduje i przedstawia ramek z tą samą szybkością, serwer oferuje ramki, pominięte klatki/sekundę (niewystarczające zasoby klienta) będą wysokie.

Jeśli licznik ramki wyjściowe/drugi dopasowuje ramki wejściowe/sekundę, ale nadal zauważysz nietypowe opóźnienia lub ogiera, średni czas kodowania może być przyczyna. Kodowanie jest procesem synchronicznym, który występuje na serwerze w scenariuszu pojedynczej sesji (vGPU) i na maszynie wirtualnej w scenariuszu obejmującym wiele sesji. Średni czas kodowania powinien mieć mniej niż 33 MS. Jeśli średni czas kodowania jest równy 33 MS, ale nadal występują problemy z wydajnością, może wystąpić problem z aplikacją lub systemem operacyjnym, z którego korzystasz.

Aby uzyskać więcej informacji o diagnozowaniu problemów związanych z aplikacjami, zobacz [liczniki wydajności opóźnienia danych wejściowych użytkownika](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/).

Ponieważ protokół RDP obsługuje średni czas kodowania 33 MS, obsługuje wejściowy współczynnik ramek do 30 klatek na sekundę. Należy pamiętać, że 33 MS to maksymalna obsługiwana szybkość klatek. W wielu przypadkach częstotliwość klatek napotkanych przez użytkownika będzie niższa, w zależności od tego, jak często ramka jest dostarczana do protokołu RDP przez źródło. Na przykład zadania, takie jak obserwowanie filmu wideo, wymagają pełnej szybkości klatek wejścia o 30 klatek/s, ale mniej znacznie intensywnie korzystających z nich zadań, takich jak rzadko edytowane są wyniki w postaci znacznie mniejszej wartości klatek wejścia/s, bez obniżenia jakości doświadczenia użytkownika.

### <a name="addressing-poor-frame-quality"></a>Rozwiązywanie słabej jakości ramki

Użyj licznika jakości ramki, aby zdiagnozować problemy z jakością ramki. Ten licznik przedstawia jakość ramki wyjściowej jako wartość procentową jakości ramki źródłowej. Utrata jakości może być spowodowana funkcją RemoteFX lub źródłem grafiki. Jeśli funkcja RemoteFX spowodowała utratę jakości, przyczyną problemu może być brak zasobów sieciowych lub serwerowych w celu wysłania zawartości o wyższej wierności.

## <a name="mitigation"></a>Ograniczanie ryzyka

Jeśli zasoby serwera powodują wąskie gardło, wypróbuj jedną z następujących metod, aby zwiększyć wydajność:

- Zmniejsz liczbę sesji na hosta.
- Zwiększ ilość pamięci i zasobów obliczeniowych na serwerze.
- Porzuć rozwiązanie.

Jeśli zasoby sieciowe powodują wąskie gardło, wypróbuj jedną z następujących metod, aby zwiększyć dostępność sieci na sesję:

- Zmniejsz liczbę sesji na hosta.
- Użyj sieci o wyższej przepustowości.
- Porzuć rozwiązanie.

Jeśli zasoby klienta powodują wąskie gardło, wypróbuj jedną z następujących metod, aby zwiększyć wydajność:

- Zainstaluj najnowszego klienta Pulpit zdalny.
- Zwiększ ilość pamięci i zasobów obliczeniowych na komputerze klienckim.

> [!NOTE]
> Obecnie nie są obsługiwane liczniki źródłowe/sekundę. Na razie klatki źródłowe/drugi licznika zawsze będą wyświetlać 0.

## <a name="next-steps"></a>Następne kroki

- Aby utworzyć maszynę wirtualną platformy Azure zoptymalizowaną pod kątem procesora GPU, zobacz [Konfigurowanie przyspieszania procesora GPU dla środowiska pulpitu wirtualnego systemu Windows](configure-vm-gpu.md).
- Aby zapoznać się z omówieniem rozwiązywania problemów i śledzenia eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczną](troubleshoot-set-up-overview.md).
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu systemu Windows](environment-setup.md).
