---
title: Łączenie danych zdarzeń zabezpieczeń systemu Windows z platformą Azure Microsoft Docs
description: Dowiedz się, jak za pomocą łącznika zdarzeń zabezpieczeń przesyłać strumieniowo wszystkie zdarzenia zabezpieczeń z systemów Windows do obszaru roboczego wskaźnikowego platformy Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: bc75ee64174957ad6486146b4da6f8a66a2120e5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570561"
---
# <a name="connect-windows-security-events"></a>Łączenie ze zdarzeniami zabezpieczeń systemu Windows 

Łącznik zdarzeń zabezpieczeń umożliwia przesyłanie strumieniowe wszystkich zdarzeń zabezpieczeń z systemów Windows (serwerów i stacji roboczych, fizycznych i wirtualnych) do obszaru roboczego wskaźnikowego platformy Azure. Dzięki temu można wyświetlać zdarzenia zabezpieczeń systemu Windows na pulpitach nawigacyjnych, korzystać z nich w tworzeniu niestandardowych alertów i polegać na nich w celu ulepszania dochodzeń, dzięki czemu możesz uzyskać więcej informacji o sieci organizacji i rozszerzać możliwości operacji związanych z bezpieczeństwem. Możesz wybrać, które zdarzenia mają być przesyłane strumieniowo spośród następujących zestawów: <a name="event-sets"></a>

- **Wszystkie zdarzenia** — wszystkie zdarzenia zabezpieczeń systemu Windows i funkcji AppLocker.
- **Wspólny** — standardowy zestaw zdarzeń do celów inspekcji. W tym zestawie znajduje się pełny dziennik inspekcji użytkownika. Na przykład zawiera zdarzenia logowania użytkownika i logowania użytkowników (identyfikatory zdarzeń 4624, 4634). Istnieją także akcje inspekcji, takie jak zmiany grupy zabezpieczeń, kluczowego kontrolera domeny i inne typy zdarzeń w wierszu z zaakceptowanymi najlepszymi rozwiązaniami.

    **Typowy** zestaw zdarzeń może zawierać niektóre typy zdarzeń, które nie są tak popularne.  Wynika to z faktu, że głównym punktem **wspólnego** zestawu jest zmniejszenie ilości zdarzeń na bardziej możliwy do zarządzania, utrzymując jednocześnie pełną funkcję dziennika inspekcji.

- **Minimalny** — niewielki zestaw zdarzeń, które mogą wskazywać na potencjalne zagrożenia. Ten zestaw nie zawiera pełnego dziennika inspekcji. Obejmuje tylko zdarzenia, które mogą wskazywać na pomyślne naruszenie, oraz inne ważne zdarzenia, które mają bardzo niskie wskaźniki. Przykładowo zawiera powodzenie i nieudane logowania użytkowników (identyfikatory zdarzeń 4624, 4625), ale nie zawiera informacji o wylogowaniu (4634), które nie mają znaczenia dla inspekcji, a jednocześnie ma stosunkowo duże ilości danych. Większość ilości danych tego zestawu składa się z zdarzeń logowania i zdarzeń tworzenia procesów (Identyfikator zdarzenia 4688).

- **Brak** — brak zdarzeń zabezpieczeń lub funkcji AppLocker. (To ustawienie służy do wyłączania łącznika).

    Poniższa lista zawiera kompletny podział identyfikatorów zdarzeń związanych z zabezpieczeniami i blokowaniem aplikacji dla każdego zestawu:

    | Zestaw zdarzeń | Identyfikatory zebranych zdarzeń |
    | --- | --- |
    | **Minimalny** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024 |
    | **Wspólne** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719 , 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933 , 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> Zbieranie zdarzeń zabezpieczeń w kontekście jednego obszaru roboczego można skonfigurować z poziomu Azure Security Center lub platformy Azure, ale nie obu. Jeśli dołączysz wskaźnik platformy Azure do obszaru roboczego, który już otrzymuje alerty usługi Azure Defender z Azure Security Center i jest ustawiony na zbieranie zdarzeń zabezpieczeń, masz dwie opcje:
> - Pozostaw zbieranie zdarzeń zabezpieczeń w Azure Security Center. Będzie można wykonywać zapytania i analizować te zdarzenia na platformie Azure, a także w usłudze Azure Defender. Nie będzie jednak można monitorować stanu łączności łącznika ani zmieniać jego konfiguracji na platformie Azure — wskaźnik. Jeśli jest to ważne dla Ciebie, weź pod uwagę drugą opcję.
>
> - [Wyłącz zbieranie zdarzeń zabezpieczeń](../security-center/security-center-enable-data-collection.md) w Azure Security Center, a następnie Dodaj łącznik zdarzeń zabezpieczeń na platformie Azure. Podobnie jak w przypadku pierwszej opcji, można wysyłać zapytania i analizować zdarzenia zarówno z platformy Azure, jak i usługi Azure Defender/ASC, ale teraz będzie można monitorować stan łączności łącznika lub zmieniać jego konfigurację w systemach i tylko na platformie Azure.

## <a name="set-up-the-windows-security-events-connector"></a>Konfigurowanie łącznika zdarzeń zabezpieczeń systemu Windows

Aby zebrać zdarzenia zabezpieczeń systemu Windows na platformie Azure — wskaźnik:

1. Z menu nawigacyjnego wskaźnikowego platformy Azure wybierz pozycję **Łączniki danych**. Z listy łączników kliknij pozycję **zdarzenia zabezpieczeń**, a następnie na przycisk **Otwórz stronę łącznika** w prawym dolnym rogu. Następnie postępuj zgodnie z instrukcjami wyświetlanymi na ekranie na karcie **instrukcje** , zgodnie z opisem w pozostałej części tej sekcji.

1. Sprawdź, czy masz odpowiednie uprawnienia, zgodnie z opisem w sekcji **wymagania wstępne** na stronie łącznika.

1. Pobierz i zainstaluj [agenta log Analytics](../azure-monitor/agents/log-analytics-agent.md) (znanego również jako Microsoft Monitoring Agent lub MMA) na maszynach, dla których chcesz przesyłać strumieniowo zdarzenia zabezpieczeń do usługi Azure wskaźnikowej.

    W przypadku usługi Azure Virtual Machines:
    
    1. Kliknij pozycję **Zainstaluj agenta na maszynie wirtualnej platformy Azure systemu Windows**, a następnie w wyświetlonym poniżej obszarze linku.
    1. Dla każdej maszyny wirtualnej, która ma zostać nawiązane połączenie, kliknij jej nazwę na liście, która pojawia się po prawej stronie, a następnie kliknij przycisk **Połącz**.

    W przypadku maszyn z systemem Windows nienależących do platformy Azure (fizycznych, wirtualnych w Premium lub wirtualnych w innej chmurze):

    1. Kliknij pozycję **Zainstaluj agenta na komputerze spoza systemu Windows**, a następnie w wyświetlonym obszarze linku.
    1. Kliknij odpowiednie linki do pobrania, które pojawiają się po prawej stronie w obszarze **komputery z systemem Windows**.
    1. Używając pobranego pliku wykonywalnego, Zainstaluj agenta w wybranych systemach Windows i skonfiguruj go przy użyciu **identyfikatora obszaru roboczego i kluczy** , które są wyświetlane poniżej linków pobierania wymienionych powyżej.

    > [!NOTE]
    >
    > Aby zezwolić na używanie systemów Windows bez konieczności łączności z Internetem w celu przesyłania strumieniowego zdarzeń do platformy Azure, należy pobrać i zainstalować **bramę pakietu OMS** na oddzielnym komputerze przy użyciu linku w prawym dolnym rogu, aby działać jako serwer proxy.  Nadal trzeba będzie zainstalować agenta Log Analytics w każdym systemie Windows, którego zdarzenia mają być zbierane.
    >
    > Aby uzyskać więcej informacji na temat tego scenariusza, zobacz dokumentację usługi [ **log Analytics Gateway**](../azure-monitor/agents/gateway.md).

    Dodatkowe opcje instalacji i dalsze szczegóły znajdują się w [dokumentacji **agenta log Analytics**](../azure-monitor/agents/agent-windows.md).

1. Wybierz zestaw zdarzeń ([wszystkie, typowe lub minimalne](#event-sets)), które chcesz przesłać strumieniowo.

1. Kliknij przycisk **Update** (Aktualizuj).

1. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń zabezpieczeń systemu Windows, wpisz `SecurityEvent` w oknie zapytania.

## <a name="validate-connectivity"></a>Sprawdź poprawność łączności

Rozpoczęcie wyświetlania dzienników w Log Analytics może potrwać około 20 minut. 

### <a name="configure-the-security-events-connector-for-anomalous-rdp-login-detection"></a>Konfigurowanie łącznika zdarzeń zabezpieczeń pod kątem nietypowego wykrywania logowania protokołu RDP

> [!IMPORTANT]
> Nietypowe wykrywanie logowania za pomocą protokołu RDP jest obecnie w publicznej wersji zapoznawczej.
> Ta funkcja jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wskaźnik na platformie Azure może zastosować Uczenie maszynowe (ML) do danych zdarzeń związanych z zabezpieczeniami, aby zidentyfikować nietypowe działanie logowania Remote Desktop Protocol (RDP). Scenariusze obejmują:

- **Nietypowy IP** — adres IP rzadko lub nie został zaobserwowany w ciągu ostatnich 30 dni

- **Nietypowa lokalizacja geograficzna** — adres IP, miasto, kraj i ASN są rzadko lub nigdy Nieobserwowane w ciągu ostatnich 30 dni

- **Nowy użytkownik** — nowy użytkownik loguje się przy użyciu adresu IP i lokalizacji geograficznej, lub z których nie oczekuje się, że zostały one zaobserwowane na podstawie danych z 30-dniowych wcześniej.

**Instrukcje dotyczące konfiguracji**

1. Należy zebrać dane logowania do protokołu RDP (Identyfikator zdarzenia 4624) za pomocą łącznika danych **zdarzeń zabezpieczeń** . Upewnij się, że wybrano [zestaw zdarzeń](#event-sets) poza "Brak", aby przesłać strumieniowo do usługi Azure wskaźnikowej.

1. W portalu Azure wskaźnikowym kliknij pozycję **Analiza**, a następnie kliknij kartę **Szablony reguł** . Wybierz **(w wersji zapoznawczej) regułę wykrywania nietypowej nazwy logowania protokołu RDP** i przenieś suwak **stanu** na wartość **włączone**.

    > [!NOTE]
    > Ponieważ algorytm uczenia maszynowego wymaga, aby dane były w trakcie tworzenia profilu linii bazowej zachowania użytkownika przez 30 dni, przed wykryciem zdarzeń można zebrać dane zdarzeń zabezpieczeń.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób łączenia zdarzeń zabezpieczeń systemu Windows z wskaźnikiem kontrolnym platformy Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij wykrywanie zagrożeń przy użyciu funkcji wskaźnikowej platformy Azure, korzystając z [wbudowanych](tutorial-detect-threats-built-in.md) lub [niestandardowych](tutorial-detect-threats-custom.md) reguł.

